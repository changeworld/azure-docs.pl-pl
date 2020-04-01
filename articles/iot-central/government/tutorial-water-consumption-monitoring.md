---
title: 'Samouczek: Tworzenie aplikacji do monitorowania zużycia wody za pomocą usługi Azure IoT Central'
description: 'Samouczek: Dowiedz się, jak utworzyć aplikację do monitorowania zużycia wody przy użyciu szablonów aplikacji Azure IoT Central.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 7f00947504e5c6355379ce1e400fd2325016e05a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77122063"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-with-azure-iot-central"></a>Samouczek: Tworzenie aplikacji do monitorowania zużycia wody za pomocą usługi Azure IoT Central

W tym samouczku pokazano, jak utworzyć aplikację do monitorowania zużycia wody usługi Azure IoT Central przy użyciu szablonu aplikacji monitorowania zużycia wody usługi Azure IoT Central.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Użyj szablonu monitorowania zużycia wody w usłudze Azure IoT Central, aby utworzyć aplikację do monitorowania zużycia wody.
> * Eksploruj i dostosuj pulpit nawigacyjny operatora.
> * Poznaj szablony urządzeń.
> * Eksploruj symulowane urządzenia.
> * Eksploruj i konfiguruj reguły.
> * Konfigurowanie zadań.
> * Dostosuj znakowanie aplikacji za pomocą białego etykietowania.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz ją utworzyć na [stronie rejestracji platformy Azure.](https://aka.ms/createazuresubscription)

## <a name="create-a-water-consumption-monitoring-app-with-azure-iot-central"></a>Tworzenie aplikacji do monitorowania zużycia wody za pomocą usługi Azure IoT Central

W tej sekcji użyj szablonu monitorowania zużycia wody usługi Azure IoT Central, aby utworzyć aplikację do monitorowania zużycia wody w usłudze Azure IoT Central.

Aby utworzyć nową aplikację do monitorowania zużycia wody w usłudze Azure IoT Central:

1. Przejdź do witryny [głównej witryny głównej usługi Azure IoT Central.](https://aka.ms/iotcentral)

    Jeśli masz subskrypcję platformy Azure, zaloguj się przy użyciu poświadczeń, których używasz, aby uzyskać do niej dostęp. W przeciwnym razie zaloguj się przy użyciu konta Microsoft.

    ![Określanie konta organizacji](media/tutorial-waterconsumptionmonitoring/sign-in.png)

1. Wybierz **pozycję Buduj** w lewym okienku i wybierz kartę **Rząd.** Na stronie **Rząd** jest wyświetlanych kilka szablonów aplikacji rządowych.

   ![Tworzenie szablonów aplikacji rządowych](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. Wybierz szablon aplikacji **monitorowania zużycia wody.**
Ten szablon zawiera przykładowy szablon urządzenia do zużywania wody, symulowane urządzenie, pulpit nawigacyjny operatora i wstępnie skonfigurowane reguły monitorowania.

1. Wybierz **pozycję Utwórz aplikację,** aby otworzyć formularz Nowe tworzenie **aplikacji** z następującymi polami:
    * **Nazwa aplikacji:** Domyślnie aplikacja używa *monitorowania zużycia wody,* po którym następuje unikatowy ciąg identyfikatorów generowany przez usługę Azure IoT Central. Opcjonalnie wybierz przyjazną nazwę aplikacji. Nazwę aplikacji można zmienić później.
    * **ADRES URL:** Usługa Azure IoT Central automatyczniegeneruje adres URL na podstawie nazwy aplikacji. Możesz zaktualizować adres URL do swoich potrzeb. Możesz też później zmienić adres URL.
    * Jeśli masz subskrypcję platformy Azure, wprowadź **katalog,** **subskrypcję platformy Azure**i informacje o **lokalizacji.** Jeśli nie masz subskrypcji, możesz wybrać **opcję 7-dniowego bezpłatnego okresu próbnego** i uzupełnić wymagane informacje kontaktowe.

    Aby uzyskać więcej informacji o katalogach i subskrypcjach, zobacz [Tworzenie szybkiego startu aplikacji](../core/quick-deploy-iot-central.md).

1. Wybierz pozycję **Utwórz** w dolnej części strony.

    ![Nowa strona aplikacji Usługi Azure IoT Central](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

    ![Strona informacji o centralnych rozliczeniach usługi Azure IoT](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring-billinginfo.png)

Aplikacja do monitorowania zużycia wody została utworzona przy użyciu szablonu monitorowania zużycia wody w usłudze Azure IoT Central.

Aplikacja do monitorowania zużycia wody jest wyposażona w wstępnie skonfigurowane:

* Przykładowe pulpity nawigacyjne operatora.
* Próbka wstępnie zdefiniowanego przepływu wody i szablonów urządzeń zaworów.
* Symulowany przepływ wody i inteligentne urządzenia zaworowe.
* Zasady i miejsca pracy.
* Przykładowe znakowanie przy użyciu białego etykietowania.

Jest to aplikacja i można ją zmodyfikować w dowolnym momencie. Teraz eksplorujmy aplikację i dokonajmy pewnych dostosowań.

## <a name="explore-and-customize-the-operator-dashboard"></a>Eksplorowanie i dostosowywanie pulpitu nawigacyjnego operatora

Po utworzeniu aplikacji zostanie otwarta próbka **Szerokiego świata na pulpicie nawigacyjnym zużycia wody.**

   ![Pulpit nawigacyjny monitorowania zużycia wody](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

Jako konstruktor możesz tworzyć i dostosowywać widoki na pulpicie nawigacyjnym dla operatorów. Przejrzyjmy pulpit nawigacyjny, zanim spróbujesz go dostosować.

> [!NOTE]
> Wszystkie dane wyświetlane na pulpicie nawigacyjnym są oparte na symulowanych danych urządzenia, które omówimy w następnej sekcji.
  
Pulpit nawigacyjny składa się z różnych rodzajów kafelków:

* **Wide World Water Utility płytki obrazu**: Pierwszy kafelek w desce rozdzielczej jest płytka obrazu fikcyjnego przedsiębiorstwa wodociągowego Wide World Water. Kafelek można dostosować, wstawiając własny obraz lub usuwając go.
* **Średni przepływ wody płytki KPI**: Kafelek KPI jest skonfigurowany do wyświetlania jako przykład *średniej w ciągu ostatnich 30 minut*. Kafelek kluczowego wskaźnika wydajności można dostosować i ustawić na inny typ i zakres czasu.
* **Płytki sterujące urządzenia:** Płytki te obejmują **zawór Zamknij,** **Zawór otwarty**i Ustawić płytki **położenia zaworu.** Wybranie poleceń powoduje przejście do strony polecenia symulowanego urządzenia. W usłudze Azure IoT Central *polecenie* jest typem *możliwości urządzenia.* Omówimy tę koncepcję w dalszej części sekcji "Szablon urządzenia" w tym samouczku.
* **Mapa obszaru dystrybucji wody:** Mapa używa usługi Azure Maps, którą można skonfigurować bezpośrednio w usłudze Azure IoT Central. Kafelek mapy wyświetla lokalizację urządzenia. Umieść wskaźnik myszy na mapie i wypróbuj kontrolki nad mapą, takie jak *powiększanie,* *pomniejszanie*lub *rozwijanie.*

    ![Mapa pulpitu nawigacyjnego monitorowania zużycia wody](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* **Wykres linii przepływu wody** i **schemat linii warunków środowiskowych:** Można wizualizować jedną lub wiele telemetrii urządzeń wykreślonych jako wykres liniowy w żądanym zakresie czasu.
* **Wykres mapy cieplnej średniego ciśnienia zaworu:** Można wybrać typ wizualizacji mapy cieplnej danych telemetrycznych urządzenia, które mają być dystrybuowane w zakresie czasu z indeksem kolorów.
* **Resetowanie kafelka zawartości progów alertów:** można dołączyć kafelki zawartości wezwania do działania i osadzić łącze do strony akcji. W takim przypadku próg alertu resetowania prowadzi do **zadania**aplikacji , gdzie można uruchomić aktualizacje właściwości urządzenia. Tę opcję omówimy w dalszej części sekcji "Konfigurowanie zadań" w tym samouczku.
* **Płytki właściwości:** Na desce rozdzielczej wyświetlane są **informacje operacyjne valve,** **progi alertów przepływu**oraz kafelki informacji o **konserwacji.**

### <a name="customize-the-dashboard"></a>Dostosowywanie pulpitu nawigacyjnego

Jako konstruktor możesz dostosować widoki na pulpicie nawigacyjnym dla operatorów.

1. Wybierz **edytuj,** aby dostosować **pulpit nawigacyjny zużycie wody na całym świecie**. Pulpit nawigacyjny można dostosować, wybierając menu **Edycja.** Po przejściu pulpitu nawigacyjnego w **trybie edycji** można dodać nowe kafelki lub skonfigurować je.

     ![Edytuj pulpit nawigacyjny](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

1. Wybierz **+ Nowy,** aby utworzyć nowy pulpit nawigacyjny i skonfigurować go od podstaw. Możesz mieć wiele pulpitów nawigacyjnych i można poruszać się między pulpitami nawigacyjnymi w menu pulpitu nawigacyjnego.

## <a name="explore-the-device-template"></a>Eksplorowanie szablonu urządzenia

Szablon urządzenia w usłudze Azure IoT Central definiuje możliwości urządzenia, które może być telemetrią, właściwością lub poleceniem. Jako konstruktor możesz zdefiniować jeden lub więcej szablonów urządzeń w usłudze Azure IoT Central, które reprezentują możliwości urządzeń, które będą łączyć.

Aplikacja do monitorowania zużycia wody jest wyposażona w dwa szablony urządzeń referencyjnych, które reprezentują *przepływomierz* i inteligentne urządzenie *zaworowe.*

Aby wyświetlić szablon urządzenia:

1. Wybierz **szablony urządzeń** w lewym okienku aplikacji w usłudze Azure IoT Central. Na liście **Szablony urządzeń** zobaczysz dwa szablony urządzeń, **Inteligentny zawór** i **przepływomierz**.

   ![Szablon urządzenia](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

1. Wybierz szablon urządzenia **przepływomierza** i zapoznaj się z możliwościami urządzenia.

     ![Przepływomierz szablonu urządzenia](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customize-the-device-template"></a>Dostosowywanie szablonu urządzenia

Aby dostosować szablon urządzenia:

1. Przejdź do **menu Dostosuj** w menu **Szablony urządzeń.**
1. Znajdź `Temperature` typ telemetrii.
1. Zaktualizuj `Temperature` `Reported temperature` **nazwę wyświetlaną** do .
1. Zaktualizuj jednostkę miary lub ustaw **wartość Min** i **Max**.
1. Wybierz **pozycję Zapisz,** aby zapisać wszelkie zmiany.

### <a name="add-a-cloud-property"></a>Dodawanie właściwości w chmurze

1. Przejdź do **usługi Właściwości chmury** w menu **Szablony urządzeń.**
1. Dodaj nową właściwość chmury, wybierając **opcję + Dodaj właściwość chmury**.
    W usłudze Azure IoT Central można dodać właściwość, która jest odpowiednia dla urządzenia. Na przykład właściwością chmury może być próg alertów specyficzny dla obszaru instalacji, informacji o zasobach lub innych informacji o konserwacji.
1. Wybierz **pozycję Zapisz,** aby zapisać wszelkie zmiany.

### <a name="views"></a>Widoki

Szablon urządzenia monitora zużycia wody zawiera wstępnie zdefiniowane widoki. Eksploruj widoki i możesz dokonywać aktualizacji. Widoki definiują sposób, w jaki operatorzy widzą dane urządzenia, ale także właściwości chmury wejściowej.

  ![Widoki szablonu urządzenia](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>Publikowanie

Jeśli wprowadzono jakiekolwiek zmiany, upewnij się, **że opublikowanie** szablonu urządzenia.

### <a name="create-a-new-device-template"></a>Tworzenie nowego szablonu urządzenia

Wybierz **+ Nowy,** aby utworzyć nowy szablon urządzenia i postępuj zgodnie z procesem tworzenia.
Można utworzyć niestandardowy szablon urządzenia od podstaw lub wybrać szablon urządzenia z wykazu urządzeń platformy Azure.

## <a name="explore-simulated-devices"></a>Eksplorowanie symulowanych urządzeń

W usłudze Azure IoT Central można utworzyć symulowane urządzenia do testowania szablonu urządzenia i aplikacji. Aplikacja do monitorowania zużycia wody ma dwa symulowane urządzenia mapowane na **przepływomierz** i szablony urządzeń **Smart Valve.**

### <a name="view-the-devices"></a>Wyświetlanie urządzeń

1. Wybierz **pozycję Urządzenia** > **Wszystkie urządzenia** w lewym okienku.

   ![Okienko Wszystkie urządzenia](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

1. Wybierz **inteligentny zawór 1**.

    ![Inteligentny zawór 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

1. Na karcie **Polecenia** można zobaczyć trzy polecenia urządzenia (**Zamknij zawór,** **Otwarty zawór**i **Ustaw pozycję zaworu),** które są możliwościami zdefiniowanymi w szablonie urządzenia **Smart Valve.**

1. Zapoznaj się z kartą **Właściwości urządzenia** i pulpit nawigacyjny **urządzenia.**

> [!NOTE]
> Należy zauważyć, że wszystkie karty są skonfigurowane z widoków szablonu urządzenia.

### <a name="add-new-devices"></a>Dodawanie nowych urządzeń

Dodaj nowe urządzenia, wybierając **pozycję + Nowy** na karcie **Urządzenia.**

## <a name="explore-and-configure-rules"></a>Eksplorowanie i konfigurowanie reguł

W usłudze Azure IoT Central można utworzyć reguły do automatycznego monitorowania danych telemetrycznych urządzenia i wyzwalania akcji, gdy spełnione są co najmniej jedno warunki. Akcje mogą obejmować wysyłanie powiadomień e-mail lub wyzwalanie akcji automatyzacji zasilania firmy Microsoft lub akcji elementu webhook w celu wysyłania danych do innych usług.

Utworzona aplikacja do monitorowania zużycia wody ma trzy wstępnie skonfigurowane reguły.

### <a name="view-rules"></a>Wyświetlanie reguł

1. Wybierz **pozycję Reguły** w lewym okienku.

   ![Okienko Reguły](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

1. Wybierz **alert wysokiego pH**, który jest jedną ze wstępnie skonfigurowanych reguł w aplikacji.

     ![Alert wysokiego pH](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    Reguła `High flow alert` jest skonfigurowana do `Acidity (pH)` sprawdzania `greater than` `Max flow threshold`stanu jest . Maksymalny próg przepływu to właściwość chmury zdefiniowana w szablonie urządzenia **Smart Valve.** Wartość `Max flow threshold` jest ustawiana na wystąpienie urządzenia.

Teraz utwórzmy akcję e-mail.

Aby dodać akcję do reguły:

1. Wybierz **+ E-mail**.
1. Wprowadź **alert wysokiego pH** jako przyjazną **nazwę wyświetlaną** dla akcji.
1. Wprowadź adres e-mail skojarzony z kontem Usługi Azure IoT Central w **obszarze Do**.
1. Opcjonalnie wprowadź notatkę, która ma być uwzględnina w tekście wiadomości e-mail.
1. Wybierz **pozycję Gotowe,** aby ukończyć akcję.
1. Wybierz **pozycję Zapisz,** aby zapisać i aktywować nową regułę.

W ciągu kilku minut powinieneś otrzymać wiadomość e-mail po spełnionej konfiguracji.

> [!NOTE]
> Aplikacja wysyła wiadomość e-mail za każdym razem, gdy warunek jest spełniony. Wybierz **opcję Wyłącz,** aby wyłączyć regułę, aby zatrzymać odbieranie wiadomości e-mail z reguły automatycznej.
  
Aby utworzyć nową regułę:

* Wybierz **+ Nowy** na karcie **Reguły** w lewym okienku.

## <a name="configure-jobs"></a>Konfigurowanie zadań

W usłudze Azure IoT Central zadania umożliwiają wyzwalanie aktualizacji urządzeń lub właściwości w chmurze na wielu urządzeniach. Oprócz właściwości można również użyć zadań do wyzwalania poleceń urządzenia na wielu urządzeniach. Usługa Azure IoT Central automatyzuje przepływ pracy.

1. Wybierz **pozycję Zadania** w lewym okienku.
1. Wybierz **+ Nowy**i skonfiguruj jedno lub więcej zadań.

## <a name="customize-your-application"></a>Dostosowywanie aplikacji

Jako konstruktor można zmienić kilka ustawień, aby dostosować środowisko użytkownika w aplikacji.

1. Wybierz **pozycję Administracja** > **Dostosuj aplikację**.
1. Aby wybrać obraz do przesłania jako **logo aplikacji,** wybierz przycisk **Zmień.**
1. Aby wybrać obraz **ikony przeglądarki,** który pojawi się na kartach przeglądarki, wybierz przycisk **Zmień.**
1. Domyślne **kolory przeglądarki** można również zastąpić, dodając kody kolorów szesnastkowych HTML.

   ![Wybór logo aplikacji, ikony przeglądarki i kolorów przeglądarki](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1. Obrazy aplikacji można również zmieniać, wybierając pozycję Ustawienia aplikacji **administracyjnej** > **Application settings**. Aby wybrać obraz do przesłania jako obraz aplikacji, wybierz przycisk **Wybierz obraz.**
1. Na koniec można również zmienić **motyw,** wybierając ikonę **Ustawienia** w prawym górnym rogu aplikacji.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz nadal korzystać z tej aplikacji, usuń ją.

1. Wybierz **pozycję Administracja** w lewym okienku aplikacji Usługi Azure IoT Central.
1. Wybierz **pozycję Ustawienia aplikacji**, a następnie wybierz pozycję **Usuń** u dołu strony.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej [o pojęciach monitorowania zużycia wody](./concepts-waterconsumptionmonitoring-architecture.md).
