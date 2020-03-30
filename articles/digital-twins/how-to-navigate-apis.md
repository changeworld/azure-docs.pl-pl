---
title: Poruszanie się po interfejsach API — cyfrowe bliźniacze usługi Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak typowe wzorce wykonywania zapytań o interfejsy API zarządzania usługą Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: e9cdfd40a9672d19ef32dede0baadcdd56266bab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265170"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>Jak korzystać z interfejsów API zarządzania usługą Azure Digital Twins

Interfejsy API zarządzania usługą Azure Digital Twins zapewniają zaawansowane funkcje dla aplikacji IoT. W tym artykule pokazano, jak poruszać się po strukturze interfejsu API.  

## <a name="api-summary"></a>Podsumowanie interfejsu API

Na poniższej liście przedstawiono składniki interfejsów API elementów programu Digital Twins.

* [/spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces): Te interfejsy API współdziałają z fizycznymi lokalizacjami w konfiguracji. Ułatwiają one tworzenie, usuwanie i zarządzanie mapowaniami cyfrowymi lokalizacji fizycznych w formie [wykresu przestrzennego](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph).

* [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices): Te interfejsy API współdziałają z urządzeniami w konfiguracji. Urządzenia te mogą zarządzać co najmniej jednym czujnikiem. Na przykład urządzeniem może być telefon, czujnik Raspberry Pi lub brama Lora itd.

* [/sensors](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors): Te interfejsy API ułatwiają komunikację z czujnikami powiązanymi z urządzeniami i lokalizacjami fizycznymi. Czujniki rejestrują i wysyłają wartości otoczenia, które mogą być następnie używane do manipulowania środowiskiem przestrzennym.  

* [/resources:](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources)Te interfejsy API ułatwiają konfigurowanie zasobów, takich jak centrum IoT, dla wystąpienia cyfrowych bliźniąt.

* [/types:](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types)Te interfejsy API umożliwiają skojarzenie typów rozszerzonych z obiektami Digital Twins, aby dodać określone cechy do tych obiektów. Te typy umożliwiają łatwe filtrowanie i grupowanie obiektów w interfejsie użytkownika i funkcjach niestandardowych, które przetwarzają dane telemetryczne. Przykładami typów rozszerzonych są *DeviceType*, *SensorType*, *SensorDataType*, *SpaceType*, *SpaceSubType*, *SpaceBlobType*, *SpaceResourceType*, i tak dalej.

* [/ontologies:](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies)Te interfejsy API ułatwiają zarządzanie ontologi, które są kolekcje typów rozszerzonych. Ontologie zawierają nazwy typów obiektów, zgodnie z przestrzenią fizyczną, którą reprezentują. Na przykład ontologia *BACnet* zawiera określone nazwy *typów czujników, typów* *danych,* *typów danych*i *jednostek danych.* Ontologie są zarządzane i tworzone przez usługę. Użytkownicy mogą ładować i zwalniać ontologie. Po załadowaniu ontologii wszystkie skojarzone nazwy typów są włączone i gotowe do zainicjowania obsługi administracyjnej na wykresie przestrzennym. 

* [/propertyKeys](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys): Za pomocą tych interfejsów API można tworzyć niestandardowe właściwości *spacji,* *urządzeń,* *użytkowników*i *czujników.* Te właściwości są tworzone jako pary klucz/wartość. Typ danych dla tych właściwości można zdefiniować, ustawiając ich typ danych *pierwotnych*. Na przykład można zdefiniować właściwość o nazwie *BasicTemperatureDeltaProcessingRefreshTime* typu *uint* dla czujników, a następnie przypisać wartość dla tej właściwości dla każdego z czujników. Podczas tworzenia właściwości, takich jak zakresy *Min* i *Max,* można również dodawać ograniczenia dla tych wartości, a także dozwolone wartości jako *ValidationData*.

* [/matchers:](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers)Te interfejsy API umożliwiają określenie warunków, które chcesz ocenić na podstawie przychodzących danych urządzenia. Więcej informacji zawiera [ten artykuł](concepts-user-defined-functions.md#matchers). 

* [/userDefinedFunctions](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions): Te interfejsy API umożliwiają tworzenie, usuwanie lub aktualizowanie funkcji niestandardowej, która będzie wykonywana w przypadku wystąpienia warunków zdefiniowanych przez *matchery,* aby przetwarzać dane pochodzące z konfiguracji. Zobacz [ten artykuł,](concepts-user-defined-functions.md#user-defined-functions) aby uzyskać więcej informacji na temat tych funkcji niestandardowych, nazywanych również *funkcjami zdefiniowanymi przez użytkownika*. 

* [/endpoints:](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints)Te interfejsy API umożliwiają tworzenie punktów końcowych, dzięki czemu rozwiązanie Digital Twins może komunikować się z innymi usługami platformy Azure w celu przechowywania danych i analiz. Przeczytaj [ten artykuł,](concepts-events-routing.md) aby uzyskać więcej informacji. 

* [/keyStores:](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores)Te interfejsy API umożliwiają zarządzanie magazynami kluczy zabezpieczeń dla przestrzeni. Te magazyny mogą zawierać kolekcję kluczy zabezpieczeń i umożliwiają łatwe pobieranie najnowszych prawidłowych kluczy.

* [/users](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users): Te interfejsy API umożliwiają kojarzenie użytkowników z przestrzeniami, aby zlokalizować te osoby, gdy jest to wymagane. 

* [/system](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System): Te interfejsy API umożliwiają zarządzanie ustawieniami całego systemu, takimi jak domyślne typy przestrzeni i czujników. 

* [/roleAssignments:](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments)Te interfejsy API umożliwiają kojarzenie ról z jednostkami, takimi jak identyfikator użytkownika, identyfikator funkcji zdefiniowanej przez użytkownika itp. Każde przypisanie roli zawiera identyfikator jednostki do skojarzenia, typ jednostki, identyfikator roli do skojarzenia, identyfikator dzierżawy i ścieżkę, która definiuje górny limit zasobu, do której jednostka może uzyskać dostęp z tym skojarzeniem. Przeczytaj [ten artykuł,](security-role-based-access-control.md) aby uzyskać więcej informacji.


## <a name="api-navigation"></a>Nawigacja api

Interfejsy API elementów cyfrowych bliźniąt obsługują filtrowanie i nawigację po wykresie przestrzennym przy użyciu następujących parametrów:

- **spaceId**: Interfejs API będzie filtrować wyniki według podanego identyfikatora miejsca. Ponadto flaga logiczna **useParentSpace** ma zastosowanie do interfejsów API [/spaces,](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces) co wskazuje, że podany identyfikator miejsca odnosi się do przestrzeni nadrzędnej zamiast do bieżącego miejsca. 

- **minLevel** i **maxLevel**: Przestrzenie główne są uważane za na poziomie 1. Spacje z przestrzenią nadrzędną na poziomie *n* znajdują się na poziomie *n+1*. Po ustawieniu tych wartości można filtrować wyniki na określonych poziomach. Są to wartości włączające po ustawieniu. Urządzenia, czujniki i inne obiekty są uważane za znajdujące się na tym samym poziomie co ich najbliższa przestrzeń. Aby uzyskać wszystkie obiekty na danym poziomie, należy ustawić zarówno **minLevel** i **maxLevel** do tej samej wartości.

- **minRelative** i **maxRelative**: Gdy te filtry są podane, odpowiedni poziom jest względem poziomu danego identyfikatora miejsca:
   - Względny poziom *0* jest na tym samym poziomie co dany identyfikator miejsca.
   - Względny poziom *1* reprezentuje spacje na tym samym poziomie co podrzędne danego identyfikatora miejsca. Poziom względny *n* reprezentuje spacje niższe niż określone miejsce o *n* poziomy.
   - Względny poziom *-1* reprezentuje spacje na tym samym poziomie co przestrzeń nadrzędna określonego miejsca.

- **traverse**: Umożliwia przechodzenie w obu kierunkach z danego identyfikatora przestrzeni, zgodnie z następującymi wartościami.
   - **Brak:** Ta wartość domyślna jest filtrować do podanego identyfikatora miejsca.
   - **W dół:** To filtruje według danego identyfikatora miejsca i jego elementów podrzędnych. 
   - **W górę:** To filtruje według danego identyfikatora miejsca i jego przodków. 
   - **Zakres:** Filtruje poziomą część wykresu przestrzennego, na tym samym poziomie co dany identyfikator miejsca. Wymaga to **albo minRelative** lub **maxRelative** do ustawionego true. 


### <a name="examples"></a>Przykłady

Na poniższej liście przedstawiono kilka przykładów nawigacji za pośrednictwem interfejsów API [/devices.](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices) Należy zauważyć, `YOUR_MANAGEMENT_API_URL` że symbol zastępczy odwołuje się do identyfikatora `https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/api/v1.0/`URI `YOUR_INSTANCE_NAME` interfejsów API cyfrowych bliźniaczych w formacie , gdzie jest nazwa wystąpienia usługi Azure Digital Twins i `YOUR_LOCATION` jest regionem, w którym znajduje się wystąpienie.

- `YOUR_MANAGEMENT_API_URL/devices?maxLevel=1`zwraca wszystkie urządzenia podłączone do przestrzeni głównych.
- `YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4`zwraca wszystkie urządzenia podłączone do przestrzeni poziomów 2, 3 lub 4.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId`zwraca wszystkie urządzenia podłączone bezpośrednio do mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down`zwraca wszystkie urządzenia podłączone do mySpaceId lub jednego z jego elementów podrzędnych.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true`zwraca wszystkie urządzenia dołączone do elementów podrzędnych mySpaceId, z wyłączeniem mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true`zwraca wszystkie urządzenia podłączone do bezpośrednich dzierzeń mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Up&maxLevel=-1&maxRelative=true`zwraca wszystkie urządzenia podłączone do jednego z przodków mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&maxLevel=5`zwraca wszystkie urządzenia dołączone do elementów podrzędnych mySpaceId, które są na poziomie mniejszym lub równym 5.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true`zwraca wszystkie urządzenia podłączone do spacji, które znajdują się na tym samym poziomie co mySpaceId.


## <a name="odata-support"></a>Obsługa OData

Większość interfejsów API zwracających kolekcje, takie jak wywołanie GET na /spaces, obsługuje następujący podzbiór ogólnych opcji kwerend [systemowych OData:](https://www.odata.org/getting-started/basic-tutorial/#queryData)  

* **$filter**
* **$orderby** 
* **$top**
* **$skip** — jeśli zamierzasz wyświetlić całą kolekcję, należy zażądać go jako cały zestaw w jednym wywołaniu, a następnie wykonać stronicowania w aplikacji. 

> [!NOTE]
> Niektóre opcje OData (takie jak opcje **kwerendy $count** **, $expand**i **$search)** nie są obecnie obsługiwane.

### <a name="examples"></a>Przykłady

Poniższa lista przedstawia kilka zapytań z prawidłową składnią OData:

- `YOUR_MANAGEMENT_API_URL/devices?$top=3&$orderby=Name desc`
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,'space')`
- `YOUR_MANAGEMENT_API_URL/devices?$filter=TypeId eq 2`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=StatusId ne 1`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,'k')&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor',Name)`
 
## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się kilka typowych wzorców zapytań interfejsu API, przeczytaj [artykuł Jak wysyłać zapytania do interfejsów API usługi Azure Digital Twins w przypadku typowych zadań.](./how-to-query-common-apis.md)

Aby dowiedzieć się więcej o punktach końcowych interfejsu API, przeczytaj [artykuł Jak używać funkcji Digital Twins Swagger](./how-to-use-swagger.md).

Aby przejrzeć składnię OData i dostępne operatory porównawcze, przeczytaj [operatory porównania OData w usłudze Azure Cognitive Search](../search/search-query-odata-comparison-operators.md).
