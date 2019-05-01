---
title: 'Szybki start: Wykrywanie języka tekstu, Java — interfejs API tłumaczenia tekstu w usłudze Translator'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start dowiesz się, jak wykryć język dostarczonego tekstu przy użyciu języka Java i interfejsu API REST tłumaczenia tekstu w usłudze Translator.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: ed10c673e0cc76c6ed195027c9b28f7ab6084176
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64916724"
---
# <a name="quickstart-use-the-translator-text-api-to-detect-text-language-using-java"></a>Szybki start: Korzystanie z interfejsu API tłumaczenia tekstu w usłudze Translator do wykrywania języka tekstu przy użyciu języka Java

W tym przewodniku Szybki start dowiesz się, jak wykryć język dostarczonego tekstu przy użyciu języka Java i interfejsu API REST tłumaczenia tekstu w usłudze Translator.

Ten przewodnik Szybki start wymaga [konta usługi Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z zasobem tłumaczenia tekstu w usłudze Translator. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/), aby uzyskać klucz subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

* [Zestaw JDK 7 lub nowszy](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Narzędzie [Gradle](https://gradle.org/install/)
* Klucz subskrypcji platformy Azure na potrzeby tłumaczenia tekstu w usłudze Translator

## <a name="initialize-a-project-with-gradle"></a>Inicjowanie projektu za pomocą narzędzia Gradle

Zaczniemy od utworzenia katalogu roboczego dla tego projektu. W wierszu polecenia (lub terminalu) uruchom następujące polecenie:

```console
mkdir detect-sample
cd detect-sample
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
    mainClassName = "Detect"
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

Następnie utwórz w tym folderze plik o nazwie `Detect.java`.

## <a name="import-required-libraries"></a>Importowanie wymaganych bibliotek

Otwórz plik `Detect.java` i dodaj następujące instrukcje import:

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
public class Detect {
  // All project code goes here...
}
```

Dodaj następujące wiersze do klasy `Detect`:

```java
String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
String url = "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0";
```

## <a name="create-a-client-and-build-a-request"></a>Tworzenie klienta i budowanie żądania

Dodaj następujący wiersz do klasy `Detect` w celu utworzenia wystąpienia klasy `OkHttpClient`:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Następnie utworzymy żądanie POST. Możesz zmienić tekst, którego język ma zostać wykryty.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"Salve mondo!\"\n}]");
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
        Detect detectRequest = new Detect();
        String response = detectRequest.Post();
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

Znajdź skrót kraju, w tym [listę języków](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

```json
[
  {
    "language": "it",
    "score": 1.0,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "en",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z kodem przykładowym z tego przewodnika Szybki start i innych, dotyczącym między innymi tłumaczenia i transliteracji, a także z innymi projektami przykładowymi dotyczącymi tłumaczenia tekstu w usłudze Translator i znajdującymi się w usłudze GitHub.

> [!div class="nextstepaction"]
> [Poznaj przykłady dla języka Java w usłudze GitHub](https://aka.ms/TranslatorGitHub?type=&language=java)

## <a name="see-also"></a>Zobacz też

* [Tłumaczenie tekstu](quickstart-java-translate.md)
* [Transliteracja tekstu](quickstart-java-transliterate.md)
* [Identyfikowanie język na podstawie danych wejściowych](quickstart-java-detect.md)
* [Uzyskiwanie alternatywnych tłumaczeń](quickstart-java-dictionary.md)
* [Pobieranie listy obsługiwanych języków](quickstart-java-languages.md)
* [Określanie długości zdań na podstawie danych wejściowych](quickstart-java-sentences.md)
