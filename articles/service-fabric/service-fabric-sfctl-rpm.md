---
title: Sieci szkieletowej usługi Azure CLI- sfctl rpm
description: Dowiedz się więcej o sfctl, interfejsie wiersza polecenia sieci szkieletowej usługi Azure. Zawiera listę poleceń dla usługi menedżera napraw.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 1787c0c8a8d5994ce1f1e9e4c55f155bbca6934c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76904941"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Wysyłaj kwerendy i wysyłaj polecenia do usługi menedżera napraw.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| siła zatwierdzania | Wymusza zatwierdzenie danego zadania naprawczego. |
| delete | Usuwa ukończone zadanie naprawy. |
| list | Pobiera listę zadań naprawy pasujących do danego filtrów. |

## <a name="sfctl-rpm-approve-force"></a>sfctl rpm approve-force (siła zatwierdzania obr./min)
Wymusza zatwierdzenie danego zadania naprawczego.

Ten interfejs API obsługuje platformę sieci szkieletowej usług; nie jest przeznaczony do użycia bezpośrednio z kodu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --task-id [Wymagane] | Identyfikator zadania naprawy. |
| --wersja | Bieżący numer wersji zadania naprawy. Jeśli nie zero, żądanie zakończy się pomyślnie tylko wtedy, gdy ta wartość jest zgodna z rzeczywistą bieżącą wersją zadania naprawy. Jeśli zero, to nie jest wykonywane sprawdzanie wersji. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-rpm-delete"></a>sfctl rpm usunąć
Usuwa ukończone zadanie naprawy.

Ten interfejs API obsługuje platformę sieci szkieletowej usług; nie jest przeznaczony do użycia bezpośrednio z kodu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --task-id [Wymagane] | Identyfikator ukończonego zadania naprawy do usunięcia. |
| --wersja | Bieżący numer wersji zadania naprawy. Jeśli nie zero, żądanie zakończy się pomyślnie tylko wtedy, gdy ta wartość jest zgodna z rzeczywistą bieżącą wersją zadania naprawy. Jeśli zero, to nie jest wykonywane sprawdzanie wersji. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-rpm-list"></a>lista obrotów obrotowych sfctl
Pobiera listę zadań naprawy pasujących do danego filtrów.

Ten interfejs API obsługuje platformę sieci szkieletowej usług; nie jest przeznaczony do użycia bezpośrednio z kodu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --executor-filter | Nazwa wykonawcy naprawy, którego zadania roszczenia powinny być zawarte na liście. |
| --state-filter | Bitowy-OR z następujących wartości, określając, które stany zadań powinny być uwzględnione na liście wyników. <ul><li>1 - Utworzono</li><li>2 - Zgłoszone</li><li>4 - Przygotowanie</li><li>8 - Zatwierdzone</li><li>16 - Wykonanie</li><li>32 - Przywracanie</li><li>64 - Zakończono</li></ul>
| --task-id-filter | Prefiks identyfikatora zadania naprawy, który ma być dopasowany. |

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
