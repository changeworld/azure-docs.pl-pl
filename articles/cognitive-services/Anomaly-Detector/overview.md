---
title: Co to jest interfejs API narzędzia do wykrywania anomalii?
titleSuffix: Azure Cognitive Services
description: Zaawansowane algorytmy interfejsu API detektora anomalii umożliwia identyfikowanie anomalii w danych szeregów czasowych.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: 9237e670dd8d43c4036f996c477948944718e3aa
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80053712"
---
# <a name="what-is-the-anomaly-detector-api"></a>Co to jest interfejs API narzędzia do wykrywania anomalii?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Interfejs API detektora anomalii umożliwia monitorowanie i wykrywanie nieprawidłowości w danych szeregów czasowych za pomocą uczenia maszynowego. Interfejs API detektora anomalii dostosowuje się, automatycznie identyfikując i stosując najlepiej dopasowane modele do danych, niezależnie od branży, scenariusza lub ilości danych. Za pomocą danych szeregów czasowych interfejsu API określa granice wykrywania anomalii, oczekiwane wartości i punkty danych, które są anomalie.

![Wykrywanie zmian wzorca w żądaniach usług](./media/anomaly_detection2.png)

Korzystanie z detektora anomalii nie wymaga wcześniejszego doświadczenia w uczeniu maszynowym, a interfejs API RESTful umożliwia łatwą integrację usługi z aplikacjami i procesami.

## <a name="features"></a>Funkcje

Za pomocą detektora anomalii można automatycznie wykrywać anomalie w danych szeregów czasowych lub w czasie rzeczywistym.

|Funkcja  |Opis  |
|---------|---------|
|Wykrywaj anomalie w czasie rzeczywistym. | Wykrywanie anomalii w danych przesyłania strumieniowego przy użyciu wcześniej widocznych punktów danych, aby ustalić, czy ostatni jest anomalią. Ta operacja generuje model przy użyciu wysyłanych punktów danych i określa, czy punkt docelowy jest anomalią. Wywołując interfejs API z każdym nowym punktem danych, który generujesz, można monitorować dane podczas ich tworzenia. |
|Wykrywanie anomalii w całym zestawie danych jako partii. | Użyj szeregów czasowych, aby wykryć wszelkie anomalie, które mogą istnieć w danych. Ta operacja generuje model przy użyciu całych danych szeregów czasowych, z każdego punktu analizowane z tego samego modelu.         |
| Uzyskaj dodatkowe informacje o swoich danych. | Uzyskaj przydatne informacje o danych i wszelkich obserwowanych anomaliach, w tym oczekiwanych wartościach, granicach anomalii i pozycjach. |
| Dostosuj granice wykrywania anomalii. | Interfejs API detektora anomalii automatycznie tworzy granice wykrywania anomalii. Dostosuj te granice, aby zwiększyć lub zmniejszyć czułość interfejsu API na anomalie danych i lepiej dopasować dane. |

## <a name="demo"></a>Demonstracja

Zapoznaj się z tym [interaktywnym demo,](https://aka.ms/adDemo) aby zrozumieć, jak działa Wykrywacz Anomalii.
Aby uruchomić demo, należy utworzyć zasób Detektor anomalii i uzyskać klucz interfejsu API i punkt końcowy.

## <a name="notebook"></a>Notes

Aby dowiedzieć się, jak wywołać interfejs API detektora anomalii, wypróbuj ten [notes platformy Azure](https://aka.ms/adNotebook). Ten internetowy notes Jupyter pokazuje, jak wysłać żądanie interfejsu API i wizualizować wynik.

Aby uruchomić notes, wykonaj następujące czynności:

1. Uzyskaj prawidłowy klucz subskrypcji interfejsu API detektora anomalii i punkt końcowy interfejsu API. Poniżej znajduje się instrukcja rejestracji.
1. Zaloguj się, a następnie kliknij przycisk Klonuj w prawym górnym rogu.
1. Odznania opcji "publiczne" w oknie dialogowym przed zakończeniem operacji klonowania, w przeciwnym razie notes, w tym klucze subskrypcji, będzie publiczny.
1. Kliknij **przycisk Uruchom na darmowych obliczeniach**
1. Wybierz jeden z notesów.
1. Dodaj prawidłowy klucz subskrypcji interfejsu `subscription_key` API detektora anomalii do zmiennej.
1. Zmień `endpoint` zmienną na punkt końcowy. Na przykład: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. Na górnym pasku menu kliknij pozycję **Komórka**, a następnie **pozycję Uruchom wszystko**.

## <a name="workflow"></a>Przepływ pracy

Interfejs API detektora anomalii jest usługą sieci web RESTful, dzięki czemu można łatwo wywołać z dowolnego języka programowania, który może tworzyć żądania HTTP i analizować JSON.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Po zarejestrowaniu się:

1. Zabierz dane szeregów czasowych i przekonwertuj je na prawidłowy format JSON. Użyj [najlepszych rozwiązań](concepts/anomaly-detection-best-practices.md) podczas przygotowywania danych, aby uzyskać najlepsze wyniki.
1. Wyślij żądanie do interfejsu API detektora anomalii z danymi.
1. Przetwórz odpowiedź interfejsu API, analizując zwrócony komunikat JSON.

## <a name="algorithms"></a>Algorytmy

* Zobacz następujące blogi techniczne, aby uzyskać informacje na temat używanych algorytmów:
    * [Wprowadzenie do interfejsu API narzędzia do wykrywania anomalii platformy Azure](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162)
    * [Omówienie algorytmu SR-CNN w usłudze Azure Anomaly Detector](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Overview-of-SR-CNN-algorithm-in-Azure-Anomaly-Detector/ba-p/982798)

Możesz przeczytać papier [time-series anomaly detection service w firmie Microsoft](https://arxiv.org/abs/1906.03821) (zaakceptowany przez KDD 2019), aby dowiedzieć się więcej o algorytmach SR-CNN opracowanych przez firmę Microsoft.


> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="join-the-anomaly-detector-community"></a>Dołączanie do społeczności narzędzia do wykrywania anomalii

* Dołącz do [grupy Doradcy wykrywania anomalii w usłudze Microsoft Teams](https://aka.ms/AdAdvisorsJoin)
* Zobacz [wybraną zawartość generowaną przez użytkownika](user-generated-content.md)

## <a name="next-steps"></a>Następne kroki

* [Szybki start: wykrywanie anomalii w danych szeregów czasowych przy użyciu interfejsu API REST detektora anomalii](quickstarts/detect-data-anomalies-csharp.md)
* Demo online interfejsu API detektora [anomalii](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector)
* Odwołanie do [interfejsu API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) detektora anomalii
