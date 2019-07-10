---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 00cc63f53388ab7bea05a0b55784247f63477684
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704294"
---
## <a name="validate-that-a-container-is-running"></a>Sprawdzić, czy kontener jest uruchomiona 

Istnieje kilka sposobów, aby zweryfikować, że kontener został uruchomiony. 

|Żądanie|Przeznaczenie|
|--|--|
|`http://localhost:5000/`|Kontener zawiera strony głównej.|
|`http://localhost:5000/status`|Żądanie za pomocą GET, aby zweryfikować, że kontener został uruchomiony bez powodowania kwerendę punktu końcowego. To żądanie może służyć do rozwiązania Kubernetes [sondy żywotności i gotowość](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).|
|`http://localhost:5000/swagger`|Kontener zawiera pełny zestaw dokumentacji dla punktów końcowych i `Try it now` funkcji. Za pomocą tej funkcji możesz wprowadzić ustawienia do formularza HTML opartych na sieci web i wprowadzić zapytania bez konieczności pisania kodu. Zapytanie zwraca, przykładowe polecenie programu CURL jest świadczona wykazanie, że format nagłówków HTTP i treści jest to wymagane. |

![Strona główna kontenera](./media/cognitive-services-containers-api-documentation/container-webpage.png)
