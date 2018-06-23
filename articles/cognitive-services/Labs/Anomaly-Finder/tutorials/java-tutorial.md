---
title: Wykrywanie anomalii aplikacji Java - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
description: Poznaj aplikacji Java, która używa interfejsu API wykrywania anomalii w kognitywnych usług firmy Microsoft. Wyślij oryginalnego punkty danych do interfejsu API i uzyskać oczekiwaną wartością i punkty anomalii.
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: 228d440da358eba1322e2228c54f21e925e36ecd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348165"
---
# <a name="anomaly-detection-java-application"></a>Aplikacja Java wykrywania anomalii

W tym artykule przedstawiono przy użyciu prostej aplikacji Java do wywołania interfejsu API wykrywania anomalii.  
Przykład przesyła dane serii czas do interfejsu API wykrywania anomalii kluczem subskrypcji, a następnie pobiera wszystkie punkty anomalii i oczekiwana wartość dla każdego punktu danych z interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="platform-requirements"></a>Wymagania dotyczące platformy

W tym samouczku został utworzony przy użyciu [IntelliJ IDEA](https://www.jetbrains.com/idea). A także należy zainstalować [Java Development Kit (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/index.html) wersji 1.8 + i aktualne [w Apache Maven](http://maven.apache.org/) narzędzie kompilacji.

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Subskrybowanie wykrywania anomalii i uzyskiwanie klucza subskrypcji 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]
 

## <a name="download-the-tutorial-project"></a>Pobierz samouczek projektu

1. Przejdź do MicrosoftAnomalyDetection [repozytorium Java](https://github.com/MicrosoftAnomalyDetection/java-sample).
2. Kliknij przycisk klonu, lub przycisk Pobierz.
3. Kliknij przycisk Pobierz ZIP do pobrania plik .zip zawierający projekt samouczka.

<a name="Step1"></a>
### <a name="open-the-tutorial-project"></a>Otwórz projekt samouczka

1. Wyodrębnij plik zip samouczek projektu.
2. W IntelliJ IDEA kliknij **Plik > Otwórz**, zostanie wyświetlone okno dialogowe Otwieranie pliku lub projektu.
3. Wybierz ścieżkę katalogu głównego projektu wyodrębnionego, a następnie kliknij przycisk OK.
4. W panelu projekty rozwiń **src > główny > java**.
5. Kliknij dwukrotnie com.microsoft.cognitiveservice.anomalydetection.Main.java można załadować pliku do edytora.

<a name="Step2"></a>
### <a name="replace-subscriptionkey-and-uri-region"></a>Zastąpienie subscriptionKey i identyfikator URI regionu

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

1. Wyświetlić menu przez prawym przyciskiem myszy na karcie Kod źródłowy com.microsoft.cognitiveservice.anomalydetection.Main.java. 
2. Wybierz opcję uruchamiania Main.main()
3. Wynik przykładowe żądanie zostanie zwrócony i wyświetlany w terminalu.

### <a name="result-of-the-tutorial-project"></a>Wynik projekt samouczka

[!INCLUDE [diagrams](../includes/diagrams.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
