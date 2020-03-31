---
title: Kondycja usługi Azure AD Connect — dane usługi kondycji nie są aktualne alert | Dokumenty firmy Microsoft
description: W tym dokumencie opisano przyczynę alertu "Dane usługi kondycji nie są aktualne" i sposób rozwiązywania problemów z nim.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: SamuelD
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a94bd07cf5020981cdf028ec0eccfa8fa531d240
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897169"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Dane usługi zdrowotnej nie są aktualne

## <a name="overview"></a>Omówienie

Agenci na komputerach lokalnych, które monitorują usługę Azure AD Connect Health okresowo przekazują dane do usługi Azure AD Connect health Service. Jeśli usługa nie odbierze danych od agenta, informacje prezentune przez portal będą przestarzałe. Aby wyróżnić problem, usługa podniesie **dane usługi kondycji nie jest aktualny** alert. Ten alert jest generowany, gdy usługa nie otrzymała pełnych danych w ciągu ostatnich dwóch godzin.  

- **Alert stanu ostrzeżenie** jest uruchamiany, jeśli usługa kondycji otrzymała tylko **częściowe** typy danych wysłane z serwera w ciągu ostatnich dwóch godzin. Alert o stanie ostrzeżenia nie wyzwala powiadomień e-mail do skonfigurowanych adresatów. 
- Alert o stanie **błędu** jest uruchamiany, jeśli usługa kondycji nie otrzymała żadnych typów danych z serwera w ciągu ostatnich dwóch godzin. Alert o stanie błędu wyzwala powiadomienia e-mail do skonfigurowanych adresatów.

Usługa pobiera dane od agentów, które są uruchomione na komputerach lokalnych, w zależności od typu usługi. W poniższej tabeli wymieniono agentów, którzy działają na komputerze, co robią i typy danych, które generuje usługa. W niektórych przypadkach w proces zaangażowanych jest wiele usług, więc każdy z nich może być winowajcą. 

## <a name="understanding-the-alert"></a>Opis alertu

**Szczegóły alertu bloku** pokazuje, kiedy alert wystąpił i został ostatnio wykryty. Proces w tle, który jest uruchamiany co dwie godziny generuje i ponownie ocenia alert. W poniższym przykładzie alert początkowy wystąpił 03/10 o 9:59 AM. Alert nadal istniał 03/12 o godzinie 10:00, gdy alert został ponownie oceniony. Bloku również szczegóły czasu służby kondycji ostatnio odebrane określonego typu danych. 
 
 ![Szczegóły alertu usługi Azure AD Connect](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
W poniższej tabeli przedstawiono typy usług odpowiadające im wymagane typy danych:

| Typ usługi | Agent (nazwa usługi systemu Windows) | Przeznaczenie | Wygenerowany typ danych  |
| --- | --- | --- | --- |  
| Usługa Azure AD Connect (synchronizacja) | Usługa szczegółowych informacji synchronizacji programu Azure AD Connect Health | Zbieranie informacji specyficznych dla połączenia AAD (łączniki, reguły synchronizacji itp.) | - AadSyncService-SynchronizationRules <br />  - AadSyncService-Łączniki <br /> - AadSyncService-GlobalConfigurations  <br />  - AadSyncService-RunProfileResults <br /> - AadSyncService-ServiceKonfiguracje <br /> - AadSyncService-ServiceStatus   |
|  | Usługa monitorowania synchronizacji programu Azure AD Connect Health | Zbieranie liczników perf specyficznych dla połączenia AAD, śladów ETW, plików | Licznik wydajności |
| AD DS | Usługa szczegółowych informacji usług AD DS programu Azure AD Connect Health | Wykonywanie testów syntetycznych, zbieranie informacji topologii, metadane replikacji |  - Dodaje-TopologyInfo-Json <br /> - Common-TestData-Json (tworzy wyniki testu)   | 
|  | Usługa monitorowania usług AD DS programu Azure AD Connect Health | Zbieranie liczników perf specyficznych dla dodań, śladów ETW, plików | - Licznik wydajności  <br /> - Common-TestData-Json (przesyła wyniki testu)  |
| AD FS | Usługa diagnostyczna usług AD FS programu Azure AD Connect Health | Wykonywanie testów syntetycznych | TestResult (tworzy wyniki testu) | 
| | Usługa szczegółowych informacji usług AD FS programu Azure AD Connect Health  | Zbieranie metryk użycia usługi ADFS | Adfs-UsageMetria |
| | Usługa monitorowania usług AD FS programu Azure AD Connect Health | Zbieranie liczników perf specyficznych dla usługi ADFS, śladów ETW, plików | TestResult (przesyła wyniki testu) |

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów 

Kroki wymagane do zdiagnozowania problemu podano poniżej. Pierwszy to zestaw podstawowych kontroli, które są wspólne dla wszystkich typów usług. 

> [!IMPORTANT] 
> Ten alert jest zgodny z [zasadami przechowywania danych](reference-connect-health-user-privacy.md#data-retention-policy) Connect Health

* Upewnij się, że są zainstalowane najnowsze wersje agentów. Wyświetlanie [historii wydań](reference-connect-health-version-history.md). 
* Upewnij się, że usługi Agenty kondycji usługi Azure AD Connect są **uruchomione** na komputerze. Na przykład Connect Health for AD FS powinien mieć trzy usługi.
  ![Weryfikowanie programu Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* Upewnij się, że przejdź i spełniają [wymagania sekcji](how-to-connect-health-agent-install.md#requirements).
* Użyj [narzędzia do łączności testowej,](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) aby wykryć problemy z łącznością.
* Jeśli masz serwer proxy HTTP, wykonaj następujące [kroki konfiguracji](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 


## <a name="next-steps"></a>Następne kroki
Jeśli którykolwiek z powyższych kroków zidentyfikował problem, rozwiąż go i poczekaj na rozwiązanie alertu. Proces alertu w tle jest uruchamiany co 2 godziny, więc rozwiązanie alertu potrwa do 2 godzin. 

* [Zasady przechowywania danych usługi Azure AD Connect](reference-connect-health-user-privacy.md#data-retention-policy)
* [Azure AD Connect Health — często zadawane pytania](reference-connect-health-faq.md)
