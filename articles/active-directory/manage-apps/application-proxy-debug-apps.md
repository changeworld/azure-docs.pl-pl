---
title: Aplikacje serwera proxy aplikacji debugowania — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Problemy z debugowaniem aplikacji proxy aplikacji usługi Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: 575891d99c077299f5e7abf008c1ebb2b158373f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382075"
---
# <a name="debug-application-proxy-application-issues"></a>Debugowanie problemów z aplikacjami serwera proxy aplikacji 

Ten artykuł ułatwia rozwiązywanie problemów z aplikacjami proxy aplikacji usługi Azure Active Directory (Azure AD). Jeśli używasz usługi Proxy aplikacji do zdalnego dostępu do lokalnej aplikacji sieci web, ale masz problemy z połączeniem się z aplikacją, użyj tego schematu blokowego do debugowania problemów z aplikacją. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

Podczas rozwiązywania problemów z serwerem proxy aplikacji zaleca się rozpoczęcie od łączników. Najpierw postępuj zgodnie z przepływem rozwiązywania problemów z [łącznikiem serwera proxy aplikacji debugowania,](application-proxy-debug-connectors.md) aby upewnić się, że łączniki serwera proxy aplikacji są poprawnie skonfigurowane. Jeśli nadal występują problemy, wróć do tego artykułu, aby rozwiązać problem z aplikacją.  

Aby uzyskać więcej informacji na temat serwera proxy aplikacji, zobacz:

- [Zdalny dostęp do aplikacji lokalnych za pośrednictwem serwera proxy aplikacji](application-proxy.md)
- [Złącza serwera proxy aplikacji](application-proxy-connectors.md)
- [Instalowanie i rejestrowanie łącznika](application-proxy-add-on-premises-application.md)
- [Rozwiązywanie problemów z serwerem proxy aplikacji i komunikatów o błędach](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>Schemat blokowy dla problemów z aplikacją

Ten schemat blokowy przeprowadzi Cię przez kroki debugowania niektóre z bardziej typowych problemów z łączeniem się z aplikacją. Aby uzyskać szczegółowe informacje na temat każdego kroku, zobacz tabelę po schematze blokowym.

![Schemat blokowy przedstawiający kroki debugowania aplikacji](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

|  | Akcja | Opis | 
|---------|---------|---------|
|1 | Otwieranie przeglądarki, uzyskiwanie dostępu do aplikacji i wprowadzanie poświadczeń | Spróbuj użyć poświadczeń, aby zalogować się do aplikacji i sprawdź, czy nie ma błędów związanych z użytkownikiem, takich jak [Ta aplikacja firmowa nie może uzyskać dostępu.](application-proxy-sign-in-bad-gateway-timeout-error.md) |
|2 | Weryfikowanie przypisania użytkowników do aplikacji | Upewnij się, że twoje konto użytkownika ma uprawnienia dostępu do aplikacji z wewnątrz sieci firmowej, a następnie przetestuj logowanie się do aplikacji, wykonując kroki opisane w [sekcji Testowanie aplikacji](application-proxy-add-on-premises-application.md#test-the-application). Jeśli problemy z logowaniem będą się powtarzać, zobacz [Jak rozwiązać problem z błędami logowania](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context).  |
|3 | Otwórz przeglądarkę i spróbuj uzyskać dostęp do aplikacji | Jeśli błąd pojawia się natychmiast, sprawdź, czy serwer proxy aplikacji jest poprawnie skonfigurowany. Aby uzyskać szczegółowe informacje o określonych komunikatach o błędach, zobacz [Rozwiązywanie problemów z serwerem proxy aplikacji i komunikatów o błędach](application-proxy-troubleshoot.md).  |
|4 | Sprawdź niestandardową konfigurację domeny lub rozwiąż problem z błędem | Jeśli strona w ogóle nie jest wyświetlana, upewnij się, że domena niestandardowa jest poprawnie skonfigurowana, przeglądając [opcję Praca z domenami niestandardowymi](application-proxy-configure-custom-domain.md).<br></br>Jeśli strona nie ładuje się i pojawi się komunikat o błędzie, rozwiąż problem, odwołując się do [rozwiązywania problemów z serwerem proxy aplikacji i komunikatów o błędach](application-proxy-troubleshoot.md). <br></br>Jeśli trwa dłużej niż 20 sekund na wyświetlenie komunikatu o błędzie, może to być problem z łącznością. Przejdź do artykułu rozwiązywania problemów [z łącznikami serwera proxy aplikacji debugowania.](application-proxy-debug-connectors.md)  |
|5 | Jeśli problemy będą się powtarzać, przejdź do debugowania łącznika | Może wystąpić problem z łącznością między serwerem proxy a łącznikiem lub między łącznikiem a zapleczem. Przejdź do artykułu rozwiązywania problemów [z łącznikami serwera proxy aplikacji debugowania.](application-proxy-debug-connectors.md) |
|6 | Publikowanie wszystkich zasobów, sprawdzanie narzędzi programistycznych przeglądarki i naprawianie łączy | Upewnij się, że ścieżka publikowania zawiera wszystkie niezbędne obrazy, skrypty i arkusze stylów dla aplikacji. Aby uzyskać szczegółowe informacje, zobacz [Dodawanie aplikacji lokalnej do usługi Azure AD.](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) <br></br>Użyj narzędzi programistycznych przeglądarki (narzędzia F12 w programie Internet Explorer lub Microsoft Edge) i sprawdź, czy nie występują problemy z publikowaniem, jak opisano na [stronie Aplikacji, nie są wyświetlane poprawnie.](application-proxy-page-appearance-broken-problem.md) <br></br>Opcje przeglądu rozwiązywania uszkodzonych łączy w [linkach na stronie nie działają](application-proxy-page-links-broken-problem.md). |
|7 | Sprawdzanie opóźnienia sieci | Jeśli strona ładuje się powoli, dowiedz się więcej o sposobach zminimalizowania opóźnień w sieci w [zagadnieniach dotyczących zmniejszenia opóźnienia](application-proxy-network-topology.md#considerations-for-reducing-latency). | 
|8 | Zobacz dodatkową pomoc dotyczącą rozwiązywania problemów | Jeśli problemy będą się powtarzać, znajdź dodatkowe artykuły dotyczące rozwiązywania problemów w [dokumentacji rozwiązywania problemów z serwerem proxy aplikacji](application-proxy-troubleshoot.md). |

## <a name="next-steps"></a>Następne kroki


* [Publikowanie aplikacji w oddzielnych sieciach i lokalizacjach przy użyciu grup łączników](application-proxy-connector-groups.md)
* [Praca z istniejącymi lokalnymi serwerami proxy](application-proxy-configure-connectors-with-proxy-servers.md)
* [Rozwiązywanie problemów z błędami serwera proxy i łączników aplikacji](application-proxy-troubleshoot.md)
* [Jak dyskretnie zainstalować łącznik serwera proxy aplikacji usługi Azure AD](application-proxy-register-connector-powershell.md)
