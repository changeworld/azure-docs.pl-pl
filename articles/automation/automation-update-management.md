---
title: Update Management rozwiązanie na platformie Azure
description: Ten artykuł ma na celu ułatwienie zrozumienia sposobu korzystania z rozwiązania Update Management platformy Azure do zarządzania aktualizacjami komputerów z systemami Windows i Linux.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/22/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4bd0b6f0652f49c16bd67bbca5a89d19e17a8b2c
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498418"
---
# <a name="update-management-solution-in-azure"></a>Update Management rozwiązanie na platformie Azure

Update Management rozwiązanie w Azure Automation służy do zarządzania aktualizacjami systemu operacyjnego na komputerach z systemami Windows i Linux na platformie Azure, w środowiskach lokalnych lub innych dostawców chmury. Umożliwia ono szybką ocenę stanu dostępnych aktualizacji na wszystkich komputerach agentów oraz zarządzanie procesem instalacji wymaganych aktualizacji serwerów.

Update Management dla maszyn wirtualnych można włączyć bezpośrednio z poziomu Azure Automation konta. Aby dowiedzieć się, jak włączyć Update Management dla maszyn wirtualnych na podstawie konta usługi Automation, zobacz [Zarządzanie aktualizacjami dla wielu maszyn wirtualnych](manage-update-multi.md). Update Management dla maszyny wirtualnej można również włączyć na stronie maszyny wirtualnej w Azure Portal. Ten scenariusz jest dostępny dla maszyn wirtualnych z systemem [Linux](../virtual-machines/linux/tutorial-monitoring.md#enable-update-management) i [Windows](../virtual-machines/windows/tutorial-monitoring.md#enable-update-management) .

> [!NOTE]
> Rozwiązanie Update Management wymaga połączenia obszaru roboczego Log Analytics z kontem usługi Automation. Aby uzyskać ostateczną listę obsługiwanych regionów, zobacz [./How-to/region-Mappings.MD]. Mapowania regionów nie mają wpływu na możliwość zarządzania maszynami wirtualnymi w osobnym regionie niż konto usługi Automation.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="solution-overview"></a>Omówienie rozwiązania

Komputery zarządzane przez program Update Management używają następujących konfiguracji do przeprowadzania wdrożeń oceny i aktualizacji:

* Microsoft Monitoring Agent (MMA) dla systemu Windows lub Linux
* Platforma PowerShell Desired State Configuration (DSC) dla systemu Linux
* Hybrydowy proces roboczy elementu runbook usługi Automation
* Microsoft Update lub Windows Server Update Services (WSUS) dla komputerów z systemem Windows

Na poniższym diagramie przedstawiono widok koncepcyjny zachowań i przepływu danych, w wyniku których rozwiązanie ocenia i stosuje aktualizacje zabezpieczeń do wszystkich połączonych komputerów z systemem Windows Server i Linux w obszarze roboczym:

![Przepływ procesu Update Management](./media/automation-update-management/update-mgmt-updateworkflow.png)

Update Management może służyć do natywnego dołączania maszyn w ramach wielu subskrypcji w ramach tej samej dzierżawy.

Po wydaniu pakietu trwa 2-3 godzin, aby poprawka była wyświetlana dla maszyn z systemem Linux na potrzeby oceny. W przypadku maszyn z systemem Windows trwa 12-15 godzin, aby poprawka była wyświetlana na potrzeby oceny po jej udostępnieniu.

Gdy komputer ukończy skanowanie pod kątem zgodności aktualizacji, Agent przekaże informacje zbiorczo do dzienników Azure Monitor. Na komputerze z systemem Windows skanowanie zgodności jest domyślnie uruchamiane co 12 godzin.

Oprócz harmonogramu skanowania skanowanie pod kątem zgodności z aktualizacjami jest inicjowane w ciągu 15 minut od ponownego uruchomienia MMA, przed instalacją aktualizacji i po zainstalowaniu aktualizacji.

W przypadku komputera z systemem Linux skanowanie zgodności jest wykonywane co godzinę domyślnie. Jeśli Agent MMA zostanie ponownie uruchomiony, skanowanie zgodności zostanie zainicjowane w ciągu 15 minut.

Rozwiązanie raportuje, jak aktualna jest Konfiguracja komputera, na podstawie którego źródła skonfigurowano do synchronizacji. Jeśli komputer z systemem Windows jest skonfigurowany do raportowania do programu WSUS, w zależności od tego, kiedy program WSUS został ostatnio zsynchronizowany z Microsoft Update, wyniki mogą się różnić od tego, co są wyświetlane w usłudze Microsoft Updates. Takie zachowanie jest takie samo dla komputerów z systemem Linux, które są skonfigurowane do raportowania do lokalnego repozytorium, a nie do repozytorium publicznego.

> [!NOTE]
> Aby poprawnie zgłosić usługę, Update Management wymaga włączenia określonych adresów URL i portów. Aby dowiedzieć się więcej na temat tych wymagań, zobacz [Planowanie sieci dla hybrydowych procesów roboczych](automation-hybrid-runbook-worker.md#network-planning).

Aktualizacje oprogramowania można wdrożyć i zainstalować na komputerach, które ich wymagają, tworząc zaplanowane wdrożenie. Aktualizacje sklasyfikowane jako *opcjonalne* nie są uwzględnione w zakresie wdrożenia dla komputerów z systemem Windows. Zakres wdrożenia obejmuje tylko wymagane aktualizacje.

Zaplanowane wdrożenie określa, które komputery docelowe otrzymują odpowiednie aktualizacje, jawnie określając komputery lub wybierając [grupę komputerów](../azure-monitor/platform/computer-groups.md) opartą na przeszukiwaniu dzienników określonego zestawu komputerów lub [kwerendzie platformy Azure](#azure-machines) Umożliwia to dynamiczne Wybieranie maszyn wirtualnych platformy Azure na podstawie określonych kryteriów. Te grupy różnią się od [konfiguracji zakresu](../azure-monitor/insights/solution-targeting.md), która jest używana tylko do określania, które maszyny otrzymują pakiety administracyjne, które umożliwiają rozwiązanie.

Należy również określić harmonogram zatwierdzania i ustawiania okresu czasu, w którym można zainstalować aktualizacje. Ten okres czasu jest określany w oknie obsługi. Dziesięć minut okna obsługi jest zarezerwowane dla ponownych uruchomień, jeśli jest wymagane ponowne uruchomienie komputera i wybrano odpowiednią opcję ponownego uruchamiania. Jeśli stosowanie poprawek trwa dłużej niż oczekiwano, a w oknie obsługi jest mniej niż dziesięć minut, ponowne uruchomienie nie zostanie wykonane.

Aktualizacje są instalowane przez elementy runbook w usłudze Azure Automation. Nie można wyświetlić tych elementów Runbook, a elementy Runbook nie wymagają żadnej konfiguracji. Po utworzeniu wdrożenia aktualizacji wdrożenie aktualizacji tworzy harmonogram, który uruchamia główny element Runbook aktualizacji w określonym czasie dla uwzględnionych komputerów. Główny element Runbook uruchamia podrzędny element Runbook na każdym agencie, aby zainstalować wymagane aktualizacje.

W dniu i czasie określonym we wdrożeniu aktualizacji komputery docelowe wykonują wdrożenie równolegle. Przed rozpoczęciem instalacji zostanie uruchomione skanowanie w celu sprawdzenia, czy aktualizacje są nadal wymagane. W przypadku komputerów klienckich WSUS, jeśli aktualizacje nie są zatwierdzone w programie WSUS, wdrożenie aktualizacji zakończy się niepowodzeniem.

Posiadanie maszyny zarejestrowanej dla Update Management w więcej niż jednym Log Analytics obszarach roboczych (multihostingu) nie jest obsługiwane.

## <a name="clients"></a>Klienci

### <a name="supported-client-types"></a>Typy obsługiwanych klientów

W poniższej tabeli przedstawiono listę obsługiwanych systemów operacyjnych:

|System operacyjny  |Uwagi  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Obsługuje tylko oceny aktualizacji.         |
|Windows Server 2008 R2 z dodatkiem SP1 lub nowszym.  |Wymagany jest .NET Framework 4.5.1 lub nowszy. ([Pobierz .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> Wymagany jest program Windows PowerShell w wersji 4,0 lub nowszej. ([Pobierz zawmf 4,0](https://www.microsoft.com/download/details.aspx?id=40855))<br/> W celu zwiększenia niezawodności zaleca się używanie programu Windows PowerShell 5,1.  ([Pobierz zawmf 5,1](https://www.microsoft.com/download/details.aspx?id=54616))        |
|CentOS 6 (x86/x64) i 7 (x64)      | Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji. Stosowanie poprawek opartych na klasyfikacji wymaga, aby element "yum" zwracał dane zabezpieczeń, które nie znajdują się w polu CentOS. Aby uzyskać więcej informacji na temat stosowania poprawek opartych na klasyfikacji na CentOS, zobacz [Aktualizacja klasyfikacji w systemie Linux](#linux-2)          |
|Red Hat Enterprise 6 (x86/x64) i 7 (x64)     | Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji.        |
|SUSE Linux Enterprise Server 11 (x86/x64) i 12 (x64)     | Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji.        |
|Ubuntu 14,04 LTS, 16,04 LTS i 18,04 (x86/x64)      |Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji.         |

> [!NOTE]
> Zestawy skalowania maszyn wirtualnych platformy Azure mogą być zarządzane za pomocą Update Management. Update Management działa na samych wystąpieniach, a nie na obrazie podstawowym. Należy zaplanować aktualizacje w sposób przyrostowy, co nie spowoduje jednoczesnego zaktualizowania wszystkich wystąpień maszyn wirtualnych.

### <a name="unsupported-client-types"></a>Typy klientów nieobsługiwanych

W poniższej tabeli wymieniono systemy operacyjne, które nie są obsługiwane:

|System operacyjny  |Uwagi  |
|---------|---------|
|Klient systemu Windows     | Systemy operacyjne klienta (takich jak Windows 7 i Windows 10) nie są obsługiwane.        |
|Windows Server 2016 Nano Server     | Nieobsługiwane.       |

### <a name="client-requirements"></a>Wymagania dotyczące klienta

#### <a name="windows"></a>Windows

Agenci systemu Windows muszą być skonfigurowani do komunikowania się z serwerem WSUS lub muszą mieć dostęp do Microsoft Update. Możesz użyć Update Management z System Center Configuration Manager. Aby dowiedzieć się więcej na temat scenariuszy integracji, zobacz [integracja System Center Configuration Manager z Update Management](oms-solution-updatemgmt-sccmintegration.md#configuration). Wymagany jest [Agent systemu Windows](../azure-monitor/platform/agent-windows.md) . Agent zostanie zainstalowany automatycznie, jeśli zostanie dołączona maszyna wirtualna platformy Azure.

#### <a name="linux"></a>Linux

W przypadku systemu Linux komputer musi mieć dostęp do repozytorium aktualizacji. Repozytorium aktualizacji może być prywatne lub publiczne. Do współdziałania z Update Managementami wymagany jest protokół TLS 1,1 lub TLS 1,2. Agent Log Analytics dla systemu Linux skonfigurowany do raportowania do więcej niż jednego Log Analytics obszarów roboczych nie jest obsługiwany w tym rozwiązaniu.

Aby uzyskać informacje na temat sposobu instalowania agenta Log Analytics dla systemu Linux i pobierania najnowszej wersji, zobacz [log Analytics Agent dla systemu Linux](https://github.com/microsoft/oms-agent-for-linux). Aby uzyskać informacje na temat sposobu instalowania agenta Log Analytics dla systemu Windows, zobacz [Microsoft Monitoring Agent dla systemu Windows](../log-analytics/log-analytics-windows-agent.md).

## <a name="permissions"></a>Uprawnienia

Do tworzenia wdrożeń aktualizacji i zarządzania nimi wymagane są określone uprawnienia. Aby dowiedzieć się więcej o tych uprawnieniach, zobacz [dostęp oparty na rolach — Update Management](automation-role-based-access-control.md#update-management).

## <a name="solution-components"></a>Składniki rozwiązania

Rozwiązanie składa się z następujących zasobów. Zasoby są dodawane do konta usługi Automation. Są to bezpośrednio połączeni agenci lub w połączonej Operations Manager grupie zarządzania.

### <a name="hybrid-worker-groups"></a>Grupy hybrydowych procesów roboczych

Po włączeniu tego rozwiązania każdy komputer z systemem Windows, który jest bezpośrednio połączony z obszarem roboczym Log Analytics, zostanie automatycznie skonfigurowany jako hybrydowy proces roboczy elementu Runbook w celu obsługi elementów Runbook uwzględnionych w tym rozwiązaniu.

Każdy komputer z systemem Windows, który jest zarządzany przez rozwiązanie, znajduje się w okienku **grupy hybrydowych procesów roboczych** jako Grupa hybrydowych procesów **roboczych systemu** dla konta usługi Automation. Rozwiązania używają konwencji nazewnictwa *hostname FQDN_GUID*. Nie można kierować tymi grupami do elementów Runbook na Twoim koncie. Jeśli spróbujesz, nie powiodą się. Te grupy mają na celu obsługę tylko rozwiązania do zarządzania.

Komputery z systemem Windows można dodać do grupy hybrydowych procesów roboczych elementu Runbook na koncie usługi Automation w celu obsługi elementów Runbook usługi Automation, jeśli używasz tego samego konta zarówno dla tego rozwiązania, jak i dla członkostwa w grupie hybrydowych procesów roboczych elementu Runbook. Ta funkcja została dodana w wersji 7.2.12024.0 hybrydowego procesu roboczego elementu Runbook.

### <a name="management-packs"></a>Pakiety administracyjne

Jeśli grupa zarządzania System Center Operations Manager jest połączona z obszarem roboczym Log Analytics, następujące pakiety administracyjne są instalowane w Operations Manager. Te pakiety administracyjne są również instalowane na komputerach z systemem Windows bezpośrednio połączonym po dodaniu rozwiązania. Nie trzeba konfigurować tych pakietów administracyjnych ani nimi zarządzać.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Pakiet administracyjny wdrożenia aktualizacji

> [!NOTE]
> Jeśli masz grupę zarządzania Operations Manager 1807 z agentami skonfigurowanymi na poziomie grupy zarządzania do skojarzenia z obszarem roboczym, bieżące obejście, aby je wyświetlić, ma na celu przesłonięcie **IsAutoRegistrationEnabled** na **true** w **Microsoft. IntelligencePacks. AzureAutomation. HybridAgent. init** — reguła.

Aby uzyskać więcej informacji na temat aktualizacji pakietów zarządzania rozwiązaniami, zobacz [Connect Operations Manager to Azure monitor Logs](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> Aby systemy z agentem programu Operations Manager mogły być w pełni zarządzane przez Update Management, Agent należy zaktualizować do Microsoft Monitoring Agent. Aby dowiedzieć się, jak zaktualizować agenta, zobacz [jak uaktualnić agenta Operations Manager](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents). W środowiskach, w których jest używany Operations Manager, wymagany jest program System Center Operations Manager 2012 R2 UR 14 lub nowszy.

## <a name="onboard"></a>Włącz Update Management

Aby rozpocząć stosowanie poprawek do systemów, należy włączyć rozwiązanie Update Management. Istnieje wiele sposobów dołączania maszyn do Update Management. Poniżej przedstawiono zalecane i obsługiwane sposoby dołączenia rozwiązania:

* [Z maszyny wirtualnej](automation-onboard-solutions-from-vm.md)
* [Przeglądanie wielu maszyn](automation-onboard-solutions-from-browse.md)
* [Na koncie usługi Automation](automation-onboard-solutions-from-automation-account.md)
* [Z elementem Runbook Azure Automation](automation-onboard-solutions.md)

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Potwierdź, że maszyny spoza platformy Azure są dołączone

Aby upewnić się, że bezpośrednio połączone maszyny komunikują się z dziennikami Azure Monitor, po kilku minutach można uruchomić jedno z poniższych przeszukiwania dzienników.

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

Na komputerze z systemem Windows można przejrzeć następujące informacje w celu zweryfikowania łączności agentów z dziennikami Azure Monitor:

1. W panelu sterowania Otwórz **Microsoft Monitoring Agent**. Na karcie **Azure log Analytics** Agent zostanie wyświetlony następujący komunikat: **Microsoft Monitoring Agent połączyła się z log Analytics**.
2. Otwórz dziennik zdarzeń systemu Windows. Przejdź do pozycji **Application and Services Logs\Operations Manager** i Wyszukaj zdarzenia o identyfikatorze 3000 i identyfikatorze 5002 z **łącznika usługi**źródłowej. Te zdarzenia wskazują, że komputer został zarejestrowany w obszarze roboczym Log Analytics i otrzymuje konfigurację.

Jeśli Agent nie może komunikować się z dziennikami Azure Monitor i Agent jest skonfigurowany do komunikacji z Internetem za pomocą zapory lub serwera proxy, upewnij się, że zapora lub serwer proxy zostały prawidłowo skonfigurowane. Aby dowiedzieć się, jak upewnić się, że zapora lub serwer proxy są prawidłowo skonfigurowane, zobacz [Konfiguracja sieci dla agenta systemu Windows](../azure-monitor/platform/agent-windows.md) lub [Konfiguracja sieci dla agentów](../log-analytics/log-analytics-agent-linux.md)z systemem Linux.

> [!NOTE]
> Jeśli system Linux jest skonfigurowany do komunikowania się z serwerem proxy lub bramą Log Analytics i dołączasz to rozwiązanie, zaktualizuj *serwer proxy. conf* , aby przyznać grupie omiuser uprawnienia do odczytu w pliku przy użyciu następujących poleceń:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Nowo dodani agenci systemu Linux pokazują stan **aktualizacji** po wykonaniu oceny. Ten proces może potrwać do 6 godzin.

Aby upewnić się, że Operations Manager grupy zarządzania komunikuje się z dziennikami Azure Monitor, zobacz temat [Weryfikuj integrację Operations Manager z dziennikami Azure monitor](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor).

## <a name="data-collection"></a>Zbieranie danych

### <a name="supported-agents"></a>Obsługiwani agenci

W poniższej tabeli opisano połączone źródła, które są obsługiwane przez to rozwiązanie:

| Połączone źródło | Obsługiwane | Opis |
| --- | --- | --- |
| Agenci dla systemu Windows |Tak |Rozwiązanie zbiera informacje o aktualizacjach systemu z agentów systemu Windows, a następnie inicjuje instalację wymaganych aktualizacji. |
| Agenci dla systemu Linux |Tak |Rozwiązanie zbiera informacje o aktualizacjach systemu z agentów z systemem Linux, a następnie inicjuje instalację wymaganych aktualizacji w obsługiwanych dystrybucjach. |
| Grupa zarządzania programu Operations Manager |Tak |Rozwiązanie zbiera informacje o aktualizacjach systemu z agentów w połączonej grupie zarządzania.<br/>Bezpośrednie połączenie z agentem Operations Manager do Azure Monitor dzienników nie jest wymagane. Dane są przekazywane z grupy zarządzania do obszaru roboczego usługi Log Analytics. |

### <a name="collection-frequency"></a>Częstotliwość zbierania

Skanowanie jest wykonywane dwa razy dziennie dla każdego zarządzanego komputera z systemem Windows. Co 15 minut interfejs API systemu Windows jest wywoływany, aby wykonać zapytanie o czas ostatniej aktualizacji w celu ustalenia, czy stan został zmieniony. W przypadku zmiany stanu zostanie zainicjowane skanowanie zgodności.

Skanowanie jest wykonywane co godzinę dla każdego zarządzanego komputera z systemem Linux.

Wyświetlenie zaktualizowanych danych z zarządzanych komputerów przez pulpit nawigacyjny może potrwać od 30 minut do 6 godzin.

Średni Azure Monitor rejestruje użycie danych dla maszyny przy użyciu Update Management jest około 25 MB miesięcznie. Ta wartość jest tylko przybliżeniem i może ulec zmianie w zależności od używanego środowiska. Zalecamy monitorowanie środowiska, aby zobaczyć dokładne użycie.

## <a name="viewing-update-assessments"></a>Wyświetlanie ocen aktualizacji

Na koncie usługi Automation wybierz pozycję **Update Management** , aby wyświetlić stan maszyn.

Ten widok zawiera informacje dotyczące maszyn, brakujących aktualizacji, wdrożeń aktualizacji i zaplanowanych wdrożeń aktualizacji. W **kolumnie zgodność**można zobaczyć czas ostatniego oceny maszyny. W kolumnie **Aktualizowanie gotowości agenta** można sprawdzić, czy kondycja agenta aktualizacji jest poprzednia. Jeśli wystąpił problem, wybierz link, aby przejść do dokumentacji dotyczącej rozwiązywania problemów, która może ułatwić zapoznanie się z krokami, które należy podjąć w celu rozwiązania problemu.

Aby uruchomić wyszukiwanie w dzienniku, które zwraca informacje o komputerze, aktualizacji lub wdrożeniu, wybierz element z listy. Zostanie otwarte okienko przeszukiwania **dzienników** z zapytaniem dotyczącym wybranego elementu:

![Update Management widoku domyślnego](media/automation-update-management/update-management-view.png)

## <a name="install-updates"></a>Zainstaluj aktualizacje

Po ocenie aktualizacji dla wszystkich komputerów z systemem Linux i Windows w obszarze roboczym można zainstalować wymagane aktualizacje, tworząc *wdrożenie aktualizacji*. Aby można było utworzyć wdrożenie aktualizacji, musisz mieć dostęp do zapisu do konta usługi Automation i zapisywać dostęp do wszystkich maszyn wirtualnych platformy Azure, które są przeznaczone do wdrożenia. Wdrożenie aktualizacji to zaplanowana instalacja wymaganych aktualizacji na co najmniej jednym komputerze. Należy określić datę i godzinę wdrożenia oraz komputer lub grupę komputerów, które mają zostać uwzględnione w zakresie wdrożenia. Aby dowiedzieć się więcej na temat grup komputerów, zobacz [grupy komputerów w dziennikach Azure monitor](../azure-monitor/platform/computer-groups.md).

Po dołączeniu grup komputerów we wdrożeniu aktualizacji członkostwo w grupie jest oceniane tylko raz w momencie tworzenia harmonogramu. Kolejne zmiany w grupie nie są uwzględniane. Aby obejść ten sposób korzystania z [grup dynamicznych](#using-dynamic-groups), te grupy są rozwiązywane w czasie wdrażania i są definiowane przez zapytanie dotyczące maszyn wirtualnych platformy Azure lub zapisane wyszukiwanie maszyn wirtualnych spoza platformy Azure.

> [!NOTE]
> Maszyny wirtualne z systemem Windows, które są domyślnie wdrożone z portalu Azure Marketplace, są ustawione tak, aby otrzymywać aktualizacje automatyczne z usługi Windows Update. To zachowanie nie zmienia się po dodaniu tego rozwiązania lub dodaniu maszyn wirtualnych z systemem Windows do obszaru roboczego. Jeśli aktualizacje nie są aktywnie zarządzane przy użyciu tego rozwiązania, mają zastosowanie domyślne zachowanie (aby automatycznie zastosować aktualizacje).

Aby uniknąć stosowania aktualizacji poza oknem obsługi w programie Ubuntu, należy ponownie skonfigurować pakiet nienadzorowany do uaktualnienia, aby wyłączyć aktualizacje automatyczne. Informacje o sposobie konfigurowania pakietu programu znajdują się [w temacie Aktualizacje automatyczne w przewodniku po serwerze Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Maszyny wirtualne, które zostały utworzone na podstawie obrazów Red Hat Enterprise Linux na żądanie (RHEL) dostępnych w portalu Azure Marketplace, są zarejestrowane w celu uzyskania dostępu do [infrastruktury aktualizacji firmy Red Hat (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) wdrożonej na platformie Azure. Wszystkie inne dystrybucje systemu Linux należy zaktualizować z repozytorium plików online dystrybucji, postępując zgodnie z obsługiwanymi metodami dystrybucji.

Aby utworzyć nowe wdrożenie aktualizacji, wybierz pozycję **Zaplanuj wdrożenie aktualizacji**. Zostanie otwarta strona **nowe wdrożenie aktualizacji** . Wprowadź wartości dla właściwości opisanych w poniższej tabeli, a następnie kliknij pozycję **Utwórz**:

| Właściwość | Description |
| --- | --- |
| Name (Nazwa) |Unikatowa nazwa identyfikującą wdrożenie aktualizacji. |
|System operacyjny| System Linux lub Windows|
| Grupy do zaktualizowania |W przypadku maszyn platformy Azure Zdefiniuj zapytanie w oparciu o kombinację subskrypcji, grup zasobów, lokalizacji i tagów, aby utworzyć dynamiczną grupę maszyn wirtualnych platformy Azure, które mają zostać uwzględnione we wdrożeniu. </br></br>W przypadku maszyn spoza platformy Azure Wybierz istniejące zapisane wyszukiwanie, aby wybrać grupę maszyn nienależących do platformy Azure, które mają zostać uwzględnione we wdrożeniu. </br></br>Aby dowiedzieć się więcej, zobacz [Grupy dynamiczne](automation-update-management.md#using-dynamic-groups)|
| Maszyny do zaktualizowania |Wybierz zapisane wyszukiwanie bądź zaimportowaną grupę lub wybierz maszynę z listy rozwijanej, a następnie wybierz poszczególne maszyny. Jeśli wybierzesz pozycję **Maszyny**, gotowość maszyny będzie wyświetlana w kolumnie **AKTUALIZUJ GOTOWOŚĆ AGENTA**.</br> Aby dowiedzieć się więcej na temat różnych metod tworzenia grup komputerów w dziennikach usługi Azure Monitor, zobacz [Computer groups in Azure Monitor logs (Grupy komputerów w dziennikach usługi Azure Monitor)](../azure-monitor/platform/computer-groups.md) |
|Aktualizuj klasyfikacje|Wybierz wszystkie wymagane klasyfikacje aktualizacji|
|Uwzględnij/Wyklucz aktualizacje|Spowoduje to otwarcie strony dołączania **/** wykluczania. Aktualizacje, które mają zostać uwzględnione lub wykluczone, znajdują się na osobnych kartach. Aby uzyskać więcej informacji na temat sposobu obsługi dołączania, zobacz [zachowanie dołączania](automation-update-management.md#inclusion-behavior) |
|Ustawienia harmonogramu|Wybierz godzinę do uruchomienia, a następnie wybierz jedno lub cykliczne dla cyklu|
| Skrypty przed skryptami + po skrypcie|Wybierz skrypty do uruchomienia przed i po wdrożeniu|
| Okno obsługi |Liczba minut ustawiona dla aktualizacji. Wartość nie może być mniejsza niż 30 minut i nie więcej niż 6 godzin |
| Kontrola ponownego uruchamiania| Określa, jak należy obsługiwać ponowny rozruch. Dostępne opcje:</br>Ponowne uruchomienie, jeśli jest to wymagane (ustawienie domyślne)</br>Zawsze uruchamiaj ponownie</br>Nigdy nie uruchamiaj ponownie</br>Tylko ponowne uruchomienie — aktualizacje nie zostaną zainstalowane|

Wdrożenia aktualizacji można także tworzyć programowo. Aby dowiedzieć się, jak utworzyć wdrożenie aktualizacji za pomocą interfejsu API REST, zobacz [konfiguracje aktualizacji oprogramowania — tworzenie](/rest/api/automation/softwareupdateconfigurations/create). Istnieje również przykładowy element Runbook, który może służyć do tworzenia tygodniowego wdrożenia aktualizacji. Aby dowiedzieć się więcej na temat tego elementu Runbook, zobacz [Tworzenie tygodniowego wdrożenia aktualizacji dla co najmniej jednej maszyny wirtualnej w grupie zasobów](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

### <a name="multi-tenant"></a>Wdrożenia aktualizacji między dzierżawcami

Jeśli masz maszyny w innym raportowaniu dzierżawy platformy Azure, aby Update Management, które muszą zostać poprawione, musisz użyć następującego obejścia, aby je zaplanować. Możesz użyć polecenia cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) z przełącznikiem `-ForUpdate` [, aby utworzyć harmonogram, i użyć polecenia cmdlet New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) i przekazać maszyny w innym dzierżawca do `-NonAzureComputer` parametru. Poniższy przykład pokazuje przykład, jak to zrobić:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="view-missing-updates"></a>Wyświetl brakujące aktualizacje

Wybierz pozycję **brakujące aktualizacje** , aby wyświetlić listę aktualizacji, których brakuje na Twoich komputerach. Każda aktualizacja jest wyświetlana i można ją wybrać. Informacje o liczbie maszyn, które wymagają aktualizacji, systemu operacyjnego i linku, aby uzyskać więcej informacji. W okienku przeszukiwania **dzienników** są wyświetlane szczegółowe informacje o aktualizacjach.

## <a name="view-update-deployments"></a>Wyświetl wdrożenia aktualizacji

Wybierz kartę **wdrożenia aktualizacji** , aby wyświetlić listę istniejących wdrożeń aktualizacji. Wybierz dowolne wdrożenia aktualizacji w tabeli, aby otworzyć okienko **uruchamiania wdrożenia aktualizacji** dla tego wdrożenia aktualizacji. Dzienniki zadań są przechowywane przez maksymalnie 30 dni.

![Przegląd wyników wdrożenia aktualizacji](./media/automation-update-management/update-deployment-run.png)

Aby wyświetlić wdrożenie aktualizacji z interfejsu API REST, zobacz [Uruchamianie konfiguracji aktualizacji oprogramowania](/rest/api/automation/softwareupdateconfigurationruns).

## <a name="update-classifications"></a>Aktualizuj klasyfikacje

W poniższej tabeli wymieniono klasyfikacje aktualizacji w Update Management z definicją dla każdej klasyfikacji.

### <a name="windows"></a>Windows

|Klasyfikacja  |Opis  |
|---------|---------|
|Aktualizacje krytyczne     | Aktualizacja dla konkretnego problemu, która stanowi odpowiedź na krytyczną usterkę niepowiązaną z zabezpieczeniami.        |
|Aktualizacje zabezpieczeń     | Aktualizacja problemu dotyczącego określonego produktu, związanego z zabezpieczeniami.        |
|Pakiety zbiorcze aktualizacji     | Zbiorczy zestaw poprawek, które są pakowane razem w celu łatwiejszego wdrażania.        |
|Pakiety funkcji     | Nowe funkcje produktu dystrybuowane poza wydaniem produktu.        |
|Dodatki Service Pack     | Zbiorczy zestaw poprawek, które są stosowane do aplikacji.        |
|Aktualizacje definicji     | Aktualizacja dla wirusów lub innych plików definicji.        |
|Narzędzia     | Narzędzie lub funkcja, która pomaga wykonać jedno lub więcej zadań.        |
|Aktualizacje     | Aktualizacja aplikacji lub pliku, który jest aktualnie zainstalowany.        |

### <a name="linux-2"></a>Linux

|Klasyfikacja  |Opis  |
|---------|---------|
|Aktualizacje krytyczne i zabezpieczeń     | Aktualizacje dotyczące konkretnego problemu lub problemu związanego z zabezpieczeniami.         |
|Inne aktualizacje     | Wszystkie inne aktualizacje, których charakter nie ma znaczenia lub nie są aktualizacjami zabezpieczeń.        |

W przypadku systemu Linux Update Management może rozróżnić aktualizacje krytyczne i zabezpieczenia w chmurze, a dane oceny są wyświetlane z powodu wzbogacania danych w chmurze. W przypadku stosowania poprawek Update Management opiera się na danych klasyfikacji dostępnych na komputerze. W przeciwieństwie do innych dystrybucji, CentOS nie ma dostępnych informacji. Jeśli masz skonfigurowane maszyny CentOS w sposób, aby zwracały dane zabezpieczeń dla poniższego polecenia, Update Management będzie można zastosować poprawki na podstawie klasyfikacji.

```bash
sudo yum -q --security check-update
```

Obecnie nie jest obsługiwana metoda umożliwiająca natywną klasyfikację — dostępność danych w CentOS. W tej chwili tylko Najlepsza pomoc techniczna jest świadczona klientom, którzy mogli ją samodzielnie włączyć.

## <a name="firstparty-predownload"></a>Ustawienia zaawansowane

Update Management polega na Windows Update do pobrania i zainstalowania aktualizacji systemu Windows. W związku z tym szanujemy wiele ustawień używanych przez Windows Update. Jeśli używasz ustawień do włączania aktualizacji innych niż Windows, Update Management będą również zarządzać tymi aktualizacjami. Jeśli chcesz włączyć pobieranie aktualizacji przed wystąpieniem wdrożenia aktualizacji, wdrożenia aktualizacji mogą być szybsze i mniejsze niż przekroczenie okna obsługi.

### <a name="pre-download-updates"></a>Pobierz wstępnie aktualizacje

Aby skonfigurować automatyczne pobieranie aktualizacji w zasady grupy, można ustawić [ustawienie Konfiguruj aktualizacje automatyczne](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) na **3**. Spowoduje to pobranie aktualizacji wymaganych w tle, ale nie ich zainstalowanie. Pozwala to zachować Update Management kontrolę nad harmonogramami, ale umożliwia pobieranie aktualizacji poza oknem obsługi Update Management. Może to uniemożliwić przekroczenie przez **okno obsługi** błędów w Update Management.

Możesz również ustawić ten program przy użyciu programu PowerShell, uruchom następujące polecenie programu PowerShell w systemie, który ma pobierać aktualizacje do pobrania.

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

### <a name="disable-automatic-installation"></a>Wyłącz instalację automatyczną

Automatyczne instalowanie aktualizacji maszyn wirtualnych platformy Azure jest domyślnie włączone. Może to spowodować zainstalowanie aktualizacji przed zaplanowaniem ich instalacji przez Update Management. To zachowanie można wyłączyć, ustawiając `NoAutoUpdate` klucz rejestru na. `1` Poniższy fragment kodu programu PowerShell pokazuje, jak to zrobić.

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

### <a name="enable-updates-for-other-microsoft-products"></a>Włącz aktualizacje dla innych produktów firmy Microsoft

Domyślnie Windows Update udostępnia tylko aktualizacje dla systemu Windows. Jeśli włączysz opcję **Udostępnij aktualizacje dla innych produktów firmy Microsoft, gdy zaktualizuję system Windows**, masz aktualizacje dla innych produktów, w tym poprawki zabezpieczeń dla SQL Server lub oprogramowania innych firm. Tej opcji nie można skonfigurować za pomocą zasady grupy. Uruchom następujące polecenie programu PowerShell w systemach, na których chcesz włączyć inne poprawki pierwszej strony, a Update Management będzie przestrzegać tego ustawienia.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="third-party"></a>Poprawki innych firm w systemie Windows

Update Management opiera się na lokalnie skonfigurowanym repozytorium aktualizacji w celu zastosowania poprawek obsługiwanych systemów Windows. Jest to program WSUS lub Windows Update. Narzędzia, [takie](/sccm/sum/tools/updates-publisher
) jak System Center Updates Publisher (aktualizacje wydawcy), umożliwiają publikowanie niestandardowych aktualizacji w usługach WSUS. Ten scenariusz umożliwia Update Management poprawek maszyn, które używają System Center Configuration Manager jako repozytorium aktualizacji z oprogramowaniem innych firm. Aby dowiedzieć się, jak skonfigurować program Updates Publisher, zobacz [Install Updates Publisher](/sccm/sum/tools/install-updates-publisher).

## <a name="ports"></a>Planowanie sieci

Poniższe adresy są wymagane dla Update Management. Komunikacja z tymi adresami odbywa się za pośrednictwem portu 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*. azure-automation.us|

Aby uzyskać więcej informacji na temat portów wymaganych przez hybrydowy proces roboczy elementu Runbook, zobacz [porty ról hybrydowych procesów roboczych](automation-hybrid-runbook-worker.md#hybrid-worker-role).

Zaleca się użycie adresów wymienionych podczas definiowania wyjątków. Dla adresów IP można pobrać zakresy adresów [IP centrum danych Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Ten plik jest aktualizowany co tydzień i odzwierciedla aktualnie wdrożone zakresy oraz wszystkie nadchodzące zmiany w zakresach adresów IP.

## <a name="search-logs"></a>Przeszukiwanie dzienników

Oprócz szczegółów, które są dostępne w Azure Portal, można przeszukiwać dzienniki. Na stronie rozwiązania wybierz pozycję **log Analytics**. Zostanie otwarte okienko przeszukiwania **dzienników** .

Możesz również dowiedzieć się, jak dostosować zapytania lub użyć ich od różnych klientów i więcej, odwiedzając:  [Dokumentacja](
https://dev.loganalytics.io/)interfejsu API wyszukiwania log Analytics.

### <a name="sample-queries"></a>Przykładowe zapytania

Poniższe sekcje zawierają przykładowe zapytania dzienników dla rekordów aktualizacji, które są zbierane przez to rozwiązanie:

#### <a name="single-azure-vm-assessment-queries-windows"></a>Pojedyncze zapytania oceny maszyny wirtualnej platformy Azure (system Windows)

Zastąp wartość VMUUID wartością GUID maszyny wirtualnej, która jest używana do wykonywania zapytań. Możesz znaleźć VMUUID, które powinny być używane, uruchamiając następujące zapytanie w Azure Monitor dziennikach:`Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Brakujące podsumowanie aktualizacji

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
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

#### <a name="single-azure-vm-assessment-queries-linux"></a>Pojedyncze zapytania oceny maszyny wirtualnej platformy Azure (Linux)

W przypadku niektórych dystrybucje systemu Linux występuje niezgodność [endian](https://en.wikipedia.org/wiki/Endianness) z wartością VMUUID, która pochodzi z Azure Resource Manager i co jest przechowywane w dziennikach Azure monitor. Poniższe zapytanie sprawdza zgodność z dowolną przyciągiem. Zastąp wartości VMUUID wartościami formatu big-endian i little-endian identyfikatora GUID, aby prawidłowo zwrócić wyniki. Możesz znaleźć VMUUID, które powinny być używane, uruchamiając następujące zapytanie w Azure Monitor dziennikach:`Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Brakujące podsumowanie aktualizacji

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

#### <a name="multi-vm-assessment-queries"></a>Zapytania oceny z obsługą wiele maszyn wirtualnych

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

##### <a name="missing-updates-summary"></a>Brakujące podsumowanie aktualizacji

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
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
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

## <a name="using-dynamic-groups"></a>Korzystanie z grup dynamicznych

Update Management umożliwia kierowanie do dynamicznych grup platformy Azure lub maszyn wirtualnych innych niż platformy Azure na potrzeby wdrożeń aktualizacji. Te grupy są oceniane w czasie wdrażania, aby nie trzeba było edytować wdrożenia w celu dodania maszyn.

> [!NOTE]
> Podczas tworzenia wdrożenia aktualizacji wymagane są odpowiednie uprawnienia. Aby dowiedzieć się więcej, zobacz [Install Updates](#install-updates).

### <a name="azure-machines"></a>Maszyny platformy Azure

Te grupy są definiowane przez zapytanie, po rozpoczęciu wdrożenia aktualizacji są oceniane elementy członkowskie tej grupy. Grupy dynamiczne nie działają z klasycznymi maszynami wirtualnymi. Podczas definiowania zapytania następujące elementy mogą być używane razem do wypełniania grupy dynamicznej

* Subscription
* Grupy zasobów
* Lokalizacje
* `Tags`

![Wybieranie grup](./media/automation-update-management/select-groups.png)

Aby wyświetlić podgląd wyników grupy dynamicznej, kliknij przycisk **Podgląd** . Ta wersja zapoznawcza pokazuje członkostwo w grupach, w tym przykładzie wyszukiwanie maszyn z **rolą** znacznika jest równe **BackendServer**. Jeśli więcej maszyn ma ten tag, zostanie dodany do wszystkich przyszłych wdrożeń względem tej grupy.

![Podgląd grup](./media/automation-update-management/preview-groups.png)

### <a name="non-azure-machines"></a>Maszyny spoza platformy Azure

W przypadku maszyn spoza platformy Azure zapisane wyszukiwania są określane również jako grupy komputerów, które są używane do tworzenia grupy dynamicznej. Aby dowiedzieć się, jak utworzyć zapisane wyszukiwanie, zobacz [Tworzenie grupy komputerów](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Po utworzeniu grupy możesz wybrać ją z listy zapisanych wyszukiwań. Kliknij pozycję **Podgląd** , aby wyświetlić w tym momencie komputery z zapisanym wyszukiwaniem.

![Wybieranie grup](./media/automation-update-management/select-groups-2.png)

## <a name="integrate-with-system-center-configuration-manager"></a>Integracja z programem System Center Configuration Manager

Klienci, którzy zainwestowali w System Center Configuration Manager do zarządzania komputerami, serwerami i urządzeniami przenośnymi, również opierają się na sile i dojrzałości Configuration Manager, aby ułatwić im zarządzanie aktualizacjami oprogramowania. Configuration Manager jest częścią cyklu zarządzania aktualizacjami oprogramowania (SUM).

Aby dowiedzieć się, jak zintegrować rozwiązanie do zarządzania z System Center Configuration Manager, zobacz [integrowanie System Center Configuration Manager z Update Management](oms-solution-updatemgmt-sccmintegration.md).

## <a name="inclusion-behavior"></a>Zachowanie podczas włączania

Dołączenie aktualizacji umożliwia określenie określonych aktualizacji do zastosowania. Instalowane są poprawki lub pakiety. Gdy są uwzględniane poprawki lub pakiety, a także wybrana jest klasyfikacja, instalowane są zarówno elementy dołączone, jak i elementy spełniające klasyfikację.

Ważne jest, aby wiedzieć, że wykluczenia zastępują dołączenia. Na przykład, jeśli zdefiniujesz regułę wykluczania `*`dla programu, nie zostaną zainstalowane żadne poprawki ani pakiety, ponieważ są one wykluczone. Wykluczone poprawki nadal są wyświetlane jako brakujące na komputerze. W przypadku maszyn z systemem Linux, jeśli pakiet jest dołączony, ale jego pakiet jest zależny, który został wykluczony, pakiet nie jest zainstalowany.

## <a name="patch-linux-machines"></a>Poprawianie maszyn z systemem Linux

W poniższych sekcjach wyjaśniono potencjalne problemy z poprawkami systemu Linux.

### <a name="unexpected-os-level-upgrades"></a>Nieoczekiwane uaktualnienia na poziomie systemu operacyjnego

W przypadku niektórych wariantów systemu Linux, takich jak Red Hat Enterprise Linux, uaktualnienia na poziomie OS mogą odbywać się za pośrednictwem pakietów. Może to prowadzić do Update Management uruchamiania w przypadku zmiany numeru wersji systemu operacyjnego. Ponieważ Update Management używa tych samych metod do aktualizacji pakietów, których administrator może używać lokalnie na komputerze z systemem Linux, to zachowanie jest zamierzone.

Aby uniknąć aktualizacji wersji systemu operacyjnego za pomocą Update Management uruchamiania, użyj  funkcji wykluczania.

W Red Hat Enterprise Linux nazwa pakietu do wykluczenia to RedHat-Release-Server. x86_64.

![Pakiety do wykluczenia dla systemu Linux](./media/automation-update-management/linuxpatches.png)

### <a name="critical--security-patches-arent-applied"></a>Poprawki krytyczne/zabezpieczeń nie są stosowane

Podczas wdrażania aktualizacji na komputerze z systemem Linux można wybrać klasyfikacje aktualizacji. Powoduje to filtrowanie aktualizacji, które są stosowane do komputera spełniającego określone kryteria. Ten filtr jest stosowany lokalnie na komputerze podczas wdrażania aktualizacji.

Ponieważ Update Management wykonuje wzbogacanie aktualizacji w chmurze, niektóre aktualizacje mogą być oflagowane w Update Management jako mające wpływ na bezpieczeństwo, nawet jeśli maszyna lokalna nie ma tych informacji. W związku z tym w przypadku zastosowania aktualizacji krytycznych do maszyny z systemem Linux mogą wystąpić aktualizacje, które nie są oznaczone jako mające wpływ na zabezpieczenia na tym komputerze, a aktualizacje nie są stosowane.

Jednak Update Management może nadal zgłaszać, że maszyna jest niezgodna, ponieważ zawiera dodatkowe informacje o odpowiedniej aktualizacji.

Wdrażanie aktualizacji przy użyciu klasyfikacji aktualizacji nie działa na CentOS z pola. Aby poprawnie wdrożyć aktualizacje dla programu CentOS, wybierz pozycję Wszystkie klasyfikacje, aby upewnić się, że aktualizacje są stosowane. W przypadku opcji SUSE wybierz *tylko* opcję inne aktualizacje, ponieważ klasyfikacja może spowodować zainstalowanie niektórych aktualizacji zabezpieczeń, jeśli najpierw wymagane są aktualizacje zabezpieczeń powiązane z użyciu narzędzia zypper (Menedżer pakietów) lub jej zależności. To zachowanie jest ograniczeniem użyciu narzędzia zypper. W niektórych przypadkach może być konieczne ponowne uruchomienie wdrożenia aktualizacji. Aby sprawdzić, sprawdź dziennik aktualizacji.

## <a name="remove-a-vm-from-update-management"></a>Usuwanie maszyny wirtualnej z Update Management

Aby usunąć maszynę wirtualną z Update Management:

* W obszarze roboczym Log Analytics Usuń maszynę wirtualną z zapisanego wyszukiwania konfiguracji `MicrosoftDefaultScopeConfig-Updates`zakresu. Zapisane wyszukiwania można znaleźć **ogólnie** w obszarze roboczym.
* Usuń [program Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) lub [agenta log Analytics dla systemu Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Następne kroki

Przejdź do samouczka, aby dowiedzieć się, jak zarządzać aktualizacjami dla maszyn wirtualnych z systemem Windows.

> [!div class="nextstepaction"]
> [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych z systemem Windows Azure](automation-tutorial-update-management.md)

* Użyj wyszukiwania w dzienniku [Azure monitor](../log-analytics/log-analytics-log-searches.md) , aby wyświetlić szczegółowe dane aktualizacji.
* [Utwórz alerty](automation-tutorial-update-management.md#configure-alerts) dla stanu wdrożenia aktualizacji.

* Aby dowiedzieć się, jak korzystać z Update Management za pomocą interfejsu API REST, zobacz [konfiguracje aktualizacji oprogramowania](/rest/api/automation/softwareupdateconfigurations)
* Aby dowiedzieć się, jak rozwiązywać problemy z Update Management, zobacz [Rozwiązywanie problemów Update Management](troubleshoot/update-management.md)
