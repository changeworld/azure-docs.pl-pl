---
title: Konfigurowanie szablonu urządzenia w aplikacji Azure IoT centralnej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować szablon urządzenia z pomiarów, ustawienia właściwości, reguł i pulpitu nawigacyjnego.
author: viv-liu
ms.author: viviali
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: f71d4c7dc94fedfd598ab87c51366ba9fb1f184a
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063039"
---
# <a name="set-up-a-device-template"></a>Konfigurowanie szablonu urządzenia

Szablon urządzenia jest plan, który definiuje cechy i zachowania typu urządzenia, które łączy do aplikacji Microsoft Azure IoT centralnej.

Na przykład konstruktora można utworzyć szablon urządzenie podłączone IoT wentylator, który ma:

- Pomiar telemetrii temperatury

- Wentylator motocykla błąd zdarzeń miary

- Wentylator pomiaru stanu operacyjnego

- Wentylator szybkość

- Właściwość lokalizacji

- Reguły, które wysyłania alertów

- Pulpit nawigacyjny, który umożliwi ogólną widoku urządzenia

Za pomocą tego szablonu urządzenia operator można tworzyć i Podłącz urządzenia rzeczywistych wentylator o nazwach takich jak **wentylator 1** i **2 wentylator**. Wszystkie te wentylatory ma pomiarów, ustawienia właściwości, reguł i pulpit nawigacyjny, który użytkownicy aplikacji można monitorować i zarządzać nimi.

> [!NOTE]
> Tylko konstruktorów i Administratorzy mogą tworzenie, edytowanie i usuwanie szablonów urządzenia. Każdy użytkownik może utworzyć urządzenia na **Explorer urządzenia** strony z istniejących szablonów urządzenia.

## <a name="create-a-device-template"></a>Tworzenie szablonu urządzenia

1. Przejdź do **Konstruktor aplikacji** strony.

2. Aby utworzyć pusty szablon, wybierz **Utwórz szablon urządzenia**, a następnie wybierz **niestandardowe**.

3. Wprowadź nazwę dla nowego szablonu urządzenia i wybierz **Utwórz**.

   ![Strona szczegółów urządzenia z "Lodówce" jako nazwy szablonu](./media/howto-set-up-template/devicedetailspage.png)

4. Teraz możesz teraz **szczegóły urządzenia** strony nowe symulowane urządzenie. Symulowane urządzenie jest utworzony automatycznie po utworzeniu szablonu urządzenia. Raporty danych, a można sterować tak samo jak rzeczywistego urządzenia.

Teraz wyglądać na poszczególnych kartach **szczegóły urządzenia** strony.

## <a name="measurements"></a>Miary

Pomiary są dane, które pochodzi z urządzenia. Można dodać wiele miar do szablonu urządzenia, aby dopasować możliwości urządzenia.

- **Dane telemetryczne** pomiary są punkty danych liczbowych, które urządzenia zbiera wraz z upływem czasu. Są one reprezentowane jako ciągłego strumienia. Przykładem jest temperatury.
- **Zdarzenie** pomiary są dane w momencie, które reprezentują coś o znaczeniu na urządzeniu. Poziom ważności reprezentuje ważności zdarzenia. Przykładem jest to błąd motocykla wentylator.
- **Stan** pomiarów reprezentuje stan urządzenia lub jego składniki w danym okresie czasu. Na przykład można zdefiniować tryb wentylator jako mający **operacyjnego** i **zatrzymane** jako dwa możliwe stany.

### <a name="create-a-telemetry-measurement"></a>Tworzenie miar telemetrii
Aby dodać nowe miary telemetrii, zaznacz **+ nowej miary** przycisku. Wybierz **Telemetrii** jako wartość wpisz, a następnie wprowadź szczegóły **utworzyć Telemetrii** formularza.

> [!NOTE]
> Po podłączeniu urządzenia prawdziwe, należy zwrócić uwagę na nazwy miary, która raportuje urządzenia. Nazwa musi dokładnie odpowiadać **Nazwa pola** wpis dla miary.

Na przykład można dodać nowej miary telemetrii temperatury:

!["Tworzenie Telemetrii" formularza w szczegółowych informacjach dotyczących pomiaru temperatury](./media/howto-set-up-template/measurementsform.png)

Po wybraniu **zapisać**, **temperatury** pomiaru pojawi się na liście pomiarów. Operator można wyświetlić wizualizacji danych temperatury, które urządzenia są zbierane.

![Wykres pomiaru.](./media/howto-set-up-template/measurementsgraph.png)

### <a name="create-an-event-measurement"></a>Tworzenie miar zdarzeń
Aby dodać nowe miary zdarzenia, wybierz **+ nowej miary** przycisku. Wybierz **zdarzeń** jako wartość wpisz, a następnie wprowadź szczegóły **Utwórz zdarzenie** formularza.

Podaj **Nazwa wyświetlana**, **Nazwa pola**, i **ważność** szczegóły zdarzenia. Możesz wybrać spośród trzech dostępnych poziomów ważności: **błąd**, **ostrzeżenie**, i **informacji**.  

Na przykład można dodać nowego **błąd motocykla wentylator** zdarzeń.

!["Utwórz zdarzenie" formularza z szczegóły zdarzenia motocykla wentylatora](./media/howto-set-up-template/eventmeasurementsform.png)

Po wybraniu **zapisać**, **błąd motocykla wentylator** pomiaru pojawi się na liście pomiarów. Operator można wyświetlić wizualizacji danych zdarzenia, który wysyła urządzenia.

![Wykres pomiaru zdarzeń](./media/howto-set-up-template/eventmeasurementschart.png)

Aby wyświetlić szczegółowe informacje o zdarzeniu, wybierz ikonę zdarzeń na wykresie:

![Szczegóły zdarzenia "Error motocykla wentylator"](./media/howto-set-up-template/eventmeasurementsdetail.png)


### <a name="create-a-state-measurement"></a>Utwórz wartość stanu
Aby dodać nowe miary stanu, zaznacz **+ nowej miary** przycisku. Wybierz **stanu** jako wartość wpisz, a następnie wprowadź szczegóły **stanu Utwórz** formularza.

Podaj szczegóły **Nazwa wyświetlana**, **Nazwa pola**, i **wartości** stanu. Każda wartość może mieć też nazwę wyświetlaną, który będzie używany, gdy wartość znajduje się w przypadku wykresów i tabel.

Na przykład można dodać nowego **tryb wentylator** stanu, która ma dwa możliwe wartości, które mogą przesyłać dane urządzenie, **operacyjnego** i **zatrzymane**.

![Formularz "Edycja stan" w szczegółowych informacjach dotyczących trybu wentylatora](./media/howto-set-up-template/statemeasurementsform.png)

Po wybraniu **zapisać**, **tryb wentylator** pomiaru stan zostanie wyświetlony na liście pomiarów. Operator można wyświetlić wizualizacji danych stanu, który wysyła urządzenia.

![Wykres stanu pomiaru.](./media/howto-set-up-template/statemeasurementschart.png)

Jeśli urządzenie wysyła zbyt wiele punktów danych w małych czas trwania, pomiaru stanu pojawi się z różnych visual, jak pokazano na poniższym zrzucie ekranu. Jeśli klikniesz przycisk na wykresie, wszystkich punktów danych w tym przedziale czasu są wyświetlane w kolejności chronologicznej. Można również zawęzić zakres czasu, aby zobaczyć pomiaru wykreślić na wykresie.

![Szczegóły dotyczące "Statyczne tryb wentylator" stanu pomiaru.](./media/howto-set-up-template/statemeasurementsdetail.png)


## <a name="settings"></a>Ustawienia

Ustawienia sterowania urządzeniem. Umożliwiają one operatory aplikacji w celu Podaj dane wejściowe do urządzenia. Wiele ustawień można dodać do szablonu urządzenia, które są wyświetlane jako kafelki na **ustawienia** kartę dla operatorów do użycia. Można dodać sześciu typy ustawień: numer, tekst daty, Przełącz, pobranie listy i etykiety sekcji.

> [!NOTE]
> Po podłączeniu urządzenia prawdziwe, należy zwrócić uwagę na nazwy ustawienia, które urządzenie raportuje. Nazwa musi dokładnie odpowiadać **Nazwa pola** wpis dla ustawień.

Ustawienia mogą być w jednym z trzech stanów. Urządzenie raportuje te stany.

- **Zsynchronizowano**: urządzenie ma zmienione w celu uwzględnienia wartości ustawień.

- **Oczekujące**: urządzenie jest obecnie zmiana wartości ustawień.

- **Błąd**: urządzenie zwróciło błąd.

Na przykład można dodać nowe ustawienie szybkości wentylator:

!["Konfigurowanie numer" formularz zawierający szczegółowe informacje o ustawieniach szybkości](./media/howto-set-up-template/settingsform.png)

Po wybraniu **zapisać**, **szybkości wentylator** ustawienie jest wyświetlana jako Kafelek i jest gotowa do użycia, aby zmienić szybkość wentylator urządzenia.

Po utworzeniu kafelka można wypróbować nowe ustawienie. Po pierwsze Wyłącz tryb projektowania w prawej górnej części ekranu.

![Kartę "Ustawienia" z przełącznikiem "Tryb projektowania" kafelka](./media/howto-set-up-template/settingstile.png)

## <a name="properties"></a>Właściwości

Właściwości są skojarzone z urządzenia, takie jak lokalizacja urządzenia i numer seryjny metadane urządzenia. Można dodać wiele właściwości w szablonie urządzenia, które są wyświetlane jako kafelki na **właściwości** kartę. Operator można określić wartości dla właściwości, podczas tworzenia urządzenia i ich wartości tych można edytować w dowolnej chwili. Można dodać typy sześć właściwości: liczba, tekst, Data, Przełącz, właściwości urządzenia i etykiety.

Istnieją dwie kategorie właściwości:

- **Urządzenie** właściwości, które urządzenia raportów.
- **Aplikacja** właściwości, które są przechowywane wyłącznie w aplikacji. Urządzenie nie rozpoznaje właściwości aplikacji.

> [!NOTE]
> Dla właściwości urządzenia gdy rzeczywista urządzenie jest podłączone, należy zwrócić uwagę na nazwę właściwości, która raportuje urządzenia. Nazwa musi dokładnie odpowiadać **Nazwa pola** wpis dla właściwości. Dla właściwości aplikacji nazwy pola można dowolnych znaków, tak długo, jak nazwa jest unikatowa w szablonie urządzenia.

Na przykład można dodać lokalizację urządzenia jako nową właściwość:

![Formularz "Konfigurowanie tekst" na karcie "Właściwości"](./media/howto-set-up-template/propertiesform.png)

Po wybraniu **zapisać**, Lokalizacja urządzenia jest wyświetlana jako Kafelek:

![Lokalizacja kafelka](./media/howto-set-up-template/propertiestile.png)

Po utworzeniu kafelka, można zmienić wartości właściwości. Po pierwsze Wyłącz tryb projektowania w prawym górnym części ekranu.

### <a name="create-a-location-property-through-azure-maps"></a>Utwórz właściwość lokalizacji przy użyciu mapy usługi Azure
Można przekazać geograficzne kontekstu danych lokalizacji w Azure IoT centralnej i mapowanie dowolnego współrzędne geograficzne współrzędne ulicy. Lub możesz po prostu współrzędne geograficzne współrzędnych mapy. Mapy Azure umożliwia tę możliwość centralnego IoT.

Można dodać dwa typy właściwości lokalizacji:
- **Lokalizacja jako właściwość aplikacji**, który jest przechowywany wyłącznie w aplikacji. Urządzenie nie rozpoznaje właściwości aplikacji.
- **Lokalizacja jako właściwość urządzenia**, która urządzenia.

#### <a name="add-location-as-an-application-property"></a>Dodaj lokalizację jako właściwości aplikacji 
Można utworzyć właściwości location jako właściwość aplikacji przy użyciu mapy usługi Azure w aplikacji IoT centralnego. Na przykład można dodać adres instalacji urządzenia. 

1. Na **właściwości** karcie, upewnij się, że **tryb projektowania** jest **na**.

   ![Karta "Właściwości" z włączony tryb projektowania](./media/howto-set-up-template/locationcloudproperty1.png)

2. W bibliotece, wybierz **lokalizacji**.
3. Skonfiguruj **Nazwa wyświetlana**, **Nazwa pola**i (opcjonalnie) **wartości początkowej** lokalizacji. 

   !["Skonfiguruj lokalizację" formularza w szczegółowych informacjach dotyczących lokalizacji](./media/howto-set-up-template/locationcloudproperty2.png)

   Istnieją dwa obsługiwanych formatów, aby dodać lokalizację:
   - **Lokalizacja adresu**
   - **Lokalizacja jak współrzędnych.** 

4. Wybierz pozycję **Zapisz**. 

   ![Właściwość lokalizacji o dodany adres instalacji](./media/howto-set-up-template/locationcloudproperty3.png)

Operator można teraz zaktualizować wartość lokalizacji w formularzu pole lokalizacji. 

#### <a name="add-location-as-a-device-property"></a>Dodaj lokalizację jako właściwości urządzenia 

Można utworzyć właściwości location jako urządzenie zgłasza właściwości urządzenia. Na przykład, jeśli chcesz śledzić lokalizacji urządzenia:

1. Na **właściwości** karcie, upewnij się, że **tryb projektowania** jest **na**.

   ![Karta "Właściwości" z włączony tryb projektowania](./media/howto-set-up-template/locationdeviceproperty1.png)

2. Wybierz **właściwości urządzenia** z biblioteki.
3. Skonfiguruj nazwę wyświetlaną i nazwy pola i wybierz **lokalizacji** jako typ danych. 

   > [!NOTE]
   > Nazwa pola musi dokładnie odpowiadać nazwę właściwości, która raportuje urządzenia. 

   !["Konfigurowanie właściwości urządzenia" formularza w szczegółowych informacjach dotyczących lokalizacji](./media/howto-set-up-template/locationdeviceproperty2.png)

Po skonfigurowaniu właściwości Twojej lokalizacji, możesz [Dodaj mapę do wizualizacji lokalizacji na pulpicie nawigacyjnym urządzenia](#add-an-azure-maps-location-in-the-dashboard).

## <a name="commands"></a>Polecenia

Polecenia są używane do zdalnego zarządzania urządzeniem. Umożliwiają one operatory aplikacji w taki sposób, aby natychmiast uruchomić polecenia na urządzeniu. Wiele poleceń można dodać do szablonu urządzenia, które są wyświetlane jako kafelki na **polecenia** kartę dla operatorów do użycia. Jako producenta urządzenia masz możliwość definiowania polecenia zgodnie z wymaganiami.

Jak polecenie różni się od ustawienia? 

* **Ustawienie**: to ustawienie jest konfiguracji, który chcesz zastosować do urządzenia, a urządzenie ma utrwalić takiej konfiguracji, dopóki nie zostanie zmieniony. Na przykład chcesz ustawić temperatury zamrażalni Twojego i ma ustawienie nawet wtedy, gdy zamrażalni ponownego uruchomienia. 

* **Polecenie**: możesz używać poleceń, aby natychmiast uruchomić polecenie na urządzeniu zdalnie z centralnego IoT. Jeśli urządzenie nie jest podłączone, polecenie upłynie limit czasu i nie powiedzie się. Na przykład chcesz ponownie uruchomić urządzenie.  

Po uruchomieniu polecenia, można w jednym z trzech stanów w zależności od tego, czy urządzenie odebrano polecenie. 

Na przykład można dodać nowego **Echo** polecenia:

!["Konfigurowanie polecenia" formularza w szczegółowych informacjach dotyczących echo](./media/howto-set-up-template/commandsecho.png)

Po wybraniu **zapisać**, **Echo** polecenie jest wyświetlana jako Kafelek i jest gotowa do użycia, aby wyświetlić urządzenia.

Po utworzeniu kafelka można wypróbować nowe polecenie.

## <a name="rules"></a>Reguły

Reguły umożliwiają operatory monitorowanie urządzeń w najbliższym czasie rzeczywistym. Reguły automatycznie wywołują akcje, takie jak wysyłanie wiadomości e-mail, gdy reguła zostanie wywołany. Obecnie jest dostępny jeden typ reguły:

- **Reguła telemetrii**, który jest wyzwalane, gdy telemetrii wybranego urządzenia przekracza określoną wartość progową. [Dowiedz się więcej o telemetrii reguły](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>Pulpit nawigacyjny

Pulpit nawigacyjny jest, gdzie operator może przejść do można znaleźć informacji o urządzeniu. Jako konstruktora można dodać kafelków na tej stronie ułatwiających zrozumieć, jak zachowuje się urządzenia. Wiele kafelka pulpitu nawigacyjnego można dodać do szablonu urządzenia. Można dodać sześć typów kafelka pulpitu nawigacyjnego: obrazu wykresu, wykres słupkowy, kluczowego wskaźnika wydajności, ustawienia i właściwości linii i etykiety.

Na przykład można dodać **właściwości i ustawienia** Kafelek, aby pokazywać zaznaczenie bieżące wartości właściwości i ustawienia:

!["Konfigurowanie szczegółowych informacji o urządzeniu" formularza w szczegółowych informacjach dotyczących ustawień i właściwości](./media/howto-set-up-template/dashboardsettingsandpropertiesform.png)

Teraz po operatorze widoków pulpitu nawigacyjnego, można wyświetlić tego kafelka, który wyświetla właściwości i ustawienia urządzenia:

![Karta "Pulpitu nawigacyjnego" z ustawień wyświetlanych i właściwości kafelka](./media/howto-set-up-template/dashboardtile.png)

### <a name="add-an-azure-maps-location-in-the-dashboard"></a>Dodaj lokalizację mapy Azure na pulpicie nawigacyjnym

Jeśli skonfigurowano lokalizacji właściwość wcześniej w [utworzyć właściwość lokalizacji przy użyciu mapy usługi Azure](#create-a-location-property-through-azure-maps), można zwizualizować lokalizacji przy użyciu mapy na pulpicie nawigacyjnym urządzenia.

1. Na **pulpitu nawigacyjnego** karcie, upewnij się, że **tryb projektowania** jest **na**.

   ![Karta "Pulpitu nawigacyjnego" z włączony tryb projektowania](./media/howto-set-up-template/locationcloudproperty4map.png)

2. Na pulpicie nawigacyjnym urządzenia, wybierz **mapy** z biblioteki. 
3. Przypisz tytuł i wybrać właściwość lokalizacji, który wcześniej skonfigurowany jako część właściwości urządzenia.

   !["Konfigurowanie mapy" formularz zawierający szczegółowe informacje dotyczące tytułu i właściwości](./media/howto-set-up-template/locationcloudproperty5map.png)

4. Wybierz pozycję **Zapisz**. Na kafelku mapy są obecnie wyświetlane w wybranej lokalizacji. 

   ![Kafelek mapy z wybranej lokalizacji](./media/howto-set-up-template/locationcloudproperty6map.png) 

Aby żądane rozmiary, można zmienić rozmiar mapy.

Teraz operator widoków pulpitu nawigacyjnego, widzą wszystkie skonfigurowanych, wraz z mapą lokalizacji kafelka pulpitu nawigacyjnego.

![Kafelki na pulpicie nawigacyjnym](./media/howto-set-up-template/locationcloudproperty7map.png) 

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz, jak skonfigurować szablon urządzenia w aplikacji Azure IoT centralnej, można:

> [!div class="nextstepaction"]
> [Utwórz nową wersję szablonu urządzenia](howto-version-devicetemplate.md)
