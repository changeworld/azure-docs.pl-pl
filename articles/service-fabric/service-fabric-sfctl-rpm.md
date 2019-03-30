---
title: Usługa Azure usługi Service Fabric interfejsu wiersza polecenia sfctl obr. / min | Dokumentacja firmy Microsoft
description: Zawiera opis poleceń interfejsu wiersza polecenia usługi Service Fabric sfctl obr. / min.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 04080d75042bfa8a07533336936165e0abef051b
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664136"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Zapytania i wysyłanie poleceń do usługi Menedżer naprawy.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| approve-force | Wymusza zatwierdzenia zadanie naprawy danego. |
| delete | Usuwa zadanie naprawy ukończone. |
| list | Pobiera listę zadań naprawy pasujące do danych filtrów. |

## <a name="sfctl-rpm-approve-force"></a>Zatwierdź obr. / min sfctl-force
Wymusza zatwierdzenia zadanie naprawy danego.

Ten interfejs API obsługuje platformy usługi Service Fabric; nie jest przeznaczona do użycia bezpośrednio w kodzie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator zadania [wymagane] | Identyfikator zadania naprawy. |
| --version | Bieżący numer wersji zadania naprawy. Jeśli różna od zera, żądanie możliwe tylko jeśli ta wartość odpowiada rzeczywiste aktualnej wersji zadania naprawy. Jeśli zero, jest wykonywane żadne sprawdzenie wersji. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-rpm-delete"></a>Usuń interfejs sfctl obr. / min
Usuwa zadanie naprawy ukończone.

Ten interfejs API obsługuje platformy usługi Service Fabric; nie jest przeznaczona do użycia bezpośrednio w kodzie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator zadania [wymagane] | Identyfikator zadania naprawy ukończone do usunięcia. |
| --version | Bieżący numer wersji zadania naprawy. Jeśli różna od zera, żądanie możliwe tylko jeśli ta wartość odpowiada rzeczywiste aktualnej wersji zadania naprawy. Jeśli zero, jest wykonywane żadne sprawdzenie wersji. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-rpm-list"></a>Interfejs sfctl obr. / min listy
Pobiera listę zadań naprawy pasujące do danych filtrów.

Ten interfejs API obsługuje platformy usługi Service Fabric; nie jest przeznaczona do użycia bezpośrednio w kodzie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --executor-filter | Nazwa wykonawca naprawy, w których oświadczeniem zadania powinny być objęte listy. |
| --state-filter | Alternatywy następujące wartości, określając stanów zadań, które powinny być objęte listy wyników. <br> 1 — utworzone <br>2 - zgłoszone  <br>4 — przygotowywanie  <br>8 - zatwierdzone  <br>16 - wykonywania  <br>32 - przywracania  <br>64 - ukończone |
| --task-id-filter | Prefiks Identyfikatora zadania naprawy do dopasowania. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |


## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia usługi Service Fabric.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi Service Fabric [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).