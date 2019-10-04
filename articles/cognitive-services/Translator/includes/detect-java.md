---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 9ce4b25d26c0294cf7618b5851c0956af7687ee7
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71837528"
---
[!INCLUDE [Prerequisites](prerequisites-java.md)]

[!INCLUDE [Setup and use environment variables](setup-env-variables.md)]

## <a name="initialize-a-project-with-gradle"></a>Inicjowanie projektu za pomocą Gradle

Zacznijmy od utworzenia katalogu roboczego dla tego projektu. W wierszu polecenia (lub terminalu) Uruchom następujące polecenie:

```console
mkdir detect-sample
cd detect-sample
```

Następnie zamierzasz zainicjować projekt Gradle. To polecenie spowoduje utworzenie podstawowych plików kompilacji dla Gradle, co jest najważniejsze, `build.gradle.kts`, który jest używany w czasie wykonywania do tworzenia i konfigurowania aplikacji. Uruchom to polecenie z katalogu roboczego:

```console
gradle init --type basic
```

Po wyświetleniu monitu o wybranie **DSL**wybierz pozycję **Kotlin**.

## <a name="configure-the-build-file"></a>Konfiguruj plik kompilacji

Znajdź `build.gradle.kts` i otwórz go przy użyciu ulubionego środowiska IDE lub edytora tekstu. Następnie skopiuj w tej konfiguracji kompilacji:

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

Należy zauważyć, że ten przykład ma zależności od OkHttp dla żądań HTTP i Gson do obsługi i analizy JSON. Jeśli chcesz dowiedzieć się więcej o konfiguracjach kompilacji, zobacz [Tworzenie nowych kompilacji Gradle](https://guides.gradle.org/creating-new-gradle-builds/).

## <a name="create-a-java-file"></a>Utwórz plik Java

Utwórzmy folder dla przykładowej aplikacji. W katalogu roboczym Uruchom polecenie:

```console
mkdir -p src/main/java
```

Następnie w tym folderze Utwórz plik o nazwie `Detect.java`.

## <a name="import-required-libraries"></a>Importuj wymagane biblioteki

Otwórz `Detect.java` i Dodaj następujące instrukcje importu:

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;
```


## <a name="define-variables"></a>Definiuj zmienne

Najpierw należy utworzyć klasę publiczną dla projektu:

```java
public class Detect {
  // All project code goes here...
}
```

Dodaj te wiersze do klasy `Detect`. Zauważ, że klucz subskrypcji i punkt końcowy są odczytywane ze zmiennych środowiskowych:

```java
private static String subscriptionKey = System.getenv("TRANSLATOR_TEXT_SUBSCRIPTION_KEY");
private static String endpoint = System.getenv("TRANSLATOR_TEXT_ENDPOINT");
String url = endpoint + "/detect?api-version=3.0";
```

Jeśli używasz subskrypcji usługi Cognitive Services, musisz także uwzględnić w parametrach żądania `Ocp-Apim-Subscription-Region`. [Dowiedz się więcej o uwierzytelnianiu w ramach subskrypcji wielu usług](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-client-and-build-a-request"></a>Tworzenie klienta i tworzenie żądania

Dodaj ten wiersz do klasy `Detect`, aby utworzyć wystąpienie `OkHttpClient`:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Następnie Utwórzmy żądanie POST. Możesz zmienić tekst na potrzeby wykrywania języka.

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

## <a name="create-a-function-to-parse-the-response"></a>Utwórz funkcję, aby przeanalizować odpowiedź

Ta prosta funkcja analizuje i prettifies odpowiedź JSON z usługi tłumaczenie tekstu w usłudze Translator.

```java
// This function prettifies the json response.
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonElement json = parser.parse(json_text);
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="put-it-all-together"></a>Umieść wszystko razem

Ostatnim krokiem jest żądanie żądania i uzyskanie odpowiedzi. Dodaj te wiersze do projektu:

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

To wszystko, co jest gotowe do uruchomienia przykładowej aplikacji. W wierszu polecenia (lub sesji terminalu) przejdź do katalogu głównego katalogu roboczego i uruchom polecenie:

```console
gradle build
```

Po zakończeniu kompilacji Uruchom polecenie:

```console
gradle run
```

## <a name="sample-response"></a>Przykładowa odpowiedź

Po uruchomieniu przykładu na terminalu powinny zostać wyświetlone następujące elementy:

> [!NOTE]
> Znajdź skrót kraju/regionu na [liście języków](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

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

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z dokumentacją interfejsu API, aby zrozumieć wszystko, co można zrobić za pomocą interfejs API tłumaczenia tekstu w usłudze Translator.

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
