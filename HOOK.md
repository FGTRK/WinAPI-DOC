**Хук функций** в C++ —  изменение поведения функции во время ее выполнения
Обычно её используют, чтобы модифицировать функции без изменения исходного кода

Установка хука:
1. Найти адрес функции, вызов которой нужно перехватывать
2. Сохранить несколько первых байтов этой функции в другом участке памяти
3. На их место вставить машинную команду JUMP для перехода по адресу подставной функции

