---
title: 'Szybki Start: wykonywanie wyszukiwania w wiadomościach przy użyciu zestawu SDK dla języka Java — wyszukiwanie wiadomości Bing'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki start, aby wyszukać wiadomości przy użyciu zestawu SDK wyszukiwania wiadomości Bing dla języka Java i przetworzyć odpowiedź.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 12/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: b8c2ca3bf919d70123b7b5b6d13c0301e0c1fd13
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75383582"
---
# <a name="quickstart-search-for-news-with-the-bing-news-search-sdk-for-java"></a>Szybki Start: wyszukiwanie wiadomości za pomocą zestawu SDK wyszukiwanie wiadomości Bing dla języka Java

Użyj tego przewodnika Szybki start, aby rozpocząć wyszukiwanie wiadomości za pomocą zestawu SDK wyszukiwania wiadomości Bing dla języka Java. Mimo że wyszukiwanie wiadomości Bing ma interfejs API REST zgodny z większością języków programowania, zestaw SDK umożliwia łatwe zintegrowanie tej usługi z aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch).

## <a name="prerequisites"></a>Wymagania wstępne

Zainstaluj zależności zestawu SDK wyszukiwania wiadomości Bing przy użyciu oprogramowania Maven, Gradle lub innego systemu zarządzania zależnościami. Plik POM systemu Maven wymaga następującej deklaracji:

```xml
    <dependencies>
    <dependency>
        <groupId>com.microsoft.azure.cognitiveservices</groupId>
        <artifactId>azure-cognitiveservices-newssearch</artifactId>
        <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
    </dependencies>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Tworzenie i inicjowanie projektu

Utwórz nowy projekt w języku Java w ulubionym środowisku IDE lub edytorze i zaimportuj poniższe biblioteki.

```java
import com.microsoft.azure.cognitiveservices.newssearch.*;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.NewsInner;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.NewsSearchAPIImpl;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.TrendingTopicsInner;
import com.microsoft.rest.credentials.ServiceClientCredentials;
import okhttp3.Interceptor;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.Response;
import java.io.IOException;
```

## <a name="create-a-search-client-and-store-credentials"></a>Tworzenie klienta wyszukiwania i przechowywanie poświadczeń

1. Utwórz metodę o nazwie `getClient()`, która zwraca nowego klienta wyszukiwania `NewsSearchAPIImpl`. Dodaj swój punkt końcowy jako pierwszy parametr dla nowego obiektu `NewsSearchAPIImpl` oraz nowy obiekt `ServiceClientCredentials` do przechowywania poświadczeń. 

    ```java
    public static NewsSearchAPIImpl getClient(final String subscriptionKey) {
        return new NewsSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                });
    }
    ```

2. Aby utworzyć obiekt `ServiceClientCredentials`, zastąp funkcję `applyCredentialsFilter()`. Przekaż obiekt `OkHttpClient.Builder` do metody i użyj metody `addNetworkInterceptor()` konstruktora, aby utworzyć poświadczenia dla wywołania SDK.

    ```java
    new ServiceClientCredentials() {
        @Override
        public void applyCredentialsFilter(OkHttpClient.Builder builder) {
            builder.addNetworkInterceptor(
                    new Interceptor() {
                        @Override
                        public Response intercept(Chain chain) throws IOException {
                            Request request = null;
                            Request original = chain.request();
                            // Request customization: add request headers.
                            Request.Builder requestBuilder = original.newBuilder()
                                    .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
                            request = requestBuilder.build();
                            return chain.proceed(request);
                        }
                    });
        }
    });
    ```

## <a name="send-and-receive-a-search-request"></a>Wysyłanie i odbieranie żądania wyszukiwania

1. Utwórz metodę, która wywołuje funkcję `getClient()` i wysyła żądanie wyszukiwania do usługi wyszukiwania wiadomości Bing. Przefiltruj wyszukiwanie za pomocą parametrów *rynek* i *liczba*, a następnie wypisz informacje o pierwszym wyniku dotyczącym wiadomości: nazwę, adres URL, datę publikacji, opis, nazwę dostawcy i łączną liczbę szacowanych dopasowań dla Twojego wyszukiwania.

    ```java
    public static void newsSearch(String subscriptionKey)
    {
        NewsSearchAPIImpl client = getClient(subscriptionKey);
        String searchTerm = "Quantum Computing";
    
        NewsInner newsResults = client.searchs().list(searchTerm, null, null, null,
                null, null, 100, null, "en-us",
                null, null, null, null, null,
                null, null);
    
        if (newsResults.value().size() > 0)
        {
            NewsArticle firstNewsResult = newsResults.value().get(0);
    
            System.out.println(String.format("TotalEstimatedMatches value: %d", newsResults.totalEstimatedMatches()));
            System.out.println(String.format("News result count: %d", newsResults.value().size()));
            System.out.println(String.format("First news name: %s", firstNewsResult.name()));
            System.out.println(String.format("First news url: %s", firstNewsResult.url()));
            System.out.println(String.format("First news description: %s", firstNewsResult.description()));
            System.out.println(String.format("First news published time: %s", firstNewsResult.datePublished()));
            System.out.println(String.format("First news provider: %s", firstNewsResult.provider().get(0).name()));
        }
        else
        {
            System.out.println("Couldn't find news results!");
        }
    
    }
    
    ```

2. Dodaj swoją metodę wyszukiwania do metody `main()`, aby wykonać kod.

    ```java 
    public static void main(String[] args) {
        String subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
        NewsSearchSDK.newsSearch(subscriptionKey);
    }
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](tutorial-bing-news-search-single-page-app.md)
