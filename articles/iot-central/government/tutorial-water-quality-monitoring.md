---
title: Tworzenie aplikacji do monitorowania jakości wody przy użyciu usługi Azure IoT Central
description: Dowiedz się, jak utworzyć aplikację do monitorowania jakości wody przy użyciu szablonów aplikacji IoT Central platformy Azure.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 51c84410de39516312d2058eeda575023dbe32ab
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890764"
---
# <a name="tutorial-create-a-water-quality-monitoring-application-in-iot-central"></a>Samouczek: Tworzenie aplikacji do monitorowania jakości wody w IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

W tym samouczku przedstawiono tworzenie aplikacji do monitorowania jakości wody na platformie Azure IoT Central z szablonu aplikacji do monitorowania jakości wody IoT Central. 

W tym samouczku dowiesz się, jak: 

> [!div class="checklist"]
> * Tworzenie aplikacji do monitorowania jakości wody przy użyciu szablonu **monitorowania jakości wody** platformy Azure IoT Central
> * Eksplorowanie i dostosowywanie pulpitu nawigacyjnego operatora 
> * Eksploruj szablon urządzenia monitora jakości wody
> * Eksplorowanie symulowanych urządzeń
> * Eksplorowanie i konfigurowanie reguł
> * Konfigurowanie zadań
> * Dostosowywanie oznakowania aplikacji za pomocą Whitelabeling


## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, musisz:
-  Zalecana jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz ją utworzyć na [stronie rejestracji na platformie Azure](https://aka.ms/createazuresubscription).


## <a name="create-water-quality-monitoring-app-in-iot-central"></a>Tworzenie aplikacji do monitorowania jakości wody w IoT Central

W tej sekcji użyjemy **szablonu monitorowania jakości wody** platformy Azure IoT Central, aby utworzyć aplikację do monitorowania jakości wody w IoT Central.


Aby utworzyć nową aplikację do monitorowania jakości wody w systemie Azure IoT Central:  

1. Przejdź do witryny sieci Web [strony głównej platformy Azure IoT Central](https://aka.ms/iotcentral) .

      Jeśli masz subskrypcję platformy Azure, zaloguj się przy użyciu poświadczeń, których używasz, aby uzyskać do niej dostęp. w przeciwnym razie zaloguj się przy pomocy konto Microsoft:

    ![Określanie konta organizacji](./media/tutorial-waterqualitymonitoring/sign-in.png)

2. W lewym okienku kliknij pozycję **kompilacja** , a następnie wybierz pozycję Karta **rządowa** . Na stronie Administracja jest wyświetlana kilka szablonów aplikacji rządowych.

    ![Kompiluj szablony aplikacji dla instytucji rządowych](./media/tutorial-waterqualitymonitoring/iotcentral-government-tab-overview1.png)


1. Wybierz szablon aplikacji do **monitorowania jakości wody** . Ten szablon obejmuje szablon urządzeń z jakością wody, symulowane urządzenie, pulpit nawigacyjny operatora i wstępnie skonfigurowane reguły monitorowania.    

2. Kliknij pozycję **Utwórz aplikację**. spowoduje to otwarcie formularza tworzenia **nowej aplikacji** z następującymi polami:
    * **Nazwa aplikacji**. Domyślnie aplikacja korzysta z *monitorowania jakości wody* , a następuje unikatowy ciąg identyfikatora, który IoT Central generowany. Opcjonalnie możesz wybrać przyjazną nazwę aplikacji. Nazwę aplikacji można zmienić później.
    * **URL** — Opcjonalnie możesz wybrać żądany adres URL. Adres URL można zmienić później. 
    * Jeśli masz subskrypcję platformy Azure, wprowadź swój *katalog, subskrypcję platformy Azure i region*. Jeśli nie masz subskrypcji, możesz włączyć **7-dniową bezpłatną wersję próbną** i uzupełnić wymagane informacje kontaktowe.  

    Aby uzyskać więcej informacji na temat katalogów i subskrypcji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](../preview/quick-deploy-iot-central.md).

5. Kliknij przycisk **Utwórz** w dolnej części strony. 

    ![Strona Tworzenie aplikacji usługi Azure IoT Central](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1.png)


6. Aplikacja do monitorowania jakości wody została już utworzona przy użyciu **szablonu monitorowania jakości wody**na platformie Azure IoT Central. 

Nowo utworzona aplikacja zawiera wstępnie skonfigurowane:
* Przykładowe pulpity nawigacyjne operatorów
* Przykładowy wstępnie zdefiniowany szablon urządzenia monitora jakości wody
* Symulowane urządzenia monitora jakości wody
* Wstępnie skonfigurowane reguły i zadania
* Przykładowe oznakowanie przy użyciu białej etykietki 

Jest to Twoja aplikacja i możesz ją zmodyfikować w dowolnym momencie. Teraz eksplorujemy aplikację i wprowadzasz pewne dostosowania.  


## <a name="explore-and-customize-operator-dashboard"></a>Eksplorowanie i dostosowywanie pulpitu nawigacyjnego operatora 
Po utworzeniu aplikacji na **pulpicie nawigacyjnym monitorowania jakości wody w szerokim**obszarze.


   ![Pulpit nawigacyjny monitorowania jakości wody](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-dashboard1.png)

Jako Konstruktor można tworzyć i dostosowywać widoki na pulpicie nawigacyjnym dla operatorów. Przed rozpoczęciem dostosowywania skontaktuj się z pulpitem nawigacyjnym. 

Wszystkie dane wyświetlane na pulpicie nawigacyjnym są oparte na symulowanych danych urządzenia, które zostaną zbadane w następnej sekcji. 

Pulpit nawigacyjny składa się z różnych rodzajów kafelków:

* **Kafelek obrazu narzędzia Wide World wodne**: pierwszy kafelek na pulpicie nawigacyjnym to kafelek obrazu fikcyjnej wody "Wide World". Możesz dostosować kafelek i umieścić własny obraz lub usunąć go. 

* **Średni kafelek wskaźnika KPI pH**: widzisz, że w **ciągu ostatnich 30 minut**znajdują się kafelki KPI, takie jak średnia pH. Można dostosować kafelki KPI i ustawić je na inny typ i zakres czasu.

*  **Mapa obszaru monitorowania wody**: IoT Central używa Azure Maps, które można ustawić bezpośrednio w aplikacji i wyświetlić lokalizację urządzenia. Możesz również zmapować informacje o lokalizacji na urządzenie z aplikacji i użyć Azure Maps, aby wyświetlić je na mapie. Spróbuj umieścić kursor na mapie i wypróbuj kontrolki na mapie. 

* **Średni wykres mapę cieplną rozkładu pH**: możesz wybrać różne wykresy wizualizacji, aby wyświetlić dane telemetryczne urządzenia w sposób najbardziej odpowiedni dla Twojej aplikacji. Mapę cieplną 

* **Wykres liniowy wskaźników jakości krytycznych**: można wizualizować jeden lub wiele telemetrii urządzeń, które są kreślone jako wykres liniowy w żądanym zakresie czasu.  

* **Koncentracja wykresu słupkowego agentów chemicznych**: można wizualizować jedno lub wiele danych telemetrii urządzenia na wykresie słupkowym tak jak w przykładzie. 

* **Przycisk akcji**: pulpit nawigacyjny zawiera przykład kafelka akcji, który operator może zainicjować bezpośrednio z poziomu pulpitu nawigacyjnego monitorowania, takich jak inicjowanie akcji resetowania właściwości urządzenia. 

* **Kafelki listy właściwości**: pulpit nawigacyjny zawiera wiele właściwości, które reprezentują informacje o wartościach progowych, informacje o kondycji urządzenia i informacje o konserwacji. 


### <a name="customize-dashboard"></a>Dostosowywanie pulpitu nawigacyjnego 

Jako Konstruktor można dostosować widoki na pulpicie nawigacyjnym dla operatorów. 
1. Kliknij pozycję **Edytuj** , aby dostosować **pulpit nawigacyjny monitorowania jakości całego świata**. Możesz dostosować pulpit nawigacyjny, klikając menu **Edycja** . Gdy pulpit nawigacyjny jest w trybie **edycji** , możesz dodać nowe kafelki lub skonfigurować

    ![Edytuj pulpit nawigacyjny](./media/tutorial-waterqualitymonitoring/edit-dashboard.png)

2. Kliknij pozycję **+ Nowy** , aby utworzyć nowy pulpit nawigacyjny i skonfigurować go od podstaw. Możesz mieć wiele pulpitów nawigacyjnych i można przechodzić między pulpitami nawigacyjnymi z menu Pulpit nawigacyjny.

## <a name="explore-water-quality-monitor-device-template"></a>Eksploruj szablon urządzenia monitora jakości wody
Szablon urządzenia w usłudze Azure IoT Central definiuje możliwość urządzenia, które może być Telemetria, właściwości i poleceniami. Jako Konstruktor można definiować szablony urządzeń w IoT Central, które reprezentują możliwości urządzeń, które będą połączone. W IoT Central można także tworzyć symulowane urządzenia do testowania szablonu i aplikacji urządzenia. 
 

Aplikacja do **monitorowania jakości wody** utworzona na podstawie szablonu aplikacji zawiera szablon urządzenia do monitorowania jakości wody referencyjnej.

Aby wyświetlić szablon urządzenia:

1.  Kliknij pozycję **Szablony urządzeń** w lewym okienku nawigacji aplikacji w IoT Central. 
2. Na liście szablony urządzeń zostanie wyświetlony **monitor jakość wody**. Otwórz program, klikając nazwę.

    ![Szablon urządzenia](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate.png)

### <a name="customizing-the-device-template"></a>Dostosowywanie szablonu urządzenia

Spróbuj dostosować następujące elementy:
1. Przejdź do **dostosowywania** z menu szablonu urządzenia
2. Znajdź `Temperature` typ telemetrii
3. Zaktualizuj **nazwę wyświetlaną** `Temperature` do `Reported temperature`
4. Zaktualizuj jednostkę miary lub ustaw wartość *minimalną* oraz *wartość maksymalną* .
5. **Zapisz** wszystkie zmiany 

#### <a name="add-a-cloud-property"></a>Dodaj właściwość chmury 
1. Przejdź do **właściwości chmury** z menu szablonu urządzenia
2. Dodaj nową właściwość chmury, klikając pozycję **+ Dodaj właściwość chmury**. W IoT Central można dodać właściwość, która jest istotna dla urządzenia, ale nie powinna zostać wysłana przez urządzenie. Przykładowo Właściwość chmury może być progiem alertu specyficznym dla obszaru instalacji, informacji o zasobach lub informacji o konserwacji itp. 
3. **Zapisz** wszystkie zmiany 
 
### <a name="views"></a>Widoki 
Szablon urządzenia monitora jakości wody jest dostarczany ze wstępnie zdefiniowanymi widokami. Przejrzyj widoki i możesz wprowadzić aktualizacje. Widoki definiują sposób, w jaki operatory zobaczą dane urządzenia, ale również umieszczają właściwości chmury. 

  ![Widoki szablonów urządzeń](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate-views.png)

### <a name="publish"></a>Publikowanie 

Jeśli zostały wprowadzone jakiekolwiek zmiany, upewnij się, że szablon urządzenia jest **opublikowany** . 


### <a name="create-a-new-device-template"></a>Tworzenie nowego szablonu urządzenia 
1. Wybierz pozycję **+ Nowy** , aby utworzyć nowy szablon urządzenia i postępuj zgodnie z procesem tworzenia. 
2. Utwórz niestandardowy szablon urządzenia od podstaw lub wybierz szablon urządzenia z wykazu urządzeń platformy Azure. 


## <a name="explore-simulated-devices"></a>Eksplorowanie symulowanych urządzeń

Aplikacja do **monitorowania jakości wody** utworzona na podstawie szablonu aplikacji ma dwa symulowane urządzenia, które zostały zamapowane na szablon urządzenia monitora jakości wody. 

### <a name="to-view-the-devices"></a>Aby wyświetlić urządzenia:
1. Przejdź do **urządzenia** z IoT Central okienku nawigacji po lewej stronie. 

   ![Urządzenia](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devices.png)


2. Wybierz i kliknij jedno symulowane urządzenie 

    ![Urządzenie 1](./media/tutorial-waterqualitymonitoring/waterqualitymonitor-device1.png)

3. Na karcie **właściwości chmury** spróbuj zaktualizować wartość `Acidity (pH) Threshold` z `8` do `9`. 
4. Eksploruj kartę **Właściwości urządzenia** i kartę **pulpit nawigacyjny urządzenia** . 

> [!NOTE]
> Należy pamiętać, że wszystkie karty zostały skonfigurowane z **widoków szablonów urządzeń**.


### <a name="add-new-devices"></a>Dodaj nowe urządzenia
Nowe urządzenia można dodać, klikając pozycję **+ Nowy** na karcie **urządzenia** . 


## <a name="explore-and-configure-rules"></a>Eksplorowanie i konfigurowanie reguł

Na platformie Azure IoT Central można tworzyć reguły automatycznego monitorowania danych telemetrycznych urządzeń i wyzwalać akcję w przypadku spełnienia co najmniej jednego warunku. Akcje mogą obejmować wysyłanie powiadomień e-mail lub wyzwalanie akcji Microsoft Flow lub akcji elementu webhook w celu wysyłania danych do innych usług.

Utworzona przez Ciebie aplikacja do **monitorowania jakości wody** ma dwie wstępnie skonfigurowane reguły.

### <a name="to-view-rules"></a>Aby wyświetlić reguły:
1. Przejdź do **reguły** , IoT Central okienku nawigacji po lewej stronie. 

   ![Reguły](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-rules.png)


2. Wybierz i kliknij **alert o wysokim poziomie pH** , który jest jedną ze wstępnie skonfigurowanych reguł w aplikacji. 

     ![Alert o wysokim poziomie pH](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-highphalert.png)

    Reguła `High pH alert` jest skonfigurowana do sprawdzania pod kątem `Acidity (pH) is greater than 8`warunku.

Teraz Utwórzmy akcję poczty e-mail.

Aby dodać akcję do reguły:

1. Wybierz pozycję **+ poczta e-mail**. 
2.  Wprowadź *alert o wysokim poziomie pH* jako przyjazną **nazwę wyświetlaną** dla akcji.
3. Wprowadź adres e-mail skojarzony z kontem IoT Central w programie **do**. 
4. Opcjonalnie wprowadź notatkę, która ma zostać dołączona do tekstu wiadomości e-mail.
5. Wybierz pozycję **gotowe** , aby zakończyć akcję.
6. Wybierz pozycję **Zapisz** , aby zapisać i aktywować nową regułę. 

W ciągu kilku minut powinna zostać wysłana wiadomość e-mail, gdy zostanie spełniony skonfigurowany **warunek** .

> [!NOTE]
> Aplikacja wyśle wiadomość e-mail za każdym razem, gdy warunek zostanie spełniony. **Wyłączenie** reguły w celu zatrzymania otrzymywania wiadomości e-mail z reguły zautomatyzowanej. 
  
Aby utworzyć nową regułę: 
1. Wybierz pozycję **+ Nowy** w obszarze **reguły** w okienku nawigacji po lewej stronie.

## <a name="configure-jobs"></a>Konfigurowanie zadań

W IoT Central zadania umożliwiają wyzwalanie aktualizacji właściwości urządzenia lub chmury na wielu urządzeniach. Oprócz właściwości można także użyć zadań do wyzwalania poleceń urządzenia na wielu urządzeniach. IoT Central automatyzuje przepływ pracy. 

1. Przejdź do obszaru **zadania** w okienku nawigacji po lewej stronie. 
2. Kliknij pozycję **+ Nowy** i skonfiguruj co najmniej jedno zadanie. 


## <a name="customize-your-application"></a>Dostosowywanie aplikacji 
Jako Konstruktor można zmienić kilka ustawień, aby dostosować środowisko użytkownika w aplikacji.

1. Wybierz pozycję **administracja > Dostosuj aplikację**.
2. Użyj przycisku **Zmień** , aby wybrać obraz do przekazania jako **logo aplikacji**.
3.  Użyj przycisku **Zmień** , aby wybrać obraz **ikony przeglądarki** , który będzie wyświetlany na kartach przeglądarki.
4. Możesz również zastąpić domyślne **kolory przeglądarki** , dodając szesnastkowe kody kolorów html.
5. Zmień również **motyw** , klikając **Ustawienia** w czasopismu.

   ![IoT Central dostosowywanie aplikacji przez platformę Azure](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-customize-your-application1.png)

### <a name="to-update-the-application-image"></a>Aby zaktualizować obraz aplikacji:

6.  Wybierz pozycję **administracja > Ustawienia aplikacji**.

7. Użyj przycisku **Wybierz obraz** , aby wybrać obraz do przekazania jako obraz aplikacji. 


  
## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń aplikację, wykonując następujące czynności:

1. Otwórz kartę Administracja w lewym okienku aplikacji IoT Central.
2. Wybierz pozycję Ustawienia aplikacji, a następnie kliknij przycisk Usuń u dołu strony.

    ![Usuwanie aplikacji](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-application-settings-delete-app1.png)        



## <a name="next-steps"></a>Następne kroki

* Więcej informacji o [pojęciach dotyczących monitorowania jakości wody](./concepts-waterqualitymonitoring-architecture.md)