---
title: AzCopy Usuń | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia AzCopy Remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 0cc366ab2cdad9c7258dca905d8f4a06472119fe
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195908"
---
# <a name="azcopy-remove"></a>AzCopy Usuń

Usuwa jednostki z Azure Storage Blob, pliku i Azure Data Lake Storage Gen2.

## <a name="synopsis"></a>Streszczenie

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="examples"></a>Przykłady

Usuń pojedynczy obiekt BLOB z sygnaturą dostępu współdzielonego:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Usuń cały katalog wirtualny z sygnaturą dostępu współdzielonego:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Usuń tylko najpopularniejsze obiekty blob znajdujące się w katalogu wirtualnym, ale nie jego podkatalogach:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Usuń podzestaw obiektów BLOB w katalogu wirtualnym (na przykład: tylko pliki jpg i PDF lub nazwa obiektu BLOB to "exactname"):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

Usuń cały katalog wirtualny, ale Wyklucz określone obiekty blob z zakresu (na przykład: każdy obiekt BLOB, który rozpoczyna się od foo lub kończąc z paskiem):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

Usuń pojedynczy plik z Data Lake Storage Gen2 (dołączanie i wykluczanie nie jest obsługiwane):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Usuń jeden katalog z Data Lake Storage Gen2 (dołączanie i wykluczanie nie jest obsługiwane):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Opcje

|Opcja|Opis|
|--|--|
|--Wyklucz ciąg|Wyklucz pliki, w których nazwa jest zgodna z listą wzorców. Na przykład: *. jpg;* . PDF; exactname|
|-h,--pomoc|Pokaż zawartość pomocy dla polecenia Usuń.|
|--include String|Uwzględnij tylko pliki, w których nazwa jest zgodna z listą wzorców. Na przykład: *. jpg;* . PDF; exactname|
|--ciąg poziomu dziennika|Zdefiniuj poziom szczegółowości dziennika w pliku dziennika. Dostępne poziomy obejmują: INFORMACJE (wszystkie żądania/odpowiedzi), ostrzeżenie (wolne odpowiedzi), błąd (tylko Nieudane żądania) i brak (bez dzienników wyjściowych). (wartość domyślna: "INFO")|
|--rekursywnie|Poszukaj podkatalogów cyklicznie podczas synchronizacji między katalogami.|

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--Cap-MB/s UInt32|Szybkość transferu w megabitach na sekundę. Przepływność czasu na chwilę może się nieco różnić od końca. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie zostanie ograniczona.|
|--ciąg typu wyjściowego|Format danych wyjściowych polecenia. Dostępne opcje to: text, JSON. Wartość domyślna to "text".|

## <a name="see-also"></a>Zobacz także

- [AzCopy](storage-ref-azcopy.md)
