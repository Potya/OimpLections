
[Материалы лекций](/new_lections)


# Лекция 1


Disclaimer: не пытайтесь скомпилировать этот файл целиком как есть.
Тут собраны фрагменты разных программ, которые нужно компилировать и запускать по отдельности.

Общие слова про историю языка, про стандарты C++, про компиляторы и т. д.
Вот ссылка на один из последних черновиков стандарта [C++14](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2013/n3797.pdf "Стандарт C++14").

Напишем простейшую программу "Hello, world!"

    #include <iostream>

    int main() {
        std::cout << "Hello, world\n";
    }

[Run](https://godbolt.org/g/e3tgUk)

Любая программа на C++ начинает исполнение с функции main.
Здесь мы передаем строку (с завершающим символом перевода строки) в cout, то есть, на консоль.
Директива #include ... подключает заголовочный библиотечный файл с именем iostream, в котором определен объект cout.


Считается, что функция main должна возвращать целое число (значение типа int).
Причем это число равно 0, если программа завершилась успешно.
А если оно отлично от 0, то оно может обозначать код ошибки.

Если функция имеет тип int, то из нее обязательно нужно что-то вернуть с помощью оператора return.
Но для функции main сделано исключение: return 0 можно не писать. А можно было бы написать:

    #include <iostream>

    int main() {
        std::cout << "Hello, world\n";
        return 0;  // В функции main разрешается опускать этот return 0
    }

std:: - это префикс пространства имен, в котором собраны стандартные библиотечные сущности.


Скомпилируем программу из консоли с помощью компилятора clang++ и запустим её вот так:

    clang++ <имя_нашего_файла>
    ./a.out

(по умолчанию исполняемому файлу, сгенерированному компилятором, присваивается имя a.out)

Напишем программу, спрашивающую имя пользователя.

    #include <iostream>
    #include <string>  // Требуется для типа std::string

    int main() {
        std::string name;  // Объявляем переменную name типа std::string
        std::cin >> name;  // Считываем ее с клавиатуры. Обратите внимание на направление стрелок!
        std::cout << "Hello, " << name << "!\n";
    }


Теперь давайте напишем программу, которая для заданного n печатает таблицу квадратов первых n натуральных чисел.
Проиллюстрируем на этом примере разные виды циклов.

    #include <iostream>

    int main() {
        int n;
        std::cin >> n;
        int i = 0;
        while (i <= n) {  // цикл с предусловием - выполняется до тех пор, пока условие истинно
            std::cout << i << "\t" << i * i << "\n";  // выводим число и его квадрат через табуляцию
            ++i;  // увеличиваем счётчик i на единицу
        }
    }

Теперь то же самое, но с циклом for.
В его заголовке три секции: инициализация; условие продолжения цикла; выражение, вычисляющаеся после каждой итерации.

    #include <iostream>

        int main() {
        int n;
        std::cin >> n;
        for (int i = 0; i <= n; ++i) {
            std::cout << i << "\t" << i * i << "\n";
        }
    }


Вопрос из зала: а можно было бы саму печать поместить в выражение в третьей части заголовка цикла, вместо ++i?
Ответ: да, можно. Но не нужно. Смотрите:

    for (int i = 0; i <= n; std::cout << i << "\t" << i * i << "\n", ++i);

Тело цикла - пустой оператор.
Выражение в третьей части заголовка содержит оператор "запятая", о котором мы еще будем говорить.
Он прост: сначала вычисляется левая часть, затем - правая.

Но так писать не надо. Сложная программа, перегруженная конструкциями, затрудняет чтение и чревата ошибками.

Наконец, напишем цикл с постусловием:

    #include <iostream>

    int main() {
        int n;
        std::cin >> n;
        int i = 1;
        do {
            std::cout << i << "\t" << i * i << "\n";
            ++i;
        } while (i <= n);
    }

Всё ли хорошо в этой программе?
Нет. Если n <= 0, то программа всё равно выведет строку для i = 1.
Чтобы этого не произошло, придётся делать проверку.
Вообще, создатель C++ Бьярн Страуструп не любит цикл с постусловием, и рекомендует всегда стараться использовать цикл с предусловием вместо него.

Теперь - условный оператор.

    #include <iostream>

    int main() {
        int age;
        std::cin >> age;
        if (age <= 18) {
            if (age == 18) {  // Обратите внимание, как пишется проверка на равенство
                std::cout << "Equal\n";
            } else {
                std::cout << "Less\n";
            }
        } else {
            std::cout << "Greater\n";
        }
    }

Удобнее вместо многоэтажных вложенных if'ов переписать это так:

    if (age < 18) {
        std::cout << "Less\n";
    } else if (age == 18) {
        std::cout << "Equal\n";
    } else {
        std::cout << "Greater\n";
    }

Фигурные скобки в данных случаях необязательны, так как внутри операторов if/else стоит лишь один оператор.
Однако я рекомендую их всегда ставить. Это позволит избежать ошибок при редактировании кода.


Ворос из зала: как понять, какой else к какому if'у относится?
Ответ: всегда относится к ближайшему if'у. Сравните:

    if (x <= y)
        if (x < y)
            std::cout << "Less\n";
        else
            std::cout << "Equal\n";
    else
        std::cout << "Greater\n";


    if (x <= y) {
        if (x < y)
            std::cout << "Less\n";
    } else
        std::cout << "Greater\n";

В некоторых простых случаях можно воспользоваться оператором switch:

    #include <iostream>

    int main() {
        int x;
        std::cin >> x;
        switch (x) {  // "Прыгаем" на нужную метку в зависимости от значения x
            case 1:
                std::cout << "First action\n";
                break; // Если break не написать, то исполнение программы продолжится дальше
            case 2:
                std::cout << "Second action\n";
                break;
            case 3:
            case 4:
                std::cout << "Third or fourth action\n";
                break;
            default:  // не опечатайтесь: если напишете defalut, то программа всё равно скомпилируется!
                std::cout << "Wrong action\n";
        }
        // break выходит из оператора switch и передает управление сюда
    }
