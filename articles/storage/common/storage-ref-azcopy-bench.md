---
title: ławka azcopy | Dokumenty firmy Microsoft
description: Ten artykuł zawiera informacje referencyjne dla polecenia azcopy bench.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8570bce87aeea5473b4aadf9bd30bc0a648a6f0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72518306"
---
# <a name="azcopy-bench"></a>azcopy bench

Uruchamia wskaźnik wydajności, przekazując dane testowe do określonego miejsca docelowego. Dane testowe są generowane automatycznie.

Polecenie benchmark uruchamia ten sam proces przesyłania co "copy", z tą różnicą, że:

  - Nie ma parametru źródłowego.  Polecenie wymaga tylko docelowego adresu URL. W bieżącej wersji ten docelowy adres URL musi odwoływać się do kontenera obiektów blob.
  
  - Ładunek jest opisany przez parametry wiersza polecenia, które kontrolują, ile plików jest generowanych automatycznie i jak duże są. Proces generowania odbywa się w całości w pamięci. Dysk nie jest używany.
  
  - Obsługiwane są tylko niektóre z parametrów opcjonalnych, które są dostępne dla polecenia copy.
  
  - Dodatkowa diagnostyka jest mierzona i zgłaszana.
  
  - Domyślnie przesyłane dane są usuwane po zakończeniu przebiegu testu.

Tryb porównawczy automatycznie dostroi się do liczby równoległych połączeń TCP, które zapewniają maksymalną przepustowość. Na końcu zostanie wyświetlony ten numer. Aby zapobiec automatycznemu dostrajaniu, ustaw zmienną środowiskową AZCOPY_CONCURRENCY_VALUE na określoną liczbę połączeń.

Obsługiwane są wszystkie typy uwierzytelniania. Jednak najbardziej wygodne podejście do analizy porównawczej jest zazwyczaj do tworzenia pustego kontenera z tokenem sygnatury dostępu Współdzielonego i używać uwierzytelniania sygnatury dostępu Współdzielonego.

## <a name="examples"></a>Przykłady

```azcopy
azcopy bench [destination] [flags]
```

Przeprowadzić test porównawczy z parametrami domyślnymi (odpowiedni dla sieci porównawczych o przekątnych do 1 Gb/s):

- azcopy ławka "https://[account].blob.core.windows.net/[container]? <SAS>"

Uruchom test porównawczy, który przesyła 100 plików, każdy o rozmiarze 2 GiB: (odpowiedni do analizy porównawczej w szybkiej sieci, np. 10 Gb/s):'

- azcopy ławka "https://[account].blob.core.windows.net/[container]? <SAS>" --file-count 100 --size-per-file 2G

Tak samo jak powyżej, ale użyj 50 000 plików, każdy rozmiar 8 MiB i obliczyć ich skróty MD5 (w taki sam sposób, w jaki flaga --put-md5 robi to w poleceniu copy). Celem --put-md5 podczas analizy porównawczej jest sprawdzenie, czy obliczenia MD5 wpływają na przepływność dla wybranej liczby i rozmiaru plików:

- azcopy ławka "https://[account].blob.core.windows.net/[container]? <SAS>" --file-count 50000 --size-per-file 8M --put-md5

## <a name="options"></a>Opcje

**Ciąg typu --blob** definiuje typ obiektu blob w miejscu docelowym. Służy do zezwalania na porównywanie różnych typów obiektów blob. Identyczny z tym samym parametrem w poleceniu copy (domyślnie "Detect").

**--block-size-mb** float Użyj tego rozmiaru bloku (określonego w MiB). Wartość domyślna jest obliczana automatycznie na podstawie rozmiaru pliku. Dopuszczalne są ułamki dziesiętne - np. Identyczne z tym samym parametrem w poleceniu copy.

**--delete-test-data**  Jeśli to prawda, dane porównawcze zostaną usunięte po zakończeniu testu porównawczego.  Ustaw go na false, jeśli chcesz zachować dane w miejscu docelowym - na przykład, aby użyć ich do testów ręcznych poza trybem testu porównawczego (domyślna wartość true).

**--file-count** uint Liczba automatycznie wygenerowanych plików danych do użycia (domyślnie 100).

**-h, --pomoc**  Pomoc na ławce

**--log-level** string Zdefiniuj szczegółowość dziennika dla pliku dziennika, dostępne poziomy: INFO (wszystkie żądania/odpowiedzi), OSTRZEŻENIE (powolne odpowiedzi), ERROR (tylko żądania nieudane) i NONE (brak dzienników wyjściowych). (domyślnie "INFO")

**--put-md5**  Utwórz skrót MD5 każdego pliku i zapisz skrót jako właściwość Content-MD5 docelowego obiektu blob/file. (Domyślnie skrót NIE jest tworzony). Identyczne z tym samym parametrem w poleceniu copy.

**--size-per-file** string Rozmiar każdego automatycznie wygenerowanego pliku danych. Musi być numerem bezpośrednio po K, M lub G. Np. 12k lub 200G (domyślnie "250M").

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

**--cap-mbps uint32**  Limity szybkości transferu w megabitach na sekundę. Przepływność moment po chwili może się nieznacznie różnić od limitu. Jeśli ta opcja jest ustawiona na zero lub jest pomijana, przepływność nie jest ograniczona.

**--output-type** string Format wyjścia polecenia. Do wyboru są: tekst, json. Wartością domyślną jest "text". (domyślny "tekst").

## <a name="see-also"></a>Zobacz też

- [azcopy](storage-ref-azcopy.md)
