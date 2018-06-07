---
title: Omówienie rozwiązania połączonej fabryki — Azure | Microsoft Docs
description: Opis akceleratora rozwiązania Azure IoT połączonej fabryki.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 1dbaf4978bd23058d37239ba97bfe8583dc1f2c9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627094"
---
# <a name="get-started-with-the-connected-factory-solution-accelerator"></a>Wprowadzenie do akceleratora rozwiązania połączonej fabryki

[Akceleratory rozwiązań][lnk-preconfigured-solutions] Azure IoT obejmują wiele usług Azure IoT, co pozwala dostarczać kompleksowe rozwiązania, które umożliwiają implementowanie typowych scenariuszy biznesowych IoT. Akcelerator rozwiązania *połączonej fabryki* łączy i monitoruje urządzenia przemysłowe. To rozwiązanie może służyć do analizowania strumienia danych z urządzeń i zwiększania produktywności operacyjnej oraz rentowności.

W tym samouczku przedstawiono sposób aprowizowania akceleratora rozwiązania połączonej fabryki. Dostępny jest również opis podstawowych funkcji tego akceleratora rozwiązania. Dostęp do wielu z tych funkcji można uzyskać z *pulpitu nawigacyjnego* rozwiązania, który jest wdrażany jako część akceleratora rozwiązania:

![Pulpit nawigacyjny akceleratora rozwiązania połączonej fabryki][img-cf-home]

Do wykonania kroków tego samouczka jest potrzebna aktywna subskrypcja platformy Azure.

> [!NOTE]
> Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure][lnk_free_trial].

## <a name="provision-the-solution"></a>Aprowizacja rozwiązania

1. Zaloguj się w witrynie azureiotsuite.com przy użyciu poświadczeń konta platformy Azure i kliknij pozycję „**+**”, aby utworzyć rozwiązanie.
2. Kliknij pozycję **Wybierz** na kafelku **Połączona fabryka**.
3. W polu **Nazwa rozwiązania** wprowadź nazwę akceleratora rozwiązania połączonej fabryki.
4. W polach **Subskrypcja** i **Region** wybierz wartości, których chcesz użyć do aprowizacji rozwiązania.
5. Kliknij pozycję **Utwórz rozwiązanie**, aby rozpocząć proces aprowizowania. Zwykle trwa on kilka minut.

### <a name="while-you-wait-for-the-provisioning-process-to-complete"></a>Podczas oczekiwania na ukończenie procesu aprowizacji

1. Kliknij kafelek swojego rozwiązania zawierający informację o stanie **aprowizacji**.
2. Zwróć uwagę na informację o **stanach aprowizacji** podczas wdrażania usług Azure w Twojej subskrypcji platformy Azure.
3. Po ukończeniu aprowizowania stan zmieni się na wartość **Gotowe**.
4. Kliknij kafelek, aby wyświetlić szczegóły rozwiązania w prawym okienku.

> [!NOTE]
> Jeśli podczas wdrażania akceleratora rozwiązania pojawią się problemy, zapoznaj się z tematami [Uprawnienia w witrynie azureiotsuite.com][lnk-permissions] i [Connected Factory FAQ (Połączona fabryka — często zadawane pytania)](iot-accelerators-faq-cf.md). Jeśli problemy będą się powtarzać, utwórz żądanie pomocy w [portalu][lnk-portal].

Czy istnieją jakieś szczegóły dotyczące Twojego rozwiązania, które nie są wyświetlane, a Twoim zdaniem powinny być widoczne? Utwórz swoje propozycje dotyczące funkcji, korzystając ze strony [User Voice](https://feedback.azure.com/forums/321918-azure-iot) (Opinie użytkowników).

## <a name="scenario-overview"></a>Omówienie scenariusza

Podczas wdrażania akceleratora rozwiązania połączonej fabryki jest on wstępnie wypełniany zasobami, które umożliwiają wykonanie kroków opisanych w typowym scenariuszu przemysłowym. W tym scenariuszu kilka fabryk połączonych z rozwiązaniem raportuje wartości danych wymagane do obliczenia ogólnej wydajności sprzętu (OEE) oraz kluczowych wskaźników wydajności (KPI). W poniższych sekcjach opisano sposób wykonania następujących czynności:

* Monitorowanie fabryki, linii produkcyjnych, ogólnej wydajności stacji i wartości kluczowych wskaźników wydajności.
* Analizowanie danych telemetrycznych generowanych przez te urządzenia za pomocą usługi Azure Time Series Insights.
* Reagowanie na alarmy w celu rozwiązania problemów

Kluczową cechą tego scenariusza jest to, że te wszystkie akcje można wykonać zdalnie z poziomu pulpitu nawigacyjnego rozwiązania. Nie jest konieczny fizyczny dostęp do urządzeń.

## <a name="view-the-solution-dashboard"></a>Wyświetlanie pulpitu nawigacyjnego

Pulpit nawigacyjny pozwala zarządzać wdrożonym rozwiązaniem. Jest to hierarchiczna reprezentacja globalnej konfiguracji fabryki. Można na przykład wyświetlić ogólną wydajność sprzętu oraz kluczowe wskaźniki wydajności, opublikować nowe węzły na potrzeby telemetrii i reagować na alarmy.

1. Jeśli aprowizacja została ukończona, a na kafelku akceleratora rozwiązania jest wyświetlany stan **Gotowe**, wybierz pozycję **Uruchom**, aby otworzyć portal rozwiązania połączonej fabryki na nowej karcie.

    ![Uruchamianie akceleratora rozwiązania][img-launch-solution]

1. Domyślnie w portalu rozwiązania jest wyświetlany *pulpit nawigacyjny*. Korzystając z menu znajdującego się w lewej części strony, można przejść do innych obszarów portalu.

    ![Pulpit nawigacyjny akceleratora rozwiązania połączonej fabryki][cf-img-menu]

Pulpit nawigacyjny udostępnia następujące informacje:

* Panel **Lokalizacje fabryk**, który przedstawia stan, lokalizację i bieżącą konfigurację produkcji w rozwiązaniu. Przy pierwszym uruchomieniu rozwiązania dostępnych jest kilka symulowanych urządzeń. Symulacja linii produkcyjnej składa się z trzech prawdziwych serwerów OPC UA na każdą linię produkcyjną, które wykonują symulowane zadania i udostępniają dane. Aby uzyskać więcej informacji na temat OPC UA, zobacz [Connected Factory FAQ (Połączona fabryka — często zadawane pytania)](iot-accelerators-faq-cf.md).
* **Mapę** zawierającą lokalizację każdego urządzenia połączonego z rozwiązaniem. Rozwiązanie może korzystać z interfejsu API usługi Mapy Bing do wykreślania informacji na mapie. Jeśli subskrypcja obejmuje interfejs API usługi Mapy Bing w wersji Enterprise, ta funkcja jest używana automatycznie. W przeciwnym razie zobacz [Często zadawane pytania][lnk-faq], aby dowiedzieć się, jak utworzyć dynamiczną mapę.
* Panel **Alarmy**, na którym są wyświetlane alarmy generowane, gdy wartość telemetrii lub ogólnej wydajności sprzętu bądź kluczowego wskaźnika wydajności przekroczy określony próg.
* Panel **Ogólna wydajność sprzętu**, na którym są pokazane wartości ogólnej wydajności sprzętu dla całego przedsiębiorstwa lub przeglądanej fabryki/linii produkcyjnej/stacji. Ta wartość jest agregowana od widoku stacji do poziomu przedsiębiorstwa. Dane ogólnej wydajności sprzętu i jej składowe elementy można dokładniej analizować.
* Panel **Kluczowe wskaźniki wydajności**, na którym jest wyświetlana liczba wyprodukowanych jednostek i ilość zużytej energii w całym przedsiębiorstwie lub w przeglądanej fabryce/linii produkcyjnej/stacji. Te wartości są agregowane od widoku stacji do poziomu przedsiębiorstwa.

## <a name="view-factories"></a>Wyświetlanie fabryk

Na panelu *Lokalizacje fabryk* jest wyświetlana lokalizacja geograficzna wszystkich fabryk w rozwiązaniu, ich stan i bieżąca konfiguracja produkcji. Z listy lokalizacji można przejść do innych poziomów w hierarchii rozwiązania. Wiersze listy są hiperlinkami umożliwiającymi wyświetlenie szczegółowych informacji dotyczących linii produkcyjnych w lokalizacji. Możliwe jest również przejście do szczegółów linii produkcyjnej i w dół do widoku poziomu stacji. Do listy można także zastosować filtr.

![Fabryki akceleratora rozwiązania połączonej fabryki][cf-img-factories]

1. Na **panelu Fabryki** jest wyświetlana lista fabryk dla tego rozwiązania.

2. Na liście początkowo jest widocznych 6 fabryk utworzonych w procesie aprowizacji. Do rozwiązania można dodać dodatkowe symulowane i fizyczne urządzenia.

3. Aby wyświetlić szczegóły fabryki, kliknij wiersz na liście fabryk.

4. Aby wyświetlić szczegóły linii produkcyjnej, kliknij wiersz na liście.

5. Aby wyświetlić opublikowane węzły OPC UA stacji z linii produkcyjnej, kliknij wiersz na liście.

6. Aby wyświetlić szczegóły dotyczące określonego węzła w stacji, kliknij wiersz na liście. Ta akcja uruchamia panel kontekstowy z wizualizacjami usługi Time Series Insights. Kliknięcie takiego wykresu umożliwia przeprowadzenie dalszej analizy w środowisku eksploratora usługi Time Series Insights.

## <a name="view-map"></a>Wyświetlanie mapy

Jeśli Twoja subskrypcja ma dostęp do interfejsu API usługi Mapy Bing, na mapie *Fabryki* są wyświetlane lokalizacje geograficzne i status wszystkich fabryk w rozwiązaniu. Aby przejść do szczegółów lokalizacji, kliknij lokalizacje wyświetlane na mapie.

![Mapa akceleratora rozwiązania połączonej fabryki][cf-img-map]

## <a name="view-alarms"></a>Wyświetlanie alarmów

Na panelu **Alarmy** są wyświetlane alarmy wygenerowane z powodu wystąpienia wartości zgłoszonej lub wartości obliczonej ogólnej wydajności sprzętu bądź kluczowego wskaźnika wydajności, która przekroczyła skonfigurowaną wartość progową. Na tym panelu pojawiają się alarmy dotyczące każdego poziomu hierarchii: od poziomu stacji do widoku globalnego. Alarmy zawierają opis alarmu, datę, godzinę, lokalizację i liczbę wystąpień. Korzystając z danych usługi Time Series Insights, można uzyskać wgląd w dane, które spowodowały wystąpienie alarmu. Dane usługi Time Series Insights są wizualizowane w alarmach, gdy jest to uzasadnione. Jeśli jesteś administratorem, możesz wykonywać domyślne akcje związane z alarmami, takie jak:

* Zamknięcie alarmu.
* Potwierdzenie alarmu.

Opcjonalnie możesz wykonać bardziej złożone akcje. Na przykład dla węzła OPC UA Ciśnienie zestawu można wykonać następujące akcje:

* Wyświetlenie dodatkowych informacji na stronie internetowej w nowym oknie przeglądarki.
* Wyeliminowanie przyczyny alarmu za pomocą wywołania metody OPC UA na urządzeniu.
* Pominięcie dostępności domyślnych akcji.

    ![Alarmy akceleratora rozwiązania połączonej fabryki][cf-img-alerts]

> [!NOTE]
> Te alarmy są generowane na podstawie reguł określonych w pliku konfiguracyjnym w akceleratorze rozwiązania. Te reguły mogą generować alarmy, gdy wartości ogólnej wydajności sprzętu lub kluczowego wskaźnika wydajności bądź wartości węzła OPC UA przekraczają skonfigurowany dla nich próg.

1. Na **panelu Alarmy** są wyświetlane alarmy wygenerowane w tym rozwiązaniu.

2. Aby wyświetlić szczegóły alarmu, kliknij jego nazwę na panelu alarmów.

3. W celu przeprowadzenia dalszej analizy danych alarmu kliknij wykres na panelu alarmów, aby otworzyć środowiska eksploratora usługi Time Series Insights.

4. Aby rozwiązać alarm, na panelu alarmów jest dostępnych kilka akcji. Wybierz odpowiednią opcję i kliknij przycisk polecenia wykonania akcji.

## <a name="view-overall-equipment-efficiency"></a>Wyświetlanie ogólnej wydajności sprzętu

Ogólna wydajność sprzętu (OEE) pozwala ocenić wydajność procesu produkcyjnego przy użyciu kluczowych parametrów operacyjnych powiązanych z produkcją. Ogólna wydajność sprzętu to standardowy pomiar branżowy obliczany przez pomnożenie współczynników dostępności, wydajności i jakości: OEE = dostępność x wydajność x jakość.

![Ogólna wydajność sprzętu akceleratora rozwiązania połączonej fabryki][cf-img-oee]

1. Aby wyświetlić ogólną wydajność sprzętu na dowolnym poziomie hierarchii, przejdź do wymaganego widoku. Ogólna wydajność sprzętu dla tego widoku będzie wyświetlana na panelu razem ze wszystkimi elementami składowymi procentowej wartości OEE.

2. W celu dalszej analizy ogólnej wydajności sprzętu na dowolnym poziomie w danych hierarchii, kliknij pozycję dotyczącą wartości procentowej dla ogólnej wydajności sprzętu, dostępności, wydajności lub jakości. Zostanie wyświetlony panel kontekstowy zawierający wizualizacje usługi Time Series Insights z danymi z ostatniej godziny, ostatnich 24 godzin i ostatnich 7 dni.

    ![Wizualizacja usługi TSI w akceleratorze rozwiązania połączonej fabryki][cf-img-tsi-visualization]

3. Aby dalej analizować dane alarmów, kliknij wykres na panelu alarmów. Spowoduje to otwarcie środowiska eksploratora usługi Time Series Insights.

    ![Eksplorator usługi TSI w akceleratorze rozwiązania połączonej fabryki][cf-img-tsi-explorer]

## <a name="view-key-performance-indicators"></a>Wyświetlanie kluczowych wskaźników wydajności

Rozwiązanie zapewnia dwa kluczowe wskaźniki wydajności: *liczba jednostek na godzinę* i *zużycie energii w kWh*.

![Kluczowy wskaźnik wydajności akceleratora rozwiązania połączonej fabryki][cf-img-kpi]

1. Aby wyświetlić liczbę jednostek na godzinę lub ilość zużytej energii na dowolnym poziomie hierarchii, przejdź do wymaganego widoku. Liczba jednostek na godzinę i ilość zużytej energii zostaną wyświetlone na panelu.

2. Aby przeprowadzić analizę liczby jednostek na godzinę lub ilości zużytej energii na dowolnym poziomie w ramach hierarchii, kliknij miernik na panelu **Kluczowe wskaźniki wydajności**. Zostanie wyświetlony panel kontekstowy zawierający wizualizacje usługi Time Series Insights umożliwiające przeglądanie danych z ostatniej godziny, ostatnich 24 godzin i ostatnich 7 dni.

## <a name="scenario-review"></a>Przegląd scenariusza

W tym scenariuszu na pulpicie nawigacyjnym były monitorowane wartości ogólnej wydajności sprzętu i kluczowych wskaźników wydajności fabryk. Następnie użyto usługi Time Series Insights w celu udostępnienia większej ilości informacji ułatwiających dalsze badanie danych telemetrycznych ogólnej wydajności sprzętu i kluczowych wskaźników wydajności pomagających w wykrywaniu nieprawidłowości. Użyto również panelu alarmów do wyświetlenia problemów dotyczących fabryk i skorzystano z dostępnych akcji w celu rozwiązania alarmu.

## <a name="other-features"></a>Pozostałe funkcje

W poniższych sekcjach opisano niektóre dodatkowe funkcje rozwiązania połączonej fabryki, które nie zostały opisane w poprzednim scenariuszu.

## <a name="apply-filters"></a>Stosowanie filtrów

1. Kliknij ikonę **lejka**, aby wyświetlić listę dostępnych filtrów na panelu lokalizacji fabryk lub panelu alarmów.

2. Poniżej przedstawiono panel filtrów.

    ![Filtry akceleratora rozwiązania połączonej fabryki][cf-img-alert-filter]

3. Wybierz wymagany filtr. Możesz również wpisać dowolny tekst w polach filtrów.

4. Wybrany filtr zostanie zastosowany. Stan filtru jest również wyświetlany na pulpicie nawigacyjnym za pomocą lejka w tabelach fabryk i alarmów.

    ![Filtry akceleratora rozwiązania połączonej fabryki][cf-img-alert-filter-funnel]

    > [!NOTE]
    > Aktywny filtr nie ma wpływu na wyświetlanie wartości ogólnej wydajności sprzętu i kluczowych wskaźników wydajności. Filtrowana jest tylko zawartość listy.

5. Aby usunąć filtr, kliknij ikonę lejka, a następnie kliknij filtr na panelu kontekstowym filtru. W tabelach fabryk i alarmów zostanie wyświetlony tekst **Wszystkie**.

## <a name="browse-an-opc-ua-server"></a>Przeglądanie serwera OPC UA

Podczas wdrażania akceleratora rozwiązania następuje automatyczna aprowizacja symulowanych serwerów OPC UA, które można przeglądać za pomocą przeglądarki rozwiązania. Są to *symulowane serwery OPC UA*. Symulowane serwery ułatwiają eksperymentowanie z akceleratorem rozwiązania bez konieczności wdrażania prawdziwych serwerów fizycznych. Jeśli chcesz połączyć prawdziwy serwer OPC UA z rozwiązaniem, zobacz samouczek [Connect your OPC UA device to the Connected Factory solution accelerator][lnk-connect-cf] (Łączenie urządzenia OPC UA z akceleratorem rozwiązania połączonej fabryki).

1. Kliknij **ikonę przeglądarki** na pasku nawigacyjnym pulpitu nawigacyjnego.

    ![Przeglądarka serwerów w akceleratorze rozwiązania połączonej fabryki][cf-img-server-browser]

2. Wybierz jeden z serwerów ze wstępnie skonfigurowanej listy. Ta lista zawiera serwery wdrożone w akceleratorze rozwiązania.

    ![Wybór serwerów w akceleratorze rozwiązania połączonej fabryki][cf-img-server-choice]

3. Kliknij przycisk **Połącz**. Zostanie wyświetlone okno dialogowe zabezpieczeń. W tej symulacji można bezpiecznie kliknąć pozycję **Kontynuuj**.

4. Aby rozwinąć dowolny węzeł w drzewie serwerów, kliknij go. Węzły, które publikują dane telemetryczne, mają obok swoich nazw znaczniki.

    ![Drzewo serwerów w akceleratorze rozwiązania połączonej fabryki][cf-img-server-tree]

5. Kliknij element prawym przyciskiem myszy, aby odczytać, zapisać, opublikować lub wywołać ten węzeł. Dostępne akcje zależą od uprawnień posiadanych przez użytkownika i atrybutów węzła. Wybranie opcji odczytu powoduje wyświetlenie panelu kontekstowego z wartością konkretnego węzła. Wybranie opcji zapisu powoduje wyświetlenie panelu kontekstowego, w którym można wprowadzić nową wartość. Wybranie opcji wywołania powoduje wyświetlenie węzła i umożliwia wprowadzenie parametrów wywołania.

## <a name="publish-a-node"></a>Publikowanie węzła

Podczas przeglądania *symulowanego serwera OPC UA* można również opublikować nowe węzły. W rozwiązaniu można przeanalizować dane telemetryczne z tych węzłów. Te *symulowane serwery OPC UA* ułatwiają eksperymentowanie z akceleratorem rozwiązania bez konieczności wdrażania prawdziwych urządzeń fizycznych.

1. W drzewie przeglądarki serwerów OPC UA przejdź do węzła, który chcesz opublikować.

2. Kliknij węzeł prawym przyciskiem myszy.

3. Wybierz polecenie **Publikuj**.

    ![Publikowanie węzła przez połączoną fabrykę][cf-img-publish-node]

4. Wyświetlony panel kontekstowy informuje, że publikowanie zakończyło się pomyślnie. Węzeł pojawi się w widoku poziomu stacji z umieszczonym obok znacznikiem.

    ![Pomyślne publikowanie we wstępnie skonfigurowanym rozwiązaniu połączonej fabryki][cf-img-publish-success]

## <a name="command-and-control"></a>Sterowanie i kontrola

Połączona fabryka umożliwia sterowanie urządzeniami przemysłowymi i kontrolowanie ich bezpośrednio z chmury. Tej funkcji można używać do reagowania na alarmy generowane przez urządzenie. Można na przykład wysłać polecenie do urządzenia z chmury. Dostępne polecenia można znaleźć w węźle **StationCommands** w drzewie przeglądarki serwerów OPC UA. W tym scenariuszu otwierasz zawór bezpieczeństwa na stanowisku montażowym linii produkcyjnej w Monachium. Aby móc skorzystać z funkcji sterowania i kontroli, musisz mieć rolę **administratora** we wdrożeniu akceleratora rozwiązania.

1. W drzewie przeglądarki serwerów OPC UA przejdź do węzła **StationCommands**.

2. Wybierz polecenie, którego chcesz użyć.

3. Kliknij węzeł prawym przyciskiem myszy.

4. Wybierz polecenie **Wywołaj**.

    ![Polecenie wywoływania w akceleratorze rozwiązania połączonej fabryki][cf-img-call-command]

5. Zostanie wyświetlony panel kontekstowy z informacjami o metodzie, która ma zostać wywołana, i szczegółami parametru, jeśli ma to zastosowanie.

6. Wybierz polecenie **Wywołaj**.

    ![Kontekst wywołania w akceleratorze rozwiązania połączonej fabryki][cf-img-call-context]

7. Panel kontekstowy zostanie zaktualizowany o informacje o powodzeniu wywołania metody. Powodzenie wywołania metody można sprawdzić, odczytując zaktualizowaną w wyniku wywołania wartość węzła ciśnienia.

    ![Powodzenie wywołania w akceleratorze rozwiązania połączonej fabryki][cf-img-call-success]

## <a name="behind-the-scenes"></a>Za kulisami

Podczas wdrażania akceleratora rozwiązania jest tworzonych wiele zasobów w ramach wybranej subskrypcji platformy Azure. Można je wyświetlić w witrynie Azure [Portal][lnk-portal]. W procesie wdrażania jest tworzona **grupa zasobów** o nazwie odpowiadającej nazwie akceleratora rozwiązania:

![Akcelerator rozwiązania w witrynie Azure Portal][img-cf-portal]

Aby wyświetlić ustawienia danego zasobu, wybierz go z listy w grupie zasobów.

Można również wyświetlić kod źródłowy akceleratora rozwiązania. Kod źródłowy akceleratora rozwiązania połączonej fabryki znajduje się w repozytorium GitHub [azure-iot-connected-factory][lnk-cfgithub]:

Gdy wszystko będzie gotowe, możesz usunąć akcelerator rozwiązania z subskrypcji platformy Azure w witrynie [azureiotsuite.com][lnk-azureiotsuite]. Ta witryna umożliwia łatwe usunięcie wszystkich zasobów, które zostały aprowizowane po utworzeniu akceleratora rozwiązania.

> [!NOTE]
> Aby mieć pewność, że zostaną usunięte wszystkie elementy powiązane z akceleratorem rozwiązania, usuń akcelerator w witrynie [azureiotsuite.com][lnk-azureiotsuite]. Nie należy usuwać grupy zasobów w portalu.

## <a name="next-steps"></a>Następne kroki

Teraz, kiedy został wdrożony akcelerator rozwiązania, który działa, możesz kontynuować poznawanie akceleratorów rozwiązań IoT, czytając następujące artykuły:

* [Przewodnik po akceleratorze rozwiązania połączonej fabryki][lnk-rm-walkthrough]
* [Łączenie urządzenia z akceleratorem rozwiązania połączonej fabryki][lnk-connect-cf]
* [Uprawnienia w witrynie azureiotsuite.com][lnk-permissions]

[img-cf-home]:media/iot-accelerators-connected-factory-overview/cf-dashboard.png
[img-launch-solution]: media/iot-accelerators-connected-factory-overview/launch-cf.png
[cf-img-menu]: media/iot-accelerators-connected-factory-overview/cf-dashboard-menu.png
[cf-img-factories]:media/iot-accelerators-connected-factory-overview/cf-dashboard-factory.png
[cf-img-map]:media/iot-accelerators-connected-factory-overview/cf-dashboard-map.png
[cf-img-alerts]:media/iot-accelerators-connected-factory-overview/cf-dashboard-alerts.png
[cf-img-oee]:media/iot-accelerators-connected-factory-overview/cf-dashboard-oee.png
[cf-img-kpi]:media/iot-accelerators-connected-factory-overview/cf-dashboard-kpi.png
[cf-img-tsi-visualization]:media/iot-accelerators-connected-factory-overview/cf-dashboard-tsi.png
[cf-img-tsi-explorer]:media/iot-accelerators-connected-factory-overview/tsi-explorer.png
[cf-img-server-browser]: media/iot-accelerators-connected-factory-overview/cf-dashboard-browser.png
[cf-img-server-choice]: media/iot-accelerators-connected-factory-overview/cf-select-server.png
[cf-img-server-tree]:media/iot-accelerators-connected-factory-overview/cf-server-tree.png
[cf-img-publish-node]:media/iot-accelerators-connected-factory-overview/cf-publish-node1.png
[cf-img-publish-success]:media/iot-accelerators-connected-factory-overview/cf-publish-success.png
[cf-img-call-command]:media/iot-accelerators-connected-factory-overview/cf-command-and-control-call.png
[cf-img-call-context]:media/iot-accelerators-connected-factory-overview/cf-command-and-control-call-button.png
[cf-img-call-success]:media/iot-accelerators-connected-factory-overview/cf-command-and-control-succeed.png
[img-cf-portal]:media/iot-accelerators-connected-factory-overview/cf-resource-group.png
[cf-img-alert-filter]:media/iot-accelerators-connected-factory-overview/cf-filter.png
[cf-img-alert-filter-funnel]:media/iot-accelerators-connected-factory-overview/cf-filter-funnel.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]:iot-accelerators-what-are-solution-accelerators.md
[lnk-azureiotsuite]: https://www.azureiotsolutions.com
[lnk-portal]: http://portal.azure.com/
[lnk-cfgithub]: https://github.com/Azure/azure-iot-connected-factory
[lnk-rm-walkthrough]:iot-accelerators-connected-factory-sample-walkthrough.md
[lnk-connect-cf]:iot-accelerators-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-accelerators-permissions.md
[lnk-faq]: iot-accelerators-faq.md
