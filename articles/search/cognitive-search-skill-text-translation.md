---
title: Umiejętności poznawcze tłumaczenia tekstu
titleSuffix: Azure Cognitive Search
description: Oblicza tekst i dla każdego rekordu zwraca tekst przetłumaczony na określony język docelowy w potoku wzbogacania sztucznej inteligencji w usłudze Azure Cognitive Search.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5089174fcfd5a97128c1f789b818243243a5282f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460771"
---
#   <a name="text-translation-cognitive-skill"></a>Umiejętności poznawcze tłumaczenia tekstu

Umiejętności **Tłumaczenie tekstu** obliczają tekst i dla każdego rekordu zwracają tekst przetłumaczony na określony język docelowy. Ta umiejętność używa [interfejsu API tekstu tłumacza w wersji 3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) dostępnej w usługach Cognitive Services.

Ta funkcja jest przydatna, jeśli można oczekiwać, że dokumenty nie wszystkie mogą być w jednym języku, w którym to przypadku można znormalizować tekst do jednego języka przed indeksowania do wyszukiwania przez jego tłumaczenie.  Jest to również przydatne w przypadku użycia lokalizacji, gdzie można mieć kopie tego samego tekstu dostępne w wielu językach.

[Interfejs API tekstu tłumacza w wersji 3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) jest nieregiona regionalną usługą cognitive service, co oznacza, że dane nie są gwarantowane do pozostania w tym samym regionie, co usługa Azure Cognitive Search lub dołączony zasób usług Cognitive Services.

> [!NOTE]
> W miarę rozszerzania zakresu poprzez zwiększanie częstotliwości przetwarzania, dodawanie większej liczby dokumentów lub dodawanie kolejnych algorytmów sztucznej inteligencji należy [dołączyć rozliczany zasób usług Cognitive Services.](cognitive-search-attach-cognitive-services.md) Opłaty naliczane podczas wywoływania interfejsów API w usługach Cognitive Services i wyodrębniania obrazu w ramach etapu pękania dokumentów w usłudze Azure Cognitive Search. Nie ma żadnych opłat za wyodrębnianie tekstu z dokumentów.
>
> Wykonanie wbudowanych umiejętności jest naliczane według istniejącej [ceny płatności zgodnie z rzeczywistymi oczekiwaniami.](https://azure.microsoft.com/pricing/details/cognitive-services/) Ceny wyodrębniania obrazów są opisane na [stronie cennika usługi Azure Cognitive Search](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.TranslationSkill

## <a name="data-limits"></a>Limity danych
Maksymalny rozmiar rekordu powinien wynosić 50 000 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)znaków mierzonych za pomocą pliku . Jeśli musisz podzielić dane przed wysłaniem ich do umiejętności tłumaczenia tekstu, rozważ użycie [umiejętności Dzielenie tekstu](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry umiejętności

W nazwach parametrów jest rozróżniana wielkość liter.

| Dane wejściowe                | Opis |
|---------------------|-------------|
| defaultToLanguageCode | (Wymagane) Kod języka do tłumaczenia dokumentów na dokumenty, które nie określają języka do jawnie. <br/> Zobacz [Pełna lista obsługiwanych języków](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |
| defaultFromLanguageCode | (Opcjonalnie) Kod języka do tłumaczenia dokumentów z dokumentów, które nie określają języka from jawnie.  Jeśli defaultFromLanguageCode nie jest określony, automatyczne wykrywanie języka dostarczone przez interfejs API tekstu translatora będzie używany do określenia języka z. <br/> Zobacz [Pełna lista obsługiwanych języków](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |
| sugerowaneOd | (Opcjonalnie) Kod języka do tłumaczenia dokumentów, gdy nie są dostarczane ani wejście fromLanguageCode, ani parametr defaultFromLanguageCode, a automatyczne wykrywanie języka nie powiedzie się.  Jeśli sugerowanyOd języka nie jest określony, angielski (en) będzie używany jako sugerowanyZ języka. <br/> Zobacz [Pełna lista obsługiwanych języków](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |

## <a name="skill-inputs"></a>Wprowadzanie umiejętności

| Nazwa wejściowa     | Opis |
|--------------------|-------------|
| tekst | Tekst do przetłumaczenia.|
| toLanguageCode (kod języka)    | Ciąg wskazujący język, na który tekst powinien zostać przetłumaczony. Jeśli to dane wejściowe nie jest określony, defaultToLanguageCode będzie używany do tłumaczenia tekstu. <br/>Zobacz [Pełna lista obsługiwanych języków](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|
| od Kod językowy  | Ciąg wskazujący bieżący język tekstu. Jeśli ten parametr nie jest określony, do tłumaczenia tekstu zostanie użyty domyślnyzlanguagecode (lub automatyczne wykrywanie języka, jeśli nie podano defaultFromLanguageCode). <br/>Zobacz [Pełna lista obsługiwanych języków](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|

## <a name="skill-outputs"></a>Wyniki umiejętności

| Nazwa wyjściowa    | Opis |
|--------------------|-------------|
| translatedTekst | Wynik ciągu tłumaczenia tekstu z translatedFromLanguageCode do translatedToLanguageCode.|
| translatedToLanguageCode  | Ciąg wskazujący kod języka, na który został przetłumaczony tekst. Przydatne, jeśli tłumaczysz na wiele języków i chcesz mieć możliwość śledzenia, który tekst jest tym, który język.|
| translatedFromLanguageCode    | Ciąg wskazujący kod języka, z którym został przetłumaczony tekst. Przydatne, jeśli wybrałeś opcję automatycznego wykrywania języka, ponieważ to dane wyjściowe dadzą Ci wynik tego wykrywania.|

##  <a name="sample-definition"></a>Przykładowa definicja

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
    "defaultToLanguageCode": "fr",
    "suggestedFrom": "en",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "translatedText",
        "targetName": "translatedText"
      },
      {
        "name": "translatedFromLanguageCode",
        "targetName": "translatedFromLanguageCode"
      },
      {
        "name": "translatedToLanguageCode",
        "targetName": "translatedToLanguageCode"
      }
    ]
  }
```

##  <a name="sample-input"></a>Przykładowe dane wejściowe

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
        {
          "text": "We hold these truths to be self-evident, that all men are created equal."
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "text": "Estamos muy felices de estar con ustedes."
        }
    }
  ]
}
```


##  <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
        {
          "translatedText": "Nous tenons ces vérités pour évidentes, que tous les hommes sont créés égaux.",
          "translatedFromLanguageCode": "en",
          "translatedToLanguageCode": "fr"
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "translatedText": "Nous sommes très heureux d'être avec vous.",
          "translatedFromLanguageCode": "es",
          "translatedToLanguageCode": "fr"
        }
    }
  ]
}
```


## <a name="errors-and-warnings"></a>Błędy i ostrzeżenia
Jeśli podasz nieobsługinięty kod języka dla języka od lub do, zostanie wygenerowany błąd, a tekst nie zostanie przetłumaczony.
Jeśli tekst jest pusty, zostanie wysuń ostrzeżenie.
Jeśli tekst jest większy niż 50 000 znaków, tylko pierwsze 50 000 znaków zostanie przetłumaczone i zostanie wydane ostrzeżenie.

## <a name="see-also"></a>Zobacz też

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md)
