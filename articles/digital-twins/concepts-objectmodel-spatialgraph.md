---
title: Opis modeli obiektów i wykresu analizy przestrzennej — Azure Digital Twins | Dokumenty firmy Microsoft
description: Używanie usługi Azure Digital Twins do modelowania relacji między osobami, miejscami i urządzeniami
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/30/2019
ms.openlocfilehash: cafec321e7c40e27d8de731feda1103451271507
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265209"
---
# <a name="understand-digital-twins-object-models-and-spatial-intelligence-graph"></a>Zrozumienie modeli obiektów cyfrowych bliźniąt i wykresu inteligencji przestrzennej

Usługa Azure Digital Twins to usługa Azure IoT, która umożliwia kompleksowe wirtualne reprezentacje środowisk fizycznych i skojarzonych urządzeń, czujników i osób. Poprawia rozwój, organizując pojęcia specyficzne dla domeny w pomocne modele. Modele są następnie umieszczone na wykresie inteligencji przestrzennej. Takie pojęcia wiernie modelują relacje i interakcje między ludźmi, przestrzeniami i urządzeniami.

Modele obiektów kamicy cyfrowej opisują pojęcia, kategorie i właściwości specyficzne dla domeny. Modele są wstępnie zdefiniowane przez użytkowników, którzy chcą dostosować rozwiązanie do ich specyficznych potrzeb. Razem te wstępnie zdefiniowane modele obiektów Digital Twins tworzą _ontologię._ Ontologia inteligentnego budynku opisuje regiony, lokale, piętra, biura, strefy, sale konferencyjne i sale spotkań. Ontologia sieci energetycznej opisuje różne elektrownie, podstacje, zasoby energetyczne i klientów. Dzięki modelom obiektów i onlogii digital Twins można dostosować różne scenariusze i potrzeby.

Dzięki modelom obiektów Digital Twins i ontologii można wypełnić _wykres przestrzenny._ Wykresy przestrzenne są wirtualnymi reprezentacjami wielu relacji między przestrzeniami, urządzeniami i osobami, które są istotne dla rozwiązania IoT. Ten diagram przedstawia przykład wykresu przestrzennego, który używa ontologii inteligentnego budynku.

[![Cyfrowy Twins przestrzenny budynek wykresu](media/concepts/digital-twins-spatial-graph-building.png)](media/concepts/digital-twins-spatial-graph-building.png#lightbox)

Wykres przestrzenny łączy przestrzenie, urządzenia, czujniki i użytkowników. Każdy z nich jest połączony w sposób, który modeluje rzeczywisty świat. W tym przykładzie, miejsce 43 ma cztery piętra, każdy z wielu różnych obszarów. Użytkownicy są związani ze swoimi stacjami roboczymi i mają dostęp do części wykresu. Administrator ma prawo do wprowadzania zmian na wykresie przestrzennym, podczas gdy użytkownik ma prawa do wyświetlania tylko niektórych danych budynku.

## <a name="digital-twins-object-models"></a>Modele obiektów cyfrowych bliźniąt

Modele obiektów Digital Twins obsługują te główne kategorie obiektów:

- **Spacje** są wirtualnymi lub `Tenant`fizycznymi lokalizacjami, na przykład , `Customer`, `Region`i `Venue`.
- **Urządzenia** są wirtualnymi lub fizycznymi elementami wyposażenia, `AwesomeCompany Device` na przykład i `Raspberry Pi 3`.
- **Czujniki** to obiekty, które wykrywają zdarzenia, `AwesomeCompany Temperature Sensor` na przykład i `AwesomeCompany Presence Sensor`.
- **Użytkownicy** identyfikują pasażerów i ich charakterystykę.

Inne kategorie obiektów to:

- **Zasoby** są dołączone do przestrzeni i zazwyczaj reprezentują zasoby platformy Azure, które mają `IoTHub`być używane przez obiekty na wykresie przestrzennym, na przykład.
- **Obiekty blob** są dołączone do obiektów (takich jak spacje, urządzenia, czujniki i użytkownicy). Są one używane jako pliki z typem mime `maps` `pictures`i `manuals`metadanymi, na przykład , i .
- **Typy rozszerzone** są wyliczeniami rozszerzalnymi, które powiększają jednostki o określone cechy, na przykład `SpaceType` i `SpaceSubtype`.
- **Ontologie** reprezentują zestaw typów rozszerzonych, `Default`na `Building` `BACnet`przykład `EnergyGrid`, , i .
- **Klucze właściwości i wartości** są niestandardowe cechy przestrzeni, urządzeń, czujników i użytkowników. Mogą być używane wraz z wbudowanymi cechami, na przykład `DeltaProcessingRefreshTime` jako klucz i `10` jako wartość.
- **Role** to zestawy uprawnień przypisanych do użytkowników i urządzeń na `Space Administrator` `User Administrator`wykresie `Device Administrator`przestrzennym, na przykład , i .
- **Przypisania ról** są skojarzenia między rolą i obiektu na wykresie przestrzennym. Na przykład użytkownik lub podmiot usługi można udzielić uprawnień do zarządzania przestrzenią na wykresie przestrzennym.
- **Magazyny kluczy zabezpieczeń** zapewniają klucze zabezpieczeń dla wszystkich urządzeń w hierarchii w obszarze danego obiektu spacji, aby umożliwić urządzeniu bezpieczną komunikację z urządzeniami Digital Twins.
- **Funkcje zdefiniowane przez użytkownika** (UDF) umożliwiają konfigurowalne przetwarzanie danych telemetrycznych czujników na wykresie przestrzennym. Na przykład UDF może:
  - Ustaw wartość czujnika.
  - Wykonaj niestandardową logikę na podstawie odczytów czujników i ustaw dane wyjściowe na spację.
  - Dołączanie metadanych do spacji.
  - Wysyłaj powiadomienia, gdy spełnione są wstępnie zdefiniowane warunki. Obecnie pliki UDF można zapisać w języku JavaScript.
- **Matchers** są obiekty, które określają, które pliki UDF są wykonywane dla danego komunikatu telemetryczne.
- **Punkty końcowe** to lokalizacje, w których można kierować wiadomości telemetryczne i zdarzenia Digital Twins, `Event Hub`na przykład , `Service Bus`i `Event Grid`.

## <a name="spatial-intelligence-graph"></a>Wykres analizy przestrzennej

Wykres przestrzenny to hierarchiczny wykres przestrzeni, urządzeń i osób zdefiniowanych w modelu obiektów Cyfrowe bliźniacze. Wykres przestrzenny obsługuje dziedziczenie, filtrowanie, przechodzenie, skalowalność i rozszerzalność. Można zarządzać i wchodzić w interakcje z wykresem przestrzennym za pomocą kolekcji interfejsów API REST.

Jeśli wdrożysz usługę Digital Twins w ramach subskrypcji, staniesz się administratorem globalnym węzła głównego. Następnie automatycznie uzyskasz pełny dostęp do całej struktury. Inicjuj przestrzenie na wykresie przy użyciu interfejsu API przestrzeni. Aprowizuj usługi przy użyciu interfejsu API urządzenia i czujników przy użyciu interfejsu API czujnika. [Narzędzia open source](https://github.com/Azure-Samples/digital-twins-samples-csharp) są również dostępne do aprowizacji wykresu zbiorczo.

**Dziedziczenie wykresu**. Dziedziczenie ma zastosowanie do uprawnień i właściwości, które pochodzą z węzła nadrzędnego do wszystkich węzłów pod nim. Na przykład, gdy rola jest przypisana do użytkownika w danym węźle, użytkownik ma uprawnienia tej roli do danego węzła i każdego węzła pod nim. Każdy klucz właściwości i rozszerzony typ zdefiniowany dla danego węzła są dziedziczone przez wszystkie węzły pod tym węzłem.

**Filtrowanie wykresu**. Filtrowanie służy do zawężenia wyników żądania. Można filtrować według identyfikatorów, nazw, typów, podtypów, przestrzeni nadrzędnej i skojarzonych spacji. Można również filtrować według typów danych czujnika, kluczy właściwości i wartości, *przechodzenia,* *minLevel,* *maxLevel*i innych parametrów filtru OData.

**Przechodzenie przez wykres**. Wykres przestrzenny można przemierzać przez jego głębokość i szerokość. W przypadku głębokości przechodzenie przez wykres z góry na dół lub od dołu w górę za pomocą parametrów *trawers,* *minLevel*i *maxLevel*. Przechodzenie przez wykres, aby uzyskać węzły równorzędne bezpośrednio dołączone do przestrzeni nadrzędnej lub jeden z jego elementów podrzędnych dla szerokości. Podczas kwerendy obiektu, można uzyskać wszystkie powiązane obiekty, które mają relacje z tym obiektem przy użyciu *parametru includes* get interfejsów API.

**Skalowalność wykresu**. Digital Twins gwarantuje skalowalność wykresu, dzięki czemu może obsługiwać rzeczywiste obciążenia. Cyfrowe bliźniaki mogą być używane do reprezentowania dużych portfeli nieruchomości, infrastruktury, urządzeń, czujników, telemetrii i innych.

**Rozszerzalność wykresu**. Użyj rozszerzalności, aby dostosować podstawowe modele obiektów elementów cyfrowych bliźniąt za pomocą nowych typów i ontologii. Dane digital twins mogą być również wzbogacone o rozszerzalne właściwości i wartości.

### <a name="spatial-intelligence-graph-management-apis"></a>Interfejsy API zarządzania danymi API zarządzania analizą przestrzenną

Po wdrożeniu cyfrowych bliźniaczych reprezentacji z [witryny Azure portal](https://portal.azure.com)automatycznie generowany jest adres URL modułu [Swagger](https://swagger.io/tools/swagger-ui/) interfejsów API zarządzania. Jest on wyświetlany w witrynie Azure portal w sekcji **Przegląd** z następującym formatem.

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Nazwa | Zamień na |
| --- | --- |
| YOUR_INSTANCE_NAME | Nazwa używanego wystąpienia usługi Digital Twins |
| YOUR_LOCATION | Region serwera, w którym jest hostowane używane wystąpienie |

 Pełny format adresu URL pojawi się na tym obrazie.

[![Interfejs API zarządzania portalem Digital Twins](media/concepts/digital-twins-spatial-graph-management-api-url.png)](media/concepts/digital-twins-spatial-graph-management-api-url.png#lightbox)

Aby uzyskać więcej informacji na temat korzystania z wykresów analizy przestrzennej, odwiedź zajawkę interfejsów API zarządzania cyfrowymi urządzeniami i urządzeniami cyfrowymi usługi Azure.

> [!TIP]
> Podgląd zajawki Swagger jest dostarczany, aby zademonstrować zestaw funkcji interfejsu API.
> Jest on a gospodarzem [w docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

Dowiedz się więcej o [używaniu funkcji Swagger](how-to-use-swagger.md).

Wszystkie wywołania interfejsu API muszą być uwierzytelnione przy użyciu [OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code). Interfejsy API są zgodne z [konwencjami dotyczącymi interfejsu API rest firmy Microsoft](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). Większość interfejsów API zwracających kolekcje obsługuje opcje zapytań [systemowych OData.](https://www.odata.org/getting-started/basic-tutorial/#queryData)

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o łączności urządzenia i sposobie wysyłania wiadomości telemetrycznych do aplikacji Digital Twins, przeczytaj artykuł [Azure Digital Twins device connectivity and telemetry ingress](concepts-device-ingress.md).

- Aby dowiedzieć się więcej o ograniczeniach i ograniczeniach interfejsu API zarządzania, przeczytaj artykuł [Zarządzanie interfejsami API usługi Azure Digital Twins i ograniczenia](concepts-service-limits.md).
