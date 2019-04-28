---
title: 'Szybki start: wyszukiwanie obrazów za pomocą zestawu SDK wyszukiwania obrazów Bing dla języka Java'
description: Ten przewodnik Szybki start umożliwia wyszukanie pierwszego obrazu przy użyciu zestawu SDK wyszukiwania obrazów Bing, który jest otoką dla interfejsu API i zawiera te same funkcje. Ta prosta aplikacja w języku Java wysyła zapytanie dotyczące wyszukania obrazu, analizuje odpowiedź w formacie JSON i wyświetla adres URL pierwszego zwróconego obrazu.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 02/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: dd2bf11781a6dd013f033fc535b068d449dd04d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60918184"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-for-java"></a>Szybki start: wyszukiwanie obrazów za pomocą zestawu SDK wyszukiwania obrazów Bing dla języka Java

Ten przewodnik Szybki start umożliwia wyszukanie pierwszego obrazu przy użyciu zestawu SDK wyszukiwania obrazów Bing, który jest otoką dla interfejsu API i zawiera te same funkcje. Ta prosta aplikacja w języku Java wysyła zapytanie dotyczące wyszukania obrazu, analizuje odpowiedź w formacie JSON i wyświetla adres URL pierwszego zwróconego obrazu.

Kod źródłowy dla tego przykładu jest dostępny w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingImageSearch/Quickstart) wraz z dodatkową obsługą błędów i adnotacjami.

## <a name="prerequisites"></a>Wymagania wstępne
Pobierz [klucz dostępu usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) w obszarze **Wyszukiwanie**.  Zobacz też [Cennik usług Cognitive Services — interfejs API wyszukiwania Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Najnowsza wersja zestawu [Java Development Kit](https://aka.ms/azure-jdks) (JDK)

Zainstaluj zależności zestawu SDK wyszukiwania obrazów Bing przy użyciu oprogramowania Maven, Gradle lub innego systemu zarządzania zależnościami. Plik POM systemu Maven wymaga następującej deklaracji:

```xml
 <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-imagesearch</artifactId>
      <version>1.0.1</version>
    </dependency>
 </dependencies>
```

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy projekt w języku Java w ulubionym środowisku IDE lub edytorze i dodaj następujące instrukcje import do implementacji klasy:

    ```java
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchAPI;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchManager;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImageObject;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImagesModel;
    ```

2. W metodzie main utwórz zmienne dla klucza subskrypcji i szukanego terminu. Następnie utwórz wystąpienie klienta wyszukiwania obrazów Bing.

    ```java
    final String subscriptionKey = "COPY_YOUR_KEY_HERE";
    String searchTerm = "canadian rockies";
    //Image search client
    BingImageSearchAPI client = BingImageSearchManager.authenticate(subscriptionKey);
    ```

## <a name="send-a-search-request-to-the-api"></a>Wysyłanie żądania wyszukiwania do interfejsu API

1. Za pomocą metody `bingImages().search()` wyślij żądanie HTTP zawierające zapytanie wyszukiwania. Zapisz odpowiedź jako `ImagesModel`.

   ```java
    ImagesModel imageResults = client.bingImages().search()
                .withQuery(searchTerm)
                .withMarket("en-us")
                .execute();
    ```

## <a name="parse-and-view-the-result"></a>Analiza i wyświetlanie wyników

Analizuj wyniki obrazów zwróconych w odpowiedzi.
Jeśli odpowiedź zawiera wyniki wyszukiwania, zapisz pierwszy wynik i wyświetl jego szczegóły, takie jak adres URL miniatury i oryginalny adres URL, wraz z całkowitą liczbą zwróconych obrazów.  

```java
if (imageResults != null && imageResults.value().size() > 0) {
    // Image results
    ImageObject firstImageResult = imageResults.value().get(0);

    System.out.println(String.format("Total number of images found: %d", imageResults.value().size()));
    System.out.println(String.format("First image thumbnail url: %s", firstImageResult.thumbnailUrl()));
    System.out.println(String.format("First image content url: %s", firstImageResult.contentUrl()));
}
else {
        System.out.println("Couldn't find image results!");
     }

```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek dotyczący jednostronicowej aplikacji wyszukiwania obrazów Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Zobacz także

* [Czym jest funkcja wyszukiwania obrazów Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Wypróbuj interaktywny pokaz online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Pobieranie bezpłatnego klucza dostępu usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Przykłady dla zestawu Azure Cognitive Services SDK w języku Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)
* [Dokumentacja usług Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Dokumentacja interfejsu API wyszukiwania obrazów Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
