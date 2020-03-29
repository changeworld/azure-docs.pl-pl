---
title: Instalowanie kontenerów mowy
titleSuffix: Azure Cognitive Services
description: Szczegółowe opcje konfiguracji wykresu helm text-to-speech.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: dapine
ms.openlocfilehash: c87132c3ae41dec82e3493f9a0ee2397455ff881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "69971344"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Zamiana tekstu na mowę (podobsce: wykresy/textToSpeech)

Aby zastąpić wykres "parasol", dodaj prefiks `textToSpeech.` do dowolnego parametru, aby był bardziej szczegółowy. Na przykład zastąpi odpowiedni parametr, `textToSpeech.numberOfConcurrentRequest` na przykład zastępuje `numberOfConcurrentRequest`.

|Parametr|Opis|Domyślne|
| -- | -- | -- |
| `enabled` | Czy usługa **zamiany tekstu na mowę** jest włączona. | `false` |
| `numberOfConcurrentRequest` | Liczba równoczesnych żądań dla usługi **zamiany tekstu na mowę.** Ten wykres automatycznie oblicza zasoby procesora CPU i pamięci na podstawie tej wartości. | `2` |
| `optimizeForTurboMode`| Czy usługa musi zoptymalizować do wprowadzania tekstu za pomocą plików tekstowych. Jeśli `true`ten wykres przydzieli więcej zasobów procesora DO serwisu. | `false` |
| `image.registry`| Rejestr obrazów dokowania **zamiany tekstu na mowę.** | `containerpreview.azurecr.io` |
| `image.repository` | Repozytorium obrazów dokowania **zamiany tekstu na mowę.** | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | Znacznik obrazu dokowane **zamiany tekstu na mowę.** | `latest` |
| `image.pullSecrets` | Wpisy tajne obrazu do ciągnięcia obrazu dokowego **zamiany tekstu na mowę.** | |
| `image.pullByHash`| Czy obraz docker jest ściągnięty przez skrót. Jeśli `true` `image.hash` wymagane jest. | `false` |
| `image.hash`| Skrót dokowy **zamiany tekstu na mowę.** Używane tylko `image.pullByHash: true`wtedy, gdy .  | |
| `image.args.eula`(wymagane) | Wskazuje, że licencja została zaakceptowana. Jedyną prawidłową wartością jest`accept` | |
| `image.args.billing`(wymagane) | Wartość identyfikatora URI punktu końcowego rozliczeń jest dostępna na stronie Omówienie mowy w portalu Azure. | |
| `image.args.apikey`(wymagane) | Służy do śledzenia informacji rozliczeniowych. ||
| `service.type` | Typ usługi Kubernetes usługi **zamiany tekstu na mowę.** Zobacz [instrukcje typów usług Kubernetes,](https://kubernetes.io/docs/concepts/services-networking/service/) aby uzyskać więcej informacji i zweryfikować obsługę dostawcy chmury. | `LoadBalancer` |
| `service.port`|  Port usługi **zamiany tekstu na mowę.** | `80` |
| `service.annotations` | Adnotacje **zamiany tekstu** na mowę dla metadanych usługi. Adnotacje są parami kluczowych wartości. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Czy [skalowanie automatyczne podążek poziomych](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) jest włączone. Jeśli `true`zostanie `text-to-speech-autoscaler` wdrożony w klastrze Kubernetes. | `true` |
| `service.podDisruption.enabled` | Czy [budżet zakłócenia pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) jest włączona. Jeśli `true`zostanie `text-to-speech-poddisruptionbudget` wdrożony w klastrze Kubernetes. | `true` |