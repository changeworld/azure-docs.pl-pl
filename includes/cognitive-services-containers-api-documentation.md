---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 081155c8984f39cc9cc8e905eb108c07dee98d2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "70034424"
---
## <a name="validate-that-a-container-is-running"></a>Sprawdzanie, czy kontener jest uruchomiony 

Istnieje kilka sposobów sprawdzania poprawności, że kontener jest uruchomiony. Znajdź *zewnętrzny* adres IP i odsłonięty port danego kontenera i otwórz ulubioną przeglądarkę internetową. Użyj różnych adresów URL żądań poniżej, aby sprawdzić, czy kontener jest uruchomiony. Przykładowe adresy URL `http://localhost:5000`żądań wymienione poniżej są , ale określony kontener może się różnić. Należy pamiętać, że masz polegać na *zewnętrznym* adresie IP kontenera i narażonym porcie.

| Adres URL żądania | Przeznaczenie |
|--|--|
| `http://localhost:5000/` | Kontener zawiera stronę główną. |
| `http://localhost:5000/status` | Wymagane z HTTP GET, aby sprawdzić, czy kontener jest uruchomiony bez konieczności kwerendy punktu końcowego. To żądanie może służyć do [sondy żywotności i gotowości](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)kubernetes . |
| `http://localhost:5000/swagger` | Kontener zawiera pełny zestaw dokumentacji dla punktów końcowych i **Wypróbuj go** funkcji. Dzięki tej funkcji można wprowadzić ustawienia w internetowym formularzu HTML i wykonać kwerendę bez konieczności pisania kodu. Po powrocie kwerendy, przykładowe polecenie CURL jest dostarczane w celu zademonstrowania nagłówków HTTP i formatu treści, który jest wymagany. |

![Strona główna kontenera](./media/cognitive-services-containers-api-documentation/container-webpage.png)
