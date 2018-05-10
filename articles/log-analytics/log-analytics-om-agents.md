---
title: Łączenie programu Operations Manager do analizy dzienników | Dokumentacja firmy Microsoft
description: Aby chronić inwestycję istniejących w programie System Center Operations Manager i rozszerzone możliwości za pomocą analizy dzienników, Operations Manager można zintegrować z obszaru roboczego.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 245ef71e-15a2-4be8-81a1-60101ee2f6e6
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: magoedte
ms.openlocfilehash: 84eabef06b4d2ad71e6d9a947a77589f9159e030
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="connect-operations-manager-to-log-analytics"></a>Łączenie programu Operations Manager do analizy dzienników
Aby chronić inwestycję istniejących w programie System Center Operations Manager i rozszerzone możliwości za pomocą analizy dzienników, Operations Manager można zintegrować z obszaru roboczego analizy dzienników.  Dzięki temu, że wykorzystać możliwości analizy dzienników podczas dalszego korzystania z programu Operations Manager:

* Monitorowanie kondycji usług IT z programem Operations Manager
* Obsługa integracji z rozwiązaniami Zarządzanie usługami IT — Obsługa zarządzania zdarzeniami i problemów
* Zarządzanie cyklem życia agenci Wdrożeni do lokalnych i chmurze publicznej maszyn wirtualnych IaaS, które należy monitorować za pomocą programu Operations Manager

Integracja z programem System Center Operations Manager dodaje wartość strategię operacji usługi za pomocą szybkości i wydajności usługi Analiza dzienników w zbierania, przechowywania i analizowania danych z programu Operations Manager.  Zaloguj się Analytics pomaga korelując i dążyć do identyfikowania usterek, problemów i udostępniając cykle w związku z procesu zarządzania istniejący problem.  Zwiększa elastyczność aparat wyszukiwania, aby zbadać wydajność, zdarzeń i alertów siły analizy dzienników przedstawiono danych sformatowanego pulpity nawigacyjne i funkcji raportowania, aby udostępnić te dane w przejrzysty sposób, w complimenting programu Operations Manager.

Agenci raportowania w grupie zarządzania programu Operations Manager zbieranie danych z serwerów na podstawie analizy dzienników źródła danych i rozwiązania, jakie włączono w obszarze roboczym.  W zależności od rozwiązania włączone, dane są albo wysyłane bezpośrednio z serwera zarządzania programu Operations Manager z usługą lub ze względu na ilość danych zebranych w systemie zarządzane z wykorzystaniem agentów są wysyłane bezpośrednio z agenta do analizy dzienników. Serwer zarządzania przekazuje dane bezpośrednio z usługą; nigdy nie jest ona zapisywana w bazie danych magazynu danych lub operacyjne.  Gdy serwer zarządzania utraci łączność z analizy dzienników, buforować danych lokalnie, dopóki komunikacji nie zostanie nawiązane ponownie z analizy dzienników.  Jeśli serwer zarządzania jest w trybie offline z powodu zaplanowanej konserwacji i nieplanowanych awarii, inny serwer zarządzania w grupie zarządzania wznawia łączności z analizy dzienników.  

Na poniższym diagramie przedstawiono połączenia między agentami a serwerami zarządzania w grupie zarządzania programu System Center Operations Manager i analizy dziennika, w tym kierunku i portów.   

![oms-operations-manager-integration-diagram](./media/log-analytics-om-agents/oms-operations-manager-connection.png)

Jeśli zasady zabezpieczeń IT nie zezwalają na komputerach w sieci, aby nawiązać połączenie z Internetem, aby połączyć się z bramą OMS do odbierania informacji o konfiguracji i wysyłania danych zebranych w zależności od rozwiązania włączone można skonfigurować serwery zarządzania.  Aby uzyskać więcej informacji i kroki dotyczące sposobu konfigurowania grupę zarządzania programu Operations Manager do komunikowania się za pośrednictwem bramy OMS z usługą analizy dzienników, zobacz [łączenia komputerów przy użyciu bramy OMS OMS](log-analytics-oms-gateway.md).  

## <a name="system-requirements"></a>Wymagania systemowe
Przed rozpoczęciem należy przejrzeć następujące informacje, aby sprawdzić, czy zostały spełnione wymagania wstępne.

* Log Analytics obsługuje tylko UR6 programu System Center Operations Manager 1801, programu Operations Manager 2016, Operations Manager 2012 z dodatkiem SP1 lub nowszej, a programu Operations Manager 2012 R2 UR2 i większa.  Obsługa serwera proxy została dodana w programach Operations Manager 2012 SP1 UR7 i Operations Manager 2012 R2 UR3.
* Wszystkie agenty programu Operations Manager musi spełniać wymagania minimalne pomocy technicznej. Upewnij się, że agenci są przy minimalnej aktualizacji, w przeciwnym razie ruchu agenta systemu Windows może zakończyć się niepowodzeniem i wiele błędów może wypełnić dziennik zdarzeń programu Operations Manager.
* Obszar roboczy analizy dzienników.  Aby uzyskać więcej informacji, przejrzyj [wprowadzenie do analizy dzienników](log-analytics-get-started.md).

### <a name="network"></a>Sieć
Informacje poniżej listy proxy i zapory konfiguracji wymaganych informacji dla agenta programu Operations Manager, serwerów zarządzania i konsoli operacje, aby komunikować się z analizy dzienników.  Ruch z każdego składnika jest wychodzący z sieci lokalnej z usługą analizy dzienników.     

|Zasób | Numer portu| Obejście kontroli HTTP|  
|---------|------|-----------------------|  
|**Agent**|||  
|\*.ods.opinsights.azure.com| 443 |Yes|  
|\*.oms.opinsights.azure.com| 443|Yes|  
|\*.blob.core.windows.net| 443|Yes|  
|\*.azure-automation.net| 443|Yes|  
|**Serwer zarządzania**|||  
|\*.service.opinsights.azure.com| 443||  
|\*.blob.core.windows.net| 443| Yes|  
|\*.ods.opinsights.azure.com| 443| Yes|  
|*.azure-automation.net | 443| Yes|  
|**Konsola programu Operations Manager z usługą OMS**|||  
|service.systemcenteradvisor.com| 443||  
|\*.service.opinsights.azure.com| 443||  
|\*.live.com| 80 i 443||  
|\*.microsoft.com| 80 i 443||  
|\*.microsoftonline.com| 80 i 443||  
|\*.mms.microsoft.com| 80 i 443||  
|login.windows.net| 80 i 443||  
|portal.loganalytics.io| 80 i 443||
|api.loganalytics.io| 80 i 443||
|docs.loganalytics.io| 80 i 443||  

## <a name="connecting-operations-manager-to-log-analytics"></a>Łączenie programu Operations Manager do analizy dzienników
Wykonaj poniższą sekwencję czynności, aby skonfigurować grupę zarządzania programu Operations Manager do nawiązania połączenia co obszarów roboczych analizy dzienników.

Jeśli po raz pierwszy, rejestruje grupę zarządzania programu Operations Manager z obszaru roboczego analizy dzienników i serwery zarządzania muszą komunikować się z usługą za pośrednictwem serwera proxy lub serwera bramy OMS, można określić konfigurację serwera proxy dla Grupa zarządzania nie jest dostępna w konsoli operacje.  Grupa zarządzania nie mają być pomyślnie zarejestrowane w usłudze, zanim ta opcja jest dostępna.  Musisz zaktualizować konfigurację serwera proxy systemu za pomocą narzędzia Netsh w systemie konsoli operacje z systemem do skonfigurowania integracji i wszystkich serwerów zarządzania w grupie zarządzania.  

1. Otwórz podniesionego wiersza poleceń.
   a. Przejdź do **Start** i typ **cmd**.
   b. Kliknij prawym przyciskiem myszy **wiersza polecenia** i wybierz polecenie Uruchom jako administrator **.
2. Wprowadź następujące polecenie i naciśnij klawisz **Enter**:

    `netsh winhttp set proxy <proxy>:<port>`

Po zakończeniu następujące kroki, aby zintegrować z analizy dzienników, można usunąć konfiguracji, uruchamiając `netsh winhttp reset proxy` , a następnie użyj **Konfiguruj serwer proxy** opcję w konsoli operacje, aby określić serwer proxy lub OMS Serwer bramy. 

1. W konsoli programu Operations Manager, wybierz **administracji** obszaru roboczego.
2. Rozwiń węzeł usługi Operations Management Suite, a następnie kliknij przycisk **połączenia**.
3. Kliknij przycisk **przeprowadzić rejestrację w usłudze Operations Management Suite** łącza.
4. Na **Kreator dołączania Operations Management Suite: uwierzytelnianie** strony, wprowadź adres e-mail lub numer telefonu i hasło konta administratora, który jest skojarzony z subskrypcją pakietu OMS, a następnie kliknij przycisk  **Zaloguj się**.
5. Po pomyślnie uwierzytelnienia, na **Kreator dołączania Operations Management Suite: Wybierz obszar roboczy** strony, zostanie wyświetlony monit wybierz obszar roboczy analizy dzienników.  Jeśli masz więcej niż jeden obszar roboczy, wybierz obszar roboczy, aby zarejestrować grupę zarządzania programu Operations Manager z listy rozwijanej, a następnie kliknij przycisk **dalej**.
   
   > [!NOTE]
   > Operations Manager obsługuje tylko jeden obszar roboczy analizy dzienników naraz. Połączenie i komputerów, które zostały zarejestrowane do analizy dzienników z poprzednich obszaru roboczego są usuwane z analizy dzienników.
   > 
   > 
6. Na **Kreator dołączania Operations Management Suite: Podsumowanie** , Potwierdź ustawienia i jeśli są poprawne, kliknij przycisk **Utwórz**.
7. Na **Kreator dołączania Operations Management Suite: Zakończ** kliknij przycisk **Zamknij**.

### <a name="add-agent-managed-computers"></a>Dodaj komputery zarządzane z wykorzystaniem agentów
Po skonfigurowaniu integracji z obszaru roboczego analizy dzienników, to tylko nawiązuje połączenie z usługą, zbierane żadne dane z agentów raportowania do grupy zarządzania. Nie będzie to nastąpić dopiero po skonfigurowaniu, które określone komputery zarządzane z wykorzystaniem agentów zbiera dane analizy dziennika. Możesz wybrać obiekty komputerów indywidualnie lub wybrać grupy, który zawiera obiekty typu komputer z systemem Windows. Nie można wybrać grupę zawierającą wystąpienia klasy innego, takich jak dyski logiczne w systemie lub bazy danych SQL.

1. Otwórz konsolę programu Operations Manager i wybierz obszar roboczy **Administracja**.
2. Rozwiń węzeł usługi Operations Management Suite, a następnie kliknij przycisk **połączenia**.
3. Kliknij przycisk **dodać grupy** łącze w obszarze Akcje pozycji w prawej części okienka.
4. W **wyszukiwanie komputera** okno dialogowe, możesz wyszukać komputerów lub grup monitorowanych przez program Operations Manager. Wybierz komputery lub grupy, aby rozpocząć korzystanie z analizy dzienników, kliknij przycisk **Dodaj**, a następnie kliknij przycisk **OK**.

Można wyświetlić komputerów i grup skonfigurowanych do zbierania danych z komputerów zarządzanych węźle Operations Management Suite w **administracji** obszaru roboczego w konsoli operacje.  W tym miejscu można Dodaj lub usuń komputery i grupy odpowiednio do potrzeb.

### <a name="configure-proxy-settings-in-the-operations-console"></a>Skonfiguruj ustawienia serwera proxy w konsoli operacje
Jeśli wewnętrzny serwer proxy jest między grupą zarządzania a usługą Analtyics dziennika, należy wykonać następujące czynności.  Te ustawienia są centralnie zarządzanego z grupy zarządzania i przekazane do systemów zarządzane z wykorzystaniem agentów, które znajdują się w zakresie zbierania danych dla analizy dzienników.  Jest to przydatne w przypadku gdy niektórych rozwiązań obejścia serwera zarządzania i przekazuj dane bezpośrednio do usługi.

1. Otwórz konsolę programu Operations Manager i wybierz obszar roboczy **Administracja**.
2. Rozwiń pozycję Operations Management Suite, a następnie kliknij przycisk **połączenia**.
3. W widoku Połączenie OMS kliknij pozycję **Konfiguruj serwer proxy**.
4. Na **Kreator Operations Management Suite: serwer Proxy** wybierz pozycję **Użyj serwera proxy, aby uzyskać dostępu do usługi Operations Management Suite**, a następnie wpisz adres URL z numerem portu, na przykład http://corpproxy:80 a następnie kliknij przycisk **Zakończ**.

Jeśli serwer proxy wymaga uwierzytelnienia, wykonaj następujące kroki, aby skonfigurować poświadczenia i ustawienia, które należy obejmie zarządzanych komputerów, które raporty do OMS w grupie zarządzania.

1. Otwórz konsolę programu Operations Manager i wybierz obszar roboczy **Administracja**.
2. W obszarze **Konfiguracja Uruchom jako** wybierz pozycję **Profile**.
3. Otwórz profil **Serwer proxy profilu Uruchom jako usługi System Center Advisor**.
4. W Kreatora profilu Uruchom jako kliknij przycisk Dodaj, aby użyć konta Uruchom jako. Można utworzyć [konta Uruchom jako](https://technet.microsoft.com/library/hh321655.aspx) lub użyj istniejącego konta. Konto to musi mieć wystarczające uprawnienia do komunikacji za pośrednictwem serwera proxy.
5. Aby ustawić konto do zarządzania, wybierz **wybraną klasę, grupę lub obiekt**, kliknij przycisk **wybierz...** a następnie kliknij przycisk **grupy...** Aby otworzyć **wyszukiwania grupy** pole.
6. Wyszukaj, a następnie wybierz **grupy monitorowania serwera programu Microsoft System Center Advisor**.  Kliknij przycisk **OK** po wybraniu grupy, aby zamknąć **wyszukiwania grupy** pole.
7. Kliknij przycisk **OK** zamknąć **Dodaj konto Uruchom jako** pola.
8. Kliknij przycisk **zapisać** aby zakończyć pracę kreatora i zapisać zmiany.

Po utworzeniu połączenia i konfigurowanie agentów, które będą zbierania i raportowania danych z analizą dzienników, następujące konfiguracja zostanie zastosowana do grupy zarządzania, niekoniecznie w kolejności:

* Konto Uruchom jako **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** jest tworzony.  Jest on skojarzony z profilem Uruchom jako **Microsoft System Center Advisor Uruchom jako profil obiektu Blob** i dotyczy dwóch klas - **serwera zbierania** i **grupa zarządzania programu Operations Manager** .
* Są tworzone dwa łączniki.  Pierwszy nosi nazwę **Microsoft.SystemCenter.Advisor.DataConnector** i jest automatycznie konfigurowana subskrypcji, która przekazuje wszystkie alerty generowane ze wystąpień wszystkie klasy w grupie zarządzania w celu analizy dzienników. Drugi łącznik jest **łącznik usługi Advisor**, który jest odpowiedzialny za udostępnianie danych i komunikacji z usługą sieci web OMS.
* Agenci i grup, które zostały wybrane do zbierania danych w grupie zarządzania jest dodawane do **grupy monitorowania serwera programu Microsoft System Center Advisor**.

## <a name="management-pack-updates"></a>Aktualizacji pakietu administracyjnego
Po zakończeniu konfiguracji grupy zarządzania programu Operations Manager ustanawia połączenie z usługą analizy dzienników.  Serwer zarządzania synchronizuje się z usługą sieci web i odbierać informacje o zaktualizowanej konfiguracji w postaci pakietów administracyjnych dla rozwiązań włączonego, które integrują się z programem Operations Manager.   Operations Manager sprawdza dostępność aktualizacji tych pakietów administracyjnych i automatycznie pobrać i importowane, gdy są one dostępne.  Istnieją dwie reguły w szczególności kontrolować to zachowanie, które:

* **Microsoft.SystemCenter.Advisor.MPUpdate** -aktualizacji podstawowej pakietów administracyjnych analizy dzienników. Domyślnie uruchamiane co 12 godzin.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** — aktualizacje pakietów administracyjnych rozwiązania włączona w obszarze roboczym. Domyślnie uruchamiane co pięć (5) minut.

Można zastąpić te dwie reguły, aby uniknąć automatycznego pobierania wyłączając je, lub zmodyfikować częstotliwości jak często serwer zarządzania synchronizuje się z usługą OMS, aby ustalić, czy nowy pakiet zarządzania jest dostępny i powinien zostać pobrany.  Wykonaj kroki [jak zastąpienie zasady lub monitora](https://technet.microsoft.com/library/hh212869.aspx) do modyfikowania **częstotliwość** parametru z wartością w sekundach, aby zmienić harmonogram synchronizacji lub zmodyfikować **włączone**parametr można wyłączyć reguły.  Cel przesłonięcia względem wszystkich obiektów klasy grupa zarządzania programu Operations Manager.

Jeśli chcesz kontynuować następujące istniejące zmiany kontroli procesu kontroli wersji pakietu zarządzania w danej grupie zarządzania w środowisku produkcyjnym, można wyłączyć reguły i włączyć je w określonych godzinach, kiedy aktualizacje są dozwolone. Jeśli masz rozwoju lub grupy zarządzania w odpowiedzi na pytania w danym środowisku i ma łączność z Internetem, można skonfigurować tej grupy zarządzania z obszaru roboczego analizy dzienników, aby obsługiwać ten scenariusz.  Dzięki temu można Przegląd i ocena iteracyjne wersje pakietów administracyjnych analizy dzienników przed ich do grupy zarządzania produkcji.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Przełącz grupę programu Operations Manager do nowego obszaru roboczego analizy dzienników
1. Zaloguj się do witryny Azure Portal na stronie [https://portal.azure.com](https://portal.azure.com).
2. W witrynie Azure Portal kliknij pozycję **Więcej usług** w lewym dolnym rogu. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz **analizy dzienników** , a następnie utworzyć obszaru roboczego.  
3. Otwórz konsolę programu Operations Manager przy użyciu konta należącego do roli Administratorzy programu Operations Manager i wybierz **administracji** obszaru roboczego.
4. Rozwiń pozycję Operations Management Suite, a następnie wybierz **połączenia**.
5. Wybierz **skonfiguruj ponownie operację Management Suite** łącze na środku strony panelu.
6. Postępuj zgodnie z **Kreator dołączania Operations Management Suite** , a następnie wprowadź adres e-mail, numer telefonu i hasło konta administratora, który jest skojarzony z nowego obszaru roboczego analizy dzienników.
   
   > [!NOTE]
   > **Kreator dołączania Operations Management Suite: Wybierz obszar roboczy** strona przedstawia istniejący obszar roboczy, który jest używany.
   > 
   > 

## <a name="validate-operations-manager-integration-with-log-analytics"></a>Sprawdź poprawność integracji programu Operations Manager z analizy dzienników
Istnieje kilka sposobów można zweryfikować, że analizy dzienników do integracji programu Operations Manager zakończyła się powodzeniem.

### <a name="to-confirm-integration-from-the-azure-portal"></a>Aby potwierdzić integracji z portalu Azure
1. W witrynie Azure Portal kliknij pozycję **Więcej usług** w lewym dolnym rogu. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane.
2. Na liście obszarów roboczych usługi Analiza dzienników wybierz odpowiednie obszaru roboczego.  
3. Wybierz **Zaawansowane ustawienia**, wybierz pozycję **połączonych źródeł**, a następnie wybierz **programu System Center**. 
4. W tabeli w sekcji System Center Operations Manager powinna zostać wyświetlona nazwa grupy zarządzania wymienione do liczby agentów i stan podczas ostatniego Odebrano dane.
   
   ![oms-settings-connectedsources](./media/log-analytics-om-agents/oms-settings-connectedsources.png)

### <a name="to-confirm-integration-from-the-operations-console"></a>Aby potwierdzić integracji z poziomu konsoli operacje
1. Otwórz konsolę programu Operations Manager i wybierz obszar roboczy **Administracja**.
2. Wybierz **pakietów administracyjnych** i **Wyszukaj:** polu tekstowym wpisz **Advisor** lub **analizy**.
3. W zależności od rozwiązania, które aktywowano zostanie wyświetlony odpowiedni pakiet administracyjny, w wynikach wyszukiwania.  Na przykład jeśli włączono rozwiązania zarządzania alertami, pakiet administracyjny programu Microsoft System Center Advisor alertu Management jest na liście.
4. Z **monitorowanie** wyświetlić, przejdź do **operacji zarządzania Suite\Health stanu** widoku.  Wybierz serwer zarządzania, w obszarze **stan serwera zarządzania** okienku i w **: widok szczegółów** okienko upewnij się, wartość właściwości **adres URI usługi uwierzytelniania** zgodny Identyfikator dziennika analizy obszaru roboczego.
   
   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/log-analytics-om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-log-analytics"></a>Usuń Integracja z usługą analizy dzienników
Gdy nie są już potrzebne integracji między grupę zarządzania programu Operations Manager i obszaru roboczego analizy dzienników, istnieje kilka kroków wymaganych do prawidłowo Usuń połączenie i konfigurację w grupie zarządzania. Poniższa procedura ma możesz zaktualizować obszar roboczy analizy dzienników, usuwając odwołanie do grupy zarządzania, Usuń łączniki analizy dzienników, a następnie usuń pakiety administracyjne obsługujące integrację z usługą.   

Pakiety administracyjne rozwiązań włączono integrujące się z programem Operations Manager i pakiety administracyjne wymagane do obsługi integracji z usługą analizy dzienników nie można łatwo usunąć z grupy zarządzania.  Jest tak, ponieważ niektóre z pakietów administracyjnych analizy dzienników są zależne od innych pakietów administracyjnych pokrewne.  Aby usunąć pakietów administracyjnych, które ma zależność od innych pakietów administracyjnych, Pobierz skrypt [Usuń pakiet administracyjny z zależnościami](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) z Centrum skryptów w witrynie TechNet.  

1. Otwórz powłokę poleceń programu Operations Manager przy użyciu konta należącego do roli Administratorzy programu Operations Manager.
   
    > [!WARNING]
    > Upewnij się, nie masz żadnych niestandardowych pakietów administracyjnych z programu word Advisor lub IntelligencePack w nazwie przed kontynuowaniem, w przeciwnym razie następujące kroki usunąć je z grupą zarządzania.
    > 

2. W wierszu polecenia powłoki wpisz `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
3. Typ następnego `Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
4. Aby usunąć wszystkie pakiety administracyjne, które pozostały, który ma zależność od innych pakietów administracyjnych programu System Center Advisor, należy użyć skryptu *RecursiveRemove.ps1* wcześniej pobranego z Centrum skryptów w witrynie TechNet.  
 
    > [!NOTE]
    > Nie należy usuwać pakiety administracyjne programu Microsoft System Center Advisor lub programu Microsoft System Center Advisor wewnętrznego.  
    >  

5. Otwórz konsolę operacje programu Operations Manager przy użyciu konta należącego do roli Administratorzy programu Operations Manager.
6. W obszarze **administracji**, wybierz pozycję **pakietów administracyjnych** węzeł i w **Wyszukaj:** wpisz **Advisor** i sprawdź, czy w grupie zarządzania nadal zaimportowaniu pakietów administracyjnych:
   
   * Microsoft System Center Advisor
   * Microsoft System Center Advisor wewnętrzny
7. W portalu pakietu OMS kliknij kafelek **Ustawienia**.
8. Wybierz **połączone źródła**.
9. W tabeli w sekcji System Center Operations Manager powinna zostać wyświetlona nazwa grupy zarządzania, który chcesz usunąć z obszaru roboczego.  W kolumnie **dane o ostatniej**, kliknij przycisk **Usuń**.  
   
    > [!NOTE]
    > **Usuń** łącze nie będą dostępne dopiero po 14 dniach od Jeśli nic się nie wykryto z podłączonej grupy zarządzania.  
    > 

10. Zostanie wyświetlone okno z prośbą o potwierdzenie, czy chcesz kontynuować usuwanie.  Kliknij przycisk **tak** aby kontynuować. 

Aby usunąć dwa łączniki - Microsoft.SystemCenter.Advisor.DataConnector i łącznik usługi Advisor, Zapisz poniższy skrypt programu PowerShell na komputerze i wykonywanie za pomocą poniższych przykładów:

```
    .\OM2012_DeleteConnector.ps1 “Advisor Connector” <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 “Microsoft.SytemCenter.Advisor.DataConnector” <ManagementServerName>
```

> [!NOTE]
> Komputera, uruchom ten skrypt z, jeśli nie serwer zarządzania powinien mieć powłoki poleceń programu Operations Manager zainstalowane w zależności od wersji grupy zarządzania.
> 
> 

```
    param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

W przyszłości, jeśli planujesz ponowne nawiązywanie połączenia z grupy zarządzania do obszaru roboczego analizy dzienników, należy ponownie zaimportować `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` pliku pakietu administracyjnego.  W zależności od wersji programu System Center Operations Manager wdrożony w środowisku można znaleźć tego pliku w następującej lokalizacji:

* Na nośniku źródłowym, w obszarze `\ManagementPacks` folder dla programu System Center 2016 - Operations Manager lub nowszego.
* Od najnowszej zbiorczy stosowane do grupy zarządzania.  Dla programu Operations Manager 2012 jest folder źródłowy` %ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups` i 2012 R2 znajduje się w `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`.

## <a name="next-steps"></a>Kolejne kroki
Aby dodać funkcje i zbieranie danych, zobacz [rozwiązań dodać analizy dzienników z galerii rozwiązań](log-analytics-add-solutions.md).


