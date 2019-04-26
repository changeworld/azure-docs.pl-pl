---
title: 'Szybki start: Używanie zestawu SDK wyszukiwania w Internecie Bing dla języka Java'
titleSuffix: Azure Cognitive Services
description: Zestaw SDK wyszukiwania w Internecie Bing ułatwia integrowanie wyszukiwania w Internecie Bing z aplikacją Java. Z tego przewodnika Szybki start dowiesz się, jak wysłać żądanie, odebrać odpowiedź JSON oraz filtrować i analizować wyniki.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 7209df902f03a7055e142dcbbb7743b6832958b6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60344132"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-java"></a>Szybki start: Używanie zestawu SDK wyszukiwania w Internecie Bing dla języka Java

Zestaw SDK wyszukiwania w Internecie Bing ułatwia integrowanie wyszukiwania w Internecie Bing z aplikacją Java. Z tego przewodnika Szybki start dowiesz się, jak wysłać żądanie, odebrać odpowiedź JSON oraz filtrować i analizować wyniki.

Chcesz zobaczyć kod teraz? [Przykłady zastosowania zestawu SDK wyszukiwania w Internecie Bing dla języka Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/) są dostępne w usłudze GitHub.

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

Zobacz też [Cennik usług Cognitive Services — interfejs API wyszukiwania Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="prerequisites"></a>Wymagania wstępne

Oto kilka rzeczy, które są potrzebne przed rozpoczęciem tego przewodnika Szybki start:

* [Zestaw JDK 7 lub 8](https://aka.ms/azure-jdks)
* [Narzędzie Apache Maven](https://maven.apache.org/download.cgi) lub inne narzędzie do automatyzacji kompilacji
* Klucz subskrypcji

## <a name="create-a-project-and-set-up-your-pom-file"></a>Tworzenie projektu i konfigurowanie pliku POM

Utwórz nowy projekt w języku Java przy użyciu narzędzia Maven lub innego narzędzia do automatyzacji kompilacji. Przy założeniu, że używasz narzędzia Maven, Dodaj następujące wiersze do Twojej [modelu obiektu projektu (POM)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html) pliku. Zamień wszystkie wystąpienia elementu `mainClass` swoją aplikacją.

```xml
<build>
    <plugins>
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.4.0</version>
        <configuration>
          <!--Your comment
            Replace the mainClass with the path to your java application.
            It should begin with com and doesn't require the .java extension.
            For example: com.bingwebsearch.app.BingWebSearchSample. This maps to
            The following directory structure:
            src/main/java/com/bingwebsearch/app/BingWebSearchSample.java.
          -->
          <mainClass>com.path.to.your.app.APP_NAME</mainClass>
        </configuration>
      </plugin>
      <plugin>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.0</version>
        <configuration>
          <source>1.7</source>
          <target>1.7</target>
        </configuration>
      </plugin>
      <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>attached</goal>
            </goals>
            <configuration>
              <descriptorRefs>
                <descriptorRef>jar-with-dependencies</descriptorRef>
              </descriptorRefs>
              <archive>
                <manifest>
                  <!--Your comment
                    Replace the mainClass with the path to your java application.
                    For example: com.bingwebsearch.app.BingWebSearchSample.java.
                    This maps to the following directory structure:
                    src/main/java/com/bingwebsearch/app/BingWebSearchSample.java.
                  -->
                  <mainClass>com.path.to.your.app.APP_NAME.java</mainClass>
                </manifest>
              </archive>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure</artifactId>
      <version>1.9.0</version>
    </dependency>
    <dependency>
      <groupId>commons-net</groupId>
      <artifactId>commons-net</artifactId>
      <version>3.3</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-websearch</artifactId>
      <version>1.0.1</version>
    </dependency>
  </dependencies>
```

## <a name="declare-dependencies"></a>Deklarowanie zależności

Otwórz projekt w ulubionym środowisku IDE lub edytorze i zaimportuj te zależności:

```java
import com.microsoft.azure.cognitiveservices.search.websearch.BingWebSearchAPI;
import com.microsoft.azure.cognitiveservices.search.websearch.BingWebSearchManager;
import com.microsoft.azure.cognitiveservices.search.websearch.models.ImageObject;
import com.microsoft.azure.cognitiveservices.search.websearch.models.NewsArticle;
import com.microsoft.azure.cognitiveservices.search.websearch.models.SearchResponse;
import com.microsoft.azure.cognitiveservices.search.websearch.models.VideoObject;
import com.microsoft.azure.cognitiveservices.search.websearch.models.WebPage;
```

Jeśli projekt został utworzony za pomocą narzędzia Maven, ten pakiet powinien być już zadeklarowany. W przeciwnym razie zadeklaruj pakiet teraz. Na przykład:

```java
package com.bingwebsearch.app
```

## <a name="declare-the-bingwebsearchsample-class"></a>Deklarowanie klasy BingWebSearchSample

Zadeklaruj klasę `BingWebSearchSample`. Będzie ona obejmować większość naszego kodu, w tym metodę `main`.  

```java
public class BingWebSearchSample {

// The code in the following sections goes here.

}
```

## <a name="construct-a-request"></a>Konstruowanie żądania

Metoda `runSample`, która znajduje się w klasie `BingWebSearchSample`, tworzy żądanie. Skopiuj ten kod do swojej aplikacji:

```java
public static boolean runSample(BingWebSearchAPI client) {
    /*
     * This function performs the search.
     *
     * @param client instance of the Bing Web Search API client
     * @return true if sample runs successfully
     */
    try {
        /*
         * Performs a search based on the .withQuery and prints the name and
         * url for the first web pages, image, news, and video result
         * included in the response.
         */
        System.out.println("Searched Web for \"Xbox\"");
        // Construct the request.
        SearchResponse webData = client.bingWebs().search()
            .withQuery("Xbox")
            .withMarket("en-us")
            .withCount(10)
            .execute();

// Code continues in the next section...
```

## <a name="handle-the-response"></a>Obsługa odpowiedzi

Następnie dodajmy kod do analizy odpowiedzi i wyświetlania wyników. Wartości `name` i `url` dla pierwszej strony internetowej, pierwszego obrazu, artykułu i wideo są wyświetlane, jeśli zostały uwzględnione w obiekcie odpowiedzi.

```java
/*
* WebPages
* If the search response has web pages, the first result's name
* and url are printed.
*/
if (webData != null && webData.webPages() != null && webData.webPages().value() != null &&
        webData.webPages().value().size() > 0) {
    // find the first web page
    WebPage firstWebPagesResult = webData.webPages().value().get(0);

    if (firstWebPagesResult != null) {
        System.out.println(String.format("Webpage Results#%d", webData.webPages().value().size()));
        System.out.println(String.format("First web page name: %s ", firstWebPagesResult.name()));
        System.out.println(String.format("First web page URL: %s ", firstWebPagesResult.url()));
    } else {
        System.out.println("Couldn't find the first web result!");
    }
} else {
    System.out.println("Didn't find any web pages...");
}
/*
 * Images
 * If the search response has images, the first result's name
 * and url are printed.
 */
if (webData != null && webData.images() != null && webData.images().value() != null &&
        webData.images().value().size() > 0) {
    // find the first image result
    ImageObject firstImageResult = webData.images().value().get(0);

    if (firstImageResult != null) {
        System.out.println(String.format("Image Results#%d", webData.images().value().size()));
        System.out.println(String.format("First Image result name: %s ", firstImageResult.name()));
        System.out.println(String.format("First Image result URL: %s ", firstImageResult.contentUrl()));
    } else {
        System.out.println("Couldn't find the first image result!");
    }
} else {
    System.out.println("Didn't find any images...");
}
/*
 * News
 * If the search response has news articles, the first result's name
 * and url are printed.
 */
if (webData != null && webData.news() != null && webData.news().value() != null &&
        webData.news().value().size() > 0) {
    // find the first news result
    NewsArticle firstNewsResult = webData.news().value().get(0);
    if (firstNewsResult != null) {
        System.out.println(String.format("News Results#%d", webData.news().value().size()));
        System.out.println(String.format("First news result name: %s ", firstNewsResult.name()));
        System.out.println(String.format("First news result URL: %s ", firstNewsResult.url()));
    } else {
        System.out.println("Couldn't find the first news result!");
    }
} else {
    System.out.println("Didn't find any news articles...");
}

/*
 * Videos
 * If the search response has videos, the first result's name
 * and url are printed.
 */
if (webData != null && webData.videos() != null && webData.videos().value() != null &&
        webData.videos().value().size() > 0) {
    // find the first video result
    VideoObject firstVideoResult = webData.videos().value().get(0);

    if (firstVideoResult != null) {
        System.out.println(String.format("Video Results#%s", webData.videos().value().size()));
        System.out.println(String.format("First Video result name: %s ", firstVideoResult.name()));
        System.out.println(String.format("First Video result URL: %s ", firstVideoResult.contentUrl()));
    } else {
        System.out.println("Couldn't find the first video result!");
    }
} else {
    System.out.println("Didn't find any videos...");
}
```

## <a name="declare-the-main-method"></a>Deklarowanie metody main

W tej aplikacji metoda main zawiera kod, który tworzy wystąpienie klienta, weryfikuje klucz `subscriptionKey` i wywołuje metodę `runSample`. Przed kontynuowaniem upewnij się, że został wprowadzony prawidłowy klucz subskrypcji dla konta platformy Azure.

```java
public static void main(String[] args) {
    try {
        // Enter a valid subscription key for your account.
        final String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
        // Instantiate the client.
        BingWebSearchAPI client = BingWebSearchManager.authenticate(subscriptionKey);
        // Make a call to the Bing Web Search API.
        runSample(client);
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }
}
```

## <a name="run-the-program"></a>Uruchamianie programu

Ostatnim krokiem jest uruchomienie programu.

```console
mvn compile exec:java
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Pamiętaj, aby po zakończeniu pracy z tym projektem usunąć klucz subskrypcji z kodu programu.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przykłady dotyczące zestawu Java SDK dla usług Cognitive Services](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingWebSearch)

## <a name="see-also"></a>Zobacz także

* [Dokumentacja zestawu Azure Java SDK](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/websearch)