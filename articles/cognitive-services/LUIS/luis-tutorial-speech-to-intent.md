---
title: Używaj mowy zestawu SDK języka C# z użyciem usługi LUIS — Azure | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Użyj przykładu zestawu SDK języka C# mowy głosu do mikrofonu i uzyskiwać prognozy intencje i podmioty LUIS zwracane.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/26/2018
ms.author: diberry
ms.openlocfilehash: aadca428fa076d697cc0f893673672850ddc27d4
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43124400"
---
# <a name="integrate-speech-service"></a>Integracja usługi mowy
[Usługa rozpoznawania mowy](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/) umożliwia użycie pojedynczego żądania do odbierania audio i zwracać prognoz usługi LUIS obiekty JSON.

W tym artykule pobranie i używanie projektu C# w programie Visual Studio do odczytania wypowiedź do mikrofonu i otrzymywanie informacji prognoz usługi LUIS. Projekt używa mowy [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) pakietu już dołączone jako odwołanie. 

W tym artykule potrzebne bezpłatne [LUIS] [ LUIS] konta witryny sieci Web, aby zaimportować aplikację.

## <a name="create-luis-endpoint-key"></a>Tworzenie klucza punktu końcowego usługi LUIS
W witrynie Azure portal [tworzenie](luis-how-to-azure-subscription.md#create-luis-endpoint-key) **Language Understanding** klucza (LUIS). 

## <a name="import-human-resources-luis-app"></a>Importuj zasoby ludzkie LUIS aplikacji
Opcje, a wypowiedzi w tym artykule pochodzą z aplikacji usługi LUIS zarządzania zasobami ludzkimi, dostępnej w sklepie [przykłady LUIS](https://github.com/Microsoft/LUIS-Samples) repozytorium Github. Pobierz [HumanResources.json](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/HumanResources.json) pliku, zapisz go z rozszerzeniem *.json i [zaimportować](luis-how-to-start-new-app.md#import-new-app) go do usługi LUIS. 

Ta aplikacja ma intencji, jednostek i wypowiedzi związane z domeny zarządzania zasobami ludzkimi. Przykład wypowiedzi obejmują:

```
Who is John Smith's manager?
Who does John Smith manage?
Where is Form 123456?
Do I have any paid time off?
```

## <a name="add-keyphrase-prebuilt-entity"></a>Dodaj KeyPhrase wstępnie utworzone jednostki
Po zaimportowaniu aplikacji, wybierz **jednostek**, następnie **Zarządzanie ze wstępnie utworzonych jednostek**. Dodaj **KeyPhrase** jednostki. Jednostka KeyPhrase wyodrębnia klucza posiadaczem z wypowiedź.

## <a name="train-and-publish-the-app"></a>Uczenie i publikowanie aplikacji
1. Na pasku nawigacyjnym z góry, prawej wybierz **szkolenie** przycisk to w opracowywaniu aplikacji usługi LUIS.

2. Wybierz **Publikuj** można przejść do strony publikowania. 

3. W dolnej części **Publikuj** strony, należy dodać klucz usługi LUIS utworzone w [klucza punktu końcowego tworzenia usługi LUIS](#create-luis-endpoint-key) sekcji.

4. Publikowanie aplikacji usługi LUIS, wybierając **Publikuj** przycisk z prawej strony gniazda publikowania. 

  Na **Publikuj** strony, zbieraj Identyfikatora aplikacji, publikowanie regionu i subskrypcji Identyfikatora klucza usługi LUIS, utworzone w [klucza punktu końcowego tworzenia usługi LUIS](#create-luis-endpoint-key) sekcji. Należy zmodyfikować kod, aby użyć tych wartości w dalszej części tego artykułu. 

  Te wartości są uwzględnione w adresu URL punktu końcowego w dolnej części **Publikuj** strony o klucz, który został utworzony. 
  
  Czy **nie** użyć klucza bezpłatne początkowy na potrzeby tego ćwiczenia. Tylko **Language Understanding** klucza utworzonego w witrynie Azure portal będzie działać na potrzeby tego ćwiczenia. 

  https://**REGION**.api.cognitive.microsoft.com/luis/v2.0/apps/**APPID**? klucz subskrypcji =**LUISKEY**& q =

## <a name="audio-device"></a>Urządzenia audio
W tym artykule używa urządzenia audio na tym komputerze. Który może być zestaw słuchawkowy, mogą przy użyciu mikrofonu lub wbudowane urządzenia audio. Sprawdź audio poziom danych wejściowych, aby zobaczyć, jeśli należy powiesz głośniej niż zwykle zapewnienie mowy wykrywane przez urządzenie audio. 

## <a name="download-the-luis-sample-project"></a>Pobierz przykładowy LUIS projekt
 Klonuj lub Pobierz [przykłady LUIS](https://github.com/Microsoft/LUIS-Samples) repozytorium. Otwórz [mowy do projektu intencji](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-speech-intent-recognition) z programem Visual Studio i przywracanie pakietów NuGet. Plik rozwiązania programu VS jest.\LUIS-Samples-master\documentation-samples\tutorial-speech-intent-recognition\csharp\csharp_samples.sln.

Zestaw SDK rozpoznawania mowy jest już zawarte jako odwołanie. 

[![](./media/luis-tutorial-speech-to-intent/nuget-package.png "Wyświetlanie pakietu Microsoft.CognitiveServices.Speech NuGet zrzut ekranu programu Visual Studio 2017")](./media/luis-tutorial-speech-to-intent/nuget-package.png#lightbox)

## <a name="modify-the-c-code"></a>Modyfikowanie kodu C#
Otwórz **LUIS_samples.cs** pliku, a następnie Zmień następujące zmienne:

|Nazwa zmiennej|Przeznaczenie|
|--|--|
|luisSubscriptionKey|Odpowiada wartości klucz subskrypcji URL punktu końcowego na stronie publikowania|
|luisRegion|Odnosi się do pierwszego poddomeny URL punktu końcowego|
|luisAppId|Odnosi się do trasy adresu URL punktu końcowego firmy po **aplikacji /**|

[![](./media/luis-tutorial-speech-to-intent/change-variables.png "Zrzut ekranu programu Visual Studio 2017 wyświetlanie zmiennych LUIS_samples.cs")](./media/luis-tutorial-speech-to-intent/change-variables.png#lightbox)

Plik ma już intencji zarządzania zasobami ludzkimi mapowane.

[![](./media/luis-tutorial-speech-to-intent/intents.png "Zrzut ekranu programu Visual Studio 2017 wyświetlanie LUIS_samples.cs intencji")](./media/luis-tutorial-speech-to-intent/intents.png#lightbox)

Skompiluj i uruchom aplikację. 

## <a name="test-code-with-utterance"></a>Badanie kodu za pomocą wypowiedź
Wybierz **1** i mówi do mikrofonu "Będącego Menedżera Jan Kowalski".

```cmd
1. Speech recognition of LUIS intent.
0. Stop.
Your choice: 1
LUIS...
Say something...
ResultId:cc83cebc9d6040d5956880bcdc5f5a98 Status:Recognized IntentId:<GetEmployeeOrgChart> Recognized text:<Who is the manager of John Smith?> Recognized Json:{"DisplayText":"Who is the manager of John Smith?","Duration":25700000,"Offset":9200000,"RecognitionStatus":"Success"}. LanguageUnderstandingJson:{
  "query": "Who is the manager of John Smith?",
  "topScoringIntent": {
    "intent": "GetEmployeeOrgChart",
    "score": 0.617331
  },
  "entities": [
    {
      "entity": "manager of john smith",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 31
    }
  ]
}

Recognition done. Your Choice:

```

Celem poprawne **GetEmployeeOrgChart**, znaleziono bez obaw 61%. Jednostka keyPhrase została zwrócona. 

Zestaw SDK rozpoznawania mowy zwraca całej odpowiedzi usługi LUIS. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy nie jest już potrzebny, usuń ją kadry usługi LUIS. Aby to zrobić, wybierz wielokropek (**...**) po prawej stronie nazwy aplikacji na liście aplikacji i wybierz polecenie ***Delete*** (Usuń). W wyskakującym oknie dialogowym **Delete app?** (Usunąć aplikację?) wybierz pozycję **OK**.

Pamiętaj, aby usunąć katalog usługi LUIS-Samples, gdy wszystko będzie gotowe korzystającą z przykładowego kodu.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Integrowanie usługi LUIS z BOTEM](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website