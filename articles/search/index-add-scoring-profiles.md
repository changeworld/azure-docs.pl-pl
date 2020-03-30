---
title: Zwiększanie rangi wyszukiwania za pomocą profili oceniania
titleSuffix: Azure Cognitive Search
description: Zwiększ wyniki rankingu wyszukiwania dla wyników usługi Azure Cognitive Search, dodając profile oceniania.
manager: nitinme
author: Brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/28/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 516637b812afece1966006ce6d894dd1e32e6293
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245462"
---
# <a name="add-scoring-profiles-to-an-azure-cognitive-search-index"></a>Dodawanie profilów oceniania do indeksu usługi Azure Cognitive Search

  Punktacja odnosi się do obliczeń *wyniku wyszukiwania* dla każdego elementu zwróconego w wynikach wyszukiwania. Wynik jest wskaźnikiem istotności elementu w kontekście bieżącej operacji wyszukiwania. Im wyższy wynik, tym bardziej istotny element. W wynikach wyszukiwania elementy są uporządkowane od najwyższego do najniższego, na podstawie wyników wyszukiwania obliczonych dla każdego elementu.  

 Usługa Azure Cognitive Search używa domyślnego oceniania do obliczania początkowego wyniku, ale można dostosować obliczenia za pomocą *profilu oceniania*. Profile oceniania zapewniają większą kontrolę nad rankingiem elementów w wynikach wyszukiwania. Możesz na przykład zwiększyć liczbę produktów na podstawie ich potencjału przychodów, promować nowsze przedmioty lub promować przedmioty, które były zbyt długie w magazynie.  

 Profil oceniania jest częścią definicji indeksu, składającej się z pól ważonych, funkcji i parametrów.  

 Aby dać ci wyobrażenie o tym, jak wygląda profil oceniania, w poniższym przykładzie pokazano prosty profil o nazwie "geo". Ten zwiększa elementy, które mają wyszukiwany termin w polu **hotelName.** Używa również `distance` funkcji do faworyzowania elementów, które znajdują się w odległości dziesięciu kilometrów od bieżącej lokalizacji. Jeśli ktoś wyszuka termin "karcza", a "karcza" stanie się częścią nazwy hotelu, dokumenty, które zawierają hotele z "karzemem" w promieniu 10 KM od bieżącej lokalizacji, pojawią się wyżej w wynikach wyszukiwania.  


```json
"scoringProfiles": [
  {  
    "name":"geo",
    "text": {  
      "weights": {  
        "hotelName": 5
      }                              
    },
    "functions": [
      {  
        "type": "distance",
        "boost": 5,
        "fieldName": "location",
        "interpolation": "logarithmic",
        "distance": {
          "referencePointParameter": "currentLocation",
          "boostingDistance": 10
        }                        
      }                                      
    ]                     
  }            
]
```  


 Aby użyć tego profilu oceniania, kwerenda jest sformułowana w celu określenia profilu w ciągu zapytania. W poniższej kwerendzie zwróć `scoringProfile=geo` uwagę na parametr kwerendy w żądaniu.  

```  
GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2019-05-06 
```  

 Ta kwerenda wyszukuje termin "karcze" i przechodzi w bieżącej lokalizacji. Należy zauważyć, że ta kwerenda zawiera inne parametry, takie jak `scoringParameter`. Parametry kwerendy są opisane w [&#40;&#41;interfejsu API usługi Azure Cognitive Search REST. ](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)  

 Kliknij [przykład,](#bkmk_ex) aby przejrzeć bardziej szczegółowy przykład profilu oceniania.  

## <a name="what-is-default-scoring"></a>Co to jest domyślny punktacji?  
 Punktacja oblicza wynik wyszukiwania dla każdego elementu w zestawie wyników uporządkowanych. Każdemu elementowi w zestawie wyników wyszukiwania jest przypisywany wynik wyszukiwania, a następnie klasyfikowany od najwyższego do najniższego. Elementy z wyższymi wynikami są zwracane do aplikacji. Domyślnie zwracanych jest 50 pierwszych, ale `$top` można użyć parametru, aby zwrócić mniejszą lub większą liczbę elementów (do 1000 w pojedynczej odpowiedzi).  

Wynik wyszukiwania jest obliczany na podstawie właściwości statystycznych danych i zapytania. Usługa Azure Cognitive Search znajduje dokumenty, które zawierają wyszukiwane terminy w ciągu zapytania (niektóre lub wszystkie, w zależności `searchMode`od ), faworyzowanie dokumentów zawierających wiele wystąpień wyszukiwanego terminu. Wynik wyszukiwania wzrasta jeszcze wyżej, jeśli termin ten jest rzadki w indeksie danych, ale często w dokumencie. Podstawą tego podejścia do trafności obliczeniowej jest znany jako [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) lub termin częstotliwość odwrotność częstotliwości częstotliwości częstotliwości.  

 Zakładając, że nie ma niestandardowego sortowania, wyniki są następnie klasyfikowane według wyniku wyszukiwania, zanim zostaną zwrócone do aplikacji wywołującej. Jeśli nie określono $top, zwracanych jest 50 elementów o najwyższym wyniku wyszukiwania.  

 Wartości wyników wyszukiwania można powtarzać w całym zestawie wyników. Na przykład możesz mieć 10 przedmiotów z wynikiem 1,2, 20 pozycji z wynikiem 1,0 i 20 pozycji z wynikiem 0,5. Gdy wiele trafień ma ten sam wynik wyszukiwania, kolejność tych samych elementów zdobytych nie jest zdefiniowana i nie jest stabilna. Uruchom kwerendę ponownie, a może być widoczna pozycja zmiany elementów. Biorąc pod uwagę dwa przedmioty o identycznym wyniku, nie ma gwarancji, który z nich pojawia się jako pierwszy.  

## <a name="when-to-use-custom-scoring"></a>Kiedy używać niestandardowego oceniania  
 Należy utworzyć jeden lub więcej profilów punktacji, gdy domyślne zachowanie rankingu nie idzie wystarczająco daleko w realizacji celów biznesowych. Na przykład można zdecydować, że trafność wyszukiwania powinna faworyzować nowo dodane elementy. Podobnie może być pole zawierające marżę zysku lub inne pole wskazujące potencjał przychodów. Zwiększenie liczby trafień, które przynoszą korzyści Twojej firmie, może być ważnym czynnikiem przy podejmowaniu decyzji o wykorzystaniu profili punktacji.  

 Kolejność oparta na trafności jest również implementowana za pośrednictwem profili oceniania. Rozważ strony wyników wyszukiwania używane w przeszłości, które umożliwiają sortowanie według ceny, daty, oceny lub trafności. W usłudze Azure Cognitive Search profile oceniania napędzają opcję "trafność". Definicja trafności jest kontrolowana przez Ciebie, opiera się na celach biznesowych i rodzaju środowiska wyszukiwania, które chcesz zapewnić.  

##  <a name="example"></a><a name="bkmk_ex"></a>Przykład  
 Jak wspomniano wcześniej, dostosowane punktacji jest implementowana za pośrednictwem jednego lub więcej profilów oceniania zdefiniowanych w schemacie indeksu.  

 W tym przykładzie przedstawiono schemat indeksu`boostGenre`z `newAndHighlyRated`dwoma profilami oceniania ( , ). Każda kwerenda względem tego indeksu, który zawiera albo profil jako parametr kwerendy użyje profilu, aby uzyskać zestaw wyników.  

```json
{  
  "name": "musicstoreindex",  
  "fields": [  
    { "name": "key", "type": "Edm.String", "key": true },  
    { "name": "albumTitle", "type": "Edm.String" },  
    { "name": "albumUrl", "type": "Edm.String", "filterable": false },  
    { "name": "genre", "type": "Edm.String" },  
    { "name": "genreDescription", "type": "Edm.String", "filterable": false },  
    { "name": "artistName", "type": "Edm.String" },  
    { "name": "orderableOnline", "type": "Edm.Boolean" },  
    { "name": "rating", "type": "Edm.Int32" },  
    { "name": "tags", "type": "Collection(Edm.String)" },  
    { "name": "price", "type": "Edm.Double", "filterable": false },  
    { "name": "margin", "type": "Edm.Int32", "retrievable": false },  
    { "name": "inventory", "type": "Edm.Int32" },  
    { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }  
  ],  
  "scoringProfiles": [  
    {  
      "name": "boostGenre",  
      "text": {  
        "weights": {  
          "albumTitle": 1.5,  
          "genre": 5,  
          "artistName": 2  
        }  
      }  
    },  
    {  
      "name": "newAndHighlyRated",  
      "functions": [  
        {  
          "type": "freshness",  
          "fieldName": "lastUpdated",  
          "boost": 10,  
          "interpolation": "quadratic",  
          "freshness": {  
            "boostingDuration": "P365D"  
          }  
        },  
        {
          "type": "magnitude",  
          "fieldName": "rating",  
          "boost": 10,  
          "interpolation": "linear",  
          "magnitude": {  
            "boostingRangeStart": 1,  
            "boostingRangeEnd": 5,  
            "constantBoostBeyondRange": false  
          }  
        }  
      ]  
    }  
  ],  
  "suggesters": [  
    {  
      "name": "sg",  
      "searchMode": "analyzingInfixMatching",  
      "sourceFields": [ "albumTitle", "artistName" ]  
    }  
  ]   
}  
```  

## <a name="workflow"></a>Przepływ pracy  
 Aby zaimplementować niestandardowe zachowanie oceniania, dodaj profil oceniania do schematu, który definiuje indeks. W indeksie może być maksymalnie 100 profilów oceniania (zobacz [limity usług),](search-limits-quotas-capacity.md)ale w danej kwerendzie można określić tylko jeden profil w danym przypadku.  

 Zacznij od [szablonu](#bkmk_template) podanego w tym temacie.  

 Podaj nazwę Profile oceniania są opcjonalne, ale jeśli go dodasz, nazwa jest wymagana. Pamiętaj, aby postępować zgodnie z konwencjami nazewnictwa pól (zaczyna się od litery, unika znaków specjalnych i słów zastrzeżonych). Zobacz [reguły nazewnictwa &#40;&#41;usługi Azure Cognitive Search,](https://docs.microsoft.com/rest/api/searchservice/naming-rules) aby uzyskać pełną listę.  

 Treść profilu punktacji jest skonstruowana z ważonych pól i funkcji.  

|||  
|-|-|  
|**Wagi**|Określ pary nazwa-wartość, które przypisują względną wagę do pola. W [przykładzie](#bkmk_ex)pola albumTitle, genre, and artistName fields są promowane odpowiednio o 1,5, 5 i 2. Dlaczego gatunek jest wzmocniony o wiele wyżej niż inni? Jeśli wyszukiwanie jest prowadzone przez dane, które są nieco jednorodne (jak w przypadku "gatunku" w `musicstoreindex`), może być konieczne większe wariancja względnej wagi. Na przykład w `musicstoreindex`, "rock" pojawia się zarówno jako gatunek, jak i w identycznie sformułowanych opisach gatunku. Jeśli chcesz, aby gatunek przeważył nad opisem gatunku, pole gatunku będzie wymagało znacznie większej wagi względnej.|  
|**Funkcje**|Używane, gdy dodatkowe obliczenia są wymagane dla określonych kontekstów. Prawidłowe `freshness`wartości `magnitude` `distance`to `tag`, , i . Każda funkcja ma parametry, które są unikatowe dla niego.<br /><br /> -   `freshness`powinny być używane, gdy chcesz zwiększyć, jak nowy lub stary element jest. Tej funkcji można używać `datetime` tylko z polami (edm. DataTimeOffset). Zwróć `boostingDuration` uwagę, że atrybut `freshness` jest używany tylko z funkcją.<br />-   `magnitude`należy używać, gdy chcesz zwiększyć na podstawie tego, jak wysoka lub niska jest wartość liczbowa. Scenariusze, które wymagają tej funkcji obejmują zwiększenie przez marżę zysku, najwyższą cenę, najniższą cenę lub liczbę pobrań. Tej funkcji można używać tylko z polami podwójną i całkowitą.<br />     Dla `magnitude` tej funkcji można odwrócić zakres od najwyższego do najniższego, jeśli chcesz odwrotny wzór (na przykład, aby zwiększyć niższe ceny towarów więcej niż wyższe ceny towarów). Biorąc pod uwagę zakres cen od $100 do `boostingRangeStart` $1, `boostingRangeEnd` można ustawić na 100 i na 1, aby zwiększyć niższe ceny przedmiotów.<br />-   `distance`powinny być używane, gdy chcesz zwiększyć przez bliskość lub położenie geograficzne. Tej funkcji można używać `Edm.GeographyPoint` tylko z polami.<br />-   `tag`powinny być używane, gdy chcesz zwiększyć przez tagi wspólne między dokumentami i zapytaniami wyszukiwania. Tej funkcji można używać `Edm.String` `Collection(Edm.String)` tylko z polami i.<br /><br /> **Reguły korzystania z funkcji**<br /><br /> Typ funkcji`freshness` `magnitude`( `distance` `tag` , , ), musi być małe litery.<br /><br /> Funkcje nie mogą zawierać wartości null ani empty. W szczególności, jeśli dodasz nazwa pola, musisz ustawić ją na coś.<br /><br /> Funkcje można stosować tylko do pól do filtrowania. Aby uzyskać więcej informacji na temat pól filtrowania &#40;, zobacz Tworzenie&#41;[interfejsu API usługi Azure Cognitive Search REST.](https://docs.microsoft.com/rest/api/searchservice/create-index)<br /><br /> Funkcje mogą być stosowane tylko do pól zdefiniowanych w polach kolekcji indeksu.|  

 Po zdefiniowaniu indeksu skompiluj indeks, przekazując schemat indeksu, a następnie dokumenty. Zobacz [Tworzenie &#40;interfejsu API usługi Azure Cognitive Search REST&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index) oraz [dodawanie, aktualizowanie lub usuwanie dokumentów &#40;interfejsu API rest usługi Azure Cognitive Search&#41;](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) instrukcje dotyczące tych operacji. Po zbudowaniu indeksu powinien mieć funkcjonalny profil oceniania, który współpracuje z danymi wyszukiwania.  

##  <a name="template"></a><a name="bkmk_template"></a>Szablonu  
 W tej sekcji przedstawiono składnię i szablon profilów oceniania. Opisy atrybutów można znaleźć w sekcji [Indeks atrybutów](#bkmk_indexref) w następnej sekcji.  

```  
. . .   
"scoringProfiles": [  
  {   
    "name": "name of scoring profile",   
    "text": (optional, only applies to searchable fields) {   
      "weights": {   
        "searchable_field_name": relative_weight_value (positive #'s),   
        ...   
      }   
    },   
    "functions": (optional) [  
      {   
        "type": "magnitude | freshness | distance | tag",   
        "boost": # (positive number used as multiplier for raw score != 1),   
        "fieldName": "...",   
        "interpolation": "constant | linear (default) | quadratic | logarithmic",   

        "magnitude": {
          "boostingRangeStart": #,   
          "boostingRangeEnd": #,   
          "constantBoostBeyondRange": true | false (default)
        }  

        // ( - or -)  

        "freshness": {
          "boostingDuration": "..." (value representing timespan over which boosting occurs)   
        }  

        // ( - or -)  

        "distance": {
          "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)   
          "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)   
        }   

        // ( - or -)  

        "tag": {
          "tagsParameter":  "..."(parameter to be passed in queries to specify a list of tags to compare against target field)   
        }
      }
    ],   
    "functionAggregation": (optional, applies only when functions are specified) "sum (default) | average | minimum | maximum | firstMatching"   
  }   
],   
"defaultScoringProfile": (optional) "...",   
. . .  
```  

##  <a name="index-attributes-reference"></a><a name="bkmk_indexref"></a>Odwołanie do atrybutów indeksu  

> [!NOTE]  
>  Funkcję oceniania można zastosować tylko do pól, które można filtrować.  

|Atrybut|Opis|  
|---------------|-----------------|  
|`name`|Wymagany. Jest to nazwa profilu punktacji. Wynika z tych samych konwencji nazewnictwa pola. Musi zaczynać się od litery, nie może zawierać kropek, dwukropków ani symboli @ i nie może zaczynać się od frazy "azureSearch" (z uwzględnieniem wielkości liter).|  
|`text`|Zawiera właściwości wagi.|  
|`weights`|Element opcjonalny. Zawiera pary nazwa-wartość, które określają nazwę pola i względną wagę. Waga względna musi być dodatnią liczbą całkowitą lub zmiennoprzecinkową.<br /><br /> Wagi służą do wskazywania ważności jednego pola z wyszukujalnym względem innego.|  
|`functions`|Element opcjonalny. Funkcję oceniania można zastosować tylko do pól, które można filtrować.|  
|`type`|Wymagane do oceniania funkcji. Wskazuje typ funkcji do użycia. Prawidłowe wartości obejmują wielkość, świeżość, odległość i znacznik. W każdym profilu punktacji można dołączyć więcej niż jedną funkcję. Nazwa funkcji musi być małe litery.|  
|`boost`|Wymagane do oceniania funkcji. Liczba dodatnia używana jako mnożnik dla surowego wyniku. Nie może być równa 1.|  
|`fieldname`|Wymagane do oceniania funkcji. Funkcja oceniania może być stosowana tylko do pól, które są częścią kolekcji pól indeksu i które można filtrować. Ponadto każdy typ funkcji wprowadza dodatkowe ograniczenia (świeżość jest używana z polami datetime, wielkość z polami całkowitymi lub podwójnymi oraz odległość z polami lokalizacji). Można określić tylko jedno pole dla definicji funkcji. Na przykład, aby użyć wielkości dwa razy w tym samym profilu, należy dołączyć dwie definicje wielkości, po jednej dla każdego pola.|  
|`interpolation`|Wymagane do oceniania funkcji. Definiuje nachylenie, dla którego zwiększa się wynik od początku zakresu do końca zakresu. Prawidłowe wartości obejmują liniowe (domyślne), stałe, kwadratowe i logarytmiczne. Szczegółowe informacje można znaleźć w informacji o [ustawianiu interpolacji.](#bkmk_interpolation)|  
|`magnitude`|Funkcja oceniania wielkości służy do zmiany klasyfikacji na podstawie zakresu wartości dla pola liczbowego. Oto niektóre z najczęstszych przykładów użycia:<br /><br /> -   **Oceny w gwiazdkach:** Zmień punktację na podstawie wartości w polu "Ocena gwiazdkowa". Gdy dwa elementy są istotne, element o wyższej ocenie zostanie wyświetlony jako pierwszy.<br />-   **Marża:** Gdy dwa dokumenty są istotne, sprzedawca detaliczny może chcieć najpierw zwiększyć liczbę dokumentów, które mają wyższe marże.<br />-   **Liczba kliknięć:** W przypadku aplikacji, które śledzą operacje klikania w produktach lub na stronach, można użyć wielkości, aby zwiększyć liczbę elementów, które mają tendencję do uzyskania największego ruchu.<br />-   **Liczba pobranych plików:** W przypadku aplikacji, które śledzą pobieranie, funkcja wielkości umożliwia zwiększenie elementów, które mają najwięcej pobrań.|  
|`magnitude`&#124;`boostingRangeStart`|Ustawia wartość początkową zakresu, w którym skala jest punktowany. Wartość musi być liczbą całkowitą lub zmiennoprzecinkową. W przypadku ocen w gwiazdkach od 1 do 4 byłoby to 1. W przypadku marż powyżej 50% byłoby to 50.|  
|`magnitude`&#124;`boostingRangeEnd`|Ustawia wartość końcową zakresu, w którym skala jest punktowany. Wartość musi być liczbą całkowitą lub zmiennoprzecinkową. W przypadku ocen w gwiazdkach od 1 do 4 byłoby to 4.|  
|`magnitude`&#124;`constantBoostBeyondRange`|Prawidłowe wartości są prawdziwe lub fałszywe (domyślnie). Po ustawieniu wartości true, pełne wzmocnienie będzie nadal stosowane do dokumentów, które mają wartość dla pola docelowego, która jest wyższa niż górna granica zakresu. Jeśli false, zwiększenie tej funkcji nie zostaną zastosowane do dokumentów o wartości dla pola docelowego, który mieści się poza zakresem.|  
|`freshness`|Funkcja oceniania świeżości służy do zmiany wyników klasyfikacji `DateTimeOffset` elementów na podstawie wartości w polach. Na przykład element z nowszą datą może być klasyfikowany wyżej niż starsze elementy.<br /><br /> Możliwe jest również uszeregonie elementów, takich jak wydarzenia kalendarzowe z przyszłymi datami, tak aby przedmioty bliższe teraźniejszości mogły być w przyszłości wyżej klasyfikowane niż przedmioty.<br /><br /> W bieżącej wersji usługi jeden koniec zakresu zostanie ustalony na bieżący czas. Drugi koniec to czas w przeszłości `boostingDuration`oparty na . Aby zwiększyć zakres razy w przyszłości, `boostingDuration`należy użyć negatywnego .<br /><br /> Szybkość, z jaką zwiększenie zmienia się z maksymalnego i minimalnego zakresu, jest określana przez interpolację zastosowaną do profilu punktacji (patrz rysunek poniżej). Aby odwrócić zastosowany współczynnik zwiększania, wybierz współczynnik wzmocnienia mniejszy niż 1.|  
|`freshness`&#124;`boostingDuration`|Ustawia okres wygaśnięcia, po którym zwiększanie zostanie zatrzymane dla określonego dokumentu. Zobacz [Ustaw zwiększenieDuration](#bkmk_boostdur) w poniższej sekcji dla składni i przykładów.|  
|`distance`|Funkcja oceniania odległości jest używana w celu wpłyć na wynik dokumentów na podstawie odległości lub ich odległości w stosunku do referencyjnej lokalizacji geograficznej. Lokalizacja odwołania jest podana jako część kwerendy `scoringParameterquery` w parametrze (przy użyciu opcji ciągu) jako argument lon,lat.|  
|`distance`&#124;`referencePointParameter`|Parametr, który ma być przekazywany w kwerendach do użycia jako lokalizacja referencyjna. `scoringParameter`jest parametrem zapytania. Opisy parametrów zapytania [można znaleźć w dokumentach wyszukiwania &#40;&#41;interfejsie API usługi Azure Cognitive Search REST.](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)|  
|`distance`&#124;`boostingDistance`|Liczba wskazująca odległość w kilometrach od lokalizacji odniesienia, w której kończy się zasięg zwiększania.|  
|`tag`|Funkcja oceniania znaczników jest używana do wpływania na wynik dokumentów na podstawie znaczników w dokumentach i zapytaniach wyszukiwania. Dokumenty, które mają znaczniki wspólne z zapytaniem wyszukiwania zostaną promowane. Znaczniki zapytania wyszukiwania są dostarczane jako parametr oceniania w `scoringParameterquery` każdym żądaniu wyszukiwania (przy użyciu opcji ciągu).|  
|`tag`&#124;`tagsParameter`|Parametr, który ma być przekazywany w kwerendach, aby określić tagi dla określonego żądania. `scoringParameter`jest parametrem zapytania. Opisy parametrów zapytania [można znaleźć w dokumentach wyszukiwania &#40;&#41;interfejsie API usługi Azure Cognitive Search REST.](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)|  
|`functionAggregation`|Element opcjonalny. Ma zastosowanie tylko wtedy, gdy określono funkcje. Prawidłowe wartości obejmują: sumę (domyślnie), średnią, minimalną, maksymalną i pierwszą. Wynik wyszukiwania jest pojedynczą wartością obliczoną na podstawie wielu zmiennych, w tym wielu funkcji. Ten atrybut wskazuje, jak zwiększa wszystkie funkcje są łączone w jeden impuls agregacji, który następnie jest stosowany do wyniku dokumentu podstawowego. Wynik podstawowy jest oparty na wartości [tf-idf](http://www.tfidf.com/) obliczonej na podstawie dokumentu i zapytania wyszukiwania.|  
|`defaultScoringProfile`|Podczas wykonywania żądania wyszukiwania, jeśli nie określono profilu oceniania, używana jest domyślna punktacja (tylko[tf-idf).](http://www.tfidf.com/)<br /><br /> W tym miejscu można ustawić domyślną nazwę profilu oceniania, co powoduje, że usługa Azure Cognitive Search używa tego profilu, gdy w żądaniu wyszukiwania nie podano określonego profilu.|  

##  <a name="set-interpolations"></a><a name="bkmk_interpolation"></a>Ustawianie interpolacji  
 Interpolacje umożliwiają ustawienie kształtu nachylenia używanego do oceniania. Ponieważ punktacja jest wysoka do najniższej, nachylenie zawsze maleje, ale interpolacja określa krzywą nachylenia w dół. Można stosować następujące interpolacje:  

|||  
|-|-|  
|`linear`|W przypadku przedmiotów, które znajdują się w zakresie maksymalnym i minowym, wzmocnienie zastosowane do przedmiotu będzie odbywać się w stale malejącej ilości. Liniowy jest domyślną interpolacją profilu punktacji.|  
|`constant`|W przypadku elementów, które znajdują się w zakresie początkowym i końcowym, do wyników rankingu zostanie zastosowane stałe wzmocnienie.|  
|`quadratic`|W porównaniu do interpolacji liniowej, która ma stale malejący wzrost, Quadratic początkowo zmniejsza się w mniejszym tempie, a następnie w miarę zbliżania się do zakresu końcowego, zmniejsza się w znacznie wyższych odstępach czasu. Ta opcja interpolacji nie jest dozwolona w funkcjach oceniania znaczników.|  
|`logarithmic`|W porównaniu do interpolacji liniowej, która ma stale malejący wzrost, Logarytmiczna początkowo zmniejsza się w wyższym tempie, a następnie w miarę zbliżania się do zakresu końcowego zmniejsza się w znacznie mniejszym odstępie czasu. Ta opcja interpolacji nie jest dozwolona w funkcjach oceniania znaczników.|  

 ![Stałe, liniowe, kwadratowe, log10 linii na wykresie](media/scoring-profiles/azuresearch_scorefunctioninterpolationgrapht.png "AzureSearch_ScoreFunctionInterpolationGrapht")  

##  <a name="set-boostingduration"></a><a name="bkmk_boostdur"></a>Ustawianie zwiększaniaduracjania  
 `boostingDuration`jest atrybutem `freshness` funkcji. Służy do ustawiania okresu wygaśnięcia, po którym promowanie zostanie zatrzymane dla określonego dokumentu. Na przykład, aby zwiększyć linię produktów lub markę na 10-dniowy okres promocyjny, należy określić okres 10 dni jako "P10D" dla tych dokumentów.  

 `boostingDuration`musi być sformatowana jako wartość XSD "dayTimeDuration" (ograniczony podzbiór wartości czasu trwania ISO 8601). Wzorzec jest taki: "P[nD][T[nH][nM][nS]]".  

 W poniższej tabeli przedstawiono kilka przykładów.  

|Czas trwania|zwiększenieDuration|  
|--------------|----------------------|  
|1 dzień|"P1D"|  
|2 dni i 12 godzin|"P2DT12H"|  
|15 minut|"PT15M"|  
|30 dni, 5 godzin, 10 minut i 6,334 sekundy|"P30DT5H10M6.334S"|  

 Aby uzyskać więcej przykładów, zobacz [Schemat XML: Typy danych (W3.org witryny sieci Web)](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration).  

## <a name="see-also"></a>Zobacz też  
 [Azure Cognitive Search REST](https://docs.microsoft.com/rest/api/searchservice/)   
 [Tworzenie &#40;interfejsu API usługi Azure Cognitive Search REST&#41;&#40;](https://docs.microsoft.com/rest/api/searchservice/create-index)   
 [Azure Cognitive Search .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
