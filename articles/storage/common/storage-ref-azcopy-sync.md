---
title: AzCopy synchronizacji | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia Sync AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: fb6c3b711a89ae7e4ef403a75927c4c6172523d0
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195765"
---
# <a name="azcopy-sync"></a>AzCopy synchronizacji

Replikuje lokalizację źródłową do lokalizacji docelowej.

## <a name="synopsis"></a>Streszczenie

Czas ostatniej modyfikacji jest używany do porównania. Plik zostanie pominięty, jeśli godzina ostatniej modyfikacji w miejscu docelowym jest nowsza.

Obsługiwane są następujące pary:

- < lokalnego — > obiektu blob platformy Azure (można użyć uwierzytelniania SAS lub OAuth)

Polecenie Sync różni się od polecenia copy na kilka sposobów:

  1. Flaga cykliczna jest domyślnie włączona.
  2. Źródło i miejsce docelowe nie powinny zawierać wzorców (takich jak * lub?).
  3. Flagi include i Exclude mogą być listami wzorców pasujących do nazw plików. Zapoznaj się z sekcją przykładową.
  4. Jeśli w miejscu docelowym znajdują się pliki lub obiekty blob, które nie są obecne w źródle, użytkownik zostanie poproszony o ich usunięcie.

     Ten monit można wyciszać przy użyciu odpowiednich flag, aby automatycznie odpowiedzieć na pytanie dotyczące usuwania.

### <a name="advanced"></a>Zaawansowane

AzCopy automatycznie wykrywa typ zawartości plików podczas przekazywania z dysku lokalnego na podstawie rozszerzenia lub zawartości pliku (jeśli nie określono rozszerzenia).

Wbudowana tabela odnośnika jest mała, ale w systemie UNIX, została uzupełniona o pliki MIME. Types systemu lokalnego, jeśli są dostępne w jednej lub kilku z tych nazw:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

W systemie Windows typy MIME są wyodrębniane z rejestru.

```azcopy
azcopy sync [flags]
```

## <a name="examples"></a>Przykłady

Synchronizuj pojedynczy plik:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Analogicznie jak powyżej, ale ten czas również oblicza skrót MD5 zawartości pliku i zapisuje go jako właściwość Content-MD5 obiektu BLOB:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Zsynchronizuj cały katalog wraz z jego podkatalogami (należy zauważyć, że rekursywa jest domyślnie włączona):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

lub

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Synchronizuj tylko najważniejsze pliki znajdujące się w katalogu, ale nie jego podkatalogach:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Zsynchronizuj podzbiór plików w katalogu (na przykład: tylko pliki jpg i PDF, lub jeśli nazwa pliku to "exactname"):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include="*.jpg;*.pdf;exactName"
```

Zsynchronizuj cały katalog, ale Wyklucz określone pliki z zakresu (na przykład: każdy plik, który rozpoczyna się od foo lub kończąc z paskiem):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude="foo*;*bar"
```

> [!NOTE]
> Jeśli wszystkie flagi dołączania/wykluczania są używane razem, wyszukiwane są tylko pliki zgodne ze wzorcami dołączania, ale te zgodne ze wzorcami wykluczania byłyby zawsze ignorowane.

## <a name="options"></a>Opcje

|Opcja|Opis|
|--|--|
|--rozmiar bloku-MB zmiennoprzecinkowego|Użyj tego rozmiaru bloku (określonego w MiB) podczas przekazywania do usługi Azure Storage lub pobierania z usługi Azure Storage. Wartość domyślna jest obliczana automatycznie na podstawie rozmiaru pliku. Dozwolone są ułamki dziesiętne (na przykład: 0,25).|
|--Check-MD5 — ciąg|Określa, jak ściśle skróty MD5 mają być sprawdzane podczas pobierania. Ta opcja jest dostępna tylko podczas pobierania. Dostępne wartości to: NOCHECK, LogOn, FailIfDifferent, FailIfDifferentOrMissing. (domyślnie "FailIfDifferent").|
|--Usuń ciąg docelowy|Określa, czy należy usunąć dodatkowe pliki z lokalizacji docelowej, które nie znajdują się w źródle. Może być ustawiona na wartość true, false lub Prompt. Jeśli ustawiono opcję Monituj, użytkownik zostanie poproszony o podanie pytania przed zaplanowaniem plików i obiektów BLOB do usunięcia. (wartość domyślna to "false").|
|--Wyklucz ciąg|Wyklucz pliki, w których nazwa jest zgodna z listą wzorców. Na przykład: *. jpg;* . PDF; exactname.|
|-h,--pomoc|Pokaż zawartość pomocy dla polecenia Sync.|
|--include String|Uwzględnij tylko pliki, w których nazwa jest zgodna z listą wzorców. Na przykład: *. jpg;* . PDF; exactname.|
|--ciąg poziomu dziennika|Zdefiniuj poziom szczegółowości dziennika dla pliku dziennika, dostępne poziomy: INFORMACJE (wszystkie żądania/odpowiedzi), ostrzeżenie (wolne odpowiedzi), błąd (tylko Nieudane żądania) i brak (bez dzienników wyjściowych). (wartość domyślna to "INFO").|
|--Put-MD5|Utwórz skrót MD5 każdego pliku i Zapisz skrót jako właściwość Content-MD5 docelowego obiektu BLOB lub pliku. (Domyślnie skrót nie jest tworzony). Dostępne tylko podczas przekazywania.|
|--rekursywnie|Domyślnie wartość true powoduje cykliczne poszukiwanie katalogów podrzędnych podczas synchronizacji między katalogami. (wartość domyślna to true).|

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--Cap-MB/s UInt32|Szybkość transferu w megabitach na sekundę. Przepływność czasu na chwilę może się nieco różnić od końca. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie zostanie ograniczona.|
|--ciąg typu wyjściowego|Format danych wyjściowych polecenia. Dostępne opcje to: text, JSON. Wartość domyślna to "text".|

## <a name="see-also"></a>Zobacz także

- [AzCopy](storage-ref-azcopy.md)
