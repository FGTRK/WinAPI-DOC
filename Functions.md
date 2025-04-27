# HADLE
Получить дескриптор приложения
```c++
HANDLE hInst = GetModuleHadnle(NULL);
```

Получить дескриптор рабочего стола
```c++
HWND h = GetDesktopWindow(NULL);
```

# GDI
Получить дескриптор контекста устройства
```c++
HDC hdc = GetDC(hwnd); // для клиентской области
HDC hdc = GetWindowDC(hwnd); // для всего окна
```

Изменить объект контекста устройства
```c++
HGDIOBJ old = SelectObject(hdc, brush2);
```

Координаты указателя **мыши**
```c++
POINT point;
GetCursorPos(&rect);
ScreenToClient(hWnd, &point); // внутри окна
```

# ОКНО
Задать минимальный/максимальный **размер** окна
требуется обработать сообщение в оконной функции
```c++
case WM_GETMINMAXINFO: 
	LPMINMAXINFO lpMMI = (LPMINMAXINFO)lParam;
	lpMMI->ptMinTrackSize.x = 300;
	lpMMI->ptMinTrackSize.y = 300;
	break;
```

Универсальная **конкатенация** строк ANSI/UNICODE
```c++
#include <strsafe.h>
StringCchCat(msg, 100, szClassName);
```

