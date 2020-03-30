---
title: Omówienie agenta usługi Log Analytics
description: W tym temacie opisano, jak zbierać dane i monitorować komputery hostowane na platformie Azure, lokalnie lub w innym środowisku chmury za pomocą usługi Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: 1ca03cde57a9496054d0860fbb70bd286caabe46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533253"
---
# <a name="log-analytics-agent-overview"></a>Omówienie agenta usługi Log Analytics
Agent usługi Azure Log Analytics został opracowany do kompleksowego zarządzania maszynami wirtualnymi w dowolnej chmurze, na komputerach lokalnych i tych monitorowanych przez [program System Center Operations Manager.](https://docs.microsoft.com/system-center/scom/) Agenci systemu Windows i Linux wysyłają zebrane dane z różnych źródeł do obszaru roboczego usługi Log Analytics w usłudze Azure Monitor, a także unikatowe dzienniki lub metryki zdefiniowane w rozwiązaniu do monitorowania. Agent usługi Log Analytics obsługuje również szczegółowe informacje i inne usługi w usłudze Azure Monitor, takie jak [Azure Monitor dla maszyn wirtualnych,](../insights/vminsights-enable-overview.md)Usługa Azure Security [Center](/azure/security-center/)i usługa [Azure Automation.](../../automation/automation-intro.md)

Ten artykuł zawiera szczegółowe omówienie wymagań agenta, systemu i sieci oraz różnych metod wdrażania.

> [!NOTE]
> Może również zostać wyświetlony agent usługi Log Analytics, o którym mowa w programie Microsoft Monitoring Agent (MMA) lub agent systemu OMS Linux.

> [!NOTE]
> Rozszerzenie diagnostyki platformy Azure jest jednym z dostępnych agentów do zbierania danych monitorowania z systemu operacyjnego gościa zasobów obliczeniowych. Zobacz [Omówienie agentów usługi Azure Monitor, aby](agents-overview.md) uzyskać opis różnych agentów i wskazówki dotyczące wybierania odpowiednich agentów dla twoich wymagań.

## <a name="comparison-to-azure-diagnostics-extension"></a>Porównanie z rozszerzeniem diagnostyki platformy Azure
[Rozszerzenie diagnostyki platformy Azure](diagnostics-extension-overview.md) w usłudze Azure Monitor może również służyć do zbierania danych monitorowania z systemu operacyjnego gościa maszyn wirtualnych platformy Azure. Możesz użyć jednego lub obu w zależności od wymagań. Zobacz [Omówienie agentów usługi Azure Monitor, aby](agents-overview.md) uzyskać szczegółowe porównanie agentów usługi Azure Monitor. 

Najważniejsze różnice, które należy wziąć pod uwagę, to:

- Rozszerzenie diagnostyki platformy Azure może być używane tylko z maszynami wirtualnymi platformy Azure. Agent usługi Log Analytics może być używany z maszynami wirtualnymi na platformie Azure, innych chmurach i lokalnie.
- Rozszerzenie diagnostyki platformy Azure wysyła dane do usługi Azure Storage, [Metryki usługi Azure Monitor](data-platform-metrics.md) (tylko dla systemu Windows) i centrum zdarzeń. Agent usługi Log Analytics zbiera dane do [dzienników monitora platformy Azure](data-platform-logs.md).
- Agent usługi Log Analytics jest wymagany dla [rozwiązań,](../monitor-reference.md#insights-and-core-solutions) [usługi Azure Monitor dla maszyn wirtualnych](../insights/vminsights-overview.md)i innych usług, takich jak Usługa Azure Security [Center.](/azure/security-center/)

## <a name="costs"></a>Koszty
Agent usługi Log Analytics nie ponosi żadnych kosztów, ale użytkownik może naliczać opłaty za pożyczone dane. Sprawdź [Zarządzanie użyciem i kosztami za pomocą dzienników usługi Azure Monitor, aby](manage-cost-storage.md) uzyskać szczegółowe informacje na temat cen danych zebranych w obszarze roboczym usługi Log Analytics.

## <a name="data-collected"></a>Zbierane dane
W poniższej tabeli wymieniono typy danych, które można skonfigurować obszar roboczy usługi Log Analytics do zbierania od wszystkich połączonych agentów. Zobacz [Co jest monitorowany przez usługę Azure Monitor?](../monitor-reference.md)

| Źródło danych | Opis |
| --- | --- |
| [Dzienniki zdarzeń systemu Windows](data-sources-windows-events.md) | Informacje wysyłane do systemu rejestrowania zdarzeń systemu Windows. |
| [Dziennik systemu](data-sources-syslog.md)                     | Informacje wysyłane do systemu rejestrowania zdarzeń Systemu Linux. |
| [Wydajność](data-sources-performance-counters.md)  | Wartości liczbowe mierzące wydajność różnych aspektów systemu operacyjnego i obciążeń. |
| [Dzienniki usług IIS](data-sources-iis-logs.md)                 | Informacje o użyciu witryn sieci Web usług IIS działających w systemie operacyjnym gościa. |
| [Niestandardowe dzienniki](data-sources-custom-logs.md)           | Zdarzenia z plików tekstowych na komputerach z systemem Windows i Linux. |

## <a name="data-destinations"></a>Miejsca docelowe danych
Agent usługi Log Analytics wysyła dane do obszaru roboczego usługi Log Analytics w usłudze Azure Monitor. Agent systemu Windows może być wieloadresowy do wysyłania danych do wielu obszarów roboczych i grup zarządzania programu System Center Operations Manager. Agent systemu Linux może wysyłać tylko do jednego miejsca docelowego.

## <a name="other-services"></a>Inne usługi
Agent dla systemów Linux i Windows nie tylko do łączenia się z usługą Azure Monitor, ale także obsługuje usługę Azure Automation do obsługi roli procesu roboczego hybrydowego egoisji i innych usług, takich jak [śledzenie zmian,](../../automation/change-tracking.md) [zarządzanie aktualizacjami](../../automation/automation-update-management.md)i [Usługa Azure Security Center.](../../security-center/security-center-intro.md) Aby uzyskać więcej informacji na temat roli proces roboczy hybrydowego systemu Runbook, zobacz [Proces roboczy hybrydowego licznika systemu Azure Automation](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="installation-and-configuration"></a>Instalacja i konfiguracja

Podczas korzystania z agentów usługi Log Analytics do zbierania danych, należy zrozumieć następujące, aby zaplanować wdrożenie agenta:

* Aby zbierać dane od agentów systemu Windows, można [skonfigurować każdego agenta do raportowania do jednego lub więcej obszarów roboczych,](agent-windows.md)nawet jeśli jest on raportować grupę zarządzania programu System Center Operations Manager. Agent systemu Windows może zgłaszać maksymalnie cztery obszary robocze.
* Agent systemu Linux nie obsługuje multi-homing i może raportować tylko do jednego obszaru roboczego.
* Agent systemu Windows obsługuje [standard FIPS 140,](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation)podczas gdy agent Linuksa go nie obsługuje.  

Jeśli używasz programu System Center Operations Manager 2012 R2 lub nowszej:

* Każdą grupę zarządzania programem Operations Manager można [podłączyć tylko do jednego obszaru roboczego](om-agents.md).
* Komputery z systemem Linux, które zgłaszają się do grupy zarządzania, muszą być skonfigurowane do raportowania bezpośrednio do obszaru roboczego usługi Log Analytics. Jeśli komputery z systemem Linux już zgłaszają się bezpośrednio do obszaru roboczego i chcesz je monitorować za pomocą programu Operations Manager, wykonaj następujące kroki, aby [zgłosić raport do grupy zarządzania programu Operations Manager](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group).
* Agent systemu Windows usługi Log Analytics można zainstalować na komputerze z systemem Windows i zgłosić go zarówno do programu Operations Manager, jak i do innego obszaru roboczego.


Istnieje wiele metod instalowania agenta usługi Log Analytics i podłączania komputera do usługi Azure Monitor w zależności od wymagań. W poniższej tabeli wyróżniono każdą metodę, aby określić, która z nich działa najlepiej w organizacji.

|Element źródłowy | Metoda | Opis|
|-------|-------------|-------------|
|Maszyna wirtualna platformy Azure| [Ręcznie z witryny Azure portal](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json) | Określ maszyny wirtualne do wdrożenia w obszarze roboczym usługi Log Analytics. |
| | Rozszerzenie maszyny Wirtualnej usługi Log Analytics dla [systemu Windows](../../virtual-machines/extensions/oms-windows.md) lub [Linux](../../virtual-machines/extensions/oms-linux.md) przy użyciu interfejsu wiersza polecenia platformy Azure lub szablonu usługi Azure Resource Manager | Rozszerzenie instaluje agenta usługi Log Analytics na maszynach wirtualnych platformy Azure i rejestruje je w istniejącym obszarze roboczym usługi Azure Monitor. |
| | [Usługa Azure Monitor dla maszyn wirtualnych](../insights/vminsights-enable-overview.md) | Po włączeniu monitorowania za pomocą usługi Azure Monitor dla maszyn wirtualnych, instaluje rozszerzenie usługi Log Analytics i agenta. |
| | [Automatyczne inicjowanie obsługi administracyjnej usługi Azure Security Center](../../security-center/security-center-enable-data-collection.md) | Usługa Azure Security Center może aprowizować agenta usługi Log Analytics na wszystkich obsługiwanych maszynach wirtualnych platformy Azure i nowych, które są tworzone, jeśli włączysz go do monitorowania luk w zabezpieczeniach i zagrożeń. Jeśli ta opcja jest włączona, zostanie zainicjowana każda nowa lub istniejąca maszyna wirtualna bez zainstalowanego agenta. |
| Hybrydowy komputer z systemem Windows| [Instalacja ręczna](agent-windows.md) | Zainstaluj agenta monitorowania firmy Microsoft z wiersza polecenia. |
| | [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation) | Automatyzacja instalacji za pomocą usługi Azure Automation DSC. |
| | [Szablon Usługi Resource Manager z usługą Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) | Użyj szablonu usługi Azure Resource Manager, jeśli wdrożono usługę Microsoft Azure Stack w centrum danych.| 
| Hybrydowy komputer z systemem Linux| [Instalacja ręczna](../../azure-monitor/learn/quick-collect-linux-computer.md)|Zainstaluj agenta dla systemu Linux wywołującego skrypt otoki hostowany w usłudze GitHub. | 
| System Center Operations Manager|[Integracja programu Operations Manager z usługą Log Analytics](../../azure-monitor/platform/om-agents.md) | Skonfiguruj integrację między dziennikami programu Operations Manager i Azure Monitor, aby przesyłać dalej zebrane dane z komputerów z systemem Windows, które zgłaszają się do grupy zarządzania.|  


## <a name="supported-windows-operating-systems"></a>Obsługiwane systemy operacyjne Windows

Następujące wersje systemu operacyjnego Windows są oficjalnie obsługiwane przez agenta systemu Windows:

* Windows Server 2019
* Windows Server 2008 SP2 (x64), 2008 R2, 2012, 2012 R2, 2016, wersja 1709 i 1803
* Windows 7 SP1, Windows 8 Enterprise i Pro oraz Windows 10 Enterprise i Pro

>[!NOTE]
>Agent analizy dzienników dla systemu Windows został zaprojektowany do obsługi scenariuszy monitorowania serwera, zdajemy sobie sprawę, że możesz uruchomić klienta systemu Windows do obsługi obciążeń skonfigurowanych i zoptymalizowanych pod kątem systemu operacyjnego serwera. Agent obsługuje klienta systemu Windows, jednak nasze rozwiązania monitorowania nie koncentrują się na scenariuszach monitorowania klienta, chyba że wyraźnie określono.

## <a name="supported-linux-operating-systems"></a>Obsługiwane systemy operacyjne Linux

Ta sekcja zawiera szczegółowe informacje na temat obsługiwanych dystrybucji systemu Linux.

Począwszy od wersji wydanych po sierpniu 2018 r., wprowadzamy następujące zmiany w naszym modelu pomocy technicznej:  

* Obsługiwane są tylko wersje serwera, a nie klient.  
* Skoncentruj się na dowolnej dystrybucji [zatwierdzonej przez system Azure Linux.](../../virtual-machines/linux/endorsed-distros.md) Należy zauważyć, że może wystąpić pewne opóźnienie między nową dystrybucją/wersją, która jest zatwierdzona przez system Azure Linux i jest obsługiwana dla agenta systemu Linux usługi Log Analytics.
* Wszystkie wersje pomocnicze są obsługiwane dla każdej wersji głównej na liście.
* Wersje, które przeszły datę zakończenia pomocy technicznej producenta, nie są obsługiwane.  
* Nowe wersje AMI nie są obsługiwane.  
* Obsługiwane są tylko wersje z uruchomioną domeną SSL 1.x.

>[!NOTE]
>Jeśli używasz dystrybucji lub wersji, która nie jest obecnie obsługiwana i nie jest zgodna z naszym modelem pomocy technicznej, zaleca się rozwidanie tego repozytorium, potwierdzając, że pomoc techniczna firmy Microsoft nie zapewni pomocy w wersjach programów forked.

* Amazon Linux 2017.09 (x64)
* CentOS Linux 6 (x86/x64) i 7 (x64)  
* Oracle Linux 6 i 7 (x86/x64) 
* Red Hat Enterprise Linux Server 6 (x86/x64) i 7 (x64)
* Debian GNU/Linux 8 i 9 (x86/x64)
* Ubuntu 14.04 LTS (x86/x64), 16.04 LTS (x86/x64) i 18.04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x64) i 15 (x64)

>[!NOTE]
>OpenSSL 1.1.0 jest obsługiwany tylko na platformach x86_x64 (64-bitowych), a OpenSSL wcześniej niż 1.x nie jest obsługiwany na żadnej platformie.
>

### <a name="agent-prerequisites"></a>Wymagania wstępne agenta

W poniższej tabeli przedstawiono pakiety wymagane dla obsługiwanych dystrybucji systemu Linux, na których zostanie zainstalowany agent.

|Wymagany pakiet |Opis |Minimalna wersja |
|-----------------|------------|----------------|
|Glibc |    Biblioteka GNU C | 2.5-12 
|Openssl    | Biblioteki OpenSSL | 1.0.x lub 1.1.x |
|Narzędzie Curl | Klient sieci web cURL | 7.15.5 |
|Python-ctypes | | 
|PAM | Podłączane moduły uwierzytelniania (PAM) | | 

>[!NOTE]
>Do zbierania wiadomości syslog wymagane są wiadomości syslog. Domyślny demon syslog w wersji 5 red hat enterprise linux, CentOS i Oracle Linux wersja (sysklog) nie jest obsługiwana dla kolekcji zdarzeń syslog. Aby zebrać dane syslog z tej wersji tych dystrybucji, demon rsyslog powinien być zainstalowany i skonfigurowany do zastąpienia sysklog.

## <a name="tls-12-protocol"></a>Protokół TLS 1.2

Aby zapewnić bezpieczeństwo danych przesyłanych do dzienników usługi Azure Monitor, zdecydowanie zachęcamy do skonfigurowania agenta do używania co najmniej zabezpieczeń warstwy transportu (TLS) 1.2. Starsze wersje TLS/Secure Sockets Layer (SSL) okazały się podatne na ataki i chociaż nadal działają, aby umożliwić zgodność z powrotem, nie są **zalecane.**  Aby uzyskać dodatkowe informacje, zapoznaj [się z bezpiecznymi informacjami o wysyłaniu danych za pomocą protokołu TLS 1.2](data-security.md#sending-data-securely-using-tls-12). 


## <a name="sha-2-code-signing-support-requirement-for-windows"></a>Wymagania dotyczące obsługi podpisywania kodu SHA-2 dla systemu Windows
Agent systemu Windows zacznie używać wyłącznie podpisywania SHA-2 18 maja 2020. Ta zmiana będzie mieć wpływ na klientów korzystających z agenta usługi Log Analytics na starszym systemie operacyjnym w ramach dowolnej usługi platformy Azure (Azure Monitor, Usługa Azure Automation, usługa Azure Update Management, śledzenie zmian platformy Azure, centrum zabezpieczeń Azure, usługa Azure Sentinel, usługa Windows Defender ATP). Zmiana nie wymaga żadnych działań klienta, chyba że agent jest uruchomiony w starszej wersji systemu operacyjnego (Windows 7, Windows Server 2008 R2 i Windows Server 2008). Klienci działający w starszej wersji systemu operacyjnego muszą podjąć następujące działania na swoich komputerach przed 18 maja 2020 r. lub ich agenci przestaną wysyłać dane do swoich obszarów roboczych usługi Log Analytics:

1. Zainstaluj najnowszy dodatek Service Pack dla swojego systemu operacyjnego. Wymagane wersje dodatku Service Pack to:
    - Windows 7 z dodatkiem SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. Zainstaluj aktualizacje systemu Windows podpisywania SHA-2 dla systemu operacyjnego zgodnie z opisem w [2019 sha-2 Wymagania dotyczące obsługi podpisywania kodu dla systemów Windows i WSUS](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)
3. Aktualizacja do najnowszej wersji agenta systemu Windows (wersja 10.20.18029).
4. Zaleca się skonfigurowanie agenta do [używania protokołu TLS 1.2](agent-windows.md#configure-agent-to-use-tls-12). 


## <a name="network-requirements"></a>Wymagania dotyczące sieci
Agent systemu Linux i Windows komunikuje się wychodząco z usługą Azure Monitor za pośrednictwem portu TCP 443, a jeśli komputer łączy się za pośrednictwem zapory lub serwera proxy w celu komunikowania się przez Internet, zapoznaj się z poniższymi wymaganiami, aby zrozumieć konfigurację sieci Wymagane. Jeśli zasady zabezpieczeń IT nie zezwalają komputerom w sieci na łączenie się z Internetem, można skonfigurować [bramę usługi Log Analytics,](gateway.md) a następnie skonfigurować agenta do łączenia się za pośrednictwem bramy z dziennikami usługi Azure Monitor. Agent może następnie odbierać informacje o konfiguracji i wysyłać zebrane dane w zależności od reguł zbierania danych i rozwiązań monitorowania, które zostały włączone w obszarze roboczym.

![Diagram komunikacji agenta usługi Log Analytics](./media/log-analytics-agent/log-analytics-agent-01.png)


## <a name="network-firewall-requirements"></a>Wymagania dotyczące zapory sieciowej
Poniższe informacje zawiera listę informacji o konfiguracji serwera proxy i zapory wymaganych dla agenta systemu Linux i Windows do komunikowania się z dziennikami usługi Azure Monitor.  

|Zasób agenta|Porty |Kierunek |Obejście inspekcji HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |port 443 |Wychodzący|Tak |  
|*.oms.opinsights.azure.com |port 443 |Wychodzący|Tak |  
|*.blob.core.windows.net |port 443 |Wychodzący|Tak |  

Aby uzyskać informacje o zaporze wymagane dla platformy Azure Government, zobacz [Zarządzanie dla instytucji rządowych platformy Azure](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Jeśli planujesz używać procesu roboczego hybrydowego wiązki uruchomieniu usługi Azure Automation do łączenia się z usługą Automatyzacja i rejestrowania się w niej w celu używania wiązek ekwiwania lub rozwiązań do zarządzania w twoim środowisku, musi mieć dostęp do numeru portu i adresów URL opisanych w [temacie Konfigurowanie sieci dla hybrydowego procesu roboczego systemu Runbook](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

Agent systemu Windows i Linux obsługuje komunikację za pośrednictwem serwera proxy lub bramy usługi Log Analytics do usługi Azure Monitor przy użyciu protokołu HTTPS.  Obsługiwane są zarówno uwierzytelnianie anonimowe, jak i podstawowe (nazwa użytkownika/hasło).  W przypadku agenta systemu Windows podłączonego bezpośrednio do usługi konfiguracja serwera proxy jest określona podczas instalacji lub [po wdrożeniu](agent-manage.md#update-proxy-settings) z Panelu sterowania lub z programem PowerShell.  

W przypadku agenta systemu Linux serwer proxy jest określony podczas instalacji lub [po instalacji](agent-manage.md#update-proxy-settings) przez modyfikację pliku konfiguracyjnego proxy.conf.  Wartość konfiguracji serwera proxy agenta systemu Linux ma następującą składnię:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Jeśli serwer proxy nie wymaga uwierzytelniania, agent systemu Linux nadal wymaga podania pseudo użytkownika/hasła. Może to być dowolna nazwa użytkownika lub hasło.

|Właściwość| Opis |
|--------|-------------|
|Protocol (Protokół) | https |
|użytkownik | Opcjonalna nazwa użytkownika do uwierzytelniania serwera proxy |
|hasło | Opcjonalne hasło do uwierzytelniania serwera proxy |
|proxyhost | Adres lub nazwa FQDN serwera proxy/bramy usługi Log Analytics |
|port | Opcjonalny numer portu dla bramy serwera proxy/usługi Log Analytics |

Na przykład: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Jeśli w haśle\@używasz znaków specjalnych, takich jak " , zostanie wyświetlony błąd połączenia serwera proxy, ponieważ wartość jest niepoprawnie analizowana.  Aby obejść ten problem, zakoduj hasło w adresie URL za pomocą narzędzia, takiego jak [URLDecode](https://www.urldecoder.org/).  



## <a name="next-steps"></a>Następne kroki

* Przejrzyj [źródła danych,](agent-data-sources.md) aby zrozumieć dostępne źródła danych do zbierania danych z systemu Windows lub Linux. 
* Dowiedz się więcej o [zapytaniach dziennika](../log-query/log-query-overview.md) do analizowania danych zebranych ze źródeł danych i rozwiązań. 
* Dowiedz się więcej o [rozwiązaniach do monitorowania,](../insights/solutions.md) które dodają funkcje do usługi Azure Monitor, a także zbierają dane w obszarze roboczym usługi Log Analytics.

