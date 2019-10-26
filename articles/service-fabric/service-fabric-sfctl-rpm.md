---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — sfctl RPM | Microsoft Docs
description: Opisuje polecenia Service Fabric CLI sfctl RPM.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 9c37dc8131f14a3b35e68b3e88502c91f96810f6
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901032"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Wykonywanie zapytań i wysyłanie poleceń do usługi Repair Manager.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| Zatwierdź — Wymuś | Wymusza zatwierdzenie danego zadania naprawy. |
| delete | Usuwa zakończone zadanie naprawy. |
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
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji i przykładów, zobacz http\://jmespath.org/. |
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
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji i przykładów, zobacz http\://jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-rpm-list"></a>Lista sfctl RPM
Pobiera listę zadań naprawczych zgodnych z podanym filtrem.

Ten interfejs API obsługuje platformę Service Fabric. nie jest przeznaczona do użycia bezpośrednio w kodzie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --wykonawcze-Filter | Nazwa programu wykonującego naprawy, którego odpowiednie zadania powinny znajdować się na liście. |
| --State-Filter | Bitowe lub następujące wartości określające, które Stany zadań mają być uwzględnione na liście wyników. <br> 1 — utworzono <br>2 — zatwierdzono  <br>4 — przygotowywanie  <br>8 — zatwierdzone  <br>16 — wykonywanie  <br>32 — przywracanie  <br>64 — ukończono |
| --Task-ID-Filter | Prefiks identyfikatora zadania naprawy, który ma zostać dopasowany. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji i przykładów, zobacz http\://jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |


## <a name="next-steps"></a>Następne kroki
- [Skonfiguruj](service-fabric-cli.md) interfejs wiersza polecenia Service Fabric.
- Dowiedz się, jak używać interfejsu wiersza polecenia Service Fabric przy użyciu [przykładowych skryptów](/azure/service-fabric/scripts/sfctl-upgrade-application).