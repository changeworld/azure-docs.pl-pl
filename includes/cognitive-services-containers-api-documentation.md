---
author: diberry
ms.author: v-junlch
ms.service: cognitive-services
ms.topic: include
origin.date: 03/25/2019
ms.date: 04/23/2019
ms.openlocfilehash: 94e95864d8bac2d6dc0ff690a2a8f53bd2db5a40
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60598894"
---
## <a name="validate-container-is-running"></a>Sprawdź poprawność kontener jest uruchomiony 

Istnieje kilka sposobów, aby sprawdzić kontener jest uruchomiony: 

|Żądanie|Przeznaczenie|
|--|--|
|`http://localhost:5000/`|Kontener zawiera stronę główną.|
|`http://localhost:5000/status`|Zażądano za pomocą GET, można zweryfikować kontener jest uruchomiony bez powodowania kwerendę punktu końcowego. Może być używany dla rozwiązania Kubernetes [sondy żywotności i gotowość](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).|
|`http://localhost:5000/swagger`|Kontener zawiera pełny zestaw dokumentacji dla punktów końcowych, a także `Try it now` funkcji. Ta funkcja umożliwia wprowadzanie ustawień do formularza HTML, oparte na sieci web i utworzyć zapytanie bez konieczności pisania kodu. Gdy zapytanie zwraca przykład polecenia CURL jest dostarczany w celu pokazują nagłówków HTTP i treści w formacie wymaganym. |

![Strona główna kontenera](./media/cognitive-services-containers-api-documentation/container-webpage.png)

