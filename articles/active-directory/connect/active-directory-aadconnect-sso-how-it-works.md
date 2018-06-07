---
title: 'Azure AD Connect: Bezproblemowe logowanie jednokrotne — jak działa | Dokumentacja firmy Microsoft'
description: W tym artykule opisano, jak działa funkcja usługi Azure Active Directory bezproblemowe logowanie jednokrotne.
services: active-directory
keywords: Co to jest usługa Azure AD Connect, zainstaluj usługę Active Directory, wymaganych składników dla usługi Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: bcd9ec44eafd586648ba964c5cba248a184a8ec3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591565"
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Azure Active Directory bezproblemowe logowanie jednokrotne: techniczne nowości

Ten artykuł zawiera szczegółowe informacje techniczne, w sposób działania funkcji Azure Active Directory bezproblemowe rejestracji jednokrotnej (SSO bezproblemowe).

## <a name="how-does-seamless-sso-work"></a>Jak działa bezproblemowe logowanie Jednokrotne

Ta sekcja zawiera trzy części go:
1. Ustawienia funkcji bezproblemowe logowania jednokrotnego.
2. Jak transakcji pojedynczego użytkownika logowania w przeglądarce sieci web współpracuje z bezproblemowe logowania jednokrotnego.
3. Jak pojedynczego użytkownika logowania transakcji na komputerze klienckim natywnej współpracuje z bezproblemowe logowania jednokrotnego.

### <a name="how-does-set-up-work"></a>Jak skonfigurować pracy?

Bezproblemowe rejestracji Jednokrotnej jest włączone, za pomocą usługi Azure AD Connect, jak pokazano [tutaj](active-directory-aadconnect-sso-quick-start.md). Podczas włączania funkcji, zostaną wykonane następujące kroki:
- Konto komputera o nazwie `AZUREADSSOACC` (reprezentuje usługi Azure AD) jest tworzony w lokalnej usłudze Active Directory (AD).
- Klucz odszyfrowujący Kerberos konto komputera jest udostępniony w bezpieczny sposób z usługą Azure AD.
- Ponadto dwóch Kerberos głównej nazwy usługi (SPN) są tworzone do reprezentowania dwa adresy URL, które są używane podczas logowania w usłudze Azure AD.

>[!NOTE]
> Konto komputera i nazwy SPN Kerberos są tworzone w każdym lesie usługi AD synchronizowane z usługą Azure AD (za pomocą usługi Azure AD Connect) oraz dla użytkowników, którzy mają bezproblemowe logowania jednokrotnego. Przenieś `AZUREADSSOACC` konta komputera do organizacji jednostki (OU) przechowywania innych kont do zapewnienia, że odbywa się w taki sam sposób i nie zostanie usunięta.

>[!IMPORTANT]
>Zdecydowanie zalecamy, aby użytkownik [Przerzuć klucz odszyfrowujący Kerberos](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account) z `AZUREADSSOACC` konta komputera co najmniej 30 dni.

Po zakończeniu konfiguracji bezproblemowe logowanie Jednokrotne działa tak samo, jak wszystkie inne logowania, która używa zintegrowanego uwierzytelniania systemu Windows (IWA).

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>Sposób logowania w przeglądarce sieci web z pracą bezproblemowe logowanie Jednokrotne?

Przepływ logowania w przeglądarce sieci web jest następujący:

1. Użytkownik próbuje uzyskać dostęp do aplikacji sieci web (na przykład Outlook Web App - https://outlook.office365.com/owa/) z urządzenia firmowe przyłączonych do domeny w sieci firmowej.
2. Jeśli użytkownik nie jest już zalogowany, użytkownik jest przekierowany do strony logowania usługi Azure AD.
3. Użytkownik wpisze nazwy użytkownika do strony logowania usługi Azure AD.

  >[!NOTE]
  >Aby uzyskać [określonych aplikacji](./active-directory-aadconnect-sso-faq.md#what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso), są pomijane kroki 2 i 3.

4. Przy użyciu języka JavaScript w tle, usługa Azure AD będzie wymagał przeglądarki, za pośrednictwem nieautoryzowanego odpowiedzi 401, aby zapewnić biletu protokołu Kerberos.
5. Z kolei przeglądarki, żąda biletu z usługi Active Directory dla `AZUREADSSOACC` konta komputera (co reprezentuje usługi Azure AD).
6. Usługi Active Directory lokalizuje konto komputera i zwraca biletu protokołu Kerberos w przeglądarce zaszyfrowane za pomocą hasła do konta komputera.
7. Przeglądarka przekazuje biletu Kerberos, który uzyskał z usługi Active Directory do usługi Azure AD.
8. Usługi Azure AD odszyfrowuje biletu Kerberos, w tym tożsamości użytkownika zalogowaniem się do urządzeń firmowych, przy użyciu klucza wcześniej udostępniony.
9. Po dokonaniu oceny usługi Azure AD zwraca token do aplikacji lub z monitem o wykonania dodatkowych dowodów, takich jak uwierzytelnianie wieloskładnikowe.
10. Jeśli logowanie użytkowników zostanie nawiązane, użytkownik jest w stanie uzyskać dostęp do aplikacji.

Na poniższym diagramie przedstawiono wszystkie składniki i kroki do wykonania.

![Bezproblemowe Single Sign On - Web przepływu aplikacji](./media/active-directory-aadconnect-sso/sso2.png)

Bezproblemowe rejestracji Jednokrotnej jest oportunistyczne, co oznacza, że w przypadku niepowodzenia logowania w usługach powraca do regularnych zachowanie - tj, użytkownik musi wprowadzić hasło do logowania.

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>Sposób logowania na komputerze klienckim natywnej z pracą bezproblemowe logowanie Jednokrotne?

Przepływ logowania na komputerze klienckim natywnej wygląda następująco:

1. Użytkownik próbuje uzyskać dostęp do aplikacji natywnej (na przykład klient programu Outlook) z urządzenia firmowe przyłączonych do domeny w sieci firmowej.
2. Jeśli użytkownik nie jest już zalogowany, aplikacji natywnej pobiera nazwa użytkownika z sesji urządzenia z systemem Windows.
3. Aplikacja wysyła nazwę użytkownika do usługi Azure AD i pobiera punktu końcowego MEX WS-Trust swojej dzierżawy.
4. Następnie aplikacji za pośrednictwem punktu końcowego MEX WS-Trust, aby sprawdzić, czy zintegrowane uwierzytelnianie punkt końcowy jest dostępny.
5. Jeśli krok 4 zakończy się powodzeniem, zgłaszany jest żądanie protokołu Kerberos.
6. Jeśli aplikacja będzie mogła pobrać biletu Kerberos, przekazuje je do punktu końcowego zintegrowane uwierzytelnianie usługi Azure AD.
7. Usługi Azure AD odszyfrowuje biletu Kerberos, a następnie zweryfikuje go.
8. Usługi Azure AD podpisuje użytkownika w i wystawia SAML token do aplikacji.
9. Aplikacja przesyła następnie tokenu SAML token punktu końcowego usługi Azure AD OAuth2.
10. Usługi Azure AD sprawdza poprawność tokenu SAML, a token dostępu i token odświeżania dla określonego zasobu oraz identyfikator tokenu jest używany do aplikacji.
11. Użytkownik uzyskuje dostęp do zasobów aplikacji.

Na poniższym diagramie przedstawiono wszystkie składniki i kroki do wykonania.

![Bezproblemowe pojedynczego logowania - przepływu aplikacji natywnej](./media/active-directory-aadconnect-sso/sso14.png)

## <a name="next-steps"></a>Kolejne kroki

- [**Szybki Start** ](active-directory-aadconnect-sso-quick-start.md) — Uzyskaj i systemem Azure AD bezproblemowe Usługa rejestracji Jednokrotnej.
- [**Często zadawane pytania** ](active-directory-aadconnect-sso-faq.md) — odpowiedzi na często zadawane pytania.
- [**Rozwiązywanie problemów z** ](active-directory-aadconnect-troubleshoot-sso.md) — Dowiedz się, jak rozwiązać typowe problemy z funkcją.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) — w przypadku zgłoszenia żądania nowych funkcji.
