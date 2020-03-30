---
title: Umiejętność poznawcza wykrywania pii (podgląd)
titleSuffix: Azure Cognitive Search
description: Wyodrębnij i zamaskuj informacje umożliwiające identyfikację użytkownika z tekstu w potoku wzbogacania w usłudze Azure Cognitive Search. Ta umiejętność jest obecnie w publicznej wersji zapoznawczej.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: f21200bc6f5b25f3330f5bb87c0843caa5a84e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298880"
---
#    <a name="pii-detection-cognitive-skill"></a>Umiejętność poznawcza wykrywania pii

> [!IMPORTANT] 
> Ta umiejętność jest obecnie w publicznej wersji zapoznawczej. Funkcja w wersji zapoznawczej jest dostarczana bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Obecnie nie ma obsługi portalu ani SDK .NET.

Umiejętność **wykrywania informacji** umożliwiających identyfikację użytkownika wyodrębnia informacje umożliwiające identyfikację użytkownika z tekstu wejściowego i umożliwia maskowanie ich z tego tekstu na różne sposoby. Ta umiejętność korzysta z modeli uczenia maszynowego dostarczonych przez [analizę tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) w usługach Cognitive Services.

> [!NOTE]
> W miarę rozszerzania zakresu poprzez zwiększanie częstotliwości przetwarzania, dodawanie większej liczby dokumentów lub dodawanie kolejnych algorytmów sztucznej inteligencji należy [dołączyć rozliczany zasób usług Cognitive Services.](cognitive-search-attach-cognitive-services.md) Opłaty naliczane podczas wywoływania interfejsów API w usługach Cognitive Services i wyodrębniania obrazu w ramach etapu pękania dokumentów w usłudze Azure Cognitive Search. Nie ma żadnych opłat za wyodrębnianie tekstu z dokumentów.
>
> Wykonanie wbudowanych umiejętności jest naliczane według istniejącej [ceny płatności zgodnie z rzeczywistymi oczekiwaniami.](https://azure.microsoft.com/pricing/details/cognitive-services/) Ceny wyodrębniania obrazów są opisane na [stronie cennika usługi Azure Cognitive Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.skills.text.PIIDetectionSkill

## <a name="data-limits"></a>Limity danych
Maksymalny rozmiar rekordu powinien wynosić 50 000 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)znaków mierzonych za pomocą pliku . Jeśli musisz podzielić dane przed wysłaniem ich do umiejętności, rozważ użycie [umiejętności Dzielenie tekstu.](cognitive-search-skill-textsplit.md)

## <a name="skill-parameters"></a>Parametry umiejętności

W parametrach rozróżniana jest wielkość liter i wszystkie są opcjonalne.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| domyślny Kod Języka |    Kod języka tekstu wejściowego. Na razie `en` tylko jest obsługiwany. |
| minimumPrecision (minimumPrecision) | Wartość między 0,0 a 1,0. Jeśli wynik zaufania (w `piiEntities` danych wyjściowych) `minimumPrecision` jest niższa niż ustawiona wartość, jednostka nie jest zwracana lub maskowana. Wartość domyślna to 0.0. |
| maskowanieMode | Parametr, który zapewnia różne sposoby maskowania wykrytych informacji umożliwiających identyfikację użytkownika w tekście wejściowym. Obsługiwane są następujące opcje: <ul><li>`none`(domyślnie): Oznacza to, że nie `maskedText` zostanie wykonane maskowanie i dane wyjściowe nie zostaną zwrócone. </li><li> `redact`: Ta opcja spowoduje usunięcie wykrytych jednostek z tekstu wejściowego i nie zastąpi ich niczym. Należy zauważyć, że w tym `piiEntities` przypadku przesunięcie w danych wyjściowych będzie w stosunku do oryginalnego tekstu, a nie zamaskowany tekst. </li><li> `replace`: Ta opcja zastąpi wykryte jednostki znakiem `maskingCharacter` podanym w parametrze.  Znak zostanie powtórzony do długości wykrytego elementu, tak aby odsunięcia poprawnie odpowiadały `maskedText`zarówno tekstowi wejściemu, jak i wyjściu .</li></ul> |
| maskowanieCharakter | Znak, który będzie używany do maskowania `maskingMode` tekstu, `replace`jeśli parametr jest ustawiony na . Obsługiwane są następujące opcje: `*` (domyślnie), `#`, `X`. Ten parametr może `null` `maskingMode` być tylko `replace`wtedy, gdy nie jest ustawiony na . |


## <a name="skill-inputs"></a>Wprowadzanie umiejętności

| Nazwa wejściowa      | Opis                   |
|---------------|-------------------------------|
| languageCode    | Element opcjonalny. Wartość domyślna to `en`.  |
| tekst          | Tekst do analizy.          |

## <a name="skill-outputs"></a>Wyniki umiejętności

| Nazwa wyjściowa      | Opis                   |
|---------------|-------------------------------|
| PiiEntities (władarze) | Tablica typów złożonych zawierająca następujące pola: <ul><li>tekst (rzeczywiste dane umożliwiające identyfikację z wyodrębnienia)</li> <li>type</li><li>Podtypu</li><li>wynik (wyższa wartość oznacza, że jest bardziej prawdopodobne, że jest to rzeczywisty podmiot)</li><li>offset (do tekstu wejściowego)</li><li>length</li></ul> </br> [Możliwe typy i podtypy można znaleźć tutaj.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/named-entity-types?tabs=personal) |
| maskedText | Jeśli `maskingMode` jest ustawiona na `none`wartość inną niż , to dane wyjściowe będzie wynikiem ciągu maskowania wykonywanego na tekście wejściowym, zgodnie z opisem zaznaczonego `maskingMode`.  Jeśli `maskingMode` jest `none`ustawiona na , to dane wyjściowe nie będą obecne. |

##    <a name="sample-definition"></a>Przykładowa definicja

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.PIIDetectionSkill",
    "defaultLanguageCode": "en",
    "minimumPrecision": 0.5,
    "maskingMode": "replace",
    "maskingCharacter": "*",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "piiEntities"
      },
      {
        "name": "maskedText"
      }
    ]
  }
```
##    <a name="sample-input"></a>Przykładowe dane wejściowe

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Microsoft employee with ssn 859-98-0987 is using our awesome API's."
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "piiEntities":[ 
           { 
              "text":"859-98-0987",
              "type":"U.S. Social Security Number (SSN)",
              "subtype":"",
              "offset":28,
              "length":11,
              "score":0.65
           }
        ],
        "maskedText": "Microsoft employee with ssn *********** is using our awesome API's."
      }
    }
  ]
}
```

Należy zauważyć, że przesunięcia zwrócone dla jednostek w danych wyjściowych tej umiejętności są bezpośrednio zwracane z [interfejsu API analizy tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview), co oznacza, że jeśli używasz ich do indeksowania do oryginalnego ciągu, należy użyć [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) klasy w .NET w celu wyodrębnienia poprawnej zawartości.  [Więcej szczegółów można znaleźć tutaj.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/concepts/text-offsets)

## <a name="error-and-warning-cases"></a>Przypadki błędów i ostrzeżeń
Jeśli kod języka dokumentu nie jest obsługiwane, ostrzeżenie jest zwracany i nie jednostek są wyodrębniane.
Jeśli tekst jest pusty, zostanie wysuń ostrzeżenie.
Jeśli tekst jest większy niż 50 000 znaków, tylko pierwsze 50 000 znaków zostanie przeanalizowanych i zostanie wydane ostrzeżenie.

Jeśli umiejętność zwraca ostrzeżenie, `maskedText` dane wyjściowe mogą być puste.  Oznacza to, że jeśli oczekujesz, że dane wyjściowe istnieją do wprowadzenia do późniejszych umiejętności, nie będzie działać zgodnie z oczekiwaniami. Należy o tym pamiętać podczas pisania definicji zestaw umiejętności.

## <a name="see-also"></a>Zobacz też

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md)
