---
title: 'Szybki start: Tłumaczenie tekstu, Java — interfejs API tłumaczenia tekstu w usłudze Translator'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start dowiesz się, jak przetłumaczyć ciąg tekstowy z języka angielskiego na włoski i niemiecki przy użyciu języka Java i interfejsu API REST tłumaczenia tekstu w usłudze Translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: f8846e8dc6df8753562a36c05342f04307dd886e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445088"
---
# <a name="quickstart-use-the-translator-text-api-to-translate-a-string-using-java"></a>Szybki start: Tłumaczenie ciągu za pomocą interfejsu API tłumaczenia tekstu w usłudze Translator oraz języka Java

W tym przewodniku Szybki start dowiesz się, jak przetłumaczyć ciąg tekstowy z języka angielskiego na włoski i niemiecki przy użyciu języka Java i interfejsu API REST tłumaczenia tekstu w usłudze Translator.

Ten przewodnik Szybki start wymaga [konta usługi Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z zasobem tłumaczenia tekstu w usłudze Translator. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/), aby uzyskać klucz subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

* [Zestaw JDK 7 lub nowszy](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Narzędzie [Gradle](https://gradle.org/install/)
* Klucz subskrypcji platformy Azure na potrzeby tłumaczenia tekstu w usłudze Translator

## <a name="initialize-a-project-with-gradle"></a>Inicjowanie projektu za pomocą narzędzia Gradle

Zaczniemy od utworzenia katalogu roboczego dla tego projektu. W wierszu polecenia (lub terminalu) uruchom następujące polecenie:

```console
mkdir translator-sample
cd translator-sample
```

Następnie zainicjujesz projekt narzędzia Gradle. To polecenie spowoduje utworzenie niezbędnych plików kompilacji dla narzędzia Gradle, a w szczególności najważniejszego pliku `build.gradle.kts`, który jest używany czasie wykonywania do utworzenia i skonfigurowania aplikacji. Uruchom następujące polecenie w katalogu roboczym:

```console
gradle init --type basic
```

Po wyświetleniu monitu wybierz pozycję **Język DSL**, a następnie **Kotlin**.

## <a name="configure-the-build-file"></a>Konfigurowanie pliku kompilacji

Znajdź plik `build.gradle.kts` i otwórz go za pomocą ulubionego środowiska IDE lub edytora tekstów. Następnie skopiuj go do tej konfiguracji kompilacji:

```
plugins {
    java
    application
}
application {
    mainClassName = "Translate"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Weź pod uwagę, że ten przykład zależy od bibliotek OkHttp (obsługa żądań HTTP) i Gson (obsługa i analizowanie kodu JSON). Jeśli chcesz dowiedzieć się więcej na temat konfiguracji kompilacji, zobacz [Creating New Gradle Builds (Tworzenie nowych kompilacji narzędzia Gradle)](https://guides.gradle.org/creating-new-gradle-builds/).

## <a name="create-a-java-file"></a>Tworzenie pliku języka Java

Utworzymy folder dla przykładowej aplikacji. W katalogu roboczym uruchom polecenie:

```console
mkdir -p src/main/java
```

Następnie utwórz w tym folderze plik o nazwie `Translate.java`.

## <a name="import-required-libraries"></a>Importowanie wymaganych bibliotek

Otwórz plik `Translate.java` i dodaj następujące instrukcje import:

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;
```

## <a name="define-variables"></a>Definiowanie zmiennych

Najpierw należy utworzyć klasę publiczną dla projektu:

```java
public class Translate {
  // All project code goes here...
}
```

Dodaj następujące wiersze do klasy `Translate`. Zauważysz, że razem z wersją `api-version` dołączono dwa dodatkowe parametry do elementu `url`. Te parametry są używane do ustawiania danych wyjściowych tłumaczenia. W tym przykładzie ustawiamy je na język niemiecki (`de`) i włoski (`it`). Upewnij się, że wartość klucza subskrypcji została zaktualizowana.

```java
String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
String url = "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=de,it";
```

Jeśli używasz subskrypcji do wielu usług Cognitive Services, należy również uwzględnić `Ocp-Apim-Subscription-Region` w parametry żądania. [Dowiedz się więcej o uwierzytelnianiu przy użyciu wielu usług subskrypcji](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication). 

## <a name="create-a-client-and-build-a-request"></a>Tworzenie klienta i budowanie żądania

Dodaj następujący wiersz do klasy `Translate` w celu utworzenia wystąpienia klasy `OkHttpClient`:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Następnie utworzymy żądanie POST. Możesz dowolnie zmienić tekst do tłumaczenia. Tekst musi być poprzedzony znakiem ucieczki.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"Welcome to Microsoft Translator. Guess how many languages I speak!\"\n}]");
    Request request = new Request.Builder()
            .url(url).post(body)
            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>Tworzenie funkcji analizowania odpowiedzi

Ta prosta funkcja analizuje i formatuje odpowiedź JSON z usługi tłumaczenia tekstu w usłudze Translator.

```java
// This function prettifies the json response.
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonElement json = parser.parse(json_text);
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="put-it-all-together"></a>Zebranie wszystkich elementów

Ostatnim krokiem jest wykonanie żądania i pobranie odpowiedzi. Dodaj następujące wiersze do projektu:

```java
public static void main(String[] args) {
    try {
        Translate translateRequest = new Translate();
        String response = translateRequest.Post();
        System.out.println(prettify(response));
    } catch (Exception e) {
        System.out.println(e);
    }
}
```

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

To wszystko. Teraz możesz uruchomić przykładową aplikację. Z wiersza polecenia (lub sesji terminalu) przejdź do katalogu głównego używanego katalogu roboczego, a następnie uruchom polecenie:

```console
gradle build
```

Po zakończeniu kompilacji uruchom:

```console
gradle run
```

## <a name="sample-response"></a>Przykładowa odpowiedź

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "translations": [
      {
        "text": "Willkommen bei Microsoft Translator. Erraten Sie, wie viele Sprachen ich spreche!",
        "to": "de"
      },
      {
        "text": "Benvenuti a Microsoft Translator. Indovinate quante lingue parlo!",
        "to": "it"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z kodem przykładowym z tego przewodnika Szybki start i innych, dotyczącym między innymi transliteracji i rozpoznawania języka, a także z innymi projektami przykładowymi dotyczącymi tłumaczenia tekstów w usłudze Translator w repozytorium GitHub.

> [!div class="nextstepaction"]
> [Poznaj przykłady dla języka Java w usłudze GitHub](https://aka.ms/TranslatorGitHub?type=&language=java)

## <a name="see-also"></a>Zobacz też

* [Transliteracja tekstu](quickstart-java-transliterate.md)
* [Identyfikowanie język na podstawie danych wejściowych](quickstart-java-detect.md)
* [Uzyskiwanie alternatywnych tłumaczeń](quickstart-java-dictionary.md)
* [Pobieranie listy obsługiwanych języków](quickstart-java-languages.md)
* [Określanie długości zdań na podstawie danych wejściowych](quickstart-java-sentences.md)
