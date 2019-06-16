---
title: Zbieranie danych dziennika przy użyciu agenta usługi Azure Log Analytics | Dokumentacja firmy Microsoft
description: Ten temat ułatwia zrozumienie, jak zbierać dane i monitorować komputery z hostowanych na platformie Azure, lokalnie lub w innym środowisku chmury, za pomocą usługi Log Analytics.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: magoedte
ms.openlocfilehash: 436685f3bba58ed7d06dfe834d808e7fe422176b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66751982"
---
# <a name="collect-log-data-with-the-azure-log-analytics-agent"></a>Zbieranie danych dziennika przy użyciu agenta usługi Azure Log Analytics

Agenta usługi Azure Log Analytics, wcześniej nazywana zgodnie z Microsoft Monitoring Agent (MMA) lub usługi OMS Linux agent został opracowany do kompleksowego zarządzania na maszynach lokalnych komputerów monitorowanych przez [System Center Operations Manager ](https://docs.microsoft.com/system-center/scom/)i maszyn wirtualnych w chmurze. Agentów systemów Windows i Linux dołączanie do usługi Azure Monitor i przechowywać zebrane dane dzienników z różnych źródeł w obszaru roboczego usługi Log Analytics, a także wszystkie dzienniki unikatowy lub metryk zgodnie z definicją w rozwiązaniu do monitorowania. 

Ten artykuł zawiera szczegółowe omówienie agenta systemu i wymagania dotyczące sieci i różne metody wdrażania.   

## <a name="overview"></a>Przegląd

![Log Analytics agent komunikacji diagramu](./media/log-analytics-agent/log-analytics-agent-01.png)

Przed analizowanie i przetwarzanie zebranych danych, należy najpierw zainstalować i łączenia agentów dla wszystkich maszyn, które chcesz wysyłać dane do usługi Azure Monitor. Można zainstalować agentów na maszynach wirtualnych platformy Azure przy użyciu rozszerzenia maszyny Wirtualnej analizy dzienników platformy Azure, Windows i Linux oraz maszyn w środowisku hybrydowym za pomocą Instalatora, wiersza polecenia lub przy użyciu Desired State Configuration (DSC) w usłudze Azure Automation. 

Agent dla systemu Linux i Windows komunikuje się za pośrednictwem portu TCP 443 wychodzących do usługi Azure Monitor, a jeśli komputer, który nawiązuje połączenie za pośrednictwem zapory lub serwera proxy do komunikacji za pośrednictwem Internetu, zapoznaj się z wymaganiami poniżej, aby zrozumieć konfigurację sieci Wymagane. Jeśli Twoje informatyczne zasady zabezpieczeń nie zezwalają na komputerach w sieci, aby nawiązać połączenie z Internetem, możesz skonfigurować [bramy usługi Log Analytics](gateway.md) , a następnie skonfigurować agenta Aby nawiązać połączenie za pośrednictwem bramy do dzienników usługi Azure Monitor. Agenta można odbierać informacje o konfiguracji i Wyślij dane zbierane w zależności od tego, jakie dane zasad zbierania i monitorowania rozwiązań jest włączona w obszarze roboczym. 

Monitorowanie komputerów z System Center Operations Manager 2012 R2 lub nowszym, może być wieloadresowych z usługą Azure Monitor, aby zbierać dane i przekazywać je do usługi i nadal być monitorowane przez [programu Operations Manager](../../azure-monitor/platform/om-agents.md). Komputerami z systemem Linux agent nie uwzględniają składnik usługi kondycji, ponieważ nie agenta Windows, a informacje są zbierane i przetwarzane przez serwer zarządzania w jej imieniu. Ponieważ komputery z systemem Linux są monitorowane w inny sposób z programem Operations Manager, nie mają one odbierać konfigurację lub zbieranie danych bezpośrednio i przekazywania przez grupę zarządzania, takich jak jest zarządzany przez agenta systemu Windows. W rezultacie w tym scenariuszu nie jest obsługiwana z komputerów z systemem Linux raportowania programu Operations Manager.  

Windows agent może raportować do czterech obszarów roboczych usługi Log Analytics, a agenta systemu Linux obsługuje tylko raporty do jednego obszaru roboczego.  

Agent dla systemu Linux i Windows, który nie jest tylko w przypadku nawiązywania połączenia z usługi Azure Monitor, obsługuje również usługi Azure Automation do obsługi roli procesu roboczego hybrydowego elementu Runbook i innych usług, takich jak [Change Tracking](../../automation/change-tracking.md) i [rozwiązania Update Management](../../automation/automation-update-management.md). Aby uzyskać więcej informacji na temat roli hybrydowego procesu roboczego Runbook zobacz [usługi Azure Automation hybrydowego Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="supported-windows-operating-systems"></a>Obsługiwane systemy operacyjne Windows
Windows agent oficjalnie obsługuje następujące wersje systemu operacyjnego Windows:

* Windows Server 2019
* Windows Server 2008 R2, 2012, 2012 R2, 2016 w wersji 1709 i 1803
* Windows 7 z dodatkiem SP1 lub nowszy

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
* SUSE Linux Enterprise Server 12 (x 64)

>[!NOTE]
>OpenSSL 1.1.0 jest obsługiwana tylko na platformach x86_x64 (64-bitowa) i OpenSSL wcześniej niż 1.x nie jest obsługiwane na dowolnej platformie.
>

### <a name="agent-prerequisites"></a>Wymagania wstępne dotyczące agenta

W poniższej tabeli wymieniono pakiety wymagane dla obsługiwanych dystrybucjach systemu Linux, które agent zostanie zainstalowany na.

|Wymagany pakiet |Opis |Minimalna wersja |
|-----------------|------------|----------------|
|Glibc |    Biblioteka C GNU | 2.5-12 
|Openssl    | Biblioteki OpenSSL | 1.0.x lub 1.1.x |
|Curl | Klient sieci web programu cURL | 7.15.5 |
|Ctypes języka Python | | 
|PAM | Podłączane moduły uwierzytelniania | | 

>[!NOTE]
>Rsyslog lub syslog-ng są wymagane do zbierania komunikaty dziennika systemowego. Demon syslog domyślne w wersji 5 (sysklog) w wersji w systemie Red Hat Enterprise Linux, CentOS i Oracle Linux nie jest obsługiwana dla zbierania zdarzeń dziennika systemu. Do zbierania danych z serwera syslog z tej wersji tych dystrybucji, demona rsyslog powinien być zainstalowany i skonfigurowany tak, aby zastąpić sysklog.

## <a name="tls-12-protocol"></a>Protokół TLS 1.2
Na ułatwieniu zapewnienia bezpieczeństwa danych przesyłanych do usługi Azure Monitor dzienników, zdecydowanie zachęcamy do konfigurowania agenta do użycia w co najmniej zabezpieczeń TLS (Transport Layer) 1.2. Znaleziono starsze wersje protokołu TLS/Secure Sockets Layer (SSL) są narażone i gdy działają nadal obecnie Zezwalaj wstecznej zgodności, są one **niezalecane**.  Aby uzyskać dodatkowe informacje, przejrzyj [wysyłanie danych przy użyciu protokołu TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-firewall-requirements"></a>Wymagania dotyczące zapory sieciowej
Poniższe informacje lista serwera proxy i zapory konfiguracji wymaganych informacji dla agenta systemu Linux i Windows, do komunikowania się z dziennikami usługi Azure Monitor.  

|Zasób agenta|Porty |Kierunek |Obejście inspekcji HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Wychodzące|Tak |  
|*.oms.opinsights.azure.com |Port 443 |Wychodzące|Tak |  
|*.blob.core.windows.net |Port 443 |Wychodzące|Tak |  
|*.azure-automation.net |Port 443 |Wychodzące|Tak |  

Uzyskać zapory wymagane dla platformy Azure Government, zobacz [zarządzania platformy Azure Government](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Jeśli zamierzasz nawiązać połączenie i rejestrować w usłudze Automation można używać elementów runbook w środowisku za pomocą usługi Azure Automation hybrydowego procesu roboczego Runbook, musi mieć dostęp do numeru portu i adresów URL opisanych w [Konfigurowanie sieci pod kątem Hybrydowego procesu roboczego Runbook](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

Agent systemów Windows i Linux obsługuje komunikację za pośrednictwem serwera proxy lub bramy usługi Log Analytics do monitorowania platformy Azure przy użyciu protokołu HTTPS.  Uwierzytelnianie podstawowe i anonimowe (nazwę użytkownika/hasło) są obsługiwane.  Dla agenta programu Windows bezpośrednio z usługą jest połączonych, konfiguracja serwera proxy zostanie określony podczas instalacji lub [po wdrożeniu](agent-manage.md#update-proxy-settings) w Panelu sterowania lub za pomocą programu PowerShell.  

Dla agenta systemu Linux, serwer proxy został określony podczas instalacji lub [po zakończeniu instalacji](agent-manage.md#update-proxy-settings) , modyfikując plik konfiguracyjny proxy.conf.  Wartość konfiguracji serwera proxy agenta systemu Linux ma następującą składnię:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Jeśli Twój serwer proxy wymaga uwierzytelniania, agenta systemu Linux nadal wymaga zapewnienia pseudotłumaczeń użytkownika/hasła. Może to być wszystkie nazwy użytkownika i hasła.

|Właściwość| Opis |
|--------|-------------|
|Protokół | https |
|Użytkownik | Opcjonalna nazwa użytkownika dla uwierzytelniania serwera proxy |
|hasło | Opcjonalne hasło do uwierzytelniania serwera proxy |
|proxyhost | Adres lub nazwę FQDN serwera proxy serwera/Log Analytics bramy |
|port | Numer portu opcjonalne dla bramy analizy dziennika/serwera proxy |

Na przykład: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Jeśli takie jak używać znaków specjalnych "\@" swoje hasło, zostanie wyświetlony błąd połączenia serwera proxy, ponieważ wartość jest nieprawidłowo przeanalizowane.  Aby obejść ten problem, należy zakodować hasła w adresie URL, za pomocą narzędzia, takie jak [URLDecode](https://www.urldecoder.org/).  

## <a name="install-and-configure-agent"></a>Instalowanie i Konfigurowanie agenta 
Łączenie maszyn w środowisku hybrydowym lub subskrypcji platformy Azure bezpośrednio z usługi Azure Monitor dzienniki można osiągnąć przy użyciu różnych metod, w zależności od wymagań. W poniższej tabeli wymieniono każdej metody, aby określić, która sprawdza się najlepiej w danej organizacji.

|Element źródłowy | Metoda | Opis|
|-------|-------------|-------------|
|Maszyna wirtualna platformy Azure| -Log Analytics VM rozszerzenie [Windows](../../virtual-machines/extensions/oms-windows.md) lub [Linux](../../virtual-machines/extensions/oms-linux.md) przy użyciu wiersza polecenia platformy Azure lub przy użyciu szablonu usługi Azure Resource Manager<br>- [Ręcznie w witrynie Azure portal](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json). | Rozszerzenie instaluje agenta usługi Log Analytics na maszynach wirtualnych platformy Azure i rejestruje je do istniejącego obszaru roboczego usługi Azure Monitor.|
| Hybrydowy komputer z systemem Windows|- [Instalacja ręczna](agent-windows.md)<br>- [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Szablon usługi Resource Manager z usługą Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Zainstaluj Microsoft Monitoring agent z wiersza polecenia lub przy użyciu zautomatyzowanej metody, takie jak usługi Azure Automation DSC, [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications), lub przy użyciu szablonu usługi Azure Resource Manager, jeśli wdrożono firmy Microsoft Usługa Azure Stack w centrum danych.| 
| Hybrydowy komputer z systemem Linux| [Instalacja ręczna](../../azure-monitor/learn/quick-collect-linux-computer.md)|Zainstaluj agenta dla systemu Linux w serwisie GitHub skrypt otoki podczas wywoływania. | 
| System Center Operations Manager|[Integrowanie programu Operations Manager z usługą Log Analytics](../../azure-monitor/platform/om-agents.md) | Skonfiguruj integrację między programem Operations Manager i usługi Azure Monitor dzienniki, aby do przodu zebrane dane z komputerów Windows raportujących do grupy zarządzania.|  

## <a name="next-steps"></a>Kolejne kroki

* Przegląd [źródeł danych](../../azure-monitor/platform/agent-data-sources.md) zrozumienie źródła danych, których można zbierać dane z systemu Windows lub Linux. 

* Dowiedz się więcej o [rejestrowania zapytań](../../azure-monitor/log-query/log-query-overview.md) analizować dane zbierane z innych źródeł danych i rozwiązań. 

* Dowiedz się więcej o [rozwiązania do monitorowania](../../azure-monitor/insights/solutions.md) , dodawanie funkcji do usługi Azure Monitor i również zbierać dane do obszaru roboczego usługi Log Analytics.
