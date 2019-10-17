---
title: Azure Active Directory logowanie bezhasło (wersja zapoznawcza)
description: Logowanie bezhasło do usługi Azure AD przy użyciu kluczy zabezpieczeń FIDO2 lub aplikacji Microsoft Authenticator (wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89b52f356b112cff51105ed44c79788ee4542c6e
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430519"
---
# <a name="passwordless-authentication-options"></a>Opcje uwierzytelniania bezhasła

Uwierzytelnianie wieloskładnikowe (MFA) to świetny sposób zabezpieczania organizacji, ale użytkownicy uzyskują sfrustrowani z dodatkową warstwą na potrzeby zapamiętywania haseł. Metody uwierzytelniania bez hasła są wygodniejsze, ponieważ hasło jest usuwane i zastępowane przez użytkownika.

|   | Coś, czego masz | Coś lub wiesz |
| --- | --- | --- |
| Logowanie bez hasła | Urządzenia, numery telefonów lub klucze zabezpieczeń systemu Windows 10 | Biometryczna lub PIN |

Każda organizacja ma inne potrzeby związane z uwierzytelnianiem. Firma Microsoft oferuje trzy opcje uwierzytelniania bezhasła:

- Windows Hello dla firm 
- Aplikacja Microsoft Authenticator 
- FIDO2 klucze zabezpieczeń

![Uwierzytelnianie: zabezpieczenia i wygoda](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello dla firm 

Funkcja Windows Hello dla firm jest idealna dla pracowników przetwarzających informacje, którzy mają własne Wyznaczeni komputery z systemem Windows. Biometryczne i kod PIN są bezpośrednio powiązane z komputerem użytkownika, co uniemożliwia dostęp od nikogo innego niż właściciel. Dzięki integracji infrastruktury PKI i wbudowanej obsłudze logowania jednokrotnego (SSO) usługa Windows Hello dla firm zapewnia prostą i wygodną metodę bezproblemowego uzyskiwania dostępu do zasobów firmy lokalnie i w chmurze.

[Przewodnik planowania](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) usługi Windows Hello dla firm może służyć do podejmowania decyzji dotyczących typu wdrożenia usługi Windows Hello dla firm i opcji, które należy wziąć pod uwagę.

## <a name="microsoft-authenticator-app"></a>Aplikacja Microsoft Authenticator

Zezwalaj, aby telefon pracownika stał się metodą uwierzytelniania bezhasłem. Aplikacja Microsoft Authenticator może już być używana jako wygodna opcja uwierzytelniania wieloskładnikowego oprócz hasła. Teraz jest dostępna jako opcja bez hasła.

![Zaloguj się do przeglądarki Microsoft Edge przy użyciu aplikacji Microsoft Authenticator](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

Powoduje to włączenie dowolnego telefonu z systemem iOS lub Android w celu uzyskania silnych poświadczeń bez hasła przez umożliwienie użytkownikom logowania się do dowolnej platformy lub przeglądarki przez otrzymywanie powiadomienia na telefonie, odpowiadającego liczbie wyświetlanej na ekranie na telefonie, a następnie przy użyciu biometrycznych ( dotykiem lub stroną) lub numerem PIN, aby potwierdzić.

## <a name="fido2-security-keys"></a>FIDO2 klucze zabezpieczeń

Klucze zabezpieczeń FIDO2 to metoda uwierzytelniania bez hasła oparta na standardach, która może mieć dowolny współczynnik form. Szybka tożsamość w trybie online (FIDO) to otwarty standard uwierzytelniania bezhaseł. Dzięki temu użytkownicy i organizacje mogą wykorzystać standard do logowania się do swoich zasobów bez nazwy użytkownika ani hasła przy użyciu zewnętrznego klucza zabezpieczeń lub klucza platformy wbudowanego w urządzenie.

W publicznej wersji zapoznawczej pracownicy mogą korzystać z zewnętrznych kluczy zabezpieczeń, aby zalogować się do swoich Azure Active Directory przyłączonych do komputerów z systemem Windows 10 (w wersji 1809 lub nowszej) i uzyskać jednokrotne logowanie do zasobów w chmurze. Mogą także zalogować się do obsługiwanych przeglądarek.

![Zaloguj się do przeglądarki Microsoft Edge przy użyciu klucza zabezpieczeń](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

Chociaż istnieje wiele kluczy, które są FIDO2 certyfikowane przez usługi FIDO Alliance, firma Microsoft wymaga, aby niektóre opcjonalne rozszerzenia specyfikacji protokołu Klient-wystawca FIDO2 (CTAP) zostały zaimplementowane przez dostawcę w celu zapewnienia maksymalnego poziomu zabezpieczeń i najlepszego systemu.

Klucz zabezpieczeń **musi** implementować następujące funkcje i rozszerzenia z protokołu CTAP FIDO2, aby były zgodne z firmą Microsoft:

| # | Zaufanie funkcji/rozszerzenia | Dlaczego ta funkcja lub rozszerzenie jest wymagane? |
| --- | --- | --- |
| 1 | Klucz rezydentny | Ta funkcja umożliwia przenośny Klucz zabezpieczeń, w którym Twoje poświadczenia są przechowywane w kluczu zabezpieczeń. |
| 2 | Numer PIN klienta | Ta funkcja umożliwia ochronę poświadczeń przy użyciu drugiego czynnika i ma zastosowanie do kluczy zabezpieczeń, które nie mają interfejsu użytkownika. |
| 3 | HMAC-Secret | To rozszerzenie zapewnia możliwość zalogowania się na urządzeniu, gdy jest ono wyłączone lub w trybie samolotowym. |
| 4 | Wiele kont na jednostkę UZALEŻNIONą | Ta funkcja zapewnia, że można użyć tego samego klucza zabezpieczeń dla wielu usług, takich jak konto Microsoft i Azure Active Directory. |

Następujący dostawcy oferują klucze zabezpieczeń FIDO2 różnego rodzaju, które są znane jako zgodne z funkcją bezhaseł. Firma Microsoft zachęca klientów do ocenienia właściwości zabezpieczeń tych kluczy, kontaktując się z dostawcą oraz FIDO Alliance.

| Dostawca | Kontakt |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| INTERFEJSU | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| eWBM | [https://www.ewbm.com/page/sub1_5](https://www.ewbm.com/page/sub1_5) |

Jeśli jesteś dostawcą i chcesz uzyskać dostęp do urządzenia na tej liście, skontaktuj się z [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com).

Klucze zabezpieczeń FIDO2 są świetną opcją dla przedsiębiorstw, które mają bardzo duże znaczenie dla bezpieczeństwa lub mają scenariusze lub pracowników, którzy nie chcą korzystać z telefonu jako drugiego czynnika.

## <a name="what-scenarios-work-with-the-preview"></a>Jakie scenariusze pracują z podglądem?

- Administratorzy mogą włączać metody uwierzytelniania bezhasło dla swojej dzierżawy
- Administratorzy mogą kierować wszystkich użytkowników lub wybierać użytkowników/grupy w ramach ich dzierżawy dla każdej metody
- Użytkownicy końcowi mogą rejestrować te metody uwierzytelniania bezhasło i zarządzać nimi w portalu konta
- Użytkownicy końcowi mogą logować się przy użyciu tych metod uwierzytelniania bez hasła
   - Aplikacja Microsoft Authenticator: będzie działać w scenariuszach, w których używane jest uwierzytelnianie usługi Azure AD, w tym między wszystkimi przeglądarkami, podczas instalacji systemu Windows 10 (OOBE) i zintegrowanych aplikacji mobilnych w dowolnym systemie operacyjnym.
   - Klucze zabezpieczeń: będą działały na ekranie blokady systemu Windows 10 w wersji 1809 lub nowszej oraz w sieci Web w obsługiwanych przeglądarkach, takich jak Microsoft Edge.

## <a name="next-steps"></a>Następne kroki

[Włącz opcje passwordlesss klucza zabezpieczeń FIDO2 w organizacji](howto-authentication-passwordless-security-key.md)

[Włącz opcje bezhaseł w organizacji, które nie są oparte na telefonie](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Linki zewnętrzne

[FIDO Alliance](https://fidoalliance.org/)

[Specyfikacja usługi CTAP FIDO2](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
