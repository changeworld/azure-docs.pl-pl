---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — magazyn sfctl
description: Dowiedz się więcej na temat sfctl, interfejsu wiersza polecenia platformy Azure Service Fabric. Zawiera listę poleceń służących do wykonywania operacji na poziomie plików w magazynie obrazów cluter.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 64a8ea963afa9e20dfafb71db69b2baf66959369
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645263"
---
# <a name="sfctl-store"></a>sfctl store
Wykonaj podstawowe operacje na poziomie plików w magazynie obrazów klastra.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| delete | Usuwa istniejącą zawartość magazynu obrazów. |
| root-info | Pobiera informacje o zawartości w katalogu głównym magazynu obrazów. |
| Określ | Pobiera informacje o zawartości magazynu obrazu. |

## <a name="sfctl-store-delete"></a>Usuwanie magazynu sfctl
Usuwa istniejącą zawartość magazynu obrazów.

Usuwa istniejącą zawartość magazynu obrazu znajdującą się w danej ścieżce względnej magazynu obrazu. To polecenie służy do usuwania przekazanych pakietów aplikacji po ich udostępnieniu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Content-Path [wymagane] | Ścieżka względna do pliku lub folderu w magazynie obrazów ze swojego katalogu głównego. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji i przykładów, zobacz http\://jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-store-root-info"></a>Katalog główny sklepu sfctl — informacje
Pobiera informacje o zawartości w katalogu głównym magazynu obrazów.

Zwraca informacje o zawartości magazynu obrazu w katalogu głównym magazynu obrazów.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji i przykładów, zobacz http\://jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-store-stat"></a>Statystyka sklepu sfctl
Pobiera informacje o zawartości magazynu obrazu.

Zwraca informacje o zawartości magazynu obrazu w określonym contentPath. ContentPath jest względem katalogu głównego magazynu obrazów.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Content-Path [wymagane] | Ścieżka względna do pliku lub folderu w magazynie obrazów ze swojego katalogu głównego. |
| --timeout-t | Limit czasu serwera na potrzeby wykonywania operacji w sekundach. Ten limit czasu określa czas, przez jaki klient chce czekać na zakończenie wymaganej operacji. Wartość domyślna tego parametru to 60 sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji i przykładów, zobacz http\://jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |


## <a name="next-steps"></a>Następne kroki
- [Skonfiguruj](service-fabric-cli.md) interfejs wiersza polecenia Service Fabric.
- Dowiedz się, jak używać interfejsu wiersza polecenia Service Fabric przy użyciu [przykładowych skryptów](/azure/service-fabric/scripts/sfctl-upgrade-application).