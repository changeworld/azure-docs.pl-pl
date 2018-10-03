---
title: Zbieranie danych w środowisku hybrydowym za pomocą agenta usługi Azure Log Analytics | Dokumentacja firmy Microsoft
description: Ten temat ułatwia zrozumienie, jak zbierać dane i monitorować komputery z hostowanych w lokalnym lub innym środowisku chmury z usługą Log Analytics.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 43f077ef07597604eaf42cb4af47cbc2f0e6c524
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2018
ms.locfileid: "48042007"
---
# <a name="collect-data-in-a-hybrid-environment-with-log-analytics-agent"></a>Zbieranie danych w środowisku hybrydowym za pomocą agenta usługi Log Analytics

Usługa Azure Log Analytics można zbierać i działania na danych z komputerów z systemem operacyjnym Windows lub Linux jest uruchomiony w:

* [Usługa Azure virtual machines](log-analytics-quick-collect-azurevm.md) za pomocą rozszerzenia Log Analytics VM Extension 
* Centrum danych jako serwerów fizycznych lub maszyn wirtualnych
* Maszyny wirtualne w usłudze hostowanej w chmurze, takich jak Amazon Web Services (AWS)

Komputery hostowanych w danym środowisku, które mogą być bezpośrednio połączone z usługi Log Analytics lub jeśli jest już monitorowana tych komputerów z System Center Operations Manager 2012 R2 lub nowszym, grupy zarządzania programu Operations Manager można zintegrować z usługą Log Analytics Kontynuuj, utrzymywanie procesy operacji usług IT.  

## <a name="overview"></a>Przegląd

![log-analytics-agent-direct-connect-diagram](media/log-analytics-concept-hybrid/log-analytics-on-prem-comms.png)

Przed analizowanie i przetwarzanie zebranych danych, należy najpierw zainstalować i łączenia agentów dla wszystkich komputerów, które chcesz wysyłać dane do usługi Log Analytics. Można zainstalować agentów na komputerach lokalnych przy użyciu Instalatora, wiersza polecenia lub przy użyciu Desired State Configuration (DSC) w usłudze Azure Automation. 

Agent dla systemu Linux i Windows komunikuje się za pośrednictwem portu TCP 443 wychodzących za pomocą usługi Log Analytics, a jeśli komputer łączy się z serwerem zapory lub serwera proxy do komunikacji za pośrednictwem Internetu, zapoznaj się z wymaganiami poniżej, aby zrozumieć konfigurację sieci Wymagane.  Jeśli Twoje informatyczne zasady zabezpieczeń nie zezwalają na komputerach w sieci, aby nawiązać połączenie z Internetem, możesz skonfigurować [bramy pakietu OMS](log-analytics-oms-gateway.md) , a następnie skonfigurować agenta Aby nawiązać połączenie za pośrednictwem bramy do usługi Log Analytics. Agenta można odbierać informacje o konfiguracji i Wyślij dane zbierane w zależności od tego, jakie zasady zbierania danych i włączonymi przez Ciebie rozwiązaniami. 

Monitorowanie komputerów z System Center Operations Manager 2012 R2 lub nowszym, może być wieloadresowych z usługą Log Analytics, aby zbierać dane i przekazywać je do usługi i nadal być monitorowane przez [programu Operations Manager](log-analytics-om-agents.md). Komputery z systemem Linux monitorowane przez grupę zarządzania programu Operations Manager zintegrowany z usługą Log Analytics nie mają konfiguracji dla źródła danych i do przodu zebranych danych za pośrednictwem grupy zarządzania. Windows agent może raportować do czterech obszarów roboczych, gdy agent systemu Linux obsługuje tylko raporty do jednego obszaru roboczego.  

Agent dla systemu Linux i Windows, który nie jest tylko w przypadku nawiązywania połączenia z usługi Log Analytics, również obsługuje usługę Azure Automation do hosta roli procesu roboczego hybrydowego elementu Runbook i rozwiązań do zarządzania takie jak śledzenie zmian i zarządzania aktualizacjami.  Aby uzyskać więcej informacji na temat roli hybrydowego procesu roboczego Runbook zobacz [usługi Azure Automation hybrydowego Runbook Worker](../automation/automation-hybrid-runbook-worker.md).  

## <a name="supported-windows-operating-systems"></a>Obsługiwane systemy operacyjne Windows
Windows agent oficjalnie obsługuje następujące wersje systemu operacyjnego Windows:

* Windows Server 2008 Service Pack 1 (SP1) lub nowszy
* Windows 7 z dodatkiem SP1 lub nowszy.

## <a name="supported-linux-operating-systems"></a>Obsługiwane systemy operacyjne Linux
Oficjalnie obsługiwane są poniższe dystrybucje systemu Linux.  Jednak agenta systemu Linux może być również uruchomić na inne dystrybucje nie na liście.  Jeśli nie określono inaczej, wszystkie wersje pomocnicze są obsługiwane w przypadku wszystkich wersji głównych, na liście.  

* Linux Amazon 2012.09 do 2015.09 — x86/x64 64
* CentOS Linux 5, 6 i 7 — x86/x64 64  
* Oracle Linux 5, 6 i 7 — x86/x64 64 
* Red Hat Enterprise Linux Server 5, 6 i 7 — x86/x64 64
* Debian GNU/Linux 6, 7 i 8 — x86/x64 64
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 i 12 — x86/x64 64

## <a name="tls-12-protocol"></a>Protokół TLS 1.2
Na ułatwieniu zapewnienia bezpieczeństwa danych przesyłanych do usługi Log Analytics, zdecydowanie zachęcamy do konfigurowania agenta do użycia w co najmniej zabezpieczeń TLS (Transport Layer) 1.2. Znaleziono starsze wersje protokołu TLS/Secure Sockets Layer (SSL) są narażone i gdy działają nadal obecnie Zezwalaj wstecznej zgodności, są one **niezalecane**.  Aby uzyskać dodatkowe informacje, przejrzyj [wysyłanie danych przy użyciu protokołu TLS 1.2](log-analytics-data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-firewall-requirements"></a>Wymagania dotyczące zapory sieciowej
Poniższe informacje lista serwera proxy i zapory konfiguracji wymaganych informacji dla agenta systemu Linux i Windows, do komunikowania się z usługą Log Analytics.  

|Zasób agenta|Porty |Kierunek |Obejście inspekcji HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Dla ruchu przychodzącego i wychodzącego|Yes |  
|*.oms.opinsights.azure.com |Port 443 |Dla ruchu przychodzącego i wychodzącego|Yes |  
|*.blob.core.windows.net |Port 443 |Dla ruchu przychodzącego i wychodzącego|Yes |  
|*.azure-automation.net |Port 443 |Dla ruchu przychodzącego i wychodzącego|Yes |  


Jeśli zamierzasz nawiązać połączenie i rejestrować w usłudze Automation można używać elementów runbook w środowisku za pomocą usługi Azure Automation hybrydowego procesu roboczego Runbook, musi mieć dostęp do numeru portu i adresów URL opisanych w [Konfigurowanie sieci pod kątem Hybrydowego procesu roboczego Runbook](../automation/automation-hybrid-runbook-worker.md#network-planning). 

Agent systemów Windows i Linux obsługuje komunikację za pośrednictwem serwera proxy lub bramę pakietu OMS do usługi Log Analytics przy użyciu protokołu HTTPS.  Uwierzytelnianie podstawowe i anonimowe (nazwę użytkownika/hasło) są obsługiwane.  Dla agenta programu Windows bezpośrednio z usługą jest połączonych, konfiguracja serwera proxy zostanie określony podczas instalacji lub [po wdrożeniu](log-analytics-agent-manage.md#update-proxy-settings) w Panelu sterowania lub za pomocą programu PowerShell.  

Dla agenta systemu Linux, serwer proxy został określony podczas instalacji lub [po zakończeniu instalacji](log-analytics-agent-manage.md#update-proxy-settings) , modyfikując plik konfiguracyjny proxy.conf.  Wartość konfiguracji serwera proxy agenta systemu Linux ma następującą składnię:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Jeśli Twój serwer proxy wymaga uwierzytelniania, agenta systemu Linux nadal wymaga zapewnienia pseudotłumaczeń użytkownika/hasła. Może to być wszystkie nazwy użytkownika i hasła.

|Właściwość| Opis |
|--------|-------------|
|Protokół | https |
|Użytkownik | Opcjonalna nazwa użytkownika dla uwierzytelniania serwera proxy |
|hasło | Opcjonalne hasło do uwierzytelniania serwera proxy |
|proxyhost | Adres lub nazwę FQDN serwera proxy/OMS Gateway |
|port | Numer portu opcjonalne dla serwera proxy/OMS Gateway |

Na przykład: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Jeśli takie jak używać znaków specjalnych "\@" swoje hasło, zostanie wyświetlony błąd połączenia serwera proxy, ponieważ wartość jest nieprawidłowo przeanalizowane.  Aby obejść ten problem, należy zakodować hasła w adresie URL, za pomocą narzędzia, takie jak [URLDecode](https://www.urldecoder.org/).  

## <a name="install-and-configure-agent"></a>Instalowanie i Konfigurowanie agenta 
Łączenie komputerów w środowisku lokalnym bezpośrednio z usługą Log Analytics można osiągnąć przy użyciu różnych metod, w zależności od wymagań. W poniższej tabeli wymieniono każdej metody, aby określić, która sprawdza się najlepiej w danej organizacji.

|Element źródłowy | Metoda | Opis|
|-------|-------------|-------------|
| Komputer z systemem Windows|- [Instalacja ręczna](log-analytics-agent-windows.md)<br>- [Azure Automation DSC](log-analytics-agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Szablon usługi Resource Manager z usługą Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Zainstaluj Microsoft Monitoring agent z wiersza polecenia lub przy użyciu zautomatyzowanej metody, takie jak usługi Azure Automation DSC, [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications), lub przy użyciu szablonu usługi Azure Resource Manager, jeśli wdrożono firmy Microsoft Usługa Azure Stack w centrum danych.| 
|Komputer z systemem Linux| [Instalacja ręczna](log-analytics-quick-collect-linux-computer.md)|Zainstaluj agenta dla systemu Linux w serwisie GitHub skrypt otoki podczas wywoływania. | 
| System Center Operations Manager|[Integrowanie programu Operations Manager z usługą Log Analytics](log-analytics-om-agents.md) | Skonfiguruj integrację między programem Operations Manager i usługi Log Analytics do przekazywania zebrane dane z komputerów z systemami Linux i Windows raportujących do grupy zarządzania.|  

## <a name="next-steps"></a>Kolejne kroki

* Przegląd [źródeł danych](log-analytics-data-sources.md) zrozumienie źródła danych, których można zbierać dane z systemu Windows lub Linux. 

* Dowiedz się więcej o [dziennikach](log-analytics-log-searches.md) analizować dane zbierane z innych źródeł danych i rozwiązań. 

* Dowiedz się więcej o [rozwiązania](log-analytics-add-solutions.md) , dodawanie funkcji do usługi Log Analytics i również zbierać dane w repozytorium OMS.
