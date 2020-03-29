---
title: Używanie rozpoznawania jednostek w interfejsie API analizy tekstu
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak zidentyfikować i rozróżnić tożsamość jednostki znalezionej w tekście za pomocą interfejsu API REST analizy tekstu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/10/2020
ms.author: aahi
ms.openlocfilehash: 243086ddaae47eba20eea6877fe6d7f8f9889290
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79203495"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Jak używać rozpoznawania nazwanych jednostek w analizie tekstu

Interfejs API analizy tekstu umożliwia sporządzanie tekstu bez struktury i zwraca listę nieoprawnionych encji z łączami do większej ilości informacji w sieci Web. Interfejs API obsługuje zarówno rozpoznawanie nazwanych jednostek (NER), jak i łączenie jednostek.

### <a name="entity-linking"></a>Łączenie jednostek

Łączenie jednostek to możliwość identyfikowania i rozróżniania tożsamości jednostki znajdującej się w tekście (na przykład określania, czy wystąpienie słowa `Mars` odnosi się do planety, czy do rzymskiego boga wojny). Proces ten wymaga obecności bazy wiedzy w odpowiednim języku, aby połączyć rozpoznane jednostki w tekście. Entity Linking wykorzystuje [Wikipedię](https://www.wikipedia.org/) jako tę bazę wiedzy.


### <a name="named-entity-recognition-ner"></a>Rozpoznawanie nazwanych jednostek (NER)

Nazwane rozpoznawanie jednostek (NER) to możliwość identyfikowania różnych jednostek w tekście i kategoryzowania ich na wstępnie zdefiniowane klasy lub typy, takie jak: osoba, lokalizacja, zdarzenie, produkt i organizacja.  

Począwszy od wersji 3 ta funkcja interfejsu API analizy tekstu może również identyfikować typy informacji osobistych i poufnych, takie jak: numer telefonu, numer ubezpieczenia społecznego, adres e-mail i numer konta bankowego.  Identyfikowanie tych jednostek może pomóc w klasyfikowaniu poufnych dokumentów i redagowaniu informacji osobistych.

## <a name="named-entity-recognition-versions-and-features"></a>Wersje i funkcje rozpoznawania nazwanych jednostek

Interfejs API analizy tekstu oferuje dwie wersje rozpoznawania nazwanych jednostek — w wersji 2 i 3. Wersja 3 (publiczna wersja zapoznawcza) zapewnia większą szczegółowość w jednostkach, które można wykryć i skategoryzować.

| Funkcja                                                         | NER v2 | NER v3 |
|-----------------------------------------------------------------|--------|--------|
| Metody dla pojedynczych i wsadowych żądań                          | X      | X      |
| Podstawowe rozpoznawanie jednostek w kilku kategoriach              | X      | X      |
| Rozszerzona klasyfikacja dla uznanych jednostek                 |        | X      |
| Oddzielne punkty końcowe do wysyłania łączenia jednostek i żądań NER. |        | X      |
| Przechowywanie wersji modelu                                                |        | X      |

Informacje można znaleźć w [pomocy technicznej języka.](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition)


#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

### <a name="entity-types"></a>Typy jednostek

Nazwane rozpoznawanie jednostek w wersji 3 zapewnia rozszerzone wykrywanie wielu typów. Obecnie NER v3 może rozpoznawać następujące kategorie jednostek:

* Ogólne
* Dane osobowe 

Aby uzyskać szczegółową listę obsługiwanych jednostek i języków, zobacz [ner v3 obsługiwane typy jednostek](../named-entity-types.md) artykułu.

### <a name="request-endpoints"></a>Żądanie punktów końcowych

Nazwane rozpoznawanie jednostek w wersji 3 używa oddzielnych punktów końcowych dla żądań łączenia NER i encji. Użyj poniższego formatu adresu URL na podstawie prośby:

Ner
* Podmioty ogólne -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Dane osobowe -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Łączenie jednostek
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>Przechowywanie wersji modelu

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

### <a name="entity-types"></a>Typy jednostek

> [!NOTE]
> Wersja 2 o nazwie Entity Recognition(NER) obsługuje tylko następujące jednostki. Ner v3 jest w publicznej wersji zapoznawczej i znacznie rozszerza liczbę i głębokość jednostek rozpoznawanych w tekście.   

| Typ  | SubType | Przykład |
|:-----------   |:------------- |:---------|
| Person (Osoba)        | Nie dotyczy\*         | "Jeff", "Bill Gates"     |
| Lokalizacja      | Nie dotyczy\*         | "Redmond, Waszyngton", "Paryż"  |
| Organizacja  | Nie dotyczy\*         | "Microsoft"   |
| Liczba      | Liczba        | "6", "szóstka"     |
| Liczba      | Procentowe    | "50%", "pięćdziesiąt procent"|
| Liczba      | Porządkowych       | "2", "drugi"     |
| Liczba      | Wiek           | "90 dni", "30 lat"    |
| Liczba      | Waluta      | "$10.99"     |
| Liczba      | Wymiar     | "10 mil", "40 cm"     |
| Liczba      | Temperatura   | "32 stopnie"    |
| DateTime      | Nie dotyczy\*         | "18:30 4 lutego 2012"      |
| DateTime      | Data          | "2 maja 2017", "05/02/2017"   |
| DateTime      | Time          | "8 rano", "8:00"  |
| DateTime      | DataRange     | "Od 2 do 5 maja"    |
| DateTime      | Układ czasu     | "Od 18:00 do 19:00"     |
| DateTime      | Czas trwania      | "1 minuta i 45 sekund"   |
| DateTime      | Set           | "W każdy wtorek"     |
| Adres URL           | Nie dotyczy\*         | "https:\//www.bing.com"    |
| Adres e-mail         | Nie dotyczy\*         | "support@contoso.com" |
| Numer telefonu usa  | Nie dotyczy\*         | (tylko amerykańskie numery telefonów) "(312) 555-0176" |
| Adres IP    | Nie dotyczy\*         | "10.0.0.100" |

\*W zależności od danych wejściowych i wyodrębnionych jednostek niektóre jednostki mogą pominąć `SubType`plik .  Wszystkie wymienione typy obsługiwanych encji są dostępne tylko w językach angielskim, chińskim uproszczonym, francuskim, niemieckim i hiszpańskim.

### <a name="request-endpoints"></a>Żądanie punktów końcowych

Nazwane rozpoznawanie encji w wersji 2 używa pojedynczego punktu końcowego dla żądań łączenia NER i encji:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

## <a name="sending-a-rest-api-request"></a>Wysyłanie żądania interfejsu API REST

### <a name="preparation"></a>Przygotowywanie

Dokumenty JSON muszą być w tym formacie: identyfikator, tekst, język.

Każdy dokument musi mieć mniej niż 5120 znaków, a na kolekcję można mieć maksymalnie 1000 elementów.Each document must be under 5,120 characters, and you can have up to 1,000 items (IDs) per collection. Kolekcja jest przesyłana w treści żądania.

### <a name="structure-the-request"></a>Określenie struktury żądania

Utwórz żądanie POST. [Listonosz](text-analytics-how-to-call-api.md) lub **konsoli testowania interfejsu API** w poniższych łączach, aby szybko struktury i wysłać jeden. 

> [!NOTE]
> Klucz i punkt końcowy zasobu analizy tekstu można znaleźć w witrynie Azure Portal. Będą one znajdować się na stronie **Szybki start** zasobu, w obszarze **Zarządzanie zasobami**. 

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

[Odwołanie do rozpoznawania nazwanych jednostek w wersji 3](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)

Wersja 3 używa oddzielnych punktów końcowych dla żądań łączenia NER i jednostek. Użyj poniższego formatu adresu URL na podstawie prośby:

Ner
* Podmioty ogólne -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Podmioty przetwarzające dane osobowe -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Łączenie jednostek
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

[Odwołanie do rozpoznawania nazwanych jednostek (NER) w wersji 2](https://eastus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

Wersja 2 używa następującego punktu końcowego dla łączenia jednostek i żądań NER: 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

Ustaw nagłówek żądania, aby uwzględnić klucz interfejsu API analizy tekstu. W treści żądania podaj przygotowane dokumenty JSON.

### <a name="example-ner-request"></a>Przykładowe żądanie NER 

Poniżej przedstawiono przykład zawartości, którą można wysłać do interfejsu API. Format żądania jest taki sam dla obu wersji interfejsu API.

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "I had a wonderful trip to Seattle last week."
    }
  ]
}
```

## <a name="post-the-request"></a>Wysłanie żądania

Analiza jest wykonywana po odebraniu żądania. Zobacz sekcję [limity danych](../overview.md#data-limits) w przeglądzie, aby uzyskać informacje na temat rozmiaru i liczby żądań, które można wysyłać na minutę i sekundę.

Interfejs API analizy tekstu jest bezstanowy. Żadne dane nie są przechowywane na twoim koncie, a wyniki są zwracane natychmiast w odpowiedzi.

## <a name="view-results"></a>Wyświetlanie wyników

Wszystkie żądania POST zwracają sformatowaną odpowiedź JSON z identyfikatorami i wykrytymi właściwościami jednostki.

Dane wyjściowe są zwracane natychmiast. Wyniki można przesłać strumieniowo do aplikacji, która akceptuje kod JSON, lub zapisać do pliku w systemie lokalnym, a następnie zaimportować do aplikacji, która umożliwia sortowanie i wyszukiwanie danych oraz manipulowanie nimi. Ze względu na obsługę wielojęzyczną i emoji odpowiedź może zawierać przesunięcia tekstu. Zobacz, [jak przetwarzać przesunięcia tekstu, aby](../concepts/text-offsets.md) uzyskać więcej informacji.

#### <a name="version-30-preview"></a>[Wersja 3.0-preview)](#tab/version-3)

### <a name="example-v3-responses"></a>Przykładowe odpowiedzi w wersji 3

Wersja 3 zawiera oddzielne punkty końcowe dla łączenia NER i jednostek. Odpowiedzi dla obu operacji są poniżej. 

#### <a name="example-ner-response"></a>Przykładowa odpowiedź NER

```json
{
    "documents": [{
    "id": "1",
    "entities": [{
        "text": "Seattle",
        "type": "Location",
        "offset": 26,
        "length": 7,
        "score": 0.80624294281005859
    }, {
        "text": "last week",
        "type": "DateTime",
        "subtype": "DateRange",
        "offset": 34,
        "length": 9,
        "score": 0.8
    }]
    }],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

#### <a name="example-entity-linking-response"></a>Przykładowa odpowiedź łącząca encję

```json
{
  "documents": [{
    "id": "1",
    "entities": [{
      "name": "Seattle",
      "matches": [{
        "text": "Seattle",
        "offset": 26,
        "length": 7,
        "score": 0.15046201222847677
      }],
      "language": "en",
      "id": "Seattle",
      "url": "https://en.wikipedia.org/wiki/Seattle",
      "dataSource": "Wikipedia"
    }]
  }],
  "errors": [],
  "modelVersion": "2019-10-01"
}
```

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

### <a name="example-ner-v2-response"></a>Przykładowa odpowiedź NER v2
```json
{
  "documents": [{
    "id": "1",
    "entities": [{
      "name": "Seattle",
      "matches": [{
        "wikipediaScore": 0.15046201222847677,
        "entityTypeScore": 0.80624294281005859,
        "text": "Seattle",
        "offset": 26,
        "length": 7
      }],
      "wikipediaLanguage": "en",
      "wikipediaId": "Seattle",
      "wikipediaUrl": "https://en.wikipedia.org/wiki/Seattle",
      "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
      "type": "Location"
    }, {
      "name": "last week",
      "matches": [{
        "entityTypeScore": 0.8,
        "text": "last week",
        "offset": 34,
        "length": 9
      }],
      "type": "DateTime",
      "subType": "DateRange"
    }]
  }],
  "errors": []
}
```

---

## <a name="summary"></a>Podsumowanie

W tym artykule poznaliście pojęcia i przepływ pracy dla łączenia jednostek przy użyciu analizy tekstu w usługach Cognitive Services. Podsumowanie:

* Nazwane rozpoznawanie encji jest dostępne dla wybranych języków w dwóch wersjach.
* Dokumenty JSON w treści żądania zawierają identyfikator, tekst i kod języka.
* Żądania POST są wysyłane do jednego lub więcej punktów końcowych przy użyciu spersonalizowanego [klucza dostępu i punktu końcowego,](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) który jest prawidłowy dla subskrypcji.
* Dane wyjściowe odpowiedzi, które składają się z połączonych jednostek (w tym wyników zaufania, przesunięcia i łączy internetowych, dla każdego identyfikatora dokumentu) mogą być używane w dowolnej aplikacji

## <a name="next-steps"></a>Następne kroki

* [Analiza tekstu — omówienie](../overview.md)
* [Korzystanie z biblioteki klienta analizy tekstu](../quickstarts/text-analytics-sdk.md)
* [Co nowego](../whats-new.md)
