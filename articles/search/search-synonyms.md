---
pageTitle: Synonyms in Azure Search | Microsoft Docs
description: Użyj synonimy, aby rozszerzyć zakres wyszukiwania
authors: mhko
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
manager: jlembicz
ms.author: nateko
ms.openlocfilehash: 03e45aae37a0c0474dbd9cc5dd5e3fddd347bd62
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="synonyms-in-azure-search"></a>Synonimy w usłudze Azure Search

Synonimy w wyszukiwarkach skojarzyć równoważnego niejawnie rozszerzające zakres kwerendy, bez konieczności podawania faktycznie termin użytkownika. Na przykład mając skojarzenia "dog" i synonim termin "canine" i "puppy" dokumenty zawierające "kot", "pies" lub "puppy" zostaną objęte zakresem zapytania.

W usłudze Azure Search rozszerzenia synonim odbywa się na etapie zapytania. Synonim mapy można dodać do usługi za pomocą nie przerywania istniejące operacje. Możesz dodać **synonymMaps** właściwości do definicji pola bez konieczności odbudowanie indeksu.

## <a name="feature-availability"></a>Dostępność funkcji

Funkcja synonimów jest obsługiwana przez najnowszą wersją interfejsu api (interfejs api-version = 2017-11-11). Obecnie witryna Azure Portal nie jest obsługiwana.

## <a name="how-to-use-synonyms-in-azure-search"></a>Jak używać synonimy w usłudze Azure search

W usłudze Azure Search synonim pomocy technicznej jest oparta na mapy synonim, definiujących i przekazać do usługi. Mapy te stanowią niezależnym zasobem (jak indeksy lub źródeł danych) i mogą być używane przez wszystkie pola można wyszukiwać w indeksu w swojej usłudze wyszukiwania.

Synonim map i indeksów są przechowywane niezależnie od siebie. Po zdefiniowaniu mapy synonim i przekaż go do usługi, mogą włączyć funkcję synonim dla pola, dodając nową właściwość o nazwie **synonymMaps** w definicji pola. Tworzenie, aktualizowanie i usuwanie mapy synonim zawsze jest operacją całego dokumentu, co oznacza, że użytkownik nie może utworzyć, aktualizować lub usuwać części mapy synonim przyrostowo. Aktualizowanie nawet pojedynczy wpis wymaga ponowne załadowanie.

Dołączanie do aplikacji wyszukiwania synonimy jest procesem dwuetapowym:

1.  Dodaj mapowanie synonim z usługą wyszukiwania przy użyciu interfejsów API poniżej.  

2.  Skonfiguruj wyszukiwanie pole, aby użyć mapy synonim w definicji indeksu.

### <a name="synonymmaps-resource-apis"></a>Interfejsy API SynonymMaps zasobów

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>Dodać lub zaktualizować synonimu mapy w ramach usługi, za pomocą POST i PUT.

Synonim mapy są przekazywane do usługi za pośrednictwem POST i PUT. Każda reguła muszą być rozdzielane przy znaku nowego wiersza ("\n"). Można określić maksymalnie 5000 zasad na mapie synonim w bezpłatnej usługi i 10 000 reguł w innych jednostki SKU. Każda reguła może mieć maksymalnie 20 rozszerzenia.

Synonim mapy musi być w formacie Apache Solr, który znajduje się poniżej. Jeśli masz istniejący słownik synonim w innym formacie i chcesz używać go bezpośrednio, prosimy o kontakt na [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

Można utworzyć nowej mapy synonim przy użyciu metody POST protokołu HTTP, jak w poniższym przykładzie:

    POST https://[servicename].search.windows.net/synonymmaps?api-version=2017-11-11
    api-key: [admin key]

    {  
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

Alternatywnie można użyć PUT i określ nazwę mapy synonim w identyfikatorze URI. Mapa synonim nie istnieje, zostanie utworzona.

    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2017-11-11
    api-key: [admin key]

    {  
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

##### <a name="apache-solr-synonym-format"></a>Format synonim Apache Solr

Solr format obsługuje synonim równoważne i jawnego mapowania. Reguły mapowania stosować się do synonimu specyfikacja filtru typu open source Apache Solr, w tym dokumencie opisano: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). Poniżej znajduje się przykładowa reguła synonimy równoważne.
```
USA, United States, United States of America
```

Z regułą powyżej zapytania wyszukiwania rozwinie "USA" do "USA" lub "Stany Zjednoczone" lub "Stany Zjednoczone".

Jawne mapowanie jest oznaczona za pomocą strzałki "= >". Gdy jest określony, sekwencji termin zapytania wyszukiwania, która jest zgodna z lewej strony "= >" zostanie zamieniony alternatyw po prawej stronie. Biorąc pod uwagę poniższe reguły, wyszukaj zapytania "W stanie Waszyngton", "Wash." lub "WA" będą wszystkie ponownego napisania do "WA". Jawne mapowanie tylko stosuje w kierunku określony i nie Przeredaguj zapytanie "WA" do "W stanie Waszyngton" w tym przypadku.
```
Washington, Wash., WA => WA
```

#### <a name="list-synonym-maps-under-your-service"></a>Synonim listy mapy w ramach usługi.

    GET https://[servicename].search.windows.net/synonymmaps?api-version=2017-11-11
    api-key: [admin key]

#### <a name="get-a-synonym-map-under-your-service"></a>Uzyskać synonim mapy w ramach usługi.

    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2017-11-11
    api-key: [admin key]

#### <a name="delete-a-synonyms-map-under-your-service"></a>Usuń synonimy mapy w ramach usługi.

    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2017-11-11
    api-key: [admin key]

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>Skonfiguruj wyszukiwanie pole, aby użyć mapy synonim w definicji indeksu.

Nowe właściwości pola **synonymMaps** może służyć do określenia mapę synonim do użycia dla pola wyszukiwania. Synonim mapy zasobów poziomu usługi i mogą odwoływać się do dowolnego pola indeksu w usłudze.

    POST https://[servicename].search.windows.net/indexes?api-version=2017-11-11
    api-key: [admin key]

    {
       "name":"myindex",
       "fields":[
          {
             "name":"id",
             "type":"Edm.String",
             "key":true
          },
          {
             "name":"name",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"en.lucene",
             "synonymMaps":[
                "mysynonymmap"
             ]
          },
          {
             "name":"name_jp",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"ja.microsoft",
             "synonymMaps":[
                "japanesesynonymmap"
             ]
          }
       ]
    }

**synonymMaps** można określić w przypadku pól z możliwością wyszukiwania typu "Z typem Edm.String" lub "Collection(Edm.String)".

> [!NOTE]
> Może mieć tylko jeden synonim mapy jednym polu. Jeśli chcesz użyć wielu map synonim, prosimy o kontakt na [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="impact-of-synonyms-on-other-search-features"></a>Wpływ synonimy dla innych funkcji wyszukiwania

Funkcja synonimy ponownie zapisuje oryginalne zapytanie z synonimy za pomocą operatora OR. Z tego powodu wyróżnianie trafień i oceniania profile traktować oryginalny termin i synonimy jako równoważne.

Funkcja synonim dotyczy kwerendy wyszukiwania i nie ma zastosowania do filtrów lub zestawów reguł. Podobnie sugestie dotyczą tylko oryginalny termin; synonim dopasowań nie są wyświetlane w odpowiedzi.

Synonim rozszerzenia nie dotyczą terminy wyszukiwania symboli wieloznacznych; Prefiks rozmytego oraz postanowienia wyrażenia regularnego nie są rozwinięte.

## <a name="tips-for-building-a-synonym-map"></a>Wskazówki dotyczące tworzenia mapy synonim

- Mapa synonim zwięzły, dobrze zaprojektowanego jest bardziej efektywne niż pełny wykaz możliwe dopasowania. Bardzo dużych lub złożonych słowniki trwać dłużej, analizy i wpływać na opóźnienie zapytania, jeśli zapytanie rozwijany do wielu synonimy. Zamiast warunki mogą być używane dopasowanie, możesz uzyskać rzeczywiste warunki za pośrednictwem [raport analizy ruchu wyszukiwania](search-traffic-analytics.md).

- Jak wykonywać zarówno wersja wstępna i sprawdzania poprawności, Włącz, a następnie użycie tego raportu, aby precyzyjnie określić warunki, które będą korzystać z dopasowanie synonim i następnie kontynuuj używać go jako że mapy synonim jest tworzenie lepsze wyniki sprawdzania poprawności. W raporcie wstępnie zdefiniowanych Kafelki "najbardziej typowe zapytania wyszukiwania" i "Zero wynik zapytania wyszukiwania" zapewni niezbędne informacje.

- Można utworzyć wielu map synonim aplikacji wyszukiwania (np. według języka, jeśli aplikacja obsługuje wielu języków bazy klientów). Obecnie pola można używać tylko jeden z nich. W dowolnym momencie możesz zaktualizować właściwość synonymMaps pola.

## <a name="next-steps"></a>Kolejne kroki

- Jeśli masz istniejący indeks w środowisku projektowym (z systemem innym niż środowisko produkcyjne), wypróbuj słownika mała, aby zobaczyć, jak dodanie synonimy zmienia środowiska wyszukiwania, włączając wpływ na oceniania profile, wyróżnianie trafień i sugestie.

- [Włącz analizy ruchu wyszukiwania](search-traffic-analytics.md) i użyj wstępnie zdefiniowanego raportu usługi Power BI, aby dowiedzieć się, których terminy są używane najczęściej, oraz te, które zwracają zera dokumenty. Dzięki te szczegółowe informacje, sprawdź, czy słownika do uwzględnienia synonimy nieproduktywne zapytań, które powinny być rozpoznawania do dokumentów w indeksie.
