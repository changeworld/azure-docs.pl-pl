---
title: 'Samouczek: Wykrywania anomalii, język Java'
titlesuffix: Azure Cognitive Services
description: Zapoznaj się z aplikacją w języku Java, która korzysta z interfejsu API wykrywania anomalii. Wyślij oryginalne punkty danych do interfejsu API i uzyskaj oczekiwaną wartość oraz punkty anomalii.
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: ba66c96f9129b253fc5897c3a2eaaefb53056cea
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60709907"
---
# <a name="tutorial-anomaly-detection-with-java-application"></a>Samouczek: Wykrywanie anomalii przy użyciu aplikacji Java

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

W tym artykule przedstawiono wywoływanie interfejsu API wykrywania anomalii przy użyciu prostej aplikacji języka Java.  
Przykład przesyła dane szeregów czasowych do interfejsu API wykrywania anomalii za pomocą klucza subskrypcji, a następnie pobiera wszystkie punkty anomalii i oczekiwaną wartość dla każdego punktu danych z interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="platform-requirements"></a>Wymagania dotyczące platformy

Ten samouczek opracowano przy użyciu programu [IntelliJ IDEA](https://www.jetbrains.com/idea). Należy też zainstalować zestaw [Java Development Kit (JDK)](https://aka.ms/azure-jdks) w wersji 1.8 lub nowszej oraz aktualne narzędzie do kompilacji [Apache Maven](https://maven.apache.org/).

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Subskrybowanie wykrywania anomalii i uzyskiwanie klucza subskrypcji 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]
 

## <a name="download-the-tutorial-project"></a>Pobieranie projektu samouczka

1. Przejdź do [repozytorium Java](https://github.com/MicrosoftAnomalyDetection/java-sample) MicrosoftAnomalyDetection.
2. Kliknij przycisk Clone or download (Sklonuj lub pobierz).
3. Kliknij pozycję Download ZIP (Pobierz plik ZIP), aby pobrać plik zip z projektem samouczka.

<a name="Step1"></a>
### <a name="open-the-tutorial-project"></a>Otwieranie projektu samouczka

1. Wyodrębnij plik zip z projektem samouczka.
2. W programie IntelliJ IDEA kliknij kolejno pozycje **File > Open** (Plik > Otwórz). Pojawi się okno dialogowe Open File or Project (Otwieranie pliku lub projektu).
3. Wybierz ścieżkę katalogu głównego w wyodrębnionym projekcie, a następnie kliknij pozycję OK.
4. Na panelu Projects (Projekty) rozwiń gałąź **src > main > java**.
5. Kliknij dwukrotnie plik com.microsoft.cognitiveservice.anomalydetection.Main.java, aby załadować pliku w edytorze.

<a name="Step2"></a>
### <a name="replace-subscriptionkey-and-uri-region"></a>Zamienianie wartości subscriptionKey i regionu identyfikatora URI

```
// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
public static final String subscriptionKey = "<Subscription Key>";

public static final String uriBase = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

```

<a name="Step3"></a>
### <a name="build-and-run-the-tutorial-project"></a>Kompilowanie i uruchamianie projektu samouczka

1. Wyświetl menu, klikając prawym przyciskiem myszy w dowolnym miejscu na karcie kodu źródłowego com.microsoft.cognitiveservice.anomalydetection.Main.java. 
2. Wybierz pozycję Run 'Main.main()' (Uruchom funkcję „Main.main()”).
3. Wynik przykładowego żądania zostanie zwrócony i wyświetlony w terminalu.

### <a name="result-of-the-tutorial-project"></a>Wynik projektu samouczka

[!INCLUDE [diagrams](../includes/diagrams.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
