# 1 БАЗОВЫЕ ТИПЫ
```c++
(const char*)"Hello"      //   ANSI   (1 BYTE) (только англ)
(const wchar_t*)L"Привет" //  UNICODE (2 BYTE) (мультиязычная)
TEXT("Тип зависит от macros UNICODE") // _T() есть TEXT() это макросы

LPCSTR s = "Hello"; 
LPCWSTR wstr = L"Привет";
```

* "L" - "long"
* "P" - "pointer"
* "C" -"const"
* "W" - "wide"
* "STR" - "string"
* "T" - "template" (выбор ANSI или UNICODE зависит от типа компиляции)

PWSTR - указатель на широкую строку (wchar_t*)
LPCSTR - длинный указатель на неизменяемую строку (char*)

---
Строковые
```c++
CHAR ch;   //char
WCHAR wch; //wchar_t
TCHAR tch; //char или wchar_t
```

Логические
```c++
BOOL bres; //int, для получения кода ошибки
BOOLEAN b; //размер 1 байт
```

Целые
```c++
BYTE _byte;   //1 байт
WORD _word;   //2 байта
INT _int;     //4 байт
DWORD _dword; //8 байтов
```

Дробные
```c++
FLOAT _float;   //4 байта
DOUBLE _double; //8 байт
```

Специфичные
```c++
PVOID pv; //void*

HADNLE hndl; //void*, структура с данными
```
---

