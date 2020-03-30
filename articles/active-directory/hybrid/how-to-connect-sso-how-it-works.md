---
title: 'Usługa Azure AD Connect: bezproblemowe logowanie jednokrotne — jak to działa | Dokumenty firmy Microsoft'
description: W tym artykule opisano, jak działa funkcja bezproblemowego logowania usługi Azure Active Directory.
services: active-directory
keywords: co to jest usługa Azure AD Connect, instalowanie usługi Active Directory, wymagane składniki dla usługi Azure AD, logowania jednokrotnego, logowania jednokrotnego
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd4743bc38c3b2b4b9495b33535b4b73f48d1372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71176676"
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Bezproblemowe logowanie usługi Azure Active Directory: głębokie nurkowanie techniczne

W tym artykule przedstawiono szczegółowe informacje techniczne dotyczące działania funkcji bezproblemowego logowania jednokrotnego usługi Azure Active Directory.

## <a name="how-does-seamless-sso-work"></a>Jak działa bezproblemowe logowanie jednokrotne?

Ta sekcja zawiera trzy części:

1. Konfiguracja funkcji logowania jednokrotnego bez szwu.
2. Jak transakcja logowania jednego użytkownika w przeglądarce internetowej działa z bezproblemowym logowaniem jednokrotnym.
3. Jak transakcja logowania jednego użytkownika na kliencie macierzystym współpracuje z bezproblemowym logowaniem jednokrotnym.

### <a name="how-does-set-up-work"></a>Jak działa konfiguracja?

Bezproblemowe logowanie jednokrotne jest włączone przy użyciu usługi Azure AD Connect, jak pokazano [poniżej](how-to-connect-sso-quick-start.md). Podczas włączania funkcji występują następujące kroki:

- Konto komputera`AZUREADSSOACC`( ) jest tworzone w lokalnej usłudze Active Directory (AD) w każdym lesie usługi AD, który można zsynchronizować z usługą Azure AD (przy użyciu usługi Azure AD Connect).
- Ponadto wiele nazw głównych usług Kerberos (SPN) są tworzone do użycia podczas procesu logowania usługi Azure AD.
- Klucz odszyfrowywania konta komputera Kerberos jest bezpiecznie współużytkowane z usługą Azure AD. Jeśli istnieje wiele lasów ad, każde konto komputera będzie miało swój własny unikatowy klucz odszyfrowywania Protokołu Kerberos.

>[!IMPORTANT]
> Konto `AZUREADSSOACC` komputera musi być silnie chronione ze względów bezpieczeństwa. Tylko administratorzy domeny powinni mieć możliwość zarządzania kontem komputera. Upewnij się, że delegowanie protokołu Kerberos na koncie komputera jest wyłączone i `AZUREADSSOACC` że żadne inne konto w usłudze Active Directory nie ma uprawnień delegowania na koncie komputera.. Przechowuj konto komputera w jednostce organizacyjnej (OU), gdzie są one bezpieczne przed przypadkowymi usunięciami i gdzie tylko administratorzy domeny mają dostęp. Klucz odszyfrowywania protokołu Kerberos na koncie komputera również powinien być traktowany jako poufny. Zdecydowanie zaleca się [przewracanie klucza odszyfrowywania protokołu Kerberos](how-to-connect-sso-faq.md) konta `AZUREADSSOACC` komputera co najmniej co 30 dni.

Po zakończeniu konfiguracji bezproblemowe logowanie jednokrotne działa tak samo, jak każde inne logowanie, które używa zintegrowanego uwierzytelniania systemu Windows (IWA).

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>Jak działa logowanie w przeglądarce internetowej za pomocą bezproblemowego logowania jednokrotnego?

Przepływ logowania w przeglądarce internetowej jest następujący:

1. Użytkownik próbuje uzyskać dostęp do aplikacji sieci web (na przykład aplikacji Outlook Web App — https://outlook.office365.com/owa/) z urządzenia firmowego przyłączanego do domeny wewnątrz sieci firmowej.
2. Jeśli użytkownik nie jest jeszcze zalogowany, użytkownik jest przekierowywane do strony logowania usługi Azure AD.
3. Użytkownik wpisuje swoją nazwę użytkownika na stronie logowania usługi Azure AD.

   >[!NOTE]
   >W [przypadku niektórych aplikacji](./how-to-connect-sso-faq.md)pominięto kroki 2 & 3.

4. Korzystając z języka JavaScript w tle, usługa Azure AD wyzywa przeglądarkę za pośrednictwem nieautoryzowanej odpowiedzi 401, aby zapewnić bilet Protokołu Kerberos.
5. Przeglądarka z kolei żąda biletu z usługi `AZUREADSSOACC` Active Directory dla konta komputera (który reprezentuje usługę Azure AD).
6. Usługa Active Directory lokalizuje konto komputera i zwraca bilet Protokołu Kerberos do przeglądarki zaszyfrowanej przy użyciu klucza tajnego konta komputera.
7. Przeglądarka przekazuje bilet Protokołu Kerberos, który uzyskała z usługi Active Directory do usługi Azure AD.
8. Usługa Azure AD odszyfrowuje bilet Protokołu Kerberos, który zawiera tożsamość użytkownika zalogowanego do urządzenia firmowego przy użyciu wcześniej udostępnionego klucza.
9. Po przeprowadzeniu oceny usługa Azure AD zwraca token z powrotem do aplikacji lub prosi użytkownika o wykonanie dodatkowych dowodów, takich jak uwierzytelnianie wieloskładnikowe.
10. Jeśli logowanie użytkownika zakończy się pomyślnie, użytkownik może uzyskać dostęp do aplikacji.

Na poniższym diagramie przedstawiono wszystkie składniki i kroki.

![Bezproblemowe logowanie jednokrotne — przepływ aplikacji sieci Web](./media/how-to-connect-sso-how-it-works/sso2.png)

Bezproblemowe logowanie jednokrotne jest oportunistyczne, co oznacza, że jeśli się nie powiedzie, środowisko logowania powraca do swojego regularnego zachowania - czyli użytkownik musi wprowadzić swoje hasło, aby się zalogować.

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>Jak działa logowanie na kliencie macierzystym za pomocą bezproblemowego logowania jednokrotnego?

Przepływ logowania na kliencie macierzystym jest następujący:

1. Użytkownik próbuje uzyskać dostęp do aplikacji natywnej (na przykład klienta programu Outlook) z urządzenia firmowego przyłączanego do domeny w sieci firmowej.
2. Jeśli użytkownik nie jest jeszcze zalogowany, aplikacja natywna pobiera nazwę użytkownika z sesji systemu Windows urządzenia.
3. Aplikacja wysyła nazwę użytkownika do usługi Azure AD i pobiera punkt końcowy MEX dzierżawy WS-Trust. Ten punkt końcowy WS-Trust jest używany wyłącznie przez funkcję bezproblemowego logowania jednokrotnego i nie jest ogólną implementacją protokołu WS-Trust w usłudze Azure AD.
4. Następnie aplikacja wysyła zapytanie do punktu końcowego WS-Trust MEX, aby sprawdzić, czy zintegrowany punkt końcowy uwierzytelniania jest dostępny. Zintegrowany punkt końcowy uwierzytelniania jest używany wyłącznie przez funkcję bezproblemowego logowania jednokrotnego.
5. Jeśli krok 4 zakończy się pomyślnie, zostanie wydane wyzwanie Protokołu Kerberos.
6. Jeśli aplikacja jest w stanie pobrać bilet Protokołu Kerberos, przekazuje go do zintegrowanego punktu końcowego uwierzytelniania usługi Azure AD.
7. Usługa Azure AD odszyfrowuje bilet protokołu Kerberos i sprawdza jego poprawność.
8. Usługa Azure AD loguje użytkownika i wystawia token SAML do aplikacji.
9. Następnie aplikacja przesyła token SAML do punktu końcowego tokenu OAuth2 usługi Azure AD.
10. Usługa Azure AD sprawdza poprawność tokenu SAML i problemy z aplikacją token dostępu i token odświeżania dla określonego zasobu i tokenu identyfikatora.
11. Użytkownik uzyskuje dostęp do zasobu aplikacji.

Na poniższym diagramie przedstawiono wszystkie składniki i kroki.

![Bezproblemowe logowanie jednokrotne — przepływ aplikacji natywnej](./media/how-to-connect-sso-how-it-works/sso14.png)

## <a name="next-steps"></a>Następne kroki

- [**Szybki start**](how-to-connect-sso-quick-start.md) — wystartuj i uruchamiaj bezproblemowe logowanie jednokrotne usługi Azure AD.
- [**Najczęściej zadawane pytania**](how-to-connect-sso-faq.md) — odpowiedzi na często zadawane pytania.
- [**Rozwiązywanie problemów**](tshoot-connect-sso.md) — dowiedz się, jak rozwiązać typowe problemy z tą funkcją.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) — do składania nowych żądań funkcji.
