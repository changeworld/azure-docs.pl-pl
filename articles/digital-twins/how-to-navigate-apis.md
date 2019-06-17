---
title: Przejdź interfejsów API Azure Twins cyfrowych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak typowych wzorców zapytań interfejsów API zarządzania Twins cyfrowych platformy Azure.
author: dsk-2015
manager: philmea
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 1/7/2019
ms.author: dkshir
ms.openlocfilehash: 9cfcdc879e36b93e21bff6f91886536d799553bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60924498"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>Jak używać interfejsów API zarządzania Twins cyfrowych platformy Azure

Interfejsy API umożliwiające zarządzanie Twins cyfrowych platformy Azure udostępniają zaawansowane funkcje dla aplikacji IoT. W tym artykule pokazano, jak przechodzić przez strukturę interfejsu API.  

## <a name="api-summary"></a>Podsumowanie interfejsu API

Na poniższej liście przedstawiono składniki cyfrowego interfejsów API bliźniaczych reprezentacji.

* [/ spacje](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces): Te interfejsy API wchodzić w interakcje z lokalizacji fizycznych w ustawieniach. Te ułatwiają tworzenie, usuwanie i zarządzanie cyfrowego mapowania sieci lokalizacji fizycznych w formie [przestrzenne wykresu](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph).

* [/Devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices): Te interfejsy API wchodzić w interakcje z urządzeniami w ustawieniach. Te urządzenia mogą zarządzać co najmniej jeden czujników. Na przykład urządzenie może być telefonu, pod czujników urządzenia Raspberry Pi lub bramy lora utworzonej i tak dalej.

* [/Sensors](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors): Te interfejsy API ułatwiają komunikowania się z czujników skojarzonych z urządzeń i sieci lokalizacji fizycznych. Czujników, Zapisz i Wyślij otoczenia wartości, które następnie mogą być używane do manipulowania środowiska przestrzennych.  

* [/Resources](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources): Te interfejsy API ułatwiają konfigurowanie zasobów, takich jak usługi IoT hub, wystąpienia Twins cyfrowych.

* [/ typów](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types): Te interfejsy API umożliwiają skojarzenie rozszerzone typy z obiektów reprezentacje urządzeń cyfrowych, można dodać szczególne cechy do tych obiektów. Te typy umożliwiają łatwe filtrowanie i grupowanie obiektów w Interfejsie użytkownika i funkcje niestandardowe, które przetwarzają dane telemetryczne. Przykłady typów rozszerzonej *DeviceType*, *SensorType*, *SensorDataType*, *SpaceType*, *SpaceSubType* , *SpaceBlobType*, *SpaceResourceType*i tak dalej.

* [/ontologies](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies): Te interfejsy API ułatwiają zarządzanie ontologie, które są kolekcjami typów rozszerzonej. Ontologie podać nazwy dla typów obiektów, zgodnie z przestrzeni fizycznej, które reprezentują. Na przykład *BACnet* ontology zapewnia określonych nazw *typy czujnika*, *typy danych*, *datasubtypes*i *dataunittypes*. Ontologie zarządzanych i utworzone przez usługę. Użytkownikom można załadować i rozładować ontologie. Po załadowaniu ontology są wszystkie jego nazwy skojarzonych typów, włączona i gotowa do udostępnienia w grafie przestrzennych. 

* [/propertyKeys](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys): Te interfejsy API umożliwia tworzenie niestandardowych właściwości usługi *miejsca do magazynowania*, *urządzeń*, *użytkowników*, i *czujników*. Te właściwości są tworzone jako pary klucz/wartość. Można zdefiniować typ danych dla tych właściwości, ustawiając ich *PrimitiveDataType*. Na przykład można zdefiniować właściwości o nazwie *BasicTemperatureDeltaProcessingRefreshTime* typu *uint* czujniki, a następnie przypisz wartości dla tej właściwości dla każdego z czujników. Ograniczenia te wartości można również dodać podczas tworzenia właściwości, takie jak *Min* i *Max* zakresów adresów, a także dozwolonych wartości *ValidationData*.

* [/matchers](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers): Te interfejsy API umożliwiają określenie warunków, które chcesz oceniać przychodzące dane urządzenie. Zobacz [w tym artykule](concepts-user-defined-functions.md#matchers) Aby uzyskać więcej informacji. 

* [/userDefinedFunctions](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions): Te interfejsy API umożliwiają tworzenie, delete lub update warunki niestandardowe funkcja, która będzie wykonania, gdy zdefiniowana przez *dopasowujące jednostki* występują do przetwarzania danych pochodzących z konfiguracji. Zobacz [w tym artykule](concepts-user-defined-functions.md#user-defined-functions) Aby uzyskać więcej informacji na temat tych funkcji niestandardowych, nazywany również *funkcje zdefiniowane przez użytkownika*. 

* [/Endpoints](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints): Te interfejsy API umożliwiają tworzenie punktów końcowych, dzięki czemu rozwiązania cyfrowe Twins może komunikować się z innymi usługami platformy Azure do przechowywania danych i analiz. Odczyt [w tym artykule](concepts-events-routing.md) Aby uzyskać więcej informacji. 

* [/keyStores](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores): Te interfejsy API umożliwiają zarządzanie magazynami kluczy zabezpieczeń dla Twojego miejsca do magazynowania. Te magazyny można przechowywać Kolekcja kluczy zabezpieczeń i umożliwiają łatwe pobieranie najnowszych klucze.

* [/ Użytkownicy](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users): Te interfejsy API umożliwiają tworzenie skojarzeń użytkowników z Twojego miejsca do magazynowania, aby zlokalizować te osoby, gdy jest to wymagane. 

* [/system](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System): Te interfejsy API umożliwiają zarządzanie ustawienia systemowe, takie jak domyślne typy miejsc do magazynowania i czujniki. 

* [/roleAssignments](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments): Te interfejsy API umożliwiają skojarzenie ról z jednostki, takie jak identyfikator użytkownika, identyfikator funkcji zdefiniowanej przez użytkownika, itp. Każdy przypisania roli zawiera identyfikator obiektu do skojarzenia typu jednostki, identyfikator roli do skojarzenia, identyfikator dzierżawy i ścieżkę, która definiuje górny limit liczby zasobów, których jednostki może uzyskać dostęp za pomocą tego skojarzenia. Odczyt [w tym artykule](security-role-based-access-control.md) Aby uzyskać więcej informacji.


## <a name="api-navigation"></a>Interfejs API nawigacji

Cyfrowy interfejsów API Twins obsługuje filtrowanie i nawigacja w całym wykresie przestrzennych, korzystając z następujących parametrów:

- **spaceId**: Interfejs API będzie filtrować wyniki według identyfikatora danego miejsca. Ponadto flagę logiczną **useParentSpace** dotyczy [/spacje](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces) interfejsów API, co oznacza, że identyfikator danej obszaru odwołuje się do miejsca nadrzędnego, zamiast bieżącego miejsca. 

- **minLevel** i **maxLevel**: Główny spacje są uznawane za na poziomie 1. Miejsca do magazynowania z elementu nadrzędnego miejsce na poziomie *n* znajdują się na poziomie *n + 1*. Przy użyciu zestawu tych wartości można filtrować wyniki na określonym poziomie. Są to wartości włącznie po ustawieniu. W tym samym poziomie co ich najbliższe miejsca są traktowane jako urządzeń, czujników i innych obiektów. Aby uzyskać wszystkie obiekty na danym poziomie, ustaw obie **minLevel** i **maxLevel** taką samą wartość.

- **minRelative** i **maxRelative**: Gdy te filtry są podane, odpowiedniego poziomu jest względem poziomu identyfikator danego obszaru:
   - Poziom względną *0* jest tym samym poziomie jak identyfikator danego miejsca.
   - Poziom względną *1* reprezentuje miejsca do magazynowania na tym samym poziomie jako element podrzędny danego miejsca identyfikatora. Poziom względną *n* reprezentuje niższe niż określona przestrzeń przez miejsca do magazynowania *n* poziomów.
   - Poziom względną *-1* reprezentuje miejsca do magazynowania na tym samym poziomie miejsca nadrzędny określonego miejsca.

- **Przechodzenie przez**: Umożliwia przechodzenie w dowolnym kierunku z Identyfikatorem danego miejsca określony przez następujące wartości.
   - **Brak**: Ta wartość domyślna filtrów identyfikator danego miejsca.
   - **Dół**: Filtruje według Identyfikatora danego miejsca i jego elementy podrzędne. 
   - **Się**: Filtruje według Identyfikatora danego miejsca i jego elementów nadrzędnych. 
   - **Zakres**: Filtruje poziomą część przestrzenne programu graph na tym samym poziomie jak identyfikator danego miejsca. Musi on **minRelative** lub **maxRelative** należy ustawić wartość true. 


### <a name="examples"></a>Przykłady

Na poniższej liście przedstawiono kilka przykładów nawigację [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices) interfejsów API. Należy pamiętać, że symbol zastępczy `YOUR_MANAGEMENT_API_URL` odwołuje się do identyfikatora URI cyfrowego interfejsów API bliźniaczych reprezentacji w formacie `https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/api/v1.0/`, gdzie `YOUR_INSTANCE_NAME` to nazwa wystąpienia usługi Azure cyfrowego bliźniaczych reprezentacji i `YOUR_LOCATION` jest region, w którym znajduje się wystąpienie usługi.

- `YOUR_MANAGEMENT_API_URL/devices?maxLevel=1` Zwraca wszystkie urządzenia podłączone do głównego miejsca do magazynowania.
- `YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4` Zwraca wszystkie urządzenia dołączone do miejsca do magazynowania poziomów 2, 3 lub 4.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId` Zwraca wszystkie urządzenia podłączone bezpośrednio do mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down` Zwraca wszystkie urządzenia podłączone do mySpaceId lub jeden z jego obiektów podrzędnych.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true` Zwraca wszystkie urządzenia podłączone do elementów podrzędnych mySpaceId, z wyłączeniem mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true` Zwraca wszystkie urządzenia podłączone do bezpośrednie elementy podrzędne mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Up&maxLevel=-1&maxRelative=true` Zwraca wszystkie urządzenia podłączone do jednego z elementów nadrzędnych mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&maxLevel=5` Zwraca wszystkie urządzenia podłączone do obiektów podrzędnych mySpaceId, znajdujących się na poziomie mniejsze niż lub równy 5.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true` Zwraca wszystkie urządzenia dołączone do miejsca do magazynowania, które znajdują się na tym samym poziomie co mySpaceId.


## <a name="odata-support"></a>Obsługa protokołu OData
Większość interfejsów API, które zwracają kolekcje, takie jak wywołanie GET /spaces, obsługuje podzbiór następujących ogólnych [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) Opcje zapytań systemu:  

* **$filter**
* **$orderby** 
* **$top**
* **$skip** — Jeśli chcesz wyświetlić całą kolekcję powinien request całego zestawu w pojedynczym wywołaniu, a następnie wykonaj stronicowania w aplikacji. 

Należy pamiętać, że inne opcje zapytań, takich jak $count, $rozwiń $search nie są obsługiwane.

### <a name="examples"></a>Przykłady

Na poniższej liście przedstawiono kilka przykładów zapytań przy użyciu opcji zapytania systemu OData firmy:

- `YOUR_MANAGEMENT_API_URL/devices?$top=3&$orderby=Name desc`
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,’space’)`
- `YOUR_MANAGEMENT_API_URL/propertykeys?$filter=Scope ne ‘Spaces’`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=Size gt ‘M’`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,’k’)&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor’,Name)`
 

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać niektóre typowe wzorce zapytań interfejsu API, przeczytaj [jak wykonywać zapytania interfejsów API Twins cyfrowych platformy Azure do wykonywania typowych zadań](how-to-query-common-apis.md).


