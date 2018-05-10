---
title: Konfigurowanie topologii połączone fabryki | Dokumentacja firmy Microsoft
description: Jak skonfigurować topologię akcelerator rozwiązań fabryka połączenia.
services: iot-suite
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 4230914c6fb35201a8c162e2e7ecb31262d2bdca
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="configure-the-connected-factory-solution-accelerator"></a>Skonfiguruj akcelerator rozwiązań połączone fabryki

Akcelerator rozwiązań połączone fabryki pokazuje symulowane pulpitu nawigacyjnego dla fikcyjnej firmy Contoso. Ta firma dysponuje globalnie fabryki w wielu lokalizacjach globalnego.

W tym artykule używa Contoso jako przykład do opisano, jak skonfigurować topologię rozwiązania fabryka połączenia.

## <a name="simulated-factories-configuration"></a>Symulowane fabryki konfiguracji

Każdej fabryki Contoso ma produkcji wierszy, które składają się z trzech stacji. Każda stacja jest serwerem OPC UA rzeczywistych z określoną rolę:

* Zestaw stacji
* Test stacji
* Pakowania

Te serwery OPC Agent użytkownika mają OPC UA węzłów i [wydawcy OPC](https://github.com/Azure/iot-edge-opc-publisher) wysyła wartości te węzły do fabryki połączony. Obejmuje to:

* Bieżący stan operacyjny takie jak bieżące zużycie energii.
* Utworzone produkcji informacje, takie jak liczba produktów.

Aby przejść do szczegółów w topologii fabryki Contoso z widoku globalnego do widoku poziomu stacji można pulpit nawigacyjny. Fabryka połączenia pulpitu nawigacyjnego umożliwia:

* Wizualizacja wartości OEE i kluczowych wskaźników wydajności dla poszczególnych warstw w topologii.
* Wizualizacja bieżące wartości OPC UA węzłów w stacji.
* Agregacja OEE i KPI dane z poziomu stacji na poziomie globalnym.
* Wizualizacja alertów i akcje do wykonania, jeśli określone progi osiągnięcia wartości.

## <a name="connected-factory-topology"></a>Połączone topologii fabryki

Topologia fabryki, linii produkcyjnych i stacji jest hierarchiczne:

* Globalny poziom ma fabryki węzłów jako elementy podrzędne.
* Fabryk ma węzły wiersza produkcyjnym jako elementy podrzędne.
* Wiersze produkcyjnego ma węzły stacji jako elementy podrzędne.
* Stacje (serwery OPC UA) ma węzły OPC UA jako elementy podrzędne.

Każdy węzeł w topologii ma wspólny zbiór właściwości, które definiują:

* Unikatowy identyfikator węzła topologii.
* Nazwa.
* Opis.
* Obraz.
* Element podrzędny węzła topologii.
* Docelowej, wartości minimalna i maksymalna obrazków OEE oraz kluczowych wskaźników wydajności i alertów akcje do wykonania.

## <a name="topology-configuration-file"></a>Plik konfiguracji topologii

Aby skonfigurować właściwości opisanych w poprzedniej sekcji, rozwiązanie połączone fabryki używa pliku konfiguracji o nazwie [ContosoTopologyDescription.json](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json).

Możesz znaleźć tego pliku kodu źródłowego rozwiązania w `WebApp/Contoso/Topology` folderu.

Poniższy fragment kodu przedstawia zarys `ContosoTopologyDescription.json` pliku konfiguracji:

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

Wspólne właściwości `<global_configuration>`, `<factory_configuration>`, `<production_line_configuration>`, i `<station_configuration>` są:

* **Nazwa** (wpisz ciąg)

  Określa nazwę opisową, która ma być tylko jednego węzła topologii do wyświetlenia na pulpicie nawigacyjnym.

* **Opis elementu** (wpisz ciąg)

  Opisuje węzeł topologii bardziej szczegółowo.

* **Obraz** (wpisz ciąg)

  Ścieżka do obrazu w rozwiązanie aplikacji sieci Web do wyświetlenia, gdy wyświetlane są informacje o węźle topologii na pulpicie nawigacyjnym.

* **OeeOverall**, **OeePerformance**, **OeeAvailability**, **OeeQuality**, **Kpi1**, **Kpi2** (typ `<performance_definition>`)

  Te właściwości zdefiniuj minimalny, docelowy i jego maksymalne wartości operacyjne rysunek służący do generowania alertów. Akcje do wykonania w przypadku wykrycia alertu można również definiować w tych właściwości.

`<factory_configuration>` i `<production_line_configuration>` elementy mają właściwość:

* **Identyfikator GUID** (wpisz ciąg)

  Unikatowy identyfikator węzła topologii.

`<factory_configuration>` ma właściwość:

* **Lokalizacja** (typ `<location_definition>`)

  Określa lokalizację fabryka.

`<station_configuration>` ma właściwości:

* **OpcUri** (wpisz ciąg)

  Ta właściwość musi mieć ustawioną identyfikator URI aplikacji UA OPC OPC UA serwera.
  Ponieważ musi być globalnie unikatowa w specyfikacji OPC UA, ta właściwość jest używana do identyfikacji węzła topologii stacji.

* **OpcNodes**, tablicę OPC UA węzły, które są (typ `<opc_node_description>`)

`<location_definition>` ma właściwości:

* **Miasto** (wpisz ciąg)

  Nazwa miejscowości najbliżej miejsca

* **Kraj** (wpisz ciąg)

  Kraj lokalizacji

* **Szerokość geograficzna** (typ double)

  Szerokość lokalizacji

* **Długość geograficzna** (typ double)

  Długość geograficzna lokalizacji

`<performance_definition>` ma właściwości:

* **Co najmniej** (typ double)

  Dolny próg wartość może nawiązać połączenie. Jeśli bieżąca wartość jest poniżej wartości progowej, generowany jest alert.

* **Docelowy** (typ double)

  Wartość docelowa idealne.

* **Maksymalna** (typ double)

  Górny próg wartość może nawiązać połączenie. Jeśli bieżąca wartość jest powyżej wartości progowej, generowany jest alert.

* **MinimumAlertActions** (typ `<alert_action>`)

  Definiuje zestaw akcji, które można podjąć w odpowiedzi na alert minimalnej.

* **MaximumAlertActions** (typ `<alert_action>`)

  Definiuje zestaw akcji, które można podjąć w odpowiedzi na alert maksymalna.

`<alert_action`> ma właściwości:

* **Typ** (wpisz ciąg)

  Typ akcji alertu. Znane są następujące:

  * **AcknowledgeAlert**: stan alertu należy zmienić do potwierdzonego.
  * **CloseAlert**: wszystkie alerty starsze tego samego typu już nie powinny być wyświetlane na pulpicie nawigacyjnym.
  * **CallOpcMethod**: należy wywoływać metody OPC UA.
  * **OpenWebPage**: oknie przeglądarki powinien zostać otwarty wyświetlanie dodatkowych informacji kontekstowych.

* **Opis elementu** (wpisz ciąg)

  Opis akcji pokazywane na pulpicie nawigacyjnym.

* **Parametr** (wpisz ciąg)

  Parametrów wymaganych do wykonania akcji. Wartość zależy od typu akcji.

  * **AcknowledgeAlert**: Brak wymaganego parametru.
  * **CloseAlert**: Brak wymaganego parametru.
  * **CallOpcMethod**: węzeł informacji i parametry OPC UA metody do wywołania w formacie "ID. węzła węzła nadrzędnego, ID. węzła metody do wywołania, identyfikator URI serwera OPC UA."
  * **OpenWebPage**: adres URL do wyświetlenia w oknie przeglądarki.

`<opc_node_description>` zawiera informacje o węzłach OPC UA w stacji (OPC UA server). Węzły reprezentują żadnych istniejących węzłów OPC UA, które są używane jako magazyn logiki obliczeń połączone fabryki również są prawidłowe. Ma następujące właściwości:

* **ID. węzła** (wpisz ciąg)

  Adres UA OPC przestrzeni adresowej węzła w stacji (OPC UA serwera). Składnia musi być określone w specyfikacji OPC UA ID. węzła.

* **SymbolicName** (wpisz ciąg)

  Nazwa ma być wyświetlany na pulpicie nawigacyjnym, jeśli wartość tego węzła OPC UA jest wyświetlana.

* **Znaczenie** (Tablica typu ciąg)

  Wskazuje, dla których ma zastosowanie obliczeń OEE lub wskaźnik KPI OPC UA wartość węzła. Każdy element tablicy może być jedną z następujących wartości:

  * **OeeAvailability_Running**: wartość ma zastosowanie do obliczania dostępności OEE.
  * **OeeAvailability_Fault**: wartość ma zastosowanie do obliczania dostępności OEE.
  * **OeePerformance_Ideal**: wartość ma zastosowanie w przypadku obliczania OEE wydajności i zwykle jest wartością stałą.
  * **OeePerformance_Actual**: wartość ma zastosowanie w przypadku obliczania OEE wydajności.
  * **OeeQuality_Good**: wartość ma zastosowanie w przypadku obliczania OEE jakości.
  * **OeeQuality_Bad**: wartość ma zastosowanie w przypadku obliczania OEE jakości.
  * **Kpi1**: wartość jest odpowiedni do obliczeń KPI1.
  * **Kpi2**: wartość jest odpowiedni do obliczeń KPI2.

* **Kod operacji** (wpisz ciąg)

  Wskazuje sposób obsługi wartość węzła OPC UA w zapytaniach szczegółowe informacje o czasie serii i obliczeń OEE/kluczowego wskaźnika wydajności. Obiekt timespan, który jest to parametr zapytania, a następnie dostarcza wynik jest przeznaczony dla każdego zapytania szczegółowe informacje o czasie serii. Kod operacji kontroluje sposób wyniku jest obliczany i może być jedną z następujących wartości:

  * **Diff**: różnica między ostatniego i pierwsza wartość w zakres czasu.
  * **Średni**: średnią wszystkich wartości timespan.
  * **Suma**: sumę wszystkich wartości timespan.
  * **Ostatni**: obecnie nieużywane.
  * **Liczba**: liczba wartości timespan.
  * **Maksymalna liczba**: maksymalna wartość timespan.
  * **Min**: minimalnego wartość timespan.
  * **Const**: wynik jest wartością określoną przez właściwość ConstValue.
  * **SubMaxMin**: różnica między maksymalnie i minimalnej wartości.
  * **Wartość TimeSpan**: zakres czasu.

* **Jednostki** (wpisz ciąg)

  Definiuje jednostkę wartości do wyświetlenia na pulpicie nawigacyjnym.

* **Widoczne** (wpisz wartość logiczna)

  Kontroluje, czy wartość powinna być pokazywane na pulpicie nawigacyjnym.

* **ConstValue** (typ double)

  Jeśli **OpCode** jest **Const**, a następnie ta właściwość jest wartość węzła.

* **Co najmniej** (typ double)

  Jeśli bieżąca wartość spada poniżej tej wartości, minimalna alert zostanie wygenerowany.

* **Maksymalna** (typ double)

  Jeśli bieżąca wartość zgłasza powyżej tej wartości, generowany jest alert maksymalną.

* **MinimumAlertActions** (typ `<alert_action>`)

  Definiuje zestaw akcji, które można podjąć w odpowiedzi na alert minimalnej.

* **MaximumAlertActions** (typ `<alert_action>`)

  Definiuje zestaw akcji, które można podjąć w odpowiedzi na alert maksymalna.

Na poziomie stacji, zostanie również wyświetlony **symulacji** obiektów. Te obiekty służą tylko do konfigurowania symulacji połączone fabryki i nie należy używać do konfigurowania topologii prawdziwe.

## <a name="how-the-configuration-data-is-used-at-runtime"></a>Jak dane konfiguracji są używane w czasie wykonywania

Wszystkie właściwości, które są używane w pliku konfiguracji można grupować w różne kategorie, w zależności od sposobu ich używania. Dostępne są następujące kategorie:

### <a name="visual-appearance"></a>Wygląd

Właściwości w tej kategorii definiują wygląd fabryka połączenia pulpitu nawigacyjnego. Przykłady:

* Name (Nazwa)
* Opis
* Image (Obraz)
* Lokalizacja
* Jednostki
* Widoczne

### <a name="internal-topology-tree-addressing"></a>Wewnętrzny topologii drzewa adresowania

Aplikacja sieci Web przechowuje słownika danych wewnętrznych, zawierającego informacje o wszystkich węzłów topologii. Właściwości **Guid** i **OpcUri** są używane jako klucze można uzyskać dostępu do tego słownika i muszą być unikatowe.

### <a name="oeekpi-computation"></a>Obliczenia OEE/wskaźnika KPI

Rysunki OEE/KPI symulacji połączone fabryki mają zdefiniowane przez:

* OPC UA węzła wartości, które mają zostać uwzględnione w obliczeniach.
* Jak wartość jest obliczana na podstawie wartości telemetrii.

Fabryka połączonych używa formuły OEE publikowanych przez http://oeeindustrystandard.oeefoundation.org.

OPC UA obiektów węzła na stacjach Włącz znakowanie do użycia w obliczeniach OEE/kluczowego wskaźnika wydajności. **Istotność** właściwość wskazuje, dla których rysunek OEE/KPI wartość węzła OPC UA powinny być używane. **OpCode** właściwość definiuje sposób wartość jest uwzględniona w obliczeniach.

### <a name="alert-handling"></a>Obsługa alertów

Fabryka połączenia obsługuje mechanizm proste minimum i maksimum oparte na wartościach progowych Generowanie alertów. Istnieje wiele wstępnie zdefiniowanych działań, które można skonfigurować w odpowiedzi na te alerty. To mechanizmy kontroli następujące właściwości:

* Maksimum
* Minimalne
* MaximumAlertActions
* MinimumAlertActions

## <a name="correlating-to-telemetry-data"></a>Dopasowywanie do danych telemetrii

Dla niektórych operacji, takich jak wizualizacja ostatnią wartość lub tworzenie zapytań szczegółowe informacje o czasie serii aplikacji sieci Web wymaga schematu adresowania danych telemetrycznych pozyskiwane. Telemetrii wysyłane do fabryki połączony musi być przechowywany w strukturach danych wewnętrznych. Dwie właściwości włączenie tych operacji są na poziomie węzła OPC UA i stacji (serwer OPC UA):

* **OpcUri**

  Identyfikuje serwer OPC UA dane telemetryczne (unikatowych) pochodzą z. W komunikatach pozyskiwane, ta właściwość jest wysyłany jako **ApplicationUri**.

* **ID. węzła**

  Określa wartość węzłów na serwerze OPC UA. Format właściwości musi być określone w specyfikacji OPC UA. W komunikatach pozyskiwane, ta właściwość jest wysyłany jako **ID. węzła**.

Sprawdź [to](https://github.com/Azure/iot-edge-opc-publisher) stronie GitHub uzyskać więcej informacji na temat sposobu dane telemetryczne pozyskanych jest fabryką połączone za pomocą wydawcy OPC.

## <a name="example-how-kpi1-is-calculated"></a>Przykład: w jaki sposób jest obliczany KPI1

Konfiguracja w `ContosoTopologyDescription.json` plików kontroluje sposób obliczania wartości OEE/kluczowego wskaźnika wydajności. W poniższym przykładzie pokazano, jak właściwości w tym pliku formantu obliczenia KPI1.

W KPI1 fabryka połączenia służy do mierzenia liczby produkty pomyślnie przetworzone w ciągu ostatniej godziny. Każda stacja (OPC UA serwera) w symulacji połączone fabryki zawiera węzeł OPC UA (`NodeId: "ns=2;i=385"`), która zawiera dane telemetryczne do obliczenia tego wskaźnika KPI.

Konfiguracja dla tego węzła OPC UA wygląda następujący fragment kodu:

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

Ta konfiguracja umożliwia wyszukiwanie wartości telemetrii tego węzła przy użyciu usługi Insights serii czasu. Pobiera zapytanie Insights serii czasu:

* Liczba wartości.
* Minimalnej wartości.
* Wartość maksymalna.
* Średnią wszystkich wartości.
* Sumę wszystkich wartości dla wszystkich unikatowych **OpcUri** (**ApplicationUri**), **ID. węzła** pary w danym timespan.

Jedną z cech **NumberOfManufactureredProducts** wartość węzła jest fakt, że tylko zwiększa. Aby obliczyć liczbę produktów wytworzonych w zakres czasu, korzysta z połączenia fabryki **OpCode** **SubMaxMin**. Obliczenie pobiera wartość minimalna na początku zakres czasu i maksymalną wartość na koniec timespan.

**OpCode** w konfiguracji konfiguruje logiki obliczeń do obliczania wyniku różnicy maksymalne i minimalne wartości. Wyniki te są następnie zebranych elementów bottom do poziomu głównego (globalne) i wyświetlane na pulpicie nawigacyjnym.

## <a name="next-steps"></a>Kolejne kroki

A sugerowane, następnym krokiem jest, aby dowiedzieć się, jak [wdrożyć bramę Windows lub Linux dla akcelerator rozwiązań połączone fabryki](iot-suite-connected-factory-gateway-deployment.md).