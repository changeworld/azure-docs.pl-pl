---
title: Update Management rozwiązanie na platformie Azure
description: W tym artykule opisano sposób korzystania z rozwiązania Update Management platformy Azure do zarządzania aktualizacjami komputerów z systemami Windows i Linux.
services: automation
ms.subservice: update-management
ms.date: 01/21/2020
ms.topic: conceptual
ms.openlocfilehash: e7a86f3a709566bcf18241ce3c329c0355be2743
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77539556"
---
# <a name="update-management-solution-in-azure"></a>Update Management rozwiązanie na platformie Azure

Update Management rozwiązanie w Azure Automation służy do zarządzania aktualizacjami systemu operacyjnego na komputerach z systemami Windows i Linux na platformie Azure, w środowiskach lokalnych i innych dostawców chmury. Umożliwia ono szybką ocenę stanu dostępnych aktualizacji na wszystkich komputerach agentów oraz zarządzanie procesem instalacji wymaganych aktualizacji serwerów.

Update Management dla maszyn wirtualnych (VM) można włączyć bezpośrednio z poziomu konta Azure Automation. Aby dowiedzieć się, jak to zrobić, zobacz [Zarządzanie aktualizacjami dla wielu maszyn wirtualnych](manage-update-multi.md). Możesz również włączyć Update Management dla maszyny wirtualnej na stronie maszyny wirtualnej w Azure Portal. Ten scenariusz jest dostępny dla maszyn wirtualnych z systemami [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) i [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) .

> [!NOTE]
> Rozwiązanie Update Management wymaga połączenia obszaru roboczego Log Analytics z kontem usługi Automation. Aby uzyskać ostateczną listę obsługiwanych regionów, zobacz [mapowania obszaru roboczego platformy Azure](./how-to/region-mappings.md). Mapowania regionów nie mają wpływu na możliwość zarządzania maszynami wirtualnymi w innym regionie niż konto usługi Automation.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="solution-overview"></a>Omówienie rozwiązania

Komputery zarządzane przez Update Management używają następujących konfiguracji do przeprowadzania oceny i aktualizacji wdrożeń:

* Program Microsoft Monitoring Agent (MMA) dla systemu Windows lub Linux
* Platforma PowerShell Desired State Configuration (DSC) dla systemu Linux
* Hybrydowy proces roboczy elementu runbook usługi Automation
* Usługa Microsoft Update lub Windows Server Update Services (WSUS) dla komputerów z systemem Windows

Na poniższym diagramie przedstawiono sposób, w jaki rozwiązanie ocenia i stosuje aktualizacje zabezpieczeń do wszystkich połączonych komputerów z systemem Windows Server i Linux w obszarze roboczym:

![Przepływ procesu Update Management](./media/automation-update-management/update-mgmt-updateworkflow.png)

Update Management może służyć do natywnego dołączania maszyn w ramach wielu subskrypcji w ramach tej samej dzierżawy.

Po wydaniu pakietu trwa od 2 do 3 godzin, aby poprawka była wyświetlana dla maszyn z systemem Linux na potrzeby oceny. W przypadku maszyn z systemem Windows trwa od 12 do 15 godzin, aby poprawka była wyświetlana na potrzeby oceny po jej udostępnieniu.

Gdy komputer ukończy skanowanie pod kątem zgodności aktualizacji, Agent przekaże informacje zbiorczo do dzienników Azure Monitor. Na komputerze z systemem Windows skanowanie zgodności jest domyślnie uruchamiane co 12 godzin.

Oprócz harmonogramu skanowania skanowanie pod kątem zgodności z aktualizacjami jest inicjowane w ciągu 15 minut od ponownego uruchomienia MMA, przed instalacją aktualizacji i po zainstalowaniu aktualizacji.

W przypadku komputera z systemem Linux skanowanie zgodności jest wykonywane co godzinę domyślnie. Jeśli Agent MMA zostanie ponownie uruchomiony, skanowanie zgodności zostanie zainicjowane w ciągu 15 minut.

Rozwiązanie raportuje, jak aktualna jest Konfiguracja komputera, na podstawie którego źródła skonfigurowano do synchronizacji. Jeśli komputer z systemem Windows jest skonfigurowany do raportowania do programu WSUS, w zależności od tego, kiedy program WSUS został ostatnio zsynchronizowany z Microsoft Update, wyniki mogą się różnić od tego, co Microsoft Update. Takie zachowanie jest takie samo dla komputerów z systemem Linux, które są skonfigurowane do raportowania do lokalnego repozytorium, a nie do repozytorium publicznego.

> [!NOTE]
> Aby poprawnie zgłosić usługę, Update Management wymaga włączenia określonych adresów URL i portów. Aby dowiedzieć się więcej na temat tych wymagań, zobacz [Planowanie sieci dla hybrydowych procesów roboczych](automation-hybrid-runbook-worker.md#network-planning).

Aktualizacje oprogramowania można wdrożyć i zainstalować na komputerach, które ich wymagają, tworząc zaplanowane wdrożenie. Aktualizacje sklasyfikowane jako *opcjonalne* nie są uwzględnione w zakresie wdrożenia dla komputerów z systemem Windows. Zakres wdrożenia obejmuje tylko wymagane aktualizacje.

Zaplanowane wdrożenie definiuje, które komputery docelowe otrzymują odpowiednie aktualizacje. Robi to w sposób jawny określający pewne komputery lub wybierając [grupę komputerów](../azure-monitor/platform/computer-groups.md) opartą na przeszukiwaniu dzienników określonego zestawu komputerów (lub w [zapytaniu platformy Azure](automation-update-management-query-logs.md) , które dynamicznie wybiera maszyny wirtualne platformy Azure na podstawie określonych kryteriów). Te grupy różnią się od [konfiguracji zakresu](../azure-monitor/insights/solution-targeting.md), która jest używana tylko do określania, które maszyny otrzymują pakiety administracyjne, które umożliwiają rozwiązanie.

Należy również określić harmonogram zatwierdzania i ustawiania okresu, w którym można zainstalować aktualizacje. Ten okres jest nazywany oknem obsługi. 20-minutowy zakres okna obsługi jest zarezerwowany dla ponownych uruchomień, przy założeniu, że jest to wymagane i wybrano odpowiednią opcję ponownego uruchomienia. Jeśli stosowanie poprawek trwa dłużej niż oczekiwano, a w oknie obsługi jest mniej niż 20 minut, ponowne uruchomienie nie zostanie przeprowadzone.

Aktualizacje są instalowane przez elementy runbook w usłudze Azure Automation. Nie można wyświetlić tych elementów Runbook i nie wymagają one żadnej konfiguracji. Po utworzeniu wdrożenia aktualizacji tworzy harmonogram, który uruchamia główny element Runbook aktualizacji w określonym czasie dla dołączonych komputerów. Główny element Runbook uruchamia podrzędny element Runbook na każdym agencie, aby zainstalować wymagane aktualizacje.

W dniu i czasie określonym we wdrożeniu aktualizacji komputery docelowe wykonują wdrożenie równolegle. Przed rozpoczęciem instalacji zostanie uruchomione skanowanie w celu sprawdzenia, czy aktualizacje są nadal wymagane. W przypadku komputerów klienckich programu WSUS, jeśli aktualizacje nie są zatwierdzone w programie WSUS, wdrożenie aktualizacji nie powiedzie się.

Posiadanie maszyny zarejestrowanej dla Update Management w więcej niż jednym Log Analytics obszarze roboczym (wieloadresowości) nie jest obsługiwane.

## <a name="clients"></a>Klienci

### <a name="supported-client-types"></a>Typy obsługiwanych klientów

W poniższej tabeli wymieniono systemy operacyjne obsługiwane w przypadku ocen aktualizacji. Stosowanie poprawek wymaga hybrydowego procesu roboczego elementu Runbook. Aby uzyskać informacje na temat wymagań hybrydowych procesów roboczych elementu Runbook, zobacz Przewodniki instalacji dotyczące instalowania [hybrydowego procesu roboczego elementu Runbook systemu Windows](automation-windows-hrw-install.md) i [hybrydowego procesu roboczego elementu Runbook](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)w systemie Linux

|System operacyjny  |Uwagi  |
|---------|---------|
|Windows Server 2019 (Datacenter/Datacenter/standard)<br><br>Windows Server 2016 (Datacenter/Datacenter/standard)<br><br>Windows Server 2012 R2 (Datacenter/standard)<br><br>Windows Server 2012 || 
|Windows Server 2008 R2 (wersja RTM i SP1 standard)| Update Management obsługuje tylko wykonywanie ocen dla tego systemu operacyjnego, stosowanie poprawek nie jest obsługiwane, ponieważ [hybrydowy proces roboczy elementu Runbook](automation-windows-hrw-install.md) nie jest obsługiwany w przypadku systemu Windows Server 2008 R2. |
|CentOS 6 (x86/x64) i 7 (x64)      | Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji. Stosowanie poprawek opartych na klasyfikacji wymaga, aby `yum` zwracały dane zabezpieczeń, które nie są dostępne w jego wersjach RTM. Aby uzyskać więcej informacji na temat stosowania poprawek opartych na klasyfikacji na CentOS, zobacz [Aktualizacja klasyfikacji w systemie Linux](automation-view-update-assessments.md#linux-2).          |
|Red Hat Enterprise 6 (x86/x64) i 7 (x64)     | Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji.        |
|SUSE Linux Enterprise Server 11 (x86/x64) i 12 (x64)     | Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji.        |
|Ubuntu 14,04 LTS, 16,04 LTS i 18,04 (x86/x64)      |Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji.         |

> [!NOTE]
> Zestawy skalowania maszyn wirtualnych platformy Azure mogą być zarządzane za pomocą Update Management. Update Management działa na samych wystąpieniach, a nie na obrazie podstawowym. Należy zaplanować aktualizacje w sposób przyrostowy, aby nie wszystkie wystąpienia maszyn wirtualnych były aktualizowane jednocześnie.
> Węzły dla zestawów skalowania maszyn wirtualnych można dodać, wykonując czynności opisane w sekcji [dołączanie maszyny spoza platformy Azure](automation-tutorial-installed-software.md#onboard-a-non-azure-machine).

### <a name="unsupported-client-types"></a>Typy klientów nieobsługiwanych

W poniższej tabeli wymieniono nieobsługiwane systemy operacyjne:

|System operacyjny  |Uwagi  |
|---------|---------|
|Klient systemu Windows     | Systemy operacyjne klienta (takich jak Windows 7 i Windows 10) nie są obsługiwane.        |
|Windows Server 2016 Nano Server     | Nieobsługiwane.       |
|Węzły usługi Azure Kubernetes | Nieobsługiwane. Użyj procesu poprawek opisanego w temacie [stosowanie aktualizacji zabezpieczeń i jądra do węzłów systemu Linux w usłudze Azure Kubernetes Service (AKS)](../aks/node-updates-kured.md)|

### <a name="client-requirements"></a>Wymagania dotyczące klienta

Poniższe informacje opisują wymagania klienta specyficzne dla systemu operacyjnego. Aby uzyskać dodatkowe wskazówki, zobacz [Planowanie sieci](#ports).

#### <a name="windows"></a>System Windows

Agenci systemu Windows muszą być skonfigurowani do komunikowania się z serwerem WSUS lub muszą mieć dostęp do Microsoft Update.

Możesz użyć Update Management z Configuration Manager. Aby dowiedzieć się więcej na temat scenariuszy integracji, zobacz [integracja Configuration Manager z Update Management](oms-solution-updatemgmt-sccmintegration.md#configuration). Wymagany jest [Agent systemu Windows](../azure-monitor/platform/agent-windows.md) . Agent zostanie zainstalowany automatycznie, jeśli dołączysz maszynę wirtualną platformy Azure.

Domyślnie maszyny wirtualne z systemem Windows wdrożone z portalu Azure Marketplace są ustawione tak, aby otrzymywać aktualizacje automatyczne z usługi Windows Update. To zachowanie nie zmienia się po dodaniu tego rozwiązania lub dodaniu maszyn wirtualnych z systemem Windows do obszaru roboczego. Jeśli aktualizacje nie są aktywnie zarządzane przy użyciu tego rozwiązania, mają zastosowanie domyślne zachowanie (aby automatycznie zastosować aktualizacje).

> [!NOTE]
> Użytkownik może modyfikować zasady grupy, tak aby rozruch maszynowy mógł zostać wykonany tylko przez użytkownika, a nie przez system. Maszyny zarządzane mogą zostać zablokowane, jeśli Update Management nie ma uprawnień do ponownego uruchomienia maszyny bez ręcznej interakcji z użytkownikiem.
>
> Aby uzyskać więcej informacji, zobacz [Configure zasady grupy Settings for Automatic Updates](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

W przypadku systemu Linux komputer musi mieć dostęp do repozytorium aktualizacji. Repozytorium aktualizacji może być prywatne lub publiczne. Do współdziałania z Update Managementami wymagany jest protokół TLS 1,1 lub TLS 1,2. Agent Log Analytics dla systemu Linux skonfigurowany do raportowania do więcej niż jednego obszaru roboczego Log Analytics nie jest obsługiwany w tym rozwiązaniu. Na komputerze musi być zainstalowany język Python 2. x.

Aby uzyskać informacje na temat sposobu instalowania agenta Log Analytics dla systemu Linux i pobierania najnowszej wersji, zobacz [log Analytics Agent dla systemu Linux](https://github.com/microsoft/oms-agent-for-linux). Aby uzyskać informacje na temat sposobu instalowania agenta Log Analytics dla systemu Windows, zobacz [łączenie komputerów z systemem Windows w Azure monitor](../log-analytics/log-analytics-windows-agent.md).

Maszyny wirtualne, które zostały utworzone na podstawie obrazów na żądanie Red Hat Enterprise Linux (RHEL), które są dostępne w portalu Azure Marketplace, są zarejestrowane w celu uzyskania dostępu do [infrastruktury aktualizacji firmy Red Hat (RHUI)](../virtual-machines/workloads/redhat/redhat-rhui.md) wdrożonej na platformie Azure. Wszystkie inne dystrybucje systemu Linux muszą zostać zaktualizowane z repozytorium plików online dystrybucji przy użyciu obsługiwanych metod dystrybucji.

## <a name="permissions"></a>Uprawnienia

Do tworzenia wdrożeń aktualizacji i zarządzania nimi wymagane są określone uprawnienia. Aby dowiedzieć się więcej o tych uprawnieniach, zobacz [dostęp oparty na rolach — Update Management](automation-role-based-access-control.md#update-management).

## <a name="solution-components"></a>Składniki rozwiązania

Rozwiązanie składa się z następujących zasobów. Zasoby są dodawane do konta usługi Automation. Są to bezpośrednio połączeni agenci lub w połączonej Operations Manager grupie zarządzania.

### <a name="hybrid-worker-groups"></a>Grupy hybrydowych procesów roboczych

Po włączeniu tego rozwiązania każdy komputer z systemem Windows, który jest bezpośrednio połączony z obszarem roboczym Log Analytics, zostanie automatycznie skonfigurowany jako hybrydowy proces roboczy elementu Runbook w celu obsługi elementów Runbook uwzględnionych w tym rozwiązaniu.

Każdy komputer z systemem Windows, który jest zarządzany przez rozwiązanie, znajduje się w okienku **grupy hybrydowych procesów roboczych** jako **Grupa hybrydowych procesów roboczych systemu** dla konta usługi Automation. Rozwiązania używają konwencji nazewnictwa *FQDN_GUID nazwy hosta* . Nie można kierować tymi grupami do elementów Runbook na Twoim koncie. Jeśli spróbujesz, próba zakończy się niepowodzeniem. Te grupy mają na celu obsługę tylko rozwiązania do zarządzania.

Komputery z systemem Windows można dodać do grupy hybrydowych procesów roboczych elementu Runbook na koncie usługi Automation w celu obsługi elementów Runbook usługi Automation, jeśli używasz tego samego konta zarówno dla tego rozwiązania, jak i dla członkostwa w grupie hybrydowych procesów roboczych elementu Runbook. Ta funkcja została dodana w wersji 7.2.12024.0 hybrydowego procesu roboczego elementu Runbook.

### <a name="management-packs"></a>Pakiety administracyjne

Jeśli grupa zarządzania System Center Operations Manager jest połączona z obszarem roboczym Log Analytics, następujące pakiety administracyjne są instalowane w Operations Manager. Te pakiety administracyjne są również instalowane na komputerach z systemem Windows bezpośrednio połączonym po dodaniu rozwiązania. Nie trzeba konfigurować tych pakietów administracyjnych ani nimi zarządzać.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Pakiet administracyjny wdrożenia aktualizacji

> [!NOTE]
> Załóżmy, że masz grupę zarządzania Operations Manager 1807 lub 2019 z agentami skonfigurowanymi na poziomie grupy zarządzania, aby skojarzyć je z obszarem roboczym. Bieżące obejście, aby je wyświetlić, polega na przesłonięciu **IsAutoRegistrationEnabled** na **true** w regule **Microsoft. IntelligencePacks. AzureAutomation. HybridAgent. init** .

Aby uzyskać więcej informacji na temat aktualizacji pakietów zarządzania rozwiązaniami, zobacz [Connect Operations Manager to Azure monitor Logs](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> W przypadku systemów z agentem programu Operations Manager: aby Agent był w pełni zarządzany przez Update Management, Agent należy zaktualizować do MMA. Aby dowiedzieć się, jak zaktualizować agenta, zobacz [jak uaktualnić agenta Operations Manager](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents). W środowiskach, w których jest używana Operations Manager, musi być uruchomiony program System Center Operations Manager 2012 R2 UR 14 lub nowszy.

## <a name="data-collection"></a>Zbieranie danych

### <a name="supported-agents"></a>Obsługiwani agenci

W poniższej tabeli opisano połączone źródła obsługiwane przez to rozwiązanie:

| Połączone źródło | Obsługiwane | Opis |
| --- | --- | --- |
| Agenci dla systemu Windows |Yes |Rozwiązanie zbiera informacje o aktualizacjach systemu z agentów systemu Windows, a następnie inicjuje instalację wymaganych aktualizacji. |
| Agenci dla systemu Linux |Yes |Rozwiązanie zbiera informacje o aktualizacjach systemu z agentów z systemem Linux, a następnie inicjuje instalację wymaganych aktualizacji w obsługiwanych dystrybucjach. |
| Grupa zarządzania programu Operations Manager |Yes |Rozwiązanie zbiera informacje o aktualizacjach systemu z agentów w połączonej grupie zarządzania.<br/><br/>Bezpośrednie połączenie z agentem Operations Manager do Azure Monitor dzienników nie jest wymagane. Dane są przekazywane z grupy zarządzania do obszaru roboczego usługi Log Analytics. |

### <a name="collection-frequency"></a>Częstotliwość zbierania

Skanowanie jest wykonywane dwa razy dziennie dla każdego zarządzanego komputera z systemem Windows. Co 15 minut interfejs API systemu Windows jest wywoływany, aby wykonać zapytanie o czas ostatniej aktualizacji w celu ustalenia, czy stan został zmieniony. W przypadku zmiany stanu zostanie zainicjowane skanowanie zgodności.

Skanowanie jest wykonywane co godzinę dla każdego zarządzanego komputera z systemem Linux.

Wyświetlenie zaktualizowanych danych z zarządzanych komputerów przez pulpit nawigacyjny może potrwać od 30 minut do 6 godzin.

Średnie użycie danych przez Azure Monitor dzienników dla maszyny używającej Update Management wynosi około 25 megabajtów (MB) miesięcznie. Ta wartość jest tylko przybliżeniem i może ulec zmianie, w zależności od środowiska. Zalecamy monitorowanie środowiska, aby śledzić dokładne użycie.

## <a name="ports"></a>Planowanie sieci

Poniższe adresy są wymagane dla Update Management. Komunikacja z tymi adresami odbywa się za pośrednictwem portu 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*. azure-automation.us|

W przypadku maszyn z systemem Windows należy również zezwolić na ruch do wszystkich punktów końcowych wymaganych przez Windows Update. Zaktualizowaną listę wymaganych punktów końcowych można znaleźć w przypadku [problemów związanych z protokołem HTTP/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Jeśli masz lokalny [serwer Windows Update](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment), musisz również zezwolić na ruch do serwera określonego w [kluczu WSUS](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

W przypadku maszyn z systemem Red Hat Linux zapoznaj [się z tematem adresy IP dla serwerów RHUI Content Delivery](../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) for Required Endpoints. W przypadku innych dystrybucji systemu Linux zapoznaj się z dokumentacją dostawcy.

Aby uzyskać więcej informacji na temat portów wymaganych przez hybrydowy proces roboczy elementu Runbook, zobacz [porty ról hybrydowych procesów roboczych](automation-hybrid-runbook-worker.md#hybrid-worker-role).

Zalecamy korzystanie z adresów wymienionych podczas definiowania wyjątków. W przypadku adresów IP można pobrać [Microsoft Azure zakresy adresów IP centrum](https://www.microsoft.com/download/details.aspx?id=41653)danych. Ten plik jest aktualizowany co tydzień i odzwierciedla aktualnie wdrożone zakresy oraz wszystkie nadchodzące zmiany w zakresach adresów IP.

Postępuj zgodnie z instrukcjami w temacie [Connect Computers bez dostępu do Internetu](../azure-monitor/platform/gateway.md) , aby skonfigurować maszyny, które nie mają dostępu do Internetu.

## <a name="view-update-assessments"></a>Wyświetlanie ocen aktualizacji

Na koncie usługi Automation wybierz pozycję **Update Management** , aby wyświetlić stan maszyn.

Ten widok zawiera informacje dotyczące maszyn, brakujących aktualizacji, wdrożeń aktualizacji i zaplanowanych wdrożeń aktualizacji. W kolumnie **zgodność** można zobaczyć czas ostatniego oceny maszyny. W kolumnie **Aktualizowanie gotowości agenta** można sprawdzić kondycję agenta aktualizacji. Jeśli wystąpił problem, wybierz link, aby przejść do dokumentacji dotyczącej rozwiązywania problemów, która może pomóc w rozwiązaniu problemu.

Aby uruchomić wyszukiwanie w dzienniku, które zwraca informacje o komputerze, aktualizacji lub wdrożeniu, wybierz odpowiedni element na liście. Zostanie otwarte okienko **przeszukiwania dzienników** z zapytaniem dotyczącym wybranego elementu:

![Update Management widoku domyślnego](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Wyświetl brakujące aktualizacje

Wybierz pozycję **brakujące aktualizacje** , aby wyświetlić listę aktualizacji, których brakuje na Twoich komputerach. Każda aktualizacja jest wyświetlana i można ją wybrać. Informacje o liczbie maszyn, które wymagają aktualizacji, systemu operacyjnego i linku, aby uzyskać więcej informacji. W okienku **przeszukiwania dzienników** są wyświetlane szczegółowe informacje o aktualizacjach.

![Brakujące aktualizacje](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Klasyfikacje aktualizacji

W poniższej tabeli wymieniono klasyfikacje aktualizacji w Update Management z definicją dla każdej klasyfikacji.

### <a name="windows"></a>System Windows

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

### <a name="linux-2"></a>System

|Klasyfikacja  |Opis  |
|---------|---------|
|Aktualizacje krytyczne i zabezpieczeń     | Aktualizacje dotyczące konkretnego problemu lub problemu związanego z zabezpieczeniami.         |
|Inne aktualizacje     | Wszystkie inne aktualizacje, których charakter nie ma znaczenia ani aktualizacje zabezpieczeń.        |

W przypadku systemu Linux Update Management może rozróżnić aktualizacje krytyczne i aktualizacje zabezpieczeń w chmurze, a następnie wyświetlać dane oceny z powodu wzbogacania danych w chmurze. W przypadku stosowania poprawek Update Management opiera się na danych klasyfikacji dostępnych na komputerze. W przeciwieństwie do innych dystrybucji, CentOS nie ma informacji dostępnych w wersji RTM. Jeśli masz maszyny CentOS skonfigurowane do zwracania danych zabezpieczeń dla poniższego polecenia, Update Management może zostać poprawione na podstawie klasyfikacji.

```bash
sudo yum -q --security check-update
```

Obecnie nie jest obsługiwana metoda umożliwiająca natywną klasyfikację — dostępność danych w systemie CentOS. W tej chwili tylko Najlepsza pomoc techniczna jest świadczona klientom, którzy mogli ją samodzielnie włączyć. 

Aby sklasyfikować aktualizacje w systemie Red Hat Enterprise w wersji 6, należy zainstalować wtyczkę yum-Security. W Red Hat Enterprise Linux 7 wtyczka jest już częścią yum, nie ma potrzeby instalowania żadnych elementów. Aby uzyskać więcej informacji, zobacz następujący [artykuł merytoryczny](https://access.redhat.com/solutions/10021)firmy Red Hat.

## <a name="integrate-with-configuration-manager"></a>Integracja z programem Configuration Manager

Klienci, którzy zainwestowali w Configuration Manager Microsoft Endpoint Protection w celu zarządzania komputerami, serwerami i urządzeniami przenośnymi, również opierają się na sile i dojrzałości Configuration Manager, aby ułatwić im zarządzanie aktualizacjami oprogramowania. Configuration Manager jest częścią cyklu zarządzania aktualizacjami oprogramowania (SUM).

Aby dowiedzieć się, jak zintegrować rozwiązanie do zarządzania z Configuration Manager, zobacz [integrowanie Configuration Manager z Update Management](oms-solution-updatemgmt-sccmintegration.md).

### <a name="third-party-patches-on-windows"></a>Poprawki innych firm w systemie Windows

Update Management opiera się na lokalnie skonfigurowanym repozytorium aktualizacji w celu zastosowania poprawek obsługiwanych systemów Windows. Jest to program WSUS lub Windows Update. Narzędzia, takie jak [System Center Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/updates-publisher) (aktualizacje wydawcy), umożliwiają publikowanie niestandardowych aktualizacji w usługach WSUS. Ten scenariusz umożliwia Update Management poprawek maszyn, które używają Configuration Manager jako repozytorium aktualizacji z oprogramowaniem innych firm. Aby dowiedzieć się, jak skonfigurować program Updates Publisher, zobacz [Install Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/install-updates-publisher).

## <a name="patch-linux-machines"></a>Poprawianie maszyn z systemem Linux

W poniższych sekcjach wyjaśniono potencjalne problemy związane z poprawką systemu Linux dystrybucje.

### <a name="unexpected-os-level-upgrades"></a>Nieoczekiwane uaktualnienia na poziomie systemu operacyjnego

W przypadku niektórych odmian systemu Linux, takich jak Red Hat Enterprise Linux, uaktualnienia na poziomie systemu operacyjnego mogą odbywać się za poorednictwem pakietów. Może to prowadzić do Update Management uruchamiania w przypadku zmiany numeru wersji systemu operacyjnego. Ponieważ Update Management używa tych samych metod do aktualizacji pakietów, których administrator może używać lokalnie na komputerze z systemem Linux, to zachowanie jest zamierzone.

Aby uniknąć aktualizacji wersji systemu operacyjnego za pomocą Update Management uruchamiania, użyj funkcji **wykluczania** .

W Red Hat Enterprise Linux nazwa pakietu do wykluczenia to RedHat-Release-Server. x86_64.

![Pakiety do wykluczenia dla systemu Linux](./media/automation-update-management/linuxpatches.png)

### <a name="criticalsecurity-patches-arent-applied"></a>Poprawki krytyczne/zabezpieczeń nie są stosowane

Podczas wdrażania aktualizacji na komputerze z systemem Linux można wybrać klasyfikacje aktualizacji. Ta opcja umożliwia filtrowanie aktualizacji, które są stosowane do komputera spełniającego określone kryteria. Ten filtr jest stosowany lokalnie na komputerze podczas wdrażania aktualizacji.

Ponieważ Update Management wykonuje wzbogacanie aktualizacji w chmurze, niektóre aktualizacje mogą być oflagowane w Update Management jako mające wpływ na bezpieczeństwo, nawet jeśli maszyna lokalna nie ma tych informacji. W związku z tym w przypadku zastosowania aktualizacji krytycznych do maszyny z systemem Linux mogą wystąpić aktualizacje, które nie są oznaczone jako mające wpływ na zabezpieczenia na tym komputerze i dlatego aktualizacje nie są stosowane. Jednak Update Management może nadal zgłosić ten komputer jako niezgodny, ponieważ zawiera on dodatkowe informacje na temat odpowiedniej aktualizacji.

Wdrażanie aktualizacji według klasyfikacji aktualizacji nie działa w wersjach RTM CentOS. Aby poprawnie wdrożyć aktualizacje dla programu CentOS, wybierz pozycję Wszystkie klasyfikacje, aby upewnić się, że aktualizacje są stosowane. W *przypadku opcji SUSE* wybranie **innych aktualizacji** jako klasyfikacji może spowodować, że niektóre aktualizacje zabezpieczeń również zostaną zainstalowane, jeśli najpierw wymagane są aktualizacje zabezpieczeń powiązane z użyciu narzędzia zypper (Menedżer pakietów) lub jej zależności. To zachowanie jest ograniczeniem użyciu narzędzia zypper. W niektórych przypadkach może być konieczne ponowne uruchomienie wdrożenia aktualizacji. Aby sprawdzić, sprawdź dziennik aktualizacji.

### <a name="multi-tenant"></a>Wdrożenia aktualizacji między dzierżawcami

Jeśli masz maszyny w innym raportowaniu dzierżawy platformy Azure, aby Update Management, które należy zastosować, należy użyć następującego obejścia w celu zaplanowania ich planowania. Można użyć polecenia cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) z przełącznikiem `-ForUpdate`, aby utworzyć harmonogram, i użyć polecenia cmdlet [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) i przekazać maszyny w innej dzierżawie do parametru `-NonAzureComputer`. Poniższy przykład pokazuje, jak to zrobić:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="onboard"></a>Włącz Update Management

Aby rozpocząć stosowanie poprawek do systemów, należy włączyć rozwiązanie Update Management. Istnieje wiele sposobów dołączania maszyn do Update Management. Poniżej przedstawiono zalecane i obsługiwane sposoby dołączenia rozwiązania:

* [Z maszyny wirtualnej](automation-onboard-solutions-from-vm.md)
* [Przeglądanie wielu maszyn](automation-onboard-solutions-from-browse.md)
* [Na koncie usługi Automation](automation-onboard-solutions-from-automation-account.md)
* [Z elementem Runbook Azure Automation](automation-onboard-solutions.md)

## <a name="next-steps"></a>Następne kroki

Skorzystaj z następującego samouczka, aby dowiedzieć się, jak zarządzać aktualizacjami maszyn wirtualnych z systemem Windows:

> [!div class="nextstepaction"]
> [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych z systemem Windows Azure](automation-tutorial-update-management.md)

* Użyj wyszukiwania w dzienniku [Azure monitor](../log-analytics/log-analytics-log-searches.md) , aby wyświetlić szczegółowe dane aktualizacji.
* [Utwórz alerty](automation-tutorial-update-management.md#configure-alerts) dla stanu wdrożenia aktualizacji.

* Aby dowiedzieć się, jak korzystać z Update Management za pomocą interfejsu API REST, zobacz [konfiguracje aktualizacji oprogramowania](/rest/api/automation/softwareupdateconfigurations).
* Aby dowiedzieć się, jak rozwiązywać problemy z Update Management, zobacz [Rozwiązywanie problemów z Update Management](troubleshoot/update-management.md).
