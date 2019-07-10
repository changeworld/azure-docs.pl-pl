---
title: Zainstaluj kontenery mowy
titleSuffix: Azure Cognitive Services
description: Szczegóły opcje konfiguracji wykresu helm zamiany tekstu na mowę.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: e6c7dcd3015b0b8ab5b3c719ebd2397bc814b81a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717213"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Zamiana tekstu na mowę (podrzędnych wykresu: wykresy/textToSpeech)

Aby zastąpić wykresu "ogólny", Dodaj prefiks `textToSpeech.` na dowolnym parametr, aby utworzyć bardziej szczegółowe. Na przykład zostanie zastąpione odpowiadającego mu parametru np. `textToSpeech.numberOfConcurrentRequest` zastępuje `numberOfConcurrentRequest`.

|Parametr|Opis|Domyślny|
| -- | -- | -- |
| `enabled` | Czy **zamiany tekstu na mowę** usługa jest włączona. | `false` |
| `numberOfConcurrentRequest` | Liczba równoczesnych żądań dotyczących **zamiany tekstu na mowę** usługi. Ten wykres automatycznie oblicza zasobów Procesora i pamięci, w oparciu o tę wartość. | `2` |
| `optimizeForTurboMode`| Czy usługa potrzebuje Optymalizuj pod kątem tekstowego za pomocą plików tekstowych. Jeśli `true`, ten wykres spowoduje przydzielenie więcej zasobów procesora CPU do usługi. | `false` |
| `image.registry`| **Zamiany tekstu na mowę** rejestrem obrazów docker. | `containerpreview.azurecr.io` |
| `image.repository` | **Zamiany tekstu na mowę** repozytorium obrazów platformy docker. | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | **Zamiany tekstu na mowę** tag obrazu platformy docker. | `latest` |
| `image.pullSecrets` | Wpisy tajne obrazu dla ściąganie **zamiany tekstu na mowę** obrazu platformy docker. | |
| `image.pullByHash`| Czy obrazu platformy docker zostanie ściągnięty przez wyznaczania wartości skrótu. Jeśli `true`, `image.hash` jest wymagana. | `false` |
| `image.hash`| **Zamiany tekstu na mowę** skrót obrazu platformy docker. Używana tylko w przypadku `image.pullByHash: true`.  | |
| `image.args.eula` (wymagane) | Wskazuje, że zostały przez Ciebie zaakceptowane licencji. Jedyna prawidłowa wartość to `accept` | |
| `image.args.billing` (wymagane) | Punkt końcowy rozliczeń wartość identyfikatora URI jest dostępna na stronie Przegląd rozpoznawania mowy w witrynie Azure portal. | |
| `image.args.apikey` (wymagane) | Używane do śledzenia informacji dotyczących rozliczeń. ||
| `service.type` | Usługi platformy Kubernetes typu **zamiany tekstu na mowę** usługi. Zobacz [instrukcje typy usługi Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) Aby uzyskać więcej informacji i sprawdź informacje o obsłudze dostawców chmury. | `LoadBalancer` |
| `service.port`|  Port **zamiany tekstu na mowę** usługi. | `80` |
| `service.autoScaler.enabled` | Czy [poziomy skalowania automatycznego zasobników](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) jest włączona. Jeśli `true`, `text-to-speech-autoscaler` zostanie wdrożona w klastrze Kubernetes. | `true` |
| `service.podDisruption.enabled` | Czy [budżetu przerw w działaniu zasobnika](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) jest włączona. Jeśli `true`, `text-to-speech-poddisruptionbudget` zostanie wdrożona w klastrze Kubernetes. | `true` |