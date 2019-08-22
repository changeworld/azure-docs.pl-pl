---
title: Konfigurowanie szablonu urządzenia w aplikacji IoT Central platformy Azure | Microsoft Docs
description: Dowiedz się, jak skonfigurować szablon urządzenia z pomiarami, ustawieniami, właściwościami, regułami i pulpitem nawigacyjnym.
author: viv-liu
ms.author: viviali
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 63c0a04a6d18d6af850b1492d2efa9df9aa65219
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877382"
---
# <a name="set-up-a-device-template"></a>Konfigurowanie szablonu urządzenia

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Szablon urządzenia jest planem, który definiuje cechy i zachowania typu urządzenia, które nawiązuje połączenie z aplikacją IoT Central platformy Azure.

Na przykład Konstruktor może utworzyć szablon urządzenia dla połączonego wentylatora o następujących cechach:

- Pomiar telemetrii temperatury
- Pomiar lokalizacji
- Pomiar zdarzeń błędów silnika wentylatorów
- Pomiar stanu działania wentylatora
- Ustawienie szybkości wentylatora
- Reguły, które wysyłają alerty
- Pulpit nawigacyjny, który zapewnia ogólny widok urządzenia

Z tego szablonu urządzenia operator może tworzyć i łączyć urządzenia wentylatorów w rzeczywistości z nazwami, takimi jak **wentylator-1** i **wentylator-2**. Wszystkie te wentylatory mają miary, ustawienia, właściwości, reguły i pulpit nawigacyjny, które użytkownicy aplikacji mogą monitorować i zarządzać nimi.

> [!NOTE]
> Tylko konstruktory i Administratorzy mogą tworzyć, edytować i usuwać szablony urządzeń. Każdy użytkownik może tworzyć urządzenia na **Device Explorer** stronie z istniejących szablonów urządzeń.

## <a name="create-a-device-template"></a>Tworzenie szablonu urządzenia

1. Przejdź do strony **Szablony urządzeń** .

2. Aby utworzyć szablon, Zacznij od wybrania opcji **+ Nowy**.

3. Aby szybko rozpocząć pracę, wybierz spośród istniejących wstępnie skompilowanych szablonów. W przeciwnym razie wybierz opcję **niestandardowy**, wprowadź nazwę, a następnie kliknij przycisk **Utwórz** , aby skompilować własny szablon od podstaw.

   ![Biblioteka szablonów urządzeń](./media/howto-set-up-template/newtemplate.png)

4. Podczas tworzenia szablonu niestandardowego zostanie wyświetlona strona **szczegóły urządzenia** dla nowego szablonu urządzenia. IoT Central automatycznie tworzy symulowane urządzenie podczas tworzenia szablonu urządzenia. Symulowane urządzenie umożliwia testowanie zachowania aplikacji przed połączeniem rzeczywistego urządzenia.

W poniższych sekcjach opisano poszczególne karty na stronie **szablonu urządzenia** .

## <a name="measurements"></a>Miary

Pomiary to dane pochodzące z urządzenia. Do szablonu urządzenia można dodać wiele pomiarów w celu dopasowania ich do możliwości urządzenia.

- Pomiary telemetrii to liczbowe punkty danych zbierane przez urządzenie z upływem czasu. Są one reprezentowane jako ciągły strumień. Przykładem jest temperatura.
- Pomiary **zdarzeń** to dane punktu w czasie, które reprezentują coś istotności na urządzeniu. Poziom ważności reprezentuje ważność zdarzenia. Przykładem jest błąd silnika wentylatoru.
- Pomiary **stanu** przedstawiają stan urządzenia lub jego składników w danym okresie czasu. Na przykład tryb wentylatoru można zdefiniować jako działający i zatrzymany jako dwa możliwe stany.
- Pomiary **lokalizacji** są współrzędne długości i szerokości geograficznej urządzenia w danym okresie czasu. Na przykład wentylator można przenieść z jednej lokalizacji do innej.

### <a name="create-a-telemetry-measurement"></a>Tworzenie pomiaru telemetrii

Aby dodać nową miarę telemetrię, wybierz pozycję **+ Nowa miara**, wybierz opcję **Telemetria** jako typ pomiaru, a następnie wprowadź szczegóły w formularzu.

> [!NOTE]
> Nazwy pól w szablonie urządzenia muszą być zgodne z nazwami właściwości w odpowiednim kodzie urządzenia, aby pomiar telemetrii był wyświetlany w aplikacji w przypadku połączenia rzeczywistego urządzenia. Wykonaj te same czynności podczas konfigurowania ustawień, właściwości urządzenia i poleceń w miarę dalszego definiowania szablonu urządzenia w poniższych sekcjach.

Na przykład można dodać nową miarę telemetrii dla temperatury:

| Nazwa wyświetlana        | Nazwa pola    |  Jednostki    | Min.   |Maks.|
| --------------------| ------------- |-----------|-------|---|
| Temperatura         | temp          |  degC     |  0    |100|

![Formularz "Utwórz telemetrię" ze szczegółami pomiaru temperatury](./media/howto-set-up-template/measurementsform.png)

Po wybraniu opcji **Zapisz**pomiar **temperatury** pojawia się na liście pomiarów. W krótkim czasie widzisz wizualizację danych temperatury z symulowanego urządzenia.

Podczas wyświetlania telemetrii można wybrać jedną z następujących opcji agregacji: Average, minimum, maksimum, sum i Count. **Średnia** jest wybierana jako agregacja domyślna na wykresie.

> [!NOTE]
> Typ danych pomiaru telemetrii jest liczbą zmiennoprzecinkową.

### <a name="create-an-event-measurement"></a>Tworzenie pomiaru zdarzenia

Aby dodać nowe pomiary zdarzeń, wybierz pozycję **+ Nowa miara** i wybierz pozycję **zdarzenie** jako typ pomiaru. Wprowadź szczegóły w formularzu **tworzenia zdarzenia** .

Podaj **nazwę wyświetlaną**, **nazwę pola**i szczegóły **ważności** zdarzenia. Można wybrać jeden z trzech dostępnych poziomów ważności: **Błąd**, **Ostrzeżenie**i **informacje**.

Na przykład można dodać nowe zdarzenie błędu dla usługi **wentylator** .

| Nazwa wyświetlana        | Nazwa pola    |  Domyślna ważność |
| --------------------| ------------- |-----------|
| Błąd silnika wentylatora     | fanmotorerror |  Błąd    |

![Formularz "Tworzenie zdarzenia" zawierający szczegóły dotyczące zdarzenia wentylatoru](./media/howto-set-up-template/eventmeasurementsform.png)

Po wybraniu opcji **Zapisz**na liście pomiarów zostanie wyświetlona pozycja **błędu silnika wentylatorów** . W krótkim czasie zobaczysz wizualizację danych zdarzenia z symulowanego urządzenia.

Aby wyświetlić więcej szczegółów dotyczących zdarzenia, wybierz ikonę zdarzenia na wykresie:

![Szczegóły dotyczące zdarzenia "wentylator Motor" (błąd)](./media/howto-set-up-template/eventmeasurementsdetail.png)

> [!NOTE]
> Typ danych pomiaru zdarzenia to ciąg.

### <a name="create-a-state-measurement"></a>Utwórz pomiar stanu

Aby dodać nową miarę stanu, wybierz przycisk **+ nowy pomiar** i wybierz pozycję **stan** jako typ pomiaru. Wprowadź szczegóły w formularzu **tworzenia stanu** .

Podaj szczegółowe informacje o **nazwie wyświetlanej**, **nazwie pola**i **wartościach** stanu. Każda wartość może mieć również nazwę wyświetlaną, która będzie używana, gdy wartość pojawia się na wykresach i tabelach.

Na przykład można dodać nowy stan **trybu wentylator** , który ma dwie możliwe wartości, które urządzenie może wysyłać, **działać** i **zatrzymać**.

| Nazwa wyświetlana | Nazwa pola    |  Wartość 1   | Nazwa wyświetlana | Wartość 2    |Nazwa wyświetlana  |
| -------------| ------------- |----------- | -------------| -----------| -------------|
| Tryb wentylatora     | fanmode       |  1         | Działa    |     0      | Zatrzymano      |

![Formularz "Edytuj stan" z szczegółowymi informacjami na temat trybu wentylatorów](./media/howto-set-up-template/statemeasurementsform.png)

Po wybraniu opcji **Zapisz**pomiar stanu **trybu wentylatoru** pojawia się na liście pomiarów. W krótkim czasie widzisz wizualizację danych stanu z symulowanego urządzenia.

Jeśli urządzenie wysyła zbyt wiele punktów danych w niewielkim czasie, pomiar stanu jest wyświetlany z inną wizualizacją. Wybierz wykres, aby wyświetlić wszystkie punkty danych w tym okresie w kolejności chronologicznej. Możesz również zawęzić zakres czasu, aby zobaczyć pomiar wykreślony na wykresie.

> [!NOTE]
> Typ danych pomiaru stanu to ciąg.

### <a name="create-a-location-measurement"></a>Utwórz miarę lokalizacji

Aby dodać nowe pomiary lokalizacji, wybierz pozycję **+ nowy pomiar**, wybierz pozycję **Lokalizacja** jako typ pomiaru, a następnie wprowadź szczegóły w formularzu **Utwórz pomiar** .

Na przykład możesz dodać nową miarę dane telemetrii lokalizacji:

| Nazwa wyświetlana        | Nazwa pola    |
| --------------------| ------------- |
| Lokalizacja zasobu      |  assetloc     |

![Formularz "Tworzenie lokalizacji" zawierający szczegóły pomiaru lokalizacji](./media/howto-set-up-template/locationmeasurementsform.png)

Po wybraniu opcji **Zapisz**pomiar **lokalizacji** pojawia się na liście pomiarów. W krótkim czasie zobaczysz wizualizację danych lokalizacji z symulowanego urządzenia.

Podczas wyświetlania lokalizacji można wybrać jedną z następujących opcji: Najnowsza historia lokalizacji i lokalizacji. **Historia lokalizacji** jest stosowana tylko w wybranym zakresie czasu.

Typ danych pomiaru lokalizacji jest obiektem, który zawiera wartości długości geograficznej, szerokości geograficznej i opcjonalnej wysokości. Poniższy fragment kodu przedstawia strukturę JavaScript:

```javascript
assetloc: {
  lon: floating point number,
  lat: floating point number,
  alt?: floating point number
}
```

Po nawiązaniu połączenia rzeczywistej lokalizacji dodanej w polu pomiar zostanie zaktualizowany przy użyciu wartości wysyłanej przez urządzenie. Po skonfigurowaniu miary lokalizacji można [dodać mapę do wizualizacji lokalizacji na pulpicie nawigacyjnym urządzenia](#add-a-location-measurement-in-the-dashboard).

## <a name="settings"></a>Ustawienia

Ustawienia kontrolują urządzenie. Umożliwiają operatorom udostępnianie danych wejściowych na urządzeniu. Do szablonu urządzenia można dodać wiele ustawień, które są wyświetlane jako kafelki na karcie **Ustawienia** dla operatorów do użycia. Można dodać wiele typów ustawień: liczbę, tekst, datę, przełącznik i etykietę sekcji.

Ustawienia mogą być w jednym z trzech stanów. Urządzenie zgłasza te Stany.

- **Zsynchronizowano**: Urządzenie zostało zmienione w celu odzwierciedlenia wartości ustawienia.

- **Oczekiwanie**: Urządzenie jest obecnie zmieniane na wartość ustawienia.

- **Błąd**: Urządzenie zwróciło błąd.

Na przykład możesz dodać nowe ustawienie szybkości wentylatorów, wybierając pozycję **Ustawienia** i wprowadzając nowe ustawienie **numeru** :

| Nazwa wyświetlana  | Nazwa pola    |  Jednostki  | Miejsca dziesiętne |Początkowa|
| --------------| ------------- |---------| ---------|---- |
| Szybkość wentylatorów     | fanSpeed      | RPM     | 2        | 0   |

![Formularz "Konfiguruj numer" z informacjami o ustawieniach szybkości](./media/howto-set-up-template/settingsform.png)

Po wybraniu opcji **Zapisz**ustawienie **szybkość wentylatoru** pojawia się jako kafelek. Operator może użyć ustawienia na stronie **Device Explorer** , aby zmienić szybkość wentylatorów urządzenia.

## <a name="properties"></a>Właściwości

Właściwości to metadane skojarzone z urządzeniem, takie jak stała lokalizacja urządzenia i numer seryjny. Dodaj do szablonu urządzenia wiele właściwości, które są wyświetlane jako kafelki na karcie **Właściwości** . Właściwość ma typ, taki jak liczba, tekst, Data, przełącznik, Właściwość urządzenia, etykieta lub stała lokalizacja. Operator określa wartości właściwości podczas tworzenia urządzenia i można je edytować w dowolnym momencie. Właściwości urządzenia są tylko do odczytu i są wysyłane z urządzenia do aplikacji. Operator nie może zmienić właściwości urządzenia. Po nawiązaniu połączenia z rzeczywistym urządzeniem kafelek właściwości urządzenia jest aktualizowany w aplikacji.

Są dostępne dwie kategorie właściwości:

- _Właściwości urządzenia_ , które urządzenie raportuje do aplikacji IoT Central. Właściwości urządzenia są wartościami tylko do odczytu raportowanymi przez urządzenie i są aktualizowane w aplikacji, gdy jest połączone rzeczywiste urządzenie.
- _Właściwości aplikacji_ , które są przechowywane w aplikacji i mogą być edytowane przez operatora. Właściwości aplikacji są przechowywane tylko w aplikacji i nigdy nie są wyświetlane przez urządzenie.

Na przykład można dodać datę ostatniego serwisu dla urządzenia jako nową właściwość **Date** (Właściwość aplikacji) na karcie **Właściwości** :

| Nazwa wyświetlana  | Nazwa pola | Wartość początkowa   |
| --------------| -----------|-----------------|
| Ostatnio serwisowane      | lastServiced        | 01/29/2019     |

![Formularz "Konfigurowanie ostatniego serwisu" na karcie "właściwości"](./media/howto-set-up-template/propertiesform.png)

Po wybraniu opcji **Zapisz**jako kafelek zostanie wyświetlona data ostatniej obsługi urządzenia.

Po utworzeniu kafelka można zmienić wartość właściwości aplikacji w **Device Explorer**.

### <a name="create-a-location-property"></a>Utwórz Właściwość Location

Kontekst geograficzny można nadać dane lokalizacji na platformie Azure IoT Central i zamapować wszystkie współrzędne szerokości geograficznej i długości geograficznej lub ulicę. Azure Maps włącza tę możliwość w IoT Central.

Można dodać dwa typy właściwości lokalizacji:

- **Lokalizacja jako właściwość aplikacji**, która jest przechowywana w aplikacji. Właściwości aplikacji są przechowywane tylko w aplikacji i nigdy nie są wyświetlane przez urządzenie.
- **Lokalizacja jako właściwość urządzenia**, która jest raportowana przez urządzenie do aplikacji. Tego typu właściwości najlepiej używać w przypadku lokalizacji statycznej.

> [!NOTE]
> Lokalizacja jako właściwość nie rejestruje historii. Jeśli wymagana jest historia, użyj miary lokalizacji.

#### <a name="add-location-as-an-application-property"></a>Dodaj lokalizację jako właściwość aplikacji

Właściwość Location można utworzyć jako właściwość aplikacji przy użyciu Azure Maps w aplikacji IoT Central. Można na przykład dodać adres instalacji urządzenia:

1. Przejdź do karty **Właściwości** .

2. W bibliotece wybierz pozycję **Lokalizacja**.

3. Skonfiguruj **nazwę wyświetlaną**, **nazwę pola**i (opcjonalnie) **początkową wartość** lokalizacji.

    | Nazwa wyświetlana  | Nazwa pola | Wartość początkowa |
    | --------------| -----------|---------|
    | Adres instalacyjny | installAddress | Microsoft, 1 Microsoft Way, Redmond, WA 98052   |

   ![Formularz "Konfigurowanie lokalizacji" z informacjami o lokalizacji](./media/howto-set-up-template/locationcloudproperty2.png)

   Istnieją dwa formaty obsługiwane do dodawania lokalizacji:
   - **Lokalizacja jako adres**
   - **Lokalizacja jako współrzędne**

4. Wybierz pozycję **Zapisz**. Operator może zaktualizować wartość lokalizacji w **Device Explorer**.

#### <a name="add-location-as-a-device-property"></a>Dodaj lokalizację jako właściwość urządzenia

Właściwość Location można utworzyć jako właściwość urządzenia zgłaszanego przez urządzenie. Na przykład jeśli chcesz śledzić lokalizację urządzenia:

1. Przejdź do karty **Właściwości** .

2. Wybierz pozycję **Właściwość urządzenia** z biblioteki.

3. Skonfiguruj nazwę wyświetlaną i nazwę pola, a następnie wybierz pozycję **Lokalizacja** jako typ danych:

    | Nazwa wyświetlana  | Nazwa pola | Typ danych |
    | --------------| -----------|-----------|
    | Lokalizacja urządzenia | deviceLocation | location  |

   > [!NOTE]
   > Nazwy pól muszą być zgodne z nazwami właściwości w odpowiednim kodzie urządzenia

   ![Formularz "Konfigurowanie właściwości urządzenia" z informacjami o lokalizacji](./media/howto-set-up-template/locationdeviceproperty2.png)

Po nawiązaniu połączenia z rzeczywistym urządzeniem lokalizacja dodana jako właściwość urządzenia jest aktualizowana przy użyciu wartości wysyłanej przez urządzenie. Po skonfigurowaniu właściwości Location można [dodać mapę do wizualizacji lokalizacji na pulpicie nawigacyjnym urządzenia](#add-a-location-property-in-the-dashboard).

## <a name="commands"></a>Polecenia

Polecenia służą do zdalnego zarządzania urządzeniem. Umożliwiają operatorom uruchamianie poleceń na urządzeniu. Do szablonu urządzenia można dodać wiele poleceń, które są wyświetlane jako kafelki na karcie **polecenia** dla operatorów do użycia. Jako Konstruktor urządzenia masz elastyczność definiowania poleceń zgodnie z wymaganiami.

Czym różni się polecenie od ustawienia?

- **Ustawienie**: Ustawienie jest konfiguracją, która ma zostać zastosowana do urządzenia. Urządzenie ma zachować tę konfigurację do momentu jego zmiany. Na przykład, chcesz ustawić temperaturę dla blokady i chcesz to ustawienie nawet po ponownym uruchomieniu blokady.

- **Polecenie**: Za pomocą poleceń można natychmiastowo uruchomić polecenie na urządzeniu zdalnie z IoT Central. Jeśli urządzenie nie jest połączone, polecenie przejdzie w dół i zakończy się niepowodzeniem. Na przykład chcesz ponownie uruchomić urządzenie.

Na przykład można dodać nowe polecenie **echo** , wybierając kartę **polecenia** , a następnie wybierając pozycję **+ nowe polecenie**i wprowadzając nowe szczegóły polecenia:

| Nazwa wyświetlana  | Nazwa pola | Domyślny limit czasu | Typ danych |
| --------------| -----------|---------------- | --------- |
| Polecenie echo  | echo       |  30             | text      |

![Formularz "Konfigurowanie polecenia" ze szczegółami dotyczącymi ECHA](./media/howto-set-up-template/commandsecho1.png)

Po wybraniu opcji **Zapisz**polecenie **echo** pojawia się jako kafelek i jest gotowe do użycia z **Device Explorer** po nawiązaniu połączenia z rzeczywistym urządzeniem. Nazwy pól polecenia muszą być zgodne z nazwami właściwości w odpowiednim kodzie urządzenia w celu pomyślnego uruchomienia poleceń.

[Poniżej znajduje się łącze do przykładowego kodu urządzenia C.](https://github.com/Azure/iot-central-firmware/blob/ad40358906aeb8f2040a822ba5292df866692c16/MXCHIP/mxchip_advanced/src/AzureIOTClient.cpp#L34)

## <a name="rules"></a>Reguły

Reguły umożliwiają operatorom monitorowanie urządzeń w czasie niemal rzeczywistym. Reguły automatycznie wywołują akcje, takie jak wysłanie wiadomości e-mail po wyzwoleniu reguły. Jeden typ reguły jest dostępny Dzisiaj:

- **Reguła**telemetrii, która jest wyzwalana, gdy wybrana wartość telemetrii urządzenia przekroczy określony próg. [Dowiedz się więcej o regułach](howto-create-telemetry-rules.md)telemetrii.

## <a name="dashboard"></a>Pulpit nawigacyjny

Pulpit nawigacyjny jest miejscem, w którym można wyświetlić informacje o urządzeniu. Jako Konstruktor Dodaj kafelki do tej strony, aby pomóc operatorom zrozumieć, jak działa urządzenie. Możesz dodać wiele typów kafelków pulpitu nawigacyjnego, takich jak obraz, wykres liniowy, wykres słupkowy, kluczowy wskaźnik wydajności (KPI), ustawienia i właściwości oraz etykieta.

Na przykład możesz dodać kafelek **Ustawienia i właściwości** , aby wyświetlić wybrane wartości bieżących ustawień i właściwości, wybierając kartę **pulpit nawigacyjny** i kafelek z biblioteki:

![Formularz "Konfigurowanie szczegółów urządzenia" z informacjami szczegółowymi dotyczącymi ustawień i właściwości](./media/howto-set-up-template/dashboardsettingsandpropertiesform1.png)

Teraz, gdy operator przegląda pulpit nawigacyjny w **Device Explorer**, może zobaczyć kafelek.

### <a name="add-a-location-measurement-in-the-dashboard"></a>Dodawanie pomiaru lokalizacji na pulpicie nawigacyjnym

Jeśli skonfigurowano pomiar lokalizacji, można wizualizować lokalizację za pomocą mapy na pulpicie nawigacyjnym urządzenia. W przypadku pomiarów lokalizacji można wykreślić historię lokalizacji.

1. Przejdź do karty **pulpit nawigacyjny** .

1. Na pulpicie nawigacyjnym urządzenia wybierz pozycję **Mapuj** z biblioteki.

1. Nadaj tytuł mapie. W poniższym przykładzie ustawiono **bieżącą lokalizację urządzenia**tytułowego. Następnie wybierz pomiar lokalizacji, który został wcześniej skonfigurowany na karcie **pomiary** . W poniższym przykładzie wybrano opcję pomiar **lokalizacji zasobu** :

   ![Formularz "Konfigurowanie mapy" zawierający szczegóły dotyczące tytułu i właściwości](./media/howto-set-up-template/locationcloudproperty5map.png)

1. Wybierz pozycję **Zapisz**. Kafelek Mapa teraz wyświetla wybraną lokalizację.

Można zmienić rozmiar kafelka mapy. Gdy operator przegląda pulpit nawigacyjny w **Device Explorer**, wszystkie skonfigurowane kafelki pulpitu nawigacyjnego, w tym mapy lokalizacji, są widoczne.

### <a name="add-a-location-property-in-the-dashboard"></a>Dodawanie właściwości Location na pulpicie nawigacyjnym

Jeśli skonfigurowano Właściwość Location, można wizualizować lokalizację za pomocą mapy na pulpicie nawigacyjnym urządzenia.

1. Przejdź do karty **pulpit nawigacyjny** .

1. Na pulpicie nawigacyjnym urządzenia wybierz pozycję **Mapuj** z biblioteki.

1. Nadaj tytuł mapie. W poniższym przykładzie ustawiono **bieżącą lokalizację urządzenia**tytułowego. Następnie wybierz właściwość Location, która została wcześniej skonfigurowana na karcie **Właściwości** . W poniższym przykładzie wybrano pomiar **lokalizacji urządzenia** :

   ![Skonfiguruj formularz mapy z informacjami o tytule i właściwościach](./media/howto-set-up-template/locationcloudproperty6map.png)

1. Wybierz pozycję **Zapisz**. Kafelek Mapa teraz wyświetla wybraną lokalizację.

Można zmienić rozmiar kafelka mapy. Gdy operator przegląda pulpit nawigacyjny w **Device Explorer**, wszystkie skonfigurowane kafelki pulpitu nawigacyjnego, w tym mapy lokalizacji, są widoczne.

Aby dowiedzieć się więcej na temat używania kafelków w usłudze Azure IoT Central, zobacz [Używanie kafelków pulpitu nawigacyjnego](howto-use-tiles.md).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak skonfigurować szablon urządzenia w aplikacji IoT Central platformy Azure, możesz:

- [Utwórz nową wersję szablonu urządzenia](howto-version-device-template.md)
- [Łączenie urządzenia zestawu deweloperskiego IoT DevKit z aplikacją usługi Azure IoT Central](howto-connect-devkit.md)
- [Łączenie ogólnej aplikacji klienckiej z aplikacją usługi Azure IoT Central (Node. js)](howto-connect-nodejs.md)
