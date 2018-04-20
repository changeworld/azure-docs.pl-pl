---
title: Azure Service Fabric interfejsu wiersza polecenia sfctl obr | Dokumentacja firmy Microsoft
description: Zawiera opis poleceń interfejsu wiersza polecenia usługi sieć szkieletowa sfctl obr. / min.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 02/22/2018
ms.author: ryanwi
ms.openlocfilehash: 34e7693ea40df2bf12fd6e9be2ef627f30748bcd
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="sfctl-rpm"></a>sfctl rpm
Zapytania i wysyłać polecenia do naprawy service manager.

## <a name="commands"></a>Polecenia
|Polecenie|Opis|
| --- | --- |
|    approve-force| Wymusza zatwierdzenie naprawy danego zadania.|
|    usuwanie       | Usuwa zadanie naprawy ukończone.|
|    lista         | Pobiera listę spełniających kryteria filtrów danego zadania naprawy.|

## <a name="sfctl-rpm-delete"></a>Usuń sfctl obr. / min
Usuwa zadanie naprawy ukończone.

Ten interfejs API obsługuje platformy Service Fabric; nie jest przeznaczona do użycia bezpośrednio w kodzie. 

### <a name="arguments"></a>Argumenty
|Argument|Opis|
| --- | --- |
|    — Identyfikator zadania [wymagane]| Identyfikator zadania naprawy ukończone do usunięcia.|
|    --wersji           | Bieżący numer wersji zadania naprawy. Jeśli niezerową, żądanie rozpocznie się tylko, jeśli ta wartość odpowiada rzeczywiste aktualnej wersji zadania naprawy. Jeśli zero, następnie nie wersji jest sprawdzane.|

### <a name="global-arguments"></a>Argumenty globalne
|Argument|Opis|
| --- | --- |
|    --debug             | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
|    — Pomoc -h           | Pokaż ten komunikat pomocy i Zakończ.|
|    --output -o         | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.
|    — zapytania             | Ciąg zapytania JMESPath. Zobacz http://jmespath.org/ dodatkowe informacje i przykłady.|
|    -verbose           | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|


## <a name="sfctl-rpm-list"></a>sfctl listy obr. / min
Pobiera listę spełniających kryteria filtrów danego zadania naprawy.

Ten interfejs API obsługuje platformy Service Fabric; nie jest przeznaczona do użycia bezpośrednio w kodzie. 

### <a name="arguments"></a>Argumenty
|Argument|Opis|
| --- | --- |
|    --executor-filter| Nazwa modułu wykonującego naprawy, którego oświadczeniem zadania powinny być uwzględnione w liście.|
|    --state-filter   | Alternatywy następujące wartości, określając stany zadania powinny uwzględnione na liście wyników. -1 - utworzony - 2 - oświadczeniem — 4. przygotowywanie - 8 - zatwierdzone - 16 - wykonywania - 64 32 - przywracania - — ukończone.|
|    --task-id-filter | Prefiks Identyfikatora zadania naprawy do dopasowania.|

### <a name="global-arguments"></a>Argumenty globalne
|Argument|Opis|
| --- | --- |
|    --debug          | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
|    — Pomoc -h        | Pokaż ten komunikat pomocy i Zakończ.|
|    --output -o      | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne| JSON.|
|    — zapytania          | Ciąg zapytania JMESPath. Zobacz http://jmespath.org/ dodatkowe informacje i przykłady.|
|    -verbose        | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia usługi sieci szkieletowej.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi sieć szkieletowa [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).