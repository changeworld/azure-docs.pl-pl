---
title: Program Azure AD Connect Health — dane usługi kondycji nie jest maksymalnie alert Data | Dokumentacja firmy Microsoft
description: W tym dokumencie opisano przyczyny alertu "dane usługi kondycji nie jest aktualny" i jak rozwiązać problemy.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: maheshu
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: zhiweiw
ms.openlocfilehash: 3f97e9696a5138d9102037103a45c86988a7506c
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55489279"
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

### <a name="connect-health-for-sync"></a>Connect Health do celów synchronizacji

| Elementy danych | Kroki rozwiązywania problemów |
| --- | --- | 
| Funkcja kończąca | - [Łączność wychodząca z punktem końcowym usługi platformy Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [Inspekcja połączenia SSL dla ruchu wychodzącego jest filtrowana lub wyłączona](https://technet.microsoft.com/library/ee796230.aspx) <br /> - [Porty zapory na serwerze, na którym jest uruchomiony agent](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [Zezwalaj na wyznaczonym witryn sieci Web, jeśli są włączone zwiększone zabezpieczenia programu Internet Explorer](https://technet.microsoft.com/windows/ms537180(v=vs.60)) |
| AadSyncService SynchronizationRules, <br /> AadSyncService łączników <br /> AadSyncService-GlobalConfigurations, <br /> AadSyncService-RunProfileResults, <br /> AadSyncService-ServiceConfigurations, <br /> AadSyncService-ServiceStatus | -Wychodzącym na podstawie adresów IP można znaleźć [zakresów adresów IP platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653) <br /> - [Łączność wychodząca z punktem końcowym usługi platformy Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> -  [Porty zapory na serwerze, na którym jest uruchomiony agent](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) | 

### <a name="connect-health-for-adfs"></a>Program Connect Health dla usług AD FS

Dodatkowe kroki, aby zweryfikować dla usług AD FS i postępuj zgodnie z przepływem pracy w [pomocy usługi AD FS](https://adfshelp.microsoft.com/TroubleshootingGuides/Workflow/3ef51c1f-499e-4e07-b3c4-60271640e282).

| Elementy danych | Kroki rozwiązywania problemów |
| --- | --- | 
| PerfCounter, TestResult | - [Łączność wychodząca z punktem końcowym usługi platformy Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [Inspekcja połączenia SSL dla ruchu wychodzącego jest filtrowana lub wyłączona](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [Porty zapory na serwerze, na którym jest uruchomiony agent](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [Zezwalaj na wyznaczonym witryn sieci Web, jeśli są włączone zwiększone zabezpieczenia programu Internet Explorer](https://technet.microsoft.com/windows/ms537180(v=vs.60)) |
|  Adfs-UsageMetrics | Łączność wychodząca na podstawie adresów IP można znaleźć [zakresów adresów IP platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653) | 

### <a name="connect-health-for-adds"></a>Program Connect Health dla usług AD DS

| Elementy danych | Kroki rozwiązywania problemów |
| --- | --- | 
| PerfCounter, Adds-TopologyInfo-Json, Common-TestData-Json | - [Łączność wychodząca z punktem końcowym usługi platformy Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> - [Inspekcja połączenia SSL dla ruchu wychodzącego jest filtrowana lub wyłączona](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [Porty zapory na serwerze, na którym jest uruchomiony agent](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [Zezwalaj na wyznaczonym witryn sieci Web, jeśli są włączone zwiększone zabezpieczenia programu Internet Explorer](https://technet.microsoft.com/windows/ms537180(v=vs.60)) <br />  -Wychodzącym na podstawie adresów IP można znaleźć [zakresów adresów IP platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653)  |


## <a name="next-steps"></a>Kolejne kroki
* [Azure AD Connect Health — często zadawane pytania](reference-connect-health-faq.md)
