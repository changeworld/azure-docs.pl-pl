---
title: Mowy SDK C# za pomocą LUIS - Azure | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Użyj przykładu mowy C# SDK głosu do mikrofonu i pobrać prognoz zamiar i jednostki LUIS zwrócony.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/26/2018
ms.author: v-geberr;
ms.openlocfilehash: b681598f953d217ca636fb5c0adc3de4ddbebd60
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37031791"
---
# <a name="integrate-speech-service"></a>Integrowanie usługi mowy
[Mowy usługi](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/) umożliwia przy użyciu pojedynczego żądania odbierania audio i zwracany LUIS prediction obiektów JSON.

W tym artykule pobranie i używanie projektu C# w programie Visual Studio głosu utterance do mikrofonu i otrzymywanie informacji, LUIS prognozowania. Projekt używa mowy [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) pakietu już uwzględniona jako odwołanie. 

W tym artykule należy bezpłatny [LUIS] [ LUIS] konta witryny sieci Web, aby zaimportować aplikację.

## <a name="create-luis-endpoint-key"></a>Utwórz klucz punktu końcowego LUIS
W portalu Azure [utworzyć](luis-how-to-azure-subscription.md#create-luis-endpoint-key) **opis języka** klucza (LUIS). 

## <a name="import-human-resources-luis-app"></a>Importowanie zasobów ludzkich LUIS aplikacji
Intencje i zniesławiających, w tym artykule pochodzą z aplikacji LUIS kadr dostępnej w sklepie [przykłady LUIS](https://github.com/Microsoft/LUIS-Samples) repozytorium Github. Pobierz [HumanResources.json](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/HumanResources.json) pliku, Zapisz plik z rozszerzeniem *.json i [zaimportować](create-new-app.md#import-new-app) go do LUIS. 

Ta aplikacja ma lokalizacji docelowych, jednostki i zniesławiających związane z domeny zasobów ludzkich. Przykład zniesławiających obejmują:

```
Who is John Smith's manager?
Who does John Smith manage?
Where is Form 123456?
Do I have any paid time off?
```

## <a name="add-keyphrase-prebuilt-entity"></a>Dodaj KeyPhrase wbudowane jednostki
Po zaimportowaniu aplikacji, wybierz **jednostek**, następnie **Zarządzanie wbudowane jednostek**. Dodaj **KeyPhrase** jednostki. Jednostka KeyPhrase wyodrębnia klucza tematyczne z utterance.

## <a name="train-and-publish-the-app"></a>Szkolenie i publikowanie aplikacji
1. Na pasku nawigacyjnym góry, prawej, wybierz **uczenia** przycisku do uczenia LUIS aplikacji.

2. Wybierz **publikowania** aby przejść do strony publikowania. 

3. W dolnej części **publikowania** Dodaj klucz LUIS utworzone w [LUIS Utwórz klucz punktu końcowego](#create-luis-endpoint-key) sekcji.

4. Publikowanie aplikacji LUIS wybierając **publikowania** przycisku z prawej strony Opublikuj gnieździe. 

  Na **publikowania** strony, zbieranie identyfikator aplikacji, Opublikuj regionu i identyfikator subskrypcji klucza LUIS utworzone w [LUIS Utwórz klucz punktu końcowego](#create-luis-endpoint-key) sekcji. Należy zmodyfikować kod, aby użyć tych wartości w dalszej części tego artykułu. 

  Te wartości są uwzględniane w adresie URL punktu końcowego w dolnej części **publikowania** strony o klucz został utworzony. 
  
  Czy **nie** użyć klucza wolnego starter w tym ćwiczeniu. Tylko **opis języka** klucza utworzone w portalu Azure będzie działać dla tego testu. 

  https://**REGION**.api.cognitive.microsoft.com/luis/v2.0/apps/**APPID**? subskrypcji klucz =**LUISKEY**& q =

## <a name="audio-device"></a>Urządzenie audio
W tym artykule używa urządzenia audio na komputerze. Który może być słuchawki z mikrofon lub wbudowane urządzenia audio. Sprawdź audio poziom wejściowych, aby zobaczyć, jeśli użytkownik powinien mowy głośniej niż zwykle mają mowy wykrytych przez urządzenie audio. 

## <a name="download-the-luis-sample-project"></a>Pobierz LUIS przykładowy projekt
 Klonuj lub pobrać [przykłady LUIS](https://github.com/Microsoft/LUIS-Samples) repozytorium. Otwórz [mowy do konwersji projektu](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-speech-intent-recognition) z programem Visual Studio i przywracania pakietów NuGet. Plik rozwiązania VS jest.\LUIS-Samples-master\documentation-samples\tutorial-speech-intent-recognition\csharp\csharp_samples.sln.

Zestaw SDK mowy jest już uwzględniona jako odwołanie. 

[![](./media/luis-tutorial-speech-to-intent/nuget-package.png "Zrzut ekranu programu Visual Studio 2017 wyświetlania pakietu Microsoft.CognitiveServices.Speech NuGet")](./media/luis-tutorial-speech-to-intent/nuget-package.png#lightbox)

## <a name="modify-the-c-code"></a>Modyfikowanie kodu C#
Otwórz **LUIS_samples.cs** plików i Zmień następujące zmienne:

|Nazwa zmiennej|Przeznaczenie|
|--|--|
|luisSubscriptionKey|Odpowiada wartości klucza subskrypcji końcowego adresu URL ze strony publikacji|
|luisRegion|Odpowiada pierwszy poddomeny końcowego adresu URL|
|luisAppId|Odnosi się do końcowego adresu URL trasy po **aplikacji /**|

[![](./media/luis-tutorial-speech-to-intent/change-variables.png "Zrzut ekranu programu Visual Studio 2017 wyświetlanie LUIS_samples.cs zmiennych")](./media/luis-tutorial-speech-to-intent/change-variables.png#lightbox)

Plik jest już intencje kadr zamapowane.

[![](./media/luis-tutorial-speech-to-intent/intents.png "Zrzut ekranu programu Visual Studio 2017 wyświetlanie intencje LUIS_samples.cs")](./media/luis-tutorial-speech-to-intent/intents.png#lightbox)

Skompiluj i uruchom aplikację. 

## <a name="test-code-with-utterance"></a>Kod testu z utterance
Wybierz **1** i powiedz do mikrofonu "Kto jest Menedżera Jan Kowalski".

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

Prawidłowe opcje **GetEmployeeOrgChart**, znaleziono bez obaw 61%. Jednostka keyPhrase została zwrócona. 

Zestaw SDK mowy zwraca całej odpowiedzi LUIS. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy nie są już potrzebne, usunięcie aplikacji LUIS brzmi HumanResources. Aby to zrobić, wybierz menu z trzema kropkami (...) po prawej stronie nazwy aplikacji na liście aplikacji i wybierz polecenie **Delete** (Usuń). W wyskakującym oknie dialogowym **Delete app?** (Usunąć aplikację?) wybierz pozycję **OK**.

Pamiętaj, aby usunąć katalog LUIS próbek, gdy wszystko będzie gotowe za pomocą przykładowy kod.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Integracja LUIS z robotów](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website