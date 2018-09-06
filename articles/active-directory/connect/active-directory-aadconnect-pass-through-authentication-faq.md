---
title: 'Usługi Azure AD Connect: Uwierzytelnianie przekazywane — często zadawane pytania | Dokumentacja firmy Microsoft'
description: Odpowiedzi na często zadawane pytania dotyczące uwierzytelniania przekazywanego usługi Azure Active Directory
services: active-directory
keywords: Azure AD Connect uwierzytelniania przekazywanego, instalacji usługi Active Directory, wymaganych składników dla usługi Azure AD, logowania jednokrotnego, logowanie jednokrotne
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 58a33b2d3f06deab4f31c76e04d45f8bd0bbba4c
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2018
ms.locfileid: "43841968"
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Azure Active Directory uwierzytelnianie przekazywane: Często zadawane pytania

W tym artykule opisano często zadawane pytania dotyczące uwierzytelniania przekazywanego usługi Azure Active Directory (Azure AD). Zachowaj sprawdza, czy ponownie zaktualizowanej zawartości.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Która z metod, aby zalogować się do usługi Azure AD, uwierzytelniania przekazywanego hasło wyznaczania wartości skrótu synchronizacji i usługi Active Directory Federation Services (AD FS), należy wybrać?

Przegląd [tego przewodnika](https://docs.microsoft.com/azure/security/azure-ad-choose-authn) porównanie różnych usługi Azure AD logowania metod oraz sposobu wybierania bezpośrednio metodę logowania dla swojej organizacji.

## <a name="is-pass-through-authentication-a-free-feature"></a>Jest uwierzytelnianie przekazywane bezpłatną funkcją?

Uwierzytelnianie przekazywane jest bezpłatną funkcją. Nie potrzebujesz żadnych płatnej wersji usługi Azure AD z niego korzystać.

## <a name="is-pass-through-authentication-available-in-the-microsoft-azure-germany-cloudhttpwwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>Jest dostępna w uwierzytelniania przekazywanego [chmury Microsoft Azure (Niemcy)](http://www.microsoft.de/cloud-deutschland) i [chmury Microsoft Azure dla instytucji rządowych](https://azure.microsoft.com/features/gov/)?

Nie. Uwierzytelnianie przekazywane jest dostępna tylko w na całym świecie wystąpienia usługi Azure AD.

## <a name="does-conditional-accessactive-directory-conditional-access-azure-portalmd-work-with-pass-through-authentication"></a>Jest [dostępu warunkowego](../active-directory-conditional-access-azure-portal.md) pracy przy użyciu przekazywanego uwierzytelniania?

Tak. Wszystkie możliwości dostępu warunkowego, w tym usługi Azure Multi-Factor Authentication, pracować z uwierzytelniania przekazywanego.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Uwierzytelnianie przekazywane obsługuje "Alternatywny identyfikator" jako nazwy użytkownika, a nie "userPrincipalName"?

Tak. Obsługuje uwierzytelnianie przekazywane `Alternate ID` jako nazwa użytkownika w przypadku skonfigurowania w usłudze Azure AD Connect. Aby uzyskać więcej informacji, zobacz [instalację niestandardową programu Azure AD Connect](active-directory-aadconnect-get-started-custom.md). Nie wszystkie aplikacje usługi Office 365 obsługują `Alternate ID`. Zapoznaj się z instrukcji obsługi dokumentacji określonej aplikacji.

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>Synchronizacja skrótów haseł działa jako rezerwowa metoda uwierzytelniania przekazywanego?

Nie. Uwierzytelnianie przekazywane _nie_ automatycznego trybu failover na synchronizację skrótów haseł. Aby uniknąć błędów logowania użytkownika, należy skonfigurować uwierzytelnianie przekazujących [wysokiej dostępności](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-4-ensure-high-availability).

## <a name="can-i-install-an-azure-ad-application-proxymanage-appsapplication-proxymd-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Można zainstalować [serwera Proxy aplikacji usługi Azure AD](../manage-apps/application-proxy.md) łącznika na tym samym serwerze co Agent uwierzytelniania przekazywanego?

Tak. Rebranded wersje agenta uwierzytelniania przekazywanego, wersja 1.5.193.0 lub nowszej, obsługi tej konfiguracji.

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Które wersje programu Azure AD Connect i Agent uwierzytelniania przekazywanego potrzebujesz?

Ta funkcja działała, potrzebujesz wersji 1.1.750.0 później w celu usługi Azure AD Connect i 1.5.193.0 lub nowszym w przypadku agenta uwierzytelniania przekazywanego. Całe oprogramowanie należy zainstalować na serwerach z systemem Windows Server 2012 R2 lub nowszym.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>Co się stanie, jeśli upłynął hasło tego użytkownika, a także próby logowania przy użyciu uwierzytelniania przekazywanego?

Jeśli skonfigurowano [funkcji zapisywania zwrotnego haseł](../authentication/concept-sspr-writeback.md) dla określonego użytkownika, i jeśli użytkownik loguje się przy użyciu uwierzytelniania przekazywanego, można zmienić lub resetowanie haseł. Hasła są zapisywane z powrotem do lokalnej usługi Active Directory zgodnie z oczekiwaniami.

Jeśli nie skonfigurowano funkcję zapisywania zwrotnego haseł dla określonego użytkownika lub jeśli użytkownik nie ma prawidłową usługi Azure AD licencją, użytkownik nie może zaktualizować swoje hasło w chmurze. Nie mogą aktualizować swojego hasła nawet wtedy, gdy ich hasło wygasło. Użytkownik zobaczy zamiast tego komunikatu: "Twoja organizacja nie zezwala na zaktualizować swoje hasło w tej witrynie. Zaktualizuj go zgodnie z metodą zalecaną przez organizację lub skontaktuj się z administratorem Jeśli potrzebujesz pomocy." Użytkownik lub administrator zresetować swoje hasło w usłudze Active Directory w środowisku lokalnym.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Jak Usługa uwierzytelniania przekazywanego chroni należy przed atakami siłowymi hasło?

[Przeczytaj informacje o inteligentnej blokady](../authentication/howto-password-smart-lockout.md).

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>Co agentów uwierzytelniania przekazywanego komunikacji przez porty 80 i 443?

- Agentów uwierzytelniania należy żądania HTTPS za pośrednictwem portu 443 dla wszystkich operacji funkcji.
- Agentów uwierzytelniania należy żądań HTTP za pośrednictwem portu 80 do pobierania list odwołania certyfikatów SSL (CRL).

     >[!NOTE]
     >Najnowsze aktualizacje zredukowanie liczby portów wymaganych przez tę funkcję. W przypadku starszych wersji programu Azure AD Connect lub agenta uwierzytelniania, te porty były otwarte także: 5671, 8080, 9090, 9091, 9350, 9352 i 10100 10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Można agentów uwierzytelniania przekazywanego komunikują się za pośrednictwem serwera proxy ruchu wychodzącego sieci web?

Tak. Funkcja autowykrywania serwera Proxy sieci Web (WPAD) jest włączone w środowisku w środowisku lokalnym, agentów uwierzytelniania automatycznie próbę zlokalizowania i używać serwera proxy sieci web w sieci.

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Czy można zainstalować dwóch lub większej liczby agentów uwierzytelniania przekazywanego, na tym samym serwerze?

Nie, należy można zainstalować tylko jednego agenta uwierzytelniania przekazywanego na jednym serwerze. Jeśli chcesz skonfigurować wysoką dostępność, uwierzytelniania przekazywanego [postępuj zgodnie z instrukcjami w tym miejscu](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-4-ensure-high-availability).

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>Jak usunąć agenta uwierzytelniania przekazywanego?

Tak długo, jak działa Agent uwierzytelniania przekazywanego, pozostaje aktywna i stale obsługuje żądania logowania użytkownika. Jeśli chcesz odinstalować agenta uwierzytelniania, przejdź do strony **Panel sterowania -> programy -> programy i funkcje** i odinstalować zarówno **agenta: Microsoft Azure AD Connect uwierzytelniania** i  **Microsoft Azure AD Connect Agent Updater** programów.

Jeśli zaznaczysz bloku uwierzytelniania przekazywanego na [Centrum administracyjne usługi Azure Active Directory](https://aad.portal.azure.com) po ukończeniu poprzedniego kroku, zobaczysz agenta uwierzytelniania, przedstawiający jako **nieaktywny**. Jest to _Oczekiwano_. Agent uwierzytelniania są automatycznie usuwane z listy, po upływie kilku dni.

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>Mam już używanie usług AD FS do logowania do usługi Azure AD. Jak przełączyć go do uwierzytelniania przekazywanego?

Jeśli migrujesz z usług AD FS (lub inne technologie federacyjnych) do uwierzytelniania przekazywanego, zdecydowanie zalecamy, skorzystaj z naszego przewodnika wdrożenia są szczegółowo opublikowane [tutaj](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx).

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>Czy można używać uwierzytelniania przekazywanego, w środowisku wielu lasów usługi Active Directory?

Tak. Obsługiwane są środowisk wielu lasów, jeśli istnieją relacje zaufania lasu między lasami usługi Active Directory i routing sufiksów nazw jest poprawnie skonfigurowana.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Ile agentów uwierzytelniania przekazywanego trzeba zainstalować?

Instalowanie wielu agentów uwierzytelniania przekazywanego zapewnia [wysokiej dostępności](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-4-ensure-high-availability). Ale nie zapewnia ona deterministyczna Równoważenie obciążenia między agentów uwierzytelniania.

Należy wziąć pod uwagę maksymalne i średnie obciążenie żądań logowania, które powinny być widoczne w dzierżawie. Ekstrapolacji jednego agenta uwierzytelniania może obsługiwać uwierzytelnień 300 i 400 w standardowych 4-rdzeniowy Procesor, 16 GB pamięci RAM serwera na sekundę.

Aby oszacować ruchu sieciowego, użyj poniższych wskazówek ustalania rozmiaru:
- Każde żądanie ma rozmiar ładunku (0,5 k + 1 K * num_of_agents) bajtów; tzn. dane z usługi Azure AD do agenta uwierzytelniania. W tym miejscu "num_of_agents" oznacza, że liczba agentów uwierzytelniania zarejestrowany w dzierżawie.
- Każda odpowiedź ma rozmiar ładunku 1 KB; tzn. dane z agenta uwierzytelniania do usługi Azure AD.

W przypadku większości klientów dwóch lub trzech agentów uwierzytelniania w sumie są wystarczające dla wysokiej dostępności i wydajności. Blisko kontrolerów domeny do Zmniejszaj opóźnienia logowania, należy zainstalować agentów uwierzytelniania.

>[!NOTE]
>Istnieje limit systemowy wynoszący 12 agentów uwierzytelniania dla dzierżawy.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>Pierwszy Agent uwierzytelniania przekazywanego może być instalowany na innym serwerze niż ten, który jest uruchamiany program Azure AD Connect?

Nie, ten scenariusz jest _nie_ obsługiwane.

## <a name="why-do-i-need-a-cloud-only-global-administrator-account-to-enable-pass-through-authentication"></a>Dlaczego potrzebujesz konta administratora globalnego tylko w chmurze umożliwiające uwierzytelnianie przekazywane?

Zalecane jest, że można włączyć lub wyłączyć uwierzytelnianie przekazywane przy użyciu konta administratora globalnego tylko w chmurze. Dowiedz się więcej o [dodanie konta administratora globalnego tylko w chmurze](../active-directory-users-create-azure-portal.md). Już działa w ten sposób zapewnia, że możesz zablokowaniu dzierżawy.

## <a name="how-can-i-disable-pass-through-authentication"></a>Jak wyłączyć uwierzytelnianie przekazywane

Uruchom ponownie Kreatora programu Azure AD Connect i zmień metodę logowania użytkownika z uwierzytelniania przekazywanego do innej metody. Ta zmiana powoduje wyłączenie uwierzytelniania przekazywanego w ramach dzierżawy i odinstalowuje agenta uwierzytelniania z serwera. Należy ręcznie odinstalować agentów uwierzytelniania z innych serwerów.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>Co się stanie, gdy odinstalować agenta uwierzytelniania przekazywanego?

Po odinstalowaniu agenta uwierzytelniania przekazywanego z serwera powoduje serwera przestanie odpowiadać na żądania logowania. Aby uniknąć dzielenia funkcji logowania użytkowników w dzierżawie usługi, upewnij się, że inny Agent uwierzytelniania uruchomiony przed odinstalowaniem agenta uwierzytelniania przekazywanego.

## <a name="next-steps"></a>Kolejne kroki
- [Bieżące ograniczenia](active-directory-aadconnect-pass-through-authentication-current-limitations.md): Dowiedz się, jakie scenariusze są obsługiwane i te, które nie są.
- [Szybki start](active-directory-aadconnect-pass-through-authentication-quick-start.md): Rozpocznij pracę na uwierzytelnianie przekazywane usługi AD platformy Azure.
- [Migrowanie z usług AD FS do uwierzytelniania przekazywanego](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx) — szczegółowy przewodnik dotyczący migracji z usług AD FS (lub inne technologie federacyjnych) do uwierzytelniania przekazywanego.
- [Blokada Smart](../authentication/howto-password-smart-lockout.md): informacje dotyczące konfigurowania funkcji inteligentnej blokady na swoją dzierżawę, aby chronić kont użytkowników.
- [Rozbudowana technicznie](active-directory-aadconnect-pass-through-authentication-how-it-works.md): zrozumienie sposobu działania funkcji uwierzytelniania przekazywanego.
- [Rozwiązywanie problemów z](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Dowiedz się, jak rozwiązać typowe problemy przy użyciu funkcji uwierzytelniania przekazywanego.
- [Szczegółowe omówienie zabezpieczeń](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): Uzyskaj informacje szczegółowe techniczne na temat funkcji uwierzytelniania przekazywanego.
- [Usługa Azure bezproblemowe logowanie Jednokrotne AD](active-directory-aadconnect-sso.md): więcej informacji na temat tej dodatkowej funkcji.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Użyj Forum usługi Azure Active Directory do pliku sugestie dotyczące nowych funkcji.

