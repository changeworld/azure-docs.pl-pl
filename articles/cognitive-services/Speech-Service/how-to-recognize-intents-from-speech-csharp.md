---
title: 'Samouczek: rozpoznawanie intencji z mowy przy użyciu zestawu Speech SDK dla języka C#'
titleSuffix: Azure Cognitive Services
description: Z tego samouczka dowiesz się, jak rozpoznawać intencje z mowy przy użyciu zestawu Speech SDK dla języka C#.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: d61141a0955f916b1d4bfeabb22454ec38415cea
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603238"
---
# <a name="tutorial-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>Samouczek: rozpoznawanie intencji z mowy przy użyciu zestawu Speech SDK dla języka C#

Zestaw [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) usługi Cognitive Services jest integrowany z usługą [Language Understanding Service (LUIS)](https://www.luis.ai/home) w celu udostępnienia funkcji **rozpoznawania intencji**. Intencja to coś, co użytkownik chce zrobić: zarezerwować lot, sprawdzić prognozę pogody lub zadzwonić. Następnie może on użyć terminów, które wydają się naturalne. Korzystając z uczenia maszynowego, usługa LUIS mapuje żądania użytkownika na zdefiniowane intencje.

> [!NOTE]
> Aplikacja usługi LUIS definiuje intencje i jednostki, które chcesz rozpoznawać. Jest ona używana oddzielnie od aplikacji języka C#, która korzysta z usługi rozpoznawania mowy. W tym artykule „aplikacja” oznacza aplikację usługi LUIS, a „zastosowanie” oznacza kod języka C#.

W tym samouczku użyjesz zestawu Speech SDK do utworzenia aplikacji konsolowej języka C#, w której intencje będą określane jako pochodne wypowiedzi użytkowników przy użyciu mikrofonu urządzenia. Omawiane tematy:

> [!div class="checklist"]
> * Tworzenie projektu programu Visual Studio odwołującego się do pakietu NuGet zestawu Speech SDK
> * Tworzenie konfiguracji rozpoznawania mowy i uzyskiwanie aparatu rozpoznawania intencji
> * Uzyskiwanie modelu dla aplikacji usługi LUIS i dodawanie potrzebnych intencji
> * Określanie języka funkcji rozpoznawania mowy
> * Rozpoznawanie mowy z pliku
> * Używanie asynchronicznego, opartego na zdarzeniach rozpoznawania ciągłego

## <a name="prerequisites"></a>Wymagania wstępne

Przed przystąpieniem do wykonywania kroków opisanych w tym samouczku upewnij się, że dysponujesz poniższymi elementami.

* Konto usługi LUIS. Możesz uzyskać je bezpłatnie za pośrednictwem [portalu usługi LUIS](https://www.luis.ai/home).
* Program Visual Studio 2017 (dowolna wersja).

## <a name="luis-and-speech"></a>Usługa LUIS i mowa

Usługa LUIS integruje się z usług przetwarzania mowy, rozpoznawał intencji z wypowiedzi. Nie potrzebujesz subskrypcji usług przetwarzania mowy, po prostu usługi LUIS.

Usługa LUIS używa dwóch rodzajów kluczy:

|Typ klucza|Przeznaczenie|
|--------|-------|
|authoring|umożliwia programowe tworzenie i modyfikowanie aplikacji LUIS|
|endpoint |autoryzuje dostęp do danej aplikacji usługi LUIS|

Klucz punktu końcowego to klucz usługi LUIS potrzebny w tym samouczku. W tym samouczku jest używana przykładowa aplikacja usługi LUIS Home Automation, którą można utworzyć, wykonując kroki opisane w temacie [Używanie wstępnie utworzonej aplikacji Home Automation](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app). Jeśli masz już utworzoną własną aplikację usługi LUIS, możesz jej użyć.

Podczas tworzenia aplikacji usługi LUIS klucz początkowy jest generowany automatycznie, aby można było przetestować aplikację przy użyciu zapytań tekstowych. Ten klucz nie umożliwia integracji usług przetwarzania mowy i nie będzie działać w tym samouczku. Musisz utworzyć zasób usługi LUIS na pulpicie nawigacyjnym platformy Azure i przypisać go do aplikacji usługi LUIS. Na potrzeby tego samouczka możesz używać subskrypcji warstwy Bezpłatna.

Po utworzeniu zasobu usługi LUIS na pulpicie nawigacyjnym platformy Azure zaloguj się do [portalu usługi LUIS](https://www.luis.ai/home), wybierz aplikację na stronie Moje aplikacje, a następnie przejdź do strony zarządzania aplikacją. Na koniec kliknij pozycję **Klucze i punkty końcowe** na pasku bocznym.

![Ustawienia kluczy i punktów końcowych usługi LUIS](media/sdk/luis-keys-endpoints-page.png)

Na stronie ustawień punktu końcowego i kluczy:

1. Przewiń w dół do sekcji zasobów i kluczy, a następnie kliknij pozycję **Przypisz zasób**.
1. W oknie dialogowym **Przypisywanie klucza do aplikacji** wybierz następujące pozycje:

    * Wybierz pozycję Microsoft jako dzierżawę.
    * W obszarze nazwy subskrypcji wybierz subskrypcję platformy Azure, która zawiera zasób usługi LUIS do użycia.
    * W obszarze klucza wybierz zasób usługi LUIS, którego chcesz użyć z aplikacją.

Za chwilę w tabeli w dolnej części strony zostanie wyświetlona nowa subskrypcja. Kliknij ikonę obok klucza, aby skopiować go do schowka. (Możesz użyć dowolnego klucza).

![Klucze subskrypcji aplikacji usługi LUIS](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>Tworzenie projektu rozpoznawania mowy w programie Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>Dodawanie kodu

Otwórz plik `Program.cs` w projekcie programu Visual Studio i zastąp blok instrukcji `using` na początku pliku poniższymi deklaracjami.

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

Wewnątrz wybranej metody `Main()` dodaj poniższy kod.

```csharp
RecognizeIntentAsync().Wait();
Console.WriteLine("Please press Enter to continue.");
Console.ReadLine();
```

Utwórz pustą metodę asynchroniczną `RecognizeIntentAsync()`, jak pokazano w tym miejscu.

```csharp
static async Task RecognizeIntentAsync()
{
}
```

W treści nowej metody dodaj poniższy kod.

[!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

Zastąp symbole zastępcze w tej metodzie kluczem subskrypcji, regionem i identyfikatorem aplikacji usługi LUIS w pokazany poniżej sposób.

|Symbol zastępczy|Zamień na|
|-----------|------------|
|`YourLanguageUnderstandingSubscriptionKey`|Klucz punktu końcowego usługi LUIS. Jak wspomniano wcześniej, musi być to klucz uzyskany z pulpitu nawigacyjnego platformy Azure, a nie „klucz początkowy”. Możesz znaleźć go na stronie kluczy i punktów końcowych aplikacji (w obszarze Zarządzanie) w [portalu usługi LUIS](https://www.luis.ai/home).|
|`YourLanguageUnderstandingServiceRegion`|Krótki identyfikator regionu, w którym znajduje się Twoja subskrypcja usługi LUIS, na przykład `westus` dla regionu Zachodnie stany USA. Zobacz [Regions](regions.md) (Regiony).|
|`YourLanguageUnderstandingAppId`|Identyfikator aplikacji usługi LUIS. Możesz go znaleźć na stronie ustawień aplikacji w [portalu usługi LUIS](https://www.luis.ai/home).|

Po wprowadzeniu tych zmian można skompilować (Ctrl+Shift+B) i uruchomić (F5) aplikację z samouczka. Po wyświetleniu monitu spróbuj powiedzieć „Turn off the lights” (Wyłącz światła) do mikrofonu komputera. Wynik zostanie wyświetlony w oknie konsoli.

Poniższe sekcje zawierają omówienie kodu.


## <a name="create-an-intent-recognizer"></a>Tworzenie aparatu rozpoznawania intencji

Pierwszym krokiem rozpoznawania intencji w mowie jest utworzenie konfiguracji mowy przy użyciu regionu i klucza punktu końcowego usługi LUIS. Konfiguracje mowy mogą służyć do tworzenia aparatów rozpoznawania w przypadku różnych możliwości zestawu SDK rozpoznawania mowy. Konfiguracja mowy oferuje wiele sposobów określania subskrypcji do użycia. W tym miejscu użyjemy opcji `FromSubscription`, która uwzględnia region i klucz subskrypcji.

> [!NOTE]
> Użyj klucza i regionie Twojej subskrypcji usługi LUIS, nie z subskrypcji usług przetwarzania mowy.

Następnie utwórz aparat rozpoznawania mowy przy użyciu elementu `new IntentRecognizer(config)`. Ponieważ konfiguracja już wie, której subskrypcji użyć, nie trzeba ponownie określać punktu końcowego i klucza subskrypcji podczas tworzenia aparatu rozpoznawania.

## <a name="import-a-luis-model-and-add-intents"></a>Importowanie modelu usługi LUIS i dodawanie intencji

Teraz zaimportuj model z aplikacji usługi LUIS przy użyciu elementu `LanguageUnderstandingModel.FromAppId()` i dodaj intencje usługi LUIS, które chcesz rozpoznawać za pośrednictwem metody `AddIntent()` aparatu rozpoznawania. Te dwa kroki zwiększają dokładność rozpoznawania mowy, wskazując słowa, których użytkownik prawdopodobnie użyje w swoich żądaniach. Nie trzeba dodawać wszystkich intencji aplikacji, jeśli nie musisz rozpoznawać ich wszystkich w aplikacji.

Dodawanie intencji wymaga trzech argumentów: model usługi LUIS (który został utworzony i nosi nazwę `model`), nazwę intencji i identyfikatora elementu intent Różnica między identyfikatorem i nazwą jest następująca.

|Argument `AddIntent()`|Przeznaczenie|
|--------|-------|
|intentName |Nazwa intencji zgodnie z definicją w aplikacji usługi LUIS. Musi dokładnie odpowiadać nazwie intencji usługi LUIS.|
|intentID    |Identyfikator przypisany do rozpoznanej intencji przez zestaw SDK rozpoznawania mowy. Dopuszczalna jest dowolna wartość; nie musi odpowiadać nazwie intencji zdefiniowanej w aplikacji usługi LUIS. Jeśli na przykład ten sam kod obsługuje wiele intencji, możesz dla nich użyć tego samego identyfikatora.|

Aplikacja strona główna usługi LUIS automatyzacji ma dwie opcje: jeden włączania urządzenia i drugą na potrzeby wyłączenie urządzenia. Poniższe wiersze powodują dodanie tych intencji do aparatu rozpoznawania; zastąp trzy wiersze `AddIntent` w metodzie `RecognizeIntentAsync()` tym kodem.

```csharp
recognizer.AddIntent(model, "HomeAutomation.TurnOff", "off");
recognizer.AddIntent(model, "HomeAutomation.TurnOn", "on");
```

Zamiast opcji dodawania poszczególnych opcji, można również użyć `AddAllIntents` metody w celu dodania wszystkich intencje w modelu dla aparatu rozpoznawania.

## <a name="start-recognition"></a>Uruchamianie rozpoznawania

Po utworzeniu aparatu rozpoznawania i dodaniu intencji można rozpocząć rozpoznawanie. Zestaw Speech SDK obsługuje rozpoznawanie pojedyncze i ciągłe.

|Tryb rozpoznawania|Metody do wywołania|Wynik|
|----------------|-----------------|---------|
|Pojedyncze|`RecognizeOnceAsync()`|Zwraca rozpoznaną intencję, jeśli istnieje, po jednej wypowiedzi.|
|Ciągłe|`StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()`|Rozpoznaje wiele wypowiedzi. Emituje zdarzenia (np. `IntermediateResultReceived`), gdy wyniki są dostępne.|

Aplikacja z samouczka używa trybu pojedynczego i dlatego wywołuje metodę `RecognizeOnceAsync()` w celu rozpoczęcia rozpoznawania. Wynik to obiekt `IntentRecognitionResult` zawierający informacje o rozpoznanej intencji. Odpowiedź JSON usługi LUIS jest wyodrębniana z następującego wyrażenia:

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

Aplikacja z samouczka nie analizuje wyniku w formacie JSON, tylko wyświetla go w oknie konsoli.

![Wyniki rozpoznawania usługi LUIS](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>Określanie języka na potrzeby rozpoznawania

Domyślnie usługa LUIS rozpoznaje intencje w języku angielskim (Stany Zjednoczone) (`en-us`). Przypisanie kodu ustawień regionalnych do właściwości `SpeechRecognitionLanguage` konfiguracji mowy umożliwia rozpoznawanie intencji w innych językach. Na przykład dodaj element `config.SpeechRecognitionLanguage = "de-de";` w naszej aplikacji z samouczka przed utworzeniem aparatu rozpoznawania, aby rozpoznawać intencje w języku niemieckim. Zobacz [Obsługiwane języki](language-support.md#speech-to-text).

## <a name="continuous-recognition-from-a-file"></a>Ciągłe rozpoznawanie z pliku

Poniższy kod ilustruje dwie dodatkowe możliwości funkcji rozpoznawania intencji, które korzystają z zestawu SDK rozpoznawania mowy. Pierwsza z nich, wspomniana wcześniej, to rozpoznawanie ciągłe, w przypadku którego aparat rozpoznawania emituje zdarzenia, gdy wyniki są dostępne. Te zdarzenia mogą być następnie przetwarzane przez udostępnione programy obsługi zdarzeń. W przypadku rozpoznawania ciągłego w celu rozpoczęcia rozpoznawania jest wywoływana metoda `StartContinuousRecognitionAsync()` aparatu rozpoznawania, a nie metoda `RecognizeOnceAsync()`.

Inna możliwość to odczytywanie dźwięku zawierającego mowę w celu przetworzenia z pliku WAV. Obejmuje to tworzenie konfiguracji audio, której można używać podczas tworzenia aparatu rozpoznawania intencji. Plik musi być plikiem jednokanałowym (mono) z częstotliwością próbkowania wynoszącą 16 kHz.

Aby wypróbować te funkcje, zastąp treść metody `RecognizeIntentAsync()` poniższym kodem.

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

Zmodyfikuj kod, aby uwzględnić identyfikator aplikacji, region i klucz punktu końcowego usługi LUIS oraz, tak jak poprzednio, dodać intencje usługi Home Automation. Zmień wartość `whatstheweatherlike.wav` na nazwę pliku audio. Następnie skompiluj i uruchom.

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Wyszukaj kod z tego artykułu w folderze samples/csharp/sharedcontent/console.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Jak rozpoznawać mowę](how-to-recognize-speech-csharp.md)
