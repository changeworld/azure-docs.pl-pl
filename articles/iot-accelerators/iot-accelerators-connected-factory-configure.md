---
title: Konfigurowanie topologii połączonej fabryki — Platforma Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób konfigurowania akceleratora rozwiązań połączonej fabryki wraz z jego topologią.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 5fa3d4d4fdfa0dd81cd8ab8772ffb3903dda289f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820130"
---
# <a name="configure-the-connected-factory-solution-accelerator"></a>Konfigurowanie akceleratora rozwiązań Connected Factory

Akcelerator rozwiązań Połączonej fabryki pokazuje symulowany pulpit nawigacyjny fikcyjnej firmy Contoso. Firma ta posiada fabryki w wielu globalnych lokalizacjach na całym świecie.

W tym artykule używa contoso jako przykład, aby opisać, jak skonfigurować topologię rozwiązania połączonej fabryki.

## <a name="simulated-factories-configuration"></a>Konfiguracja symulowanych fabryk

Każda fabryka Contoso ma linie produkcyjne, które składają się z trzech stacji. Każda stacja jest prawdziwym serwerem OPC UA o określonej roli:

* Stacja montażowa
* Stacja testowa
* Stacja pakowania

Te serwery OPC UA mają węzły OPC UA, a [program OPC Publisher](overview-opc-publisher.md) wysyła wartości tych węzłów do połączonej fabryki. Obejmuje to:

* Aktualny stan pracy, taki jak aktualne zużycie energii.
* Informacje o produkcji, takie jak liczba wyprodukowanych produktów.

Pulpit nawigacyjny służy do przechodzenia do szczegółów topologii fabrycznej contoso z widoku globalnego w dół do widoku na poziomie stacji. Pulpit nawigacyjny Connected Factory umożliwia:

* Wizualizacja figurek OEE i KPI dla każdej warstwy w topologii.
* Wizualizacja bieżących wartości węzłów OPC UA w stacjach.
* Agregacja danych OEE i KPI z poziomu stacji do poziomu globalnego.
* Wizualizacja alertów i akcji do wykonania, jeśli wartości osiągną określone progi.

## <a name="connected-factory-topology"></a>Topologia połączonej fabryki

Topologia fabryk, linii produkcyjnych i stacji jest hierarchiczna:

* Poziom globalny ma węzły fabryczne jako dzieci.
* Fabryki mają węzły linii produkcyjnej jako dzieci.
* Linie produkcyjne mają węzły stacji jako dzieci.
* Stacje (serwery OPC UA) mają węzły OPC UA jako dzieci.

Każdy węzeł w topologii ma wspólny zestaw właściwości, które definiują:

* Unikatowy identyfikator węzła topologii.
* Nazwa.
* Opis.
* Obraz.
* Koreła węzła topologii.
* Wartości minimalne, docelowe i maksymalne dla danych OEE i KPI oraz akcji alertów do wykonania.

## <a name="topology-configuration-file"></a>Plik konfiguracji topologii

Aby skonfigurować właściwości wymienione w poprzedniej sekcji, rozwiązanie Połączonej fabryki używa pliku konfiguracyjnego o nazwie [ContosoTopologyDescription.json](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json).

Ten plik można znaleźć w kodzie `WebApp/Contoso/Topology` źródłowym rozwiązania w folderze.

Poniższy fragment kodu przedstawia konspekt pliku konfiguracyjnego: `ContosoTopologyDescription.json`

```json
{
  <global_configuration>,
  "Factories": [
    <factory_configuration>,
    "ProductionLines": [
      <production_line_configuration>,
      "Stations": [
        <station_configuration>,
        <more station_configurations>
      ],
      <more production_line_configurations>
    ]
    <more factory_configurations>
  ]
}
```

Wspólne właściwości `<global_configuration>`, `<factory_configuration>`, `<production_line_configuration>`i `<station_configuration>` są:

* **Nazwa** (ciąg typu)

  Definiuje opisową nazwę, która powinna być tylko jednym słowem dla węzła topologii, który ma być pokazywalny na pulpicie nawigacyjnym.

* **Opis** (ciąg tekstowy)

  W tym artykule opisano węzeł topologii bardziej szczegółowo.

* **Obraz** (ciąg tekstowy)

  Ścieżka do obrazu w rozwiązaniu WebApp, aby pokazać, gdy informacje o węźle topologii są wyświetlane na pulpicie nawigacyjnym.

* **OeeOverall**, **OeePerformance**, **OeeAvailability**, **OeeQuality**, **Kpi1**, **Kpi2** (typ `<performance_definition>`)

  Właściwości te definiują wartości minimalne, docelowe i maksymalne wartości liczby operacyjnej używanej do generowania alertów. Te właściwości również zdefiniować akcje do wykonania, jeśli alert zostanie wykryty.

I `<factory_configuration>` `<production_line_configuration>` elementy mają właściwość:

* **Guid** (ciąg tekstowy)

  Jednoznacznie identyfikuje węzeł topologii.

`<factory_configuration>`posiada nieruchomość:

* **Lokalizacja** (typ) `<location_definition>`

  Określa, gdzie znajduje się fabryka.

`<station_configuration>`posiada właściwości:

* **OpcUri** (ciąg typu)

  Ta właściwość musi być ustawiona na identyfikator URI aplikacji OPC UA serwera OPC UA.
  Ponieważ musi być unikatowa globalnie przez specyfikację OPC UA, ta właściwość jest używana do identyfikowania węzła topologii stacji.

* **OpcNodes**, które są tablicą węzłów OPC UA (typ) `<opc_node_description>`

`<location_definition>`posiada właściwości:

* **Miasto** (ciąg tekstowy)

  Nazwa miasta najbliższego lokalizacji

* **Kraj** (ciąg tekstowy)

  Kraj lokalizacji

* **Szerokość geograficzna** (typ podwójny)

  Szerokość geograficzna lokalizacji

* **Długość geograficzna** (typ podwójny)

  Długość geograficzna lokalizacji

`<performance_definition>`posiada właściwości:

* **Minimum** (typ podwójny)

  Niższy próg wartość może osiągnąć. Jeśli bieżąca wartość jest poniżej tego progu, generowany jest alert.

* **Cel** (typ podwójny)

  Idealna wartość docelowa.

* **Maksymalna** (typ podwójny)

  Górny próg, który może osiągnąć wartość. Jeśli bieżąca wartość jest powyżej tego progu, generowany jest alert.

* **MinimumAlertActions** (typ) `<alert_action>`

  Definiuje zestaw akcji, które można podjąć jako odpowiedź na alert minimalny.

* **MaximumAlertActions** (typ) `<alert_action>`

  Definiuje zestaw akcji, które można podjąć jako odpowiedź na maksymalny alert.

`<alert_action`> ma właściwości:

* **Typ** (ciąg tekstowy)

  Typ akcji alertu. Znane są następujące typy:

  * **AcknowledgeAlert**: stan wpisu powinien ulec zmianie na potwierdzony.
  * **CloseAlert**: wszystkie starsze alerty tego samego typu nie powinny być już wyświetlane na pulpicie nawigacyjnym.
  * **CallOpcMethod**: należy wywołać metodę OPC UA.
  * **OpenWeb**: należy otworzyć okno przeglądarki z dodatkowymi informacjami kontekstowymi.

* **Opis** (ciąg tekstowy)

  Opis akcji wyświetlanej na pulpicie nawigacyjnym.

* **Parametr** (ciąg typu)

  Parametry wymagane do wykonania akcji. Wartość zależy od typu akcji.

  * **AcknowledgeAlert**: nie jest wymagany parametr.
  * **CloseAlert**: nie jest wymagany parametr.
  * **CallOpcMethod**: informacje o węźle i parametry metody OPC UA do wywołania w formacie "NodeId węzła nadrzędnego, NodeId metody do wywołania, identyfikator URI serwera OPC UA."
  * **OpenWeb**: adres URL do wyświetlenia w oknie przeglądarki.

`<opc_node_description>`zawiera informacje o węzłach OPC UA w stacji (serwer OPC UA). Węzły, które reprezentują żadnych istniejących węzłów OPC UA, ale są używane jako magazyn w logice obliczeń connected factory są również prawidłowe. Posiada następujące właściwości:

* **Identyfikator węzła** (ciąg typu)

  Adres węzła OPC UA w przestrzeni adresowej stacji (serwera OPC UA). Składnia musi być określona w specyfikacji OPC UA dla identyfikatora węzła.

* **SymbolicName** (ciąg tekstowy)

  Nazwa, która ma być wyświetlana na pulpicie nawigacyjnym, gdy wyświetlana jest wartość tego węzła UA OPC.

* **Adekwatność** (tablica ciągu typu)

  Wskazuje, dla których obliczeń OEE lub KPI wartość węzła OPC UA jest istotna. Każdy element tablicy może być jedną z następujących wartości:

  * **OeeAvailability_Running**: wartość jest istotna dla obliczenia dostępności OEE.
  * **OeeAvailability_Fault**: wartość jest istotna dla obliczenia dostępności OEE.
  * **OeePerformance_Ideal**: wartość jest istotna dla obliczania wydajności OEE i jest zazwyczaj wartością stałą.
  * **OeePerformance_Actual**: wartość jest istotna dla obliczenia wydajności OEE.
  * **OeeQuality_Good:** wartość jest istotna dla obliczenia jakości OEE.
  * **OeeQuality_Bad:** wartość jest istotna dla obliczenia jakości OEE.
  * **Kpi1**: wartość jest istotna dla obliczenia kpi1.
  * **Kpi2**: wartość jest istotna dla obliczenia kpi2.

* **OpCode** (ciąg typu)

  Wskazuje sposób obsługi wartości węzła OPC UA w kwerendach usługi Time Series Insight i obliczeniach OEE/KPI. Każde zapytanie wglądu w czas serii jest przeznaczone dla określonego czasu, który jest parametrem kwerendy i zapewnia wynik. OpCode kontroluje sposób obliczania wyniku i może być jedną z następujących wartości:

  * **Różnica**: różnica między ostatnią i pierwszą wartością w przedziale czasowym.
  * **Średnia**: średnia wszystkich wartości w rozpiętości czasowej.
  * **Suma**: suma wszystkich wartości w rozpiętości czasowej.
  * **Ostatni**: obecnie nie jest używany.
  * **Count**: liczba wartości w rozliczeniu czasu.
  * **Max**: maksymalna wartość w rozpiętości czasowej.
  * **Min**: minimalna wartość w timespan.
  * **Const**: wynik jest wartością określoną przez właściwość ConstValue.
  * **SubMaxMin**: różnica między wartością maksymalną a minimalną.
  * **Timespan**: timespan.

* **Jednostki** (ciąg typu)

  Definiuje jednostkę wartości wyświetlania na pulpicie nawigacyjnym.

* **Widoczne** (typ logiczny)

  Określa, czy wartość powinna być wyświetlana na pulpicie nawigacyjnym.

* **ConstValue** (typ podwójny)

  Jeśli **OpCode** jest **Const**, a następnie ta właściwość jest wartości węzła.

* **Minimum** (typ podwójny)

  Jeśli bieżąca wartość spadnie poniżej tej wartości, zostanie wygenerowany alert minimalny.

* **Maksymalna** (typ podwójny)

  Jeśli bieżąca wartość podnosi powyżej tej wartości, a następnie generowany jest alert maksymalny.

* **MinimumAlertActions** (typ) `<alert_action>`

  Definiuje zestaw akcji, które można podjąć jako odpowiedź na alert minimalny.

* **MaximumAlertActions** (typ) `<alert_action>`

  Definiuje zestaw akcji, które można podjąć jako odpowiedź na maksymalny alert.

Na poziomie stacji są również widoczne obiekty **Symulacja.** Obiekty te są używane tylko do konfigurowania symulacji połączonej fabryki i nie powinny być używane do konfigurowania prawdziwej topologii.

## <a name="how-the-configuration-data-is-used-at-runtime"></a>Sposób wykorzystania danych konfiguracyjnych w czasie wykonywania

Wszystkie właściwości używane w pliku konfiguracyjnym można podzielić na różne kategorie w zależności od sposobu ich użycia. Kategorie te to:

### <a name="visual-appearance"></a>Wygląd

Właściwości w tej kategorii definiują wygląd pulpitu nawigacyjnego Połączonej fabryki. Przykłady:

* Nazwa
* Opis
* Image (Obraz)
* Lokalizacja
* Jednostki
* Widoczne

### <a name="internal-topology-tree-addressing"></a>Adresowanie drzewa topologii wewnętrznej

WebApp przechowuje wewnętrzny słownik danych zawierający informacje o wszystkich węzłach topologii. Właściwości **Guid** i **OpcUri** są używane jako klucze, aby uzyskać dostęp do tego słownika i muszą być unikatowe.

### <a name="oeekpi-computation"></a>Obliczenia OEE/KPI

Dane OEE/KPI dla symulacji połączonej fabryki są parametryzowane przez:

* Wartości węzła OPC UA, które mają zostać uwzględnione w obliczeniach.
* Sposób obliczania figury na podstawie wartości telemetrycznych.

Connected Factory używa formuł OEE opublikowanych [http://www.oeefoundation.org](http://www.oeefoundation.org)przez plik .

Obiekty węzłów OPC UA w stacjach umożliwiają tagowanie do użycia w obliczeniach OEE/KPI. **Właściwość właściwość** wskazuje, dla których OEE/KPI rysunek wartość węzła OPC UA należy użyć. **Właściwość OpCode** definiuje sposób uwzględniania wartości w obliczeniach.

### <a name="alert-handling"></a>Obsługa alertów

Connected Factory obsługuje prosty mechanizm generowania alertów oparty na minimalnym/maksymalnym progu. Istnieje wiele wstępnie zdefiniowanych akcji, które można skonfigurować w odpowiedzi na te alerty. Mechanizm ten steruje następującymi właściwościami:

* Maksimum
* Minimalne
* Maksymalna aakcje
* MinimumAlertActions (Minimalna liczba reakcji minimalnych)

## <a name="correlating-to-telemetry-data"></a>Korelowanie danych telemetrycznych

W przypadku niektórych operacji, takich jak wizualizacja ostatniej wartości lub tworzenie zapytań usługi Time Series Insight, aplikacja WebApp potrzebuje schematu adresowania dla pozyskanych danych telemetrycznych. Dane telemetryczne wysyłane do połączonej fabryki również muszą być przechowywane w wewnętrznych strukturach danych. Dwie właściwości włączające te operacje znajdują się na poziomie station (OPC UA server) i OPC UA:

* **OpcUri ( opcuri )**

  Identyfikuje (globalnie unikatowy) serwer OPC UA, z na który pochodzi dane telemetryczne. W pochłoniętych wiadomościach ta właściwość jest wysyłana jako **ApplicationUri**.

* **Identyfikator węzła**

  Identyfikuje wartość węzła na serwerze OPC UA. Format właściwości musi być określony w specyfikacji OPC UA. W ynażonych komunikatów ta właściwość jest wysyłana jako **NodeId**.

Zobacz [Co to jest Wydawca OPC, aby](overview-opc-publisher.md) uzyskać więcej informacji na temat sposobu pozyskiwania danych telemetrycznych do połączonej fabryki.

## <a name="example-how-kpi1-is-calculated"></a>Przykład: Sposób obliczania wskaźnika KPI1

Konfiguracja w `ContosoTopologyDescription.json` pliku określa sposób obliczania danych OEE/KPI. W poniższym przykładzie pokazano, jak właściwości w tym pliku kontrolują obliczenia kpi1.

W connected factory KPI1 służy do pomiaru liczby pomyślnie wyprodukowanych produktów w ciągu ostatniej godziny. Każda stacja (serwer OPC UA) w symulacji Połączonej`NodeId: "ns=2;i=385"`fabryki udostępnia węzeł OPC UA ( ), który udostępnia dane telemetryczne do obliczenia tego kluczowego wskaźnika wydajności.

Konfiguracja tego węzła OPC UA wygląda następująco:

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

Ta konfiguracja umożliwia wykonywanie zapytań o wartości telemetryczne tego węzła przy użyciu usługi Time Series Insights. Kwerenda usługi Time Series Insights pobiera:

* Liczba wartości.
* Minimalna wartość.
* Maksymalna wartość.
* Średnia wszystkich wartości.
* Suma wszystkich wartości dla wszystkich unikatowych **par OpcUri** (**ApplicationUri**), **NodeId** w danym czasie.

Jedną z cech **NumberOfManufactureredProducts** wartość węzła jest to, że tylko zwiększa. Aby obliczyć liczbę produktów wyprodukowanych w rozpiętości czasowej, connected factory używa **OpCode** **SubMaxMin**. Obliczenie pobiera wartość minimalną na początku timespan i maksymalną wartość na końcu timespan.

**OpCode** w konfiguracji konfiguruje logikę obliczeń, aby obliczyć wynik różnicy wartości maksymalnej i minimalnej. Wyniki te są następnie gromadzone oddolnie do poziomu głównego (globalnego) i wyświetlane na pulpicie nawigacyjnym.

## <a name="next-steps"></a>Następne kroki

Sugerowanym następnym krokiem jest nauczenie się [dostosowywania rozwiązania Połączonej fabryki.](iot-accelerators-connected-factory-customize.md)
