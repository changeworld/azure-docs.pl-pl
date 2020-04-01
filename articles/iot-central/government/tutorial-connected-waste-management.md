---
title: 'Samouczek: Tworzenie połączonej aplikacji do zarządzania odpadami za pomocą usługi Azure IoT Central'
description: 'Samouczek: Dowiedz się, jak utworzyć aplikację do zarządzania odpadami połączoną przy użyciu szablonów aplikacji Usługi Azure IoT Central.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 274d725a0e7987c8cb15363ba276aec7ebc541d5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77426364"
---
# <a name="tutorial-create-a-connected-waste-management-application-in-iot-central"></a>Samouczek: Tworzenie aplikacji do zarządzania odpadami podłączonych do sieci w centrum IoT



W tym samouczku można utworzyć aplikację do zarządzania odpadami połączoną z usługą Azure IoT Central na podstawie szablonu aplikacji do **zarządzania odpadami połączonych centralnie.** 

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności: 

> [!div class="checklist"]

> * Użyj szablonu **zarządzania odpadami połączonymi centralą** Usługi Azure IoT, aby utworzyć połączoną aplikację do gospodarowania odpadami
> * Eksplorowanie i dostosowywanie pulpitu nawigacyjnego operatora 
> * Poznaj szablon urządzenia podłączonego pojemnika na odpady
> * Eksplorowanie symulowanych urządzeń
> * Eksplorowanie i konfigurowanie reguł
> * Konfigurowanie zadań
> * Dostosuj znakowanie aplikacji za pomocą whitelabeling

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:
-  Zalecana jest subskrypcja platformy Azure. Opcjonalnie możesz skorzystać z bezpłatnego 7-dniowego okresu próbnego. Jeśli nie masz subskrypcji platformy Azure, możesz ją utworzyć na [stronie rejestracji platformy Azure.](https://aka.ms/createazuresubscription)

## <a name="create-connected-waste-management-app-in-iot-central"></a>Tworzenie aplikacji Connected Waste Management w U. IoT Central

W tej sekcji użyj **szablonu zarządzania odpadami połączonymi centralną** usługi Azure IoT, aby utworzyć aplikację do zarządzania odpadami połączoną w Usłudze IoT Central.

Aby utworzyć nową aplikację do zarządzania odpadami połączonymi z usługą Azure IoT Central:  

1. Przejdź do centralnej [strony głównej usługi Azure IoT.](https://aka.ms/iotcentral)

    Jeśli masz subskrypcję platformy Azure, zaloguj się przy użyciu poświadczeń, których używasz do uzyskiwania do niej dostępu, w przeciwnym razie zaloguj się przy użyciu konta Microsoft:

    ![Określanie konta organizacji](./media/tutorial-connectedwastemanagement/sign-in.png)

2. Kliknij pozycję **Zbuduj** z lewego okienka i wybierz kartę **Rząd.** Strona rządowa wyświetla kilka szablonów aplikacji rządowych.

    ![Tworzenie szablonów aplikacji rządowych](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. Wybierz szablon aplikacji **Connected Waste Management.** Ten szablon zawiera przykładowy szablon urządzenia podłączonego do pojemnika na odpady, symulowane urządzenie, pulpit nawigacyjny operatora i wstępnie skonfigurowane reguły monitorowania.    

2. Kliknij **pozycję Utwórz aplikację**, która otworzy nowy formularz tworzenia **aplikacji** z następującymi polami:
    * **Nazwa aplikacji**. Domyślnie aplikacja używa *connected zarządzania odpadami,* a następnie unikatowy ciąg identyfikatora, który generuje IoT Central. Opcjonalnie wybierz przyjazną nazwę aplikacji. Nazwę aplikacji można zmienić również później.
    * **ADRES URL** — opcjonalnie możesz wybrać żądany adres URL. Możesz też później zmienić adres URL. 
    * Jeśli masz subskrypcję platformy Azure, wprowadź *katalog, subskrypcję platformy Azure i region*. Jeśli nie masz subskrypcji, możesz włączyć **7-dniową bezpłatną wersję próbną** i wypełnić wymagane informacje kontaktowe.  

    Aby uzyskać więcej informacji na temat katalogów i subskrypcji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](../core/quick-deploy-iot-central.md).

5. Kliknij przycisk **Utwórz** u dołu strony. 

    ![Strona Azure IoT Central Create Connected Waste Application](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)
    
    ![Informacje o tworzeniu połączonego rozliczenia usługi Azure IoT Central](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement-billinginfo.png)

 
6. Aplikacja do zarządzania odpadami została utworzona przy użyciu **szablonu zarządzania odpadami połączonymi**centralną usługą Azure IoT. 

Gratulacje! Nowo utworzona aplikacja jest wyposażona w wstępnie skonfigurowaną:
* Przykładowe pulpity nawigacyjne operatora
* Przykładowe wstępnie zdefiniowane szablony podłączonych urządzeń pojemników na odpady
* Symulowane podłączone urządzenia pojemnika na odpady
* Wstępnie skonfigurowane reguły i zadania
* Znakowanie próbek przy użyciu białego etykietowania 

Jest to aplikacja i można go zmodyfikować w dowolnym momencie. Teraz eksplorujmy aplikację i dokonujmy pewnych dostosowań.  

## <a name="explore-and-customize-operator-dashboard"></a>Eksplorowanie i dostosowywanie pulpitu nawigacyjnego operatora 
Po utworzeniu aplikacji wylądujesz w **desce rozdzielczej do gospodarowania odpadami podłączonych do szerokich odpadów.**

   ![Połączony pulpit gospodarki odpadami](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

Jako konstruktor możesz tworzyć i dostosowywać widoki na pulpicie nawigacyjnym dla operatorów. Zanim spróbujesz dostosować, zapoznajmy się z pulpitem nawigacyjnym. 

> [!NOTE]
> Wszystkie dane wyświetlane na pulpicie nawigacyjnym są oparte na symulowanych danych urządzenia, które zostaną zbadane w następnej sekcji. 

Pulpit nawigacyjny składa się z różnych rodzajów kafelków:

* ***Wide World Waste narzędzie płytki obrazu:*** pierwszy kafelek w desce rozdzielczej jest płytka obrazu fikcyjnego narzędzia odpadów "Wide World Waste". Możesz dostosować kafelek i umieścić własny obraz lub usunąć go. 

* ***Kafelek obrazu pojemnika na odpady:*** można użyć kafelków obrazu i zawartości, aby utworzyć wizualną reprezentację monitorowanego urządzenia wraz z tekstem opisowym. 

* ***Płytka KPI poziomu wypełnienia:*** kafelek wyświetla wartość zgłoszoną przez czujnik *poziomu napełnienia* w pojemniku na odpady. *Poziom napełnienia* i inne czujniki, takie jak *miernik zapachu* lub *waga* w pojemniku na odpady, mogą być zdalnie monitorowane. Operator może podjąć działania, takie jak wysyłanie śmieci ciężarówki. 

*  ***Mapa obszaru monitorowania odpadów:*** mapa korzysta z usługi Azure Maps, którą można skonfigurować bezpośrednio w usłudze Azure IoT Central. Kafelek mapy wyświetla lokalizację urządzenia. Spróbuj najechać kursorem na mapę i wypróbować kontrolki na mapie, takie jak powiększanie, pomniejszanie lub rozszerzanie.

     ![Mapa pulpitu nawigacyjnego connected waste management](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* ***Wypełnienie, zapach, wykres słupkowy poziomu wagi:** można wizualizować dane telemetryczne jednego lub wielu urządzeń na wykresie słupkowym. Można również rozwinąć wykres słupkowy.  

  ![Połączony wykres słupkowy pulpitu nawigacyjnego gospodarowania odpadami](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Kafelek zawartości usług terenowych:** pulpit nawigacyjny zawiera łącze do sposobu integracji z usługami terenowymi Dynamics 365 z aplikacji Azure IoT Central. Na przykład można użyć field services do tworzenia biletów do wysyłania usług zbierania śmieci. 


### <a name="customize-dashboard"></a>Dostosowywanie pulpitu nawigacyjnego 

Jako konstruktor możesz dostosować widoki na pulpicie nawigacyjnym dla operatorów. Możesz spróbować:
1. Kliknij **edytuj,** aby dostosować **pulpit nawigacyjny zarządzania odpadami połączonymi z szerokim światem**. Pulpit nawigacyjny można dostosować, klikając menu **Edycja.** Gdy pulpit nawigacyjny jest w trybie **edycji,** można dodać nowe kafelki lub 

    ![Edytowanie pulpitu nawigacyjnego](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

2. Można również kliknąć **na + Nowy,** aby utworzyć nowy pulpit nawigacyjny i skonfigurować od podstaw. Możesz mieć wiele pulpitów nawigacyjnych i można poruszać się między pulpitami nawigacyjnymi z menu pulpitu nawigacyjnego. 

## <a name="explore-connected-waste-bin-device-template"></a>Poznaj szablon urządzenia podłączonego pojemnika na odpady

Szablon urządzenia w usłudze Azure IoT Central definiuje możliwości urządzenia, które może być telemetrią, właściwościami lub poleceniem. Jako konstruktor możesz zdefiniować szablony urządzeń, które reprezentują możliwości urządzeń, które zostaną nawiązane na połączenie. 
 

Aplikacja **Connected waste management** jest wyposażona w szablon urządzenia do pojemnika na odpady podłączonego do próbek.

Aby wyświetlić szablon urządzenia:

1. Kliknij **szablony urządzeń** z lewego okienka aplikacji w centrum IoT. 

    ![Szablon urządzenia](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

2. Na liście Szablony urządzeń zostanie wyświetlony widok **Podłączony pojemnik na odpady**. Otwórz, klikając nazwę.

3. Zapoznaj się z możliwościami szablonu urządzenia. Widać, że definiuje czujniki, takie jak *poziom wypełnienia,* *miernik zapachu,* *waga,* *lokalizacja*i inne.

   ![Szablon urządzenia](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)

   

### <a name="customizing-the-device-template"></a>Dostosowywanie szablonu urządzenia

Spróbuj dostosować następujące elementy:
1. Przejdź do **menu Dostosowywanie** z menu szablonu urządzenia
2. Znajdowanie `Odor meter` typu telemetrii
3. Zaktualizuj `Odor meter` **nazwę wyświetlaną** do`Odor level`
4. Można również spróbować zaktualizować jednostkę miary lub ustawić *wartość Min* i *Wartość Maksymalna*
5. **Zapisywanie** wszelkich zmian 

### <a name="add-a-cloud-property"></a>Dodawanie właściwości w chmurze 

1. Przejdź do **właściwości Cloud** z menu szablonu urządzenia
2. Dodaj nową właściwość chmury, klikając **przycisk + Dodaj właściwość chmury**. W Uiścić W uikw. Na przykład właściwość chmury może być próg alertów specyficzne dla obszaru instalacji, informacje o zasobach lub konserwacji informacji i innych informacji. 
3. **Zapisywanie** wszelkich zmian 
 
### <a name="views"></a>Widoki 
* Szablon podłączonego urządzenia pojemnika na odpady jest dostarczany ze wstępnie zdefiniowanymi widokami. Eksploruj widoki i możesz dokonywać aktualizacji. Widoki definiują sposób, w jaki operatorzy będą widzieć dane urządzenia, ale także wprowadzanie właściwości chmury. 

  ![Widoki szablonu urządzenia](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>Publikowanie 

* Jeśli wprowadzono jakiekolwiek zmiany, upewnij **się, że opublikowanie** szablonu urządzenia. 

### <a name="create-a-new-device-template"></a>Tworzenie nowego szablonu urządzenia 

* Wybierz **+ Nowy,** aby utworzyć nowy szablon urządzenia i postępuj zgodnie z procesem tworzenia. Będzie można utworzyć niestandardowy szablon urządzenia od podstaw lub wybrać szablon urządzenia z wykazu urządzeń platformy Azure. 

## <a name="explore-simulated-devices"></a>Eksplorowanie symulowanych urządzeń

W Uiszczona sieć IoT Central można tworzyć symulowane urządzenia w celu przetestowania szablonu i aplikacji urządzenia. 

Aplikacja **Connected waste management** ma dwa symulowane urządzenia mapowane na szablon urządzenia podłączonego pojemnika na odpady. 

### <a name="to-view-the-devices"></a>Aby wyświetlić urządzenia:

1. Przejdź do **pozycji Urządzenie** z lewego okienka IoT Central. 

   ![Urządzenia](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

2. Wybierz i kliknij podłączone urządzenie kosza na śmieci.  

     ![Urządzenie 1](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

3. Przejdź do karty **Właściwości chmury** spróbuj `Bin full alert threshold` zaktualizować `95` `100`wartość od do . 
* Zapoznaj się z kartą **Właściwości urządzenia** i pulpit **nawigacyjny urządzenia.** 

> [!NOTE]
> Należy pamiętać, że wszystkie karty zostały skonfigurowane z **widoków szablonu urządzenia**.

### <a name="add-new-devices"></a>Dodawanie nowych urządzeń

* Możesz dodać nowe urządzenia, klikając na **+ Nowy** na karcie **Urządzenia.** 

## <a name="explore-and-configure-rules"></a>Eksplorowanie i konfigurowanie reguł

W usłudze Azure IoT Central można utworzyć reguły do automatycznego monitorowania danych telemetrycznych urządzenia i wyzwalania akcji, gdy spełnione jest co najmniej jedno warunki. Akcje mogą obejmować wysyłanie powiadomień e-mail, wyzwalanie akcji usługi Microsoft Flow lub akcję elementu webhook w celu wysyłania danych do innych usług.

**Aplikacja Connected waste management** ma cztery przykładowe reguły.

### <a name="to-view-rules"></a>Aby wyświetlić reguły:
1. Przechodzenie do **reguł** z lewego okienka IoT Central

   ![Reguły](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

2. Wybieranie **pełnego alertu o koszu**

     ![Pełny alert pojemnika](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 3. Sprawdza, `Bin full alert` kiedy **warunek** `Fill level is greater than or equal to Bin full alert threshold`.

    Jest `Bin full alert threshold` *to właściwość w chmurze* zdefiniowana w szablonie `Connected waste bin` urządzenia. 

Teraz utwórzmy akcję e-mail.

### <a name="create-an-email-action"></a>Tworzenie akcji e-mail
Aby skonfigurować akcję wiadomości e-mail na liście działań reguły:
1. Wybierz **+ E-mail**. 
2. Wprowadź *alert wysokiego pH* jako przyjazną **nazwę wyświetlaną** dla akcji.
3. Wprowadź adres e-mail skojarzony z kontem IoT Central w **obszarze Do**. 
4. Opcjonalnie wprowadź notatkę, która ma być uwzględnina w tekście wiadomości e-mail.
5. Wybierz **pozycję Gotowe,** aby ukończyć akcję.
6. Wybierz **pozycję Zapisz,** aby zapisać i aktywować nową regułę. 

Po spełnieniu skonfigurowanych **warunków** należy otrzymywać wiadomości e-mail.

> [!NOTE]
> Aplikacja będzie wysyłać wiadomości e-mail za każdym razem, gdy warunek zostanie spełniony. **Wyłącz** regułę, aby przestać otrzymywać wiadomości e-mail z reguły automatycznej. 
  
Aby utworzyć nową regułę: 
1. Z lewego okienka wybierz **pozycję +Nowy** w **regułach.**

## <a name="configure-jobs"></a>Konfigurowanie zadań

W Usłudze IoT Central zadania umożliwiają wyzwalanie aktualizacji właściwości urządzenia lub chmury na wielu urządzeniach. Oprócz właściwości można również użyć zadań do wyzwalania poleceń urządzenia na wielu urządzeniach. IoT Central zautomatyzuje przepływ pracy. 

1. Przejdź do **zadania** z lewego okienka. 
2. Kliknij **pozycję +Nowy** i skonfiguruj jedno lub więcej zadań. 


## <a name="customize-your-application"></a>Dostosowywanie aplikacji 

Jako konstruktor można zmienić kilka ustawień, aby dostosować środowisko użytkownika w aplikacji.

### <a name="to-change-the-application-theme"></a>Aby zmienić motyw aplikacji:

1. Przejdź do **> Administracja dostosuj aplikację**.
3. Użyj przycisku **Zmień,** aby wybrać obraz do przesłania jako **logo Aplikacji**.
4. Użyj przycisku **Zmień,** aby wybrać obraz **ikony przeglądarki,** który pojawi się na kartach przeglądarki.
5. Domyślne **kolory przeglądarki** można również zastąpić, dodając kody kolorów szesnastkowych HTML.

   ![Usługa Azure IoT Central dostosowuje aplikację](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

6. Można również zmienić obrazy aplikacji, przechodząc do **ustawień aplikacji > Administracja** i Wybierz **obraz,** aby wybrać obraz do przekazania jako obraz aplikacji.
7. Na koniec można również zmienić **motyw,** klikając **ustawienia** na nagłówku aplikacji.

  
## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz nadal korzystać z tej aplikacji, usuń aplikację, wykonując następujące czynności:

1. Otwórz kartę Administracja z lewego okienka aplikacji IoT Central.
2. Wybierz pozycję Ustawienia aplikacji i kliknij przycisk Usuń u dołu strony.

  

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [pojęciach dotyczących gospodarowania odpadami połączonymi](./concepts-connectedwastemanagement-architecture.md)
