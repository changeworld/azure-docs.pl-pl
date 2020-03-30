---
title: azcopy usunąć | Dokumenty firmy Microsoft
description: Ten artykuł zawiera informacje referencyjne dla polecenia azcopy remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: abce1acb88e920c0de7bbb6447ec9d838f10486c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034000"
---
# <a name="azcopy-remove"></a>azcopy remove

Usuwanie obiektów blob lub plików z konta magazynu platformy Azure.

## <a name="synopsis"></a>Streszczenie

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Powiązane artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Przesyłanie danych za pomocą pamięci masowej AzCopy i Blob](storage-use-azcopy-blobs.md)
- [Przesyłanie danych za pomocą AzCopy i przechowywania plików](storage-use-azcopy-files.md)
- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z programem AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Przykłady

Usuń pojedynczy obiekt blob z sygnaturą dostępu Współdzielonego:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Usuń cały katalog wirtualny z sygnaturą dostępu Współdzielonego:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Usuń tylko górne obiekty blob wewnątrz katalogu wirtualnego, ale nie jego podkatarzy:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Usuń podzbiór obiektów blob w katalogu wirtualnym (na przykład: tylko pliki jpg i pdf lub jeśli nazwa obiektu blob to "exactName"):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

Usuń cały katalog wirtualny, ale wyklucz niektóre obiekty BLOB z zakresu (na przykład: każdy obiekt blob, który zaczyna się od foo lub kończy się na pasku):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

Usuń określone obiekty BLOB i katalogi wirtualne, umieszczając w pliku ich ścieżki względne (NIE zakodowane w adresie URL):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
file content:
  dir1/dir2
  blob1
  blob2

```

Usuń pojedynczy plik z konta magazynu obiektów blob, który ma hierarchiczny obszar nazw (dołącz/wyklucz nie jest obsługiwany).

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Usuń pojedynczy katalog konta magazynu obiektów Blob, który ma hierarchiczny obszar nazw (uwzględnij/wyklucz nie jest obsługiwany):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Opcje

**Ciąg --exclude-path**      Wyklucz te ścieżki podczas usuwania. Ta opcja nie obsługuje symboli wieloznacznych (*). Sprawdza względny prefiks ścieżki. Na przykład: myFolder;myFolder/subDirName/file.pdf.

**Ciąg --exclude-pattern** Wyklucz pliki, w których nazwa jest zgodna z listą wzorców. Na przykład: *.jpg;*. pdf;exactName

**-h, --pomoc** w usuwaniu

**--include-path** string Uwzględnij tylko te ścieżki podczas usuwania. Ta opcja nie obsługuje symboli wieloznacznych (*). Sprawdza względny prefiks ścieżki. Na przykład: mójFolder;mójFolder/subDirName/file.pdf

**--include-pattern** string Uwzględnij tylko pliki, w których nazwa jest zgodna z listą wzorców. Na przykład: *.jpg;*. pdf;exactName

**Ciąg --list-of-files** Definiuje lokalizację pliku, który zawiera listę plików i katalogów do usunięcia. Ścieżki względne powinny być rozdzielane podziałami wierszy, a ścieżki NIE powinny być zakodowane w adresie URL.

**Ciąg --log-level** Zdefiniuj szczegółowość dziennika dla pliku dziennika. Dostępne poziomy obejmują: INFO (wszystkie żądania/odpowiedzi), OSTRZEŻENIE (powolne odpowiedzi), BŁĄD (tylko nieudane żądania) i BRAK (brak dzienników danych wyjściowych). (domyślnie 'INFO') (domyślnie "INFO")

**--rekursywny**                Przyjrzyj się cyklicznie podkatarzy podczas synchronizacji między katalogami.

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--cap-mbps uint32|Limity szybkości transferu w megabitach na sekundę. Przepływność moment po chwili może się nieznacznie różnić od limitu. Jeśli ta opcja jest ustawiona na zero lub jest pomijana, przepływność nie jest ograniczona.|
|Ciąg typu --output|Format danych wyjściowych polecenia. Do wyboru są: tekst, json. Wartością domyślną jest "tekst".|

## <a name="see-also"></a>Zobacz też

- [azcopy](storage-ref-azcopy.md)
