---
title: Wykrywanie anomalii aplikacji Java — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
description: Zapoznaj się z aplikacji w języku Java, która używa interfejsu API wykrywania anomalii w usługach Microsoft Cognitive Services. Wyślij oryginalnego punkty danych do interfejsu API i uzyskać oczekiwaną wartość i anomalii punktów.
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: ac26e29f4a839f69b123489600c2c83fe395c48a
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48247909"
---
# <a name="anomaly-detection-java-application"></a>Aplikacja Java wykrywania anomalii

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

W tym artykule przedstawiono, przy użyciu prostej aplikacji języka Java do wywołania interfejsu API wykrywania anomalii.  
Przykład przesyła danych szeregów czasowych do interfejsu API wykrywania anomalii z kluczem subskrypcji, a następnie pobiera wszystkie punkty anomalii i oczekiwana wartość dla każdego punktu danych z interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="platform-requirements"></a>Wymagania dotyczące platformy

W tym samouczku został utworzony przy użyciu [IntelliJ IDEA](https://www.jetbrains.com/idea). I należy również zainstalować [Java Development Kit (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/index.html) wersji 1.8 + a aktualne [firmy Apache Maven](http://maven.apache.org/) narzędzie kompilacji.

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Subskrybowanie do wykrywania anomalii i Uzyskaj klucz subskrypcji 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]
 

## <a name="download-the-tutorial-project"></a>Pobierz samouczek projekt

1. Przejdź do MicrosoftAnomalyDetection [repozytorium Java](https://github.com/MicrosoftAnomalyDetection/java-sample).
2. Kliknij przycisk klonu, lub przycisk Pobierz.
3. Kliknij przycisk Pobierz ZIP, aby pobrać plik zip w projekcie samouczka.

<a name="Step1"></a>
### <a name="open-the-tutorial-project"></a>Otwórz projekt samouczka

1. Wyodrębnij plik zip projektu samouczka.
2. IntelliJ IDEA kliknij **Plik > Otwórz**, pojawi się okno dialogowe Otwieranie pliku lub projektu.
3. Wybierz projekt wyodrębniony ścieżki katalogu głównego, a następnie kliknij przycisk OK.
4. Na panelu projekty rozwiń **src > główny > java**.
5. Kliknij dwukrotnie com.microsoft.cognitiveservice.anomalydetection.Main.java można załadować pliku do edytora.

<a name="Step2"></a>
### <a name="replace-subscriptionkey-and-uri-region"></a>Zastąp subscriptionKey i region, identyfikator URI

```
// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
public static final String subscriptionKey = "<Subscription Key>";

public static final String uriBase = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

```

<a name="Step3"></a>
### <a name="build-and-run-the-tutorial-project"></a>Skompiluj i uruchom projekt samouczka

1. Wyświetlić menu, kliknij prawym przyciskiem myszy w dowolnym miejscu na karcie kodu źródłowego com.microsoft.cognitiveservice.anomalydetection.Main.java. 
2. Wybierz opcję uruchamiania Main.main()
3. Wynik przykładowe żądanie zostanie zwrócony i wyświetlane w terminalu.

### <a name="result-of-the-tutorial-project"></a>Wynik w projekcie samouczka

[!INCLUDE [diagrams](../includes/diagrams.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
