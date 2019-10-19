---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 10/08/2019
ms.openlocfilehash: a7ae6cb1231e4c202dfd0a39602c03b33099d088
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554720"
---
Rozpocznij korzystanie z usługi wykrywania anomalii, tworząc jeden z poniższych zasobów platformy Azure.

* [Zasób próbny](https://azure.microsoft.com/try/cognitive-services/#decision) (nie jest wymagana subskrypcja platformy Azure): 
    * Ważne przez siedem dni bezpłatnie. Po zarejestrowaniu się w [witrynie sieci Web platformy Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/)będzie dostępny klucz wersji próbnej i punkt końcowy. 
    * Jest to doskonałe rozwiązanie, jeśli chcesz wypróbować detektor anomalii, ale nie masz subskrypcji platformy Azure.

* [Zasób wykrywania anomalii](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector):
    * Dostępne w [Azure Portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade) do momentu usunięcia zasobu.
    * Skorzystaj z warstwy cenowej bezpłatna do wypróbowania usługi i przeprowadź uaktualnienie później do warstwy płatnej dla środowiska produkcyjnego.

### <a name="create-an-environment-variable"></a>Utwórz zmienną środowiskową

>[!NOTE]
> Punkty końcowe dla zasobów nieprzeznaczonych dla wersji próbnej utworzonych po 1 lipca 2019 używają niestandardowego formatu poddomen pokazanego poniżej. Aby uzyskać więcej informacji i pełną listę regionalnych punktów końcowych, zobacz [niestandardowe nazwy domen poddomen dla Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Przy użyciu klucza i punktu końcowego z utworzonego zasobu Utwórz dwa zmienne środowiskowe do uwierzytelnienia:

* `ANOMALY_DETECTOR_KEY` — klucz zasobu do uwierzytelniania żądań.
* `ANOMALY_DETECTOR_ENDPOINT` — punkt końcowy zasobu do wysyłania żądań interfejsu API. Będzie wyglądać następująco: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Skorzystaj z instrukcji dotyczących systemu operacyjnego.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx ANOMALY_DETECTOR_KEY your-anomaly-detector-key
setx ANOMALY_DETECTOR_ENDPOINT your-anomaly-detector-endpoint
```

Po dodaniu zmiennej środowiskowej Uruchom ponownie okno konsoli.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export ANOMALY_DETECTOR_KEY=your-anomaly-detector-key
export ANOMALY_DETECTOR_ENDPOINT=your-anomaly-detector-endpoint
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source ~/.bashrc` z okna konsoli, aby zmiany zostały uwzględnione.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Edytuj `.bash_profile` i Dodaj zmienną środowiskową:

```bash
export ANOMALY_DETECTOR_KEY=your-anomaly-detector-key
export ANOMALY_DETECTOR_ENDPOINT=your-anomaly-detector-endpoint
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source .bash_profile` z okna konsoli, aby zmiany zostały uwzględnione.

***