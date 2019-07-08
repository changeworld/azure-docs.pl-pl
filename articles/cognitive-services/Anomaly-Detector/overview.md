---
title: Co to jest interfejs API narzędzia do wykrywania anomalii? | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Użyj zaawansowanych algorytmów API wykrywanie anomalii, aby identyfikować anomalie w danych szeregów czasowych.
services: cognitive-services
author: aahill
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 38b23ee4bfa8a1dbcc11615425ccd580c23eb3e1
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593083"
---
# <a name="what-is-the-anomaly-detector-api"></a>Co to jest interfejs API narzędzia do wykrywania anomalii?

Interfejs API usługi Wykrywanie anomalii pozwala na monitorowanie i wykrywanie nieprawidłowości w danych szeregów czasowych z usługą machine learning. Interfejs API usługi Wykrywanie anomalii dostosowuje się automatycznie identyfikując i stosowanie najlepszego dopasowania modeli danych, niezależnie od branży, scenariusz i ilości danych. Przy użyciu danych szeregów czasowych, interfejs API określa granic na potrzeby wykrywania anomalii, oczekiwane wartości i punkty danych, które są anomalie.

![Wykrywanie zmian wzorców w żądaniach usługi](./media/anomaly_detection2.png)

Korzystanie z wykrywanie anomalii nie wymaga żadnych doświadczenie w uczenia maszynowego i interfejsu API RESTful pozwala łatwo zintegrować usługę w aplikacje sieci Web i procesów.

## <a name="features"></a>Funkcje

Za pomocą wykrywania anomalii, mogą automatycznie wykrywać anomalie w danych szeregów czasowych, lub, w jakiej występują w czasie rzeczywistym. 

|Cecha  |Opis  |
|---------|---------|
|Wykrywanie anomalii, w jakiej występują w czasie rzeczywistym. | Wykrycia anomalii w danych przesyłania strumieniowego za pomocą punktów danych widoczna poprzednio do określenia, czy Twojego najnowszego anomalii. Ta operacja generuje model przy użyciu punktów danych, wysyłania i określa, czy dany punkt docelowy anomalii. Przez wywołanie interfejsu API z każdego nowego punktu danych, wygenerowane przez Ciebie, możesz monitorować dane podczas jego tworzenia. |
|Wykrywanie anomalii w całym zestawie danych jako zadania wsadowego. | Użyj serii czasu, aby wykryć wszelkie anomalie, które mogą istnieć w całym danych. Ta operacja generuje model przy użyciu całej szeregami czasowymi, z każdym punkcie analizowane za pomocą tego samego modelu.         |
| Uzyskaj dodatkowe informacje na temat danych. | Uzyskaj przydatne szczegółowe informacje o danych i zaobserwowanego anomalii, w tym oczekiwanych wartości, granice anomalii i pozycji. |
| Dostosuj granice wykrywania anomalii. | Interfejs API usługi Wykrywanie anomalii automatycznie tworzyć granice dla wykrywania anomalii. Dostosuj te granice, aby zwiększyć lub zmniejszyć wrażliwość interfejsu API na anomalie danych i lepszego dopasowania danych. |

## <a name="demo"></a>Demonstracja

Aby szybko rozpocząć korzystanie z interfejsu API wykrywanie anomalii, spróbuj [prezentację online](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector) , mogą być uruchamiane w przeglądarce. Ten pokaz działa w notesu programu Jupyter hostowanych w sieci web i dowiesz się, jak wysyłać żądania interfejsu API i Wizualizuj wyniki.

Aby uruchomić wersję demonstracyjną, wykonaj następujące czynności:

1. Uzyskaj prawidłowy klucz subskrypcji dla interfejsu API wykrywanie anomalii i punkt końcowy interfejsu API. Poniższa sekcja zawiera instrukcje dotyczące rejestracji. 
2. Zaloguj się, a następnie kliknąć sklonowania, w prawym górnym rogu.
3. Kliknij przycisk **systemem wolnej mocy obliczeniowej**
4. Wybierz jeden z notesów dla tego przykładu.
5. Dodaj prawidłowy interfejs API wykrywanie anomalii subskrypcji klucza `subscription_key` zmiennej. Zmiana `endpoint` zmiennej do punktu końcowego usługi. Na przykład: `https://westus2.api.cognitive.microsoft.com`
1. Na pasku menu u góry kliknij **komórki**, następnie **Uruchom wszystkie**.

## <a name="workflow"></a>Przepływ pracy

Interfejs API usługi Wykrywanie anomalii jest to usługa sieci web typu RESTful, co ułatwia wywołać z dowolnego języka programowania, który może wysyłać żądania HTTP i Przeanalizuj dane JSON.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Po zarejestrowaniu się:

1. Pobrać danych szeregów czasowych i przekonwertować go na prawidłowy format JSON. Użyj [najlepsze praktyki](concepts/anomaly-detection-best-practices.md) podczas przygotowywania danych w celu uzyskania najlepszych wyników.
1. Wyślij żądanie do interfejsu API wykrywanie anomalii ze swoimi danymi.
1. Przetwórz odpowiedź interfejsu API, analizując zwrócony komunikat JSON.

## <a name="next-steps"></a>Kolejne kroki

* [Szybki start: Wykrywanie anomalii w danych szeregów czasowych za pomocą interfejsu API REST wykrywanie anomalii](quickstarts/detect-data-anomalies-csharp.md)
* Interfejs API usługi Wykrywanie anomalii [prezentację online](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector)
* Wykrywanie anomalii [dokumentacja interfejsu API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)