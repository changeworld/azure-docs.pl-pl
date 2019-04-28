---
title: 'Szybki start: Sprawdzanie pisowni za pomocą interfejsu API REST sprawdzania pisowni Bing i języka Java'
titlesuffix: Azure Cognitive Services
description: Rozpocznij korzystanie z interfejsu API REST sprawdzania pisowni Bing, aby sprawdzać pisownię i poprawność gramatyczną.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/11/2019
ms.author: aahi
ms.openlocfilehash: a139d0558565114725c6198f64e139e5a5019c75
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61066305"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>Szybki start: Sprawdzanie pisowni za pomocą interfejsu API REST sprawdzania pisowni Bing i języka Java

Użyj tego przewodnika Szybki start, aby wykonać pierwsze wywołanie interfejsu API REST sprawdzania pisowni Bing. Ta prosta aplikacja w języku Java wysyła żądanie do interfejsu API i zwraca listę sugerowanych poprawek. Chociaż ta aplikacja jest napisana w języku Java, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania. Kod źródłowy tej aplikacji jest dostępny w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheckv7.java).

## <a name="prerequisites"></a>Wymagania wstępne

* Zestaw Java Development Kit (JDK) w wersji 7 lub nowszej.

* Importuj [gson 2.8.5.jar](https://libraries.io/maven/com.google.code.gson%3Agson) lub najbardziej aktualnej [Gson](https://github.com/google/gson) wersji. Do wykonania wiersza polecenia, należy dodać `.jar` do folderu Java z klasą główną.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy projekt języka Java w ulubionym środowisku IDE lub edytora nazwą klasy wybrane, a następnie zaimportuj następujące pakiety.

    ```java
    import java.io.*;
    import java.net.*;
    import com.google.gson.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. Utwórz zmienne dla hosta punktu końcowego interfejsu API, ścieżki i klucza subskrypcji. Następnie utwórz zmienne dla rynku i tekstu, w którym ma być sprawdzana pisownia, oraz ciąg dla trybu sprawdzania pisowni.

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "<ENTER-KEY-HERE>";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>Tworzenie i wysyłanie żądania interfejsu API

1. Utwórz funkcję o nazwie `check()`, która utworzy i wyśle żądanie interfejsu API. W ramach tej funkcji wykonaj następujące czynności. Utwórz ciąg dla parametrów żądania. Dołącz parametr `?mkt=` do ciągu rynku i parametr `&mode=` do trybu sprawdzania pisowni.  

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
      // add the rest of the code snippets here (except prettify() and main())...
   }
   ```

2. Utwórz adres URL, łącząc hosta punktu końcowego, ścieżkę i ciąg parametrów. Utwórz nową `HttpsURLConnection` obiektu.

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    ```

3. Otwórz połączenie z adresem URL. Ustaw metodę żądania na `POST`. Dodaj parametry żądania. Pamiętaj, aby dodać klucz subskrypcji do nagłówka `Ocp-Apim-Subscription-Key`.

    ```java
    connection.setRequestMethod("POST");
    connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
    connection.setDoOutput(true);
    ```

4. Utwórz nowy obiekt `DataOutputStream` i wyślij żądanie do interfejsu API.

    ```java
        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();
    ```

## <a name="format-and-read-the-api-response"></a>Formatowanie i odczytać odpowiedzi interfejsu API

1. Dodaj tę metodę do klasy. Operacja formatowania JSON czytelność danych wyjściowych.

    ``` java
    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

1. Create a `BufferedReader` and read the response from the API. Print it to the console.
    
    ```java
    BufferedReader in = new BufferedReader(
    new InputStreamReader(connection.getInputStream()));
    String line;
    while ((line = in.readLine()) != null) {
        System.out.println(prettify(line);
    }
    in.close();
    ```

## <a name="call-the-api"></a>Wywoływanie interfejsu API

W funkcji main aplikacji Wywołaj metodę check() utworzonego powyżej.

    ```java
    public static void main(String[] args) {
        try {
            check();
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
    ```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Skompiluj i uruchom projekt.

Jeśli korzystasz z wiersza polecenia, użyj następujących poleceń, aby skompilować i uruchomić aplikację.

**Kompilacja:**
```bash
javac -classpath .;gson-2.2.2.jar\* <CLASS_NAME>.java
```

**Uruchom polecenie:**
```bash
java -cp .;gson-2.2.2.jar\* <CLASS_NAME>
```

## <a name="example-json-response"></a>Przykładowa odpowiedź JSON

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON, jak pokazano w następującym przykładzie:

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](../tutorials/spellcheck.md)

- [Czym jest interfejs API sprawdzania pisowni Bing?](../overview.md)
- [Dokumentacja interfejsu API sprawdzania pisowni Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
