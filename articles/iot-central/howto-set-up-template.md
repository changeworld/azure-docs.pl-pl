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
ms.openlocfilehash: 0bef8722ee51c916652e6964305f324341052341
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36754666"
---
# <a name="set-up-a-device-template"></a>Konfigurowanie szablonu urządzenia

Szablon urządzenia jest plan, który definiuje cechy i zachowania typu urządzenia, które łączy do aplikacji Microsoft Azure IoT centralnej.

Na przykład konstruktora można utworzyć szablon urządzenia IoT wentylator połączonych, który ma:

- Pomiar telemetrii temperatury

- Wentylator motocykla błąd zdarzeń miary

- Wentylator pomiaru stanu operacyjnego

- Wentylator szybkość

- Właściwość lokalizacji

- Reguły, które wysyłania alertów

- Pulpit nawigacyjny, który daje pełny widok o urządzeniu

Za pomocą tego szablonu urządzenia operator można tworzyć i Podłącz urządzenia rzeczywistych wentylator o nazwach takich jak **wentylator 1** i **2 wentylator**. Wszystkie te wentylatory mieć reguły pomiarów, ustawienia i właściwości oraz pulpit nawigacyjny, który użytkownicy aplikacji można monitorować i zarządzać nimi.

> [!NOTE]
> Tylko konstruktorów i Administratorzy mogą tworzenie, edytowanie i usuwanie szablonów urządzenia. Każdy użytkownik może tworzyć urządzeń w **Explorer urządzenia** strony z istniejących szablonów urządzenia.

## <a name="create-a-new-device-template"></a>Tworzenie nowego szablonu urządzenia

1. Przejdź do **Konstruktor aplikacji** strony.

2. Można utworzyć pustego szablonu, wybierz **Utwórz szablon urządzenia**, a następnie wybierz pozycję **niestandardowy**.

3. Wprowadź nazwę dla nowego szablonu urządzenia i wybierz polecenie **Utwórz**.

    ![Strona szczegółów urządzenia](./media/howto-set-up-template/devicedetailspage.png)

4. Teraz możesz teraz **szczegóły urządzenia** strony nowe symulowane urządzenie. Symulowane urządzenie jest utworzony automatycznie podczas tworzenia nowego szablonu urządzenia. Raporty danych, a można sterować tak samo jak rzeczywistego urządzenia.

Teraz wyglądać na poszczególnych kartach **szczegóły urządzenia** strony.

## <a name="measurements"></a>Miary

Pomiary są danych z urządzenia. Można dodać wiele miar do szablonu urządzenia, aby dopasować możliwości urządzenia. Obecnie dane telemetryczne i zdarzenia są typy obsługiwanych pomiarów.

- **Dane telemetryczne** pomiary są punkty danych liczbowych, urządzenie zbiera wraz z upływem czasu i są przedstawiane stały strumień. Na przykład temperatury.
- **Zdarzenie** pomiary są dane w momencie reprezentować coś o znaczeniu na urządzeniu. Zdarzenia mają ważność skojarzonych z nimi, które reprezentuje ważności zdarzenia. Na przykład błąd motocykla wentylatora
- **Stan** pomiarów reprezentuje stan urządzenia lub jego składniki w danym okresie czasu. Na przykład wentylator tryb, który może być zdefiniowana jako mająca działania i zatrzymana jako dwa możliwe stany.

### <a name="create-a-telemetry-measurement"></a>Tworzenie miar Telemetrii
Aby dodać nowe miary telemetrii, kliknij na **+ nowej miary** przycisku, który zostaje otwarty formularz z opcjami, aby wybrać typ miary. Wybierz **Telemetrii** i wprowadź szczegóły **utworzyć Telemetrii** formularza.

> [!NOTE]
> Gdy rzeczywiste urządzenie jest połączone, płać uwagę na nazwy miary raporty urządzenia. Nazwa musi dokładnie odpowiadać **Nazwa pola** miary.

Na przykład można dodać nowej miary telemetrii temperatury:

![Pomiary formularza](./media/howto-set-up-template/measurementsform.png)

Po wybraniu **zapisać**, **temperatury** pomiaru pojawi się na liście pomiarów i operatora można wyświetlić wizualizacji danych temperatury zbiera urządzenia.

![Pomiary wykresu](./media/howto-set-up-template/measurementsgraph.png)

### <a name="create-an-event-measurement"></a>Tworzenie miar zdarzeń
Aby dodać nowe miary zdarzenia, kliknij na **+ nowej miary** przycisku, który zostaje otwarty formularz z opcjami, aby wybrać typ miary. Wybierz **zdarzeń** i wprowadź szczegóły **Utwórz zdarzenie** formularza.

W tym formularzu podaj **Nazwa wyświetlana**, **Nazwa pola**i **ważność** zdarzenia. Możesz wybrać spośród trzech dostępnych poziomów ważności - **błąd**, **ostrzeżenie**, i **informacji**.  

Na przykład można dodać nowe zdarzenie "Wentylator motocykla błąd".

![Formularz pomiarów zdarzeń](./media/howto-set-up-template/eventmeasurementsform.png)

Po wybraniu **zapisać**, **błąd motocykla wentylator** pomiaru pojawi się na liście pomiarów i operatora można wyświetlić wizualizacji danych zdarzeń wysyła urządzenia.

![Wykres pomiarów zdarzeń](./media/howto-set-up-template/eventmeasurementschart.png)

Aby wyświetlić dodatkowe szczegóły dotyczące zdarzenia, kliknij ikonę zdarzeń na wykresie:

![Szczegóły zdarzenia pomiarów](./media/howto-set-up-template/eventmeasurementsdetail.png)


### <a name="create-a-state-measurement"></a>Utwórz wartość stanu
Aby dodać nowe miary stanu, kliknij na **+ nowej miary** przycisku, który zostaje otwarty formularz z opcjami, aby wybrać typ miary. Wybierz **stanu** i wprowadź szczegóły **stanu Utwórz** formularza.

W tym formularzu podaj **Nazwa wyświetlana**, **Nazwa pola**i **wartości** stanu. Każdy **wartość** ma także nazwę wyświetlaną, który będzie używany podczas wyświetlania wartości w przypadku wykresów i tabel.

Na przykład można dodać nowy stan 'Wentylator Mode', która ma dwa możliwe wartości, które urządzenie może wysyłać, **operacyjnego** i **zatrzymane**.

![Stan pomiarów formularza](./media/howto-set-up-template/statemeasurementsform.png)

Po wybraniu **zapisać**, **tryb wentylator** pomiaru stan zostanie wyświetlony na liście pomiarów i operator można wyświetlić wizualizacji danych o stanie wysyła urządzenia.

![Stan pomiarów wykresu](./media/howto-set-up-template/statemeasurementschart.png)

Jeśli w krótkim okresie urządzenie wyśle za dużo punktów danych, miara stanu będzie wyświetlana za pomocą innej wizualizacji, jak pokazano poniżej. Jeśli klikniesz wykres, wszystkie punkty danych w danym okresie zostaną wyświetlone w kolejności chronologicznej. Można również zawęzić zakres czasu, aby zobaczyć pomiaru wykreślić na wykresie.

![Szczegóły stanu pomiarów](./media/howto-set-up-template/statemeasurementsdetail.png)


## <a name="settings"></a>Ustawienia

Ustawienia sterowania urządzeniem. Umożliwiają one operatory aplikacji w celu Podaj dane wejściowe do urządzenia. Wiele ustawień można dodać do szablonu urządzenia, które są wyświetlane jako kafelki na **ustawienia** kartę dla operatorów do użycia. Istnieje sześć typów ustawień, które można dodać: numer, tekst daty, Przełącz, pobranie listy i etykiety sekcji.

> [!NOTE]
> Po podłączeniu urządzenia prawdziwe, należy zwrócić uwagę na nazwę ustawienia raporty dotyczące urządzeń. Nazwa musi dokładnie odpowiadać **Nazwa pola** ustawienia.

Ustawienia mogą być w jednym z trzech stanów. Te stany są zgłaszane przez urządzenie.

- **Zsynchronizowano**: urządzenie ma zmienione w celu uwzględnienia wartości ustawień.

- **Oczekujące**: urządzenie jest obecnie zmiana wartości ustawień.

- **Błąd**: urządzenie zwróciło błąd.

Na przykład można dodać nowe ustawienie szybkości wentylator:

![Ustawienia formularza](./media/howto-set-up-template/settingsform.png)

Po wybraniu **zapisać**, **wentylatorów szybkości** ustawienie jest wyświetlana jako Kafelek i jest gotowy do do użycia, aby zmienić szybkość wentylator urządzenia.

Po utworzeniu nowego kafelka można wypróbować nowe ustawienie. Po pierwsze, wyłącz tryb projektowania u góry po prawej części ekranu.

![Ustawienia](./media/howto-set-up-template/settingstile.png)

## <a name="properties"></a>Właściwości

Właściwości są urządzenia metadane skojarzone z urządzenia, takie jak lokalizacja urządzenia i numer seryjny. Można dodać wiele właściwości szablonu urządzenia, które są wyświetlane jako kafelki na **właściwości** kartę. Operator można określić wartości dla właściwości, podczas tworzenia nowego urządzenia i ich wartości tych można edytować w dowolnej chwili. Istnieje sześć typów można dodać właściwości: liczba, tekst, Data, Przełącz, właściwości urządzenia i etykiety.

Istnieją dwa typy właściwości:

- **Właściwości urządzenia** właściwości są raportowane przez urządzenie.
- **Właściwości aplikacji** właściwości przechowywane wyłącznie w aplikacji. Urządzenie nie ma informacji o właściwości aplikacji.

> [!NOTE]
> Dla właściwości urządzenia gdy rzeczywiste urządzenie jest połączone, płać uwagi do nazwy właściwości urządzenia raporty. Nazwa musi dokładnie odpowiadać **Nazwa pola** właściwości. Dla właściwości aplikacji nazwy pola można dowolnych znaków, tak długo, jak nazwa jest unikatowa w szablonie urządzenia.

Na przykład można dodać lokalizację urządzenia jako nową właściwość:

![Właściwości formularza](./media/howto-set-up-template/propertiesform.png)

Po wybraniu **zapisać**, Lokalizacja urządzenia jest wyświetlana jako Kafelek:

![Kafelek właściwości](./media/howto-set-up-template/propertiestile.png)

> [!NOTE]
> Po utworzeniu nowego kafelka, można zmienić wartości właściwości. Po pierwsze, wyłącz tryb projektowania u góry po prawej części ekranu.

### <a name="create-a-location-property-powered-by-azure-maps"></a>Utwórz właściwość lokalizacji obsługiwane przez usługę Azure mapy
Można przekazać geograficzne kontekstu danych lokalizacji w Azure IoT centralnej i mapowanie dowolnego szerokości geograficznej i współrzędne geograficzne ulicę lub po prostu współrzędne współrzędne geograficzne. Tej funkcji w usłudze Azure IoT centralnej jest obsługiwany przez usługę Azure mapy.

Istnieją dwa typy właściwości lokalizacji, które można dodać:
- **Lokalizacja jako właściwość aplikacji** której będą przechowywane wyłącznie w aplikacji. Urządzenie nie ma informacji o właściwości aplikacji.
- **Lokalizacja jako właściwość urządzenia** które będą zgłaszane przez urządzenie.

#### <a name="adding-location-as-an-application-property"></a>Dodawanie lokalizacji jako właściwości aplikacji 

Możesz utworzyć lokalizację mapy właściwości jako właściwość aplikacji za pomocą platformy Azure w aplikacji Azure IoT centralnej. Na przykład można dodać adres instalacji urządzenia. 

1. Przejdź do karty właściwości urządzenia; Upewnij się, że jest włączony tryb projektowania.

   ![Właściwość lokalizacji](./media/howto-set-up-template/locationcloudproperty1.png)

2. Na karcie właściwości kliknij lokalizację.
3. Opcjonalnie skonfiguruj nazwę wyświetlaną, nazwy pola i wartość początkową lokalizacji. 

   ![Lokalizacja właściwości formularza](./media/howto-set-up-template/locationcloudproperty2.png)

   Istnieją dwa obsługiwanych formatów, aby dodać lokalizację:
   - **Lokalizacja adresu**
   - **Lokalizacja jak współrzędnych.** 

4.  Kliknij pozycję **Zapisz**. 

    ![Pole właściwości lokalizacji](./media/howto-set-up-template/locationcloudproperty3.png)

Operator można teraz zaktualizować wartość lokalizacji w formularzu pole lokalizacji. 

#### <a name="adding-location-as-a-device-property"></a>Dodawanie lokalizacji jako właściwości urządzenia 

Można utworzyć właściwości location jako właściwość urządzenia zgłoszonego przez urządzenie.
Na przykład chcesz śledzić lokalizacji urządzenia.

1.  Przejdź do karty właściwości urządzenia; Upewnij się, że jest włączony tryb projektowania.

2.  Kliknij przycisk Właściwości urządzenia z biblioteki.

    ![Pole właściwości lokalizacji](./media/howto-set-up-template/locationdeviceproperty1.png)

3.  Skonfigurować nazwę wyświetlaną, nazwy pola i wybierz polecenie "Lokalizacja", jako typ danych. 

    > [!NOTE]
    > Nazwa pola musi dokładnie pasować do nazwy właściwości urządzenia raportów. 

    ![Pole właściwości lokalizacji](./media/howto-set-up-template/locationdeviceproperty2.png)

    ![Widok operator właściwość lokalizacji](./media/howto-set-up-template/locationdeviceproperty2.png)

Teraz, gdy skonfigurowano właściwości Twojej lokalizacji, można dodać mapy w celu wizualizowania lokalizacji na pulpicie nawigacyjnym urządzenia. Zobacz temat jak [Dodaj lokalizację mapy platformy Azure na pulpicie nawigacyjnym](howto-set-up-template.md#add-location-azure-map-in-dashboard).

## <a name="commands"></a>Polecenia

Polecenia są używane do zdalnego zarządzania urządzeniem. Umożliwiają one operatory aplikacji w taki sposób, aby natychmiast uruchomić polecenia na urządzeniu. Wiele poleceń można dodać do szablonu urządzenia, które są wyświetlane jako kafelki na **polecenia** kartę dla operatorów do użycia. Jako konstruktora urządzenia możesz swobodnie do definiowania polecenia zgodnie z wymaganiami.

Jak jest **polecenia** inny niż **ustawienie**? 

* Ustawienie: Jest konfiguracją, które chcesz zastosować do urządzenia, a urządzenie ma utrwalić takiej konfiguracji, dopóki nie zostanie zmieniony. Na przykład chcesz ustawić temperatury zamrażalni Twojego i ma ustawienie nawet wtedy, gdy zamrażalni ponownego uruchomienia. 

* Polecenie: Należy użyć poleceń Aby natychmiast uruchomić polecenie na urządzeniu zdalnie z IoTCentral, jeśli urządzenie nie jest połączony, a następnie limitu czasu polecenia, jak i kończy się niepowodzeniem. Na przykład chcesz ponowne uruchomienie/ponowne uruchomienie urządzenia.  

Polecenia po wykonaniu mogą być w jednym z trzech stanów w zależności od tego, czy urządzenie odebrano polecenie. 

Na przykład można dodać nowego polecenia echo:

![Formularz poleceń](./media/howto-set-up-template/commandsecho.png)

Po wybraniu **zapisać**, **Echo** polecenia jest wyświetlana jako Kafelek i jest gotowy do ma być używany do Echo urządzenia.

Po utworzeniu nowego kafelka można wypróbować nowe polecenie.

## <a name="rules"></a>Reguły

Reguły umożliwiają operatory monitorowanie urządzeń w najbliższym czasie rzeczywistym. Reguły automatycznie wywołują **akcje** takie jak wysyłanie wiadomości e-mail, gdy zasada wyzwala. Brak dostępnej jeden typ reguły dzisiaj:

- **Reguła telemetrii:** reguły telemetrii uaktywnia telemetrii wybranego urządzenia przekracza określoną wartość progową. Dowiedz się więcej o [reguły telemetrii](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>Pulpit nawigacyjny

Pulpit nawigacyjny jest, gdzie operator może przejść do można znaleźć informacji o urządzeniu. Jako konstruktora można dodać do tej strony, które pomagają operatory zrozumieć, jak urządzenie zachowuje się Kafelki. Wiele kafelka pulpitu nawigacyjnego można dodać do szablonu urządzenia. Istnieje sześć typów kafelka pulpitu nawigacyjnego można dodawać: obrazu wykresu, wykres słupkowy, kluczowego wskaźnika wydajności, ustawienia i właściwości linii i etykiety.

Na przykład można dodać **właściwości i ustawienia** Kafelek, aby pokazywać zaznaczenie bieżące wartości właściwości i ustawienia:

![Formularz szczegółów urządzenia pulpitu nawigacyjnego](./media/howto-set-up-template/dashboardsettingsandpropertiesform.png)

Teraz po operatorze widoków pulpitu nawigacyjnego, można wyświetlić tego kafelka, który wyświetla właściwości i ustawienia urządzenia:

![Kafelka pulpitu nawigacyjnego](./media/howto-set-up-template/dashboardtile.png)

### <a name="add-location-azure-map-in-dashboard"></a>Dodaj lokalizację mapy platformy Azure na pulpicie nawigacyjnym

Jeśli skonfigurowano właściwość lokalizacji jak kroki [utworzyć właściwość lokalizacji obsługiwane przez usługę Azure mapy](howto-set-up-template.md), będzie można zwizualizować lokalizacji przy użyciu mapy bezpośrednio w pulpicie nawigacyjnym urządzenia.

1.  Przejdź do karty Pulpit nawigacyjny urządzenia; Upewnij się, że jest włączony tryb projektowania.

2.  Na pulpicie nawigacyjnym urządzenia wybierz mapę z biblioteki. 

    ![Wybierz lokalizację Azure mapy pulpitu nawigacyjnego](./media/howto-set-up-template/locationcloudproperty4map.png)

3.  Nadaj tytuł, a następnie wybierz właściwość lokalizacji wcześniej skonfigurowanych jako część programu właściwości urządzenia.

    ![Konfigurowanie usługi Azure mapy lokalizacji pulpitu nawigacyjnego](./media/howto-set-up-template/locationcloudproperty5map.png)

4.  Zapisz i zobaczą mapy kafelka wyświetlanie wybranej lokalizacji. 

    ![Lokalizacja na pulpicie nawigacyjnym wizualizacji mapy platformy Azure](./media/howto-set-up-template/locationcloudproperty6map.png) 

    Będzie można zmienić rozmiar mapy w celu żądane rozmiary.

    Teraz po operatorze widoków pulpitu nawigacyjnego, będą mogli wyświetlać tego wszystkie kafelka pulpitu nawigacyjnego zostały skonfigurowane w tym miejscu Mapa!

    ![Pulpit nawigacyjny Azure mapy lokalizacji pulpitu nawigacyjnego](./media/howto-set-up-template/locationcloudproperty7map.png) 

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz sposobu konfigurowania szablonu urządzenia w aplikacji Azure IoT centralnej, Oto sugerowane następnego kroku:

> [!div class="nextstepaction"]
> [Utwórz nową wersję szablonu urządzenia](howto-version-devicetemplate.md)
