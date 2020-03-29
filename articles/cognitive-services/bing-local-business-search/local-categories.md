---
title: Kategorie wyszukiwania interfejsu API wyszukiwania lokalnego firmy Bing
titleSuffix: Azure Cognitive Services
description: Ten artykuł służy do określania kategorii wyszukiwania dla punktu końcowego interfejsu API wyszukiwania lokalnego firmy Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 56b94d66eb0929d2fd0ca74a1a631d229330adfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "69906393"
---
# <a name="search-categories-for-the-bing-local-business-search-api"></a>Kategorie wyszukiwania interfejsu API wyszukiwania lokalnego firmy Bing

Interfejs API wyszukiwania lokalnej firmy Bing umożliwia wyszukiwanie lokalnych jednostek biznesowych w różnych kategoriach, przy czym priorytet nadano wynikom zamknięcia lokalizacji użytkownika. Wyszukiwania te można uwzględnić w `localCircularView` `localMapView` wyszukiwaniach wraz z parametrami i [parametrami](specify-geographic-search.md).


## <a name="toplevel-categories"></a>Kategorie Najwyższej klasy 

Następujące typy definiują główne kategorie wyszukiwania.  Więcej niż jedną kategorię można określić za pomocą listy rozdzielanych przecinkami przypisanej do parametru. `localCategories`  
- EatDrink (Niem. 
- ZobaczDo 
- Sklep 
- HoteleAndMotels 
- BankiAndCreditUnions 
- Parking 
- Szpitale 

## <a name="sub-categories"></a>Podkategorie
Podkategorie są przekazywane w `localCategories`taki sam sposób jak . Podkategorie są bardziej szczegółowe kategorie. Są one podporządkowane w tym sensie, że jeśli określisz kategorię C i jedną z jej podkategorii S na tej samej liście rozdzielanych przecinkami, otrzymasz takie same wyniki, jak w przypadku określenia tylko C.

### <a name="eat-drink"></a>Jedz drinka 
|  |  |  |  |
| - | - | - | - |
| BrowaryAndBrewPubs | CocktailLounges (Koktajle) | Afrykańskierestauraty |
| AmerykańskieRestauraty | Bajgle | Restauraty do grillowania |
| Tawern | Bary sportowe | Bary |
| BarsGrillsAndPubs | BuffetRestaurants| BelgijskiRestauraty | 
| BrytyjskieRestauraty | CafeRestaurants | KaraibyRestaurants |
| ChińskieRestauraty | KawaAndTea | Delicatessens | 
| Usługa doręczenia | Diner amerykański | Sklepy ze zniżkami | 
| Pączki | Fastfood | FrancuskieRestauraty | 
| MrożoneJgurt | NiemieckieRestauraty | Artykuły spożywcze | 
| GreckiRestauratory | Sklep spożywczy | HawajskieRestauranty | 
| WęgierskiRestauraty | LodyAndFrozenDesserts | Indyjskirestauratory | 
| WłoskiRestauraty | Japońskirestauraty | Soki | 
| Koreańskirestauratory | LiquorStores ( LiquorStores ) | MeksykańskieRestauranty |
| MiddleEasternRestaurants | Pizza | PolskieRestauraty | 
| PortugalskiRestauranty | Precle | Restauracje | 
| RussianAndUkrainianRestaurants (Rosjanie III- ukraińskierestauraty) | Kanapki | Owoce morzaRestauraty | 
| HiszpańskiRestauratory | SteakHouseRestauraty | SushiRestaurants | 
| Fast food | TajskiRestauraty | Tureckirestauranty | 
| Wegetariańskie iveganrestauranty | WietnamskiRestauranty|  |
 
### <a name="see-do"></a>Zobacz Do 
|  |  |  |
| -- | -- | -- |
| Parki rozrywki | Atrakcji | Karnawał |
| Kasyna | Punkty orientacyjneAndHistoricalSites | Miniaturowe Courses |
| MovieTheaters (Teaters) | Muzea | Parki |
| Wycieczki | Informacje turystyczne | Ogrody zoologiczne |
 
### <a name="shop"></a>Sklep 
|  |  |  |
| -- | -- | -- |
| Sklepy z antykami | Księgarnie | CdAndRecordSklepy |
| DzieciSklei | CygaraAndTobaccoSklepów | Sklepy z komiksami |
| Sklepy departmentstores | Sklepy ze zniżkami | FleaMarketsAndBazaars |
| MebleSklepy | Strona głównaImprovementStores | BiżuteriaIWatchesSsedy |
| Naczynia kuchenneSklepy | LiquorStores ( LiquorStores ) | Centra handloweIcentra handlowe |
| Odzież męskaSklepy | Sklepy muzyczne | OutletSklepy |
| Sklepy pet | PetSupplySklepy | SchoolAndOfficeSupplyStores |
| Sklepy obuwnicze | SportingGoodsSklepy | Sklepy z toyandgamestores |
| VitaminAndSupplementSkleje | DamskieSklepy |  |


## <a name="examples-of-local-categories-search"></a>Przykłady wyszukiwania kategorii lokalnych

Następujące przykłady get wyniki `localCategories` zgodnie z parametrem:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=HotelsAndMotels`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=EatDrink`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Shop`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Hospitals`

Następujące zapytanie ogranicza liczbę wyników "szpitalnych" do pierwszych trzech zwróconych z interfejsu API wyszukiwania lokalnego firmy Bing:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localCategories=Hospitals&count=3&offset=0`

Poniższa przykładowa odpowiedź JSON obejmuje trzy szpitale w większym obszarze Seattle:

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

## <a name="next-steps"></a>Następne kroki
- [Granice wyszukiwania geograficznego](specify-geographic-search.md)
- [Zapytanie i odpowiedź](local-search-query-response.md)
- [Szybki start w języku C #](quickstarts/local-quickstart.md)
