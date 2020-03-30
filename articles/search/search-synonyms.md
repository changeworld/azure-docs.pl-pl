---
title: Synonimy rozszerzania kwerend w indeksie wyszukiwania
titleSuffix: Azure Cognitive Search
description: Utwórz mapę synonimów, aby rozszerzyć zakres zapytania wyszukiwania w indeksie usługi Azure Cognitive Search. Zakres jest rozszerzany o równoważne terminy, które podajesz na liście.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: aa573e84fa9fff83bd6a894f516ce5f67b3afa79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194346"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Synonimy w usłudze Azure Cognitive Search

Synonimy w wyszukiwarkach kojarzą równoważne terminy, które niejawnie rozszerzają zakres zapytania, bez konieczności podawania tego terminu przez użytkownika. Na przykład, biorąc pod uwagę termin "pies" i synonimy skojarzeń "psów" i "szczeniak", wszelkie dokumenty zawierające "pies", "psi" lub "szczeniak" będzie wchodzić w zakres zapytania.

W usłudze Azure Cognitive Search rozszerzenie synonimu odbywa się w czasie kwerendy. Mapy synonimów można dodać do usługi bez zakłóceń w istniejących operacjach. Można dodać **właściwość synonimMaps** do definicji pola bez konieczności przebudowy indeksu.

## <a name="create-synonyms"></a>Tworzenie synonimów

Nie ma obsługi portalu do tworzenia synonimów, ale można użyć interfejsu API REST lub .NET SDK. Aby rozpocząć pracę z REST, zalecamy [użycie listonosza](search-get-started-postman.md) i sformułowanie żądań za pomocą tego interfejsu API: [Tworzenie map synonimów.](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map) Dla deweloperów języka C# można rozpocząć z [Dodawanie synonimów w usłudze Azure Cognitive Searching przy użyciu języka C#](search-synonyms-tutorial-sdk.md).

Opcjonalnie, jeśli używasz [kluczy zarządzanych](search-security-manage-encryption-keys.md) przez klienta do szyfrowania po stronie usługi w spoczynku, można zastosować tę ochronę do zawartości mapy synonimów.

## <a name="use-synonyms"></a>Używanie synonimów

W usłudze Azure Cognitive Search obsługa synonimów jest oparta na mapach synonimów, które definiujesz i przekazujesz do usługi. Te mapy stanowią niezależny zasób (np. indeksy lub źródła danych) i mogą być używane przez dowolne pole z wyszukujalne w dowolnym indeksie w usłudze wyszukiwania.

Mapy synonimów i indeksy są utrzymywane niezależnie. Po zdefiniowaniu mapy synonimów i przekazaniu jej do usługi można włączyć funkcję synonimu w polu, dodając nową właściwość o nazwie **synonimMapy** w definicji pola. Tworzenie, aktualizowanie i usuwanie mapy synonimów jest zawsze operacją całego dokumentu, co oznacza, że nie można stopniowo tworzyć, aktualizować ani usuwać części mapy synonimu. Aktualizowanie nawet pojedynczego wpisu wymaga ponownego załadowania.

Włączenie synonimów do aplikacji wyszukiwania jest procesem dwuetapowym:

1.  Dodaj mapę synonimów do usługi wyszukiwania za pośrednictwem poniższych interfejsów API.  

2.  Skonfiguruj pole z wyszukuj, aby używało mapy synonimów w definicji indeksu.

Można utworzyć wiele map synonimów dla aplikacji wyszukiwania (na przykład według języka, jeśli aplikacja obsługuje wielojęzyczną bazę klientów). Obecnie pole może używać tylko jednego z nich. Właściwość synonimMapy pola można zaktualizować w dowolnym momencie.

### <a name="synonymmaps-resource-apis"></a>Interfejsy API zasobów SynonymMaps

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>Dodaj lub zaktualizuj mapę synonimów w ramach usługi, używając POST lub PUT.

Mapy synonimów są przesyłane do usługi za pośrednictwem POST lub PUT. Każda reguła musi być rozdzielona przez nowy znak wiersza ('\n'). Można zdefiniować maksymalnie 5000 reguł na mapę synonimów w bezpłatnej usłudze i 20 000 reguł na mapę we wszystkich innych jednostkach SKU. Każda reguła może mieć maksymalnie 20 rozszerzeń.

Mapy synonimów muszą być w formacie Apache Solr, który jest wyjaśniony poniżej. Jeśli masz istniejący słownik synonimów w innym formacie i chcesz go używać bezpośrednio, poinformuj nas o tym na [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

Nową mapę synonimów można utworzyć za pomocą protokołu HTTP POST, tak jak w poniższym przykładzie:

    POST https://[servicename].search.windows.net/synonymmaps?api-version=2019-05-06
    api-key: [admin key]

    {
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

Alternatywnie można użyć PUT i określić nazwę mapy synonimu w identyfikatorze URI. Jeśli mapa synonimów nie istnieje, zostanie utworzona.

    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

    {
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

##### <a name="apache-solr-synonym-format"></a>Format synonimu Apache Solr

Format Solr obsługuje równoważne i jawne mapowania synonimów. Reguły mapowania są zgodne ze specyfikacją filtra synonimów open source apache solr, opisaną w tym dokumencie: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). Poniżej znajduje się przykładowa reguła dla równoważnych synonimów.
```
USA, United States, United States of America
```

Zgodnie z powyższą regułą zapytanie "USA" zostanie rozszerzone na "USA" LUB "Stany Zjednoczone" LUB "Stany Zjednoczone Ameryki".

Jawne mapowanie jest oznaczane strzałką "=>". Po określeniu sekwencja terminów kwerendy wyszukiwania, która pasuje do lewej strony "=>" zostanie zastąpiona alternatywami po prawej stronie. Biorąc pod uwagę poniższą regułę, zapytania wyszukiwania "Waszyngton", "Umyć". lub "WA" zostaną przepisane na "WA". Jawne mapowanie ma zastosowanie tylko w określonym kierunku i nie przepisuje kwerendy "WA" do "Washington" w tym przypadku.
```
Washington, Wash., WA => WA
```

#### <a name="list-synonym-maps-under-your-service"></a>Lista map synonimów w ramach usługi.

    GET https://[servicename].search.windows.net/synonymmaps?api-version=2019-05-06
    api-key: [admin key]

#### <a name="get-a-synonym-map-under-your-service"></a>Pobierz mapę synonimów w ramach swojej usługi.

    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

#### <a name="delete-a-synonyms-map-under-your-service"></a>Usuń mapę synonimów w ramach usługi.

    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>Skonfiguruj pole z wyszukuj, aby używało mapy synonimów w definicji indeksu.

Nowa właściwość pola **synonimMapy** może służyć do określania mapy synonimów do użycia dla pola z wyszukuj. Mapy synonimów są zasobami na poziomie usług i mogą się odwoływać do dowolnego pola indeksu w ramach usługi.

    POST https://[servicename].search.windows.net/indexes?api-version=2019-05-06
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

**synonimMapy** można określić dla pól z wyszukujników typu "Edm.String" lub "Collection(Edm.String)".

> [!NOTE]
> Na pole można mieć tylko jedną mapę synonimów. Jeśli chcesz korzystać z wielu map synonimów, poinformuj nas o tym na [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="impact-of-synonyms-on-other-search-features"></a>Wpływ synonimów na inne funkcje wyszukiwania

Funkcja synonimów przepisuje oryginalną kwerendę z synonimami z operatorem OR. Z tego powodu profile wyróżniania i oceniania trafienia traktują oryginalny termin i synonimy jako równoważne.

Funkcja synonimu ma zastosowanie do zapytań wyszukiwania i nie ma zastosowania do filtrów lub aspektów. Podobnie sugestie są oparte tylko na pierwotnym terminie; dopasowania synonimów nie są wyświetlane w odpowiedzi.

Rozszerzenia synonimów nie mają zastosowania do wyszukiwanych haseł z symbolami wieloznacznych; prefiks, rozmyte i wyrażenia regularne nie są rozwinięte.

Jeśli musisz wykonać pojedynczą kwerendę, która stosuje rozszerzenie synonimów i wieloznaczne, wyrażenia regularne lub wyszukiwania rozmyte, można połączyć kwerendy przy użyciu składni OR. Na przykład, aby połączyć synonimy z symbolami wieloznaczowymi dla `<query> | <query>*`składni prostych zapytań, terminem będzie .

Jeśli masz istniejący indeks w środowisku deweloperskim (nieprodukcyjnym), poeksperymentuj z małym słownikiem, aby zobaczyć, jak dodanie synonimów zmienia środowisko wyszukiwania, w tym wpływ na profile oceniania, wyróżnianie trafień i sugestie.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie mapy synonimów](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)