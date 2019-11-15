---
title: 'Samouczek: Tworzenie aplikacji do monitorowania zużycia wody przy użyciu usługi Azure IoT Central'
description: 'Samouczek: informacje na temat tworzenia aplikacji do monitorowania zużycia wody przy użyciu IoT Central szablonów aplikacji platformy Azure.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 0b100e0bebc7c5b26449e396650da5434334075d
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112624"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-in-iot-central"></a>Samouczek: Tworzenie aplikacji do monitorowania zużycia wody w IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ten samouczek przedstawia sposób tworzenia aplikacji do monitorowania zużycia wody przez usługę Azure IoT Central przy użyciu szablonu aplikacji do monitorowania zużycia wody IoT Central. 

W tym samouczku dowiesz się, jak: 

> [!div class="checklist"]
> * Tworzenie aplikacji do monitorowania zużycia wody przy użyciu szablonu **monitorowania zużycia wody** IoT Central platformy Azure
> * Eksplorowanie i dostosowywanie pulpitu nawigacyjnego operatora 
> * Eksploruj szablon urządzenia
> * Eksplorowanie symulowanych urządzeń
> * Eksplorowanie i konfigurowanie reguł
> * Konfigurowanie zadań
> * Dostosowywanie oznakowania aplikacji za pomocą Whitelabeling

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, musisz:
-  Zalecana jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz ją utworzyć na [stronie rejestracji na platformie Azure](https://aka.ms/createazuresubscription).

## <a name="create-water-consumption-monitoring-app-in-iot-central"></a>Tworzenie aplikacji do monitorowania zużycia wody w IoT Central

W tej sekcji **Szablon monitorowania zużycia wody** IoT Central platformy Azure zostanie użyty do utworzenia aplikacji do monitorowania zużycia wody w IoT Central.

Aby utworzyć nową aplikację monitorowania zużycia wody w usłudze Azure IoT Central:  

1. Przejdź do witryny sieci Web [strony głównej platformy Azure IoT Central](https://aka.ms/iotcentral) .

    Jeśli masz subskrypcję platformy Azure, zaloguj się przy użyciu poświadczeń, których używasz, aby uzyskać do niej dostęp. w przeciwnym razie zaloguj się przy pomocy konto Microsoft:

    ![Określanie konta organizacji](media/tutorial-waterconsumptionmonitoring/sign-in.png)

2. W lewym okienku kliknij pozycję **kompilacja** , a następnie wybierz pozycję Karta **rządowa** . Na stronie Administracja jest wyświetlana kilka szablonów aplikacji rządowych.

   ![Kompiluj szablony aplikacji dla instytucji rządowych](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. Wybierz szablon aplikacja do **monitorowania zużycia wody** . Ten szablon obejmuje przykładowy szablon urządzenia do wykorzystania wody, symulowane urządzenie, pulpit nawigacyjny operatora i wstępnie skonfigurowane reguły monitorowania.    

2. Kliknij pozycję **Utwórz aplikację**. spowoduje to otwarcie formularza tworzenia **nowej aplikacji** z następującymi polami:
    * **Nazwa aplikacji**: Domyślnie aplikacja używa *monitorowania zużycia wody* oraz unikatowego ciągu identyfikatora, który IoT Central generowany. Opcjonalnie możesz wybrać przyjazną nazwę aplikacji. Nazwę aplikacji można zmienić później.
    * **Adres URL**: IoT Central automatycznie generuje adres URL na podstawie nazwy aplikacji. Możesz zaktualizować adres URL do swoich potrzeb. Adres URL można zmienić później. 
    * Jeśli masz subskrypcję platformy Azure, wprowadź swój *katalog, subskrypcję platformy Azure i region*. Jeśli nie masz subskrypcji, możesz włączyć **7-dniową bezpłatną wersję próbną** i uzupełnić wymagane informacje kontaktowe.  

    Aby uzyskać więcej informacji na temat katalogów i subskrypcji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](../preview/quick-deploy-iot-central.md).

5. Kliknij przycisk **Utwórz** w dolnej części strony. 

    ![Strona Tworzenie aplikacji usługi Azure IoT Central](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

6. Aplikacja do monitorowania zużycia wody została już utworzona przy użyciu szablonu **monitorowania zużycia wody** w usłudze Azure IoT Central.

Gratulacje! Ukończono tworzenie aplikacji do monitorowania jakości wody, która zawiera wstępnie skonfigurowane:
* Przykładowe pulpity nawigacyjne operatorów
* Przykład wstępnie zdefiniowanego przepływu wody i szablonów urządzeń z zaworem
* Symulowany przepływ wody i urządzenia przenośne
* Wstępnie skonfigurowane reguły i zadania
* Przykładowe oznakowanie przy użyciu białej etykietki 

Jest to Twoja aplikacja i możesz ją zmodyfikować w dowolnym momencie. Teraz eksplorujemy aplikację i wprowadzasz pewne dostosowania.  

## <a name="explore-and-customize-operator-dashboard"></a>Eksplorowanie i dostosowywanie pulpitu nawigacyjnego operatora 
Po utworzeniu aplikacji na pulpicie nawigacyjnym przykładowego operatora o nazwie **pulpit nawigacyjny monitorowania zużycia wody Wide World**.

   ![Pulpit nawigacyjny monitorowania zużycia wody](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

Jako Konstruktor można tworzyć i dostosowywać widoki na pulpicie nawigacyjnym dla operatorów. Przed rozpoczęciem dostosowywania skontaktuj się z pulpitem nawigacyjnym. 

> [!NOTE]
> Wszystkie dane wyświetlane na pulpicie nawigacyjnym są oparte na symulowanych danych urządzenia, które zostaną omówione w następnej sekcji. 
  
Pulpit nawigacyjny składa się z różnych rodzajów kafelków:

* **Kafelek obrazu narzędzia Wide World wodne**: pierwszy kafelek na pulpicie nawigacyjnym to kafelek obrazu fikcyjnej wody "Wide World". Możesz dostosować kafelek i umieścić własny obraz lub usunąć go. 

* **KAFELEK KWW średniego przepływu wody**: kafelek KPI jest skonfigurowany do wyświetlania jako przykład *średniej w ciągu ostatnich 30 minut*. Można dostosować kafelki KPI i ustawić je na inny typ i zakres czasu.

* Następnie ma prawo na kafelkach *poleceń* pulpitu nawigacyjnego, aby **zamknąć zawór**, **zawór otwarty**lub **ustawić położenie zaworu**. Kliknięcie poleceń spowoduje przejście do strony polecenia symulowanego urządzenia. W IoT Central *polecenie* jest typem *możliwości urządzenia* , który będziemy przeglądać w dalszej **części szablonu urządzenia** w tym samouczku.

*  **Mapa obszaru dystrybucji wody**: mapa używa Azure Maps, którą można skonfigurować bezpośrednio w usłudze Azure IoT Central. Kafelek Mapa Wyświetla lokalizację urządzenia. Spróbuj umieścić kursor na mapie i wypróbuj kontrolki na mapie, takie jak *powiększenie*, *Powiększ* lub *Rozwiń*. 

    ![Mapa pulpitu nawigacyjnego monitorowania zużycia wody](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* Wykres liniowy **wykresu** liniowego i **warunków środowiska**: można wizualizować jeden lub wiele telemetrii urządzeń, które są kreślone jako wykres liniowy w żądanym zakresie czasu.  

* **Średni wykres mapę cieplnąego naciskania zaworów**: możesz wybrać typ wizualizacji mapę cieplną dane telemetryczne urządzenia, które chcesz przyjrzeć rozkładowi w zakresie czasu z indeksem koloru.

* **Kafelek resetowania zawartości progu alertu**: możesz dołączyć wywołanie do kafelków zawartości akcji osadzania linku do strony akcji. W tym przypadku wartość progowa resetowania alertu spowoduje przejście do **zadań** aplikacji, w których można uruchomić aktualizacje właściwości urządzeń, które będziemy omawiać później w sekcji **Konfigurowanie zadań** w tym samouczku.

* **Kafelki właściwości**: na pulpicie nawigacyjnym są wyświetlane **informacje operacyjne dotyczące zaworów**, **progi alertów przepływu**oraz **Informacje o konserwacji** , które są właściwościami urządzeń.  


### <a name="customize-dashboard"></a>Dostosowywanie pulpitu nawigacyjnego 

Jako Konstruktor można dostosować widoki na pulpicie nawigacyjnym dla operatorów. Możesz spróbować:
1. Kliknij pozycję **Edytuj** , aby dostosować **pulpit nawigacyjny monitorowania zużycia**na całym świecie. Możesz dostosować pulpit nawigacyjny, klikając menu **Edycja** . Gdy pulpit nawigacyjny jest w trybie **edycji** , możesz dodać nowe kafelki lub skonfigurować 

     ![Edytuj pulpit nawigacyjny](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

2. Kliknij pozycję **+ Nowy** , aby utworzyć nowy pulpit nawigacyjny i skonfigurować go od podstaw. Możesz mieć wiele pulpitów nawigacyjnych i można przechodzić między pulpitami nawigacyjnymi z menu Pulpit nawigacyjny. 

## <a name="explore-device-template"></a>Eksploruj szablon urządzenia
Szablon urządzenia w usłudze Azure IoT Central definiuje możliwość urządzenia, które może być Telemetria, właściwości lub polecenia. Jako Konstruktor można zdefiniować co najmniej jeden szablon urządzenia w IoT Central, który reprezentuje możliwość nawiązania połączenia. 
 

Aplikacja do **monitorowania zużycia wody** obejmuje dwa szablony urządzeń referencyjnych przedstawiające *Miernik przepływu* *i urządzenie przenośne* . 

Aby wyświetlić szablon urządzenia:

1. Kliknij pozycję **Szablony urządzeń** w lewym okienku nawigacji aplikacji w IoT Central. 
    Na liście szablony urządzeń zostaną wyświetlone dwa urządzenia i **inteligentny zawór** **przepływu** .

   ![Szablon urządzenia](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

2. Kliknij szablon urządzenia **miernika przepływu** i zapoznaj się z możliwościami urządzenia 

     ![Licznik przepływu szablonu urządzenia](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customizing-the-device-template"></a>Dostosowywanie szablonu urządzenia

Spróbuj dostosować następujące elementy:
1. Przejdź do **dostosowywania** z menu szablonu urządzenia
2. Znajdź `Temperature` typ telemetrii
3. Zaktualizuj **nazwę wyświetlaną** `Temperature` do `Reported temperature`
4. Zaktualizuj jednostkę miary lub ustaw wartość *minimalną* oraz *wartość maksymalną* .
5. **Zapisz** wszystkie zmiany 

### <a name="add-a-cloud-property"></a>Dodaj właściwość chmury 
1. Przejdź do **właściwości chmury** z menu szablonu urządzenia
2. Dodaj nową właściwość chmury, klikając pozycję **+ Dodaj właściwość chmury**. 
    W IoT Central można dodać właściwość, która jest istotna dla urządzenia. Przykładowo Właściwość chmury może być progiem alertu specyficznym dla obszaru instalacji, informacji o zasobach lub informacji o konserwacji itp. 
3. **Zapisz** wszystkie zmiany 
 
### <a name="views"></a>Widoki 
Szablon urządzenia monitora zużycia wody jest dostarczany ze wstępnie zdefiniowanymi widokami. Przejrzyj widoki i możesz wprowadzić aktualizacje. Widoki definiują sposób, w jaki operatory zobaczą dane urządzenia, ale również umieszczają właściwości chmury. 

  ![Widoki szablonów urządzeń](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>Publikowanie 
Jeśli zostały wprowadzone jakiekolwiek zmiany, upewnij się, że szablon urządzenia jest **opublikowany** . 

### <a name="create-a-new-device-template"></a>Tworzenie nowego szablonu urządzenia 
- Wybierz pozycję **+ Nowy** , aby utworzyć nowy szablon urządzenia i postępuj zgodnie z procesem tworzenia. Można utworzyć niestandardowy szablon urządzenia od podstaw lub wybrać szablon urządzenia z wykazu urządzeń platformy Azure. 

## <a name="explore-simulated-devices"></a>Eksplorowanie symulowanych urządzeń
W IoT Central można tworzyć symulowane urządzenia do testowania szablonu i aplikacji urządzenia. Aplikacja do **monitorowania zużycia wody** ma dwa symulowane urządzenia zamapowane na *Miernik przepływu* i *inteligentne* szablony urządzeń. 

### <a name="to-view-the-devices"></a>Aby wyświetlić urządzenia:
1. Przejdź do **urządzenia** z IoT Central okienku nawigacji po lewej stronie. 

   ![Urządzenia](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

2. Kliknij jeden **zawór inteligentny 1** 

    ![Urządzenie 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

3.  W **poleceniach dotyczących urządzeń** można zobaczyć trzy *polecenia, zawór* *zamka*i *ustawić położenie zaworu* , które są możliwościami zdefiniowanymi w szablonie *urządzenia* przenośnego. 
4. Eksploruj kartę **Właściwości urządzenia** i kartę **pulpit nawigacyjny urządzenia** . 

> [!NOTE]
> Należy pamiętać, że wszystkie karty zostały skonfigurowane z widoków szablonów urządzeń.

### <a name="add-new-devices"></a>Dodaj nowe urządzenia
* Dodaj nowe urządzenia, klikając pozycję **+ Nowy** na karcie **urządzenia** . 

## <a name="explore-and-configure-rules"></a>Eksplorowanie i konfigurowanie reguł

Na platformie Azure IoT Central można tworzyć reguły automatycznego monitorowania danych telemetrycznych urządzeń oraz wyzwalać akcje po spełnieniu jednego lub większej liczby warunków. Akcje mogą obejmować wysyłanie powiadomień e-mail lub wyzwalanie akcji Microsoft Flow lub akcji elementu webhook w celu wysyłania danych do innych usług.

Utworzona przez Ciebie aplikacja do **monitorowania zużycia wody** ma trzy wstępnie skonfigurowane reguły.

### <a name="to-view-rules"></a>Aby wyświetlić reguły:
1. Przejdź do **reguły** , IoT Central okienku nawigacji po lewej stronie. 

   ![Reguły](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

2. Wybierz i kliknij **alert o wysokim poziomie pH** , który jest jedną ze wstępnie skonfigurowanych reguł w aplikacji.

     ![Alert o wysokim poziomie pH](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    Reguła `High flow alert` jest skonfigurowana do sprawdzania pod kątem warunku, `Acidity (pH)` `greater than` `Max flow threshold`. Maksymalny próg przepływu to właściwość chmury zdefiniowana w *szablonie urządzenia przenośnego urządzenia* przenośnego. Wartość `Max flow threshold` jest ustawiana na wystąpienie urządzenia. 

Teraz Utwórzmy akcję poczty e-mail.

Aby dodać akcję do reguły:

1. Wybierz pozycję **+ poczta e-mail**. 
1. Wprowadź *alert o wysokim poziomie pH* jako przyjazną **nazwę wyświetlaną** dla akcji.
    * Wprowadź adres e-mail skojarzony z kontem IoT Central w programie **do**. 
1. Opcjonalnie wprowadź notatkę, która ma zostać dołączona do tekstu wiadomości e-mail.
1. Wybierz pozycję **gotowe** , aby zakończyć akcję.
1. Wybierz pozycję **Zapisz** , aby zapisać i aktywować nową regułę. 

W ciągu kilku minut powinna zostać wysłana wiadomość e-mail, gdy zostanie spełniony skonfigurowany **warunek** .

> [!NOTE]
> Aplikacja wyśle wiadomość e-mail za każdym razem, gdy warunek zostanie spełniony. **Wyłączenie** reguły w celu zatrzymania otrzymywania wiadomości e-mail z reguły zautomatyzowanej. 
  
Aby utworzyć nową regułę: 
- Wybierz pozycję **+ Nowy** w obszarze **reguły** w okienku nawigacji po lewej stronie.

## <a name="configure-jobs"></a>Konfigurowanie zadań

W IoT Central zadania umożliwiają wyzwalanie aktualizacji właściwości urządzenia lub chmury na wielu urządzeniach. Oprócz właściwości można także użyć zadań do wyzwalania poleceń urządzenia na wielu urządzeniach. IoT Central automatyzuje przepływ pracy. 

1. Przejdź do obszaru **zadania** w okienku nawigacji po lewej stronie. 
2. Kliknij pozycję **+ Nowy** i skonfiguruj co najmniej jedno zadanie. 


## <a name="customize-your-application"></a>Dostosowywanie aplikacji 
Jako Konstruktor można zmienić kilka ustawień, aby dostosować środowisko użytkownika w aplikacji.

1.  Przejdź do pozycji **administracja > Dostosuj aplikację**.
3. Użyj przycisku **Zmień** , aby wybrać obraz do przekazania jako **logo aplikacji**.
4. Użyj przycisku **Zmień** , aby wybrać obraz **ikony przeglądarki** , który będzie wyświetlany na kartach przeglądarki.
5. Możesz również zastąpić domyślne **kolory przeglądarki** , dodając szesnastkowe kody kolorów html.

   ![IoT Central dostosowywanie aplikacji przez platformę Azure](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1.  Możesz również zmienić obrazy aplikacji, przechodząc do opcji **administracja > Ustawienia aplikacji** i **Wybierz obraz** przycisk, aby wybrać obraz do przekazania jako obraz aplikacji. 
2. Na koniec możesz również zmienić **kompozycję** , klikając pozycję **Ustawienia** w obszarze tytuł aplikacji. 

  
## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń aplikację, wykonując następujące czynności:

1. Otwórz kartę Administracja w lewym okienku aplikacji IoT Central. 
2. Wybierz pozycję Ustawienia aplikacji, a następnie kliknij przycisk Usuń u dołu strony. 


## <a name="next-steps"></a>Następne kroki

* Więcej informacji o [pojęciach dotyczących monitorowania zużycia wody](./concepts-waterconsumptionmonitoring-architecture.md)
