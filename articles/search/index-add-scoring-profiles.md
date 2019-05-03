---
title: Dodać profile oceniania do indeksu wyszukiwania — usługa Azure Search
description: Zwiększ randze wyszukiwania dla wyników wyszukiwania w usłudze Azure Search, dodając profile oceniania.
ms.date: 05/02/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: 9ccb6944227208cee8601751cf43a53c111c09c6
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65021627"
---
# <a name="add-scoring-profiles-to-an-azure-search-index"></a>Dodać profile oceniania do indeksu usługi Azure Search

  Ocenianie odwołuje się do obliczania *Wyszukaj wynik* dla każdego elementu w wynikach wyszukiwania. Wynik jest wskaźnikiem istotności elementów w kontekście bieżącej operacji wyszukiwania. Im większa liczba punktów, tym większe znaczenie elementu. W wynikach wyszukiwania elementy są uporządkowane od największej do niski, oparte na wyszukiwaniu obliczane dla każdego elementu rangi.  

 Usługa Azure Search korzysta z domyślnego przyznawania ocen, aby obliczenia wyniku początkowej, ale można dostosować obliczenia za pośrednictwem *profil oceniania*. Profile oceniania zapewniają większą kontrolę nad kolejność elementów w wynikach wyszukiwania. Na przykład można zwiększyć elementów, w oparciu o ich potencjalne przychody, promowanie nowe elementy i być może zwiększyć elementy, które zostały w spisie jest za długa.  

 Profil oceniania jest częścią definicji indeksu, składa się z pól ważona, funkcje i parametry.  

 Umożliwiają oszacowanie wygląda profilu oceniania, poniższy przykład pokazuje prosty profil o nazwie "geograficznej". Ta zwiększa elementy, które zawierają termin wyszukiwania w **hotelName** pola. Korzysta również `distance` funkcję, aby preferował elementów znajdujących się w ciągu dziesięciu kilometrów od bieżącej lokalizacji. Ktoś wyszukuje wystąpienia terminu "numer identyfikacyjny" i "numer identyfikacyjny" stanie się być częścią Nazwa hotelu, dokumenty, które obejmują hotele za pomocą "inn" w promieniu 10 KM bieżącą lokalizację pojawi się wyżej w wynikach wyszukiwania.  


```  
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


 Aby użyć tego profilu oceniania, zapytanie jest określona w sposób wybierz profil, który w ciągu zapytania. W poniższym zapytaniu Zwróć uwagę, parametr zapytania `scoringProfile=geo` w żądaniu.  

```  
GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2019-05-06 
```  

 To zapytanie wyszukuje wystąpienia terminu "numer identyfikacyjny" i przekazuje w bieżącej lokalizacji. Zwróć uwagę, że to zapytanie zawiera inne parametry takich jak `scoringParameter`. Parametry zapytania są opisane w [wyszukiwania dokumentów &#40;interfejsu API REST usługi Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).  

 Kliknij przycisk [przykład](#bkmk_ex) Aby zapoznać się z bardziej szczegółowy przykład profilu oceniania.  

## <a name="what-is-default-scoring"></a>Co to jest oceniania domyślny?  
 Ocenianie oblicza wynik wyszukiwania dla każdego elementu w zestawie rangi uporządkowanych wyników. Każdy element w zestawie wyników wyszukiwania jest przypisany wyniku wyszukiwania, a następnie randze spośród wszystkich dokumentów najwyższej do najniższej. Elementy z wyższym wyniki są zwracane do aplikacji. Domyślnie zwracane są najważniejsze 50, ale można użyć `$top` parametru do zwrócenia mniejszy lub większy liczbę elementów (maksymalnie 1000 w jednej odpowiedzi).  

Wynik wyszukiwania jest obliczana na podstawie właściwości statystyczne danych i zapytania. Usługa Azure Search znajduje dokumenty, które zawierają terminy wyszukiwania w ciągu zapytania (niektóre lub wszystkie, w zależności od `searchMode`), favoring dokumentów, które zawierają wiele wystąpień termin wyszukiwania. Wynik wyszukiwania przechodzi w górę nawet większe, jeśli termin jest rzadkie dla indeksu danych, ale typowe w dokumencie. Podstawa dla tej metody do przetwarzania informacji o zgodności jest znany jako [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) lub określenie częstotliwości odwrotność dokumentu częstotliwości.  

 Przy założeniu, że nie ma żadnych niestandardowych sortowanie, wyniki są następnie randze spośród wszystkich dokumentów według wyników wyszukiwania zanim zostaną one zwrócone do aplikacji wywołującej. Jeśli nie określono $top, zwracane są 50 elementów o najwyższym wynikiem wyszukiwania.  

 Wartości wynik wyszukiwania można powtarzać w całym zestawie wyników. Przykładowo może istnieć 10 elementów z wynikiem 1.2, 20 elementów z wynikiem 1.0 i 20 elementów z wynikiem wynosi 0,5. Gdy wiele trafień mają ten sam wynik wyszukiwania, kolejność tych samych towarów ocenami nie jest zdefiniowana, a nie jest stabilna. Uruchom zapytanie ponownie, a zobaczysz w nim elementy pozycji przesunięcia. Biorąc pod uwagę dwa elementy z oceną identyczne, nie ma gwarancji co występuje jako pierwszy.  

## <a name="when-to-use-custom-scoring"></a>Kiedy należy używać niestandardowego przyznawania ocen  
 Należy utworzyć co najmniej jeden profil oceniania, gdy nie jest akceptowana wartość domyślna Klasyfikacja zachowanie wystarczający w spełnienie celów biznesowych. Na przykład można zdecydować, czy nowo dodanych elementów powinny favor, trafności wyszukiwania. Podobnie Niewykluczone, że pole zawierające marża zysku lub inne pole, wskazując potencjalne przychody. Ulepszanie trafień, które zapewniają korzyści dla Twojej firmy może być ważnym czynnikiem w podejmowaniu decyzji o użyciu profile oceniania.  

 Na podstawie dokładność kolejność również jest implementowane za pomocą profile oceniania. Należy wziąć pod uwagę strony wyników wyszukiwania używane w przeszłości, dzięki którym możesz sortować według ceny, Data, ocena lub istotności. W usłudze Azure Search profile oceniania dysku opcji "przydatność". Definicja istotności jest kontrolowana przez Ciebie uzależniona od celów biznesowych i typu wyszukiwania, które mają zostać dostarczone.  

##  <a name="bkmk_ex"></a> Przykład  
 Jak wspomniano wcześniej, dostosowane oceniania jest implementowane za pomocą co najmniej jeden profil oceniania zdefiniowanej w schemacie indeksu.  

 Ten przykład przedstawia schematu indeksu dwa profile oceniania (`boostGenre`, `newAndHighlyRated`). Wszystkie zapytania względem tego indeksu, który zawiera profil albo parametr zapytania będziesz używać profilu, który będzie oceniać zestaw wyników.  

```  
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
 Aby zaimplementować niestandardowe zachowanie oceniania, Dodaj profil oceniania schematu, który definiuje indeks. Może mieć maksymalnie 100 profile w ramach indeksu oceniania (zobacz [limity usługi](search-limits-quotas-capacity.md)), ale w czasie w dowolnej podanej kwerendy można określić tylko jeden profil.  

 Rozpoczynać [szablonu](#bkmk_template) podane w tym temacie.  

 Podaj nazwę. Profile oceniania są opcjonalne, ale jeśli dodasz jeden, wymagana jest nazwa. Należy postępować zgodnie z konwencjami nazewnictwa dla pola (rozpoczyna się od litery i pozwala uniknąć znaki specjalne i słowa zastrzeżone). Zobacz [reguły nazewnictwa &#40;usługi Azure Search&#41; ](https://docs.microsoft.com/rest/api/searchservice/naming-rules) pełną listę.  

 Treść profilu oceniania jest tworzona z pól ważona i funkcji.  

|||  
|-|-|  
|**Wagi**|Określ pary nazwa wartość, których przypisanie względną wagę do pola. W [przykład](#bkmk_ex), albumTitle gatunku i artistName pola są odpowiednio wzmocnione wersje od 1.5, 5 i 2. Dlaczego jest gatunku wzmocnione znacznie wyższe niż inne? Jeśli wyszukiwanie jest przeprowadzane nad danymi, które są nieco jednorodnego (tak jak w przypadku za pomocą "gatunku" w `musicstoreindex`), możesz potrzebować większej wariancji w względnych wag. Na przykład w `musicstoreindex`, "rock" pojawia się jako zarówno gatunku i w opisach gatunku identycznie ma inną pisownię. Chcąc gatunku, aby zrównoważyć opis gatunku, pole gatunku należy znacznie wyższa waga względnych.|  
|**Funkcje**|Używany, gdy dodatkowe obliczenia są wymagane dla określonych kontekstach. Prawidłowe wartości to `freshness`, `magnitude`, `distance`, i `tag`. Każda funkcja ma następujące parametry, które są unikatowe.<br /><br /> -   `freshness` Jeśli chcesz zwiększyć możliwości, należy użyć jak nowej lub starej element to. Tej funkcji należy używać tylko z `datetime` pola (edm. DataTimeOffset). Zwróć uwagę `boostingDuration` atrybut jest używany tylko w przypadku `freshness` funkcji.<br />-   `magnitude` należy używać, gdy chcesz zwiększyć oparte na sposób wysokiego lub niska wartość liczbowa jest. Scenariusze, które wywołują tę funkcję, aby uzyskać obejmują zwiększania wyniku marża zysku, najwyższej ceny, najniższą cenę lub liczba plików do pobrania. Ta funkcja może służyć tylko z polami Podwójna precyzja i liczby całkowitej.<br />     Aby uzyskać `magnitude` funkcji, można odwrócić zakresu, wysoka, aby małych, jeśli chcesz, aby odwrotność wzorca (na przykład, aby elementy boost tańszych więcej niż wyższych cenach elementów). Biorąc pod uwagę zakres cen od 100 do $1, należy ustawić `boostingRangeStart` 100 i `boostingRangeEnd` od 1 do poprawienia elementów niższej cenie.<br />-   `distance` należy używać, gdy użytkownik chce zwiększyć sąsiedztwie lub lokalizacji geograficznej. Tej funkcji należy używać tylko z `Edm.GeographyPoint` pola.<br />-   `tag` należy używać, gdy użytkownik chce zwiększyć według tagów w typowych między dokumentami a zapytania wyszukiwania. Tej funkcji należy używać tylko z `Edm.String` i `Collection(Edm.String)` pola.<br /><br /> **Reguły dotyczące używania funkcji**<br /><br /> Typ funkcji (`freshness`, `magnitude`, `distance`), `tag` muszą być małymi literami.<br /><br /> Funkcji nie może zawierać wartości null ani być pusta. W szczególności jeśli nazwa pola, należy ustawić tekst w rodzaju.<br /><br /> Funkcje można stosować tylko do pól z możliwością filtrowania. Zobacz [Create Index &#40;interfejsu API REST usługi Azure Search&#41; ](https://docs.microsoft.com/rest/api/searchservice/create-index) Aby uzyskać więcej informacji na temat pól z możliwością filtrowania.<br /><br /> Funkcje będzie stosowany tylko do pól, które są zdefiniowane w kolekcji pól indeks.|  

 Po zdefiniowaniu indeksu, należy utworzyć indeks przez przekazanie schemat indeksu, a następnie dokumentów. Zobacz [Create Index &#40;interfejsu API REST usługi Azure Search&#41; ](https://docs.microsoft.com/rest/api/searchservice/create-index) i [Add, Update lub usuwanie dokumentów &#40;interfejsu API REST usługi Azure Search&#41; ](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) instrukcje na temat tych operacji. Po utworzeniu indeksu powinny mieć profil oceniania funkcjonalności, który pracuje z danymi wyszukiwania.  

##  <a name="bkmk_template"></a> Szablon  
 W tej sekcji przedstawiono składnię i szablon profile oceniania. Zapoznaj się [indeksu dokumentacja atrybutów](#bkmk_indexref) w następnej sekcji, aby uzyskać opis atrybutów.  

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

##  <a name="bkmk_indexref"></a> Dokumentacja atrybutów indeksu  

> [!NOTE]  
>  Funkcję oceniania będzie stosowany tylko do pól, które są filtrowania.  

|Atrybut|Opis|  
|---------------|-----------------|  
|`Name`|Wymagany. To jest nazwa profilu oceniania. Jest zgodna z tej samej konwencji nazewnictwa pola. To musi zaczynać się literą, nie może zawierać kropki, dwukropki i @ symbole i nie może zaczynać się od wyrażenia "azureSearch" (z uwzględnieniem wielkości liter).|  
|`Text`|Zawiera właściwość wagi.|  
|`Weights`|Opcjonalny. Pary nazwa wartość, który określa nazwę pola i — Względna waga. Względna waga musi być dodatnią liczbą całkowitą lub zmiennoprzecinkową. Wartość maksymalna to wartość int32. MaxValue.<br /><br /> Można określić nazwę pola bez odpowiedniej wagi. Wagi są używane do wskazać wagę jedno pole względem innego.|  
|`Functions`|Opcjonalny. Funkcję oceniania będzie stosowany tylko do pól, które są filtrowania.|  
|`Type`|Wymagane na potrzeby oceniania funkcji. Wskazuje typ funkcja do użycia. Prawidłowe wartości to wielkości, świeżości, odległości i tagu. Może zawierać więcej niż jedną funkcję w każdym profilu oceniania. Nazwa funkcji musi być małymi literami.|  
|`Boost`|Wymagane na potrzeby oceniania funkcji. Liczba dodatnia używana jako mnożnik nieprzetworzonej oceny. Nie może być równa 1.|  
|`Fieldname`|Wymagane na potrzeby oceniania funkcji. Funkcja oceniania będzie stosowany tylko do pól, które są częścią kolekcji pól Indeks i które są filtrowania. Ponadto każdy typ funkcji wprowadza dodatkowe ograniczenia (świeżości jest używany z pól daty i godziny, wielkości z liczbą całkowitą lub podwójne pola i odległość z polami lokalizacji). Można określić tylko jedno pole dla definicji funkcji. Na przykład aby użyć wielkości dwa razy ten sam profil, będziesz potrzebować obejmujący dwa wielkości definicje, jeden dla każdego pola.|  
|`Interpolation`|Wymagane na potrzeby oceniania funkcji. Określa nachylenie dla którego zwiększania wyniku od początku zakresu do końca zakresu. Prawidłowe wartości to liniowej (ustawienie domyślne), stała, umożliwiającej obliczanie kwadratów i skali logarytmicznej. Zobacz [Ustaw interpolations](#bkmk_interpolation) Aby uzyskać szczegółowe informacje.|  
|`magnitude`|Wielkość oceniania funkcja jest używana do wpływanie na podstawie zakresu wartości pola liczbowego. Najbardziej typowe przykłady użycia tego należą:<br /><br /> -   **Klasyfikacji w formie gwiazdek:** Instrukcja ALTER oceniania na podstawie wartości w polu "Ocena Star". Gdy dwa elementy są istotne, element o wyższych ocena pojawi się najpierw.<br />-   **Margines:** Gdy dwa dokumenty są istotne, sprzedawcy mogą chcieć zwiększania dokumentów, które mają wyższe marginesy najpierw.<br />-   **Kliknij liczniki:** Aplikacje, które śledzą kliknij za pomocą akcji do produktów lub stron, można użyć wielkości boost elementów, które przeważnie, aby uzyskać najbardziej ruchu.<br />-   **Pobierz liczniki:** Dla aplikacji, śledzenie pliki do pobrania, wielkości funkcja pozwala na zwiększenie elementy, które mają najwięcej plików do pobrania.|  
|`magnitude` &#124; `boostingRangeStart`|Ustawia wartość początkową zakresu, w którym jest oceniana. Wartość musi być liczbą całkowitą lub zmiennoprzecinkową. Dla klasyfikacji w formie gwiazdek od 1 do 4 będzie to 1. Marginesy w ponad 50% będzie to 50.|  
|`magnitude` &#124; `boostingRangeEnd`|Ustawia wartość końcową zakresu, w którym jest oceniana. Wartość musi być liczbą całkowitą lub zmiennoprzecinkową. Dla klasyfikacji w formie gwiazdek od 1 do 4 będzie to 4.|  
|`magnitude` &#124; `constantBoostBeyondRange`|Prawidłowe wartości to true lub false (domyślnie). Po ustawieniu na wartość true, pełne zwiększanie wyniku będzie stosowane do dokumentów, które mają wartość pola docelowego jest większa niż górna granica zakresu. Jeśli wartość to false, zwiększanie wyniku tej funkcji nie zostanie zastosowany do dokumentów, których wartość pola docelowego jest spoza zakresu.|  
|`freshness`|Świeżości oceniania funkcja służy do zmiany świeżości na podstawie wartości w `DateTimeOffset` pola. Na przykład element z datą nowszą można sklasyfikować wyższe niż starszych elementów.<br /><br /> Istnieje również możliwość rangi elementy, takie jak zdarzenia kalendarza znajdują się daty w przyszłości, tak, aby bliżej elementy bieżącej można sklasyfikować wyższej niż elementów bardziej szczegółowo w przyszłości.<br /><br /> W bieżącej wersji usługi co koniec zakresu problem zostanie rozwiązany do bieżącego czasu. Drugi to czas w przeszłości, w oparciu o `boostingDuration`. Aby zwiększyć zakres czasu w przyszłości, należy użyć ujemnych `boostingDuration`.<br /><br /> Szybkość, z jaką zwiększania wyniku zmieni się z maksymalnie i minimalny zakres jest określany przez interpolacji stosowane do profilu oceniania (zobacz rysunek poniżej). Aby odwrócić zwiększenie współczynnika stosowane, wybierz współczynnik boost mniejsza niż 1.|  
|`freshness` &#124; `boostingDuration`|Ustawia okres, po którym zwiększanie wyniku zostanie zatrzymane dla określonego dokumentu. Zobacz [Ustaw boostingDuration](#bkmk_boostdur) w poniższej sekcji o składni i przykłady.|  
|`distance`|Odległość, używanych funkcji oceniania wpływa na wynik dokumentu, zależnie od zamknąć lub znacznie są względem odwołanie do lokalizacji geograficznej. Lokalizacja odwołanie jest podawana jako część zapytania w parametrze (przy użyciu `scoringParameterquery` ciąg opcji) jako długa, lat argumentu.|  
|`distance` &#124; `referencePointParameter`|Parametr przekazywany w zapytaniach do użycia jako odwołanie do lokalizacji. `scoringParameter` to parametr zapytania. Zobacz [wyszukiwania dokumentów &#40;interfejsu API REST usługi Azure Search&#41; ](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) opisy parametrów zapytania.|  
|`distance` &#124; `boostingDistance`|Liczba, która określa odległość w kilometrów od lokalizacji gdzie kończy się końca zakresu zwiększania wyniku.|  
|`tag`|Tag oceniania funkcja jest używana do wpływa na wynik dokumentu na podstawie tagów w dokumentach i zapytaniach wyszukiwania. Dokumenty, które mają tagi wspólnych zapytania wyszukiwania będą zwiększane. Tagi dla zapytania wyszukiwania jest dostarczana jako parametr oceniania w każdym żądaniu wyszukiwania (przy użyciu `scoringParameterquery` ciąg opcji).|  
|`tag` &#124; `tagsParameter`|Parametr przekazywany w zapytaniach, aby określić znaczniki dla określonego żądania. `scoringParameter` to parametr zapytania. Zobacz [wyszukiwania dokumentów &#40;interfejsu API REST usługi Azure Search&#41; ](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) opisy parametrów zapytania.|  
|`functionAggregation`|Opcjonalny. Ma zastosowanie tylko wtedy, gdy funkcje są określone. Prawidłowe wartości to: Suma (ustawienie domyślne), średnią, minimalna, maksymalna i firstMatching. Wynik wyszukiwania jest pojedyncza wartość, która jest obliczany na podstawie wielu zmiennych, łącznie z wielu funkcji. Ten atrybut wskazuje, jak zwiększa wszystkich funkcji są łączone w jednej wartości zagregowanej zwiększania, który następnie jest stosowany do wyniku podstawowego dokumentu. Wynik podstawowy opiera się na [tf-idf](http://www.tfidf.com/) wartości obliczane na podstawie dokumentu i zapytania wyszukiwania.|  
|`defaultScoringProfile`|Podczas wykonywania żądania wyszukiwania, jeśli nie określono profil oceniania, a następnie oceniania domyślnej jest używana ([tf-idf](http://www.tfidf.com/) tylko).<br /><br /> Domyślna nazwa profilu oceniania można ustawić w tym miejscu, powodując usługi Azure Search użyć tego profilu, gdy nie określonego profilu jest podany w żądaniu wyszukiwania.|  

##  <a name="bkmk_interpolation"></a> Zestaw interpolations  
 Interpolations umożliwiają ustawienie kształt nachylenie używane do oceniania. Ponieważ oceniania jest wysoko lub nisko, zmniejsza się zawsze nachylenie, ale interpolacji określa krzywą nachylenie w dół. Można użyć następujących interpolations:  

|||  
|-|-|  
|`Linear`|Dla elementów, które znajdują się w zakresie maksymalne i minimalne boost stosowany do elementu, zostanie wykonane w stale zmniejsza ilość. Liniowy jest interpolacji domyślnego profilu oceniania.|  
|`Constant`|Dla elementów, które znajdują się w początkowej i końcowej zakresu stałe zwiększanie wyniku zostaną zastosowane do rangi wyników.|  
|`Quadratic`|Porównaniu interpolacji liniowej ma boost stale maleje umożliwiającej obliczanie kwadratów początkowo zostaną obniżone tempie mniejszych, a następnie jako zbliża się koniec zakresu, jego maleje interwałem znacznie wyższa. Ta opcja interpolacji nie jest dozwolone w tagu funkcje oceniania.|  
|`Logarithmic`|Porównaniu interpolacji liniowej ma boost stale maleje skali logarytmicznej początkowo zostaną obniżone tempie wyższej, a następnie jako zbliża się koniec zakresu, jego maleje w znacznie mniejszych odstępach czasu. Ta opcja interpolacji nie jest dozwolone w tagu funkcje oceniania.|  

 ![LOG10 — stałe, liniowych, kwadratowych, linii na wykresie](media/scoring-profiles/azuresearch_scorefunctioninterpolationgrapht.png "AzureSearch_ScoreFunctionInterpolationGrapht")  

##  <a name="bkmk_boostdur"></a> Zestaw boostingDuration  
 `boostingDuration` jest to atrybut `freshness` funkcji. Służy do ustawienia wygasania kropki, po którym zwiększanie wyniku zostanie zatrzymane dla określonego dokumentu. Na przykład aby zwiększyć linia produktów lub marki dla 10-dniowego okresu promocyjnego, należy określić 10-dniowego okresu jako "P10D" w tych dokumentach.  

 `boostingDuration` muszą być sformatowane jako wartość XSD "dayTimeDuration" (ograniczony podzestaw wartości czasu trwania ISO 8601). Wzorzec ten jest: "P[nD][T[nH][nM][nS]]".  

 Poniższa tabela zawiera kilka przykładów.  

|Czas trwania|boostingDuration|  
|--------------|----------------------|  
|1 dzień|"P1D"|  
|2 dni i 12 godzin|"P2DT12H"|  
|15 minut|"PT15M"|  
|30 dni, 5 godzin, 10 minut, a 6.334 sekund|"P30DT5H10M6.334S"|  

 Aby uzyskać więcej przykładów, zobacz [schematu XML: Typy danych (witryna sieci web adresem W3.org)](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration).  

## <a name="see-also"></a>Zobacz także  
 [REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice/)   
 [Tworzenie indeksu &#40;interfejsu API REST usługi Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)   
 [Zestaw SDK platformy .NET usługi Azure Search](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
