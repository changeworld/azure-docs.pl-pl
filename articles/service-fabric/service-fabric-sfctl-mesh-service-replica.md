---
title: Azure Service Fabric interfejsu wiersza polecenia — interfejs sfctl siatki replik usługi | Dokumentacja firmy Microsoft
description: W tym artykule opisano poleceń interfejsu wiersza polecenia usługi Service Fabric sfctl siatki replik usługi.
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
ms.openlocfilehash: 1e0955ef2a52a6313f0449b956229e03f0a5b5e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61038452"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh service-replica
Pobierz szczegóły repliki i listy replik danej usługi, w zasobie aplikacji.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| list | Wyświetla listę wszystkich replik usług. |
| pokaż | Pobiera dany repliki usługi aplikacji. |

## <a name="sfctl-mesh-service-replica-list"></a>Interfejs sfctl siatki replik usługi listy
Wyświetla listę wszystkich replik usług.

Pobiera informacje o wszystkich replik usług. Informacje zawierają opis i inne właściwości repliki usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --— Nazwa aplikacji — Nazwa aplikacji [wymagane] | Nazwa aplikacji. |
| — [wymagane] nazwa usługi | Nazwa usługi. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-mesh-service-replica-show"></a>Interfejs sfctl siatki replik usługi show
Pobiera dany repliki usługi aplikacji.

Pobiera informacje o repliki usługi o podanej nazwie. Informacje zawierają opis i inne właściwości repliki usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --— Nazwa aplikacji — Nazwa aplikacji [wymagane] | Nazwa aplikacji. |
| — Nazwa -n [wymagane] | Nazwa repliki usługi. |
| — [wymagane] nazwa usługi | Nazwa usługi. |

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