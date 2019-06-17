---
title: Modele obiektów cyfrowego bliźniaczych reprezentacji i wykres analizy przestrzennej | Dokumentacja firmy Microsoft
description: Modeluj relacje między osobami, miejscami i urządzeniami za pomocą usługi Azure Digital Twins
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: alinast
ms.openlocfilehash: e7efe1a8632643e2a299b6c9a1b1407414deee4b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60925884"
---
# <a name="understand-digital-twins-object-models-and-spatial-intelligence-graph"></a>Modele obiektów cyfrowego bliźniaczych reprezentacji i analizy przestrzennej wykresu

Twins cyfrowych platformy Azure jest usługą Azure IoT, zapewniająca kompleksową wirtualnego reprezentacje środowisk fizycznych i skojarzonych urządzeń, czujników i osoby. Zwiększa rozwoju organizując pojęć specyficznych dla domeny modeli pomocne. Modele następnie znajdują się wykres analizy przestrzennej. Takie pojęcia model poważaniem, relacje i interakcje między osoby, miejsca do magazynowania i urządzenia.

Cyfrowy modele obiektów bliźniaczych reprezentacji opisano pojęcia specyficznego dla domeny, kategorii i właściwości. Modele są wstępnie zdefiniowane przez użytkowników, którzy chcą dopasować rozwiązanie do ich własnych potrzeb. Razem te wstępnie zdefiniowane cyfrowego reprezentacje urządzeń, które tworzą modeli obiektów _ontology_. Ontology inteligentne tworzenie opisuje regionów, miejsc, podłóg, oddziałów, strefy, salach konferencyjnych i pokoje zespołu. Ontology siatki energii w tym artykule opisano różne elektrownie podstacje, zużycie zasobów i klientów. Za pomocą Twins cyfrowego modele obiektów i ontologie różne scenariusze i potrzeb można dostosować.

Za pomocą Twins cyfrowego modele obiektów i ontology w miejscu, które możesz wypełnić _przestrzenne wykresu_. Wykresy przestrzenne są wirtualnych reprezentujących wiele relacji między miejsca do magazynowania, urządzeń i osób, które są istotne dla rozwiązania IoT. Ten diagram przedstawia przykład przestrzenne wykres, który wykorzystuje inteligentne tworzenie ontology.

![Cyfrowy Tworzenie wykresu przestrzenne bliźniaczych reprezentacji][1]

<a id="model"></a>

Wykres przestrzenne łączy miejsca do magazynowania, urządzeń, czujników i użytkowników. Każdy połączony w taki sposób, że modele rzeczywistych. W tym przykładzie miejsc 43 ma cztery podłóg, każdy z wielu różnych obszarów. Użytkownicy są skojarzone z ich stacjach roboczych i umożliwiającej dostęp do części wykresu. Administrator ma uprawnienia do wprowadzania zmian w przestrzenne wykresu, gdy obiekt odwiedzający ma uprawnienia do wyświetlania tylko niektórych danych tworzenia.

## <a name="digital-twins-object-models"></a>Cyfrowy modele obiektów bliźniaczych reprezentacji

Cyfrowy modele obiektów Twins obsługują następujące główne kategorie obiektów:

- **Miejsca do magazynowania** są lokalizacji wirtualnych lub fizycznych, na przykład `Tenant`, `Customer`, `Region`, i `Venue`.
- **Urządzenia** są części wirtualnych lub fizycznych urządzeń, na przykład `AwesomeCompany Device` i `Raspberry Pi 3`.
- **Czujniki** to obiekty, które wykrywa zdarzenia, na przykład `AwesomeCompany Temperature Sensor` i `AwesomeCompany Presence Sensor`.
- **Użytkownicy** identyfikowanie użytkowników i ich właściwości.

Inne kategorie obiektów są:

- **Zasoby** są dołączone do miejsca i zazwyczaj reprezentują zasoby platformy Azure, który będzie używany przez obiekty na wykresie przestrzennych, na przykład `IoTHub`.
- **Obiekty BLOB** są dołączone do obiektów (takich jak spacje, urządzeń, czujników i użytkowników). Służą one jako pliki o typie mime i metadanych, na przykład `maps`, `pictures`, i `manuals`.
- **Rozszerzone typy** są rozszerzalne wyliczenia, które polepszają jednostki o konkretnych cechach, na przykład `SpaceType` i `SpaceSubtype`.
- **Ontologie** reprezentują zestaw rozszerzonych typów, na przykład `Default`, `Building`, `BACnet`, i `EnergyGrid`.
- **Właściwości kluczy i wartości** charakterystyk niestandardowego miejsca do magazynowania, urządzeń, czujników i użytkowników. Można nimi wraz z wbudowanej właściwości, na przykład `DeltaProcessingRefreshTime` jako klucz i `10` jako wartość.
- **Role** zestawów uprawnień przypisanych do użytkowników i urządzeń, na wykresie przestrzennych, na przykład `Space Administrator`, `User Administrator`, i `Device Administrator`.
- **Przypisania ról** to skojarzenie między roli oraz obiektu przestrzennego wykresie. Na przykład użytkownik lub ta jednostka usługi może mieć uprawnienie do zarządzania miejscem na wykresie przestrzennych.
- **Magazyny kluczy zabezpieczeń** dostarczania kluczy zabezpieczeń dla wszystkich urządzeń w hierarchii w obszarze obiektu danego miejsca, aby umożliwić urządzenia do bezpiecznego komunikowania się za pomocą Twins cyfrowych.
- **Funkcje zdefiniowane przez użytkownika** (przez użytkownika UDF) Zezwalaj na telemetrię można dostosowywać czujnik przetwarzania przestrzennych wykresu. Na przykład UDF wykonywać następujące czynności:
  - Ustaw wartość czujnika.
  - Wykonać logikę niestandardową, w oparciu o odczyty czujników, a następnie ustawić dane wyjściowe do miejsca.
  - Dołącz metadanych do miejsca.
  - Wysyłanie powiadomień, gdy wstępnie zdefiniowane warunki są spełnione. Obecnie można pisać funkcje UDF w języku JavaScript.
- **Dopasowujące jednostki** są obiekty, które określają, które funkcje zdefiniowane przez użytkownika są wykonywane dla komunikatów danego telemetrii.
- **Punkty końcowe** lokalizacji, gdzie komunikaty telemetryczne i Twins cyfrowego zdarzenia te można skierować, na przykład `Event Hub`, `Service Bus`, i `Event Grid`.

<a id="graph"></a>

## <a name="spatial-intelligence-graph"></a>Wykres analizy przestrzennej

Przestrzenne wykresu jest wykres hierarchiczny miejsca do magazynowania, urządzenia i osoby zdefiniowanego w modelu obiektu cyfrowego bliźniaczych reprezentacji. Przestrzenne programu graph obsługuje dziedziczenie, filtrowanie, przechodzenie, skalowalność i rozszerzalności. Można zarządzać i interakcję z przestrzennego grafu za pomocą to zbiór interfejsów API REST.

W przypadku wdrożenia usługi cyfrowego reprezentacji urządzeń w Twojej subskrypcji, staje się administratorem globalnym węzła głównego. Następnie są automatycznie udzielony pełny dostęp do całej struktury. Aprowizacja miejsca do magazynowania w programie graph przy użyciu interfejsu API miejsca. Aprowizowanie usługi przy użyciu interfejsu API urządzenia i czujniki przy użyciu interfejsu API czujnika. [Narzędzia open source](https://github.com/Azure-Samples/digital-twins-samples-csharp) są także dostępne do aprowizowania na wykresie zbiorczo.

**Wykres dziedziczenia**. Dziedziczenie odnosi się do uprawnień i właściwości, które jest elementem podrzędnym elementu węzła nadrzędnego na wszystkie węzły znajdujące się poniżej. Na przykład gdy rola jest przypisywana do użytkowników w danym węźle, użytkownik ma uprawnienia tej roli do Podany węzeł i każdy węzeł poniżej. Każdy klucz właściwości i typie rozszerzonym zdefiniowane dla danego węzła jest dziedziczona przez wszystkie węzły znajdujące się poniżej tego węzła.

**Filtrowanie wykresu**. Filtrowanie służy do zawężenia wyników żądania. Można filtrować według identyfikatorów, nazwy, typy, podtypy, miejsce nadrzędnego i skojarzone miejsca do magazynowania. Można również filtrować według typów danych czujników, właściwości kluczy i wartości, *przechodzenie*, *minLevel*, *maxLevel*i inne parametry filtru OData.

**Przechodzenie grafu**. Może przechodzić przestrzenne programu graph za pośrednictwem jej wiedzy i doświadczeniu. Dla głębokości, Graf góry do dołu lub od dołu do góry przy użyciu parametrów *przechodzenie*, *minLevel*, i *maxLevel*. Przejdźmy Graf w celu uzyskać dostęp do węzłów równorzędnych podłączone bezpośrednio do miejsca nadrzędnej lub jeden z jego obiektów podrzędnych dla zakresu. Kiedy wykonujesz zapytanie o obiektu, możesz uzyskać wszystkich powiązanych obiektów, które mają relacje do tego obiektu przy użyciu *obejmuje* parametru Pobierz interfejsów API.

**Wykres skalowalność**. Cyfrowy bliźniaczych reprezentacji gwarantuje skalowalność wykresu, więc może obsługiwać swoje obciążenia w rzeczywistych warunkach. Cyfrowy Twins może służyć do reprezentowania dużych zestawów nieruchomości, infrastruktury, urządzeń, czujników, dane telemetryczne i więcej.

**Wykres rozszerzalności**. Rozszerzalność umożliwia dostosowywanie podstawowych modeli obiektów Twins cyfrowego za pomocą nowych typów i ontologie. Dane Twins cyfrowego mogą również wzbogacone extensible właściwości i wartości.

### <a name="spatial-intelligence-graph-management-apis"></a>Wykres analizy przestrzennej interfejsów API zarządzania

Po wdrożeniu cyfrowego bliźniaczych elementów z [witryny Azure portal](https://portal.azure.com), [Swagger](https://swagger.io/tools/swagger-ui/) URL interfejsów API Management jest generowany automatycznie. Pojawi się w witrynie Azure portal w **Przegląd** sekcji w następującym formacie.

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Name (Nazwa) | Zamień na |
| --- | --- |
| YOUR_INSTANCE_NAME | Nazwa używanego wystąpienia usługi Digital Twins |
| YOUR_LOCATION | Region serwera, w którym jest hostowane używane wystąpienie |

 Pełny format adresu URL pojawia się na tej ilustracji.

![Cyfrowy portal Twins interfejsu API zarządzania][2]

Aby uzyskać więcej informacji na temat korzystania z analizy przestrzennej wykresy można znaleźć rzucić okiem cyfrowego Twins zarządzania interfejsów API usługi Azure.

> [!TIP]
> Rzucić okiem struktury Swagger jest dostarczany, aby zademonstrować tę funkcję interfejsu API zestawu.
> Jest ona hostowana na [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

Dowiedz się więcej o [sposób używania struktury Swagger](how-to-use-swagger.md).

Wszystkie wywołania interfejsu API musi zostać uwierzytelniony przy użyciu [OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code). Postępuj zgodnie z interfejsami API [konwencje wytycznych interfejsu API REST firmy Microsoft](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). Większość interfejsów API, które zwracają kolekcje obsługuje [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) Opcje zapytań systemu.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się, łączność urządzeń i jak wysyłać komunikaty telemetryczne do reprezentacji urządzeń cyfrowych, przeczytaj [Azure cyfrowego bliźniaczych reprezentacji urządzeń łączności i dane telemetryczne ruch przychodzący](concepts-device-ingress.md).

- Aby dowiedzieć się więcej o ograniczeniach interfejsu API zarządzania i ograniczenia, przeczytaj [Zarządzanie interfejsu API usługi Azure cyfrowego bliźniaczych reprezentacji i ograniczenia](concepts-service-limits.md).

<!-- Images -->
[1]: media/concepts/digital-twins-spatial-graph-building.png
[2]: media/concepts/digital-twins-spatial-graph-management-api-url.png
