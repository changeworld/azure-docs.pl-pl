---
title: Opis cyfrowego bliźniaczych reprezentacji obiektów modeli i wykres analizy przestrzennej | Dokumentacja firmy Microsoft
description: Za pomocą Twins cyfrowych platformy Azure do modelowania relacji między osoby, miejsca i urządzenia
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: c1d66e0b58567244f8c1406ee258c9311994ff20
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2018
ms.locfileid: "50215110"
---
# <a name="understanding-digital-twins-object-models-and-spatial-intelligence-graph"></a>Opis modeli obiektów cyfrowego bliźniaczych reprezentacji i analizy przestrzennej wykresu

Twins cyfrowych platformy Azure jest usługą Azure IoT, zapewniająca kompleksową wirtualnego reprezentacje środowisk fizycznych i skojarzonych urządzeń, czujników i osoby. Zwiększa modeli pomocny, które są także znajduje się w wykres analizy przestrzennej rozwoju przez organizowanie pojęć specyficznych dla domeny. Takie pojęcia model poważaniem, relacje i interakcje między osoby, miejsca do magazynowania i urządzenia.

_Cyfrowy modele obiektów Twins_ opisano pojęcia specyficznego dla domeny, kategorii i właściwości. Modele są wstępnie zdefiniowane przez użytkowników, którzy chcą dopasować rozwiązanie do ich własnych potrzeb. Razem te wstępnie zdefiniowane modele obiektów cyfrowego Twins tworzą _Ontology_. Inteligentne tworzenie ontology co opisywałoby, regiony, miejsc, podłóg, oddziałów, strefy, salach konferencyjnych i pokoje zespołu. Ontology siatki energii co opisywałoby, różne elektrownie podstacje, zużycie zasobów i klientów. Te cyfrowe modele obiektów bliźniaczych reprezentacji i ontologie umożliwiają dostosowywanie Twins cyfrowych platformy Azure do potrzeb i różnych scenariuszy.

Za pomocą _cyfrowego modele obiektów bliźniaczych reprezentacji_ i _Ontology_ w miejscu, jeden wypełnić _przestrzenne wykresu_. Wykresy przestrzenne są wirtualnych reprezentujących wiele relacji między miejsca do magazynowania, urządzeń i osób, które dotyczą rozwiązania IoT. Na poniższym diagramie przedstawiono przykład przestrzenne grafu za pomocą ontology inteligentne tworzenie.

![Cyfrowy bliźniaczych elementów przestrzennych Tworzenie wykresu][1]

<a id="model"></a>

Wykres przestrzenne łączy miejsca do magazynowania, urządzeń, czujników i użytkowników. Każdy połączony w taki sposób, że modele rzeczywistych: miejsc 43 ma cztery podłóg, każdy z wielu różnych obszarów. Użytkownicy są skojarzone z ich stacjach roboczych i mających dostęp do części wykresu.  Na przykład administrator musi uprawnień do wprowadzania zmian w przestrzenne programu graph, gdy użytkownik może mieć jedynie uprawnienia do wyświetlania niektórych danych tworzenia.

## <a name="digital-twins-object-models"></a>Cyfrowy modele obiektów bliźniaczych reprezentacji

Cyfrowy modele obiektów Twins obsługują następujące główne kategorie obiektów:

- **Miejsca do magazynowania** są lokalizacji wirtualnych lub fizycznych, na przykład `Tenant`, `Customer`, `Region`, `Venue`.
- **Urządzenia** są części wirtualnych lub fizycznych urządzeń, na przykład `AwesomeCompany Device`, `Raspberry Pi 3`.
- **Czujniki** to obiekty, które wykrywa zdarzenia, na przykład `AwesomeCompany Temperature Sensor`, `AwesomeCompany Presence Sensor`.
- **Użytkownicy** identyfikowanie użytkowników i ich właściwości.

Inne kategorie obiektów są:

- **Zasoby** są dołączone do miejsca i zazwyczaj reprezentują zasoby platformy Azure, który będzie używany przez obiekty na wykresie przestrzennych, na przykład `IoTHub`.
- **Obiekty BLOB** są dołączone do obiektów (takich jak spacje, urządzeń, czujników i użytkowników) i używany jako pliki z typu mime oraz metadane, na przykład `maps`, `pictures`, `manuals`.
- **Rozszerzone typy** są rozszerzalne wyliczenia, które polepszają jednostki o konkretnych cechach, na przykład `SpaceType`, `SpaceSubtype`.
- **Ontologie** reprezentują zestaw rozszerzonych typów, na przykład `Default`, `Building`, `BACnet`, `EnergyGrid`.
- **Właściwości kluczy i wartości** charakterystyk niestandardowego miejsca do magazynowania, urządzeń, czujników i użytkowników. Mogą być używane poza wbudowanej właściwości, na przykład `DeltaProcessingRefreshTime` jako klucz i `10` jako wartość.
- **Role** zestawów uprawnień przypisanych do użytkowników i urządzeń w przestrzenne wykres, na przykład `Space Administrator`, `User Administrator`, `Device Administrator`.
- **Przypisania ról** to skojarzenie między roli oraz obiektu przestrzennego grafu, na przykład udzielania użytkownika lub uprawnienia podmiotu zabezpieczeń usługi, zarządzania miejscem na wykresie przestrzennych.
- **Magazyny klucz zabezpieczeń** dostarczania kluczy zabezpieczeń dla wszystkich urządzeń w hierarchii w obszarze obiektu danego miejsca, aby umożliwić urządzenia do bezpiecznego komunikowania się z usługą cyfrowego bliźniaczych reprezentacji.
- **— Funkcje zdefiniowane przez użytkownika** lub **UDF** Zezwalaj na telemetrię można dostosowywać czujnik przetwarzania przestrzennych wykresu. Na przykład funkcji zdefiniowanej przez użytkownika można ustawić wartość czujnik, wykonać logikę niestandardową, w oparciu o odczyty czujników ustawić dane wyjściowe do miejsca, dołączanie metadanych do miejsca i wysyłać powiadomienia, gdy wstępnie zdefiniowane warunki są spełnione. Obecnie można pisać w języku JavaScript funkcji zdefiniowanych przez użytkownika.
- **Dopasowujące jednostki** są obiekty, które określają, które funkcje zdefiniowane przez użytkownika, które zostaną wykonane telemetrii danego komunikatu.
- **Punkty końcowe** lokalizacji, gdzie komunikaty telemetryczne i Twins cyfrowego zdarzenia te można skierować, na przykład `Event Hub`, `Service Bus`, `Event Grid`.

<a id="graph"></a>

## <a name="spatial-intelligence-graph"></a>Wykres analizy przestrzennej

**Wykres przestrzenne** jest wykres hierarchiczny miejsca do magazynowania, urządzeń, i ludzie zdefiniowane w **cyfrowego bliźniaczych reprezentacji modelu obiektów**. Przestrzenne programu graph obsługuje _dziedziczenia_, _filtrowanie_, _przechodzenie_, _skalowalność_, i _rozszerzalności_ . Użytkownicy mogą zarządzać i interakcję z ich przestrzenne wykres z to zbiór interfejsów API REST (zobacz poniżej).

Użytkownik, który wdraża usługi cyfrowego bliźniaczych reprezentacji w ramach swojej subskrypcji staje się administratorem globalnym węzeł główny automatycznie przyznanie pełnego dostępu do całej struktury. Ten użytkownik może następnie aprowizować miejsca do magazynowania w programie graph przy użyciu interfejsu API miejsca. Urządzenia może być aprowizowane przy użyciu interfejsu API urządzenia, czujniki może być obsługiwana za pomocą interfejsu API z czujników itp. Oferujemy również [narzędzia open source](https://github.com/Azure-Samples/digital-twins-samples-csharp) do aprowizowania na wykresie zbiorczo.

Wykres _dziedziczenia_ odnosi się do uprawnień i właściwości, które jest elementem podrzędnym elementu węzła nadrzędnego na wszystkie węzły znajdujące się poniżej. Na przykład gdy rola jest przypisywana do użytkowników w danym węźle, użytkownik będzie miał uprawnienia tej roli do Podany węzeł i każdy węzeł poniżej. Ponadto każdego właściwość klucza i rozszerzone typu zdefiniowane dla danego węzła będą dziedziczone przez wszystkie węzły znajdujące się poniżej tego węzła.

Wykres _filtrowanie_ umożliwia użytkownikom zawęzić wyniki żądania według identyfikatorów, nazwy, podtypy, klucze właściwości, miejsce nadrzędnego, typy danych czujników, typów, skojarzone miejsca do magazynowania i wartości, *przechodzenie*,  *minLevel*, *maxLevel*i inne parametry filtru OData.

Wykres _przechodzenie_ pozwala użytkownikom na przechodzenie przestrzenne programu graph za pośrednictwem jej wiedzy i doświadczeniu. Dla głębokości, wykres może te typy można przemierzać góry do dołu lub od dołu do góry przy użyciu parametrów nawigacji *przechodzenie*, *minLevel*, *maxLevel*. Dla zakresu wykres może nastąpi przejście, uzyskać dostęp do węzłów równorzędnych podłączone bezpośrednio do miejsca nadrzędnej lub jeden z jego obiektów podrzędnych. Podczas wykonywania zapytań względem obiektu, można pobrać wszystkich powiązanych obiektów, które mają relacje z tego obiektu przy użyciu *obejmuje* parametru Pobierz interfejsów API.

Azure Twins cyfrowego gwarantuje wykres _skalowalność_, więc może obsługiwać swoje obciążenia w rzeczywistych warunkach. Cyfrowy Twins może służyć do reprezentowania dużych zestawów nieruchomości, infrastruktury, urządzeń, czujników, dane telemetryczne i więcej.

Wykres _rozszerzalności_ umożliwia użytkownikom dostosowywanie podstawowych modeli obiektów cyfrowego bliźniaczych reprezentacji w nowe typy i ontologie. Dane usługi cyfrowego twins mogą zostać również wzbogacone extensible właściwości i wartości.

### <a name="spatial-intelligence-graph-management-apis"></a>Wykres analizy przestrzennej interfejsów API zarządzania

Po wdrożeniu Twins cyfrowych platformy Azure z [witryny Azure portal](https://portal.azure.com), [Swagger](https://swagger.io/tools/swagger-ui/) URL interfejsów API Management jest generowany automatycznie i będzie wyświetlany w witrynie Azure portal **—Omówienie** sekcji w następującym formacie:

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management/swagger
```

| Nazwa atrybutu niestandardowego | Zamień |
| --- | --- |
| *yourInstanceName* | Nazwa wystąpienia usługi Azure cyfrowego bliźniaczych reprezentacji |
| *yourLocation* | Który region serwer wystąpienie usługi jest hostowana na |

 Pełny format adresu URL są widoczne używany na poniższej ilustracji:

![Interfejs API zarządzania portalu cyfrowego bliźniaczych reprezentacji][2]

Aby uzyskać więcej informacji na temat korzystania z przestrzennego wykresy analizy odwiedź stronę rzucić okiem cyfrowego Twins zarządzania interfejsów API usługi Azure.

> [!TIP]
> Rzucić okiem struktury Swagger jest dostarczany, aby zademonstrować tę funkcję interfejsu API zestawu.
> Jest ona hostowana na [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

Dowiedz się więcej o [sposób używania struktury Swagger](how-to-use-swagger.md).

Wszystkie wywołania interfejsu API musi zostać uwierzytelniony przy użyciu [OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code). Postępuj zgodnie z interfejsami API [konwencje wytycznych interfejsu API REST firmy Microsoft](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). Większość interfejsów API, które zwracają kolekcje obsługuje [OData](http://www.odata.org/getting-started/basic-tutorial/#queryData) Opcje zapytań systemu.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, łączność urządzeń oraz jak wysyłać komunikaty telemetryczne do usługi Azure cyfrowego bliźniaczych reprezentacji, przeczytaj [Azure cyfrowego bliźniaczych reprezentacji urządzeń łączności i dane telemetryczne ruch przychodzący](concepts-device-ingress.md).

Aby dowiedzieć się więcej o ograniczeniach interfejsu API zarządzania i ograniczenia, przeczytaj [Zarządzanie interfejsu API usługi Azure cyfrowego bliźniaczych reprezentacji i ograniczenia](concepts-service-limits.md).

<!-- Images -->
[1]: media/concepts/digital-twins-spatial-graph-building.png
[2]: media/concepts/digital-twins-spatial-graph-management-api-url.png
