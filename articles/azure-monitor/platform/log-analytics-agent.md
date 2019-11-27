---
title: Zbieranie danych dziennika przy użyciu agenta usługi Azure Log Analytics | Dokumentacja firmy Microsoft
description: Ten temat ułatwia zrozumienie, jak zbierać dane i monitorować komputery z hostowanych na platformie Azure, lokalnie lub w innym środowisku chmury, za pomocą usługi Log Analytics.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 11/21/2019
ms.openlocfilehash: 33ba07ac8d89546856666cc7ab94fae650020001
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74306523"
---
# <a name="collect-log-data-with-the-log-analytics-agent"></a>Zbieranie danych dziennika za pomocą agenta Log Analytics

Agent Log Analytics platformy Azure, wcześniej określany jako Microsoft Monitoring Agent (MMA) lub Agent usługi OMS Linux, został opracowany na potrzeby kompleksowego zarządzania na maszynach lokalnych, komputerach monitorowanych przez [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/)i maszynach wirtualnych w dowolnej chmurze. Agenci systemów Windows i Linux dołączają do Azure Monitor i przechowują zebrane dane dzienników z różnych źródeł w obszarze roboczym Log Analytics, a także do wszelkich unikatowych dzienników lub metryk zgodnie z definicją w rozwiązaniu monitorowania. 

Ten artykuł zawiera szczegółowe omówienie agenta systemu i wymagania dotyczące sieci i różne metody wdrażania.

## <a name="overview"></a>Omówienie

![Log Analytics agent komunikacji diagramu](./media/log-analytics-agent/log-analytics-agent-01.png)

Przed analizowaniem i działaniem zebranych danych należy najpierw zainstalować agentów i połączyć je ze wszystkimi maszynami, które mają wysyłać dane do usługi Azure Monitor. Można zainstalować agentów na maszynach wirtualnych platformy Azure przy użyciu rozszerzenia maszyny Wirtualnej analizy dzienników platformy Azure, Windows i Linux oraz maszyn w środowisku hybrydowym za pomocą Instalatora, wiersza polecenia lub przy użyciu Desired State Configuration (DSC) w usłudze Azure Automation. 

Agent dla systemów Linux i Windows komunikuje się z usługą Azure Monitor za pośrednictwem portu TCP 443, a jeśli komputer nawiązuje połączenie za pośrednictwem zapory lub serwera proxy w celu komunikowania się za pośrednictwem Internetu, przejrzyj poniższe wymagania, aby poznać konfigurację sieci Wymagane. Jeśli zasady zabezpieczeń IT nie zezwalają komputerom w sieci na łączenie się z Internetem, można skonfigurować [bramę log Analytics](gateway.md) , a następnie skonfigurować agenta do nawiązywania połączeń za pomocą bramy do Azure monitor dzienników. Agent może następnie odbierać informacje o konfiguracji i wysyłać zebrane dane w zależności od tego, jakie reguły zbierania danych i rozwiązania monitorowania, które zostały włączone w obszarze roboczym. 

Korzystając z Log Analytics agentów do zbierania danych, należy zrozumieć następujące kwestie w celu zaplanowania wdrożenia agenta:

* Aby zbierać dane z agentów systemu Windows, można [skonfigurować każdego agenta w celu raportowania do co najmniej jednego obszaru roboczego](agent-windows.md), nawet gdy jest on raportowany do System Center Operations Manager grupy zarządzania. Agent systemu Windows może zgłosić do czterech obszarów roboczych.
* Agent systemu Linux nie obsługuje wiele multihostingu i może być raportowany tylko do jednego obszaru roboczego.
* Agent systemu Windows obsługuje [Standard FIPS 140](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation), a agent Linux nie obsługuje go.  

Jeśli używasz System Center Operations Manager 2012 R2 lub nowszego:

* Każda Operations Manager grupy zarządzania może być [połączona tylko z jednym obszarem roboczym](om-agents.md).
* Komputery z systemem Linux, które są zgłaszane do grupy zarządzania, muszą być skonfigurowane do bezpośredniego raportowania do obszaru roboczego Log Analytics. Jeśli komputery z systemem Linux są już zgłaszane bezpośrednio do obszaru roboczego i chcesz monitorować je za pomocą Operations Manager, wykonaj następujące kroki, aby [zgłosić Operations Manager do grupy zarządzania](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group).
* Program Log Analytics Agent systemu Windows można zainstalować na komputerze z systemem Windows i mieć do niego raport Operations Manager zintegrowany z obszarem roboczym i innym obszarze roboczym.

Agent dla systemu Linux i Windows nie tylko do łączenia się z Azure Monitor, obsługuje także Azure Automation do hostowania roli hybrydowego procesu roboczego elementu Runbook i innych usług, takich jak [Change Tracking](../../automation/change-tracking.md), [Update Management](../../automation/automation-update-management.md)i [Azure Security Center](../../security-center/security-center-intro.md). Aby uzyskać więcej informacji na temat roli hybrydowego procesu roboczego elementu Runbook, zobacz [Azure Automation hybrydowego procesu roboczego elementu Runbook](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="supported-windows-operating-systems"></a>Obsługiwane systemy operacyjne Windows

Windows agent oficjalnie obsługuje następujące wersje systemu operacyjnego Windows:

* Windows Server 2019
* Windows Server 2008 z dodatkiem SP2 (x64), 2008 R2, 2012, 2012 R2, 2016, wersja 1709 i 1803
* Windows 7 z dodatkiem SP1, Windows 8 Enterprise i Pro oraz Windows 10 Enterprise i Pro

>[!NOTE]
>Agent Log Analytics dla systemu Windows został zaprojektowany z myślą o obsłudze scenariuszy monitorowania serwera, dlatego można uruchomić klienta systemu Windows w celu obsługi obciążeń skonfigurowanych i zoptymalizowanych pod kątem systemu operacyjnego serwera. Agent obsługuje klienta systemu Windows, jednak nasze rozwiązania do monitorowania nie koncentrują się na scenariuszach monitorowania klientów, chyba że określono inaczej.

## <a name="supported-linux-operating-systems"></a>Obsługiwane systemy operacyjne Linux

Ta sekcja zawiera szczegółowe informacje o obsługiwanych dystrybucjach systemu Linux.

Począwszy od wersji wydana po sierpnia 2018 wprowadzamy następujące zmiany do nasz model pomocy technicznej:  

* Tylko serwera, które wersje są obsługiwane, nie klient.  
* Nowe wersje [poświadczonej dystrybucje systemu Azure](../../virtual-machines/linux/endorsed-distros.md) w systemie Linux są zawsze obsługiwane.  
* Wszystkie wersje pomocnicze są obsługiwane w przypadku wszystkich wersji głównych, na liście.
* Wersje, które upłynęły Data zakończenia okresu objęcia wsparciem ich producent nie są obsługiwane.  
* Nowe wersje AMI nie są obsługiwane.  
* Tylko te wersje, z systemem SSL 1.x domyślnie są obsługiwane.

>[!NOTE]
>Jeśli używasz dostępnych dla określonych dystrybucji lub wersji, który nie jest obecnie obsługiwane i nie są wyrównywane do nasz model pomocy technicznej, zaleca się rozwidlenie tego repozytorium, potwierdzając, że pomocy technicznej firmy Microsoft, nie będą umożliwiać pomocy z agentem rozwidlone wersji.

* Linux Amazon 2017.09 (x 64)
* CentOS Linux — x86/x64 64 6 i 7 (x 64)  
* Oracle Linux 6 i 7 — x86/x64 64 
* Red Hat Enterprise Linux Server 6 — x86/x64 64 i 7 (x 64)
* Debian GNU/Linux 8 i 9 — x86/x64 64
* Ubuntu 14.04 LTS — x86/x64 64, 16.04 LTS — x86/x64 64 i 18.04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x64) i 15 (x64)

>[!NOTE]
>OpenSSL 1.1.0 jest obsługiwana tylko na platformach x86_x64 (64-bitowa) i OpenSSL wcześniej niż 1.x nie jest obsługiwane na dowolnej platformie.
>

### <a name="agent-prerequisites"></a>Wymagania wstępne agenta

W poniższej tabeli przedstawiono pakiety wymagane dla obsługiwanych dystrybucje systemu Linux, na których zostanie zainstalowany agent.

|Wymagany pakiet |Opis |Wersja minimalna |
|-----------------|------------|----------------|
|Glibc |    Biblioteka GNU C | 2.5-12 
|OpenSSL    | Biblioteki OpenSSL | 1.0. x lub 1.1. x |
|Odsłon | zazwinięcie klienta sieci Web | 7.15.5 |
|Python-ctypes | | 
|WYGASŁ | Moduły uwierzytelniania podłączane | | 

>[!NOTE]
>Do zbierania komunikatów dziennika systemowego wymagane są rsyslog lub Dziennik systemu. Demon syslog domyślne w wersji 5 (sysklog) w wersji w systemie Red Hat Enterprise Linux, CentOS i Oracle Linux nie jest obsługiwana dla zbierania zdarzeń dziennika systemu. Aby zebrać dane dziennika systemu z tej wersji dystrybucji, demona rsyslog powinna zostać zainstalowana i skonfigurowana do zastępowania sysklog.

## <a name="tls-12-protocol"></a>Protokół TLS 1.2

Aby zapewnić bezpieczeństwo danych przesyłanych do dzienników Azure Monitor, zdecydowanie zachęcamy do skonfigurowania agenta do korzystania z co najmniej Transport Layer Security (TLS) 1,2. Starsze wersje protokołu TLS/SSL (SSL) są zagrożone i chociaż nadal działają tak, aby umożliwić zgodność z poprzednimi wersjami, nie są **zalecane**.  Aby uzyskać dodatkowe informacje, zapoznaj się z [bezpiecznym przesyłaniem danych przy użyciu protokołu TLS 1,2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-firewall-requirements"></a>Wymagania dotyczące zapory sieciowej

W poniższych informacjach znajdują się informacje o konfiguracji serwera proxy i zapory wymagane dla systemu Linux i Windows Agent do komunikowania się z dziennikami Azure Monitor.  

|Zasób agenta|Porty |Kierunek |Obejście inspekcji HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Wychodzące|Yes |  
|*.oms.opinsights.azure.com |Port 443 |Wychodzące|Yes |  
|*.blob.core.windows.net |Port 443 |Wychodzące|Yes |  

Informacje dotyczące zapory wymagane do Azure Government można znaleźć w temacie [Azure Government Management](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Jeśli planujesz używać Azure Automation hybrydowego procesu roboczego elementu Runbook do nawiązywania połączenia z usługą Automation i zarejestrowania się z nią w celu używania elementów Runbook lub rozwiązań do zarządzania w danym środowisku, musi on mieć dostęp do numeru portu i adresów URL opisanych w temacie [Konfigurowanie sieci dla hybrydowego procesu roboczego elementu Runbook](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

Agent systemów Windows i Linux obsługuje komunikację za pośrednictwem serwera proxy lub bramy Log Analytics, aby Azure Monitor przy użyciu protokołu HTTPS.  Uwierzytelnianie podstawowe i anonimowe (nazwę użytkownika/hasło) są obsługiwane.  W przypadku agenta systemu Windows połączonego bezpośrednio z usługą konfiguracja serwera proxy jest określana podczas instalacji lub [po wdrożeniu](agent-manage.md#update-proxy-settings) z panelu sterowania lub za pomocą programu PowerShell.  

W przypadku agenta systemu Linux serwer proxy jest określony podczas instalacji lub [po instalacji](agent-manage.md#update-proxy-settings) , modyfikując plik konfiguracji proxy. conf.  Wartość konfiguracji serwera proxy agenta systemu Linux ma następującą składnię:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Jeśli Twój serwer proxy wymaga uwierzytelniania, agenta systemu Linux nadal wymaga zapewnienia pseudotłumaczeń użytkownika/hasła. Może to być wszystkie nazwy użytkownika i hasła.

|Właściwość| Opis |
|--------|-------------|
|Protokół | Protokół HTTPS |
|Użytkownik | Opcjonalna nazwa użytkownika dla uwierzytelniania serwera proxy |
|hasło | Opcjonalne hasło do uwierzytelniania serwera proxy |
|proxyhost | Adres lub nazwę FQDN serwera proxy serwera/Log Analytics bramy |
|port | Numer portu opcjonalne dla bramy analizy dziennika/serwera proxy |

Na przykład: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Jeśli używasz znaków specjalnych, takich jak "\@" w haśle, pojawi się komunikat o błędzie połączenia z serwerem proxy, ponieważ wartość jest analizowana nieprawidłowo.  Aby obejść ten problem, zakoduj hasło w adresie URL przy użyciu narzędzia, takiego jak [UrlDecode](https://www.urldecoder.org/).  

## <a name="install-and-configure-agent"></a>Instalowanie i Konfigurowanie agenta

Łączenie maszyn w ramach subskrypcji platformy Azure lub środowiska hybrydowego bezpośrednio z dziennikami Azure Monitor można wykonać przy użyciu różnych metod, w zależności od wymagań. W poniższej tabeli wymieniono każdej metody, aby określić, która sprawdza się najlepiej w danej organizacji.

|Element źródłowy | Metoda | Opis|
|-------|-------------|-------------|
|Maszyna wirtualna platformy Azure| -Log Analytics rozszerzenie maszyny wirtualnej dla [systemu Windows](../../virtual-machines/extensions/oms-windows.md) lub [Linux](../../virtual-machines/extensions/oms-linux.md) przy użyciu interfejsu wiersza polecenia platformy Azure lub szablonu Azure Resource Manager<br>- [ręcznie z Azure Portal](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json)<br>- [Azure Security Center automatycznej aprowizacji](../../security-center/security-center-enable-data-collection.md)| -Rozszerzenie instaluje agenta Log Analytics na maszynach wirtualnych platformy Azure i rejestruje je w istniejącym Azure Monitor obszarze roboczym.<br>-Azure Security Center może udostępnić agentowi Log Analytics na wszystkich obsługiwanych maszynach wirtualnych platformy Azure i nowych, które są tworzone w przypadku włączenia ich do monitorowania luk w zabezpieczeniach i zagrożeń. W przypadku włączenia tej nowej lub istniejącej maszyny wirtualnej bez zainstalowanego agenta zostanie zainicjowana obsługa administracyjna.|
| Hybrydowy komputer z systemem Windows|- [instalacji ręcznej](agent-windows.md)<br>- [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [szablon Menedżer zasobów z Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Zainstaluj program Microsoft Monitoring Agent z wiersza polecenia lub korzystając z zautomatyzowanej metody, takiej jak Azure Automation DSC, [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications)lub z szablonem Azure Resource Manager, jeśli wdrożono Microsoft Azure Stack w centrum danych.| 
| Hybrydowy komputer z systemem Linux| [Instalacja ręczna](../../azure-monitor/learn/quick-collect-linux-computer.md)|Zainstaluj agenta dla systemu Linux w serwisie GitHub skrypt otoki podczas wywoływania. | 
| System Center Operations Manager|[Integracja Operations Manager z usługą Log Analytics](../../azure-monitor/platform/om-agents.md) | Skonfiguruj integrację między dziennikami Operations Manager i Azure Monitor, aby przekazywać zebrane dane z komputerów z systemem Windows do grupy zarządzania.|  

## <a name="next-steps"></a>Następne kroki

* Przejrzyj [źródła danych](../../azure-monitor/platform/agent-data-sources.md) , aby zrozumieć źródła danych dostępne do zbierania danych z systemu Windows lub Linux. 

* Informacje na temat [zapytań dzienników](../../azure-monitor/log-query/log-query-overview.md) w celu analizowania danych zebranych ze źródeł danych i rozwiązań. 

* Dowiedz się więcej o [rozwiązaniach do monitorowania](../../azure-monitor/insights/solutions.md) , które dodają funkcje do Azure monitor, a także Zbieraj dane w obszarze roboczym log Analytics.
