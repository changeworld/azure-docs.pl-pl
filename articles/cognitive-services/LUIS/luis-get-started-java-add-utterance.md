---
title: Zmienianie, uczenie aplikacji, Java — LUIS
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start języka Java dodasz przykładowe wypowiedzi do aplikacji Home Automation i przeprowadzisz uczenie aplikacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: diberry
ms.openlocfilehash: bfe195ecd4dd777d5073c03c2e4fbd4bfcaaaa06
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560585"
---
# <a name="quickstart-change-model-using-java"></a>Szybki start: zmiana modelu przy użyciu języka Java 

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-change-model-intro-para.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Quickstart prerequisites for endpoint](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* [Biblioteka JSON GSON firmy Google](https://github.com/google/gson).

[!INCLUDE [Quickstart note about code repository](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>Plik JSON z przykładowymi wypowiedziami

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>Tworzenie kodu przewodnika Szybki start

1. Dodaj zależności Java do pliku o nazwie `AddUtterances.java`.

   [!code-java[Java Dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=23-26 "Java Dependencies")]

2. Utwórz klasę `AddUtterances`. Ta klasa zawiera wszystkie fragmenty kodu znajdujące się poniżej.

    ```Java
    public class AddUtterances {
        // Insert code here
    }
    ```

3. Dodaj stałe usługi LUIS do klasy. Skopiuj poniższy kod i zmień na swój klucz tworzenia, identyfikator aplikacji i identyfikator wersji.

   [!code-java[LUIS-based IDs](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=33-44 "LUIS-based IDs")]

4. Dodaj metodę umożliwiającą wykonywanie wywołań interfejsu API usługi LUIS do klasy AddUtterances. 

   [!code-java[HTTP request to LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=46-168 "HTTP request to LUIS")]

5. Dodaj metodę na potrzeby odpowiedzi HTTP z interfejsu API usługi LUIS do klasy AddUtterances.

   [!code-java[HTTP response from LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=170-202 "HTTP response from LUIS")]

6. Dodaj obsługę wyjątków do klasy AddUtterances. 

   [!code-java[Exception Handling](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=205-243 "Exception Handling")]

7. Dodaj funkcję main do klasy AddUtterances.

   [!code-java[Add main function](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=245-278 "Add main function")]

## <a name="build-code"></a>Kompilowanie kodu

Kompilowanie elementu AddUtterance z zależnościami

```console
> javac -classpath gson-2.8.2.jar AddUtterances.java
```

## <a name="run-code"></a>Uruchamianie kodu
Wywołanie polecenia `AddUtterance` bez żadnych argumentów powoduje dodanie wypowiedzi usługi LUIS do aplikacji bez jej uczenia.

```console
> java -classpath .;gson-2.8.2.jar AddUtterances
```

W tym wierszu polecenia wyświetlane są wyniki wywołania interfejsu API dodawania wypowiedzi. 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Po ukończeniu przewodnika Szybki start usuń wszystkie pliki utworzone w tym przewodniku Szybki start. 

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"] 
> [Programowe tworzenie aplikacji LUIS](luis-tutorial-node-import-utterances-csv.md) 
