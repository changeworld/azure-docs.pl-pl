---
title: 'Azure AD Connect: Przekazywanego uwierzytelniania — często zadawane pytania | Dokumentacja firmy Microsoft'
description: Odpowiedzi na często zadawane pytania dotyczące uwierzytelniania przekazywanego Azure Active Directory
services: active-directory
keywords: Azure AD Connect przekazywanego uwierzytelniania, instalacji usługi Active Directory, wymaganych składników dla usługi Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2018
ms.author: billmath
ms.openlocfilehash: 7a817092581ef0be0b6be063282f7484c46ff094
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Azure Active Directory przekazywanego uwierzytelniania: Często zadawane pytania

W tym artykule opisano często zadawane pytania dotyczące usługi Azure Active Directory (Azure AD) przekazywanego uwierzytelniania. Sprawdzanie wstecz dla zaktualizowanej zawartości.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Która z metod, aby zalogować się do usługi Azure AD, uwierzytelniania przekazywanego hasła skrótów synchronizacji i Active Directory Federation Services (AD FS), należy wybrać?

Go zależy od lokalnego środowiska i wymagań organizacyjnych. Przegląd [opcje logowania użytkowników usługi Azure AD Connect](active-directory-aadconnect-user-signin.md) artykułu porównanie różnych metod usługi Azure AD logowania.

## <a name="is-pass-through-authentication-a-free-feature"></a>Jest bezpłatne funkcji uwierzytelniania przekazywanego?

Przekazywanego uwierzytelniania jest funkcją wolne. Nie wymagają żadnych wersji płatnej usługi Azure AD z niego korzystać.

## <a name="is-pass-through-authentication-available-in-the-microsoft-azure-germany-cloudhttpwwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>Jest dostępna w uwierzytelniania przekazywanego [chmury Microsoft Azure Niemcy](http://www.microsoft.de/cloud-deutschland) i [chmury Microsoft Azure dla instytucji rządowych](https://azure.microsoft.com/features/gov/)?

Nie. Przekazywanego uwierzytelniania jest dostępna tylko w wystąpieniu na całym świecie usługi Azure AD.

## <a name="does-conditional-accessactive-directory-conditional-access-azure-portalmd-work-with-pass-through-authentication"></a>Jest [dostępu warunkowego](../active-directory-conditional-access-azure-portal.md) pracy przy użyciu uwierzytelniania przekazywanego?

Tak. Wszystkie możliwości dostępu warunkowego, w tym Azure Multi-Factor Authentication działa przy użyciu uwierzytelniania przekazywanego.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Uwierzytelniania przekazywanego obsługuje "Alternatywnego Identyfikatora" jako nazwy użytkownika, a nie "userPrincipalName"?

Tak. Obsługuje uwierzytelniania przekazywanego `Alternate ID` jako nazwy użytkownika, gdy skonfigurowane w programie Azure AD Connect. Aby uzyskać więcej informacji, zobacz [Instalacja niestandardowa programu Azure AD Connect](active-directory-aadconnect-get-started-custom.md). Nie wszystkie aplikacje usługi Office 365 obsługują `Alternate ID`. Odwołuje się do instrukcji obsługi dokumentacji określonej aplikacji.

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>Synchronizacja skrótów haseł działać jako powrotu do uwierzytelniania przekazywanego?

Nie. Uwierzytelniania przekazywanego _nie_ automatycznie w tryb failover synchronizacji skrótu hasła. Działa tylko jako rezerwowe dla [scenariusze uwierzytelniania przekazywanego nie obsługuje obecnie](active-directory-aadconnect-pass-through-authentication-current-limitations.md#unsupported-scenarios). Aby uniknąć błędów logowania użytkownika, należy skonfigurować uwierzytelniania przekazywanego [wysokiej dostępności](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="can-i-install-an-azure-ad-application-proxymanage-appsapplication-proxymd-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Można zainstalować [serwera Proxy aplikacji usługi Azure AD](../manage-apps/application-proxy.md) łącznika na tym samym serwerze, ponieważ Agent uwierzytelniania przekazywanego?

Tak. Rebranded wersji agenta uwierzytelniania przekazywanego wersji 1.5.193.0 lub nowszym obsługuje tej konfiguracji.

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Które wersje programu Azure AD Connect i Agent uwierzytelniania przekazywanego potrzebujesz?

Ta funkcja działała, potrzebna wersja 1.1.486.0 później w celu Azure AD Connect i 1.5.58.0 lub nowszym w przypadku Agent uwierzytelniania przekazywanego. Całe oprogramowanie należy zainstalować na serwerach z systemem Windows Server 2012 R2 lub nowszym.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>Co się stanie, jeśli hasło tego użytkownika utracił ważność i podejmą próbę zalogowania przy użyciu uwierzytelniania przekazywanego?

Jeśli skonfigurowano [funkcji zapisywania zwrotnego haseł](../active-directory-passwords-update-your-own-password.md) dla określonego użytkownika, a jeśli użytkownik loguje się przy użyciu przekazywanego uwierzytelniania, można zmienić lub resetowanie haseł. Hasła są zapisywane z powrotem do lokalnej usługi Active Directory zgodnie z oczekiwaniami.

Jeśli nie skonfigurowano funkcję zapisywania zwrotnego haseł dla określonego użytkownika lub jeśli użytkownik nie ma prawidłową usługi Azure AD licencją, użytkownik nie może zaktualizować swoje hasło w chmurze. One nie można zaktualizować swoje hasło, nawet jeśli ich hasło wygasło. Użytkownik widzi zamiast tego komunikatu: "organizacji nie zezwala na zaktualizuj hasło w tej witrynie. Zaktualizuj go zgodnie z metodą zalecaną przez organizację lub poproś administratora w celu uzyskania pomocy." Użytkownik lub administrator musi zresetować swoje hasło w lokalnej usłudze Active Directory.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Sposób uwierzytelniania przekazywanego chroni jednak przed atakami siłowymi hasło?

Odczyt [uwierzytelniania przekazywanego Azure Active Directory: blokady inteligentnej](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) Aby uzyskać więcej informacji.

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>Co agentów uwierzytelniania przekazywanego komunikacji za pośrednictwem portów 80 i 443?

- Agenci uwierzytelniania tworzą żądania HTTPS przez port 443 dla wszystkich operacji funkcji.
- Agenci uwierzytelniania należy żądań HTTP za pośrednictwem portu 80 do pobierania listy odwołania certyfikatów SSL (CRL).

     >[!NOTE]
     >Najnowsze aktualizacje zredukowanie liczby porty wymagane przez funkcję. Jeśli masz starsze wersje usługi Azure AD Connect lub Agent uwierzytelniania, nie zamykaj tych portów również: 5671, 8080 9090, 9091, 9350, 9352 i 10100 10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Można agentów uwierzytelniania przekazywanego komunikują się za pośrednictwem serwera proxy ruchu wychodzącego sieci web?

Tak. Automatyczne odnajdowanie serwera Proxy sieci Web (WPAD) jest włączona w środowisku lokalnym, agentów uwierzytelniania automatycznie próbę zlokalizuj i używać serwera proxy sieci web w sieci.

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Na tym samym serwerze można zainstalować dwóch lub więcej agentów uwierzytelniania przekazywanego?

Nie, tylko można zainstalować jeden Agent uwierzytelniania przekazywanego na jednym serwerze. Jeśli chcesz skonfigurować uwierzytelniania przekazywanego wysokiej dostępności, postępuj zgodnie z instrukcjami [uwierzytelniania przekazywanego Azure Active Directory: Szybki start](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>Jak usunąć Agent uwierzytelniania przekazywanego?

Tak długo, jak działa Agent uwierzytelniania przekazywanego, pozostaje aktywna i stale obsługi żądania logowania użytkownika. Jeśli chcesz odinstalować agenta uwierzytelniania, przejdź do **Panel sterowania -> programy -> programy i funkcje** i odinstalować zarówno **agenta programu Microsoft Azure AD Connect uwierzytelniania** i  **Microsoft Azure AD Connect Agent Updater** programy.

Jeśli wybierzesz bloku uwierzytelniania przekazywanego na [Centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com) po ukończeniu poprzedniego kroku zobaczysz Agent uwierzytelniania przedstawiający jako **nieaktywne**. Jest to _Oczekiwano_. Agent uwierzytelniania jest automatycznie usuwane z listy, po upływie kilku dni.

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>Już używać usług AD FS do logowania do usługi Azure AD. Jak przełączać jej do uwierzytelniania przekazywanego?

Jeśli usługi AD FS zostały skonfigurowane jako metody do logowania za pomocą Kreatora programu Azure AD Connect, zmień metodę, których użytkownik używa do logowania do uwierzytelniania przekazywanego. Ta zmiana umożliwia użycie uwierzytelniania przekazywanego dla dzierżawcy i konwertuje _wszystkie_ Twojego Sfederowanych domen w domenach zarządzanych. Uwierzytelniania przekazywanego obsługuje wszystkie kolejne żądania logować się do dzierżawy. Obecnie nie istnieje sposób obsługiwane w ramach usługi Azure AD Connect być zastosowanie kombinacji uwierzytelniania przekazywanego i usług AD FS w różnych domenach.

Jeśli usługi AD FS został skonfigurowany jako metodę, aby zarejestrować się w _poza_ Kreator Azure AD Connect, zmień logowania użytkownika w metodę uwierzytelniania przekazywanego. Aby wprowadzić tę zmianę, z **nieskonfigurowane** opcji. Ta zmiana umożliwia przekazywanego uwierzytelniania w ramach dzierżawy, ale wszystkie Sfederowanych domen będą w dalszym ciągu używać usług AD FS dla logowania. Ręcznie konwertowanie niektóre lub wszystkie z tych domen federacyjnych do domen zarządzanych za pomocą programu PowerShell. Po wprowadzeniu tej zmiany *tylko* uwierzytelniania przekazywanego obsługuje wszystkie żądania do logowania się w domenach zarządzanych.

>[!IMPORTANT]
>Uwierzytelniania przekazywanego nie obsługuje logowania tylko w chmurze Azure AD users.

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>W środowisku wielu lasów usługi Active Directory można używać uwierzytelniania przekazywanego?

Tak. Obejmującego wiele lasów środowiskach są obsługiwane, jeśli istnieją relacje zaufania lasu między z lasów usługi Active Directory i w razie routing sufiksów nazw jest poprawnie skonfigurowana.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Ile agentów uwierzytelniania przekazywanego należy zainstalować?

Instalowanie wielu agentów uwierzytelniania przekazywanego zapewnia [wysokiej dostępności](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability). Ale nie zapewnia ona deterministyczna zrównoważenia obciążenia między agentami uwierzytelniania.

Należy wziąć pod uwagę maksymalne i średnie obciążenie żądań logowania, które powinny być widoczne w dzierżawie. Jako wzorca jednego agenta uwierzytelniania może obsługiwać 300 i 400 uwierzytelnienia w ciągu sekundy na standardowe 4 rdzenie procesora CPU, 16 GB pamięci RAM serwera.

Aby oszacować ruchu sieciowego, użyj poniższych wskazówek zmiany rozmiaru:
- Każde żądanie ma rozmiar ładunku (0,5 k + 1 K * num_of_agents) bajtów; np. dane z usługi Azure AD do uwierzytelnienia agenta. W tym miejscu "num_of_agents" oznacza, że liczba agentów uwierzytelniania zarejestrowane na Twojej dzierżawy.
- Każda odpowiedź ma rozmiar ładunku 1 KB; np. dane z agentem uwierzytelniania do usługi Azure AD.

Dla większości klientów dwóch lub trzech agentów uwierzytelniania łącznie są wystarczające dla wysokiej dostępności i wydajności. Należy zainstalować agentów uwierzytelniania bliski kontrolerów domeny do poprawy logowania opóźnienia.

>[!NOTE]
>Brak limitu systemowego 12 agentów uwierzytelniania dla każdego dzierżawcy.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>Pierwszy agenta uwierzytelniania przekazywanego można zainstalować na serwerze innym niż ten, który uruchamia Azure AD Connect?

Nie, ten scenariusz jest _nie_ obsługiwane.

## <a name="how-can-i-disable-pass-through-authentication"></a>Jak wyłączyć uwierzytelniania przekazywanego?

Uruchom ponownie Kreatora programu Azure AD Connect i zmień metodę logowania użytkownika z uwierzytelniania przekazywanego do innej metody. Ta zmiana powoduje wyłączenie uwierzytelniania przekazywanego dla dzierżawcy i odinstalowuje agenta uwierzytelniania z serwera. Należy ręcznie odinstalować agentów uwierzytelniania przez inne serwery.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>Co się stanie, gdy odinstalować agenta uwierzytelniania przekazywanego?

Po odinstalowaniu agenta uwierzytelniania przekazywanego z serwera powoduje serwer przestał akceptować żądania logowania. Aby uniknąć dzielenia funkcji logowania użytkowników w dzierżawie, upewnij się, że masz innego uwierzytelniania Agent uruchomiony przed odinstalowaniem agenta uwierzytelniania przekazywanego.

## <a name="next-steps"></a>Kolejne kroki
- [Bieżące ograniczenia](active-directory-aadconnect-pass-through-authentication-current-limitations.md): Dowiedz się, jakie scenariusze są obsługiwane i zostały.
- [Szybki start](active-directory-aadconnect-pass-through-authentication-quick-start.md): rozpocząć pracę na Azure AD przekazywanego uwierzytelniania.
- [Inteligentne blokady](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): informacje o sposobie konfigurowania funkcji blokady inteligentnej na swojej dzierżawy, aby chronić kont użytkowników.
- [Nowości techniczne](active-directory-aadconnect-pass-through-authentication-how-it-works.md): zrozumieć sposób działania funkcji uwierzytelniania przekazywanego.
- [Rozwiązywanie problemów z](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Dowiedz się, jak rozwiązać typowe problemy z funkcją uwierzytelniania przekazywanego.
- [Nowości zabezpieczeń](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): uzyskiwanie informacji głębokość technicznych dotyczących funkcji uwierzytelniania przekazywanego.
- [Azure AD SSO bezproblemowe](active-directory-aadconnect-sso.md): Dowiedz się więcej o tej funkcji uzupełniające.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): plik nowego żądania funkcji za pomocą na Forum usługi Azure Active Directory.

