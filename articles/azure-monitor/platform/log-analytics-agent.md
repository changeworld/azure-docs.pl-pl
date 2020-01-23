---
title: Zbieranie danych dziennika przy użyciu agenta usługi Azure Log Analytics | Dokumentacja firmy Microsoft
description: Ten temat ułatwia zrozumienie, jak zbierać dane i monitorować komputery z hostowanych na platformie Azure, lokalnie lub w innym środowisku chmury, za pomocą usługi Log Analytics.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/24/2019
ms.openlocfilehash: 8e563ae095cf39cdce3e671d4099d2bf1592100a
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513630"
---
# <a name="collect-log-data-with-the-log-analytics-agent"></a>Zbieranie danych dziennika za pomocą agenta Log Analytics

Agent Log Analytics platformy Azure, wcześniej określany jako Microsoft Monitoring Agent (MMA) lub Agent usługi OMS Linux, został opracowany na potrzeby kompleksowego zarządzania na maszynach lokalnych, komputerach monitorowanych przez [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/)i maszynach wirtualnych w dowolnej chmurze. Agenci systemów Windows i Linux dołączą do Azure Monitor i przechowywania zebranych danych dzienników z różnych źródeł w obszarze roboczym Log Analytics, a także wszelkich unikatowych dzienników lub metryk zgodnie z definicją w rozwiązaniu monitorowania. 

Ten artykuł zawiera szczegółowe omówienie agenta systemu i wymagania dotyczące sieci i różne metody wdrażania.

## <a name="overview"></a>Przegląd

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

Agent dla systemu Linux i Windows nie tylko do łączenia się z Azure Monitor, obsługuje także Azure Automation do hostowania roli hybrydowego procesu roboczego elementu Runbook i innych usług, takich jak [Change Tracking](../../automation/change-tracking.md), [Update Management](../../automation/automation-update-management.md)i [Azure Security Center](../../security-center/security-center-intro.md). Aby uzyskać więcej informacji na temat roli hybrydowego procesu roboczego Runbook zobacz [usługi Azure Automation hybrydowego Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).  

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
* Nowe wersje [dystrybucje zatwierdzone na platformie Azure Linux](../../virtual-machines/linux/endorsed-distros.md) są zawsze obsługiwane.  
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

|Wymagany pakiet |Opis |Minimalna wersja |
|-----------------|------------|----------------|
|Glibc |    Biblioteka GNU C | 2.5-12 
|Openssl    | Biblioteki OpenSSL | 1.0. x lub 1.1. x |
|Narzędzie Curl | zazwinięcie klienta sieci Web | 7.15.5 |
|Python-ctypes | | 
|PAM | Podłączane moduły uwierzytelniania (PAM) | | 

>[!NOTE]
>Do zbierania komunikatów dziennika systemowego wymagane są rsyslog lub Dziennik systemu. Demon syslog domyślne w wersji 5 (sysklog) w wersji w systemie Red Hat Enterprise Linux, CentOS i Oracle Linux nie jest obsługiwana dla zbierania zdarzeń dziennika systemu. Aby zebrać dane dziennika systemu z tej wersji dystrybucji, demona rsyslog powinna zostać zainstalowana i skonfigurowana do zastępowania sysklog.

## <a name="tls-12-protocol"></a>Protokół TLS 1.2

Aby zapewnić bezpieczeństwo danych przesyłanych do dzienników Azure Monitor, zdecydowanie zachęcamy do skonfigurowania agenta do korzystania z co najmniej Transport Layer Security (TLS) 1,2. Znaleziono starsze wersje protokołu TLS/Secure Sockets Layer (SSL) są narażone i gdy działają nadal obecnie Zezwalaj wstecznej zgodności, są one **niezalecane**.  Aby uzyskać dodatkowe informacje, przejrzyj [wysyłanie danych przy użyciu protokołu TLS 1.2](data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-firewall-requirements"></a>Wymagania dotyczące zapory sieciowej

W poniższych informacjach znajdują się informacje o konfiguracji serwera proxy i zapory wymagane dla systemu Linux i Windows Agent do komunikowania się z dziennikami Azure Monitor.  

|Zasób agenta|Porty |Kierunek |Obejście inspekcji HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Wychodzące|Tak |  
|*.oms.opinsights.azure.com |Port 443 |Wychodzące|Tak |  
|*.blob.core.windows.net |Port 443 |Wychodzące|Tak |  

Informacje dotyczące zapory wymagane do Azure Government można znaleźć w temacie [Azure Government Management](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Jeśli planujesz używać Azure Automation hybrydowego procesu roboczego elementu Runbook do nawiązywania połączenia z usługą Automation i zarejestrowania się z nią w celu używania elementów Runbook lub rozwiązań do zarządzania w danym środowisku, musi on mieć dostęp do numeru portu i adresów URL opisanych w temacie [Konfigurowanie sieci dla hybrydowego procesu roboczego elementu Runbook](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

Agent systemów Windows i Linux obsługuje komunikację za pośrednictwem serwera proxy lub bramy Log Analytics, aby Azure Monitor przy użyciu protokołu HTTPS.  Uwierzytelnianie podstawowe i anonimowe (nazwę użytkownika/hasło) są obsługiwane.  Dla agenta programu Windows bezpośrednio z usługą jest połączonych, konfiguracja serwera proxy zostanie określony podczas instalacji lub [po wdrożeniu](agent-manage.md#update-proxy-settings) w Panelu sterowania lub za pomocą programu PowerShell.  

Dla agenta systemu Linux, serwer proxy został określony podczas instalacji lub [po zakończeniu instalacji](agent-manage.md#update-proxy-settings) , modyfikując plik konfiguracyjny proxy.conf.  Wartość konfiguracji serwera proxy agenta systemu Linux ma następującą składnię:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Jeśli Twój serwer proxy wymaga uwierzytelniania, agenta systemu Linux nadal wymaga zapewnienia pseudotłumaczeń użytkownika/hasła. Może to być wszystkie nazwy użytkownika i hasła.

|Właściwość| Opis |
|--------|-------------|
|Protocol (Protokół) | https |
|Użytkownik | Opcjonalna nazwa użytkownika dla uwierzytelniania serwera proxy |
|hasło | Opcjonalne hasło do uwierzytelniania serwera proxy |
|proxyhost | Adres lub nazwę FQDN serwera proxy serwera/Log Analytics bramy |
|port | Numer portu opcjonalne dla bramy analizy dziennika/serwera proxy |

Na przykład: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Jeśli takie jak używać znaków specjalnych "\@" swoje hasło, zostanie wyświetlony błąd połączenia serwera proxy, ponieważ wartość jest nieprawidłowo przeanalizowane.  Aby obejść ten problem, należy zakodować hasła w adresie URL, za pomocą narzędzia, takie jak [URLDecode](https://www.urldecoder.org/).  

## <a name="install-and-configure-agent"></a>Instalowanie i Konfigurowanie agenta

Łączenie maszyn w ramach subskrypcji platformy Azure lub środowiska hybrydowego bezpośrednio z dziennikami Azure Monitor można wykonać przy użyciu różnych metod, w zależności od wymagań. W poniższej tabeli wymieniono każdej metody, aby określić, która sprawdza się najlepiej w danej organizacji.

|Źródło | Metoda | Opis|
|-------|-------------|-------------|
|Maszyna wirtualna platformy Azure| -Log Analytics VM rozszerzenie [Windows](../../virtual-machines/extensions/oms-windows.md) lub [Linux](../../virtual-machines/extensions/oms-linux.md) przy użyciu wiersza polecenia platformy Azure lub przy użyciu szablonu usługi Azure Resource Manager<br>- [ręcznie z Azure Portal](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json)<br>- [Azure Security Center automatycznej aprowizacji](../../security-center/security-center-enable-data-collection.md)| -Rozszerzenie instaluje agenta Log Analytics na maszynach wirtualnych platformy Azure i rejestruje je w istniejącym Azure Monitor obszarze roboczym.<br>-Azure Security Center może udostępnić agentowi Log Analytics na wszystkich obsługiwanych maszynach wirtualnych platformy Azure i nowych, które są tworzone w przypadku włączenia ich do monitorowania luk w zabezpieczeniach i zagrożeń. W przypadku włączenia tej nowej lub istniejącej maszyny wirtualnej bez zainstalowanego agenta zostanie zainicjowana obsługa administracyjna.|
| Hybrydowy komputer z systemem Windows|- [Instalacja ręczna](agent-windows.md)<br>- [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Szablon usługi Resource Manager z usługą Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Zainstaluj program Microsoft Monitoring Agent z wiersza polecenia lub korzystając z zautomatyzowanej metody, takiej jak Azure Automation DSC, [Configuration Manager](https://docs.microsoft.com/configmgr/apps/deploy-use/deploy-applications)lub z szablonem Azure Resource Manager, jeśli wdrożono Microsoft Azure Stack w centrum danych.| 
| Hybrydowy komputer z systemem Linux| [Instalacja ręczna](../../azure-monitor/learn/quick-collect-linux-computer.md)|Zainstaluj agenta dla systemu Linux w serwisie GitHub skrypt otoki podczas wywoływania. | 
| System Center Operations Manager|[Integrowanie programu Operations Manager z usługą Log Analytics](om-agents.md) | Skonfiguruj integrację między dziennikami Operations Manager i Azure Monitor, aby przekazywać zebrane dane z komputerów z systemem Windows do grupy zarządzania.|  

## <a name="next-steps"></a>Następne kroki

* Przegląd [źródeł danych](agent-data-sources.md) zrozumienie źródła danych, których można zbierać dane z systemu Windows lub Linux. 

* Dowiedz się więcej o [rejestrowania zapytań](../log-query/log-query-overview.md) analizować dane zbierane z innych źródeł danych i rozwiązań. 

* Dowiedz się więcej o [rozwiązania do monitorowania](../insights/solutions.md) , dodawanie funkcji do usługi Azure Monitor i również zbierać dane do obszaru roboczego usługi Log Analytics.
