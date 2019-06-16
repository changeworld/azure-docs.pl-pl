---
title: Wyszukaj kategorie lokalnych firm interfejsu API wyszukiwania Bing | Dokumentacja firmy Microsoft
titleSuffix: Azure Cognitive Services
description: Aby dowiedzieć się, jak określić kategorie wyszukiwania dla punktu końcowego interfejsu API wyszukiwania Bing lokalnych firmach, należy użyć w tym artykule.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 61bd230ffdb79b31390e96179e5beb3646902142
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65796730"
---
# <a name="search-categories-for-the-bing-local-business-search-api"></a>Przeszukaj kategorie lokalnych firm interfejsu API wyszukiwania Bing

Lokalnych firm interfejsu API wyszukiwania Bing umożliwia wyszukiwanie jednostek lokalnych firm w różnych kategorii, z pierwszeństwa Zamknij wyniki lokalizację użytkownika. Wyszukiwanie można uwzględnić w wyszukiwaniach wraz z `localCircularView` i `localMapView` [parametry](specify-geographic-search.md).


## <a name="toplevel-categories"></a>Kategorie TopLevel 

Następujące typy definiują główne kategorie wyszukiwania.  Można określić więcej niż jednej kategorii przy użyciu rozdzielana przecinkami lista przypisane do `localCategories` parametru.  
- EatDrink 
- SeeDo 
- Rejestrator 
- HotelsAndMotels 
- BanksAndCreditUnions 
- Parkowania 
- Szpitalach 

## <a name="sub-categories"></a>Kategorie podrzędne
Podkategorie są przekazywane w taki sam sposób jak `localCategories`. Podkategorie są dokładniej określonych kategorii. Są podrzędne w tym sensie, że jeśli określisz w tym samym rozdzielana przecinkami lista kategorii C i jedna z jej podkategorii S, zostanie wyświetlony takie same wyniki tak, jakby określono C samodzielnie.

### <a name="eat-drink"></a>Jeść napoju 
|  |  |  |  |
| - | - | - | - |
| BreweriesAndBrewPubs | CocktailLounges | AfricanRestaurants |
| AmericanRestaurants | Bagels | BarbecueRestaurants |
| Taverns | SportsBars | Paski |
| BarsGrillsAndPubs | BuffetRestaurants| BelgianRestaurants | 
| BritishRestaurants | CafeRestaurants | CaribbeanRestaurants |
| ChineseRestaurants | CoffeeAndTea | Delicatessens | 
| DeliveryService | Diners | DiscountStores | 
| Donuts | FastFood | FrenchRestaurants | 
| FrozenYogurt | GermanRestaurants | Supermarkety | 
| GreekRestaurants | Grocers | HawaiianRestaurants | 
| HungarianRestaurants | IceCreamAndFrozenDesserts | IndianRestaurants | 
| ItalianRestaurants | JapaneseRestaurants | Soku | 
| KoreanRestaurants | LiquorStores | MexicanRestaurants |
| MiddleEasternRestaurants | pizza | PolishRestaurants | 
| PortugueseRestaurants | Pretzels | Restauracje | 
| RussianAndUkrainianRestaurants | Kanapki | SeafoodRestaurants | 
| SpanishRestaurants | SteakHouseRestaurants | SushiRestaurants | 
| TakeAway | ThaiRestaurants | TurkishRestaurants | 
| VegetarianAndVeganRestaurants | VietnameseRestaurants|  |
 
### <a name="see-do"></a>Zobacz czy 
|  |  |  |
| -- | -- | -- |
| AmusementParks | Atrakcje | Carnivals |
| Kasyna | LandmarksAndHistoricalSites | MiniatureGolfCourses |
| MovieTheaters | Muzeów | Parki |
| SightseeingTours | TouristInformation | Zoo |
 
### <a name="shop"></a>Rejestrator 
|  |  |  |
| -- | -- | -- |
| AntiqueStores | Księgarniach | CDAndRecordStores |
| ChildrensClothingStores | CigarAndTobaccoShops | ComicBookStores |
| DepartmentStores | DiscountStores | FleaMarketsAndBazaars |
| FurnitureStores | HomeImprovementStores | JewelryAndWatchesStores |
| KitchenwareStores | LiquorStores | MallsAndShoppingCenters |
| MensClothingStores | MusicStores | OutletStores |
| PetShops | PetSupplyStores | SchoolAndOfficeSupplyStores |
| ShoeStores | SportingGoodsStores | ToyAndGameStores |
| VitaminAndSupplementStores | WomensClothingStores |  |


## <a name="examples-of-local-categories-search"></a>Przykłady kategorii lokalnego wyszukiwania

Poniższe przykłady GET wyniki na podstawie położenia `localCategories` parametru:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=HotelsAndMotels`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=EatDrink`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Shop`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Hospitals`

Następujące zapytanie ogranicza liczbę wyników "szpitali" pierwsze trzy zwrócony z lokalnych firm interfejsu API wyszukiwania Bing:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localCategories=Hospitals&count=3&offset=0`

Poniższy przykład odpowiedź w formacie JSON zawiera trzy szpitali w obszarze Seattle większa:

```json
BingAPIs-TraceId: 68AFB51807C6485CAB8AAF20E232EFFF
BingAPIs-SessionId: F89E7B8539B34BF58AAF811485E83B20
X-MSEdge-ClientID: 1C44E64DBFAA6BCA1270EADDBE7D6A22
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 68AFB51807C6485CAB8AAF20E232EFFF Ref B: CO1EDGE0108 Ref C: 2018-10-22T18:52:28Z

{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": ""
   },
   "places": {
      "readLink": "https:\/\/www.bingapis.com\/api\/v7\/places\/search?q=",
      "totalEstimatedMatches": 0,
      "value": [
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.0",
            "name": "Overlake Hospital Medical Center",
            "url": "http:\/\/www.overlakehospital.org\/",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6204986572266,
               "longitude": -122.185829162598
            },
            "routablePoint": {
               "latitude": 47.6204986572266,
               "longitude": -122.185668945312
            },
            "address": {
               "streetAddress": "1035 116th Ave NE",
               "addressLocality": "Bellevue",
               "addressRegion": "WA",
               "postalCode": "98004",
               "addressCountry": "US",
               "neighborhood": "",
               "text": "1035 116th Ave NE, Bellevue, WA, 98004"
            },
            "telephone": "(425) 688-5000"
         },
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.1",
            "name": "Virginia Mason University Village Medical Center",
            "url": "https:\/\/virginiamason.org\/Seattle",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6095390319824,
               "longitude": -122.327941894531
            },
            "routablePoint": {
               "latitude": 47.6093978881836,
               "longitude": -122.328277587891
            },
            "address": {
               "streetAddress": "1100 9th Ave",
               "addressLocality": "Seattle",
               "addressRegion": "WA",
               "postalCode": "98101",
               "addressCountry": "US",
               "neighborhood": "University District",
               "text": "1100 9th Ave, Seattle, WA, 98101"
            },
            "telephone": "(206) 223-6600"
         },
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.2",
            "name": "Seattle Children’s Hospital",
            "url": "http:\/\/www.seattlechildrens.org\/",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6625061035156,
               "longitude": -122.283760070801
            },
            "routablePoint": {
               "latitude": 47.6631507873535,
               "longitude": -122.284614562988
            },
            "address": {
               "streetAddress": "4800 Sand Point Way NE",
               "addressLocality": "Seattle",
               "addressRegion": "WA",
               "postalCode": "98105",
               "addressCountry": "US",
               "neighborhood": "Laurelhurst",
               "text": "4800 Sand Point Way NE, Seattle, WA, 98105"
            },
            "telephone": "(206) 987-2000"
         }
      ],
      "searchAction": {

      }
   }
}
```

## <a name="next-steps"></a>Kolejne kroki
- [Wyszukiwanie geograficzne granice](specify-geographic-search.md)
- [Zapytania i odpowiedzi](local-search-query-response.md)
- [Przewodnik Szybki Start wC#](quickstarts/local-quickstart.md)
