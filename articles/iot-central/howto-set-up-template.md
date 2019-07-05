---
title: Konfigurowanie szablonu urządzenia w aplikacji usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować szablon urządzenia z pomiarów, ustawienia, właściwości, reguł i pulpitu nawigacyjnego.
author: viv-liu
ms.author: viviali
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 7fb0fba519a7833ac318c713dc9eb3c6ac7f8b5b
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509551"
---
# <a name="set-up-a-device-template"></a>Konfigurowanie szablonu urządzenia

Szablon urządzenia jest planu, który definiuje jakie cechy i zachowania typu urządzenia, który nawiązuje połączenie z aplikacją usługi Azure IoT Central.

Na przykład konstruktora, można utworzyć szablon urządzenia połączone wentylator, która ma następujące cechy:

- Pomiar telemetrii temperatury
- Pomiar lokalizacji
- Wentylator motor błąd zdarzenie pomiarów
- Wentylator pomiaru stan operacyjny
- Wentylator szybkość
- Reguły, które wysyłania alertów
- Pulpit nawigacyjny, który daje ogólny widok urządzenia

Za pomocą tego szablonu urządzenia można tworzyć i łączenie urządzeń rzeczywistych wentylator z nazwy, takie jak operator **1 wentylator** i **2 wentylator**. Wszystkie te wentylatory mają pomiarów, ustawienia, właściwości, reguł i pulpit nawigacyjny, który użytkownicy twojej aplikacji można monitorować i zarządzać nimi.

> [!NOTE]
> Tylko twórcy i Administratorzy mogą tworzyć, edytować i usuwanie szablonów urządzenia. Każdy użytkownik może utworzyć urządzenia na **Device Explorer** strony z istniejących szablonów urządzenia.

## <a name="create-a-device-template"></a>Tworzenie szablonu urządzenia

1. Przejdź do **szablonów urządzeń** strony.

2. Aby utworzyć szablon, uruchom, wybierając **+ nowy**.

3. Aby szybko rozpocząć pracę, wybierz z istniejących, wstępnie skompilowane szablony. W przeciwnym razie wybierz **niestandardowe**, wprowadź nazwę i kliknij przycisk **Utwórz** tworzyć szablon od podstaw.

   ![Biblioteka szablonów urządzenia](./media/howto-set-up-template/newtemplate.png)

4. Podczas tworzenia szablonu niestandardowego, zobacz **szczegóły urządzenia** strona tworzenia nowego szablonu urządzenia. Podczas tworzenia szablonu urządzenia IoT Central automatycznie tworzy symulowane urządzenie. Symulowane urządzenie umożliwia testowanie zachowanie aplikacji, aby połączyć prawdziwe urządzenie.

W poniższych sekcjach opisano każdej z kart na **szablon urządzenia** strony.

## <a name="measurements"></a>Miary

Pomiary są dane, które pochodzą z urządzenia. Możesz dodać wiele miar do szablonu urządzenia, aby dopasować możliwości urządzenia.

- **Dane telemetryczne** pomiary są punkty danych numerycznych, które urządzenie zbiera wraz z upływem czasu. Są one reprezentowane w formie ciągłego strumienia. Przykładem jest temperatura.
- **Zdarzenie** pomiary są dane w momencie, które przedstawiają coś o znaczeniu na urządzeniu. Poziom ważności reprezentuje wagę zdarzenie. Przykładem jest to błąd motor wentylator.
- **Stan** pomiarów reprezentuje stan urządzenia lub jej składniki w okresie czasu. Na przykład można zdefiniować jako posiadające tryb wentylator **operacyjnego** i **zatrzymane** jako dwa możliwe stany.
- **Lokalizacja** pomiary są współrzędne długości i szerokości geograficznej urządzenia w danym momencie w. Na przykład z jednego miejsca można przenieść kibicem.

### <a name="create-a-telemetry-measurement"></a>Tworzenie miary telemetrii

Aby dodać nowe miary telemetrii, wybierz **+ nowej miary**, wybierz **Telemetrii** jako wartość typu, a następnie wprowadź szczegóły na formularzu.

> [!NOTE]
> Nazwy pól w szablonie urządzenia muszą być zgodne nazwy właściwości w odpowiednim kodzie urządzenia w kolejności do pomiaru dane telemetryczne, mają być wyświetlane w aplikacji, jeśli rzeczywiste urządzenie jest połączone. Tak samo, podczas konfigurowania ustawień i właściwości urządzenia, poleceń, w miarę postępu definiowania szablonu urządzenia w poniższych sekcjach.
.PNG na przykład można dodać nowej miary telemetrii temperatury:

| Nazwa wyświetlana        | Nazwa pola    |  Jednostki    | Min.   |Maks.|
| --------------------| ------------- |-----------|-------|---|
| Temperatura         | Temp          |  degC     |  0    |100|

!["Utwórz dane telemetryczne" formularza przy użyciu szczegółów do pomiarów temperatury](./media/howto-set-up-template/measurementsform.png)

Po wybraniu **Zapisz**, **temperatury** miary, który pojawia się na liście pomiarów. W chwili, możesz zobaczyć wizualizacji dane dotyczące temperatury z symulowanego urządzenia.

Podczas wyświetlania danych telemetrycznych, można wybrać spośród następujących opcji agregacji: Średnia, minimalna, maksymalna, Sum i liczba. **Średnia** jest wybrany jako domyślna agregacja na wykresie.

> [!NOTE]
> Typ danych miary telemetrii jest zmiennoprzecinkowy numer punktu.

### <a name="create-an-event-measurement"></a>Tworzenie miary zdarzeń

Aby dodać nowe miary zdarzenia, wybierz **+ nowej miary** i wybierz **zdarzeń** jako typ miary. Wprowadź szczegóły **Utwórz zdarzenie** formularza.

Podaj **nazwę wyświetlaną**, **nazwę pola**, i **ważność** szczegóły zdarzenia. Możesz wybrać z trzech dostępnych poziomów ważności: **Błąd**, **ostrzeżenie**, i **informacji**.

Na przykład można dodać nowego **błąd Motor wentylator** zdarzeń.

| Nazwa wyświetlana        | Nazwa pola    |  Domyślna ważność |
| --------------------| ------------- |-----------|
| Błąd silnika wentylatora     | fanmotorerror |  Błąd    |

!["Utwórz zdarzenie" formularza ze szczegółowymi informacjami dla zdarzenia motor wentylatora](./media/howto-set-up-template/eventmeasurementsform.png)

Po wybraniu **Zapisz**, **błąd Motor wentylator** miary, który pojawia się na liście pomiarów. W chwili, możesz zobaczyć wizualizacji dane zdarzenia z symulowanego urządzenia.

Aby wyświetlić więcej szczegółów na temat zdarzenia, wybierz ikonę zdarzeń na wykresie:

![Szczegóły dotyczące zdarzeń "Wentylator Motor Error"](./media/howto-set-up-template/eventmeasurementsdetail.png)

> [!NOTE]
> Typ danych miary zdarzeń jest ciągiem.

### <a name="create-a-state-measurement"></a>Tworzenie miary stanu

Aby dodać nowe miary stanu, wybierz **+ nowej miary** i wybrać **stanu** jako typ miary. Wprowadź szczegóły **stanu Utwórz** formularza.

Podaj szczegóły **nazwę wyświetlaną**, **nazwę pola**, i **wartości** stanu. Każda wartość może mieć również nazwę wyświetlaną, który będzie używany, gdy wartość jest wyświetlana w wykresów i tabel.

Na przykład można dodać nowego **tryb wentylator** stanu, który ma dwa możliwe wartości, które urządzenia mogą wysyłać, **operacyjnego** i **zatrzymane**.

| Nazwa wyświetlana | Nazwa pola    |  Wartość 1   | Nazwa wyświetlana | Wartość 2    |Nazwa wyświetlana  | 
| -------------| ------------- |----------- | -------------| -----------| -------------|
| Tryb wentylatora     | fanmode       |  1         | Działa    |     0      | Zatrzymano      |

![Formularz "Edytuj stan" ze szczegółowymi informacjami w trybie wentylatora](./media/howto-set-up-template/statemeasurementsform.png)

Po wybraniu **Zapisz**, **tryb wentylator** pomiaru stanu, który pojawia się na liście pomiarów. W chwili, możesz zobaczyć wizualizacji danych o stanie z symulowanego urządzenia.

Jeśli urządzenie wysyła nadmiar punktów danych w małych czas pomiaru stanu jest za pomocą innej wizualizacji. Wybierz wykres, aby wyświetlić wszystkie punkty danych w tym przedziale czasu, w kolejności chronologicznej. Można również zawęzić zakres czasu, aby zobaczyć pomiaru wykreślić na wykresie.

> [!NOTE]
> Typ danych miary stanu jest ciągiem.

### <a name="create-a-location-measurement"></a>Tworzenie miary lokalizacji

Aby dodać nowe miary lokalizacji, wybierz **+ nowej miary**, wybierz **lokalizacji** jako wartość typu, a następnie wprowadź szczegóły **tworzenie miary** formularza.

Na przykład można dodać nowej miary telemetrii lokalizacji:

| Nazwa wyświetlana        | Nazwa pola    |
| --------------------| ------------- |
| Lokalizacja zasobu      |  assetloc     |

!["Utwórz w lokalizacji" formularza przy użyciu szczegółów lokalizacji miary](./media/howto-set-up-template/locationmeasurementsform.png)

Po wybraniu **Zapisz**, **lokalizacji** miary, który pojawia się na liście pomiarów. W chwili, możesz zobaczyć wizualizacji danych lokalizacji z symulowanego urządzenia.

Podczas wyświetlania lokalizacji, możesz korzystać z następujących opcji: najnowsze lokalizacją i historią lokalizacji. **Historia lokalizacji** stosuje się tylko za pośrednictwem wybranego zakresu czasu.

Typ danych miary lokalizacji jest obiekt, który zawiera długości, szerokości i wysokości opcjonalne. Poniższy fragment kodu przedstawia strukturę JavaScript:

```javascript
assetloc: {
  lon: floating point number,
  lat: floating point number,
  alt?: floating point number
}
```

## <a name="settings"></a>Ustawienia

Ustawienia określają urządzenia. Umożliwiają one operatorom Podaj dane wejściowe do urządzenia. Wiele ustawień można dodać do szablonu urządzenia, które są wyświetlane jako kafelki na **ustawienia** kartę dla operatorów do użycia. Można dodawać wiele różnych ustawień: liczba, tekst, Data, przełącznika, listy wyboru i Etykieta sekcji.

Ustawienia mogą być w jednym z trzech stanów. Urządzenie raportuje tych stanów.

- **Zsynchronizowano**: Urządzenie ma zmienione w celu uwzględnienia wartości ustawienia.

- **Oczekujące**: Urządzenie jest obecnie zmiana wartości ustawień.

- **Błąd**: Urządzenie zwróciło błąd.

Na przykład można dodać nowe ustawienie szybkość wentylator wybierając **ustawienia** i wprowadzając w nowym **numer** ustawienia:

| Nazwa wyświetlana  | Nazwa pola    |  Jednostki  | Miejsca dziesiętne |Początkowa|
| --------------| ------------- |---------| ---------|---- |
| Wentylator szybkości     | fanSpeed      | OBR. / MIN     | 2        | 0   |

!["Konfigurowanie numer" formularz zawierający szczegółowe informacje o ustawieniach szybkość](./media/howto-set-up-template/settingsform.png)

Po wybraniu **Zapisz**, **szybkość wentylator** ustawienie jest wyświetlane jako Kafelek. Operator może użyć ustawienia w **Device Explorer** strony, aby zmienić szybkość wentylator urządzenia.

## <a name="properties"></a>Właściwości

Właściwości są metadane, która jest skojarzona z urządzenia, takie jak lokalizacji stały urządzenia i numer seryjny. Dodać wiele właściwości do szablonu urządzenia, które są wyświetlane jako kafelki na **właściwości** kartę. Właściwość ma typ, takie jak liczba, tekst, Data, przełącznika, właściwości urządzenia, etykiety lub położenie ustalone. Operator określa wartości dla właściwości tworzą urządzenia, gdy ich te wartości można edytować w dowolnym momencie. Właściwości urządzenia są przeznaczone tylko do odczytu i są wysyłane z urządzenia do aplikacji. Operator nie można zmienić właściwości urządzenia. Gdy rzeczywiste urządzenie nawiąże połączenie, aktualizacji kafelka właściwości urządzenia w aplikacji.

Są dostępne dwie kategorie właściwości:

- _Właściwości urządzenia_ zgłaszaną przez urządzenie do aplikacji IoT Central. Właściwości tylko do odczytu wartości zgłaszanych przez urządzenie i urządzenia są aktualizowane w aplikacji, jeśli rzeczywiste urządzenie jest połączone.
- _Właściwości aplikacji_ , są przechowywane w aplikacji i mogą być edytowane przez operatora. Właściwości aplikacji są przechowywane tylko w aplikacji i nigdy nie są widoczne dla urządzenia.

Na przykład można dodać datę ostatniej obsługiwanym urządzeniu jako nowy **data** właściwości (właściwość aplikacji) **właściwości** karty:

| Nazwa wyświetlana  | Nazwa pola | Wartość początkowa   |
| --------------| -----------|-----------------|
| Ostatnio serwisowane      | lastServiced        | 01/29/2019     |

!["Configure ostatniego Serviced" formularza na karcie "Właściwości"](./media/howto-set-up-template/propertiesform.png)

Po wybraniu **Zapisz**, ostatni obsłużonych daty dla urządzenia jest wyświetlany jako Kafelek.

Po utworzeniu kafelka, można zmienić wartość właściwości aplikacji **Device Explorer**.

### <a name="create-a-location-property"></a>Utwórz właściwość lokalizacji

Można zapewnić kontekst geograficzny do danych o Twojej lokalizacji w usłudze Azure IoT Central i mapowanie dowolnego szerokości i współrzędne geograficzne lub adres. Usługi Azure Maps umożliwia tę możliwość usługi IoT Central.

Możesz dodać dwa typy właściwości lokalizacji:

- **Lokalizacja jako właściwość aplikacji**, który jest przechowywany w aplikacji. Właściwości aplikacji są przechowywane tylko w aplikacji i nigdy nie są widoczne dla urządzenia.
- **Lokalizacja jako właściwości urządzenia**, którego urządzenie raportuje do aplikacji. Tego rodzaju właściwości najlepiej nadaje się do lokalizacji statyczne.

> [!NOTE]
> Lokalizacja jako właściwość nie rejestruje historię. W razie potrzeby historii użycia pomiaru lokalizacji.

#### <a name="add-location-as-an-application-property"></a>Dodaj lokalizację jako właściwość aplikacji

Właściwość location jako właściwość aplikacji można utworzyć za pomocą usługi Azure Maps w aplikacji IoT Central. Na przykład można dodać adres instalacji urządzenia:

1. Przejdź do **właściwości** kartę.

2. W bibliotece, wybierz **lokalizacji**.

3. Konfigurowanie **nazwę wyświetlaną**, **nazwę pola**oraz (opcjonalnie) **wartość początkową** dla lokalizacji.

    | Nazwa wyświetlana  | Nazwa pola | Wartość początkowa |
    | --------------| -----------|---------|
    | Adres instalacji | installAddress | Microsoft, 1 Microsoft Way, Redmond, WA 98052   |

   !["Konfigurowanie lokalizacji" formularza przy użyciu szczegółów lokalizacji](./media/howto-set-up-template/locationcloudproperty2.png)

   Istnieją dwa obsługiwanych formatów, aby dodać lokalizację:
   - **Lokalizacja adresu**
   - **Lokalizacja jako współrzędne**

4. Wybierz pozycję **Zapisz**. Operator może zaktualizować wartość lokalizacji **Device Explorer**.

#### <a name="add-location-as-a-device-property"></a>Dodaj lokalizację jako właściwości urządzenia

Właściwość lokalizacji można utworzyć jako właściwość urządzenia zgłaszanego przez urządzenie. Na przykład, jeśli chcesz śledzić lokalizacji urządzenia:

1. Przejdź do **właściwości** kartę.

2. Wybierz **właściwości urządzenia** z biblioteki.

3. Skonfiguruj nazwę wyświetlaną i nazwę pola, a następnie wybierz pozycję **lokalizacji** jako typ danych:

    | Nazwa wyświetlana  | Nazwa pola | Typ danych |
    | --------------| -----------|-----------|
    | Lokalizacja urządzenia | deviceLocation | location  |

   > [!NOTE]
   > Nazwy pól muszą być zgodne nazwy właściwości w odpowiednim kodzie urządzenia

   !["Konfigurowanie właściwości urządzenia" formularza przy użyciu szczegółów lokalizacji](./media/howto-set-up-template/locationdeviceproperty2.png)

Po nawiązaniu połączenia rzeczywistego urządzenia lokalizacji dodane jako właściwości urządzenia zostanie zaktualizowany o wartości wysyłane przez urządzenie. Po skonfigurowaniu właściwości swojej lokalizacji możesz [dodać mapę, aby wizualizować lokalizacji na pulpicie nawigacyjnym urządzenia](#add-a-location-in-the-dashboard).

## <a name="commands"></a>Polecenia

Polecenia są używane do zdalnego zarządzania urządzeniem. Umożliwiają one operatory do uruchamiania poleceń na urządzeniu. Wiele poleceń można dodać do szablonu urządzenia, które są wyświetlane jako kafelki na **polecenia** kartę dla operatorów do użycia. Jako producenta urządzenia masz możliwość definiowania polecenia zgodnie z wymaganiami.

Czym różni się polecenie z ustawienia?

* **Ustawienie**: To ustawienie jest konfiguracja, który chcesz zastosować do urządzenia. Chcesz, aby urządzenia, aby zachować tę konfigurację, dopóki nie zostanie zmieniony. Na przykład chcesz ustawić temperatura swoje zamrażalni i ma ustawienie nawet wtedy, gdy zamrażarce powoduje ponowne uruchomienie.

* **Polecenie**: Polecenia aby natychmiast uruchomić polecenie na urządzeniu zdalnie z IoT Central. Jeśli urządzenie nie jest połączony, polecenie upłynie limit czasu i kończy się niepowodzeniem. Na przykład chcesz ponownie uruchomić urządzenie.

Na przykład można dodać nowego **Echo** polecenia, wybierając **polecenia** kartę, a następnie wybierając pozycję **+ nowe polecenie**i wprowadzić nowe szczegóły polecenia:

| Nazwa wyświetlana  | Nazwa pola | Domyślny limit czasu | Typ danych |
| --------------| -----------|---------------- | --------- |
| Polecenie echo  | echo       |  30             | tekst      |

!["Configure polecenia" formularza przy użyciu szczegółów echo](./media/howto-set-up-template/commandsecho1.png)

Po wybraniu **Zapisz**, **Echo** polecenia pojawi się jako Kafelek i jest gotowa do użycia w **Device Explorer** gdy z rzeczywistego urządzenia. Nazwy pól polecenia muszą być zgodne nazwy właściwości w odpowiednim kodzie urządzenia, aby polecenia, aby pomyślnie uruchomić.

## <a name="rules"></a>Reguły

Reguły umożliwiają operatorów do monitorowania urządzeń w czasie zbliżonym do rzeczywistego. Reguły automatycznie wywołują akcje, takie jak wysyłanie wiadomości e-mail po wyzwoleniu reguły. Obecnie jest dostępna jeden typ reguły:

- **Reguła telemetrii**, który program jest wyzwalany w przypadku, gdy dane telemetryczne wybranego urządzenia przekracza określoną wartość progową. [Dowiedz się więcej o regułach telemetrii](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>Pulpit nawigacyjny

Pulpit nawigacyjny jest, gdy operator prowadzi do informacji o urządzeniu. Jako Konstruktor możesz dodać Kafelki do tej strony, aby zrozumieć, jak zachowuje się urządzenia operatorom pomocy. Można dodawać wiele różnych kafelków pulpitu nawigacyjnego, takich jak obraz, wykres liniowy, wykres słupkowy, kluczowy wskaźnik wydajności (KPI), ustawień i właściwości i etykiety.

Na przykład można dodać **ustawień i właściwości** Kafelek, aby wyświetlić wybór bieżące wartości ustawień i właściwości, wybierając **pulpit nawigacyjny** kartę i Kafelek z biblioteki:

!["Configure szczegóły urządzenia" formularz Szczegóły ustawień i właściwości](./media/howto-set-up-template/dashboardsettingsandpropertiesform1.png)

Teraz, gdy operator widoków pulpitu nawigacyjnego w **Device Explorer**, zobaczą Kafelek.

### <a name="add-a-location-in-the-dashboard"></a>Dodaj lokalizację na pulpicie nawigacyjnym

Jeśli skonfigurowano pomiaru lokalizacji można wizualizować lokalizacji z mapą na pulpicie nawigacyjnym urządzenia.

1. Przejdź do **pulpit nawigacyjny** kartę.

1. Na pulpicie nawigacyjnym urządzenia wybierz **mapy** z biblioteki.

1. Nadaj mapy tytuł. W poniższym przykładzie przedstawiono tytuł **bieżącej lokalizacji urządzenia**. Następnie wybierz lokalizację miary, który został wcześniej skonfigurowany w **pomiarów** kartę. W poniższym przykładzie **lokalizacji zasobów** jest wybierana:

   !["Configure mapy" formularza przy użyciu szczegółów tytułu i właściwości](./media/howto-set-up-template/locationcloudproperty5map.png)

1. Wybierz pozycję **Zapisz**. Kafelek mapa zawiera teraz wybranej lokalizacji.

Można zmienić rozmiar kafelka mapy. Gdy operator widoków pulpitu nawigacyjnego w **Device Explorer**, pulpicie nawigacyjnym Kafelki, że zostały skonfigurowane, w tym mapę lokalizacji są widoczne.

Aby dowiedzieć się więcej o sposobie używania fragmentacji w usłudze Azure IoT Central, zobacz [Użyj Kafelki pulpitu nawigacyjnego](howto-use-tiles.md).

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz sposób konfigurowania szablonu urządzenia w aplikacji usługi Azure IoT Central, możesz wykonywać następujące czynności:

> [!div class="nextstepaction"]
> [Utwórz nową wersję szablonu urządzenia](howto-version-device-template.md)
> [podłączenie urządzenia z systemem zestawu deweloperskiego IoT Mxchip z aplikacją usługi Azure IoT Central](howto-connect-devkit.md)
> [łączenie aplikacji klienckiej ogólnego do subskrypcji platformy Azure IoT Central aplikacji (Node.js)](howto-connect-nodejs.md)