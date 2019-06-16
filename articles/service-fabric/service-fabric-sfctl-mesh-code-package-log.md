---
title: Azure Service Fabric interfejsu wiersza polecenia — interfejs sfctl siatki dziennik w przypadku pakietu kodu | Dokumentacja firmy Microsoft
description: W tym artykule opisano poleceń interfejsu wiersza polecenia usługi Service Fabric sfctl siatki dziennik w przypadku pakietu kodu.
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
ms.openlocfilehash: e7bc8491071946eaa2e322517e5d36d681a49130
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60837644"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Pobierz dzienniki dla kontenera pakietu określony kod dla repliki danej usługi.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| Pobierz | Pobiera dzienniki z kontenera. |

## <a name="sfctl-mesh-code-package-log-get"></a>Pobierz dziennik w przypadku pakietu kodu siatki interfejsu sfctl
Pobiera dzienniki z kontenera.

Pobiera dzienniki dla kontenera pakietu określony kod repliki usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --— Nazwa aplikacji — Nazwa aplikacji [wymagane] | Nazwa aplikacji. |
| --Kod pakietu nazwa-[wymagane] | Nazwa pakietu kodu usługi. |
| — [wymagane] Nazwa repliki | Nazwa repliki usługi Service Fabric. |
| — [wymagane] nazwa usługi | Nazwa usługi. |
| --tail | Liczba wierszy do pokazania od końca dzienniki. Domyślna to 100. "wszystkie", aby wyświetlić pełne dzienniki. |

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