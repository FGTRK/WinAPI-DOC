# 1 СОЗДАТЬ ФАЙЛ
```c++
HANDLE WINAPI CreateFileA(
	_In_ LPCSTR                     lpFileName,      // путь к файлу
	_In_ DWORD                      dwDesiredAccess, // доступ к файлу
	_In_ DWORD                      dwSharedMode,    // доступ к файлу другому ПО
	_In_opt_ LPSECURITY_ATTRIBUTES  lpSecurityAttributes,  // NULL
	_In_ DWORD                      dwCreationDescription, // как открыть файл
	_In_ DWORD                      dwFlagsAndAttributes,  // атрибуты файла
	_In_opt_ HANDLE                 hTempalteFile // дескриптор родительского файла
);
```
Возвращает <ins>дескриптор открытого файла</ins>

<ins>opt</ins> - optional, параметр можно не указывать (указать NULL)

(2 dwDesiredAccess) Доступ к файлу (можно объединить через | ):
* GENERIC_READ - только читать
* GEENRIC_WRITE - только писать

(3 dwDesiredAccess) Доступ к файлу другому ПО (можно объединить через | ):
* NULL - другое ПО не имеет никакого доступа к файлу, пока дескриптор занят основной программой
* FILE_SHARE_READ - только чтение
* FILE_SHARE_WRITE - только чтение и запись
* FILE_SHARE_DELETE - чтение, запись, удаление

(5 dwCreationDescription) Как открыть файл:
* CREATE_NEW - создает файл, если такого нет. Иначе возвращает NULL
* CREATE_ALWAYS - создает новый файл. Если такой есть, то перезаписывает его
* CREATE_EXISTING - открыть существующий файл, есть такой есть. Иначе возвращает NULL
* OPEN_ALWAYS - открыть существующий файл. Если такой есть, то перезаписывает его
* TRUNCATE_EXISTING - дописать в конец существующего файла. Файл дб открыть на запись

(6 dwFlagsAndAttributes) Атрибуты файла:
* FILE_ATTRIBUTE_NORMAL - атрибуты

(7 hTempalteFile) Дескриптор файла, от которого наследуются атрибуты

ПРИМЕР РАБОТЫ CreateFile
```c++
PCWSTR PATH = L"D:/1/test.txt"; // путь к файлу

HANDLE hFile = CreateFile(
    PATH,
    GENERIC_READ | GENERIC_WRITE,
    NULL,
    NULL,
    OPEN_ALWAYS,
    FILE_ATTRIBUTE_NORMAL,
    NULL
);

// что-то делаем с файлом

CloseHandle(hFile); // освободить ресурс
```
---

# 2 ЧТЕНИЕ И ЗАПИСЬ
```c++
OVERLAPPED olf{NULL}; // структура, задающая позицию курсора в файле
LARGE_INTEGER li;
li.QuadPart = 1;
```

OVERLAPPED - структура, задающая позицию курсора в файле. Состоит из:
* hEvent - дескриптор
* Offset - младшие биты, куда поставить курсор (младшие 32 бит)
* OffsetHigh - старшие биты, куда поставить курсор (старшие 32 бит)
* Iternal - новая позиция курсора, возвращаемое значение (младшие 32 бит)
* IternalHigh - новая позиция курсора, возвращаемое значение (старшие 32 бит)

LARGE_INTEGER - структура для объединения двух 32-битных чисел в 64-битное. Состоит из:
* LowPart - младшие 32 бит
* Highpart - старшие 32 бит
* QuadPart - целое 64-битное число


ПРИМЕР ЗАПИСИ В ФАЙЛ
```c++
CWSTR PATH = L"D:/1/test.txt"; // путь к файлу

HANDLE hFile = CreateFile(
    PATH,
    GENERIC_READ | GENERIC_WRITE,
    NULL,
    NULL,
    OPEN_ALWAYS,
    FILE_ATTRIBUTE_NORMAL,
    NULL
);
  
const INT SIZE_BUFFER = 256;

OVERLAPPED olf{ NULL };

LPSTR buffer = (CHAR*)calloc(SIZE_BUFFER + 1, sizeof(CHAR));
DWORD iNumRead = 0; // колво считанных байт

if (!ReadFile(hFile, buffer, SIZE_BUFFER, &iNumRead, &olf))
    return 1;

if (olf.Internal == -1 && GetLastError())
    return 2;

olf.Offset += iNumRead;

LPCSTR str = "It is my file...\r\n";

DWORD iNumWrite = 0; // колво записанных байт

if (!WriteFile(hFile, str, strlen(str), &iNumWrite, &olf))
    return 3;

if (olf.Internal == -1 && GetLastError())
    return 4;

CloseHandle(hFile);
```

