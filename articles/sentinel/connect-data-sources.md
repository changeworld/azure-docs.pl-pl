---
title: Łączenie źródeł danych z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak łączyć źródła danych z danymi wskaźnikowymi platformy Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: angrobe
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: rkarlin
ms.openlocfilehash: a76f149b8ab9ca8515a7475cd7954b6d4862a92d
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75746878"
---
# <a name="connect-data-sources"></a>Łączenie ze źródłami danych

Aby przejść do tablicy wskaźnikowej platformy Azure, musisz najpierw nawiązać połączenie ze źródłami danych. Wskaźnik platformy Azure obejmuje wiele łączników dla rozwiązań firmy Microsoft, dostępnych poza platformą i zapewniania integracji w czasie rzeczywistym, w tym rozwiązań firmy Microsoft do ochrony przed zagrożeniami, a także źródeł Microsoft 365, takich jak Office 365, Azure AD, Azure ATP i Microsoft Cloud App Security i nie tylko. Ponadto istnieją wbudowane łączniki do szerszego ekosystemu zabezpieczeń dla rozwiązań firm innych niż Microsoft. Możesz również użyć typowego formatu zdarzeń, dziennika systemowego lub REST-API, aby połączyć źródła danych z systemem Azure.  

1. Z menu wybierz pozycję **Łączniki danych**. Ta strona umożliwia wyświetlenie pełnej listy łączników udostępnianych przez wskaźnik platformy Azure oraz ich stan. Wybierz łącznik, który chcesz połączyć, a następnie wybierz pozycję **Otwórz stronę łącznika**. 

   ![Moduły zbierające dane](./media/collect-data/collect-data-page.png)

1. Na określonej stronie łącznika upewnij się, że spełniono wszystkie wymagania wstępne, i postępuj zgodnie z instrukcjami, aby połączyć dane z platformą Azure wskaźnikiem. Synchronizowanie dzienników z platformą Azure jest możliwe dopiero po pewnym czasie. Po nawiązaniu połączenia zobaczysz podsumowanie danych w grafie **otrzymane dane** oraz stan łączności typów danych.

   ![Połącz moduły zbierające](./media/collect-data/opened-connector-page.png)
  
1. Kliknij kartę **następne kroki** , aby uzyskać listę zawartości wbudowanej platformy Azure, która zapewnia dla określonego typu danych.

   ![Moduły zbierające dane](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>Metody połączenia danych

Następujące metody łączenia danych są obsługiwane przez wskaźnik na platformie Azure:

- **Integracja między usługą a usługą**:<br> Niektóre usługi są połączone natywnie, takie jak AWS i usługi firmy Microsoft, te usługi wykorzystują platformę Azure Foundation do zintegrowanej integracji, ale następujące rozwiązania mogą być połączone za pomocą kilku kliknięć:
    - [Amazon Web Services — CloudTrail](connect-aws.md)
    - [Office 365](connect-office-365.md)
    - [Dzienniki inspekcji usługi Azure AD i logowania](connect-azure-active-directory.md)
    - [Aktywność platformy Azure](connect-azure-activity.md)
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Azure Security Center](connect-azure-security-center.md)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Azure Advanced Threat Protection](connect-azure-atp.md)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Zdarzenia zabezpieczeń systemu Windows](connect-windows-security-events.md) 
    - [Zapora systemu Windows](connect-windows-firewall.md)

- **Rozwiązania zewnętrzne za pośrednictwem interfejsu API**: Niektóre źródła danych są połączone przy użyciu interfejsów API, które są udostępniane przez połączone źródło danych. Zazwyczaj większość technologii zabezpieczeń zapewnia zestaw interfejsów API, za pomocą których można pobrać dzienniki zdarzeń. Interfejsy API nawiązują połączenie z platformą Azure, a następnie zbierają określone typy danych i wysyłają je do Log Analytics platformy Azure. Urządzenia połączone za pośrednictwem interfejsu API obejmują:
    - [Barracuda](connect-barracuda.md)
    - [Symantec](connect-symantec.md)
    - [Analiza Citrix (zabezpieczenia)](connect-citrix-analytics.md)

- **Rozwiązania zewnętrzne przez agenta**: wskaźnik na platformie Azure może być połączony ze wszystkimi innymi źródłami danych, które mogą wykonywać przesyłanie strumieniowe w czasie rzeczywistym przy użyciu protokołu dziennika systemowego za pośrednictwem agenta. <br>Większość urządzeń używa protokołu dziennika systemowego do wysyłania komunikatów zdarzeń, które obejmują sam dziennik i dane dotyczące dziennika. Format dzienników jest różny, ale większość urządzeń obsługuje formatowanie oparte na formacie Common Event format (CEF) dla danych dzienników. <br>Agent wskaźnikowy platformy Azure, który jest oparty na agencie Log Analytics, konwertuje CEF sformatowane dzienniki do formatu, który może zostać pozyskany przez Log Analytics. W zależności od typu urządzenia Agent jest instalowany bezpośrednio na urządzeniu lub na dedykowanym serwerze z systemem Linux. Agent dla systemu Linux odbiera zdarzenia z demona dziennika systemu za pośrednictwem protokołu UDP, ale jeśli oczekuje się, że maszyna z systemem Linux będzie zbierać duże ilości zdarzeń dziennika systemowego, są one wysyłane za pośrednictwem protokołu TCP z demona dziennika systemowego do agenta i z tego miejsca do Log Analytics.
    - Zapory, proxy i punkty końcowe:
        - [F5](connect-f5.md)
        - [Punkt kontrolny](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [Fortinet](connect-fortinet.md)
        - [Palo Alto](connect-paloalto.md)
        - [Inne urządzenia CEF](connect-common-event-format.md)
        - [Inne urządzenia dziennika systemu](connect-syslog.md)
        - [Zapora CloudGen Barracuda](connect-barracuda-cloudgen-firewall.md)
        - [ExtraHop (x)](connect-extrahop.md)
        - [Ochrona jednej tożsamości](connect-one-identity.md)
        - [Trend Micro — zabezpieczenia](connect-trend-micro.md)
    - Rozwiązania DLP
    - [Dostawcy analizy zagrożeń](connect-threat-intelligence.md)
    - [Maszyny DNS](connect-dns.md) — Agent zainstalowany bezpośrednio na komputerze DNS
    - Serwery z systemem Linux
    - Inne chmury
    
## Opcje połączenia z agentem<a name="agent-options"></a>

Aby połączyć urządzenie zewnętrzne z centrum danych platformy Azure, należy wdrożyć agenta na dedykowanym komputerze (maszynie wirtualnej lub lokalnie), aby umożliwić obsługę komunikacji między urządzeniem i wskaźnikiem kontrolnym platformy Azure. Agenta można wdrożyć automatycznie lub ręcznie. Automatyczne wdrażanie jest dostępne tylko wtedy, gdy dedykowany komputer jest nową maszyną wirtualną, którą tworzysz na platformie Azure. 


![CEF na platformie Azure](./media/connect-cef/cef-syslog-azure.png)

Alternatywnie można wdrożyć agenta ręcznie na istniejącej maszynie wirtualnej platformy Azure, na maszynie wirtualnej w innej chmurze lub na maszynie lokalnej.

![CEF lokalnie](./media/connect-cef/cef-syslog-onprem.png)

## <a name="map-data-types-with-azure-sentinel-connection-options"></a>Mapowanie typów danych za pomocą opcji połączenia usługi Azure wskaźnikowego


| **Typ danych** | **Jak nawiązać połączenie** | **Łącznik danych?** | **Komentarze** |
|------|---------|-------------|------|
| AWSCloudTrail | [Łączenie usługi AWS](connect-aws.md) | V | |
| AzureActivity | [Omówienie funkcji i dzienników aktywności](../azure-monitor/platform/platform-logs-overview.md) [platformy Azure](connect-azure-activity.md)| V | |
| Dzienniki inspekcji | [Łączenie z usługą Azure AD](connect-azure-active-directory.md)  | V | |
| Dzienniki logowania | [Łączenie z usługą Azure AD](connect-azure-active-directory.md)  | V | |
| AzureFirewall |[Diagnostyka Azure](../firewall/tutorial-diagnostics.md) | V | |
| InformationProtectionLogs_CL  | [Raporty Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip)<br>[Połącz Azure Information Protection](connect-azure-information-protection.md)  | V | Zwykle używa funkcji **InformationProtectionEvents** oprócz typu danych. Aby uzyskać więcej informacji, zobacz [jak modyfikować raporty i tworzyć zapytania niestandardowe](https://docs.microsoft.com/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries)|
| AzureNetworkAnalytics_CL  | [Analiza ruchu](../network-watcher/traffic-analytics.md) [schematu analitycznego ruchu](../network-watcher/traffic-analytics.md)  | | |
| CommonSecurityLog  | [Połącz CEF](connect-common-event-format.md)  | V | |
| OfficeActivity | [Łączenie usługi Office 365](connect-office-365.md) | V | |
| SecurityEvents | [Łączenie zdarzeń zabezpieczeń systemu Windows](connect-windows-security-events.md)  | V | W przypadku skoroszytów z niezabezpieczonymi protokołami zobacz [Ustawienia skoroszytu niezabezpieczonych protokołów](https://blogs.technet.microsoft.com/jonsh/azure-sentinel-insecure-protocols-dashboard-setup/)  |
| Dziennik systemu | [Połącz dziennik systemowy](connect-syslog.md) | V | |
| Zapora aplikacji sieci Web firmy Microsoft (WAF) — (AzureDiagnostics) |[Połącz zaporę aplikacji sieci Web firmy Microsoft](connect-microsoft-waf.md) | V | |
| SymantecICDx_CL | [Połącz firmę Symantec](connect-symantec.md) | V | |
| ThreatIntelligenceIndicator  | [Łączenie analizy zagrożeń](connect-threat-intelligence.md)  | V | |
| VMConnection <br> ServiceMapComputer_CL<br> ServiceMapProcess_CL|  [Mapa usługi Azure Monitor](../azure-monitor/insights/service-map.md)<br>[Azure Monitor dołączania do usługi VM Insights](../azure-monitor/insights/vminsights-onboard.md) <br> [Włącz Azure Monitor szczegółowych informacji o maszynie wirtualnej](../azure-monitor/insights/vminsights-enable-overview.md) <br> [Korzystanie z jednej maszyny wirtualnej na płycie](../azure-monitor/insights/vminsights-enable-single-vm.md)<br>  [Korzystanie z funkcji dołączania za pośrednictwem zasad](../azure-monitor/insights/vminsights-enable-at-scale-policy.md)| X | Skoroszyt usługi VM Insights  |
| DnsEvents | [Połącz system DNS](connect-dns.md) | V | |
| W3CIISLog | [Łączenie dzienników usług IIS](../azure-monitor/platform/data-sources-iis-logs.md)  | X | |
| Typowe | [Łączenie danych telekomunikacyjnych](../azure-monitor/insights/wire-data.md) | X | |
| WindowsFirewall | [Podłączanie zapory systemu Windows](connect-windows-firewall.md) | V | |
| AADIP SecurityAlert  | [Łączenie z usługą Azure AD Identity Protection](connect-azure-ad-identity-protection.md)  | V | |
| AATP SecurityAlert  | [Łączenie usługi Azure ATP](connect-azure-atp.md) | V | |
| SecurityAlert ASC  | [Połącz Azure Security Center](connect-azure-security-center.md)  | V | |
| MCAS SecurityAlert  | [Połącz Microsoft Cloud App Security](connect-cloud-app-security.md)  | V | |
| SecurityAlert | | | |
| Sysmon (zdarzenie) | [Połącz Sysmon](https://azure.microsoft.com/blog/detecting-in-memory-attacks-with-sysmon-and-azure-security-center)<br> [Połącz zdarzenia systemu Windows](../azure-monitor/platform/data-sources-windows-events.md) <br> [Pobierz Analizator Sysmon](https://github.com/Azure/Azure-Sentinel/blob/master/Parsers/SysmonParser.txt)| X | Kolekcja Sysmon nie jest instalowana domyślnie na maszynach wirtualnych. Aby uzyskać więcej informacji na temat sposobu instalowania agenta Sysmon, zobacz [Sysmon](https://docs.microsoft.com/sysinternals/downloads/sysmon). |
| ConfigurationData  | [Automatyzowanie spisu maszyn wirtualnych](../automation/automation-vm-inventory.md)| X | |
| Zmianakonfiguracji  | [Automatyzowanie śledzenia maszyn wirtualnych](../automation/change-tracking.md) | X | |
| F5 BIG-IP | [Połącz F5 BIG-IP](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel.md)  | X | |
| McasShadowItReporting  |  | X | |
| Barracuda_CL | [Połącz Barracuda](connect-barracuda.md) | V | |


## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć pracę z systemem Azure — wskaźnikiem, potrzebna jest subskrypcja do Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
- Dowiedz się, jak dołączać [dane do usługi Azure wskaźnikowej](quickstart-onboard.md)i [uzyskiwać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
