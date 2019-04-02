---
title: Program Azure AD Connect Health — dane usługi kondycji nie jest maksymalnie alert Data | Dokumentacja firmy Microsoft
description: W tym dokumencie opisano przyczyny alertu "dane usługi kondycji nie jest aktualny" i jak rozwiązać problemy.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: SamuelD
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: zhiweiw
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16794dfdcdc6ed9c2effe412237d2681fca4f394
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58803298"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Dane usługi kondycji nie są na bieżąco alert

## <a name="overview"></a>Przegląd
Agenci na maszynach w środowisku lokalnym program Azure AD Connect Health monitoruje okresowego przekazywania danych do usługi Azure AD Connect Health. Jeśli usługa nie odbiera danych z agenta, informacje znajdujące się w portalu będą nieaktualne. Aby wyróżnić ten problem, usługa zostanie podniesiony **dane usługi kondycji nie są na bieżąco** alertu. Są one generowane, gdy usługa nie otrzymał danych ukończone w ciągu ostatnich dwóch godzin.  

* **Ostrzeżenie** stan alertu, jeżeli usługa kondycji odebrała tylko **częściowe** typy danych wysłanych z serwera w ostatnich dwóch godzin. Alert stanu ostrzeżenia, nie spowoduje wyzwolenia powiadomienia e-mail do skonfigurowanych adresatów. 
* **Błąd** stan alertu, jeżeli usługa kondycji nie otrzymał żadnych typów danych z serwera w ostatnich dwóch godzin. Błąd stanu alertu wywołuje wiadomości e-mail z powiadomieniami do skonfigurowanych adresatów.

Usługa pobiera dane z agentów, które są uruchomione na maszynach lokalnych. W zależności od typu usługi w poniższej tabeli wymieniono agentów, które są uruchamiane na komputerze, co robią oraz typów danych, które są generowane przez usługę. W niektórych przypadkach są różne usługi związane z procesem, więc którąś z tych funkcji może być przyczyna nadmiernego. 

## <a name="understanding-the-alert"></a>Opis alertu
W bloku szczegółów alertu wskazuje czas, gdy Alert jest wyświetlany i czas ostatniego wykrycia. Alert jest generowany/ponowne-evaluated przez proces w tle, który jest uruchamiany co dwie godziny. W poniższym przykładzie początkowej został zgłoszony alert 03/10 o 9:59. Nadal istnieje, a nawet w 03/12 10:00:00 kiedy alert został oceniony jako ponownie.
Blok szczegółowo opisuje także czas ostatniego otrzymania określonego typu danych przez usługę kondycji. 
 
 ![Usługa Azure AD Connect Health szczegóły alertu](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
Poniżej przedstawiono mapy i odpowiedni typ danych wymaganych typów usług.

| Typ usługi | Agent (nazwa usługi Windows) | Przeznaczenie | Typ danych, wygenerowane  |
| --- | --- | --- | --- |  
| Usługa Azure AD Connect (synchronizacja) | Usługa szczegółowych informacji synchronizacji programu Azure AD Connect Health | Zbieraj konkretne informacje z narzędziem AAD Connect (łączniki, synchronizacja zasady itp.) | AadSyncService SynchronizationRules <br />  AadSyncService łączników <br /> AadSyncService GlobalConfigurations  <br />  - AadSyncService-RunProfileResults <br /> - AadSyncService-ServiceConfigurations <br /> AadSyncService bajty   |
|  | Usługa monitorowania synchronizacji programu Azure AD Connect Health | Zbierz pliki (AAD Connect określonych) liczników wydajności, śladów funkcji ETW | Licznik wydajności |
| USŁUGI AD DS | Usługa szczegółowych informacji usług AD DS programu Azure AD Connect Health | Wykonaj syntetycznych testy, zbieranie informacji o topologii, metadane replikacji |  - Adds-TopologyInfo-Json <br /> — Typowe-TestData-Json (tworzy wyniki testów)   | 
|  | Usługa monitorowania usług AD DS programu Azure AD Connect Health | Zbierz pliki śladów funkcji ETW, liczniki wydajności (specyficzne dla usług AD DS) | -Licznik wydajności  <br /> — Typowe-TestData-Json (przekazuje wyniki testów)  |
| AD FS | Usługa diagnostyczna usług AD FS programu Azure AD Connect Health | Wykonaj testy syntetycznego | TestResult (tworzy wyniki testów) | 
| | Usługa szczegółowych informacji usług AD FS programu Azure AD Connect Health  | Zbieranie metryk użycia usług AD FS | Adfs-UsageMetrics |
| | Usługa monitorowania usług AD FS programu Azure AD Connect Health | Zbierz pliki śladów funkcji ETW, liczniki wydajności (specyficzne dla usług AD FS) | TestResult (przekazuje wyniki testów) |

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów 

Poniżej podano kroki wymagane do zdiagnozowania problemu. Pierwszy to zestaw podstawowych testów, które są wspólne dla wszystkich typów usług. Tabela poniżej zawierającego się poznać konkretne kroki dla każdego typu usługi i typ danych. 

> [!IMPORTANT] 
> Ten alert jest zgodna Connect Health [zasady przechowywania danych](reference-connect-health-user-privacy.md#data-retention-policy)

* Upewnij się, że są zainstalowane najnowsze wersje agentów. Widok [Historia wersji](reference-connect-health-version-history.md). 
* Upewnij się, że usługi agentów programu Azure AD Connect Health są **systemem** na maszynie. Na przykład programu Connect Health dla usług AD FS mają trzy usługi.
  ![Weryfikowanie usługi Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* Upewnij się, że omijają i spełniać [sekcji wymagania dotyczące](how-to-connect-health-agent-install.md#requirements).
* Użyj [narzędzia łączności test](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) do wykrywania problemów z łącznością.
* Jeśli masz serwer HTTP Proxy, postępuj zgodnie z tymi [czynności konfiguracyjnych](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 


## <a name="next-steps"></a>Kolejne kroki
Jeśli dowolny z powyższych kroków zidentyfikować problem, napraw go i zaczekaj alertu rozwiązać. Proces w tle alertu jest uruchamiane co 2 godziny, potrwa do 2 godzin, aby rozwiązać alert. 

* [Zasady przechowywania danych w usłudze Azure AD Connect Health](reference-connect-health-user-privacy.md#data-retention-policy)
* [Azure AD Connect Health — często zadawane pytania](reference-connect-health-faq.md)
