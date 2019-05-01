---
title: Rozwiązania Update Management na platformie Azure
description: Ten artykuł ma na celu pomóc Ci zrozumieć, jak używać rozwiązania Azure Update Management do zarządzania aktualizacjami komputerów Windows i Linux.
services: automation
ms.service: automation
ms.subservice: update-management
author: georgewallace
ms.author: gwallace
ms.date: 04/29/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b39d9788372fb0f682bc1e5b737542b400dd4035
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64919708"
---
# <a name="update-management-solution-in-azure"></a>Rozwiązania Update Management na platformie Azure

Rozwiązanie do zarządzania aktualizacjami w usłudze Azure Automation służy do zarządzania aktualizacjami systemu operacyjnego, komputerów Windows i Linux na platformie Azure, w środowiskach lokalnych lub w chmurze innych dostawców. Umożliwia ono szybką ocenę stanu dostępnych aktualizacji na wszystkich komputerach agentów oraz zarządzanie procesem instalacji wymaganych aktualizacji serwerów.

Zarządzanie aktualizacjami dla maszyn wirtualnych można włączyć bezpośrednio z konta usługi Azure Automation. Aby dowiedzieć się, jak włączanie rozwiązania Update Management dla maszyn wirtualnych na koncie usługi Automation, zobacz [zarządzanie aktualizacjami dla wielu maszyn wirtualnych](manage-update-multi.md). Można również włączyć rozwiązanie Update Management dla maszyny wirtualnej na stronie maszyny wirtualnej w witrynie Azure portal. Ten scenariusz jest dostępny dla [Linux](../virtual-machines/linux/tutorial-monitoring.md#enable-update-management) i [Windows](../virtual-machines/windows/tutorial-monitoring.md#enable-update-management) maszyn wirtualnych.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="solution-overview"></a>Omówienie rozwiązania

Komputery, które są zarządzane przez zarządzania aktualizacjami do oceny i wdrożenia aktualizacji, użyj następujących konfiguracji:

* Microsoft Monitoring Agent (MMA), Windows i Linux
* Platforma PowerShell Desired State Configuration (DSC) dla systemu Linux
* Hybrydowy proces roboczy elementu runbook usługi Automation
* Microsoft Update lub Windows Server Update Services (WSUS) dla komputerów Windows

Na poniższym diagramie przedstawiono koncepcyjny widok działania i przepływu danych przy użyciu jak oceniania i stosowania aktualizacji zabezpieczeń dla wszystkich połączonych systemu Windows Server i komputerów z systemem Linux w obszarze roboczym:

![Przepływ procesu zarządzania aktualizacjami](./media/automation-update-management/update-mgmt-updateworkflow.png)

Rozwiązanie Update Management może służyć do natywnie dołączanie maszyn w wielu subskrypcji w ramach tej samej dzierżawy.

CVE po wersji może potrwać 2 – 3 godziny poprawki pojawienie się maszyn z systemem Linux dla oceny.  W przypadku maszyn Windows zajmuje 12 – 15 godzin poprawki do wyświetlenia dla oceny po udostępnieniu.

Po ukończeniu skanowania pod kątem zgodności aktualizacji komputera agent przesyła dalej informacje zbiorcze, aby dzienniki usługi Azure Monitor. Na komputerze Windows skanowanie pod kątem zgodności jest domyślnie uruchamiane co 12 godzin.

Oprócz harmonogramem skanowania pod kątem zgodności aktualizacji jest inicjowane w ciągu 15 minut MMA uruchamiany ponownie, przed instalacją aktualizacji i po zainstalowaniu aktualizacji.

Komputera z systemem Linux skanowanie pod kątem zgodności jest domyślnie przeprowadzane co 3 godziny. Jeśli ponownego uruchomienia agenta MMA skanowania pod kątem zgodności jest inicjowane w ciągu 15 minut.

Rozwiązanie informuje, jak aktualne, komputer jest oparty na źródle jest skonfigurowany do synchronizacji z usługą. Jeśli komputer Windows jest skonfigurowany do raportu w programie WSUS, w zależności od tego, kiedy usługi WSUS od ostatniej synchronizacji upłynęła z usługą Microsoft Update, wyniki mogą różnić się od pokazuje Microsoft Updates. To zachowanie jest takie same dla komputerów z systemem Linux, które są skonfigurowane do raportu, aby zamiast z lokalnego repozytorium do repozytorium publicznego.

> [!NOTE]
> Aby poprawnie zgłosić do usługi, rozwiązanie Update Management wymaga określonych adresów URL i portów, aby włączyć. Aby dowiedzieć się więcej na temat tych wymagań, zobacz [sieci, planowanie hybrydowych procesów roboczych](automation-hybrid-runbook-worker.md#network-planning).

Aktualizacje oprogramowania można wdrożyć i zainstalować na komputerach, które ich wymagają, tworząc zaplanowane wdrożenie. Aktualizacje sklasyfikowane jako *opcjonalnie* nie są uwzględnione w zakresie wdrożenia dla komputerów Windows. Tylko wymagane aktualizacje są uwzględnione w zakresie wdrażania.

Zaplanowane wdrożenie definiuje, które komputery docelowe otrzymywać odpowiednie aktualizacje, jawnie określając komputerów lub wybierając [grupa](../azure-monitor/platform/computer-groups.md) opartego na dziennikach z konkretnego zestawu komputerów, lub [Azure zapytania](#azure-machines) wybiera dynamicznie maszyn wirtualnych platformy Azure, w oparciu o określone kryteria. Grupy te różnią się od [konfiguracji zakresu](../azure-monitor/insights/solution-targeting.md), która służy tylko do określenia, jakie maszyny pobrać pakiety administracyjne, które umożliwiają rozwiązania. 

Należy również określić harmonogram zatwierdzania i ustawić okres, w którym można instalować aktualizacje. Termin ten nosi nazwę okna obsługi. Dziesięć minut okna obsługi jest zarezerwowana do ponownego uruchamiania, jeśli wymagane jest ponowne uruchomienie i została wybrana opcja odpowiednie ponowny rozruch. Poprawianie trwa dłużej, niż oczekiwano, jeśli ma mniej niż dziesięć minut okna obsługi, nie nastąpi ponowne uruchomienie komputera.

Aktualizacje są instalowane przez elementy runbook w usłudze Azure Automation. Nie można wyświetlić tych elementów runbook i elementy runbook nie wymaga żadnej konfiguracji. Po utworzeniu wdrożenia aktualizacji wdrożenia aktualizacji powoduje utworzenie harmonogramu, który uruchamia główny element runbook aktualizacji w określonym czasie na uwzględnionych komputerach. Główny element runbook uruchamia podrzędny element runbook na każdym agencie w celu zainstalowania wymaganych aktualizacji.

Od daty i czasu określony we wdrożeniu aktualizacji komputery docelowe równolegle uruchomić wdrożenie. Przed rozpoczęciem instalacji Aby sprawdzić, czy aktualizacje są nadal wymagane przeprowadzania skanowania. Dla komputerów klienckich programu WSUS Jeśli aktualizacje nie są zatwierdzone w programie WSUS, wdrożenie aktualizacji kończy się niepowodzeniem.

Masz maszynę zarejestrowane do zarządzania aktualizacjami w więcej niż jeden obszarów roboczych usługi Log Analytics (Obsługa wielu regionów) nie jest obsługiwane.

## <a name="clients"></a>Klienci

### <a name="supported-client-types"></a>Typy obsługiwanych klientów

W poniższej tabeli przedstawiono listę obsługiwanych systemów operacyjnych:

|System operacyjny  |Uwagi  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Obsługuje tylko zaktualizować oceny.         |
|Windows Server 2008 R2 z dodatkiem SP1 i nowsze (łącznie z systemem Windows Server 2012 i 2016)    |.NET framework 4.5.1 lub nowszy jest wymagany. ([Pobierz program .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> Windows PowerShell 4.0 lub nowszy jest wymagany. ([Pobierz platformę WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))<br/> Programu Windows PowerShell 5.1 jest zalecane w celu zwiększenia niezawodności.  ([Pobierz platformę WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |
|CentOS 6 (x86/x64) i 7 (x64)      | Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji. Oparte na klasyfikacji poprawek wymaga "yum", aby zwrócić dane zabezpieczeń, które CentOS nie ma gotowych. Aby uzyskać więcej informacji na temat na podstawie klasyfikacji poprawek na CentOS, zobacz [Aktualizuj klasyfikacje, w systemie Linux](#linux-2)          |
|Red Hat Enterprise 6 (x86/x64) i 7 (x64)     | Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji.        |
|SUSE Linux Enterprise Server 11 (x86/x64) i 12 (x64)     | Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji.        |
|Ubuntu 14.04 LTS, 16.04 LTS i 18.04 — x86/x64 64      |Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji.         |

> [!NOTE]
> Zestawy skalowania maszyn wirtualnych platformy Azure można zarządzać przy użyciu rozwiązania Update Management. Rozwiązanie Update Management działa na wystąpieniach samodzielnie i obrazu podstawowego. Należy zaplanować aktualizacje w sposób przyrostowe, aby nie aktualizować wszystkie wystąpienia maszyn wirtualnych jednocześnie.

### <a name="unsupported-client-types"></a>Typy klientów nieobsługiwanych

W poniższej tabeli wymieniono systemy operacyjne, które nie są obsługiwane:

|System operacyjny  |Uwagi  |
|---------|---------|
|Klient systemu Windows     | Systemy operacyjne klienta (takich jak Windows 7 i Windows 10) nie są obsługiwane.        |
|Windows Server 2016 Nano Server     | Nieobsługiwane.       |

### <a name="client-requirements"></a>Wymagania dotyczące klienta

#### <a name="windows"></a>Windows

Agenci Windows musi być skonfigurowany do komunikowania się z serwerem usług WSUS lub muszą mieć dostęp do usługi Microsoft Update. Rozwiązanie Update Management można użyć w programie System Center Configuration Manager. Aby dowiedzieć się więcej na temat scenariuszy integracji, zobacz [integracji System Center Configuration Manager z zarządzaniem aktualizacjami](oms-solution-updatemgmt-sccmintegration.md#configuration). [Windows agent](../azure-monitor/platform/agent-windows.md) jest wymagana. Agent jest instalowany automatycznie, jeśli dodajesz maszynie wirtualnej platformy Azure.

#### <a name="linux"></a>Linux

Dla systemu Linux komputer musi mieć dostęp do repozytorium aktualizacji. Repozytorium aktualizacji może być prywatny lub publiczny. Protokół TLS 1.1 i TLS 1.2 jest wymagany do interakcji z rozwiązania Update Management. Log Analytics Agent dla systemu Linux, który jest skonfigurowany do raportowania do więcej niż jeden obszarów roboczych usługi Log Analytics nie jest obsługiwana za pomocą tego rozwiązania.

Aby uzyskać informacje o sposobach instalowania agenta usługi Log Analytics dla systemu Linux i Pobierz najnowszą wersję, zobacz [Log Analytics Agent dla systemu Linux](https://github.com/microsoft/oms-agent-for-linux). Aby uzyskać informacje o sposobie instalowania Log Analytics Agent for Windows, zobacz [programu Microsoft Monitoring Agent for Windows](../log-analytics/log-analytics-windows-agent.md).

## <a name="permissions"></a>Uprawnienia

Aby utworzyć i zarządzać wdrożeniami aktualizacji, musisz mieć uprawnienia określone. Aby dowiedzieć się więcej o tych uprawnieniach, zobacz [opartej na rolach dostępu — zarządzanie aktualizacjami](automation-role-based-access-control.md#update-management).

## <a name="solution-components"></a>Składniki rozwiązania

To rozwiązanie składa się z następującymi zasobami. Zasoby są dodawane do konta usługi Automation. Są one albo bezpośrednio połączonych agentów lub przynależnością do grupy zarządzania podłączonej do programu Operations Manager.

### <a name="hybrid-worker-groups"></a>Grupy hybrydowych procesów roboczych

Po włączeniu tego rozwiązania, dowolnym komputerze Windows, który jest bezpośrednio połączony z obszarem roboczym usługi Log Analytics jest automatycznie konfigurowany jako hybrydowy proces roboczy elementu Runbook do obsługi elementów runbook, które znajdują się w tym rozwiązaniu.

Każdy komputer Windows, który jest zarządzany przez to rozwiązanie znajduje się w **grupy hybrydowych procesów roboczych** okienko jako **grupy systemu hybrydowych procesów roboczych** dla konta usługi Automation. Rozwiązania używać konwencji nazewnictwa *nazwa_hosta FQDN_GUID*. Te grupy za pomocą elementów runbook nie może być przeznaczony na Twoim koncie. Mogą zakończyć się niepowodzeniem, jeśli zostanie podjęta. Te grupy są przeznaczone do obsługi tylko rozwiązania do zarządzania.

Komputery Windows można dodać do grupy hybrydowego procesu roboczego Runbook na Twoim koncie usługi Automation, do obsługi elementów runbook usługi Automation, jeśli używasz tego samego konta zarówno dla rozwiązania i hybrydowego procesu roboczego Runbook członkostwa w grupie. Ta funkcja została dodana w wersji 7.2.12024.0 hybrydowego procesu roboczego elementu Runbook.

### <a name="management-packs"></a>Pakiety administracyjne

Jeśli grupa zarządzania programu System Center Operations Manager jest połączona z obszarem roboczym usługi Log Analytics, następujące pakiety administracyjne są instalowane w programie Operations Manager. Te pakiety administracyjne są również instalowane na bezpośrednio połączonych komputerach Windows po dodaniu rozwiązania. Nie trzeba konfigurować ani niczym zarządzać tych pakietów administracyjnych.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Pakiet administracyjny wdrożenia aktualizacji

> [!NOTE]
> W przypadku grupy zarządzania programu Operations Manager 1807 z agentami, konfigurowane na poziomie grupy zarządzania, ma zostać skojarzony z obszarem roboczym, jest zastąpienie bieżącego obejście pozwalające uzyskać ich pojawienie się **IsAutoRegistrationEnabled** do **True** w **Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init** reguły.

Aby uzyskać więcej informacji na temat sposobu aktualizowania pakietów administracyjnych rozwiązania, zobacz [dzienniki łączenie programu Operations Manager do usługi Azure Monitor](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> Dla systemów z agenta programu Operations Manager aby można było w pełni zarządzane przez rozwiązania Update Management agent musi zostać zaktualizowany do programu Microsoft Monitoring Agent. Aby dowiedzieć się, jak zaktualizować agenta, zobacz [jak uaktualnić agenta programu Operations Manager](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents). Dla środowisk przy użyciu programu Operations Manager, wymagane jest, że używasz programu System Center Operations Manager 2012 R2 z pakietem zbiorczym aktualizacji 14 lub nowszej.

## <a name="onboard"></a>Włączanie rozwiązania Update Management

Aby rozpocząć, poprawiania systemów, należy włączyć rozwiązanie Update Management. Istnieje wiele sposobów dołączania maszyn do zarządzania aktualizacjami. Poniżej są zalecanym i obsługiwane sposoby na dołączenie rozwiązania:

* [Z maszyny wirtualnej](automation-onboard-solutions-from-vm.md)
* [Z wieloma maszynami przeglądania](automation-onboard-solutions-from-browse.md)
* [Na koncie usługi Automation](automation-onboard-solutions-from-automation-account.md)
* [Za pomocą elementu runbook usługi Azure Automation](automation-onboard-solutions.md)
  
### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Upewnij się, że maszyny spoza platformy Azure są dołączone

Aby upewnić się, że bezpośrednio połączone maszyny komunikują się przy użyciu dzienników usługi Azure Monitor po kilku minutach możesz uruchomić jedną następujących przeszukiwania dzienników.

#### <a name="linux"></a>Linux

```loganalytics
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```loganalytics
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Na komputerze Windows możesz przejrzeć następujące informacje, aby sprawdzić połączenie agenta przy użyciu dzienników usługi Azure Monitor:

1. W Panelu sterowania otwórz **Microsoft Monitoring Agent**. Na **usługi Azure Log Analytics** karcie agent wyświetla następujący komunikat: **Program Microsoft Monitoring Agent pomyślnie połączył się z usługą Log Analytics**.
2. Otwórz dziennik zdarzeń Windows. Przejdź do **Application and Services log\operations Manager** i wyszukaj Identyfikatory zdarzeń 3000 i 5002 identyfikator zdarzenia ze źródła **łącznika usługi**. Te zdarzenia wskazują, że komputer został zarejestrowany za pomocą obszaru roboczego usługi Log Analytics i odbiera konfigurację.

Jeśli agent nie może komunikować się z dzienników usługi Azure Monitor i agent jest skonfigurowany do komunikowania się z Internetem przez zaporę lub serwer proxy, upewnij się, Zapora lub serwer proxy ma prawidłową konfigurację. Aby dowiedzieć się, jak sprawdzić, serwer zapory lub serwera proxy jest prawidłowo skonfigurowany, zobacz [konfiguracji sieci dla agenta Windows](../azure-monitor/platform/agent-windows.md) lub [konfigurację sieci dla agenta systemu Linux](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Jeśli Twoje systemy Linux są skonfigurowane do komunikowania się z serwerem proxy lub brama usługi Log Analytics i dodajesz tego rozwiązania, aktualizacja *proxy.conf* uprawnień, aby przyznać grupie omiuser uprawnienie do odczytu w pliku przy użyciu następujące polecenia:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Nowo dodani agenci systemu Linux jest wyświetlany stan **zaktualizowano** po przeprowadzeniu oceny. Ten proces może potrwać do 6 godzin.

Aby upewnić się, że grupy zarządzania programu Operations Manager komunikuje się przy użyciu dzienników usługi Azure Monitor, zobacz [integracji sprawdzania poprawności z programu Operations Manager przy użyciu dzienników usługi Azure Monitor](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor).

## <a name="data-collection"></a>Zbieranie danych

### <a name="supported-agents"></a>Obsługiwani agenci

W poniższej tabeli opisano połączone źródła, które są obsługiwane przez to rozwiązanie:

| Połączone źródło | Obsługiwane | Opis |
| --- | --- | --- |
| Agenci dla systemu Windows |Yes |Rozwiązanie zbiera informacje o aktualizacjach systemu z agentów dla Windows i inicjuje instalowanie wymaganych aktualizacji. |
| Agenci dla systemu Linux |Yes |Rozwiązanie zbiera informacje o aktualizacjach systemu z agentów dla systemu Linux i inicjuje instalowanie wymaganych aktualizacji w obsługiwanych dystrybucjach. |
| Grupa zarządzania programu Operations Manager |Yes |Rozwiązanie zbiera informacje o aktualizacjach systemu z agentów w połączonej grupie zarządzania.<br/>Bezpośrednie połączenie agenta programu Operations Manager do dzienników usługi Azure Monitor nie jest wymagana. Dane są przekazywane z grupy zarządzania do obszaru roboczego usługi Log Analytics. |

### <a name="collection-frequency"></a>Częstotliwość zbierania

Skanowanie odbywa się dwa razy dziennie w przypadku każdego zarządzanego komputera z Windows. Co 15 minut wywoływany jest interfejs API Windows Aby wykonać zapytanie o czas ostatniej aktualizacji ustalić, czy stan się zmienił. Jeśli stan się zmienił, inicjowane jest skanowanie pod kątem zgodności.

Skanowanie jest przeprowadzane co 3 godziny, do każdego zarządzanego komputera z systemem Linux.

Może upłynąć od 30 minut do 6 godzin dla pulpitu nawigacyjnego wyświetlić zaktualizowane dane z zarządzanych komputerów.

Średnia użycie danych dzienników usługi Azure Monitor na maszynie za pomocą rozwiązania Update Management jest około 25MB miesięcznie. Ta wartość jest tylko przybliżeniem i może ulec zmianie w zależności od używanego środowiska. Zaleca się, że monitorować swoje środowisko, aby sprawdzić użycie dokładnie, czy masz.

## <a name="viewing-update-assessments"></a>Wyświetlanie ocen aktualizacji

Na koncie usługi Automation wybierz **rozwiązania Update Management** Aby wyświetlić stan maszyn.

Ten widok zawiera informacje dotyczące maszyn, brakujące aktualizacje, wdrożeń aktualizacji i zaplanowanych wdrożeń aktualizacji. W **kolumny zgodności**, możesz zobaczyć czas ostatniego oceniono maszyny. W **AKTUALIZUJ gotowość AGENTA** kolumny, można wyświetlić kondycję usługi Windows update agent. Jeśli występuje problem, wybierz link, aby przejść do rozwiązywania problemów z dokumentacją, które mogą ułatwić dowiesz się, jakie czynności wykonać, aby rozwiązać ten problem.

Aby uruchomić przeszukiwanie dziennika, które zwraca informacje dotyczące maszyny, aktualizacji lub wdrażania, wybierz element na liście. **Wyszukiwanie w dzienniku** okienko jest otwierane z zapytaniem dla wybranego elementu:

![Widok domyślny zarządzania aktualizacjami](media/automation-update-management/update-management-view.png)

## <a name="install-updates"></a>Zainstaluj aktualizacje

Po aktualizacji są oceniane dla wszystkich komputerów z systemami Linux i Windows w obszarze roboczym, można zainstalować wymagane aktualizacje, tworząc *wdrożenie aktualizacji*. Aby utworzyć wdrożenie aktualizacji, musi mieć dostęp do zapisu do konta usługi Automation i do zapisu w żadnych maszyn wirtualnych platformy Azure są przeznaczone we wdrożeniu. Wdrożenie aktualizacji to zaplanowana instalacja wymaganych aktualizacji na co najmniej jeden komputer. Należy określić datę i godzinę wdrożenia i komputera lub grupy komputerów, które do uwzględnienia w zakresie wdrożenia. Aby dowiedzieć się więcej na temat grup komputerów, zobacz [grup komputerów w dziennikach w usłudze Azure Monitor](../azure-monitor/platform/computer-groups.md).

Po włączeniu grupy komputerów we wdrożeniu aktualizacji członkostwa w grupie jest oceniane tylko raz, w momencie tworzenia harmonogramu. Kolejne zmiany do grupy nie są uwzględniane. Aby obejść to wykorzystania [grup dynamicznych](#using-dynamic-groups), te grupy są rozwiązywane w czasie wdrażania i są definiowane przez zapytanie dla maszyn wirtualnych platformy Azure lub zapisanej operacji wyszukiwania dla maszyn wirtualnych spoza platformy Azure.

> [!NOTE]
> Windows maszyn wirtualnych, które są wdrażane w portalu Azure Marketplace, domyślnie są ustawione na automatyczne otrzymywanie aktualizacji z usługi programu Windows Update. To zachowanie nie zmienia się po dodaniu tego rozwiązania lub Dodaj maszyny wirtualne Windows do obszaru roboczego. Jeśli nie aktywnie zarządzanie aktualizacjami za pomocą tego rozwiązania, stosuje się domyślne zachowanie (czyli automatyczne stosowanie aktualizacji).

Aby uniknąć stosowania aktualizacji poza oknem obsługi w systemie Ubuntu, zmień konfigurację pakietu Unattended-Upgrade tak, aby wyłączyć aktualizacje automatyczne. Aby uzyskać informacje o sposobie konfigurowania pakietu, zobacz [temat poświęcony aktualizacjom automatycznym w podręczniku systemu Ubuntu Server](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Maszyny wirtualne, które zostały utworzone z obrazów Red Hat Enterprise Linux (RHEL) na żądanie, które są dostępne w witrynie Azure Marketplace są rejestrowane na dostęp [Red Hat Update infrastruktury (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) wdrożonej na platformie Azure. Innych dystrybucji systemu Linux należy aktualizować przy użyciu repozytorium plików online dystrybucji wykonując obsługiwane metody dystrybucji.

Aby utworzyć nowe wdrożenie aktualizacji, wybierz **Zaplanuj wdrażanie aktualizacji**. **Nowe wdrożenie aktualizacji** zostanie otwarta strona. Wprowadź wartości dla właściwości opisane w poniższej tabeli, a następnie kliknij przycisk **Utwórz**:

| Właściwość | Opis |
| --- | --- |
| Name (Nazwa) |Unikatowa nazwa identyfikującą wdrożenie aktualizacji. |
|System operacyjny| System Linux lub Windows|
| Grupy aktualizacji |Dla maszyn należy zdefiniować zapytań, w zależności od kombinacji subskrypcji, grupy zasobów, lokalizacje i tagi, do tworzenia grupy dynamicznej maszyn wirtualnych platformy Azure, aby uwzględnić w danym wdrożeniu. </br></br>W przypadku komputerów spoza platformy Azure wybierz istniejącą zapisanego wyszukiwania, aby wybrać grupę maszyn spoza platformy Azure w celu uwzględnione we wdrożeniu. </br></br>Aby dowiedzieć się więcej, zobacz [Grupy dynamiczne](automation-update-management.md#using-dynamic-groups)|
| Maszyny do zaktualizowania |Wybierz zapisane wyszukiwanie bądź zaimportowaną grupę lub wybierz maszynę z listy rozwijanej, a następnie wybierz poszczególne maszyny. Jeśli wybierzesz pozycję **Maszyny**, gotowość maszyny będzie wyświetlana w kolumnie **AKTUALIZUJ GOTOWOŚĆ AGENTA**.</br> Aby dowiedzieć się więcej na temat różnych metod tworzenia grup komputerów w dziennikach usługi Azure Monitor, zobacz [Computer groups in Azure Monitor logs (Grupy komputerów w dziennikach usługi Azure Monitor)](../azure-monitor/platform/computer-groups.md) |
|Aktualizuj klasyfikacje|Wybierz wszystkie klasyfikacje aktualizacji, które są potrzebne|
|Uwzględnianie/wykluczanie aktualizacji|Spowoduje to otwarcie **uwzględniania/wykluczania** strony. Aktualizacje, które mają zostać uwzględnione lub wykluczone, znajdują się na osobnych kartach. Aby uzyskać więcej informacji na temat sposobu obsługi dołączania, zobacz [zachowanie dołączania](automation-update-management.md#inclusion-behavior) |
|Ustawienia harmonogramu|Wybierz godzinę do uruchomienia i wybrać jednorazowo lub cykliczne cyklu|
| Skrypty przed i skryptu używanego po utworzeniu|Wybierz skrypty do uruchomienia przed i po wdrożeniu|
| Okno obsługi |Liczba minut dla aktualizacji. Wartość nie może być mniejsza niż 30 minut, a nie więcej niż 6 godzin |
| Ponowne uruchomienie kontroli| Określa sposób obsługi jest uruchamiany ponownie. Dostępne opcje:</br>Ponowne uruchomienie, jeśli jest to wymagane (ustawienie domyślne)</br>Zawsze uruchamiaj ponownie</br>Nigdy nie uruchamiaj ponownie</br>Tylko ponowne uruchomienie — aktualizacje nie zostaną zainstalowane|

Można także programowo tworzyć wdrożenia aktualizacji. Aby dowiedzieć się, jak utworzyć wdrożenie aktualizacji za pomocą interfejsu API REST, zobacz [konfiguracji aktualizacji oprogramowania — tworzenie](/rest/api/automation/softwareupdateconfigurations/create). Istnieje również przykładowy element runbook, który może służyć do tworzenia tygodniowy wdrożenia aktualizacji. Aby dowiedzieć się więcej na temat tego elementu runbook, zobacz [utworzyć tygodniowy wdrożenie aktualizacji dla jednego lub więcej maszyn wirtualnych w grupie zasobów](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

### <a name="multi-tenant"></a>Wdrożenia aktualizacji międzydzierżawowe

Jeśli masz maszyny w innej dzierżawie platformy Azure, raportowanie do zarządzania aktualizacjami, który chcesz zastosować poprawki należy użyć następującego obejścia można pobrać je według harmonogramu. Możesz użyć [polecenia New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) polecenia cmdlet z przełącznikiem `-ForUpdate` Tworzenie harmonogramu i używanie [New AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) polecenia cmdlet i przekaż maszyny w innej dzierżawie do `-NonAzureComputer` parametru. Poniższy przykład przedstawia przykład, jak to zrobić:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="view-missing-updates"></a>Wyświetl brakujące aktualizacje

Wybierz **brakujące aktualizacje** Aby wyświetlić listę aktualizacji, które są nieobecne maszyny. Każda aktualizacja znajduje się na liście i można go wybrać. Wyświetlane są informacje o liczbie maszyn, które wymagają aktualizacji, system operacyjny i łącze, aby uzyskać więcej informacji. **Wyszukiwanie w dzienniku** okienko zawiera więcej informacji na temat aktualizacji.

## <a name="view-update-deployments"></a>Wyświetlanie wdrożeń aktualizacji

Wybierz **wdrożenia aktualizacji** kartę, aby wyświetlić listę istniejących wdrożeń aktualizacji. Wybierz jedno z wdrożeń aktualizacji w tabeli, aby otworzyć **aktualizowanie uruchomienia wdrażania** okienko dla tego wdrożenia aktualizacji.

![Przegląd wyników wdrożenia aktualizacji](./media/automation-update-management/update-deployment-run.png)

Aby wyświetlić wdrożenie aktualizacji z interfejsu API REST, zobacz [przebiegów konfiguracji aktualizacji oprogramowania](/rest/api/automation/softwareupdateconfigurationruns).

## <a name="update-classifications"></a>Aktualizuj klasyfikacje

W poniższej tabeli wymieniono klasyfikacji aktualizacji w zarządzania aktualizacjami definicji dla każdej klasyfikacji.

### <a name="windows"></a>Windows

|Klasyfikacja  |Opis  |
|---------|---------|
|Aktualizacje krytyczne     | Aktualizacji dotyczącej konkretnego problemu, odnoszący się do krytyczną usterkę niepowiązaną z zabezpieczeniami.        |
|Aktualizacje zabezpieczeń     | Aktualizacji dotyczącej problemu z konkretnym produktem, powiązanego z zabezpieczeniami.        |
|Pakiety zbiorcze aktualizacji     | Zbiorczego zestawu poprawek zebranych w jednym pakiecie w celu łatwiejszego wdrażania.        |
|Pakiety funkcji     | Nowych funkcji produktów, rozpowszechnianych poza wydaniami.        |
|Dodatki Service Pack     | Zbiorczego zestawu poprawek stosujących się do aplikacji.        |
|Aktualizacje definicji     | Aktualizacji definicji wirusów lub innych plików definicji.        |
|Narzędzia     | Narzędzia lub funkcji pomagających ukończone co najmniej jedno zadanie.        |
|Aktualizacje     | Aktualizacja aplikacji lub pliku, który aktualnie jest zainstalowana.        |

### <a name="linux"></a>Linux

|Klasyfikacja  |Opis  |
|---------|---------|
|Aktualizacje krytyczne i zabezpieczeń     | Aktualizacje dla konkretnego problemu lub problem z konkretnym produktem, powiązanego z zabezpieczeniami.         |
|Inne aktualizacje     | Wszystkie inne aktualizacje, które nie są szczególnie ważne w charakterze lub które nie są aktualizacje zabezpieczeń.        |

Dla systemu Linux, rozwiązania Update Management można rozróżnić krytycznych i aktualizacji zabezpieczeń w chmurze podczas wyświetlania danych oceny ze względu na Wzbogacanie danych w chmurze. Zastosowanie poprawek, zarządzanie aktualizacjami opiera się na klasyfikacji danych na maszynie. W przeciwieństwie do innych dystrybucji CentOS nie ma dostępnych informacji gotowych. Jeśli masz maszyny CentOS konfigurowane w taki sposób, aby zwrócić dane zabezpieczeń dla następującego polecenia, rozwiązanie Update Management będzie zastosowania poprawki oparte na klasyfikacji.

```bash
sudo yum -q --security check-update
```

Nie jest obecnie żadna metoda metody obsługiwanej umożliwiające dostępność danych natywnych klasyfikacji na CentOS. W tej chwili jedynie optymalnych obsługa jest udostępniana klientom, którzy mogą mieć włączone to własnych.

## <a name="firstparty-predownload"></a>Ustawienia zaawansowane

Rozwiązanie Update Management opiera się na Windows Update, aby pobrać i zainstalować aktualizacje Windows. W rezultacie Szanujemy wiele ustawień używanych przez usługę Windows Update. Jeśli używasz ustawienia włączającą aktualizacje inne niż Windows rozwiązania Update Management będzie zarządzać te aktualizacje również. Jeśli chcesz umożliwić pobieranie aktualizacji, zanim nastąpi wdrożenie aktualizacji, wdrożeń aktualizacji można przyspieszyć i jest mniej prawdopodobne przekracza okna obsługi.

### <a name="pre-download-updates"></a>Wstępnie pobierać aktualizacje

Aby skonfigurować automatyczne pobieranie aktualizacji zasad grupy, można ustawić [Konfigurowanie aktualizacji automatycznych](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#BKMK_comp5) do **3**. Pobiera aktualizacje wymagane w tle, ale nie można zainstalować je. Dzięki temu zarządzanie aktualizacjami w formancie harmonogramów, ale zezwala na aktualizacje do pobrania poza oknem obsługi rozwiązania Update Management. Może to spowodować **Przekroczono czas okna obsługi** błędy w zarządzania aktualizacjami.

Możesz również ustawić za pomocą programu PowerShell, uruchom następujące polecenie programu PowerShell w systemie, który chcesz automatycznego pobierania aktualizacji.

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

### <a name="disable-automatic-installation"></a>Wyłączenie automatycznej instalacji

Maszyny wirtualne platformy Azure mają automatycznej instalacji aktualizacji, domyślnie włączone. Może to spowodować, że aktualizacje do zainstalowania przed możesz zaplanować do zainstalowania przez rozwiązanie Update Management. To zachowanie można wyłączyć, ustawiając `NoAutoUpdate` klucza rejestru w celu `1`. Poniższy fragment kodu programu PowerShell pokazuje, jak to zrobić.

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

### <a name="enable-updates-for-other-microsoft-products"></a>Włącz aktualizacje dla innych produktów firmy Microsoft

Domyślnie Windows Update udostępnia aktualizacje tylko dla Windows. Po włączeniu **nadać mi aktualizacje dla innych produktów firmy Microsoft, gdy aktualizuję Windows**, otrzymasz aktualizacje dla innych produktów, w tym poprawki zabezpieczeń dla programu SQL Server lub inne pierwszy oprogramowanie innych firm. Tej opcji nie można skonfigurować przy użyciu zasad grupy. Uruchom następujące polecenie programu PowerShell w systemach, które chcesz włączyć innych pierwszy poprawek innych firm na i zarządzania aktualizacjami będą stosować tego ustawienia.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="third-party"></a> Poprawkami innych firm dla Windows

Rozwiązanie Update Management opiera się na repozytorium aktualizacji lokalnie skonfigurowanych do poprawiania obsługiwanych systemów Windows. Jest to WSUS lub Windows Update. Narzędzia takie jak [System Center Updates Publisher](/sccm/sum/tools/updates-publisher
) (Updates Publisher) pozwalają na publikowanie niestandardowych aktualizacji do programu WSUS. Ten scenariusz umożliwia zarządzania aktualizacjami maszyn poprawki, które używają programu System Center Configuration Manager jako repozytorium aktualizacji, ich za pomocą oprogramowania innych firm. Aby dowiedzieć się, jak skonfigurować program Updates Publisher, zobacz [zainstalować Updates Publisher](/sccm/sum/tools/install-updates-publisher).

## <a name="ports"></a>Planowanie sieci

Następujące adresy są wymagane dla rozwiązania Update Management. Na te adresy są przesyłane za pośrednictwem portu 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

Aby uzyskać więcej informacji na temat porty wymagane przez hybrydowy proces roboczy elementu Runbook, zobacz [porty roli hybrydowego procesu roboczego](automation-hybrid-runbook-worker.md#hybrid-worker-role).

Zaleca się używania adresów wymienionych podczas definiowania wyjątków. Adresy IP, możesz pobrać [zakresów IP centrum danych Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Ten plik jest aktualizowana co tydzień i odzwierciedla aktualnie wdrożone zakresy i wszystkie nadchodzące zmiany w zakresach adresów IP.

## <a name="search-logs"></a>Przeszukiwanie dzienników

Oprócz szczegółów, które znajdują się w witrynie Azure portal można wykonać wyszukiwania w dziennikach. Na stronach rozwiązania wybierz **usługi Log Analytics**. **Wyszukiwanie w dzienniku** zostanie otwarte okienko.

Możesz także dowiedzieć się, jak dostosować zapytania lub używać ich z różnych klientów i więcej, odwiedzając:  [Dokumentacja interfejsu API wyszukiwania usługi log Analytics](
https://dev.loganalytics.io/).

### <a name="sample-queries"></a>Przykładowe zapytania

Poniższe sekcje zawierają przykładowe dziennika zapytania dotyczące rekordów aktualizacji, które są zbierane przez to rozwiązanie:

#### <a name="single-azure-vm-assessment-queries-windows"></a>Pojedynczego zapytania oceny maszyn wirtualnych platformy Azure (Windows)

Zastąp wartość symbolu VMUUID o identyfikatorze GUID maszyny Wirtualnej z maszyny wirtualnej, której dotyczy kwerenda. Można znaleźć VMUUID, które mają być używane, uruchamiając następujące zapytanie w dziennikach w usłudze Azure Monitor: `Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Brakujące aktualizacje podsumowania

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security"
```

##### <a name="missing-updates-list"></a>Brak listy aktualizacji

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

#### <a name="single-azure-vm-assessment-queries-linux"></a>Pojedynczego zapytania oceny maszyny Wirtualnej platformy Azure (Linux)

Dla niektórych dystrybucje systemu Linux jest [kolejność bajtów](https://en.wikipedia.org/wiki/Endianness) niezgodność z wartością VMUUID, który pochodzi z usługi Azure Resource Manager i co to jest przechowywany w dziennikach w usłudze Azure Monitor. Następujące zapytanie wyszukuje dopasowania w obu kolejność bajtów. Zastąp wartości VMUUID big-endian i little-endian format identyfikatora GUID prawidłowo zwracania wyników. Można znaleźć VMUUID, które mają być używane, uruchamiając następujące zapytanie w dziennikach w usłudze Azure Monitor: `Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Brakujące aktualizacje podsumowania

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="missing-updates-list"></a>Brak listy aktualizacji

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

#### <a name="multi-vm-assessment-queries"></a>Zapytania oceny wielu maszyn wirtualnych

##### <a name="computers-summary"></a>Podsumowanie komputerów

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount


```

##### <a name="missing-updates-summary"></a>Brakujące aktualizacje podsumowania

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security"
```

##### <a name="computers-list"></a>Lista komputerów

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

##### <a name="missing-updates-list"></a>Brak listy aktualizacji

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="using-dynamic-groups"></a>Za pomocą grup dynamicznych

Rozwiązanie Update Management umożliwia dynamiczne grupy platformy Azure lub maszyn wirtualnych platformy Azure na inne niż w przypadku wdrożeń aktualizacji. Te grupy są oceniane w czasie wdrażania, dzięki czemu nie trzeba edytować wdrożenia w celu dodania maszyn.

> [!NOTE]
> Podczas tworzenia wdrożenia aktualizacji, musi mieć odpowiednie uprawnienia. Aby dowiedzieć się więcej, zobacz [Zainstaluj aktualizacje](#install-updates).

### <a name="azure-machines"></a>Maszyny platformy Azure

Te grupy są definiowane przez kwerendę, gdy rozpocznie się wdrożenie aktualizacji, Członkowie tej grupy są oceniane. Grupy dynamiczne nie działają z klasycznych maszyn wirtualnych. Podczas definiowania kwerendy, następujące elementy można ze sobą do wypełniania grupy dynamicznej

* Subskrypcja
* Grupy zasobów
* Lokalizacje
* Tagi

![Wybieranie grup](./media/automation-update-management/select-groups.png)

Aby wyświetlić podgląd wyników grupę dynamiczną, kliknij przycisk **Podgląd** przycisku. Tej wersji zapoznawczej pokazuje członkostwa w grupie w tym czasie, w tym przykładzie Trwa wyszukiwanie maszyn ze znacznikiem **roli** jest równa **BackendServer**. Jeśli jedna maszyna mają ten tag dodany, będzie można dodać do wszystkich przyszłych wdrożeń względem tej grupy.

![grupy (wersja zapoznawcza)](./media/automation-update-management/preview-groups.png)

### <a name="non-azure-machines"></a>Maszyny spoza platformy Azure

Na platformie Azure bez maszyn, zapisane wyszukiwania również określane jako grup komputerów są używane do tworzenia grupy dynamicznej. Aby dowiedzieć się, jak utworzyć zapisanego kryterium wyszukiwania, zobacz [utworzyć grupę komputerów](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Po utworzeniu grupy możesz wybrać go z listy zapisanych wyszukiwań. Kliknij przycisk **Podgląd** nad wersją zapoznawczą komputerów w zapisanego wyszukiwania, w tym czasie.

![Wybieranie grup](./media/automation-update-management/select-groups-2.png)

## <a name="integrate-with-system-center-configuration-manager"></a>Integracja z programem System Center Configuration Manager

Klienci, którzy zainwestowali w programie System Center Configuration Manager do zarządzania komputerami, serwerami i urządzeniami przenośnymi polegają również na sile i dojrzałości programu Configuration Manager, aby ułatwić zarządzanie aktualizacjami oprogramowania. Configuration Manager jest częścią cyklu zarządzania (suma) aktualizacji oprogramowania.

Aby dowiedzieć się, jak zintegrować rozwiązanie do zarządzania z System Center Configuration Manager, zobacz [integracji System Center Configuration Manager z zarządzaniem aktualizacjami](oms-solution-updatemgmt-sccmintegration.md).

## <a name="inclusion-behavior"></a>Zachowanie dołączania

Włączenie aktualizacji można określić określonej aktualizacji do zastosowania. Poprawki lub pakietów, które są uwzględniane są zainstalowane. Po wybraniu klasyfikacji również i uwzględniono poprawki lub pakietów, dołączone elementy i elementy, które spełniają klasyfikacji są instalowane.

Jest ważne dowiedzieć się, że wykluczenia zastąpienia dołączenia. Na przykład jeśli zdefiniujesz regułę wykluczania z `*`, a następnie żadnych poprawek ani pakiety są zainstalowane, ponieważ są one wszystkie wyłączone. Wykluczone poprawki nadal wyświetlane jako brakujące na tej maszynie. Dla maszyn z systemem Linux Jeśli pakiet jest dołączony, ale ma z pakietu zależnego, który został wykluczony, pakiet nie jest zainstalowany.

## <a name="patch-linux-machines"></a>Maszyny z systemem Linux poprawki

W poniższych sekcjach opisano potencjalne problemy z poprawek w systemie Linux.

### <a name="unexpected-os-level-upgrades"></a>Nieoczekiwany uaktualnień na poziomie systemu operacyjnego

W niektórych wariantów systemu Linux, takie jak Red Hat Enterprise Linux uaktualnień na poziomie systemu operacyjnego mogą wystąpić za pośrednictwem pakietów. Może to prowadzić do uruchomienia rozwiązania Update Management gdzie zmienia numer wersji systemu operacyjnego. Ponieważ zarządzania aktualizacjami używa tych samych metod, aby zaktualizować pakiety, które administrator może używać go lokalnie na komputerze z systemem Linux, to zachowanie jest celowe.

Aby uniknąć, aktualizowanie wersji systemu operacyjnego przy użyciu rozwiązania Update Management działa, należy użyć **wykluczeń** funkcji.

W systemie Red Hat Enterprise Linux nazwy pakietu, które mają zostać wykluczone jest redhat-release-server.x86_64.

![Pakietów do wykluczenia dla systemu Linux](./media/automation-update-management/linuxpatches.png)

### <a name="critical--security-patches-arent-applied"></a>Krytyczne / poprawek zabezpieczeń nie są stosowane.

Wdrażając aktualizacje na maszynę z systemem Linux, możesz wybrać klasyfikacje aktualizacji. Filtruje aktualizacji, które są stosowane do maszyny, które spełniają określone kryteria. Ten filtr jest stosowany lokalnie na komputerze po wdrożeniu aktualizacji.

Bo zarządzania aktualizacjami wzbogacania aktualizacji w chmurze, niektóre aktualizacje mogą oflagowane w zarządzania aktualizacjami jako mające wpływ na bezpieczeństwo, nawet jeśli komputer lokalny nie ma tych informacji. W rezultacie zastosowanie aktualizacji krytycznych na maszynę z systemem Linux, mogą istnieć aktualizacje, które nie są oznaczone jako mające wpływ na zabezpieczenia na maszynie i aktualizacje nie są stosowane.

Jednak zarządzania aktualizacjami, nadal może raportować tej maszyny, co jest niezgodne, ponieważ ma ona dodatkowe informacje o odpowiednich aktualizacji.

Wdrażanie aktualizacji według klasyfikacji aktualizacji nie działa w przypadku CentOS gotowe. Aby prawidłowo wdrożyć aktualizacje, centos, wybierz wszystkie klasyfikacje, aby upewnić się, że aktualizacje są stosowane. Dla SUSE wybierając *tylko* inne aktualizacje klasyfikacji może spowodować pewne zabezpieczenia aktualizuje również zainstalowania aktualizacji zabezpieczeń związane z zypper (Menedżera pakietów) oraz jego zależności wymagane najpierw. To zachowanie jest ograniczenie zypper. W niektórych przypadkach może wymagać ponownego uruchomienia wdrożenia aktualizacji. Aby sprawdzić, przejrzyj dziennik aktualizacji.

## <a name="remove-a-vm-for-update-management"></a>Usuwanie maszyny Wirtualnej do zarządzania aktualizacjami

Aby usunąć Maszynę wirtualną z rozwiązania Update Management:

* W obszarze roboczym usługi Log Analytics, należy usunąć maszynę Wirtualną z zapisanego wyszukiwania dla konfiguracji zakresu `MicrosoftDefaultScopeConfig-Updates`. Zapisane wyszukiwania można znaleźć w obszarze **ogólne** w obszarze roboczym.
* Usuń [Microsoft Monitoring agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) lub [agenta usługi Log Analytics dla systemu Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Kolejne kroki

Przejdź do samouczka na temat sposobu zarządzania aktualizacjami dla maszyn wirtualnych Windows.

> [!div class="nextstepaction"]
> [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych Windows Azure](automation-tutorial-update-management.md)

* Użyj wyszukiwania w dzienniku [dzienniki usługi Azure Monitor](../log-analytics/log-analytics-log-searches.md) do wyświetlania szczegółowych danych aktualizacji.
* [Tworzenie alertów](automation-tutorial-update-management.md#configure-alerts) aktualizacji stanu wdrożenia.

* Aby dowiedzieć się, jak korzystać z rozwiązania Update Management za pośrednictwem interfejsu API REST, zobacz [konfiguracji aktualizacji oprogramowania](/rest/api/automation/softwareupdateconfigurations)
* Aby dowiedzieć się, jak rozwiązywać problemy z Twojego rozwiązania Update Management, zobacz [Rozwiązywanie problemów z rozwiązania Update Management](troubleshoot/update-management.md)
