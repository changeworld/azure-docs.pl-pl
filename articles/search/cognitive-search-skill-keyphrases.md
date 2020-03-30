---
title: Kluczowe wyrażenie Ekstrakcja umiejętności poznawczych
titleSuffix: Azure Cognitive Search
description: Oblicza tekst bez struktury i dla każdego rekordu zwraca listę kluczowych fraz w potoku wzbogacania sztucznej inteligencji w usłudze Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ccdd25d82af2b4893260af18dac818816d9e4579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72791973"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Kluczowe wyrażenie Ekstrakcja umiejętności poznawczych

**Umiejętności Wyodrębnianie fraz kluczowych** ocenia tekst nieustrukturyzowany, a dla każdego rekordu zwraca listę kluczowych fraz. Ta umiejętność korzysta z modeli uczenia maszynowego dostarczonych przez [analizę tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) w usługach Cognitive Services.

Ta funkcja jest przydatna, jeśli trzeba szybko zidentyfikować główne punkty rozmowy w rekordzie. Na przykład, biorąc pod uwagę tekst wejściowy "Jedzenie było pyszne i nie było wspaniały personel", usługa zwraca "jedzenie" i "wspaniały personel".

> [!NOTE]
> W miarę rozszerzania zakresu poprzez zwiększanie częstotliwości przetwarzania, dodawanie większej liczby dokumentów lub dodawanie kolejnych algorytmów sztucznej inteligencji należy [dołączyć rozliczany zasób usług Cognitive Services.](cognitive-search-attach-cognitive-services.md) Opłaty naliczane podczas wywoływania interfejsów API w usługach Cognitive Services i wyodrębniania obrazu w ramach etapu pękania dokumentów w usłudze Azure Cognitive Search. Nie ma żadnych opłat za wyodrębnianie tekstu z dokumentów.
>
> Wykonanie wbudowanych umiejętności jest naliczane według istniejącej [ceny płatności zgodnie z rzeczywistymi oczekiwaniami.](https://azure.microsoft.com/pricing/details/cognitive-services/) Ceny wyodrębniania obrazów są opisane na [stronie cennika usługi Azure Cognitive Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Limity danych
Maksymalny rozmiar rekordu powinien wynosić 50 000 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)znaków mierzonych za pomocą pliku . Jeśli musisz podzielić dane przed wysłaniem ich do ekstraktora fraz kluczowych, rozważ użycie [umiejętności Dzielenie tekstu](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametry umiejętności

W nazwach parametrów jest rozróżniana wielkość liter.

| Dane wejściowe                | Opis |
|---------------------|-------------|
| domyślny Kod Języka | (Opcjonalnie) Kod języka do zastosowania do dokumentów, które nie określają języka jawnie.  Jeśli domyślny kod języka nie jest określony, angielski (en) będzie używany jako domyślny kod języka. <br/> Zobacz [Pełna lista obsługiwanych języków](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages). |
| maksymalnynalicznik Klawiatury   | (Opcjonalnie) Maksymalna liczba kluczowych fraz do wyprodukowania. |

## <a name="skill-inputs"></a>Wprowadzanie umiejętności

| Dane wejściowe     | Opis |
|--------------------|-------------|
| tekst | Tekst, który ma być analizowany.|
| languageCode  |  Ciąg wskazujący język rekordów. Jeśli ten parametr nie jest określony, domyślny kod języka będzie używany do analizowania rekordów. <br/>Zobacz [Pełna lista obsługiwanych języków](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)|

##  <a name="sample-definition"></a>Przykładowa definicja

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      },
      {
        "name": "languageCode",
        "source": "/document/languagecode" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
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
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
             "language": "en"
           }
      }
    ]
```


##  <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "keyPhrases": 
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
           }
      }
    ]
}
```


## <a name="errors-and-warnings"></a>Błędy i ostrzeżenia
Jeśli podasz nieobsługinięty kod języka, zostanie wygenerowany błąd i kluczowe frazy nie zostaną wyodrębnione.
Jeśli tekst jest pusty, zostanie wysuń ostrzeżenie.
Jeśli tekst jest większy niż 50 000 znaków, tylko pierwsze 50 000 znaków zostanie przeanalizowanych i zostanie wydane ostrzeżenie.

## <a name="see-also"></a>Zobacz też

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md)
