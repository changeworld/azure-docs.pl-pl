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
ms.openlocfilehash: c88b27edab71527b4b3eca71c00af2930c22f8cd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629239"
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
Tylko konstruktorów i Administratorzy mogą tworzenie, edytowanie i usuwanie szablonów urządzenia. Każdy użytkownik może tworzyć urządzeń w **Explorer urządzenia** strony z istniejących szablonów urządzenia.

## <a name="create-a-new-device-template"></a>Tworzenie nowego szablonu urządzenia

1. Przejdź do **Konstruktor aplikacji** strony.

1. Można utworzyć pustego szablonu, wybierz **Utwórz szablon urządzenia**, a następnie wybierz pozycję **niestandardowy**.

1. Wprowadź nazwę dla nowego szablonu urządzenia i wybierz polecenie **Utwórz**.

    ![Strona szczegółów urządzenia](./media/howto-set-up-template/devicedetailspage.png)

1. Teraz możesz teraz **szczegóły urządzenia** strony nowe symulowane urządzenie. Symulowane urządzenie jest utworzony automatycznie podczas tworzenia nowego szablonu urządzenia. Raporty danych, a można sterować tak samo jak rzeczywistego urządzenia.

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

> [!NOTE]
> Po utworzeniu nowego kafelka można wypróbować nowe ustawienie. Po pierwsze, wyłącz tryb projektowania u góry po prawej części ekranu:

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

## <a name="rules"></a>Reguły

Reguły umożliwiają operatory monitorowanie urządzeń w najbliższym czasie rzeczywistym. Reguły automatycznie wywołują **akcje** takie jak wysyłanie wiadomości e-mail, gdy zasada wyzwala. Brak dostępnej jeden typ reguły dzisiaj:

- **Reguła telemetrii:** reguły telemetrii uaktywnia telemetrii wybranego urządzenia przekracza określoną wartość progową. Dowiedz się więcej o [reguły telemetrii](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>Pulpit nawigacyjny

Pulpit nawigacyjny jest, gdzie operator może przejść do można znaleźć informacji o urządzeniu. Jako konstruktora można dodać do tej strony, które pomagają operatory zrozumieć, jak urządzenie zachowuje się Kafelki. Wiele kafelka pulpitu nawigacyjnego można dodać do szablonu urządzenia. Istnieje sześć typów kafelka pulpitu nawigacyjnego można dodawać: obrazu wykresu, wykres słupkowy, kluczowego wskaźnika wydajności, ustawienia i właściwości linii i etykiety.

Na przykład można dodać **właściwości i ustawienia** Kafelek, aby pokazywać zaznaczenie bieżące wartości właściwości i ustawienia:

![Formularz szczegółów urządzenia pulpitu nawigacyjnego](./media/howto-set-up-template/dashboardsettingsandpropertiesform.png)

Teraz po operatorze widoków pulpitu nawigacyjnego, można wyświetlić tego kafelka, który wyświetla właściwości i ustawienia urządzenia:

![Kafelka pulpitu nawigacyjnego](./media/howto-set-up-template/dashboardtile.png)

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz sposobu konfigurowania szablonu urządzenia w aplikacji Azure IoT centralnej, Oto sugerowane następnego kroku:

> [!div class="nextstepaction"]
> [Utwórz nową wersję szablonu urządzenia](howto-version-devicetemplate.md)
