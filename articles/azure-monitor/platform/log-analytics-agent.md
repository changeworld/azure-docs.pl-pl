---
title: Zbieranie danych dziennika za pomocą usługi Azure Log Analytics Agent | Microsoft Docs
description: Ten temat pomaga zrozumieć, jak zbierać dane i monitorować komputery hostowane na platformie Azure, lokalnie lub w innym środowisku chmury przy użyciu Log Analytics.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 10/07/2019
ms.openlocfilehash: 8070abad675acc69f5b1da232b60179078adbc57
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932238"
---
# <a name="collect-log-data-with-the-log-analytics-agent"></a>Zbieranie danych dziennika za pomocą agenta Log Analytics

Program Azure Log Analytics Agent, wcześniej określany jako Microsoft Monitoring Agent (MMA) lub Agent usługi OMS Linux, został opracowany z myślą o zarządzaniu na maszynach lokalnych, komputerach monitorowanych przez [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/)i Maszyny wirtualne w dowolnej chmurze. Agenci systemów Windows i Linux dołączają do Azure Monitor i przechowują zebrane dane dzienników z różnych źródeł w obszarze roboczym Log Analytics, a także do wszelkich unikatowych dzienników lub metryk zgodnie z definicją w rozwiązaniu monitorowania. 

Ten artykuł zawiera szczegółowe omówienie wymagań dotyczących agenta, systemu i sieci oraz różnych metod wdrażania.

## <a name="overview"></a>Przegląd

![Diagram komunikacji agenta Log Analytics](./media/log-analytics-agent/log-analytics-agent-01.png)

Przed analizowaniem i działaniem zebranych danych należy najpierw zainstalować agentów i połączyć je ze wszystkimi maszynami, które mają wysyłać dane do usługi Azure Monitor. Agentów można instalować na maszynach wirtualnych platformy Azure przy użyciu rozszerzenia maszyny wirtualnej Log Analytics platformy Azure dla systemów Windows i Linux oraz dla maszyn w środowisku hybrydowym przy użyciu Instalatora, wiersza polecenia lub z konfiguracją żądanego stanu (DSC) w Azure Automation. 

Agent dla systemów Linux i Windows komunikuje się z usługą Azure Monitor za pośrednictwem portu TCP 443, a jeśli komputer nawiązuje połączenie za pośrednictwem zapory lub serwera proxy w celu komunikowania się za pośrednictwem Internetu, przejrzyj poniższe wymagania, aby poznać konfigurację sieci Wymagane. Jeśli zasady zabezpieczeń IT nie zezwalają komputerom w sieci na łączenie się z Internetem, można skonfigurować [bramę log Analytics](gateway.md) , a następnie skonfigurować agenta do nawiązywania połączeń za pomocą bramy do Azure monitor dzienników. Agent może następnie odbierać informacje o konfiguracji i wysyłać zebrane dane w zależności od tego, jakie reguły zbierania danych i rozwiązania monitorowania, które zostały włączone w obszarze roboczym. 

Korzystając z Log Analytics agentów do zbierania danych, należy zrozumieć następujące kwestie w celu zaplanowania wdrożenia agenta:

* Aby zbierać dane z agentów systemu Windows, można [skonfigurować każdego agenta w celu raportowania do co najmniej jednego obszaru roboczego](agent-windows.md), nawet gdy jest on raportowany do System Center Operations Manager grupy zarządzania. Agent systemu Windows może zgłosić do czterech obszarów roboczych.
* Agent systemu Linux nie obsługuje wiele multihostingu i może być raportowany tylko do jednego obszaru roboczego.
* Agent systemu Windows obsługuje [Standard FIPS 140](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation), a agent Linux nie obsługuje go.  

Jeśli używasz System Center Operations Manager 2012 R2 lub nowszego:

* Każda Operations Manager grupy zarządzania może być [połączona tylko z jednym obszarem roboczym](om-agents.md).
* Komputery z systemem Linux, które są zgłaszane do grupy zarządzania, muszą być skonfigurowane do bezpośredniego raportowania do obszaru roboczego Log Analytics. Jeśli komputery z systemem Linux są już zgłaszane bezpośrednio do obszaru roboczego i chcesz monitorować je za pomocą Operations Manager, wykonaj następujące kroki, aby [zgłosić Operations Manager do grupy zarządzania](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group).
* Program Log Analytics Agent systemu Windows można zainstalować na komputerze z systemem Windows i mieć do niego raport Operations Manager zintegrowany z obszarem roboczym i innym obszarze roboczym.

Agent dla systemów Linux i Windows nie tylko do łączenia się z Azure Monitor, obsługuje także Azure Automation do hostowania roli hybrydowego procesu roboczego elementu Runbook i innych usług, takich jak [Change Tracking](../../automation/change-tracking.md), [Update Management](../../automation/automation-update-management.md)i [Azure Security Center ](../../security-center/security-center-intro.md). Aby uzyskać więcej informacji na temat roli hybrydowego procesu roboczego elementu Runbook, zobacz [Azure Automation hybrydowego procesu roboczego elementu Runbook](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="supported-windows-operating-systems"></a>Obsługiwane systemy operacyjne Windows

Następujące wersje systemu operacyjnego Windows są oficjalnie obsługiwane dla agenta systemu Windows:

* Windows Server 2019
* Windows Server 2008 z dodatkiem SP2 (x64), 2008 R2, 2012, 2012 R2, 2016, wersja 1709 i 1803
* Windows 7 z dodatkiem SP1, Windows 8 Enterprise i Pro oraz Windows 10 Enterprise i Pro

>[!NOTE]
>Agent Log Analytics dla systemu Windows został zaprojektowany z myślą o obsłudze scenariuszy monitorowania serwera, dlatego można uruchomić klienta systemu Windows w celu obsługi obciążeń skonfigurowanych i zoptymalizowanych pod kątem systemu operacyjnego serwera. Agent obsługuje klienta systemu Windows, jednak nasze rozwiązania do monitorowania nie koncentrują się na scenariuszach monitorowania klientów, chyba że określono inaczej.

## <a name="supported-linux-operating-systems"></a>Obsługiwane systemy operacyjne Linux

Ta sekcja zawiera szczegółowe informacje o obsługiwanych dystrybucjach systemu Linux.

Począwszy od wersji wydanej po 2018 sierpnia, wprowadzamy następujące zmiany w naszym modelu pomocy technicznej:  

* Obsługiwane są tylko wersje serwera, a nie klient.  
* Nowe wersje [poświadczonej dystrybucje systemu Azure](../../virtual-machines/linux/endorsed-distros.md) w systemie Linux są zawsze obsługiwane.  
* Wszystkie wersje pomocnicze są obsługiwane dla każdej wymienionej wersji głównej.
* Wersje, które przekazały datę zakończenia obsługi przez producenta, nie są obsługiwane.  
* Nowe wersje AMI nie są obsługiwane.  
* Obsługiwane są tylko wersje, które domyślnie korzystają z protokołu SSL 1. x.

>[!NOTE]
>Jeśli używasz dystrybucji lub wersji, która nie jest obecnie obsługiwana i nie jest zgodna z naszym modelem pomocy technicznej, zalecamy przerozwidlenie tego repozytorium, co oznacza, że pomoc techniczna firmy Microsoft nie będzie świadczyć pomocy z wersjami agentów z rozwidleniami.

* Amazon Linux 2017,09 (x64)
* CentOS Linux 6 (x86/x64) i 7 (x64)  
* Oracle Linux 6 i 7 (x86/x64) 
* Red Hat Enterprise Linux Server 6 (x86/x64) i 7 (x64)
* Debian GNU/Linux 8 i 9 (x86/x64)
* Ubuntu 14,04 LTS (x86/x64), 16,04 LTS (x86/x64) i 18,04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x64) i 15 (x64)

>[!NOTE]
>OpenSSL 1.1.0 jest obsługiwana tylko na platformach x86_x64 (64-bitowe) i OpenSSL starszej niż 1. x nie jest obsługiwana na żadnej platformie.
>

### <a name="agent-prerequisites"></a>Wymagania wstępne agenta

W poniższej tabeli przedstawiono pakiety wymagane dla obsługiwanych dystrybucje systemu Linux, na których zostanie zainstalowany agent.

|Wymagany pakiet |Opis |Wersja minimalna |
|-----------------|------------|----------------|
|Glibc |    Biblioteka GNU C | 2.5 – 12 
|OpenSSL    | Biblioteki OpenSSL | 1.0. x lub 1.1. x |
|Odsłon | zazwinięcie klienta sieci Web | 7.15.5 |
|Python — ctypes | | 
|WYGASŁ | Moduły uwierzytelniania podłączane | | 

>[!NOTE]
>Do zbierania komunikatów dziennika systemowego wymagane są rsyslog lub Dziennik systemu. Domyślny demon dziennika systemowego w wersji 5 Red Hat Enterprise Linux, CentOS i Oracle Linux wersja (sysklog) nie jest obsługiwany w przypadku zbierania zdarzeń dziennika systemowego. Aby zebrać dane dziennika systemu z tej wersji dystrybucji, demona rsyslog powinna zostać zainstalowana i skonfigurowana do zastępowania sysklog.

## <a name="tls-12-protocol"></a>Protokół TLS 1,2

Aby zapewnić bezpieczeństwo danych przesyłanych do dzienników Azure Monitor, zdecydowanie zachęcamy do skonfigurowania agenta do korzystania z co najmniej Transport Layer Security (TLS) 1,2. Starsze wersje protokołu TLS/SSL (SSL) są zagrożone i chociaż nadal działają tak, aby umożliwić zgodność z poprzednimi wersjami, nie są **zalecane**.  Aby uzyskać dodatkowe informacje, zapoznaj się z [bezpiecznym przesyłaniem danych przy użyciu protokołu TLS 1,2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-firewall-requirements"></a>Wymagania dotyczące zapory sieciowej

W poniższych informacjach znajdują się informacje o konfiguracji serwera proxy i zapory wymagane dla systemu Linux i Windows Agent do komunikowania się z dziennikami Azure Monitor.  

|Zasób agenta|Porty |Kierunek |Obejście inspekcji HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Wychodzące|Tak |  
|*.oms.opinsights.azure.com |Port 443 |Wychodzące|Tak |  
|*.blob.core.windows.net |Port 443 |Wychodzące|Tak |  
|*.azure-automation.net |Port 443 |Wychodzące|Tak |  

Informacje dotyczące zapory wymagane do Azure Government można znaleźć w temacie [Azure Government Management](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Jeśli planujesz używać Azure Automation hybrydowego procesu roboczego elementu Runbook do nawiązywania połączenia z usługą Automation i zarejestrowania się z nią w celu używania elementów Runbook w środowisku, musi on mieć dostęp do numeru portu i adresów URL opisanych w temacie [Konfigurowanie sieci dla hybrydowego elementu Runbook Proces roboczy](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

Agent systemów Windows i Linux obsługuje komunikację za pośrednictwem serwera proxy lub bramy Log Analytics, aby Azure Monitor przy użyciu protokołu HTTPS.  Obsługiwane są zarówno uwierzytelnianie anonimowe, jak i podstawowe (nazwa użytkownika i hasło).  W przypadku agenta systemu Windows połączonego bezpośrednio z usługą konfiguracja serwera proxy jest określana podczas instalacji lub [po wdrożeniu](agent-manage.md#update-proxy-settings) z panelu sterowania lub za pomocą programu PowerShell.  

W przypadku agenta systemu Linux serwer proxy jest określony podczas instalacji lub [po instalacji](agent-manage.md#update-proxy-settings) , modyfikując plik konfiguracji proxy. conf.  Wartość konfiguracji serwera proxy agenta systemu Linux ma następującą składnię:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Jeśli serwer proxy nie wymaga uwierzytelniania, Agent systemu Linux nadal wymaga podania pseudo użytkownika/hasła. Może to być dowolna nazwa użytkownika lub hasło.

|Właściwość| Opis |
|--------|-------------|
|Protocol (Protokół) | https |
|Użytkownicy | Opcjonalna nazwa użytkownika dla uwierzytelniania serwera proxy |
|hasło | Opcjonalne hasło do uwierzytelniania serwera proxy |
|proxyhost | Adres lub nazwa FQDN serwera proxy/bramy Log Analytics |
|port | Opcjonalny numer portu dla bramy serwera proxy/Log Analytics |

Na przykład: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Jeśli używasz znaków specjalnych, takich jak "\@" w haśle, pojawi się komunikat o błędzie połączenia z serwerem proxy, ponieważ wartość jest analizowana nieprawidłowo.  Aby obejść ten problem, zakoduj hasło w adresie URL przy użyciu narzędzia, takiego jak [UrlDecode](https://www.urldecoder.org/).  

## <a name="install-and-configure-agent"></a>Instalowanie i Konfigurowanie agenta

Łączenie maszyn w ramach subskrypcji platformy Azure lub środowiska hybrydowego bezpośrednio z dziennikami Azure Monitor można wykonać przy użyciu różnych metod, w zależności od wymagań. W poniższej tabeli przedstawiono każdą metodę, aby określić, która działa najlepiej w organizacji.

|Źródło | Metoda | Opis|
|-------|-------------|-------------|
|Maszyna wirtualna platformy Azure| -Log Analytics rozszerzenie maszyny wirtualnej dla [systemu Windows](../../virtual-machines/extensions/oms-windows.md) lub [Linux](../../virtual-machines/extensions/oms-linux.md) przy użyciu interfejsu wiersza polecenia platformy Azure lub szablonu Azure Resource Manager<br>- [ręcznie z Azure Portal](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json)<br>- [Azure Security Center automatycznej aprowizacji](../../security-center/security-center-enable-data-collection.md)| -Rozszerzenie instaluje agenta Log Analytics na maszynach wirtualnych platformy Azure i rejestruje je w istniejącym Azure Monitor obszarze roboczym.<br>-Azure Security Center może udostępnić agentowi Log Analytics na wszystkich obsługiwanych maszynach wirtualnych platformy Azure i nowych, które są tworzone w przypadku włączenia ich do monitorowania luk w zabezpieczeniach i zagrożeń. W przypadku włączenia tej nowej lub istniejącej maszyny wirtualnej bez zainstalowanego agenta zostanie zainicjowana obsługa administracyjna.|
| Hybrydowy komputer z systemem Windows|[Ręczna instalacja](agent-windows.md) - <br>- [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>szablon Menedżer zasobów - [z Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Zainstaluj program Microsoft Monitoring Agent z wiersza polecenia lub korzystając z zautomatyzowanej metody, takiej jak Azure Automation DSC, [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications)lub z szablonem Azure Resource Manager, jeśli wdrożono Microsoft Azure Stack w centr.| 
| Hybrydowy komputer z systemem Linux| [Instalacja ręczna](../../azure-monitor/learn/quick-collect-linux-computer.md)|Zainstaluj agenta dla systemu Linux wywołującego skrypt otoki hostowany w witrynie GitHub. | 
| System Center Operations Manager|[Integracja Operations Manager z usługą Log Analytics](../../azure-monitor/platform/om-agents.md) | Skonfiguruj integrację między dziennikami Operations Manager i Azure Monitor, aby przekazywać zebrane dane z komputerów z systemem Windows do grupy zarządzania.|  

## <a name="next-steps"></a>Następne kroki

* Przejrzyj [źródła danych](../../azure-monitor/platform/agent-data-sources.md) , aby zrozumieć źródła danych dostępne do zbierania danych z systemu Windows lub Linux. 

* Informacje na temat [zapytań dzienników](../../azure-monitor/log-query/log-query-overview.md) w celu analizowania danych zebranych ze źródeł danych i rozwiązań. 

* Dowiedz się więcej o [rozwiązaniach do monitorowania](../../azure-monitor/insights/solutions.md) , które dodają funkcje do Azure monitor, a także Zbieraj dane w obszarze roboczym log Analytics.
