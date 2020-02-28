---
title: Omówienie agenta Log Analytics
description: Ten temat ułatwia zrozumienie, jak zbierać dane i monitorować komputery z hostowanych na platformie Azure, lokalnie lub w innym środowisku chmury, za pomocą usługi Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: 365c228edd97ffcd02b86508deff4272365447f6
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77672144"
---
# <a name="log-analytics-agent-overview"></a>Omówienie agenta Log Analytics
Agent usługi Azure Log Analytics został opracowany z myślą o rozbudowanym zarządzaniu między maszynami wirtualnymi w każdej chmurze, maszynach lokalnych i tych monitorowanych przez [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/). Agenci systemów Windows i Linux wysyłają zebrane dane z różnych źródeł do obszaru roboczego Log Analytics w Azure Monitor, a także do wszystkich unikatowych dzienników lub metryk zgodnie z definicją w rozwiązaniu monitorowania. Agent Log Analytics obsługuje także szczegółowe informacje i inne usługi w Azure Monitor, takie jak [Azure monitor dla maszyn wirtualnych](../insights/vminsights-enable-overview.md), [Azure Security Center](/azure/security-center/)i [Azure Automation](../../automation/automation-intro.md).

Ten artykuł zawiera szczegółowe omówienie agenta systemu i wymagania dotyczące sieci i różne metody wdrażania.

> [!NOTE]
> Może również zostać wyświetlony Agent Log Analytics nazywany Microsoft Monitoring Agent (MMA) lub agentem usługi OMS Linux.

> [!NOTE]
> Diagnostyka Azure rozszerzenie jest jednym z agentów dostępnych do zbierania danych monitorowania z systemu operacyjnego gościa zasobów obliczeniowych. Zapoznaj się z [omówieniem agentów Azure monitor](agents-overview.md) , aby uzyskać opis różnych agentów i wskazówki dotyczące wybierania odpowiednich agentów w celu spełnienia określonych wymagań.

## <a name="comparison-to-azure-diagnostics-extension"></a>Porównanie z rozszerzeniem usługi Diagnostyka Azure
[Rozszerzenia usługi Azure Diagnostics](diagnostics-extension-overview.md) w Azure monitor można także użyć do zbierania danych monitorowania z systemu operacyjnego gościa maszyn wirtualnych platformy Azure. W zależności od potrzeb można użyć lub obu tych opcji. Szczegółowe porównanie agentów Azure Monitor można znaleźć w temacie [Omówienie agentów Azure monitor](agents-overview.md) . 

Kluczowe różnice, które należy wziąć pod uwagę:

- Rozszerzenia Diagnostyka Azure można używać tylko z maszynami wirtualnymi platformy Azure. Agent Log Analytics może być używany z maszynami wirtualnymi platformy Azure, innymi chmurami i lokalnymi.
- Diagnostyka Azure rozszerzenie wysyła dane do usługi Azure Storage, [Azure monitor metryk](data-platform-metrics.md) (tylko system Windows) i Event Hubs. Agent Log Analytics zbiera dane do [dzienników Azure monitor](data-platform-logs.md).
- Agent Log Analytics jest wymagany w przypadku [rozwiązań](../monitor-reference.md#insights-and-core-solutions), [Azure monitor dla maszyn wirtualnych](../insights/vminsights-overview.md)i innych usług, takich jak [Azure Security Center](/azure/security-center/).

## <a name="costs"></a>Koszty
Nie ma kosztu dla agenta Log Analytics, ale opłaty za dane pozyskiwane mogą być naliczane. Aby uzyskać szczegółowe informacje na temat cen zebranych w obszarze roboczym Log Analytics, zobacz [temat Zarządzanie użyciem i kosztami za pomocą dzienników Azure monitor](manage-cost-storage.md) .

## <a name="data-collected"></a>Zebrane dane
Poniższa tabela zawiera listę typów danych, które można skonfigurować dla obszaru roboczego Log Analytics do zebrania ze wszystkich połączonych agentów. Zobacz, [co jest monitorowane przez Azure monitor?](../monitor-reference.md) , aby zapoznać się z listą szczegółowych informacji, rozwiązań i innych rozwiązań, które używają agenta log Analytics do zbierania innych rodzajów danych.

| Źródło danych | Opis |
| --- | --- |
| [Dzienniki zdarzeń systemu Windows](data-sources-windows-events.md) | Informacje wysyłane do systemu rejestrowania zdarzeń systemu Windows. |
| [Syslog](data-sources-syslog.md)                     | Informacje wysyłane do systemu rejestrowania zdarzeń w systemie Linux. |
| [Wydajność](data-sources-performance-counters.md)  | Wartości liczbowe mierzące wydajność różnych aspektów systemu operacyjnego i obciążeń. |
| [Dzienniki usług IIS](data-sources-iis-logs.md)                 | Informacje o użyciu witryn sieci Web usług IIS działających w systemie operacyjnym gościa. |
| [Niestandardowe dzienniki](data-sources-custom-logs.md)           | Zdarzenia z plików tekstowych na komputerach z systemem Windows i Linux. |

## <a name="data-destinations"></a>Miejsca docelowe danych
Agent Log Analytics wysyła dane do Log Analytics obszaru roboczego w Azure Monitor. Agent systemu Windows może być wieloadresowy w celu wysyłania danych do wielu obszarów roboczych i System Center Operations Manager grup zarządzania. Agent systemu Linux może wysyłać tylko do jednego miejsca docelowego.

## <a name="other-services"></a>Inne usługi
Agent dla systemu Linux i Windows nie tylko do łączenia się z Azure Monitor, obsługuje także Azure Automation do hostowania roli hybrydowego procesu roboczego elementu Runbook i innych usług, takich jak [Change Tracking](../../automation/change-tracking.md), [Update Management](../../automation/automation-update-management.md)i [Azure Security Center](../../security-center/security-center-intro.md). Aby uzyskać więcej informacji na temat roli hybrydowego procesu roboczego elementu Runbook, zobacz [Azure Automation hybrydowego procesu roboczego elementu Runbook](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="installation-and-configuration"></a>Instalacja i konfiguracja

Korzystając z Log Analytics agentów do zbierania danych, należy zrozumieć następujące kwestie w celu zaplanowania wdrożenia agenta:

* Aby zbierać dane z agentów systemu Windows, można [skonfigurować każdego agenta w celu raportowania do co najmniej jednego obszaru roboczego](agent-windows.md), nawet gdy jest on raportowany do System Center Operations Manager grupy zarządzania. Agent systemu Windows może zgłosić do czterech obszarów roboczych.
* Agent systemu Linux nie obsługuje wiele multihostingu i może być raportowany tylko do jednego obszaru roboczego.
* Agent systemu Windows obsługuje [Standard FIPS 140](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation), a agent Linux nie obsługuje go.  

Jeśli używasz System Center Operations Manager 2012 R2 lub nowszego:

* Każda Operations Manager grupy zarządzania może być [połączona tylko z jednym obszarem roboczym](om-agents.md).
* Komputery z systemem Linux, które są zgłaszane do grupy zarządzania, muszą być skonfigurowane do bezpośredniego raportowania do obszaru roboczego Log Analytics. Jeśli komputery z systemem Linux są już zgłaszane bezpośrednio do obszaru roboczego i chcesz monitorować je za pomocą Operations Manager, wykonaj następujące kroki, aby [zgłosić Operations Manager do grupy zarządzania](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group).
* Program Log Analytics Agent systemu Windows można zainstalować na komputerze z systemem Windows i mieć do niego raport Operations Manager zintegrowany z obszarem roboczym i innym obszarze roboczym.


Istnieje wiele metod instalacji agenta Log Analytics i podłączenia maszyny do Azure Monitor w zależności od wymagań. W poniższej tabeli wymieniono każdej metody, aby określić, która sprawdza się najlepiej w danej organizacji.

|Element źródłowy | Metoda | Opis|
|-------|-------------|-------------|
|Maszyna wirtualna platformy Azure| [Ręcznie z Azure Portal](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json) | Określ maszyny wirtualne do wdrożenia z obszaru roboczego Log Analytics. |
| | Log Analytics rozszerzenie maszyny wirtualnej dla [systemu Windows](../../virtual-machines/extensions/oms-windows.md) lub [Linux](../../virtual-machines/extensions/oms-linux.md) przy użyciu interfejsu wiersza polecenia platformy Azure lub szablonu Azure Resource Manager | Rozszerzenie instaluje agenta usługi Log Analytics na maszynach wirtualnych platformy Azure i rejestruje je do istniejącego obszaru roboczego usługi Azure Monitor. |
| | [Azure Monitor dla maszyn wirtualnych](../insights/vminsights-enable-overview.md) | Włączenie monitorowania za pomocą Azure Monitor dla maszyn wirtualnych powoduje zainstalowanie rozszerzenia Log Analytics i agenta. |
| | [Azure Security Center automatycznej aprowizacji](../../security-center/security-center-enable-data-collection.md) | Azure Security Center może udostępnić agentowi Log Analytics na wszystkich obsługiwanych maszynach wirtualnych platformy Azure i nowych, które są tworzone w przypadku włączenia ich do monitorowania luk w zabezpieczeniach i zagrożeń. W przypadku włączenia tej nowej lub istniejącej maszyny wirtualnej bez zainstalowanego agenta zostanie zainicjowana obsługa administracyjna. |
| Hybrydowy komputer z systemem Windows| [Instalacja ręczna](agent-windows.md) | Zainstaluj program Microsoft Monitoring Agent z poziomu wiersza polecenia. |
| | [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation) | Automatyzowanie instalacji przy użyciu Azure Automation DSC. |
| | [Menedżer zasobów szablon z Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) | Jeśli wdrożono Microsoft Azure Stack w centrum danych, użyj szablonu Azure Resource Manager.| 
| Hybrydowy komputer z systemem Linux| [Instalacja ręczna](../../azure-monitor/learn/quick-collect-linux-computer.md)|Zainstaluj agenta dla systemu Linux w serwisie GitHub skrypt otoki podczas wywoływania. | 
| System Center Operations Manager|[Integracja Operations Manager z usługą Log Analytics](../../azure-monitor/platform/om-agents.md) | Skonfiguruj integrację między dziennikami Operations Manager i Azure Monitor, aby przekazywać zebrane dane z komputerów z systemem Windows do grupy zarządzania.|  


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

Aby zapewnić bezpieczeństwo danych przesyłanych do dzienników Azure Monitor, zdecydowanie zachęcamy do skonfigurowania agenta do korzystania z co najmniej Transport Layer Security (TLS) 1,2. Starsze wersje protokołu TLS/SSL (SSL) są zagrożone i chociaż nadal działają tak, aby umożliwić zgodność z poprzednimi wersjami, nie są **zalecane**.  Aby uzyskać dodatkowe informacje, zapoznaj się z [bezpiecznym przesyłaniem danych przy użyciu protokołu TLS 1,2](data-security.md#sending-data-securely-using-tls-12). 


## <a name="network-requirements"></a>Wymagania dotyczące sieci
Agent dla systemów Linux i Windows komunikuje się z usługą Azure Monitor za pośrednictwem portu TCP 443, a jeśli komputer nawiązuje połączenie za pośrednictwem zapory lub serwera proxy w celu komunikowania się za pośrednictwem Internetu, przejrzyj poniższe wymagania, aby poznać konfigurację sieci Wymagane. Jeśli zasady zabezpieczeń IT nie zezwalają komputerom w sieci na łączenie się z Internetem, można skonfigurować [bramę log Analytics](gateway.md) , a następnie skonfigurować agenta do nawiązywania połączeń za pomocą bramy do Azure monitor dzienników. Agent może następnie odbierać informacje o konfiguracji i wysyłać zebrane dane w zależności od tego, jakie reguły zbierania danych i rozwiązania monitorowania, które zostały włączone w obszarze roboczym.

![Log Analytics agent komunikacji diagramu](./media/log-analytics-agent/log-analytics-agent-01.png)


## <a name="network-firewall-requirements"></a>Wymagania dotyczące zapory sieciowej
W poniższych informacjach znajdują się informacje o konfiguracji serwera proxy i zapory wymagane dla systemu Linux i Windows Agent do komunikowania się z dziennikami Azure Monitor.  

|Zasób agenta|Porty |Kierunek |Obejście inspekcji HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Wychodzący|Yes |  
|*.oms.opinsights.azure.com |Port 443 |Wychodzący|Yes |  
|*.blob.core.windows.net |Port 443 |Wychodzący|Yes |  

Informacje dotyczące zapory wymagane do Azure Government można znaleźć w temacie [Azure Government Management](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Jeśli planujesz używać Azure Automation hybrydowego procesu roboczego elementu Runbook do nawiązywania połączenia z usługą Automation i zarejestrowania się z nią w celu używania elementów Runbook lub rozwiązań do zarządzania w danym środowisku, musi on mieć dostęp do numeru portu i adresów URL opisanych w temacie [Konfigurowanie sieci dla hybrydowego procesu roboczego elementu Runbook](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

Agent systemów Windows i Linux obsługuje komunikację za pośrednictwem serwera proxy lub bramy Log Analytics, aby Azure Monitor przy użyciu protokołu HTTPS.  Uwierzytelnianie podstawowe i anonimowe (nazwę użytkownika/hasło) są obsługiwane.  W przypadku agenta systemu Windows połączonego bezpośrednio z usługą konfiguracja serwera proxy jest określana podczas instalacji lub [po wdrożeniu](agent-manage.md#update-proxy-settings) z panelu sterowania lub za pomocą programu PowerShell.  

W przypadku agenta systemu Linux serwer proxy jest określony podczas instalacji lub [po instalacji](agent-manage.md#update-proxy-settings) , modyfikując plik konfiguracji proxy. conf.  Wartość konfiguracji serwera proxy agenta systemu Linux ma następującą składnię:

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
> Jeśli używasz znaków specjalnych, takich jak "\@" w haśle, pojawi się komunikat o błędzie połączenia z serwerem proxy, ponieważ wartość jest analizowana nieprawidłowo.  Aby obejść ten problem, zakoduj hasło w adresie URL przy użyciu narzędzia, takiego jak [UrlDecode](https://www.urldecoder.org/).  



## <a name="next-steps"></a>Następne kroki

* Przejrzyj [źródła danych](agent-data-sources.md) , aby zrozumieć źródła danych dostępne do zbierania danych z systemu Windows lub Linux. 
* Informacje na temat [zapytań dzienników](../log-query/log-query-overview.md) w celu analizowania danych zebranych ze źródeł danych i rozwiązań. 
* Dowiedz się więcej o [rozwiązaniach do monitorowania](../insights/solutions.md) , które dodają funkcje do Azure monitor, a także Zbieraj dane w obszarze roboczym log Analytics.

