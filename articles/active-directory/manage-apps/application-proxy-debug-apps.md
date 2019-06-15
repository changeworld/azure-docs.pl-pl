---
title: Debugowanie aplikacji serwera Proxy aplikacji — Azure Active Directory | Dokumentacja firmy Microsoft
description: Debugowanie problemów z aplikacjami serwera Proxy aplikacji usługi Azure Active Directory (Azure AD).
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
ms.openlocfilehash: d0a12bde119e9dae3f950603fac4bce060bb5f91
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66172268"
---
# <a name="debug-application-proxy-application-issues"></a>Debugowanie problemów z aplikacjami serwera Proxy aplikacji 

Ten artykuł ułatwia rozwiązywanie problemów z aplikacje serwera Proxy aplikacji usługi Azure Active Directory (Azure AD). Jeśli używasz usługi serwera Proxy aplikacji dostępu zdalnego do aplikacji sieci web w środowisku lokalnym, ale występują problemy z nawiązaniem połączenia z aplikacją, debugowanie problemów z aplikacjami przy użyciu funkcji tym schemacie blokowym. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

Podczas rozwiązywania problemów z serwera Proxy aplikacji, zalecamy rozpoczęcie od łączników. Po pierwsze, postępuj zgodnie z przepływem rozwiązywania problemów w [łącznika serwera Proxy aplikacji debugowanie problemów](application-proxy-debug-connectors.md) się upewnić, że łączników serwera Proxy aplikacji zostały skonfigurowane prawidłowo. Jeśli nadal występują problemy, zwróć się do tego artykułu, aby rozwiązać problemy z aplikacją.  

Aby uzyskać więcej informacji o serwerze Proxy aplikacji Zobacz:

- [Dostęp zdalny do aplikacji lokalnych za pośrednictwem serwera Proxy aplikacji](application-proxy.md)
- [Łączników serwera Proxy aplikacji](application-proxy-connectors.md)
- [Instalowanie i rejestrowanie łącznika](application-proxy-add-on-premises-application.md)
- [Rozwiązywanie problemów z serwera Proxy aplikacji i komunikaty o błędach](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>Schemat blokowy problemów z aplikacjami

Ten schemat blokowy przeprowadzi Cię przez kroki do debugowania, niektóre najbardziej typowe problemy z nawiązaniem połączenia z aplikacji. Aby uzyskać szczegółowe informacje o każdym kroku Zobacz tabeli schematu blokowego.

![Schemat blokowy przedstawiający kroki dotyczące debugowania aplikacji](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

|  | Akcja | Opis | 
|---------|---------|---------|
|1 | Otwórz przeglądarkę, uzyskać dostęp do aplikacji i wprowadź swoje poświadczenia | Spróbuj zalogować się do aplikacji i sprawdź błędy związane z użytkownikiem, jak przy użyciu poświadczeń [nie można uzyskać dostępu do tej aplikacji firmowej](application-proxy-sign-in-bad-gateway-timeout-error.md). |
|2 | Sprawdź przypisania użytkownika do aplikacji | Upewnij się, Twoje konto użytkownika ma uprawnienia do dostępu do aplikacji z wewnątrz sieci firmowej, a następnie przetestuj logowanie do aplikacji, wykonując kroki opisane w [przetestować aplikację](application-proxy-add-on-premises-application.md#test-the-application). Jeśli problemy dotyczące logowania będą się powtarzać, zobacz [jak rozwiązywać problemy z błędami logowania](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-troubleshoot-sign-in-errors).  |
|3 | Otwórz przeglądarkę i spróbuj uzyskać dostęp do aplikacji | Jeśli błąd pojawia się natychmiast, sprawdź, czy serwer Proxy aplikacji jest skonfigurowana poprawnie. Aby uzyskać szczegółowe informacje o określone komunikaty o błędach, zobacz [Rozwiązywanie problemów z serwera Proxy aplikacji problemy i komunikaty o błędach](application-proxy-troubleshoot.md).  |
|4 | Sprawdź konfigurację domeny niestandardowej lub usuń błąd | Jeśli strona nie jest wyświetlany na wszystkich, upewnij się, domena niestandardowa jest poprawnie skonfigurowany, przeglądając [Praca z domenami niestandardowymi](application-proxy-configure-custom-domain.md).<br></br>Jeśli strona nie załadowała się i zostanie wyświetlony komunikat o błędzie, należy rozwiązać problem, odwołując się do [Rozwiązywanie problemów z serwera Proxy aplikacji problemy i komunikaty o błędach](application-proxy-troubleshoot.md). <br></br>Jeśli trwa dłużej niż 20 sekund na wyświetlenie komunikatu o błędzie, może to być problem z łącznością. Przejdź do [łączników serwera Proxy aplikacji usługi debugowania](application-proxy-debug-connectors.md) artykule dotyczącym rozwiązywania problemów.  |
|5 | Jeśli problemy będą się powtarzać, przejdź do debugowania łącznika | Może to być problem z łącznością między serwera proxy i łącznika lub między łącznika i zaplecza. Przejdź do [łączników serwera Proxy aplikacji usługi debugowania](application-proxy-debug-connectors.md) artykule dotyczącym rozwiązywania problemów. |
|6 | Publikowanie wszystkich zasobów, sprawdź narzędzia deweloperskie przeglądarki i naprawić łącza | Upewnij się, że ścieżka publikowania zawiera wszystkie niezbędne obrazy, skrypty i arkusze stylów dla aplikacji. Aby uzyskać więcej informacji, zobacz [Dodawanie aplikacji lokalnych do usługi Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). <br></br>Użyj narzędzia dla deweloperów (F12 tools w programie Internet Explorer lub Microsoft Edge) w przeglądarce i sprawdź publikowania problemy, zgodnie z opisem w [strony aplikacji nie są wyświetlane poprawnie](application-proxy-page-appearance-broken-problem.md). <br></br>Zapoznaj się z opcjami postępowania po otrzymaniu uszkodzonych połączeń w [nie działają łącza na stronie](application-proxy-page-links-broken-problem.md). |
|7 | Sprawdzanie dla opóźnienia sieci | Jeśli strona ładuje się powoli, więcej informacji na temat sposobów, aby zminimalizować opóźnienie sieci w [zagadnienia dotyczące zredukowanie opóźnień](application-proxy-network-topology.md#considerations-for-reducing-latency). | 
|8 | Zobacz dodatkowe pomoc dotyczącą rozwiązywania problemów | Jeśli problemy będą się powtarzać, Znajdź dodatkowe artykuły dotyczące rozwiązywania problemów w [Rozwiązywanie problemów z dokumentacją serwera Proxy aplikacji](application-proxy-page-appearance-broken-problem.md). |

## <a name="next-steps"></a>Kolejne kroki


* [Publikuj aplikacje w oddzielnych sieciach i miejsc za pomocą grupy łączników](application-proxy-connector-groups.md)
* [Praca z istniejących serwerów proxy w środowisku lokalnym](application-proxy-configure-connectors-with-proxy-servers.md)
* [Rozwiązywanie problemów z błędami serwera Proxy aplikacji i łączników](application-proxy-troubleshoot.md)
* [Jak dyskretnie zainstalować łącznik serwera Proxy aplikacji usługi Azure AD](application-proxy-register-connector-powershell.md)
