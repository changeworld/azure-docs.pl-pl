---
title: 'Szybki Start: Wyszukiwanie obrazów za pomocą zestawu SDK wyszukiwania obrazów Bing i języka Java'
description: Ten przewodnik Szybki Start umożliwia wyszukiwanie i znajdowanie obrazów w sieci web przy użyciu zestawu SDK wyszukiwania obrazów Bing i języka Java.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 08/28/2018
ms.author: aahi
ms.openlocfilehash: 12bd6f9a9a0b43b4571a7e0311ffbea54c7b9054
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574065"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-and-java"></a>Szybki Start: Wyszukiwanie obrazów za pomocą zestawu SDK wyszukiwania obrazów Bing i języka Java

Ten przewodnik Szybki Start umożliwia wyszukiwanie pierwsze obraz przy użyciu zestawu SDK wyszukiwania obrazów Bing, w jest otoką dla interfejsu API, która zawiera te same funkcje. Ta prosta aplikacja Java wysyła kwerendę wyszukiwania obrazów, analizuje odpowiedź w formacie JSON i wyświetla adres URL pierwszy obraz zwracane.

Kod źródłowy dla tego przykładu jest dostępny [w serwisie GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingImageSearch/Quickstart) za pomocą obsługi dodatkowych błędów i adnotacji. 

## <a name="prerequisites"></a>Wymagania wstępne 

Najnowszą wersję [zestawu Java Development Kit](http://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK)

Instalowanie zależności zestawu SDK wyszukiwania obrazów Bing za pomocą narzędzia Maven, Gradle lub innego systemu zarządzania zależnościami. Pliku POM narzędzia Maven wymaga następującą deklarację:

```xml
 <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-imagesearch</artifactId>
      <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
 </dependencies> 
```

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Tworzenie i Inicjowanie aplikacji

1. Utwórz nowy projekt języka Java w Twoim ulubionym środowiskiem IDE lub edytora i dodaj następujące instrukcje importu do implimentation swojej klasy:

    ```java
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchAPI;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchManager;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImageObject;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImagesModel;
    ```

2. W głównej metody tworzenia zmiennych dla swój klucz subskrypcji i szukanego terminu. Następnie utwórz wystąpienie klienta wyszukiwania obrazów Bing.

    ```java
    final String subscriptionKey = "COPY_YOUR_KEY_HERE";
    String searchTerm = "canadian rockies";
    //Image search client
    BingImageSearchAPI client = BingImageSearchManager.authenticate(subscriptionKey);
    ```

## <a name="send-a-search-request-to-the-bing-image-search-api"></a>Wyślij żądanie wyszukiwania do interfejsu API wyszukiwania obrazów Bing

1. Za pomocą `bingImages().search()`, Wyślij żądanie HTTP zawierające zapytania wyszukiwania. Zapisywanie odpowiedzi jako `ImagesModel`.
    ```java
    ImagesModel imageResults = client.bingImages().search()
                .withQuery(searchTerm)
                .withMarket("en-us")
                .execute();
    ```

## <a name="parse-and-view-the-result"></a>Analizowanie i wyświetlić wyniki

Analizowanie wyników obraz zwrócona w odpowiedzi.
Jeśli odpowiedź zawiera wyniki wyszukiwania, przechowywania pierwszego wyniku i wydrukować jej szczegóły, takie jak miniatura URL, oryginalny adres URL wraz z całkowitą liczbą zwrócił obrazów.  

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
}
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek dotyczący aplikacji jednostronicowej wyszukiwania obrazów Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Zobacz także 

* [Co to jest wyszukiwanie obrazów Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Wypróbuj prezentację online dla interaktywne](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Uzyskaj bezpłatne klucza dostępu usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api) 
* [Przykładów zestawem SDK usługi Azure Cognitive Services w języku Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
* [Dokumentacja usługi Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Dokumentacja interfejsu API wyszukiwania obrazów Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
