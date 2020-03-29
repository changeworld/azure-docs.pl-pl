---
title: Magazyn interfejsu WIERSZ SIECI SZKIELETOWEJ usługi Azure
description: Dowiedz się więcej o sfctl, interfejsie wiersza polecenia sieci szkieletowej usługi Azure. Zawiera listę poleceń służących do wykonywania operacji na poziomie plików w magazynie obrazów klastra.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 75c62b54ff3aa7f3af344aa3e1ca81d431ae0ab2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905630"
---
# <a name="sfctl-store"></a>sfctl store
Wykonywanie podstawowych operacji na poziomie pliku w magazynie obrazów klastra.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| delete | Usuwa istniejącą zawartość magazynu obrazów. |
| root-info | Pobiera informacje o zawartości w katalogu głównym magazynu obrazów. |
| Stat | Pobiera informacje o zawartości magazynu obrazów. |

## <a name="sfctl-store-delete"></a>sfctl sklep usunąć
Usuwa istniejącą zawartość magazynu obrazów.

Usuwa istniejącą zawartość magazynu obrazów znajdującą się w danej ścieżce względnej magazynu obrazów. To polecenie może służyć do usuwania przekazanych pakietów aplikacji po ich zainicjowaniu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --content-path [Wymagane] | Ścieżka względna do pliku lub folderu w magazynie obrazów z jego katalogu głównego. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-store-root-info"></a>sfctl sklep root-info
Pobiera informacje o zawartości w katalogu głównym magazynu obrazów.

Zwraca informacje o zawartości magazynu obrazów w katalogu głównym magazynu obrazów.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-store-stat"></a>sfctl stat sklepu
Pobiera informacje o zawartości magazynu obrazów.

Zwraca informacje o zawartości magazynu obrazów w określonej contentPath. ContentPath jest względem katalogu głównego magazynu obrazów.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --content-path [Wymagane] | Ścieżka względna do pliku lub folderu w magazynie obrazów z jego katalogu głównego. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |


## <a name="next-steps"></a>Następne kroki
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia sieci szkieletowej usług.
- Dowiedz się, jak używać interfejsu wiersza polecenia sieci szkieletowej usług przy użyciu [przykładowych skryptów.](/azure/service-fabric/scripts/sfctl-upgrade-application)