---
title: Program Azure AD Connect Health — dane usługi kondycji nie jest maksymalnie alert Data | Dokumentacja firmy Microsoft
description: W tym dokumencie opisano przyczyny alertu "dane usługi kondycji nie jest aktualny" i jak rozwiązać problemy.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: zhiweiw
ms.openlocfilehash: e470a44732b881311eacecfdf2bd2211598d880a
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49984864"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Dane usługi kondycji nie są na bieżąco alert

## <a name="overview"></a>Przegląd
<li>Program Azure AD Connect Health umożliwia wygenerowanie alertu świeże danych podczas nie otrzyma wszystkich punktów danych z serwera przez dwie godziny. Tytuł alertu jest **dane usługi kondycji nie są na bieżąco**. </li>
<li>**Ostrzeżenie** stan alertu w przypadku programu Connect Health nie otrzyma elementy częściowe dane wysyłane z serwera przez dwie godziny. Alert stanu ostrzeżenia, nie będzie wyzwalać powiadomienia e-mail do administratora dzierżawy. </li>
<li>**Błąd** stan alertu w przypadku programu Connect Health nie otrzyma żadnych elementów dane wysyłane z serwera przez dwie godziny. Błąd stanu alertu wywołuje powiadomienia e-mail, aby administratora dzierżawy. </li>

>[!IMPORTANT] 
> Ten alert jest zgodna Connect Health [zasady przechowywania danych](reference-connect-health-user-privacy.md#data-retention-policy)

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów 
* Upewnij się, że omijają i spełniać [sekcji wymagania dotyczące](how-to-connect-health-agent-install.md#requirements).
* Użyj [narzędzia łączności test](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) do wykrywania problemów z łącznością.
* Jeśli masz serwer HTTP Proxy, wykonaj [kroków konfiguracji, w tym miejscu](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 

### <a name="connect-health-for-adfs"></a>Program Connect Health dla usług AD FS
Dodatkowe kroki, aby zweryfikować dla usług AD FS i postępuj zgodnie z przepływem pracy w [pomocy usługi AD FS](https://adfshelp.microsoft.com/TroubleshootingGuides/Workflow/3ef51c1f-499e-4e07-b3c4-60271640e282).

### <a name="data-collection-map-required-steps"></a>Zbieranie danych mapy czynności
| Nazwa usługi | Elementy danych | Kroki rozwiązywania problemów |
| --- | --- | --- | 
| Program Connect Health dla usługi AD FS | Funkcja kończąca, TestResult | - [Łączność wychodząca z punktem końcowym usługi platformy Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [Inspekcja połączenia SSL dla ruchu wychodzącego jest filtrowana lub wyłączona](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [Porty zapory na serwerze, na którym jest uruchomiony agent](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [Zezwalaj na wyznaczonym witryn sieci Web, jeśli są włączone zwiększone zabezpieczenia programu Internet Explorer](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing) |
|  | UsageMetrics usług AD FS | Łączność wychodząca na podstawie adresów IP można znaleźć [zakresów adresów IP platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653) | 
| Connect Health do celów synchronizacji | Funkcja kończąca | - [Łączność wychodząca z punktem końcowym usługi platformy Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [Inspekcja połączenia SSL dla ruchu wychodzącego jest filtrowana lub wyłączona](https://technet.microsoft.com/library/ee796230.aspx) <br /> - [Porty zapory na serwerze, na którym jest uruchomiony agent](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [Zezwalaj na wyznaczonym witryn sieci Web, jeśli są włączone zwiększone zabezpieczenia programu Internet Explorer](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing) |
|  | AadSyncService SynchronizationRules, <br /> AadSyncService łączników <br /> AadSyncService GlobalConfigurations, <br /> AadSyncService RunProfileResults, <br /> AadSyncService ServiceConfigurations, <br /> Bajty AadSyncService | -Wychodzącym na podstawie adresów IP można znaleźć [zakresów adresów IP platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653) <br /> - [Łączność wychodząca z punktem końcowym usługi platformy Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> -  [Porty zapory na serwerze, na którym jest uruchomiony agent](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) | 
| Program Connect Health dla usług AD DS  | Funkcja kończąca, dodaje TopologyInfo-Json, typowe TestData Json | - [Łączność wychodząca z punktem końcowym usługi platformy Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> - [Inspekcja połączenia SSL dla ruchu wychodzącego jest filtrowana lub wyłączona](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [Porty zapory na serwerze, na którym jest uruchomiony agent](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [Zezwalaj na wyznaczonym witryn sieci Web, jeśli są włączone zwiększone zabezpieczenia programu Internet Explorer](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing) <br />  -Wychodzącym na podstawie adresów IP można znaleźć [zakresów adresów IP platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653)  |




## <a name="next-steps"></a>Kolejne kroki
* [Azure AD Connect Health — często zadawane pytania](reference-connect-health-faq.md)
