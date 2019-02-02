---
title: Konfigurowanie szablonu urządzenia w aplikacji usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować szablon urządzenia z pomiarów, ustawienia, właściwości, reguł i pulpitu nawigacyjnego.
author: viv-liu
ms.author: viviali
ms.date: 10/26/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: fc18262326a8474cac417b67a37df35d91d75439
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2019
ms.locfileid: "55657743"
---
# <a name="set-up-a-device-template"></a>Konfigurowanie szablonu urządzenia

Szablon urządzenia jest planu, który definiuje jakie cechy i zachowania typu urządzenia, który nawiązuje połączenie z aplikacją usługi Azure IoT Central.

Na przykład konstruktora, można utworzyć szablon urządzenia połączone IoT wentylator, który ma:

- Pomiar telemetrii temperatury

- Wentylator motor błąd zdarzenie pomiarów

- Wentylator pomiaru stan operacyjny

- Wentylator szybkość

- Właściwość lokalizacji

- Reguły, które wysyłania alertów

- Pulpit nawigacyjny, który daje ogólny widok urządzenia

Za pomocą tego szablonu urządzenia można tworzyć i łączenie urządzeń rzeczywistych wentylator z nazwy, takie jak operator **1 wentylator** i **2 wentylator**. Wszystkie te wentylatory mają pomiarów, ustawienia, właściwości, reguł i pulpit nawigacyjny, który użytkownicy twojej aplikacji można monitorować i zarządzać nimi.

> [!NOTE]
> Tylko twórcy i Administratorzy mogą tworzyć, edytować i usuwanie szablonów urządzenia. Każdy użytkownik może utworzyć urządzenia na **Device Explorer** strony z istniejących szablonów urządzenia.

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="create-a-device-template"></a>Tworzenie szablonu urządzenia

1. Przejdź do **Konstruktor aplikacji** strony.

2. Aby utworzyć pusty szablon, wybierz **Utwórz szablon urządzenia**, a następnie wybierz pozycję **niestandardowe**.

3. Wprowadź nazwę (np. lodówki-1) dla nowego szablonu urządzenia, a następnie wybierz pozycję **Utwórz**.

   ![Strony szczegółów urządzenia za pomocą "Lodówki" jako nazwa szablonu](./media/howto-set-up-template/devicedetailspage.png)

4. Teraz możesz teraz **szczegóły urządzenia** strony nowe urządzenie symulowane. Symulowane urządzenie są tworzone automatycznie podczas tworzenia szablonu urządzenia. On raporty danych i mogą być kontrolowane podobnie jak rzeczywistego urządzenia.

Teraz Przyjrzyjmy się każdej z kart **szczegóły urządzenia** strony.

## <a name="measurements"></a>Miary

Pomiary są dane, które pochodzą z urządzenia. Możesz dodać wiele miar do szablonu urządzenia, aby dopasować możliwości urządzenia.

- **Dane telemetryczne** pomiary są punkty danych numerycznych, które urządzenie zbiera wraz z upływem czasu. Są one reprezentowane w formie ciągłego strumienia. Przykładem jest temperatura.
- **Zdarzenie** pomiary są dane w momencie, które przedstawiają coś o znaczeniu na urządzeniu. Poziom ważności reprezentuje wagę zdarzenie. Przykładem jest to błąd motor wentylator.
- **Stan** pomiarów reprezentuje stan urządzenia lub jej składniki w okresie czasu. Na przykład można zdefiniować jako posiadające tryb wentylator **operacyjnego** i **zatrzymane** jako dwa możliwe stany.

### <a name="create-a-telemetry-measurement"></a>Tworzenie miary telemetrii
Aby dodać nowe miary telemetrii, wybierz **Edytuj szablon**, a następnie kliknij przycisk **+ nowej miary** przycisku. Wybierz **Telemetrii** jako wartość typu, a następnie wprowadź szczegóły **tworzenie Telemetrii** formularza.

> [!NOTE]
> Nazwy pól w szablonie urządzenia muszą być zgodne nazwy właściwości w odpowiednim kodzie urządzenia w kolejności do pomiaru dane telemetryczne, mają być wyświetlane w aplikacji, jeśli rzeczywiste urządzenie jest połączone. Tak samo, podczas konfigurowania ustawień i właściwości urządzenia, poleceń, w miarę postępu definiowania szablonu urządzenia w poniższych sekcjach.

Na przykład można dodać nowej miary telemetrii temperatury:
| Nazwa wyświetlana        | Nazwa pola    |  Jednostki    | Min.   |Maks.|
| --------------------| ------------- |-----------|-------|---|
| Temperatura         | Temp          |  degC     |  0    |100|

!["Utwórz dane telemetryczne" formularza przy użyciu szczegółów do pomiarów temperatury](./media/howto-set-up-template/measurementsform.png)

Po wybraniu **gotowe**, **temperatury** miary, który pojawia się na liście pomiarów. W krótkim trochę czasu można zobaczyć wizualizacji dane dotyczące temperatury, która wygenerowała symulowanego urządzenia. Podczas tworzenia szablonu urządzenia symulowanego urządzenia jest generowany na podstawie szablonu, który umożliwia testowanie zachowania aplikacji, zanim urządzenie fizyczne/rzeczywistym jest połączone.


> [!NOTE]
  Typ danych miary telemetrii jest zmiennoprzecinkowy numer punktu.

### <a name="create-an-event-measurement"></a>Tworzenie miary zdarzeń
Aby dodać nowe miary zdarzenia, wybierz **Edytuj szablon**, a następnie kliknij przycisk **+ nowej miary** przycisku. Wybierz **zdarzeń** jako wartość typu, a następnie wprowadź szczegóły **Utwórz zdarzenie** formularza.

Podaj **nazwę wyświetlaną**, **nazwę pola**, i **ważność** szczegóły zdarzenia. Możesz wybrać z trzech dostępnych poziomów ważności: **Błąd**, **ostrzeżenie**, i **informacji**.  

Na przykład można dodać nowego **błąd Motor wentylator** zdarzeń.

| Nazwa wyświetlana        | Nazwa pola    |  Domyślna ważność | 
| --------------------| ------------- |-----------|
| Błąd silnika wentylatora     | fanmotorerror |  Błąd    | 

!["Utwórz zdarzenie" formularza ze szczegółowymi informacjami dla zdarzenia motor wentylatora](./media/howto-set-up-template/eventmeasurementsform.png)

Po wybraniu **gotowe**, **błąd Motor wentylator** miary, który pojawia się na liście pomiarów. Operator widzą wizualizacji dane zdarzenia, które wysyła urządzenia.

![Zdarzenie pomiarów wykresu](./media/howto-set-up-template/eventmeasurementschart.png)

Aby wyświetlić więcej szczegółów na temat zdarzenia, wybierz ikonę zdarzeń na wykresie.

![Szczegóły dotyczące zdarzeń "Wentylator Motor Error"](./media/howto-set-up-template/eventmeasurementsdetail.png)

> [!NOTE]
  Typ danych miary zdarzeń jest ciągiem.

### <a name="create-a-state-measurement"></a>Tworzenie miary stanu
Aby dodać nowe miary stanu, wybierz **Edytuj szablon**, a następnie kliknij przycisk **+ nowej miary** przycisku. Wybierz **stanu** jako wartość typu, a następnie wprowadź szczegóły **stanu Utwórz** formularza.

Podaj szczegóły **nazwę wyświetlaną**, **nazwę pola**, i **wartości** stanu. Każda wartość może mieć również nazwę wyświetlaną, który będzie używany, gdy wartość jest wyświetlana w wykresów i tabel.

Na przykład można dodać nowego **tryb wentylator** stanu, który ma dwa możliwe wartości, które urządzenia mogą wysyłać, **operacyjnego** i **zatrzymane**.


| Nazwa wyświetlana | Nazwa pola    |  Wartość 1   | Nazwa wyświetlana | Wartość 2    |Nazwa wyświetlana  | 
| -------------| ------------- |----------- | -------------| -----------| -------------|
| Tryb wentylatora     | fanmode       |  1         | Działa    |     0      | Zatrzymano      |

![Formularz "Edytuj stan" ze szczegółowymi informacjami w trybie wentylatora](./media/howto-set-up-template/statemeasurementsform.png)

Po wybraniu **gotowe**, **tryb wentylator** pomiaru stanu, który pojawia się na liście pomiarów. Operator widzą wizualizacji danych stanu, który wysyła urządzenia.

![Wykres stanu pomiaru](./media/howto-set-up-template/statemeasurementschart.png)

Jeśli urządzenie wysyła nadmiar punktów danych w małych czas trwania, pomiar stanu pojawia się z inną wizualizację, jak pokazano na poniższym zrzucie ekranu. Po kliknięciu na wykresie wszystkie punkty danych, w tym okresie są wyświetlane w porządku chronologicznym. Można również zawęzić zakres czasu, aby zobaczyć pomiaru wykreślić na wykresie.

> [!NOTE]
  Typ danych miary stanu jest ciągiem.

## <a name="settings"></a>Ustawienia

Ustawienia określają urządzenia. Umożliwiają one operatory aplikacji w celu zapewnienia danych wejściowych do urządzenia. Wiele ustawień można dodać do szablonu urządzenia, które są wyświetlane jako kafelki na **ustawienia** kartę dla operatorów do użycia. Można dodawać wiele różnych ustawień: liczba, tekst, Data, przełącznika, listy wyboru i Etykieta sekcji. 

Ustawienia mogą być w jednym z trzech stanów. Urządzenie raportuje tych stanów.

- **Zsynchronizowano**: Urządzenie ma zmienione w celu uwzględnienia wartości ustawienia.

- **Oczekujące**: Urządzenie jest obecnie zmiana wartości ustawień.

- **Błąd**: Urządzenie zwróciło błąd.

Na przykład można dodać nowe ustawienie szybkość wentylator wybierając **Edytuj szablon** i wprowadzając w nowym **numer** ustawienia:

| Nazwa wyświetlana  | Nazwa pola    |  Jednostki  | Miejsca dziesiętne |Początkowa|
| --------------| ------------- |---------| ---------|---- |
| Wentylator szybkości     | fanSpeed      | OBR. / MIN     | 2        | 0   |

!["Konfigurowanie numer" formularz zawierający szczegółowe informacje o ustawieniach szybkość](./media/howto-set-up-template/settingsform.png)

Po wybraniu **Zapisz**, **szybkość wentylator** ustawienie jest wyświetlana jako Kafelek i jest gotowe do użycia, aby zmienić szybkość wentylator urządzenia.

Po utworzeniu kafelka, wybierz **gotowe** w prawej górnej części ekranu. Gdy rzeczywiste urządzenie jest podłączone do aplikacji, wartość ustawienia zmieni się na zsynchronizowane.

![Kartę "Ustawienia" z przełącznikiem "Tryb projektowania" dla kafelka](./media/howto-set-up-template/settingstile.png)

## <a name="properties"></a>Właściwości

Właściwości są metadane urządzenia skojarzone z urządzenia, takie jak lokalizacja urządzenia i numer seryjny. Wiele właściwości atrybutu można dodać do szablonu urządzenia, które są wyświetlane jako kafelki na **właściwości** kartę. Można dodać wiele typów właściwości: liczba, tekst, Data, Przełącz, właściwości urządzenia, etykiety i lokalizacji. Operator można określić wartości dla właściwości, tworzą urządzenia, gdy ich te wartości można edytować w dowolnym momencie. Jednak właściwości urządzenia są przeznaczone tylko do odczytu są wysyłane z urządzenia do aplikacji i nie można zmienić przez operatora. Jeśli rzeczywiste urządzenie jest połączone, Kafelek właściwości urządzenia zostaną zaktualizowane w aplikacji. 

Są dostępne dwie kategorie właściwości:

- **Urządzenie** właściwości, które urządzenie raportuje do aplikacji IoT Central. Te wartości tylko do odczytu zgłoszone przez urządzenie i zostanie zaktualizowany w aplikacji, gdy rzeczywiste urządzenie jest połączone. 
- **Aplikacja** właściwości, które są przechowywane wyłącznie w aplikacji i może być edytowana przez operatora. Urządzenie nie rozpoznaje właściwości aplikacji.

Na przykład można dodać lokalizacji urządzenia jako nowy **tekstu** właściwością (aplikacji), wybierając **Edytuj szablon** i wprowadzając w nowej właściwości:

| Nazwa wyświetlana  | Nazwa pola | Przytnij początkowe spacje  | Przytnij końcowe spacje  | Rozróżnianie wielkości liter| Minimalna długość | Maksymalna długość |
| --------------| -----------|---------| ---------|---- |----|----|
| Lokalizacja      | loc        | Wyłączone     |  Wyłączone     | Mieszane  | 0 | 100|

!["Configure tekst" formularza na karcie "Właściwości"](./media/howto-set-up-template/propertiesform.png)

Po wybraniu **Zapisz**, lokalizacji urządzenia jest wyświetlany jako kafelka:

![Lokalizacja kafelka](./media/howto-set-up-template/propertiestile.png)

Po utworzeniu kafelka, można zmienić wartości właściwości aplikacji. Najpierw wybierz **gotowe** w prawej górnej części ekranu.

### <a name="create-a-location-property-through-azure-maps"></a>Utwórz właściwość lokalizacji za pośrednictwem usługi Azure Maps
Można zapewnić kontekst geograficzny do danych o Twojej lokalizacji w usłudze Azure IoT Central i wszelkie współrzędne długości i szerokości geograficznej adresu pocztowego mapy. Lub może po prostu mapy współrzędne geograficzne. Usługi Azure Maps umożliwia tę możliwość usługi IoT Central.

Możesz dodać dwa typy właściwości lokalizacji:
- **Lokalizacja jako właściwość aplikacji**, które są przechowywane wyłącznie w aplikacji. Urządzenie nie rozpoznaje właściwości aplikacji.
- **Lokalizacja jako właściwości urządzenia**, którego urządzenie raportuje do aplikacji.

#### <a name="add-location-as-an-application-property"></a>Dodaj lokalizację jako właściwość aplikacji 
Właściwość location jako właściwość aplikacji można utworzyć za pomocą usługi Azure Maps w aplikacji IoT Central. Na przykład można dodać adres instalacji urządzenia. 

1. Na **właściwości** zaznacz **Edytuj szablon**.

   ![Karta "Properties" z po włączeniu trybu projektowania](./media/howto-set-up-template/locationcloudproperty1.png)

2. W bibliotece, wybierz **lokalizacji**.
3. Konfigurowanie **nazwę wyświetlaną**, **nazwę pola**oraz (opcjonalnie) **wartość początkową** dla lokalizacji. 

    | Nazwa wyświetlana  | Nazwa pola | Wartość początkowa |
    | --------------| -----------|---------| 
    | Adres instalacji | insta_address | Microsoft, 1 Microsoft Way, Redmond, WA 98052   |

   !["Konfigurowanie lokalizacji" formularza przy użyciu szczegółów lokalizacji](./media/howto-set-up-template/locationcloudproperty2.png)

   Istnieją dwa obsługiwanych formatów, aby dodać lokalizację:
   - **Lokalizacja adresu**
   - **Lokalizacja jako współrzędne** 

4. Wybierz **Zapisz** i **gotowe**. Teraz operator można zaktualizować wartości lokalizacji w postaci pole lokalizacji. 

#### <a name="add-location-as-a-device-property"></a>Dodaj lokalizację jako właściwości urządzenia 

Właściwość lokalizacji można utworzyć jako właściwość urządzenia zgłaszanego przez urządzenie. Na przykład, jeśli chcesz śledzić lokalizacji urządzenia:

1. Na **właściwości** zaznacz **Edytuj szablon**.

   ![Karta "Properties" z po włączeniu trybu projektowania](./media/howto-set-up-template/locationdeviceproperty1.png)

2. Wybierz **właściwości urządzenia** z biblioteki.
3. Skonfiguruj nazwę wyświetlaną i nazwę pola, a następnie wybierz pozycję **lokalizacji** jako typ danych. 

    | Nazwa wyświetlana  | Nazwa pola | Typ danych |
    | --------------| -----------|-----------| 
    | Lokalizacja urządzenia | deviceLoc| location  |

   > [!NOTE]
   > Nazwy pól muszą być zgodne nazwy właściwości w odpowiednim kodzie urządzenia

   !["Konfigurowanie właściwości urządzenia" formularza przy użyciu szczegółów lokalizacji](./media/howto-set-up-template/locationdeviceproperty2.png)

Po nawiązaniu połączenia rzeczywistego urządzenia lokalizacji, w której zostanie dodany jako właściwości urządzenia zostaną zaktualizowane o wartości wysyłane przez urządzenie. Adres instalacji, lokalizacji, w której zostanie dodany jako właściwość aplikacji jest edytowalny kafelka. Teraz, gdy skonfigurowano właściwość Twojej lokalizacji, możesz [dodać mapę, aby wizualizować lokalizacji na pulpicie nawigacyjnym urządzenia](#add-an-azure-maps-location-in-the-dashboard).

## <a name="commands"></a>Polecenia

Polecenia są używane do zdalnego zarządzania urządzeniem. Umożliwiają one operatorów w Twojej aplikacji, aby natychmiast uruchomić polecenia na urządzeniu. Wiele poleceń można dodać do szablonu urządzenia, które są wyświetlane jako kafelki na **polecenia** kartę dla operatorów do użycia. Jako producenta urządzenia masz możliwość definiowania polecenia zgodnie z wymaganiami.

Czym różni się polecenie z ustawienia? 

* **Ustawienie**: To ustawienie jest konfiguracji, który chcesz zastosować do urządzenia, a urządzenie, aby zachować tę konfigurację, dopóki nie zostanie zmieniony. Na przykład chcesz ustawić temperatura swoje zamrażalni i ma ustawienie nawet wtedy, gdy zamrażarce powoduje ponowne uruchomienie. 

* **Polecenie**: Polecenia aby natychmiast uruchomić polecenie na urządzeniu zdalnie z IoT Central. Jeśli urządzenie nie jest połączony, polecenie upłynie limit czasu i kończy się niepowodzeniem. Na przykład chcesz ponownie uruchomić urządzenie.  


Na przykład można dodać nowego **Echo** polecenia, wybierając **edycji szablonu**, klikając **+ nowe polecenie**i wprowadzając w nowe polecenie:

| Nazwa wyświetlana  | Nazwa pola | Domyślny limit czasu | Typ danych |
| --------------| -----------|---------------- | --------- | 
| Polecenie echo  | echo       |  30             | tekst      |

!["Configure polecenia" formularza przy użyciu szczegółów echo](./media/howto-set-up-template/commandsecho.png)

Po wybraniu **Zapisz** i **gotowe**, **Echo** polecenia pojawi się jako Kafelek i jest gotowa do użycia, aby wyświetlić urządzenia, gdy rzeczywiste urządzenie jest podłączone. Nazwy pól polecenia muszą być zgodne nazwy właściwości w odpowiednim kodzie urządzenia w kolejności poleceń pomyślne uruchomienie.


## <a name="rules"></a>Reguły

Reguły umożliwiają operatorów do monitorowania urządzeń w czasie zbliżonym do rzeczywistego. Reguły automatycznie wywołują akcje, takie jak wysyłanie wiadomości e-mail po wyzwoleniu reguły. Obecnie jest dostępna jeden typ reguły:

- **Reguła telemetrii**, który program jest wyzwalany w przypadku, gdy dane telemetryczne wybranego urządzenia przekracza określoną wartość progową. [Dowiedz się więcej o regułach telemetrii](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>Pulpit nawigacyjny

Pulpit nawigacyjny jest, gdy operator można przejść do informacji o urządzeniu. Jako Konstruktor możesz dodać Kafelki na tej stronie, aby zrozumieć, jak zachowuje się urządzenia operatorom pomocy. Wiele kafelków pulpitu nawigacyjnego można dodać do szablonu urządzenia. Można dodawać wiele różnych kafelków pulpitu nawigacyjnego, takich jak obraz, wykres liniowy, wykres słupkowy, kluczowy wskaźnik wydajności (KPI), ustawień i właściwości i etykiety.

Na przykład można dodać **ustawień i właściwości** Kafelek, aby wyświetlić wybór bieżące wartości ustawień i właściwości, wybierając **Edytuj szablon** i Kafelek z biblioteki:

!["Configure szczegóły urządzenia" formularz Szczegóły ustawień i właściwości](./media/howto-set-up-template/dashboardsettingsandpropertiesform.png)

Teraz gdy operator widoków pulpitu nawigacyjnego, można wyświetlić tego kafelka, który wyświetla właściwości i ustawienia urządzenia:

![Karta "Pulpit nawigacyjny" z wyświetlanych ustawień i właściwości dla kafelka](./media/howto-set-up-template/dashboardtile.png)

### <a name="add-an-azure-maps-location-in-the-dashboard"></a>Dodaj lokalizację usługi Azure Maps na pulpicie nawigacyjnym

Jeśli skonfigurowano właściwość location wcześniej w [utworzyć właściwość lokalizacji za pośrednictwem usługi Azure Maps](#create-a-location-property-through-azure-maps), możesz wizualizować lokalizacji za pomocą mapy na pulpicie nawigacyjnym urządzenia.

1. Na **pulpit nawigacyjny** zaznacz **Edytuj szablon**.

   ![Karta "Pulpit nawigacyjny" z po włączeniu trybu projektowania](./media/howto-set-up-template/locationcloudproperty4map.png)

2. Na pulpicie nawigacyjnym urządzenia wybierz **mapy** z biblioteki. 
3. Nadaj jej tytuł. W poniższym przykładzie tytułem lokalizację instalacji, następnie wybierz właściwość lokalizacji, które wcześniej skonfigurowano na karcie właściwości. W poniższym przykładzie **adres instalacji** jest zaznaczone.

   !["Configure mapy" formularza przy użyciu szczegółów tytułu i właściwości](./media/howto-set-up-template/locationcloudproperty5map.png)

4. Wybierz pozycję **Zapisz**. Kafelek mapa zawiera teraz wybranej lokalizacji. 

   ![Kafelek mapy z wybranej lokalizacji](./media/howto-set-up-template/locationcloudproperty6map.png) 

Można zmienić rozmiar mapy rozmiarowi żądaną. Teraz gdy operator wyświetla pulpit nawigacyjny, mogą zobaczyć wszystkie Kafelki pulpitu nawigacyjnego, które zostały skonfigurowane, w tym mapę lokalizacji.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz sposób konfigurowania szablonu urządzenia w aplikacji usługi Azure IoT Central, możesz wykonywać następujące czynności:

> [!div class="nextstepaction"]
> [Utwórz nową wersję szablonu urządzenia](howto-version-devicetemplate.md)
