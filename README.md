# Постановка задачи

Согласно условиям кейса, перед нами была поставлена задача разработать систему на основе ИИ, которая бы анализировала список пользовательских ответов и возвращала понятное и интепретируемое облако слов. При этом стоит учитывать, что пользовательские ответы могут быть сходными по смыслу, но различными по использованным грамматическим и лексическим единицам. Таким образом, для выполнения задачи необходимо было учесть не только основные понятия, с наибольшей вероятностью встречающиеся среди ответов, но и синонимичные им.

# Проверенные решения 

В ходе работы нами были рассмотрены два метода решения задачи: с помощью ChatGPT и LSVC (линейного метода опорных векторов). ChatGPT показал следующие результаты: загрузив на вход список из 34 возможных ключевых слов и команду выбрать одно, наиболее подходящее по смыслу предложения, мы получили ответ в виде списка ключевых слов через 40 секунд. Такой результат мы сочли слишком медленной работой, поэтому приняли решение самостоятельно обучить модель на данных двух составленных нами датасетов.

# Датасеты для обучения модели

  Создание данных было основано на необходимости разработать систему, обрабатывающую ответы сотрудников компании на различны темы, касающиеся их профессиональной деятельности (например, о мотивации в работе, недостатках и преимуществах работы в данной компании/должности и тд). Для этого нужно было определить ключевые факторы, влияющие на удовлетворенность и продуктивность сотрудников. В силу ограниченности времени, ответы сотрудников были сгенерированы с помощью СhatGPT. Далее были определены ключевые слова, на основе которых было создано два датасета: 
  1) Датасет "dataset.txt" содержит в качестве ответов сотрудников предложения, которые при обучении модели используются в качестве признаков, и соответствующие смыслу предложений ключевые слова, которые являются метками при обучении. Размер датасета: 2565 строк.
     ![image](https://github.com/user-attachments/assets/eaf627df-e373-4831-84fa-fa2ca69087ed)

  3) Датасет "dataset_with_syn.txt" содержит в качестве ответов сотрудников не только предложения, но и возможные синонимы к ключевым словам, которые так же используются в качестве признаков для модели. Метками в датасете так же являются соответствующие по смыслу ключевые слова. Тем самым в данном датасете мы учитываем возможные ответы в виде одного слова. Размер датасета: 3920 строк.
     
     ![image](https://github.com/user-attachments/assets/b2314145-055c-46fc-8c98-a8b5a687c29a)

# Сравнение моделей для классификации

**Для сравнения были выбраны следующие модели:**

Линейные алгоритмы: 1) Логистическая регрессия / Logistic Regression (‘LR’)

Нелинейные алгоритмы:
2) Метод k-ближайших соседей (классификация) / K-Neighbors Classifier (‘KNN’)
3) Деревья принятия решений / Decision Tree Classifier (‘CART’)
4) Линейный метод опорных векторов (классификация) / Linear Support Vector Classification (‘LSVC’)
5) Метод опорных векторов (классификация) / C-Support Vector Classification (‘SVC’)

Ансамблевые алгоритмы:
6) Случайный лес (классификация) / Random Forest Classifier (‘RF’)
7) Экстра-деревья (классификация) / Extra Trees Classifier (‘ET’)
8) AdaBoost (классификация) / AdaBoost Classifier (‘AB’) (AdaBoost = Adaptive Boosting)

**Обучение каждой модели проводилось на двух наборах данных:**

"dataset_with_syn.txt"



"dataset.txt"

![image](https://github.com/user-attachments/assets/6d8915d9-a5b9-4813-88f1-5a97fb763bdc)


# Выбранная модель: Линейный метод опорных векторов (LSVC)
**Перед обучением модели проводилась предобработка текста ответов пользователей:**
1) Приведение к нижнему регистру
2) Оставляем только текст
3) Токенизация предложения
4) Удаление стоп-слов
5) Лемматизация слов в предложении
6) Векторизация предложений
   
![image](https://github.com/user-attachments/assets/f5356759-f488-46b8-82fd-155b0271872b)

**Обучение модели**
Модель обучалась на двух датасетах: 
1) dataset.txt, содержащем в качестве ответов только предложения (2565 строк)
2) dataset_with_syn.tx, содержащем и предложения, и синонимы (3917 строк)
   
Точности при обучении моделей на этих двух датасетах также различались:
1) для dataset.txt: train = 0.780 / test = 0.816
2) для dataset_with_syn.tx: train = 0.503 (0.030) / test = 0.545

В итоге было принято решение использовать для внедрения в пользовательский интерфейс модель, обученную на данных из датасета "dataset.txt"

# Формат данных

- На вход обученной модели подаётся текстовый файл с ответами пользователей через точку с запятой в формате:
"Нравится коллектив; Хочу много зарабатывать; ..."

- На выходе модель выдаёт список соответствующих ключевых слов

# Визуализация данных

- Из полученного списка ключевых слов мы составляем словарь, где в качестве ключа ключевое слово, в качестве значения - частота встречаемости слова в списке
- Помимо словаря на основе полученного списка мы составляем облако ключевых слов
  
  ![image](https://github.com/user-attachments/assets/d50e1b89-0b81-454b-8bf0-c5724128f307)

# Пользовательский интерфейс

В качестве пользовательского интерфейса для отправки файлов с ответами пользователей модели было выбрано два варианта:
1) **Веб-интерфейс**
   
   ![image](https://github.com/user-attachments/assets/9ec5fba7-7afa-49a0-b23b-6477ea9b7f20)

3) **telegram-бот**
   
   ![image](https://github.com/user-attachments/assets/22090443-1e0e-476a-a3a5-bc22288945dd)
   
**Ссылка на telegram-бот**: http://t.me/MTSLinkII_bot

# Ссылка на ноутбук с кодом решения
https://colab.research.google.com/drive/1MtbNrb40_DkXmO0KqmDF5izlK0iGT_FX?usp=sharing
