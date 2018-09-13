---
title: Wywołaj interfejs API przetwarzania obrazów | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wywołać interfejs API przetwarzania obrazów za pomocą interfejsu REST w usługach Cognitive Services.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 01/20/2017
ms.author: kefre
ms.openlocfilehash: 34705681e665b57a89c43f31ca695e0acb45ae3f
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35760251"
---
# <a name="how-to-call-the-computer-vision-api"></a>Jak wywołać interfejs API przetwarzania obrazów

Ten przewodnik pokazuje sposób wywołania interfejsu API przetwarzania obrazów przy użyciu interfejsu REST. Przykłady są zapisywane zarówno w języku C# przy użyciu biblioteki klienta interfejsu API przetwarzania obrazów, jak i jako wywołania HTTP POST/GET. Skupimy się na:

-   Jak uzyskać "Tags", "Description" i "Kategorie".
-   Jak można pobrać informacji o "Specyficznego dla domeny" (osobistości).

### <a name="Prerequisites">Wymagania wstępne</a> 
Adres URL obrazu lub ścieżkę do obraz przechowywany lokalnie.
  * Obsługiwane metody wejściowe: nieprzetworzone binarne w formie application/octet strumienia lub adres URL
  * Obsługiwane formaty obrazów: JPEG, PNG, GIF, BMP
  * Rozmiar pliku obrazu: mniej niż 4MB
  * Wymiar obrazu: większa niż 50 x 50 pikseli
  
W poniższych przykładach przedstawiono następujące funkcje:

1. Analizowanie obrazu i pobieranie tagów i opis zwracane.
2. Analizowanie obrazu za pomocą modelu specyficznego dla domeny (w szczególności model "osobistości") i wprowadzenie odpowiednich spowodować dostrojenie JSON.

Funkcje są podzielone na:

  * **Opcja jeden:** analizy polu — analizowanie podanego modelu
  * **Opcja 2:** analizy rozszerzony — analizowanie w celu dostarczenie dodatkowych szczegółów o [Taksonomia kategorii 86](../Category-Taxonomy.md)
  
### <a name="Step1">Krok 1: Autoryzowanie wywołania interfejsu API</a> 
Każde wywołanie interfejsu API przetwarzania obrazów wymaga klucza subskrypcji. Ten klucz musi zostać przekazany przez parametr ciągu zapytania albo określony w nagłówku żądania. 

Aby uzyskać klucz subskrypcji, zobacz [jak uzyskiwanie kluczy subskrypcji](../Vision-API-How-to-Topics/HowToSubscribe.md
).

**1.** Przekazywanie klucz subskrypcji za pomocą ciągu zapytania, poniżej przedstawiono przykład interfejsu API przetwarzania obrazów:

```https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>```

**2.** Przekazywanie klucz subskrypcji można również określić w nagłówku żądania HTTP:

```ocp-apim-subscription-key: <Your subscription key>```

**3.** Podczas korzystania z biblioteki klienta, klucz subskrypcji jest przekazywana w Konstruktorze VisionServiceClient:

```var visionClient = new VisionServiceClient(“Your subscriptionKey”);```

### <a name="Step2">Krok 2: Przekazywanie obrazu do usługi interfejsu API przetwarzania obrazów i wrócić tagów, opisów i osobistości</a>
Podstawowy sposób wykonywania wywołań interfejsu API przetwarzania obrazów to bezpośrednie przekazanie obrazu. Odbywa się to, wysyłając żądanie "POST" z typem zawartości application/octet-stream, wraz z danymi odczytu z obrazu. "Tags" i "Description" Ta metoda przekazywania będzie taka sama dla wszystkich wywołań interfejsu API przetwarzania obrazów. Jedyną różnicą będzie parametrów zapytania, które użytkownik określa. 

Oto jak uzyskać "Tags" i "Description" dla danego obrazu:

**Opcja jeden:** listy "Tags" i jednym "Description"
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```
```
using Microsoft.ProjectOxford.Vision;
using Microsoft.ProjectOxford.Vision.Contract;
using System.IO;

AnalysisResult analysisResult;
var features = new VisualFeature[] { VisualFeature.Tags, VisualFeature.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.AnalyzeImageAsync(fs, features);
}
```
**Opcja druga** pobranie listy "Tags" tylko lub na liście "Opis" tylko:

###### <a name="tags-only"></a>Tylko znaczniki:
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/tag&subscription-key=<Your subscription key>
var analysisResult = await visionClient.GetTagsAsync("http://contoso.com/example.jpg");
```

###### <a name="description-only"></a>Tylko do opisu:
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/describe&subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.DescribeAsync(fs);
}
```
### <a name="here-is-how-to-get-domain-specific-analysis-in-our-case-for-celebrities"></a>Oto jak uzyskać analizy specyficznego dla domeny (w naszym przypadku dla osobistości).

**Opcja jeden:** analizy polu — analizowanie podanego modelu
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```
Dla tej opcji wszystkie inne parametry zapytania {visualFeatures, szczegóły} nie są prawidłowe. Jeśli chcesz wyświetlić wszystkie modele, należy użyć: 
```
GET https://westus.api.cognitive.microsoft.com/vision/v2.0/models 
var models = await visionClient.ListModelsAsync();
```
**Opcja 2:** analizy rozszerzony — analizowanie w celu dostarczenie dodatkowych szczegółów o [Taksonomia kategorii 86](../Category-Taxonomy.md)

Dla aplikacji, w której chcesz uzyskać ogólny obraz analizy oprócz szczegółów z jednego lub więcej modeli specyficznych dla domeny możemy rozszerzyć interfejsu API w wersji 1, za pomocą parametru zapytania modeli.
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?details=celebrities
```
Gdy ta metoda jest wywoływana, firma Microsoft będzie wywoływać klasyfikatora 86 kategorii. Jeśli dowolnej kategorii są zgodne z modelem znane/dopasowania, drugi — dostęp próbny klasyfikatora wywołania wystąpi. Na przykład jeśli "Szczegóły = all", lub "szczegóły" dołączyć "osobistości", firma Microsoft będzie wywoływać modelu osobistości po nosi nazwę klasyfikatora 86 kategorii, a wynik obejmuje osoby kategorii. Spowoduje to zwiększenie opóźnienia i Użytkownicy zainteresowani osobistości, w porównaniu z jedną opcję.

Wszystkie parametry zapytania v1 będzie działa tak samo w tym przypadku.  Jeśli visualFeatures = kategorii nie jest określona, zostanie niejawnie uaktywniony.

### <a name="Step3">Krok 3: Pobieranie i zrozumienia dane wyjściowe JSON dla analizowanie i visualFeatures znaczniki, opis =</a>

Oto przykład:
```
  {
    “tags”: [
    {
    "name": "outdoor",
        "score": 0.976
    },
    {
    "name": "bird",
        "score": 0.95
    }
            ],
    “description”: 
    {
    "tags": [
    "outdoor",
    "bird"
            ],
    "captions": [
    {
    "text”: “partridge in a pear tree”,
            “confidence”: 0.96
    }
                ]
    }
  }
```
Pole   | Typ  | Zawartość
------|------|------|
Tagi    | obiekt    | Obiekt najwyższego poziomu dla tablicy tagów
tagi []. Nazwa | ciąg    | Słowo kluczowe z klasyfikatora tagów
tagi []. Wynik    | numer    | Współczynnik ufności od 0 do 1.
description  | obiekt   | Obiekt najwyższego poziomu, aby uzyskać opis.
[] Description.Tags |    ciąg  | Lista tagów.  Jeśli występują niewystarczające zaufania możliwość utworzenia podpis, znaczniki może być tylko informacji dostępnych do obiektu wywołującego.
Description.Captions[].Text | ciąg    | Frazę opisujące obraz.
Description.Captions[].Confidence   | numer    | Poziom zaufania dla wyrażenia.

### <a name="Step4">Krok 4: Pobieranie i zrozumienia dane wyjściowe JSON modeli specyficznych dla domeny</a>

**Opcja jeden:** analizy polu — analizowanie podanego modelu

Dane wyjściowe będą o znacznikach, jest przykładem podobną do przykładowej:
```
  { 
    "result": [ 
      { 
    "name": "golden retriever", 
    "score": 0.98
    },
    { 
    "name": "Labrador retriever", 
    "score": 0.78
    }
              ]
  }
```

**Opcja 2:** analiza rozszerzony - analizować, aby zapewnić dodatkowe szczegóły Taksonomia kategorii 86

W przypadku modeli specyficznych dla domeny przy użyciu dwóch opcji (analiza rozszerzone) kategorie zwracać typ zostanie przedłużony. Następujący przykład:
```
  {
    "requestId": "87e44580-925a-49c8-b661-d1c54d1b83b5",
    "metadata":     {
      "width": 640,
      "height": 430,
      "format": "Jpeg"
                    },
    "result": {
      "celebrities": 
      [
        {
        "name": "Richard Nixon",
        "faceRectangle": {
          "left": 107,
          "top": 98,
          "width": 165,
          "height": 165
                         },
        "confidence": 0.9999827
        }
      ]
  }
```

Pole kategorii znajduje się lista co najmniej jeden z [kategorie 86](../Category-Taxonomy.md) oryginalnego taksonomii. Należy zauważyć, że kategorie kończące się na znaku podkreślenia będą zgodne w tej kategorii i jego elementów podrzędnych (na przykład people_ także people_group modelu osobistości).

Pole   | Typ  | Zawartość
------|------|------|
kategorie | obiekt | Obiekt najwyższego poziomu
.name [] kategorii    | ciąg   | Nadaj nazwę z Taksonomia kategorii 86
kategorie [] .score  | numer    | Współczynnik ufności, od 0 do 1
kategorie [] .detail  | obiekt?      | Opcjonalne szczegóły obiektu

Należy pamiętać, że jeśli odpowiada wielu kategorii (na przykład kategoria 86 klasyfikatora zwraca wynik dla people_ i people_young, gdy model = osobistości), szczegóły są dołączone do najbardziej ogólnym poziomie dopasowania (people_ w tym przykładzie).

### <a name="Errors">Odpowiedzi na błędy</a>
Te są identyczne z vision.analyze, z powodu błędu dodatkowe NotSupportedModel błędu (HTTP 400), które mogą być zwracane w scenariuszach jedną z opcji i dwóch opcji. Dla opcji dwóch (analiza rozszerzony), jeśli dowolny modele określone w szczegółach nie zostanie rozpoznany, interfejs API zwróci NotSupportedModel nawet, jeśli co najmniej jeden z nich są prawidłowe.  Użytkownicy mogą wywołać listModels, aby dowiedzieć się, jakie modele są obsługiwane.

### <a name="Summary">Podsumowanie</a>

Oto podstawowe funkcje interfejsu API przetwarzania obrazów: jak przekazywać obrazy i pobierania metadanych cenne w zamian.

Aby korzystać z interfejsu API REST, przejdź do [dokumentacja interfejsu API przetwarzania komputera](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44).
 
