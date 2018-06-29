---
title: Aktualizacja rozwiązania do zarządzania na platformie Azure
description: Ten artykuł dotyczy ułatwiające zrozumienie, jak używać rozwiązania zarządzania aktualizacjami Azure do zarządzania aktualizacjami dla komputerów z systemem Windows i Linux.
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 06/28/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3de93c06285f36353d91a66db975c0a579c1379c
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097448"
---
# <a name="update-management-solution-in-azure"></a>Aktualizacja rozwiązania do zarządzania na platformie Azure

Rozwiązanie do zarządzania aktualizacjami w automatyzacji Azure służy do zarządzania aktualizacjami systemu operacyjnego dla komputerów z systemem Windows i Linux wdrożonych na platformie Azure, w środowiskach lokalnych lub w innych dostawców chmury. Umożliwia ono szybką ocenę stanu dostępnych aktualizacji na wszystkich komputerach agentów oraz zarządzanie procesem instalacji wymaganych aktualizacji serwerów.

Zarządzanie aktualizacjami dla maszyn wirtualnych można włączyć bezpośrednio z Twojego konta usługi Automatyzacja Azure. Informacje na temat włączania zarządzania aktualizacjami dla maszyn wirtualnych z Twojego konta automatyzacji, zobacz [zarządzania aktualizacjami dla wielu maszyn wirtualnych](manage-update-multi.md). Można również włączyć zarządzanie aktualizacji dla jednej maszyny wirtualnej z okienka maszyny wirtualnej w portalu Azure. Ten scenariusz jest dostępna dla [Linux](../virtual-machines/linux/tutorial-monitoring.md#enable-update-management) i [Windows](../virtual-machines/windows/tutorial-monitoring.md#enable-update-management) maszyn wirtualnych.

## <a name="solution-overview"></a>Omówienie rozwiązania

Komputery, które są zarządzane przez zaktualizować zarządzania przeprowadzenie oceny i wdrożenia aktualizacji, użyj następujących konfiguracji:

* Microsoft Monitoring Agent (MMA) dla systemu Windows lub Linux
* Platforma PowerShell Desired State Configuration (DSC) dla systemu Linux
* Hybrydowy proces roboczy elementu runbook usługi Automation
* Usługa Microsoft Update lub Windows Server Update Services (WSUS) dla komputerów z systemem Windows

Na poniższym diagramie przedstawiono koncepcję zachowanie, a przepływ danych jak rozwiązania ocenia i dotyczy wszystkich aktualizacji systemu Windows Server i komputery z systemem Linux w obszarze roboczym:

![Aktualizowanie przepływu procesów zarządzania](media/automation-update-management/update-mgmt-updateworkflow.png)

Po komputerze wykonuje skanowanie zgodności aktualizacji, agent przekazuje informacje zbiorcze Analiza dzienników Azure. Na komputerze z systemem Windows domyślnie skanowania zgodności jest wykonywane co 12 godzin.

Oprócz harmonogram skanowania w poszukiwaniu skanowanie pod kątem zgodności aktualizacji jest inicjowana w ciągu 15 minut po uruchomieniu MMA przed instalacją aktualizacji i po zainstalowaniu aktualizacji.

Dla komputera z systemem Linux domyślnie skanowania zgodności jest wykonywane co 3 godziny. Po uruchomieniu MMA agent skanowania zgodności jest inicjowana w ciągu 15 minut.

Rozwiązanie jak aktualny komputer opiera się na co źródło raportów są skonfigurowane do synchronizacji z usługą. Jeśli komputer z systemem Windows jest skonfigurowany do raportowania w programie WSUS, w zależności od tego, kiedy usługi WSUS od ostatniej synchronizacji upłynęła z usługą Microsoft Update, wyniki mogą różnić się od pokazuje Microsoft Updates. Jest to ten sam dla komputerów z systemem Linux, które są skonfigurowane do raportu do lokalnego repozytorium zamiast do publicznego repozytorium.

> [!NOTE]
> Aby zgłosić poprawnie z usługą, zarządzanie aktualizacjami wymaga niektórych adresów URL i portów, aby włączyć. Aby dowiedzieć się więcej na temat tych wymagań, zobacz [sieci, planowanie hybrydowych procesów roboczych](automation-hybrid-runbook-worker.md#network-planning).

Aktualizacje oprogramowania można wdrożyć i zainstalować na komputerach, które ich wymagają, tworząc zaplanowane wdrożenie. Aktualizacje sklasyfikowane jako *opcjonalnie* nie są uwzględniane w zakresie wdrażania dla komputerów z systemem Windows. Tylko wymagane aktualizacje znajdują się w zakresie wdrażania. 

Zaplanowane wdrożenie definiuje, jakie komputery docelowe otrzymywać odpowiednie aktualizacje, jawnie określając komputerów lub wybierając [grupy komputerów](../log-analytics/log-analytics-computer-groups.md) opartego na dziennik wyszukiwania z określonego zestawu komputerów. Należy także określić harmonogram w celu zatwierdzenia i wyznaczyć okres czasu, w którym można zainstalować aktualizacji. 

Aktualizacje są instalowane przez elementy runbook w usłudze Azure Automation. Nie można wyświetlić te elementy runbook i elementy runbook nie wymaga żadnej konfiguracji. Po utworzeniu wdrożenia aktualizacji wdrożenia aktualizacji tworzy harmonogram, który uruchamia element runbook aktualizacji głównego o podanej godzinie na komputerach dołączone. Główny element runbook uruchamia podrzędnego elementu runbook na każdego agenta do przeprowadzenia instalacji wymaganych aktualizacji.

Na datę i godzinę, określonym we wdrożeniu aktualizacji komputerów docelowych, należy wykonać wdrożenie równoległe. Przed rozpoczęciem instalacji skanowanie jest przeprowadzane, aby sprawdzić, czy aktualizacje są nadal wymagane. Dla komputerów klienckich WSUS Jeśli aktualizacje nie są zatwierdzane w programie WSUS, wdrożenie aktualizacji kończy się niepowodzeniem.

## <a name="clients"></a>Klienci

### <a name="supported-client-types"></a>Typy obsługiwanych klientów

W poniższej tabeli przedstawiono listę obsługiwanych systemów operacyjnych:

|System operacyjny  |Uwagi  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Obsługuje tylko zaktualizować oceny.         |
|Windows Server 2008 R2 z dodatkiem SP1 lub nowszy     |.NET framework 4.5 lub nowszy jest wymagany. ([.NET Framework pobrać](/dotnet/framework/install/guide-for-developers))<br/> Środowisko Windows PowerShell w wersji 4.0 lub nowszy jest wymagany. ([Pobierz WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))<br/> Windows PowerShell w wersji 5.1 zaleca się zwiększenie niezawodności.  ([Pobierz WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |
|CentOS 6 (x86/x64) i 7 (x64)      | Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji. Poprawki na podstawie klasyfikacji wymaga "yum", aby zwrócić dane zabezpieczeń, która CentOS nie ma się poza pole.         |
|Red Hat Enterprise 6 (x86/x64) i 7 (x64)     | Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji.        |
|SUSE Linux Enterprise Server 11 (x86/x64) i 12 (x64)     | Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji.        |
|Ubuntu 14.04 LTS i 16.04 LTS (x86/x64)      |Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji.         |

### <a name="unsupported-client-types"></a>Typy klientów nieobsługiwana

W poniższej tabeli wymieniono systemy operacyjne, które nie są obsługiwane:

|System operacyjny  |Uwagi  |
|---------|---------|
|Klient systemu Windows     | Klienckie systemy operacyjne (na przykład systemu Windows 7 i Windows 10) nie są obsługiwane.        |
|Windows Server 2016 Nano Server     | Nieobsługiwane.       |

### <a name="client-requirements"></a>Wymagania dotyczące klienta

#### <a name="windows"></a>Windows

Agentów systemu Windows musi być skonfigurowana do komunikowania się z serwerem WSUS lub muszą mieć dostęp do usługi Microsoft Update. Można użyć zarządzania aktualizacjami w programie System Center Configuration Manager. Aby dowiedzieć się więcej na temat scenariuszy integracji, zobacz [integracji System Center Configuration Manager z zarządzania aktualizacjami](oms-solution-updatemgmt-sccmintegration.md#configuration). [Agenta Windows](../log-analytics/log-analytics-agent-windows.md) jest wymagana. Agent jest instalowany automatycznie w przypadku przechodzenia do maszyny wirtualnej platformy Azure.

#### <a name="linux"></a>Linux

Dla systemu Linux komputer musi mieć dostęp do repozytorium aktualizacji. Repozytorium aktualizacji może być prywatny lub publiczny. Protokołu TLS 1.1 i TLS 1.2 jest wymagany do interakcji z zarządzania aktualizacjami. Agenta Operations Management Suite (OMS) dla systemu Linux, który jest skonfigurowany pod kątem raportowania do wielu obszarów roboczych usługi Analiza dzienników nie jest obsługiwany w tym rozwiązaniu.

Aby uzyskać informacje o sposobach instalowania agenta pakietu OMS dla systemu Linux i pobrać najnowszą wersję, zobacz [Operations Management Suite agenta dla systemu Linux](https://github.com/microsoft/oms-agent-for-linux). Aby uzyskać informacje o sposobie instalowania OMS agenta dla systemu Windows, temacie [Operations Management Suite agenta dla systemu Windows](../log-analytics/log-analytics-windows-agent.md).

## <a name="permissions"></a>Uprawnienia

Aby utworzyć i zarządzać wdrożeniami aktualizacji, musisz mieć uprawnienia określone. Aby dowiedzieć się więcej o tych uprawnieniach, zobacz [dostępu opartej na rolach — zarządzanie aktualizacjami](automation-role-based-access-control.md#update-management).

## <a name="solution-components"></a>Składniki rozwiązania

Rozwiązania składa się z następujących zasobów. Zasoby zostaną dodane do Twojego konta automatyzacji. Są one albo bezpośrednio połączone agentów lub w grupie zarządzania połączenia programu Operations Manager.

### <a name="hybrid-worker-groups"></a>Grupy hybrydowych procesów roboczych

Po włączeniu tego rozwiązania dowolnego komputera z systemem Windows, podłączonej bezpośrednio do swojego obszaru roboczego analizy dzienników jest automatycznie konfigurowany jako hybrydowy proces roboczy elementu Runbook do obsługi elementów runbook, które znajdują się w tym rozwiązaniu.

Każdy komputer z systemem Windows jest zarządzane przez rozwiązanie znajduje się w **hybrydowego procesu roboczego grupy** okienko jako **grupy systemu hybrydowych procesów roboczych** dla konta automatyzacji. Rozwiązania zastosować konwencję nazewnictwa *Hostname FQDN_GUID*. Te grupy z elementami runbook nie może być przeznaczony na Twoim koncie. Się one zakończyć niepowodzeniem, Jeśli spróbujesz. Te grupy są przeznaczone do obsługi tylko rozwiązania do zarządzania.

Komputery z systemem Windows można dodać do grupy hybrydowego procesu roboczego elementu Runbook do Twojego konta automatyzacji do obsługi elementu runbook usługi Automatyzacja, korzystając z tego samego konta dla rozwiązania i członkostwo w grupie hybrydowy proces roboczy elementu Runbook. Ta funkcja została dodana w wersji 7.2.12024.0 hybrydowy proces roboczy elementu Runbook.

### <a name="management-packs"></a>Pakiety administracyjne

Jeśli grupa zarządzania programu System Center Operations Manager jest podłączony do obszaru roboczego analizy dzienników, następujące pakiety administracyjne są zainstalowane w programie Operations Manager. Te pakiety administracyjne są również instalowane na komputerach z systemem Windows podłączonego bezpośrednio po dodaniu rozwiązania. Nie trzeba skonfigurować lub zarządzać tych pakietów administracyjnych.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Pakiet administracyjny wdrożenia aktualizacji

Aby uzyskać więcej informacji dotyczących sposobu aktualizowania rozwiązania pakietów administracyjnych, zobacz [połączenie programu Operations Manager do analizy dzienników](../log-analytics/log-analytics-om-agents.md).

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Sprawdź, czy komputery z systemem innym niż Azure są został załadowany

Aby upewnić się, że z analizy dzienników po kilku minutach komunikują się bezpośrednio połączone maszyny można uruchomić jeden następujące wyszukiwania dziennika.

#### <a name="linux"></a>Linux

```
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Na komputerze z systemem Windows możesz przejrzeć następujące informacje, aby sprawdzić połączenie agenta z analizy dzienników:

1. W Panelu sterowania otwórz **programu Microsoft Monitoring Agent**. Na **Azure Log Analytics** kartę, agent wyświetli następujący komunikat: **programu Microsoft Monitoring Agent została pomyślnie połączono z analizy dzienników**.
2. Otwórz dziennik zdarzeń systemu Windows. Przejdź do **aplikacji i usług Menedżera Logs\Operations** i poszukaj zdarzeń identyfikator 3000 i 5002 identyfikator zdarzenia ze źródła **łącznika usługi**. Te zdarzenia wskazują, że komputer został zarejestrowany za pomocą obszaru roboczego analizy dzienników i otrzymuje konfiguracji.

Jeśli agent jest skonfigurowany do komunikowania się z Internetem za pośrednictwem zapory lub serwera proxy agenta nie może komunikować się z analizy dzienników, upewnij się, że serwer zapory lub serwera proxy jest poprawnie skonfigurowany. Aby dowiedzieć się, jak sprawdzić, czy serwer zapory lub serwera proxy jest poprawnie skonfigurowany, zobacz [konfigurację sieci dla systemu Windows, agent](../log-analytics/log-analytics-agent-windows.md) lub [konfigurację sieci dla agenta systemu Linux](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Jeśli systemy Linux są skonfigurowane do komunikacji z serwerem proxy lub bramy OMS, a w przypadku przechodzenia do tego rozwiązania, aktualizacja *proxy.conf* uprawnień, aby udzielić grupie omiuser uprawnienie do odczytu w pliku za pomocą następujących polecenia:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Nowo dodany agentów systemu Linux pokazuje stan **zaktualizowane** po przeprowadzeniu oceny. Ten proces może potrwać do 6 godzin.

Aby upewnić się, że grupy zarządzania programu Operations Manager komunikuje się z analizy dzienników, zobacz [integracji sprawdzania poprawności programu Operations Manager z analizy dzienników](../log-analytics/log-analytics-om-agents.md#validate-operations-manager-integration-with-log-analytics).

## <a name="data-collection"></a>Zbieranie danych

### <a name="supported-agents"></a>Obsługiwani agenci

W poniższej tabeli opisano połączonych źródeł, które są obsługiwane przez to rozwiązanie:

| Połączone źródło | Obsługiwane | Opis |
| --- | --- | --- |
| Agenci dla systemu Windows |Yes |Rozwiązania zbiera informacje o aktualizacjach systemu z agentów systemu Windows, a następnie inicjuje instalacji wymaganych aktualizacji. |
| Agenci dla systemu Linux |Yes |Rozwiązanie zbiera informacje o aktualizacjach systemu z agentów systemów Linux i inicjuje instalacji wymaganych aktualizacji na obsługiwanych dystrybucjach. |
| Grupa zarządzania programu Operations Manager |Yes |Rozwiązanie zbiera informacje o aktualizacjach systemu z agentów w połączonej grupie zarządzania.<br/>Analiza dzienników bezpośredniego połączenia z agenta programu Operations Manager nie jest wymagane. Dane są przesyłane dalej z grupy zarządzania do obszaru roboczego analizy dzienników. |

### <a name="collection-frequency"></a>Częstotliwość zbierania

Skanowanie jest przeprowadzane dwa razy dziennie dla każdego zarządzanego komputera z systemem Windows. Co 15 minut, wywołuje interfejs API systemu Windows można wyszukiwać czas ostatniej aktualizacji określić, czy stan został zmieniony. Jeśli stan został zmieniony, jest inicjowane skanowania zgodności. 

Skanowanie jest przeprowadzane co 3 godziny, do każdego zarządzanego komputera z systemem Linux.

Może upłynąć od 30 minut do 6 godzin dla pulpitu nawigacyjnego wyświetlić zaktualizowane dane z zarządzanych komputerów.

## <a name="viewing-update-assessments"></a>Widok aktualizacji ocen

Twoje konto usługi Automatyzacja, wybierz **zarządzania aktualizacjami** Aby wyświetlić stan maszyny.

Ten widok zawiera informacje o maszynach brakujące aktualizacje, wdrożenia aktualizacji oraz zaplanowana instalacja aktualizacji wdrożenia. W **kolumny zgodności**, zostanie wyświetlony podczas ostatniego oceniono komputera. W **gotowości aktualizacji AGENTA** kolumny, można wyświetlić kondycję usługi Windows update agent. Jeśli wystąpi problem, wybierz łącze, aby przejść do rozwiązywania problemów z dokumentacją, które mogą ułatwić dowiesz się, jakie czynności wymagane do rozwiązania problemu.

Aby uruchomić wyszukiwanie dziennika, które zwraca informacje dotyczące komputera, aktualizacji lub wdrożenia, wybierz element na liście. **Wyszukiwania dziennika** okienko zostanie otwarty przy użyciu zapytania wybranego elementu:

![Aktualizowanie zarządzania widok domyślny](media/automation-update-management/update-management-view.png)

## <a name="install-updates"></a>Instalowanie aktualizacji

Po aktualizacji są oceniane pod kątem wszystkich komputerów systemu Linux i Windows w obszarze roboczym, można zainstalować wymagane aktualizacje, tworząc *wdrożenia aktualizacji*. Wdrażanie aktualizacji jest zaplanowana instalacja aktualizacji wymaganych dla co najmniej jeden komputer. Należy określić datę i godzinę dla wdrożenia i komputera lub grupy komputerów do uwzględnienia w zakresie wdrożenia. Aby dowiedzieć się więcej na temat grup komputerów, zobacz [Computer groups in Log Analytics](../log-analytics/log-analytics-computer-groups.md) (Grupy komputerów w usłudze Log Analytics).

 Po dołączeniu grup komputerów w danym wdrożeniu aktualizacji członkostwa w grupie jest oceniane tylko raz, w momencie tworzenia harmonogramu. Kolejne zmiany do grupy nie są uwzględniane. Aby obejść ten problem, Usuń wdrożenie zaplanowanych aktualizacji i utwórz go ponownie.

> [!NOTE]
> Maszyny wirtualne systemu Windows, które są wdrażane w witrynie Azure Marketplace domyślnie są ustawiane odbierania automatycznych aktualizacji z usługi Windows Update. To zachowanie nie powoduje zmiany podczas dodawania tego rozwiązania lub dodać do swojego obszaru roboczego maszyny wirtualne systemu Windows. Jeśli nie aktywnie zarządzać aktualizacjami za pomocą tego rozwiązania, stosuje domyślne zachowanie (powoduje automatyczne zastosowanie aktualizacji).

Aby uniknąć aktualizacje stosowane poza oknem konserwacji na Ubuntu, należy ponownie skonfigurować pakiet nienadzorowanego uaktualnienia, aby wyłączyć aktualizacje automatyczne. Aby uzyskać informacje o sposobie konfigurowania pakietu, zobacz [temat aktualizacji automatycznych w podręczniku Ubuntu Server](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Maszyny wirtualne, które zostały utworzone z obrazów Red Hat Enterprise Linux (RHEL) na żądanie, które są dostępne w portalu Azure Marketplace są rejestrowane na dostęp [Red Hat aktualizacji infrastruktury (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) wdrożonym na platformie Azure. Inne dystrybucji systemu Linux musi zostać zaktualizowany z repozytorium pliku online dystrybucji przez następujących obsługiwanych metod dystrybucji.

## <a name="view-missing-updates"></a>Wyświetl brakujące aktualizacje

Wybierz **brakujących aktualizacji** Aby wyświetlić listę aktualizacji, które są dostępne na maszynach. Każda aktualizacja jest wyświetlane i można wybrać. Wyświetlane są informacje o liczbie maszyn, które wymagają aktualizacji systemu operacyjnego i łącze, aby uzyskać więcej informacji. **Wyszukiwania dziennika** w okienku zostaną wyświetlone szczegółowe informacje o aktualizacji.

## <a name="view-update-deployments"></a>Wdrożenia aktualizacji widoku

Wybierz **wdrożeń aktualizacji** kartę, aby wyświetlić listę istniejących wdrożeń aktualizacji. Wybierz jedno z wdrożeń aktualizacji w tabeli, aby otworzyć **wdrożenie aktualizacji uruchomione** okienko dla tego wdrożenia aktualizacji.

![Przegląd wyników wdrożenia aktualizacji](./media/automation-update-management/update-deployment-run.png)

## <a name="create-or-edit-an-update-deployment"></a>Utwórz lub Edytuj wdrożenia aktualizacji

Aby utworzyć nowe wdrożenie aktualizacji, wybierz **harmonogram wdrożenia aktualizacji**. **Nowe wdrożenie aktualizacji** zostanie otwarte okienko. Wprowadź wartości dla właściwości opisane w poniższej tabeli:

| Właściwość | Opis |
| --- | --- |
|Name (Nazwa) |Unikatowa nazwa identyfikującą wdrożenie aktualizacji. |
|System operacyjny| Wybierz **Linux** lub **Windows**.|
|Komputery do zaktualizowania |Wybierz zapisanego kryterium wyszukiwania lub **maszyny** z listy rozwijanej, a następnie wybierz poszczególne maszyny. |
|Klasyfikacje aktualizacji|Wybierz wszystkie klasyfikacje aktualizacji, które są potrzebne. CentOS nie obsługują tych poza pole.|
|Aktualizacje, które mają zostać wykluczone|Wprowadź aktualizacje, które mają zostać wykluczone. W systemie Windows, wprowadź artykułu KB bez **KB** prefiks. Dla systemu Linux wprowadź nazwę pakietu, lub użyj symbolu wieloznacznego.  |
|Ustawienia harmonogramu|Wybierz godzinę rozpoczęcia, a następnie wybierz opcję **raz** lub **cykliczny** cyklu.|| Okno obsługi |Liczba minut dla aktualizacji. Wartość nie może być mniejsza niż 30 minut lub więcej niż 6 godzin. |

## <a name="update-classifications"></a>Klasyfikacje aktualizacji

W poniższych tabelach przedstawiono klasyfikacje aktualizacji w przystawce Zarządzanie aktualizacji definicji dla każdej klasyfikacji.

### <a name="windows"></a>Windows

|Klasyfikacja  |Opis  |
|---------|---------|
|Aktualizacje krytyczne     | Aktualizacje dla konkretnego problemu, którego dotyczy krytyczną usterkę niepowiązaną z zabezpieczeń.        |
|Aktualizacje zabezpieczeń     | Aktualizacji dotyczącej problemu z konkretnym produktem, związanych z zabezpieczeniami.        |
|Pakiety zbiorcze aktualizacji     | Zbiorczego zestawu poprawek zebranych w jednym pakiecie w celu łatwiejszego wdrażania.        |
|Pakiety funkcji     | Nowych funkcji produktów, rozpowszechnianych poza wydaniami.        |
|Dodatki Service Pack     | Zbiorczego zestawu poprawek przeznaczonych do aplikacji.        |
|Aktualizacje definicji     | Aktualizacji definicji wirusów lub innych plików definicji.        |
|Narzędzia     | Narzędzia lub funkcji, która ułatwia wykonywanie jednego lub więcej zadań.        |
|Aktualizacje     | Aktualizacja aplikacji lub pliku, który obecnie jest zainstalowany.        |

### <a name="linux"></a>Linux

|Klasyfikacja  |Opis  |
|---------|---------|
|Aktualizacje krytyczne i zabezpieczeń     | Aktualizacje dla konkretnego problemu lub problemu z konkretnym produktem, związanych z zabezpieczeniami.         |
|Inne aktualizacje     | Wszystkie inne aktualizacje, które nie są szczególnie ważne w charakterze lub nie są aktualizacje zabezpieczeń.        |

Dla systemu Linux, zarządzania aktualizacjami można rozróżnić krytyczne i aktualizacje zabezpieczeń w chmurze podczas wyświetlania danych oceny wydajności z powodu wzbogacenia danych w chmurze. Zarządzanie aktualizacjami dla stosowania poprawek, opiera się na klasyfikacji danych dostępna na komputerze. W przeciwieństwie do innych dystrybucje CentOS nie ma dostępnych informacji poza pole. Jeśli masz maszyny CentOS konfigurowane w taki sposób, aby zwrócić dane zabezpieczeń dla następującego polecenia, zarządzania aktualizacjami będzie można poprawki oparte na klasyfikacji.

```bash
sudo yum -q --security check-update
```

Nie jest obecnie żadna metoda metoda obsługiwana umożliwiające dostępności danych natywnych klasyfikacji na CentOS. W tej chwili tylko optymalnych jest obsługiwane dla klientów, którzy mogą mieć włączone na ich własnych.

## <a name="ports"></a>Porty

Następujące adresy są wymagane w szczególności do zarządzania aktualizacjami. Dane na te adresy są przesyłane za pośrednictwem portu 443.

|Azure publicznego  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net|*. blob.core.usgovcloudapi.net|

Aby uzyskać więcej informacji na temat porty wymagane przez hybrydowy proces roboczy elementu Runbook, zobacz [portów roli hybrydowy proces roboczy](automation-hybrid-runbook-worker.md#hybrid-worker-role).

## <a name="search-logs"></a>Dzienniki wyszukiwania

Oprócz szczegółów, które zostały opublikowane w portalu Azure możesz to zrobić wyszukiwania przed dzienniki. Na stronach rozwiązanie, wybierz **analizy dzienników**. **Wyszukiwania dziennika** zostanie otwarte okienko.

Można też uzyskać jak dostosować zapytania lub używać ich od różnych klientów i bardziej odwiedzając: [dokumentacji działanie interfejsu API usługi Analiza dzienników](
https://dev.loganalytics.io/).

### <a name="sample-queries"></a>Przykładowe zapytania

W poniższych sekcjach przedstawiono przykładowe zapytania dziennika dla rekordów aktualizacji, które są zbierane przez to rozwiązanie:

#### <a name="single-azure-vm-assessment-queries-windows"></a>Pojedynczego zapytania oceny maszyny Wirtualnej Azure (system Windows)

Zamień wartość VMUUID identyfikator GUID maszyny Wirtualnej maszyny wirtualnej, który jest kwerenda. Można znaleźć VMUUID, które mają być używane, uruchamiając następujące zapytanie w analizy dzienników: `Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Brak aktualizacji podsumowania

```
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security"
```

##### <a name="missing-updates-list"></a>Brak listy aktualizacji

```
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

Dla niektórych dystrybucjach systemu Linux jest [kolejności bajtów](https://en.wikipedia.org/wiki/Endianness) niezgodność z wartością VMUUID, które pochodzą z usługi Azure Resource Manager i jakie są przechowywane w analizy dzienników. Następujące zapytanie sprawdza, czy dopasowanie w każdej kolejności bajtów. Zastąp wartości VMUUID big-endian i little endian format identyfikatora GUID do prawidłowo zwracają wyniki. Można znaleźć VMUUID, które mają być używane, uruchamiając następujące zapytanie w analizy dzienników: `Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Brak aktualizacji podsumowania

```
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="missing-updates-list"></a>Brak listy aktualizacji

```
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

```
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
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeeedOtherUpdatesCount=sum(computersNeeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount


```

##### <a name="missing-updates-summary"></a>Brak aktualizacji podsumowania

```
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

```
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

```
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

## <a name="integrate-with-system-center-configuration-manager"></a>Integracja z programem System Center Configuration Manager

Klienci, którzy zainwestowały w programie System Center Configuration Manager do zarządzania komputerami, serwerami i urządzeniami przenośnymi również zależy od siły i dojrzałości programu Configuration Manager, aby ułatwić im zarządzanie aktualizacjami oprogramowania. Configuration Manager jest częścią ich cyklu zarządzania (suma) aktualizacji oprogramowania.

Aby dowiedzieć się, jak zintegrować rozwiązania do zarządzania w programie System Center Configuration Manager, zobacz [integracji System Center Configuration Manager z zarządzania aktualizacjami](oms-solution-updatemgmt-sccmintegration.md).

## <a name="patch-linux-machines"></a>Maszyny z systemem Linux poprawki

W poniższych sekcjach opisano potencjalne problemy z stosowanie poprawek systemu Linux.

### <a name="unexpected-os-level-upgrades"></a>Nieoczekiwany uaktualnień systemu operacyjnego na poziomie

Na niektóre z nich Linux, takich jak Red Hat Enterprise Linux uaktualnień systemu operacyjnego na poziomie może wystąpić przy użyciu pakietów. Może to doprowadzić do zarządzania aktualizacjami uruchamia gdzie zmienia numer wersji systemu operacyjnego. Ponieważ aktualizacji Management używa tych samych metod, aby zaktualizować pakiety, które administrator może użyć lokalnie na komputerze z systemem Linux, to zachowanie jest zamierzone.

Aby uniknąć wersji systemu operacyjnego za pomocą zarządzania aktualizacjami przebiegów aktualizacji, należy użyć **wykluczeń** funkcji.

W systemie Red Hat Enterprise Linux nazwę pakietu, które mają zostać wykluczone jest redhat-release-server.x86_64.

![Pakiety do wykluczenia dla systemu Linux](./media/automation-update-management/linuxpatches.png)

### <a name="critical--security-patches-arent-applied"></a>Krytyczne / poprawki zabezpieczeń nie są stosowane.

Podczas wdrażania aktualizacji do maszyny z systemem Linux, możesz wybrać klasyfikacje aktualizacji. Filtry to aktualizacje, które są stosowane do tych, które spełniają określone kryteria. Ten filtr jest stosowane lokalnie na komputerze po wdrożeniu aktualizacji.

Ponieważ zaktualizować zarządzania przeprowadza wzbogacenia aktualizacji w chmurze, niektóre aktualizacje mogą oflagowane w przystawce Zarządzanie aktualizacji jako mające wpływ na zabezpieczenia, nawet jeśli komputer lokalny nie ma informacji. W związku z tym jeśli aktualizacje krytyczne dotyczą maszyny z systemem Linux, może być aktualizacje, które nie są oznaczone jako mające wpływ zabezpieczeń na komputerze oraz aktualizacje nie są stosowane.

Jednak aktualizacja zarządzania nadal może raportować tego komputera jako są niezgodne, ponieważ ma on dodatkowe informacje o odpowiednich aktualizacji.

Wdrażanie aktualizacji przez aktualizację klasyfikacji nie działa na CentOS poza pole. Dla SUSE wybierając *tylko* inne aktualizacje zgodnie z klasyfikacji może spowodować pewne zabezpieczenia aktualizuje również zainstalowania aktualizacji zabezpieczeń związane z zypper (Menedżera pakietów) lub najpierw jego zależności są wymagane. Jest to ograniczenie zypper. W niektórych przypadkach konieczne może być ponowne uruchomienie wdrożenia aktualizacji, aby sprawdzić w dzienniku aktualizacji.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Aby dowiedzieć się, jak rozwiązywać problemy z zarządzania aktualizacjami, zobacz [Rozwiązywanie problemów z zarządzaniem aktualizacji](troubleshoot/update-management.md)

## <a name="next-steps"></a>Następne kroki

Nadal samouczkiem, aby dowiedzieć się, jak zarządzanie aktualizacjami dla maszyn wirtualnych systemu Windows.

> [!div class="nextstepaction"]
> [Zarządzanie aktualizacje i poprawki dla maszyn wirtualnych systemu Windows Azure](automation-tutorial-update-management.md)

* Użyj dziennika wyszukiwania w [analizy dzienników](../log-analytics/log-analytics-log-searches.md) do wyświetlania danych szczegółowe aktualizacji.
* [Tworzenie alertów](../log-analytics/log-analytics-alerts.md) po wykryciu aktualizacje krytyczne jako brakujące z komputerów lub jeśli na komputerze jest wyłączona, funkcja Aktualizacje automatyczne.
