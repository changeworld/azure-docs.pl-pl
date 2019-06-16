---
title: Konfigurowanie topologii połączonej fabryki — Azure | Dokumentacja firmy Microsoft
description: Jak skonfigurować topologię akcelerator rozwiązania połączonej fabryki.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 042277899ff22066cfa890e64f5c6c0f2e0134f9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080470"
---
# <a name="configure-the-connected-factory-solution-accelerator"></a>Konfigurowanie akcelerator rozwiązania połączonej fabryki

Akcelerator rozwiązania połączonej fabryki pokazano symulowane pulpit nawigacyjny dla fikcyjnej firmy Contoso. To firma dysponuje fabryki globalnie w wielu lokalizacjach globalnego.

W tym artykule używa Contoso jako przykład opisujący sposób konfigurowania topologii rozwiązania połączonej fabryki.

## <a name="simulated-factories-configuration"></a>Konfiguracja symulowanej fabryki

Każda fabryka Contoso ma linii produkcyjnych, które składają się z trzech stacji. Każda stacja jest prawdziwy serwer OPC UA z określoną rolą:

* Stanowisku montażowym
* Test stacji
* Pakowania

Te serwery OPC UA ma węzły OPC UA i [wydawca OPC](https://github.com/Azure/iot-edge-opc-publisher) wysyła wartości te węzły do połączonej fabryki. Obejmuje to:

* Bieżący stan operacyjny takie jak bieżące zużycie energii.
* Informacje o produkcji, takie jak liczba produktów utworzone.

Aby przejść do topologii fabryka Contoso z globalny widok w dół do widoku poziomu stacji, można użyć pulpitu nawigacyjnego. Pulpit nawigacyjny połączonej fabryki umożliwia:

* Wizualizacja wartości ogólnej wydajności sprzętu i kluczowych wskaźników wydajności dla każdej warstwy w topologii.
* Wizualizacja bieżące wartości węzły OPC UA na stacjach.
* Agregacja wartości ogólnej wydajności sprzętu i kluczowych wskaźników wydajności z poziomu stacji na poziomie globalnym.
* Wizualizacja i akcje do wykonania, jeśli wartości określone progi alertów.

## <a name="connected-factory-topology"></a>Topologia usługi połączonej fabryki

Topologia fabryki, linii produkcyjnych i stacje są hierarchiczne:

* Globalny poziom ma węzły fabryki jako elementy podrzędne.
* Fabryk mieć węzły linii produkcyjnej jako elementy podrzędne.
* Linii produkcyjnych mieć węzły stacji jako elementy podrzędne.
* Stacje (serwerów OPC UA) ma węzły OPC UA jako elementy podrzędne.

Każdy węzeł w topologii ma wspólny zbiór właściwości, które definiują:

* Unikatowy identyfikator dla węzła topologii.
* Nazwa.
* Opis.
* Obraz.
* Elementy podrzędne węzła topologii.
* Co najmniej docelowego i maksymalne wartości dla wartości ogólnej wydajności sprzętu i kluczowych wskaźników wydajności i alertów akcje do wykonania.

## <a name="topology-configuration-file"></a>Plik konfiguracji topologii

Aby skonfigurować właściwości opisanych w poprzedniej sekcji, rozwiązania połączonej fabryki korzysta z pliku konfiguracji o nazwie [ContosoTopologyDescription.json](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json).

Ten plik można znaleźć w kodzie źródłowym rozwiązania w `WebApp/Contoso/Topology` folderu.

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

Wspólne właściwości `<global_configuration>`, `<factory_configuration>`, `<production_line_configuration>`, i `<station_configuration>` są:

* **Nazwa** (typ string)

  Określa nazwę opisową, która powinna być tylko jednego słowa dla węzła topologii do wyświetlenia na pulpicie nawigacyjnym.

* **Opis** (typ string)

  W tym artykule opisano węzła topologii bardziej szczegółowo.

* **Obraz** (typ string)

  Ścieżka do obrazu w rozwiązaniu aplikacji sieci Web do wyświetlenia, gdy informacje o węźle topologia jest wyświetlany na pulpicie nawigacyjnym.

* **OeeOverall**, **OeePerformance**, **OeeAvailability**, **OeeQuality**, **Kpi1**, **Kpi2** (typ `<performance_definition>`)

  Te właściwości, zdefiniuj minimalny, docelowy i maksymalny wartości operacyjnej rysunek służącego do generowania alertów. Te właściwości również definiują akcje do wykonania w przypadku wykrycia alertu.

`<factory_configuration>` i `<production_line_configuration>` elementy mają właściwości:

* **Identyfikator GUID** (typ string)

  Jednoznacznie identyfikuje węzeł topologii.

`<factory_configuration>` ma właściwość:

* **Lokalizacja** (typ `<location_definition>`)

  Określa, gdzie znajduje się fabryki.

`<station_configuration>` ma właściwości:

* **OpcUri** (typ string)

  Ta właściwość musi być równa OPC UA aplikacji identyfikator URI serwera OPC UA.
  Ponieważ musi być unikatowa w skali globalnej specyfikacji OPC UA, ta właściwość jest używana do identyfikacji węzła topologii stacji.

* **OpcNodes**, służą do tablicy węzły OPC UA (typ `<opc_node_description>`)

`<location_definition>` ma właściwości:

* **Miasto** (typ string)

  Nazwa miasta najbliższą lokalizację

* **Kraj** (typ string)

  Kraj lokalizacji

* **Szerokość geograficzna** (typ double)

  Szerokość geograficzna lokalizacji

* **Długość geograficzna** (typ double)

  Długość geograficzna lokalizacji

`<performance_definition>` ma właściwości:

* **Co najmniej** (typ double)

  Aby osiągnąć niższego progu wartości. Jeśli aktualna wartość jest poniżej tego progu, generowany jest alert.

* **Docelowy** (typ double)

  Wartość docelowa idealnym rozwiązaniem.

* **Maksymalna** (typ double)

  Górny próg wartości można nawiązać połączenia. Jeśli aktualna wartość jest powyżej wartości progowej, generowany jest alert.

* **MinimumAlertActions** (typ `<alert_action>`)

  Definiuje zestaw akcji, które mogą być podejmowane w odpowiedzi na alert w postaci minimalnej.

* **MaximumAlertActions** (typ `<alert_action>`)

  Definiuje zestaw akcji, które mogą być podejmowane w odpowiedzi na alert dotyczący maksymalnej.

`<alert_action`> ma właściwości:

* **Typ** (typ string)

  Typ akcji alertu. Znane są następujące typy:

  * **AcknowledgeAlert**: stan alertu należy zmienić do potwierdzonego.
  * **CloseAlert**: wszystkie alerty starsze tego samego typu, nie jest już powinna być wyświetlana na pulpicie nawigacyjnym.
  * **CallOpcMethod**: powinna być wywoływana z metody OPC UA.
  * **OpenWebPage**: okna przeglądarki powinien zostać otwarty wyświetlane dodatkowe informacje kontekstowe.

* **Opis** (typ string)

  Opis akcji widoczne w pulpicie nawigacyjnym.

* **Parametr** (typ string)

  Parametry wymagane do wykonania akcji. Wartość zależy od typu akcji.

  * **AcknowledgeAlert**: nie parametr wymagany.
  * **CloseAlert**: nie parametr wymagany.
  * **CallOpcMethod**: informacje o węźle i parametry metody OPC UA do wywołania w formacie "ID. węzła nadrzędnego węzła NodeId metody do wywołania, identyfikator URI serwera OPC UA."
  * **OpenWebPage**: adres URL, aby pokazać w oknie przeglądarki.

`<opc_node_description>` zawiera informacje o węzłów OPC UA stacji (serwer OPC UA). Węzły, które reprezentują żadnych istniejących węzłów OPC UA, ale są używane jako magazyn w logiki obliczeń połączonej fabryki, również są prawidłowe. Ma następujące właściwości:

* **NodeId** (typ string)

  Adres serwera OPC UA w przestrzeni adresowej węzła w stacji (OPC UA serwera). Składnia muszą być określone w specyfikacji NodeId serwera OPC UA.

* **SymbolicName** (typ string)

  Nazwa ma być wyświetlany na pulpicie nawigacyjnym, jeśli wartość tego węzła OPC UA jest wyświetlana.

* **Wg istotności** (tablicę typu string)

  Wskazuje, której obliczenie wartości ogólnej wydajności sprzętu lub kluczowego wskaźnika wydajności wartość węzła OPC UA jest istotne. Każdy element tablicy może być jednym z następujących wartości:

  * **OeeAvailability_Running**: wartość jest odpowiednie do obliczania dostępności ogólnej wydajności sprzętu.
  * **OeeAvailability_Fault**: wartość jest odpowiednie do obliczania dostępności ogólnej wydajności sprzętu.
  * **OeePerformance_Ideal**: wartość ma zastosowanie do obliczania wydajności ogólnej wydajności sprzętu i zwykle jest wartością stałą.
  * **OeePerformance_Actual**: wartość jest odpowiednie do obliczania wydajności ogólnej wydajności sprzętu.
  * **OeeQuality_Good**: wartość jest odpowiednie do obliczenia ogólnej wydajności sprzętu jakości.
  * **OeeQuality_Bad**: wartość jest odpowiednie do obliczenia ogólnej wydajności sprzętu jakości.
  * **Kpi1**: wartość jest odpowiednie do obliczania KPI1.
  * **Kpi2**: wartość jest odpowiednie do obliczania KPI2.

* **OpCode** (typ string)

  Wskazuje sposób obsługi wartości węzła OPC UA w zapytaniach Time Series Insights i obliczenia ogólnej wydajności sprzętu bądź kluczowego wskaźnika wydajności. Każda kwerenda Time Series Insights jest przeznaczony dla określonego przedziału czasu, co jest parametr zapytania i zapewnia wynik. OpCode kontroluje sposób wyniku jest obliczany i może być jedną z następujących wartości:

  * **Diff**: różnica między ostatniego i pierwsza wartość timespan.
  * **Średnia liczba**: średnią wszystkich wartości timespan.
  * **Suma**: sumę wszystkich wartości timespan.
  * **Ostatni**: obecnie nieużywane.
  * **Liczba**: liczba wartości timespan.
  * **Maksymalna liczba**: maksymalna wartość timespan.
  * **Min**: minimalne wartość timespan.
  * **Const**: wynik jest wartością określonej przez właściwość ConstValue.
  * **SubMaxMin**: różnica między maksymalnie i minimalnej wartości.
  * **Przedział czasu**: timespan.

* **Jednostki** (typ string)

  Definiuje jednostkę wartości do wyświetlenia na pulpicie nawigacyjnym.

* **Widoczne** (typ wartość logiczna)

  Kontroluje, czy wartość powinna być wyświetlana na pulpicie nawigacyjnym.

* **ConstValue** (typ double)

  Jeśli **OpCode** jest **Const**, a następnie ta właściwość jest wartość węzła.

* **Co najmniej** (typ double)

  Jeśli bieżącą wartość spada poniżej tej wartości, generowany jest alert w postaci minimalnej.

* **Maksymalna** (typ double)

  Jeśli bieżąca wartość wywołuje powyżej tej wartości, jest generowany alert dotyczący maksymalnej.

* **MinimumAlertActions** (typ `<alert_action>`)

  Definiuje zestaw akcji, które mogą być podejmowane w odpowiedzi na alert w postaci minimalnej.

* **MaximumAlertActions** (typ `<alert_action>`)

  Definiuje zestaw akcji, które mogą być podejmowane w odpowiedzi na alert dotyczący maksymalnej.

Na poziomie stacji, powinien być też widoczny **symulacji** obiektów. Te obiekty są używane wyłącznie do konfigurowania symulacji połączonej fabryki i nie należy używać do konfigurowania topologii rzeczywistych.

## <a name="how-the-configuration-data-is-used-at-runtime"></a>Jak dane konfiguracji są używane w czasie wykonywania

Wszystkie właściwości, które są używane w pliku konfiguracji można podzielić na różne kategorie w zależności od sposobu ich używania. Te kategorie są:

### <a name="visual-appearance"></a>Wygląd

Właściwości z tej kategorii definiują wygląd pulpit nawigacyjny połączonej fabryki. Przykłady:

* Name (Nazwa)
* Opis
* Image
* Lokalizacja
* Jednostki
* Widoczne

### <a name="internal-topology-tree-addressing"></a>Adresowanie drzewo wewnętrznych topologii

Aplikacja przechowuje słownika danych wewnętrznych, zawierający informacje o wszystkich węzłów topologii. Właściwości **Guid** i **OpcUri** są używane jako klucze do dostępu do tego słownika i muszą być unikatowe.

### <a name="oeekpi-computation"></a>Obliczenia ogólnej wydajności sprzętu bądź kluczowego wskaźnika wydajności

Dane ogólnej wydajności sprzętu bądź kluczowego wskaźnika wydajności symulacji połączonej fabryki są sparametryzowany przez:

* Wartości węzła OPC UA do uwzględnienia w obliczeniach.
* Jak rysunek jest obliczany na podstawie wartości telemetryczne.

Połączona fabryka używa formuł ogólnej wydajności sprzętu, jako opublikowane przez [ http://www.oeefoundation.org ](http://www.oeefoundation.org).

Obiekty węzła OPC UA stacji Włącz znakowanie do użycia podczas obliczania wartości ogólnej wydajności sprzętu bądź kluczowego wskaźnika wydajności. **Istotności** właściwość wskazuje, dla których rysunek ogólnej wydajności sprzętu bądź kluczowego wskaźnika wydajności powinna zostać użyta wartość węzła OPC UA. **OpCode** właściwość definiuje, jak wartość znajduje się w obliczeń.

### <a name="alert-handling"></a>Obsługa alertów

Połączona fabryka obsługuje mechanizm proste minimalne i maksymalne oparte na wartościach progowych Generowanie alertów. Istnieje kilka wstępnie zdefiniowanych akcji, które można skonfigurować w odpowiedzi na te alerty. Ten mechanizm kontrolować, następujące właściwości:

* Maksimum
* Minimalne
* MaximumAlertActions
* MinimumAlertActions

## <a name="correlating-to-telemetry-data"></a>Korelowanie danych telemetrycznych

W przypadku niektórych operacji, takich jak wizualizacja ostatnią wartość lub utworzenie kwerendy Time Series Insights, aplikacji internetowej musi schematu adresowania dane odebrane dane telemetryczne. Dane telemetryczne wysyłane do połączonej fabryki musi znajdować się w strukturach danych wewnętrznych. Dwie właściwości, umożliwiając te operacje są stacji (serwer OPC UA) i poziomu węzła OPC UA:

* **OpcUri**

  Identyfikuje serwer OPC UA danych telemetrycznych (globalnie unikatowa) pochodzą z. Odebrane wiadomości, ta właściwość jest wysyłany jako **identyfikator applicationuri serwera**.

* **NodeId**

  Określa wartość węzła serwera OPC UA. Plik musi mieć format właściwości określone w specyfikacji OPC UA. Odebrane wiadomości, ta właściwość jest wysyłany jako **NodeId**.

Sprawdź [to](https://github.com/Azure/iot-edge-opc-publisher) strony GitHub, aby uzyskać więcej informacji na temat sposobu dane telemetryczne są pozyskiwane do połączonej fabryki, korzystanie z wydawcy OPC.

## <a name="example-how-kpi1-is-calculated"></a>Przykład: Jak obliczana jest KPI1

Konfiguracja opisana w `ContosoTopologyDescription.json` pliku Określa, jak są obliczane wartości ogólnej wydajności sprzętu bądź kluczowego wskaźnika wydajności. Poniższy przykład pokazuje, jak kontrolować obliczeń KPI1 przez właściwości, w tym pliku.

W połączonych KPI1 Factory służy do mierzenia liczby produkty pomyślnie przetworzone w ciągu ostatniej godziny. Każdej stacji (serwer OPC UA) w symulacji Connected Factory zapewnia węzła OPC UA (`NodeId: "ns=2;i=385"`), który zawiera dane telemetryczne do obliczenia tego wskaźnika KPI.

Konfiguracja dla tego węzła OPC UA będzie wyglądać jak poniższy fragment kodu:

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

Ta konfiguracja umożliwia przesyłanie zapytań dotyczących wartości telemetryczne tego węzła przy użyciu usługi Time Series Insights. Pobiera zapytanie usługi Time Series Insights:

* Liczba wartości.
* Minimalnej wartości.
* Wartość maksymalna.
* Średnią wszystkich wartości.
* Sumę wszystkich wartości dla wszystkich unikatowych **OpcUri** (**identyfikator applicationuri serwera**), **NodeId** pary w danym przedziału czasu.

Jedną z cech **NumberOfManufactureredProducts** wartość węzła jest fakt, że tylko zwiększa. Aby obliczyć liczbę produktów wytworzonych w zakres czasu, używa połączonej fabryki **OpCode** **SubMaxMin**. Obliczenia pobiera wartość minimalna na początku zakres czasu i maksymalną wartość na końcu timespan.

**OpCode** w konfiguracji konfiguruje logiki obliczeń do obliczania wyniku różnicę wartości maksymalne i minimalne. Te wyniki są następnie zgromadzonych dolnej do poziomu głównego (globalna) i widoczne w pulpicie nawigacyjnym.

## <a name="next-steps"></a>Kolejne kroki

A sugerowane, następnym krokiem jest, aby dowiedzieć się, jak [dostosowanie rozwiązania połączonej fabryki](iot-accelerators-connected-factory-customize.md).
