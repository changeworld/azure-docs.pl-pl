---
title: Zainstaluj kontenery mowy
titleSuffix: Azure Cognitive Services
description: Szczegóły opcje konfiguracji wykresu helm mowy na tekst.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 1b46c58d3f3c804052e637f7bde2e1a456764dba
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711506"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Zamiany mowy na tekst (podrzędnych wykresu: wykresy/speechToText)

Aby zastąpić wykresu "ogólny", Dodaj prefiks `speechToText.` na dowolnym parametr, aby utworzyć bardziej szczegółowe. Na przykład zostanie zastąpione odpowiadającego mu parametru np. `speechToText.numberOfConcurrentRequest` zastępuje `numberOfConcurrentRequest`.

|Parametr|Opis|Domyślne|
| -- | -- | -- |
| `enabled` | Czy **mowy na tekst** usługa jest włączona. | `false` |
| `numberOfConcurrentRequest` | Liczba równoczesnych żądań dotyczących **mowy na tekst** usługi. Ten wykres automatycznie oblicza zasobów Procesora i pamięci, w oparciu o tę wartość. | `2` |
| `optimizeForAudioFile`| Czy usługa potrzebuje Optymalizuj pod kątem wejścia audio za pośrednictwem plików audio. Jeśli `true`, ten wykres spowoduje przydzielenie więcej zasobów procesora CPU do usługi. | `false` |
| `image.registry`| **Mowy na tekst** rejestrem obrazów docker. | `containerpreview.azurecr.io` |
| `image.repository` | **Mowy na tekst** repozytorium obrazów platformy docker. | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | **Mowy na tekst** tag obrazu platformy docker. | `latest` |
| `image.pullSecrets` | Wpisy tajne obrazu dla ściąganie **mowy na tekst** obrazu platformy docker. | |
| `image.pullByHash`| Czy obrazu platformy docker zostanie ściągnięty przez wyznaczania wartości skrótu. Jeśli `true`, `image.hash` jest wymagana. | `false` |
| `image.hash`| **Mowy na tekst** skrót obrazu platformy docker. Używana tylko w przypadku `image.pullByHash: true`.  | |
| `image.args.eula` (wymagane) | Wskazuje, że zostały przez Ciebie zaakceptowane licencji. Jedyna prawidłowa wartość to `accept` | |
| `image.args.billing` (wymagane) | Punkt końcowy rozliczeń wartość identyfikatora URI jest dostępna na stronie Przegląd rozpoznawania mowy w witrynie Azure portal. | |
| `image.args.apikey` (wymagane) | Używane do śledzenia informacji dotyczących rozliczeń. ||
| `service.type` | Usługi platformy Kubernetes typu **mowy na tekst** usługi. Zobacz [instrukcje typy usługi Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) Aby uzyskać więcej informacji i sprawdź informacje o obsłudze dostawców chmury. | `LoadBalancer` |
| `service.port`|  Port **mowy na tekst** usługi. | `80` |
| `service.autoScaler.enabled` | Czy [poziomy skalowania automatycznego zasobników](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) jest włączona. Jeśli `true`, `speech-to-text-autoscaler` zostanie wdrożona w klastrze Kubernetes. | `true` |
| `service.podDisruption.enabled` | Czy [budżetu przerw w działaniu zasobnika](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) jest włączona. Jeśli `true`, `speech-to-text-poddisruptionbudget` zostanie wdrożona w klastrze Kubernetes. | `true` |