---
title: Instalowanie kontenerów mowy
titleSuffix: Azure Cognitive Services
description: Szczegóły dotyczące opcji konfiguracji wykresu Helm zamiany tekstu na mowę.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: dapine
ms.openlocfilehash: c87132c3ae41dec82e3493f9a0ee2397455ff881
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971344"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Zamiana tekstu na mowę (wykres podrzędny: wykresy/textToSpeech)

Aby zastąpić wykres "parasol", Dodaj prefiks `textToSpeech.` na dowolnym parametrze, aby uczynić go bardziej szczegółowym. Na przykład zastąpi odpowiedni parametr na przykład `textToSpeech.numberOfConcurrentRequest` zastąpień. `numberOfConcurrentRequest`

|Parametr|Opis|Domyślny|
| -- | -- | -- |
| `enabled` | Czy usługa **zamiany tekstu na mowę** jest włączona. | `false` |
| `numberOfConcurrentRequest` | Liczba równoczesnych żądań dla usługi **zamiany tekstu na mowę** . Ten wykres automatycznie oblicza zasoby procesora i pamięci na podstawie tej wartości. | `2` |
| `optimizeForTurboMode`| Czy usługa musi zoptymalizować do wprowadzania tekstu za pośrednictwem plików tekstowych. Jeśli `true`ten wykres przydzieli więcej zasobów procesora CPU do usługi. | `false` |
| `image.registry`| Rejestr obrazu platformy Docker **zamiany tekstu na mowę** . | `containerpreview.azurecr.io` |
| `image.repository` | Repozytorium obrazów platformy Docker **zamiany tekstu na mowę** . | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | Tag obrazu aparatu Docker **zamiany tekstu na mowę** . | `latest` |
| `image.pullSecrets` | Wpisy tajne obrazu służące do ściągania obrazu platformy Docker **zamiany tekstu na mowę** . | |
| `image.pullByHash`| Czy obraz platformy Docker jest ściągany przez skrót. Jeśli `true`jesttowymagane `image.hash` . | `false` |
| `image.hash`| Skrót obrazu platformy Docker **zamiany tekstu na mowę** . Używane tylko wtedy `image.pullByHash: true`, gdy.  | |
| `image.args.eula`potrzeb | Oznacza, że licencja została zaakceptowana. Jedyna prawidłowa wartość to`accept` | |
| `image.args.billing`potrzeb | Wartość identyfikatora URI punktu końcowego rozliczenia jest dostępna na stronie Przegląd mowy Azure Portal. | |
| `image.args.apikey`potrzeb | Używane do śledzenia informacji dotyczących rozliczeń. ||
| `service.type` | Typ usługi Kubernetes dla usługi **zamiany tekstu na mowę** . Zapoznaj się z instrukcjami dotyczącymi [typów usługi Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) , aby uzyskać więcej informacji i zweryfikować obsługę dostawcy chmury. | `LoadBalancer` |
| `service.port`|  Port usługi zamiany **tekstu na mowę** . | `80` |
| `service.annotations` | Adnotacje **zamiany tekstu na mowę** dla metadanych usługi. Adnotacje to pary klucz-wartość. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Określa, czy jest włączona funkcja [automatycznego skalowania w poziomie](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) . `true` Jeśli`text-to-speech-autoscaler` program zostanie wdrożony w klastrze Kubernetes. | `true` |
| `service.podDisruption.enabled` | Czy jest włączony [budżet](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) w przypadku przerwania działania. `true` Jeśli`text-to-speech-poddisruptionbudget` program zostanie wdrożony w klastrze Kubernetes. | `true` |