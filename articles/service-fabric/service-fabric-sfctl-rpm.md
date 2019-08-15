---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — sfctl RPM | Microsoft Docs
description: Opisuje polecenia Service Fabric CLI sfctl RPM.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 3f40451087aba5af5b02625ad3ac1ca6231d976c
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035887"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Wykonywanie zapytań i wysyłanie poleceń do usługi Repair Manager.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| approve-force | Wymusza zatwierdzenie danego zadania naprawy. |
| usuwanie | Usuwa zakończone zadanie naprawy. |
| list | Pobiera listę zadań naprawczych zgodnych z podanym filtrem. |

## <a name="sfctl-rpm-approve-force"></a>sfctl RPM — Zatwierdź — Wymuś
Wymusza zatwierdzenie danego zadania naprawy.

Ten interfejs API obsługuje platformę Service Fabric. nie jest przeznaczona do użycia bezpośrednio w kodzie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Task-ID [wymagane] | Identyfikator zadania naprawy. |
| --Version | Bieżący numer wersji zadania naprawy. Jeśli wartość jest różna od zera, żądanie zakończy się powodzeniem tylko wtedy, gdy zostanie ona zgodna z rzeczywistą bieżącą wersją zadania naprawy. Jeśli wartość jest równa zero, Sprawdzanie wersji nie jest wykonywane. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, Table, TSV.  Domyślny\: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać\:więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-rpm-delete"></a>sfctl RPM
Usuwa zakończone zadanie naprawy.

Ten interfejs API obsługuje platformę Service Fabric. nie jest przeznaczona do użycia bezpośrednio w kodzie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Task-ID [wymagane] | Identyfikator ukończonego zadania naprawy, które ma zostać usunięte. |
| --Version | Bieżący numer wersji zadania naprawy. Jeśli wartość jest różna od zera, żądanie zakończy się powodzeniem tylko wtedy, gdy zostanie ona zgodna z rzeczywistą bieżącą wersją zadania naprawy. Jeśli wartość jest równa zero, Sprawdzanie wersji nie jest wykonywane. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, Table, TSV.  Domyślny\: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać\:więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-rpm-list"></a>Lista sfctl RPM
Pobiera listę zadań naprawczych zgodnych z podanym filtrem.

Ten interfejs API obsługuje platformę Service Fabric. nie jest przeznaczona do użycia bezpośrednio w kodzie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --executor-filter | Nazwa programu wykonującego naprawy, którego odpowiednie zadania powinny znajdować się na liście. |
| --state-filter | Bitowe lub następujące wartości określające, które Stany zadań mają być uwzględnione na liście wyników. <br> 1 — utworzono <br>2 — zatwierdzono  <br>4 — przygotowywanie  <br>8 — zatwierdzone  <br>16 — wykonywanie  <br>32 — przywracanie  <br>64 — ukończono |
| --task-id-filter | Prefiks identyfikatora zadania naprawy, który ma zostać dopasowany. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, Table, TSV.  Domyślny\: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać\:więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |


## <a name="next-steps"></a>Następne kroki
- [Skonfiguruj](service-fabric-cli.md) interfejs wiersza polecenia Service Fabric.
- Dowiedz się, jak używać interfejsu wiersza polecenia Service Fabric przy użyciu [przykładowych skryptów](/azure/service-fabric/scripts/sfctl-upgrade-application).