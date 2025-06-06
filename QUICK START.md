**API** - набор внешних функций (интерфейсов) приложения, через которые с ним взаимодействуют другие приложения

* WinAPI имеет свои типы данных, т.к. это программный интерфейс, позволяющий запускать программу WinAPI на любой системе, где есть реализация этого интерфейса
* WinAPI написан на С, поэтому в нем **нет классов из ООП**, а есть функции и указатели
* Функции WinAPI в Windows вызываются из соответствующих `.dll`
# 1 ТОЧКА ВХОДА
```c++
#include <windows.h>
int WINAPI WinMain(HINSTANCE hInstance, HINSTANCE hPrevInstance, LPSTR lpCmdLine, int nCmdShow) {
	MessageBox(NULL, // дескриптор родительского окна
			   L"Hello, World!", // указатель на строку внутри окна
			   L"Welcome", // указатель на строку - название окна
			   MB_OK // вид окна
			   );
	return 0;
}
```

`WinMain` - функция, точка **входа** для оконного приложения (как <ins>main</ins> в С++ для консольного)
Возвращает код завершения приложения

---
WINAPI = \_\_stdcall
*stdcall* — это **СОГЛАШЕНИЕ О ВЫЗОВАХ**: способ определения того, как параметры передаются в функцию (в стеке или в регистрах), какая функция очищает параметры после возврата (вызывающая или вызываемая), как функция возвращает значения (через стек или регистры)

*stdcall* является стандартным для Win32 API

*stdcall*: аргументы функции помещаются в стек <ins>справа налево</ins>. Очищает стек <ins>вызываемая</ins> функция
*cdecl*:  аргументы функции помещаются в стек <ins>справа налево</ins>. Очищает стек <ins>вызывающая</ins> функция. Стандартное для C/C++
*fastcall*:  аргументы передаются через <ins>регистры</ins>

---
Все типы на H (handle, дескриптор) являются указателями на свою структуру
**Дескриптор** - идентификатор ресурса

*HINSTANCE* - указатель на память с библиотекой или приложением
*LPSTR* - указатель на символьную строку (char \*)

LP (long pointer) - пережиток 16-разрядной Windows, где различались короткие 16-разрядные указатели и длинные 32-битные указатели

ПАРАМЕТРЫ WinMain:
* <ins>hInstance</ins> - адрес начала приложения (**дескриптор процесса**)
* <ins>hPrevInstance</ins> - сейчас не используется, т.к. Windows (32/64 bit) имеет изолированное адресное пространство для **копий** одного и того же приложения. Нельзя просто создать копию, скопировав данные адресного пространства другого процесса
* <ins>lpCmdLine</ins> - адрес на строку (командные **аргументы**), переданную при вызове приложения через консоль
* <ins>nCmdShow</ins> - параметр **отображения** окна (обычное, свернутое, развернутое)

---
MessageBox создает **диалоговое окно** с помощью предопределенного оконного класса Параметры функции:
* дескриптор родительского окна
* адрес строки, отображающейся внутри окна (типа LPCSTR = const char*)
* адрес строки  - имени окна (типа LPCSTR = const char*)
* константа, определяющая вид окна

MessageBox имеет **свою** оконную процедуру и цикл обработки сообщений

В конце функции может стоят **суффикс** A или W (MessageBoxA, MessageBoxW) или отсутствовать (MessageBox). Он говорит о **типе** принимаемых строк:
* A - принимает **ANSI** строку (1 байт на символ, const char*)
* W - принимает **UNICODE** строку (2 байта на символ, const wchar_t*)
* отсутствует - тип строки выбирается в зависимости от наличия **макроса** UNICODE

---
# 2 Hello World
```c++
#define UNICODE
#include <windows.h>

LRESULT CALLBACK WndProc(HWND, UINT, WPARAM, LPARAM);

int WINAPI WinMain(HINSTANCE hInstance, HINSTANCE hPrevInstance, LPSTR lpCmdLine, int nCmdShow) {

    LPCWSTR szClassName = L"MyClass"; // название класса главного окна

    HWND hMainWnd; // дексриптор главного окна
    MSG msg;       // структура сообщения
    WNDCLASS wc;   // структура для определения класса окна

    // заполняем структуру окна
    memset(&wc, 0, sizeof(wc));
    wc.lpfnWndProc = WndProc;       // адрес оконной процедуры
    wc.lpszClassName = szClassName; // указатель на строку - имя класса
    wc.hInstance = hInstance;       // дексриптор приложения, где расположена оконная процедура

    wc.style = CS_HREDRAW | CS_VREDRAW; // сообщение WN_PAINT, если изменены размеры окна по горизонтали и вертикали

    wc.hbrBackground = (HBRUSH)GetStockObject(WHITE_BRUSH); // дескриптор кисти для закраски фона клиентской области окна

    // Регистрируем класс окна
    if (!RegisterClass(&wc)) {
        MessageBox(NULL, L"Cannot register class", L"Error", MB_OK);
        return 1;
    }

    // Создаем основное окно приложения
    hMainWnd = CreateWindow(
        szClassName,             // зарегистрированный класс окна
        L"Hello World Application", // заголовок окна
        WS_OVERLAPPEDWINDOW,     // стиль окна
        CW_USEDEFAULT,           // x координата
        CW_USEDEFAULT,           // у координата
        400,                     // ширина окна в пикселях
        200,                     // высота окна в пикселях
        (HWND)NULL,              // декскриптор родительского окна
        (HMENU)NULL,             // дескриптор меню окна
        (HINSTANCE)hInstance,    // дескриптор экземпляра приложения
        NULL);

    if (!hMainWnd) {
        MessageBox(NULL, L"Cannot create main window", L"Error", MB_OK);
        return 2;
    }

    // Показываем окно
    ShowWindow(hMainWnd, nCmdShow);
    UpdateWindow(hMainWnd);

    // Цикл обработки сообщений
    while (GetMessage(&msg, // адрес структуры для сообщения
                      NULL, // дескриптор окна, принимающего сообщение (любое окно)
                      0, // нижняя граница идентификатора сообщений
                      0  // верхняя граница идентификатора сообщений
                       )) {
        TranslateMessage(&msg); // для клавиатуры
        DispatchMessage(&msg);  // обработать сообщение оконной функцией
    }
    return msg.wParam;
}

LRESULT CALLBACK WndProc(HWND hWnd, UINT uMsg, WPARAM wParam, LPARAM lParam) {

    HDC hDC;        // дескриптор контекста устройства
    PAINTSTRUCT ps;
    RECT rect;      // структура прямоугольника

    switch (uMsg) {

    case WM_PAINT: // сообщение о перерисовке
        hDC = BeginPaint(hWnd, &ps); //получить контекст устройства
        GetClientRect(hWnd, &rect);// размеры клиентской области окна
        DrawText(hDC,              // дескриптор контекста устройства
                 L"Hello, World!", // выводимая строка
                 -1,               // длина строки (авто)
                 &rect,            // ограничивающий прямоугольник
                 DT_SINGLELINE | DT_CENTER | DT_VCENTER // форматирование текста
        );
        EndPaint(hWnd, &ps); //освободить контекст устройства
        break;

    case WM_CLOSE:
        DestroyWindow(hWnd); // отправить WM_DESTROY
        break;

    case WM_DESTROY:
        PostQuitMessage(0); // отправить WM_QUIT
        break;

    default: // стандартная обработка сообщений
        return DefWindowProc(hWnd, uMsg, wParam, lParam);
    }
    return 0;
}
```

Этапы построения базового каркаса приложения:
1. **WinMain** - точка входа
2. **WndProc** - функция обработки сообщений зарегистрированного класса окна
3. Зарегистрировать класс главного окна. Создать структуру WINDCLASS и зарегистрировать новый класс окна **RegisterClass**
RegisterClass отличается от RegisterClassEx двумя полями - размером структуры и иконкой
4. Создать окно на основе зарегистрированного класса. **CreateWindow** возвращает дескриптор созданного окна либо NULL, если не удалось создать
5. Отобразить окно на экране. **ShowWindow**
**UpdateWindow** посылает окну сообщение WM_PAINT, чтобы перерисовать клиентскую область. **ShowWindow** создает сообщения WM_SIZE и  WM_MOVE, а WM_SIZE порождает WM_PAINT
6. Обработка сообщений. У каждого потока программы есть очередь сообщений. Любое действие пользователя Windows помещает сообщение в эту очередь. **GetMessage** извлекает сообщение. **GetMessage** возвращает false только для сообщения WM_QUIT
**TranslateMessage** используется если есть ввод с клавиатуры, т.к. в Windows двухуровневая схема обработки сообщений от клавиатуры
**DispatchMessage** передает управление Windows, которая вызывает соответствующую оконную процедуру для соответствующего окна

Если очередь сообщений потока приложения **пуста**, то GetMessage, ожидая сообщение, **блокирует** цикл обработки сообщений. Для избежания блокировки используется **PeekMessage**, не блокирующая исполнение в случае отсутствия сообщений

---
Оконная процедура определяет **как реагировать** на сообщения к окну
```c++
LRESULT CALLBACK WndProc(HWND hWnd,  // дескриптор окна получателя сообщения
						 UINT message,  // код сообщения
						 WPARAM wParam, // зависит от типа сообщения
						 LPARAM lParam  // зависит от типа сообщения)
```
4 параметра аналогичны первым 4 параметрам структуры **MSG**

**WPARAM LPARAM** 
Раньше Windows была 16-битной и каждое сообщение могло нести W (word, слово, 16 бит) и L (long, 32 бит) 
* В WPARAM передаются дескрипторы или целые числа
* В LPARAM передаются указатели
---

1. Сообщение **WM_PAINT**. Когда надо перерисовать клиентскую область окна (она стала недействительной)
Когда требуется?
* При создании окна
* При изменении его размеров
* При разворачивании окна
* При открытии клиентской области после перекрытия другими окнами

**BeginPaint** возвращает дескриптор контекста устройства
**Контекст устройства** - структура, описывающая  устройство вывода (дисплей, принтер). Она содержит цвет фона, кисть, шрифт для **рисующих** функций, принимающих дескриптор контекста устройства в качестве аргумента
**BeginPaint** делает 2 вещи:
1. перекрашивает **фон** клиентской области (использует кисть в поле hbrBackground структуры WNDCLASSEX)
2. делает клиентскую область **действительной**, не требующей перерисовки

**GetClientRect** заполняет структуру RECT, описывающую положение и размеры клиентского окна
* (left, top) - координата левого верхнего угла
* (right, bottom) - координата правого нижнего угла

**DrawText** рисует текст в прямоугольной области
**EndPaint** освобождает ресурс контекст устройства

2. Сообщение **WM_CLOSE**
Когда появляется?
* пользователь нажимает кнопку закрытия окна
* нажатие ALT+F4
При отсутствии этого сообщения функция **DefWindowProc** вызовет по умолчанию функцию **DestroyWindow**
Окно еще не разрушено, поэтому это сообщение подходит для вывода диалогового окна: Вы точно хотите закрыть приложение?

**DestroyWindow** посылает окну сообщение WM_DESTROY, а затем его дочерним. Она завершает работу после **уничтожения** всех дочерних окон

**PostQuitMessage** посылает сообщение WM_QUIT


