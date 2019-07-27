---
title: Instalowanie kontenerów mowy
titleSuffix: Azure Cognitive Services
description: Szczegóły opcji konfiguracji wykresu Helm zamiany mowy na tekst.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 1b46c58d3f3c804052e637f7bde2e1a456764dba
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "67717231"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Zamiana mowy na tekst (wykres podrzędny: wykresy/speechToText)

Aby zastąpić wykres "parasol", Dodaj prefiks `speechToText.` na dowolnym parametrze, aby uczynić go bardziej szczegółowym. Na przykład zastąpi odpowiedni parametr, np `speechToText.numberOfConcurrentRequest` . Overrides. `numberOfConcurrentRequest`

|Parametr|Opis|Domyślny|
| -- | -- | -- |
| `enabled` | Określa, czy jest włączona usługa **zamiany mowy na tekst** . | `false` |
| `numberOfConcurrentRequest` | Liczba równoczesnych żądań dla usługi **zamiany mowy na tekst** . Ten wykres automatycznie oblicza zasoby procesora i pamięci na podstawie tej wartości. | `2` |
| `optimizeForAudioFile`| Czy usługa musi zoptymalizować dane wejściowe audio za pośrednictwem plików audio. Jeśli `true`ten wykres przydzieli więcej zasobów procesora CPU do usługi. | `false` |
| `image.registry`| Rejestr **aparatu Docker zamiany mowy na tekst** . | `containerpreview.azurecr.io` |
| `image.repository` | Repozytorium obrazów **zamiany mowy na tekst** . | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | Tag obrazu **zamiany mowy na tekst** . | `latest` |
| `image.pullSecrets` | Wpisy tajne obrazu służące do ściągania obrazu platformy Docker **zamiany mowy na tekst** . | |
| `image.pullByHash`| Czy obraz platformy Docker jest ściągany przez skrót. Jeśli `true`jesttowymagane `image.hash` . | `false` |
| `image.hash`| Wartość skrótu obrazu platformy Docker **zamiany mowy na tekst** . Używane tylko wtedy `image.pullByHash: true`, gdy.  | |
| `image.args.eula`potrzeb | Oznacza, że licencja została zaakceptowana. Jedyna prawidłowa wartość to`accept` | |
| `image.args.billing`potrzeb | Wartość identyfikatora URI punktu końcowego rozliczenia jest dostępna na stronie Przegląd mowy Azure Portal. | |
| `image.args.apikey`potrzeb | Używane do śledzenia informacji dotyczących rozliczeń. ||
| `service.type` | Typ usługi Kubernetes dla usługi **zamiany mowy na tekst** . Zapoznaj się z instrukcjami dotyczącymi [typów usługi Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) , aby uzyskać więcej informacji i zweryfikować obsługę dostawcy chmury. | `LoadBalancer` |
| `service.port`|  Port usługi zamiany **mowy na tekst** . | `80` |
| `service.autoScaler.enabled` | Określa, czy jest włączona funkcja [automatycznego skalowania w poziomie](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) . `true` Jeśli`speech-to-text-autoscaler` program zostanie wdrożony w klastrze Kubernetes. | `true` |
| `service.podDisruption.enabled` | Czy jest włączony [budżet](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) w przypadku przerwania działania. `true` Jeśli`speech-to-text-poddisruptionbudget` program zostanie wdrożony w klastrze Kubernetes. | `true` |