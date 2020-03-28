---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 10/08/2019
ms.openlocfilehash: 5089af4a4e1714d49b844a1b6823487a3f6a8dcf
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74483043"
---
Rozpocznij korzystanie z usługi Wykrywanie anomalii, tworząc jeden z poniższych zasobów platformy Azure.

* <a href="https://azure.microsoft.com/try/cognitive-services/#decision" target="_blank" rel="noopener">Tworzenie zasobu próbnego (otwiera się na nowej karcie)</a>
    * Nie jest wymagana subskrypcja platformy Azure: 
    * Ważne przez siedem dni, za darmo. Po zarejestrowaniu się klucz wersji próbnej i punkt końcowy będą dostępne w [witrynie sieci Web platformy Azure.](https://azure.microsoft.com/try/cognitive-services/my-apis/) 
    * Jest to świetna opcja, jeśli chcesz wypróbować Wykrywanie anomalii, ale nie masz subskrypcji platformy Azure.

* <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector" target="_blank" rel="noopener">Tworzenie zasobu detektora anomalii (otwiera się na nowej karcie)</a>:
    * Dostępne za pośrednictwem witryny Azure portal, dopóki nie usuniesz zasobu.
    * Użyj bezpłatnej warstwy cenowej, aby wypróbować usługę, a później uaktualnić do warstwy płatnej dla produkcji.



### <a name="create-an-environment-variable"></a>Tworzenie zmiennej środowiskowej

>[!NOTE]
> Punkty końcowe dla zasobów niepodstawowych utworzonych po 1 lipca 2019 r. używają niestandardowego formatu poddomeny przedstawionego poniżej. Aby uzyskać więcej informacji i pełną listę regionalnych punktów końcowych, zobacz [Niestandardowe nazwy poddomen dla usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Korzystając z klucza i punktu końcowego z utworzonego zasobu, utwórz dwie zmienne środowiskowe do uwierzytelniania:

* `ANOMALY_DETECTOR_KEY`- Klucz zasobu do uwierzytelniania żądań.
* `ANOMALY_DETECTOR_ENDPOINT`- Punkt końcowy zasobu do wysyłania żądań interfejsu API. Będzie to wyglądać tak: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Użyj instrukcji dla systemu operacyjnego.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx ANOMALY_DETECTOR_KEY <replace-with-your-anomaly-detector-key>
setx ANOMALY_DETECTOR_ENDPOINT <replace-with-your-anomaly-detector-endpoint>
```

Po dodaniu zmiennej środowiskowej uruchom ponownie okno konsoli.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source ~/.bashrc` z okna konsoli, aby zmiany zostały uwzględnione.

#### <a name="macos"></a>[Macos](#tab/unix)

Edytuj `.bash_profile`swój program i dodaj zmienną środowiskową:

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source .bash_profile` z okna konsoli, aby zmiany zostały uwzględnione.

***