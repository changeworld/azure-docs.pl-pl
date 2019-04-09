---
title: Korzystanie z zestawu SDK rozpoznawania mowy dla języka C#
titleSuffix: Azure Cognitive Services
description: Usługa rozpoznawania mowy umożliwia korzystanie z jednego żądania w celu odbierania dźwięku i zwrócenia obiektów JSON zawierających przewidywania usługi LUIS. W tym artykule pobierzesz projekt w języku C# i użyjesz go w programie Visual Studio, aby wypowiedzieć się do mikrofonu i uzyskać przewidywania usługi LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 04/08/2018
ms.author: diberry
ms.openlocfilehash: 9d6173ee25f28aa884513d126c06a8a7c722098d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59273842"
---
# <a name="integrate-speech-service-with-your-language-understanding-app"></a>Integracja usługi mowy z aplikacją Language Understanding
[Usługa rozpoznawania mowy](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/) umożliwia korzystanie z jednego żądania w celu odbierania dźwięku i zwrócenia obiektów JSON zawierających przewidywania usługi LUIS. W tym artykule pobierzesz projekt w języku C# i użyjesz go w programie Visual Studio, aby wypowiedzieć się do mikrofonu i uzyskać przewidywania usługi LUIS. Projekt wykorzystuje pakiet rozpoznawania mowy [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) dołączony jako źródło informacji. 

Do celów tego artykułu potrzebne jest bezpłatne konto internetowe usługi [LUIS][LUIS] w celu zaimportowania aplikacji.

## <a name="create-luis-endpoint-key"></a>Tworzenie klucza punktu końcowego usługi LUIS
W witrynie Azure portal [tworzenie](luis-how-to-azure-subscription.md) **usługi cognitive Services** klucza (LUIS) dla aplikacji usługi LUIS.  

## <a name="import-human-resources-luis-app"></a>Importowanie aplikacji Human Resources usługi LUIS
Intencje i wypowiedzi w tym artykule pochodzą z aplikacji Human Resources usługi LUIS dostępnej w repozytorium GitHub [Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding). Pobierz plik [HumanResources.json](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources.json), zapisz go z rozszerzeniem `.json` i [zaimportuj](luis-how-to-start-new-app.md#import-new-app) do usługi LUIS. 

Ta aplikacja zawiera intencje, jednostki i wypowiedzi powiązane z domeną Human Resources. Przykładowe wypowiedzi mogą być następujące:

|Przykładowe wypowiedzi|
|--|
|Who is John Smith's manager? (Kto jest menedżerem Johna Smitha?)|
|Who does John Smith manage? (Kim zarządza John Smith?)|
|Where is Form 123456? (Gdzie jest formularz 123456?)|
|Do I have any paid time off? (Czy mam płatny urlop?)|


## <a name="add-keyphrase-prebuilt-entity"></a>Wstępnie utworzona jednostka KeyPhrase
Po zaimportowaniu aplikacji wybierz kolejno pozycje **Entitites** (Jednostki) i **Add prebuilt entities** (Dodaj wstępnie utworzoną jednostkę). Dodaj jednostkę **KeyPhrase**. Jednostka KeyPhrase wyodrębnia kluczową treść z wypowiedzi.

## <a name="train-and-publish-the-app"></a>Uczenie i publikowanie aplikacji
1. W górnej części strony na pasku nawigacji po prawej stronie wybierz przycisk **Train** (Ucz), aby wyszkolić aplikację LUIS.

2. Wybierz polecenie **Manage** (Zarządzaj) na pasku w górnej części po prawej stronie i wybierz pozycję **Keys and endpoints** (Klucze i punkty końcowe). 

3. Na stronie **Keys and endpoints** przypisz klucz usługi LUIS utworzony w sekcji [Tworzenie klucza punktu końcowego usługi LUIS](#create-luis-endpoint-key).

   Na tej stronie zbierz informacje na temat identyfikatora aplikacji, regionu publikowania oraz identyfikatora subskrypcji klucza usługi LUIS utworzonego w sekcji [Tworzenie klucza punktu końcowego usługi LUIS](#create-luis-endpoint-key). Musisz zmodyfikować kod, aby użyć tych wartości późnej w tym artykule. 
  
   **Nie** używaj bezpłatnego klucza początkowego w tym ćwiczeniu. Wyłącznie klucz usługi **Language Understanding** utworzony w witrynie Azure Portal będzie działał w tym ćwiczeniu. 

   https://**REGION**.api.cognitive.microsoft.com/luis/v2.0/apps/**APPID**?subscription-key=**LUISKEY**&q=


4. Opublikuj aplikację LUIS, wybierając przycisk **Publish** (Publikuj) na pasku w górnej części strony po prawej stronie. 

## <a name="audio-device"></a>Urządzenie audio
W tym artykule wykorzystywane jest urządzenie audio na Twoim komputerze. Może być to zestaw słuchawkowy z mikrofonem lub wbudowane urządzenie audio. Sprawdź poziomy dźwięku danych wejściowych, aby dowiedzieć się, czy musisz mówić głośniej niż zazwyczaj, aby wypowiedź została wykryta przez urządzenie audio. 

## <a name="download-the-luis-sample-project"></a>Pobieranie przykładowego projektu usługi LUIS
 Sklonuj lub pobierz repozytorium [Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding). Otwórz projekt [Speech to intent](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-speech-intent-recognition) (Zamiana mowy na intencję) w programie Visual Studio i przywróć pakiety NuGet. Plik rozwiązania programu VS to documentation-samples\tutorial-speech-intent-recognition\csharp\csharp_samples.sln.

Zestaw SDK rozpoznawania mowy jest już dołączony jako źródło informacji. 

[![Screenshot programu Visual Studio 2017, wyświetlanie pakietu Microsoft.CognitiveServices.Speech NuGet](./media/luis-tutorial-speech-to-intent/nuget-package.png "wyświetlania pakietu Microsoft.CognitiveServices.Speech NuGet zrzut ekranu programu Visual Studio 2017")](./media/luis-tutorial-speech-to-intent/nuget-package.png#lightbox)

## <a name="modify-the-c-code"></a>Modyfikowanie kodu języka C#
Otwórz plik `Program.cs`, a następnie zmień następujące zmienne:

|Nazwa zmiennej|Przeznaczenie|
|--|--|
|LUIS_assigned_endpoint_key|Odnosi się do przypisanej wartości subscription-key punktu końcowego adresu URL ze strony Publish|
|LUIS_endpoint_key_region|Odnosi się do pierwszego poddomeny URL punktu końcowego, na przykład `westus`|
|LUIS_app_ID|Odnosi się to trasy punktu końcowego adresu URL po ciągu **apps/**|

Plik `Program.cs` ma już mapowane intencje aplikacji Human Resources.

Skompiluj i uruchom aplikację. 

## <a name="test-code-with-utterance"></a>Testowanie kodu za pomocą wypowiedzi
Powiedz do mikrofonu „Who are the approved dentists in Redmond?” (Jacy są zatwierdzeni dentyści w Redmond?).

[!code-console[Command line response from spoken utterance](~/samples-luis/documentation-samples/tutorial-speech-intent-recognition/console-output.txt "Command line response from spoken utterance")]

Poprawna intencja **GetEmployeeBenefits** została odnaleziona ze współczynnikiem ufności 85%. Została zwrócona jednostka keyPhrase. 

Zestaw SDK rozpoznawania mowy zwraca całą odpowiedź usługi LUIS. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy aplikacja HumanResources usługi LUIS nie będzie już potrzebna, usuń ją. Aby to zrobić, wybierz aplikację, a następnie na kontekstowym pasku narzędzi nad listą wybierz polecenie **Delete** (Usuń). W wyskakującym oknie dialogowym **Delete app?** (Usunąć aplikację?) wybierz pozycję **OK**.

Pamiętaj, aby usunąć katalog, gdy nie korzystasz już z kodu przykładowego.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Integrowanie usługi LUIS z BOTEM](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
