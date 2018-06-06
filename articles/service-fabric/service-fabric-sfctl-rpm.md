---
title: Azure Service Fabric interfejsu wiersza polecenia sfctl obr | Dokumentacja firmy Microsoft
description: Zawiera opis poleceń interfejsu wiersza polecenia usługi sieć szkieletowa sfctl obr. / min.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: 5e7cf1b28e8f38f0539e0084e1f745e0dab4dec3
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763616"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Zapytania i wysyłać polecenia do naprawy service manager.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| approve-force | Wymusza zatwierdzenie naprawy danego zadania. |
| usuwanie | Usuwa zadanie naprawy ukończone. |
| lista | Pobiera listę spełniających kryteria filtrów danego zadania naprawy. |

## <a name="sfctl-rpm-approve-force"></a>Zatwierdzanie obr. / min sfctl-force
Wymusza zatwierdzenie naprawy danego zadania.

Ten interfejs API obsługuje platformy Service Fabric; nie jest przeznaczona do użycia bezpośrednio w kodzie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator zadania [wymagane] | Identyfikator zadania naprawy. |
| --wersji | Bieżący numer wersji zadania naprawy. Jeśli niezerową, żądanie rozpocznie się tylko, jeśli ta wartość odpowiada rzeczywiste aktualnej wersji zadania naprawy. Jeśli zero, następnie nie wersji jest sprawdzane. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-rpm-delete"></a>Usuń sfctl obr. / min
Usuwa zadanie naprawy ukończone.

Ten interfejs API obsługuje platformy Service Fabric; nie jest przeznaczona do użycia bezpośrednio w kodzie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator zadania [wymagane] | Identyfikator zadania naprawy ukończone do usunięcia. |
| --wersji | Bieżący numer wersji zadania naprawy. Jeśli niezerową, żądanie rozpocznie się tylko, jeśli ta wartość odpowiada rzeczywiste aktualnej wersji zadania naprawy. Jeśli zero, następnie nie wersji jest sprawdzane. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-rpm-list"></a>sfctl listy obr. / min
Pobiera listę spełniających kryteria filtrów danego zadania naprawy.

Ten interfejs API obsługuje platformy Service Fabric; nie jest przeznaczona do użycia bezpośrednio w kodzie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --executor-filter | Nazwa modułu wykonującego naprawy, którego oświadczeniem zadania powinny być uwzględnione w liście. |
| --state-filter | Alternatywy następujące wartości, określając stany zadania powinny uwzględnione na liście wyników. <br> 1 - utworzone <br>2 - określona  <br>4 - przygotowania  <br>8 - zatwierdzenia  <br>16 - wykonywania  <br>32 - Przywracanie  <br>64 - ukończone |
| --task-id-filter | Prefiks Identyfikatora zadania naprawy do dopasowania. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia usługi sieci szkieletowej.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi sieć szkieletowa [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).