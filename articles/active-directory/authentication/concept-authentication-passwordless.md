---
title: Azure Active Directory logowanie bezhasło (wersja zapoznawcza)
description: Informacje o opcjach logowania bezhasło w celu Azure Active Directory przy użyciu kluczy zabezpieczeń FIDO2 lub aplikacji Microsoft Authenticator
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffe3d359dffea3278337de00bea42e0b959e10f6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264039"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Opcje uwierzytelniania bezhasło dla Azure Active Directory

Uwierzytelnianie wieloskładnikowe (MFA) to świetny sposób zabezpieczania organizacji, ale użytkownicy często uzyskują sfrustrowani z dodatkową warstwą zabezpieczeń na potrzeby zapamiętywania haseł. Metody uwierzytelniania bez hasła są wygodniejsze, ponieważ hasło jest usuwane i zastępowane przez użytkownika

|   | Coś, czego masz | Coś lub wiesz |
| --- | --- | --- |
| Logowanie bez hasła | Urządzenia, numery telefonów lub klucze zabezpieczeń systemu Windows 10 | Biometryczna lub PIN |

Każda organizacja ma inne potrzeby związane z uwierzytelnianiem. Firma Microsoft oferuje następujące trzy opcje uwierzytelniania bezhasła:

- Windows Hello dla firm
- Aplikacja Microsoft Authenticator
- FIDO2 klucze zabezpieczeń

![Uwierzytelnianie: zabezpieczenia i wygoda](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello dla firm

Funkcja Windows Hello dla firm jest idealna dla pracowników przetwarzających informacje, którzy mają własne Wyznaczeni komputery z systemem Windows. Biometryczna i kod PIN są bezpośrednio powiązane z komputerem użytkownika, co uniemożliwia dostęp od nikogo innego niż właściciel. Dzięki integracji infrastruktury kluczy publicznych (PKI) i wbudowanej obsłudze logowania jednokrotnego (SSO) usługa Windows Hello dla firm zapewnia wygodną metodę bezproblemowego uzyskiwania dostępu do zasobów firmy lokalnie i w chmurze.

[Przewodnik planowania](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) usługi Windows Hello dla firm może służyć do podejmowania decyzji dotyczących typu wdrożenia usługi Windows Hello dla firm i opcji, które należy wziąć pod uwagę.

## <a name="microsoft-authenticator-app"></a>Aplikacja Microsoft Authenticator

Zezwalaj, aby telefon pracownika stał się metodą uwierzytelniania bezhasłem. Aplikacja Microsoft Authenticator może już być używana jako wygodna opcja uwierzytelniania wieloskładnikowego oprócz hasła. Możesz również użyć aplikacji Authenticator jako opcji bezhaseł.

![Zaloguj się do przeglądarki Microsoft Edge przy użyciu aplikacji Microsoft Authenticator](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

Aplikacja Authenticator włącza każdy telefon z systemem iOS lub Android do silnego poświadczenia bez hasła. Użytkownicy mogą logować się do dowolnej platformy lub przeglądarki, uzyskując powiadomienie na telefonie, dopasowując liczbę wyświetlaną na ekranie na telefonie, a następnie używając ich biometrycznych (dotykowych lub ubocznych) lub numerów PIN, aby potwierdzić.

## <a name="fido2-security-keys"></a>FIDO2 klucze zabezpieczeń

Klucze zabezpieczeń FIDO2 to metoda uwierzytelniania bez hasła oparta na standardach, która może mieć dowolny współczynnik form. Szybka tożsamość w trybie online (FIDO) to otwarty standard uwierzytelniania bezhaseł. FIDO umożliwia użytkownikom i organizacjom korzystanie ze standardu do logowania się do swoich zasobów bez użycia nazwy użytkownika ani hasła przy użyciu zewnętrznego klucza zabezpieczeń lub klucza platformy wbudowanego w urządzenie.

W publicznej wersji zapoznawczej pracownicy mogą korzystać z kluczy zabezpieczeń, aby zalogować się do usługi Azure AD lub hybrydowych urządzeń z systemem Windows 10 dołączonych do usługi Azure AD i uzyskać Logowanie jednokrotne do swoich zasobów w chmurze i lokalnych. Użytkownicy mogą również zalogować się w obsługiwanych przeglądarkach. Klucze zabezpieczeń FIDO2 są świetną opcją dla przedsiębiorstw, które mają bardzo duże znaczenie dla bezpieczeństwa lub mają scenariusze lub pracowników, którzy nie chcą korzystać z telefonu jako drugiego czynnika.

![Zaloguj się do przeglądarki Microsoft Edge przy użyciu klucza zabezpieczeń](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

Chociaż istnieje wiele kluczy, które są FIDO2 certyfikowane przez usługi FIDO Alliance, firma Microsoft wymaga, aby niektóre opcjonalne rozszerzenia specyfikacji protokołu Klient-wystawca FIDO2 (CTAP) zostały zaimplementowane przez dostawcę w celu zapewnienia maksymalnego poziomu zabezpieczeń i najlepszego systemu.

Klucz zabezpieczeń **musi** implementować następujące funkcje i rozszerzenia z protokołu CTAP FIDO2, aby były zgodne z firmą Microsoft:

| # | Zaufanie funkcji/rozszerzenia | Dlaczego ta funkcja lub rozszerzenie jest wymagane? |
| --- | --- | --- |
| 1 | Klucz rezydentny | Ta funkcja umożliwia przenośny Klucz zabezpieczeń, w którym Twoje poświadczenia są przechowywane w kluczu zabezpieczeń. |
| 2 | Numer PIN klienta | Ta funkcja umożliwia ochronę poświadczeń przy użyciu drugiego czynnika i ma zastosowanie do kluczy zabezpieczeń, które nie mają interfejsu użytkownika. |
| 3 | hmac-secret | To rozszerzenie zapewnia możliwość zalogowania się na urządzeniu, gdy jest ono wyłączone lub w trybie samolotowym. |
| 4 | Wiele kont na jednostkę UZALEŻNIONą | Ta funkcja zapewnia, że można użyć tego samego klucza zabezpieczeń dla wielu usług, takich jak konto Microsoft i Azure Active Directory. |

Następujący dostawcy oferują klucze zabezpieczeń FIDO2 różnego rodzaju, które są znane jako zgodne z funkcją bezhaseł. Zachęcamy do ocenienia właściwości zabezpieczeń tych kluczy, kontaktując się z dostawcą oraz FIDO Alliance.

| Dostawca | Kontakt |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| INTERFEJSU | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| eWBM | [https://www.ewbm.com/support](https://www.ewbm.com/support) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |

> [!NOTE]
> Jeśli kupisz i planujesz korzystanie z kluczy zabezpieczeń opartych na NFC, musisz mieć obsługiwany czytnik NFC dla klucza zabezpieczeń. Czytnik NFC nie jest wymaganiem ani ograniczeniem platformy Azure. Aby uzyskać listę obsługiwanych czytników NFC, należy skontaktować się z dostawcą dla klucza zabezpieczeń opartego na NFC.

Jeśli jesteś dostawcą i chcesz uzyskać urządzenie na tej liście obsługiwanych urządzeń, skontaktuj się z firmą [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com).

## <a name="what-scenarios-work-with-the-preview"></a>Jakie scenariusze pracują z podglądem?

- Administratorzy mogą włączać metody uwierzytelniania bezhasło dla swojej dzierżawy
- Administratorzy mogą kierować wszystkich użytkowników lub wybierać użytkowników/grupy w ramach ich dzierżawy dla każdej metody
- Użytkownicy końcowi mogą rejestrować te metody uwierzytelniania bezhasło i zarządzać nimi w portalu konta
- Użytkownicy końcowi mogą logować się przy użyciu tych metod uwierzytelniania bez hasła
   - Aplikacja Microsoft Authenticator: działa w scenariuszach, w których używane jest uwierzytelnianie usługi Azure AD, w tym między wszystkimi przeglądarkami, podczas instalacji systemu Windows 10 (OOBE) i zintegrowanych aplikacji mobilnych w dowolnym systemie operacyjnym.
   - Klucze zabezpieczeń: Pracuj na ekranie blokady dla systemu Windows 10 i sieci Web w obsługiwanych przeglądarkach, takich jak Microsoft Edge (starsza i Nowa krawędź).

## <a name="next-steps"></a>Następne kroki

[Włącz opcje passwordlesss klucza zabezpieczeń FIDO2 w organizacji](howto-authentication-passwordless-security-key.md)

[Włącz opcje bezhaseł w organizacji, które nie są oparte na telefonie](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Linki zewnętrzne

[FIDO Alliance](https://fidoalliance.org/)

[Specyfikacja usługi CTAP FIDO2](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
