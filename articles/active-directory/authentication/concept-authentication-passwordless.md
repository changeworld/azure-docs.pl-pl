---
title: Usługi Active Directory bez hasła logowania do platformy Azure (wersja zapoznawcza)
description: Bez hasła logowania do usługi Azure AD przy użyciu kluczy zabezpieczeń FIDO2 lub aplikacji Microsoft Authenticator (wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d80b359be0a6249327ba1ba1d51ffbc330bb073
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712087"
---
# <a name="what-is-passwordless"></a>Co to jest bez hasła?

Uwierzytelnianie wieloskładnikowe (MFA) jest to doskonały sposób, aby zabezpieczyć Twoją organizację, ale użytkownicy uzyskują niezadowoleni z dodatkową warstwę na potrzeby pamiętania swoich haseł. Metod uwierzytelniania bez hasła są bardziej wygodne, ponieważ hasło jest usunięty i zastąpiony parametrem coś, co masz oraz coś, co jest lub coś, które znasz.

|   | Coś, co masz | Coś się lub znasz |
| --- | --- | --- |
| Logowanie bez hasła | Klucz telefonu lub zabezpieczeń | Biometryczne lub PIN |

Wiemy, że każda organizacja ma inne potrzeby, jeśli chodzi o uwierzytelnianie. Obecnie firma Microsoft oferuje Windows Hello, naszego premier środowiska bez hasła dla komputerów PC Windows. Dodajemy nowe poświadczenia do rodziny bez hasła: Aplikacja Microsoft Authenticator i FIDO2 kluczy zabezpieczeń.

## <a name="microsoft-authenticator-app"></a>Aplikacja Microsoft Authenticator

Zezwalaj na telefon przez pracownika, aby stać się metodę uwierzytelniania bez hasła. Możesz już z nich korzystali z aplikacji Microsoft Authenticator jako opcję wygodne uwierzytelnianie wieloskładnikowe, oprócz hasła. Ale teraz jest ona dostępna jako opcja bez hasła.

Zmieni się z systemem iOS ani telefon z systemem Android w silne, bez hasła poświadczenia, pozwalając użytkownikom logować się do dowolnej platformy i przeglądarki przez wprowadzenie powiadomienie na telefon, dopasowanie numeru wyświetlane na ekranie, aby na telefon, a następnie użycie ich biometryczne ( w ogóle lub przeprowadzenie identyfikacji twarzy) lub numeru PIN, aby potwierdzić.

## <a name="fido2-security-keys"></a>FIDO2 kluczy zabezpieczeń

Klucze zabezpieczeń FIDO2 są unphishable metody oparte na standardach bez hasła uwierzytelniania, która może okazać się dowolnym współczynnika postaci. Szybkie tożsamości Online FIDO () jest standardem otwartym uwierzytelniania bez hasła. Umożliwia użytkownikom i organizacjom wykorzystać standardowe Zaloguj się do swoich zasobów bez nazwy użytkownika i hasło przy użyciu klucza zabezpieczeń zewnętrznych lub klucza platformy wbudowane w urządzeniu.

W publicznej wersji zapoznawczej pracownicy można użyć kluczy zabezpieczeń zewnętrznych do logowania się na swoich komputerach usługi Azure Active Directory przyłączone do systemu Windows 10 (wersja 1809 lub nowszej) i uzyskać logowania jednokrotnego do zasobów w chmurze. Również zalogować się do obsługiwanych przeglądarek.

Dostępnych jest wiele kluczy, które są certyfikowane przez FIDO Alliance FIDO2, firma Microsoft wymaga pewnych opcjonalne rozszerzenia specyfikacji FIDO2 CTAP realizowane przez dostawcę, aby zapewnić maksymalne bezpieczeństwo i najlepsze środowisko.

Klucz zabezpieczeń **musi** zaimplementować następujące funkcje i rozszerzenia z protokół FIDO2 CTAP zgodny z programem Microsoft:

| # | Funkcja / rozszerzenie zaufania | Dlaczego jest to wymagane? |
| --- | --- | --- |
| 1 | Rezydentna klucza | Ta funkcja umożliwia klucz zabezpieczeń, aby być przenośne, przy czym Twoje poświadczenia są przechowywane w kluczu zabezpieczeń. |
| 2 | Numer pin klienta | Ta funkcja pozwala chronić poświadczenia za pomocą drugiego składnika i ma zastosowanie do kluczy zabezpieczeń, które nie mają interfejsu użytkownika. |
| 3 | hmac-secret | To rozszerzenie zapewnia, że możesz zalogować się do urządzenia po trybie offline lub w tryb samolotowy. |
| 4 | Wiele kont na jednostki Uzależnionej | Ta funkcja zapewnia, że ten sam klucz zabezpieczeń można użyć wielu usług, takich jak Account Microsoft i usługi Azure Active Directory. |

Następujący dostawcy oferują kluczy zabezpieczeń FIDO2 o różnych rozmiarach, które są znane, aby były zgodne ze środowiskiem paswordless. Firma Microsoft zachęca klientów do oceny właściwości zabezpieczeń te klucze, kontaktując się z dostawcą, a także FIDO Alliance.

| Dostawca | Skontaktuj się z |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| UKRYTO | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://ensurity.com/contact-us.html](https://ensurity.com/contact-us.html) |
| eWBM | [https://www.ewbm.com/page/sub1_5](https://www.ewbm.com/page/sub1_5) |

Jeśli jesteś dostawcą i chcesz, aby urządzenie było na tej liście, skontaktuj się z [ Fido2Request@Microsoft.com ](mailto:Fido2Request@Microsoft.com).

Klucze zabezpieczeń FIDO2 są to doskonałe rozwiązanie dla przedsiębiorstw, które są bardzo zabezpieczenia poufnych lub mają scenariuszy lub pracowników, którzy nie są gotowi, lub można używać telefonu jako drugi składnik.

## <a name="what-scenarios-work-with-the-preview"></a>Co to scenariusze odnoszą się do korzystania z wersji zapoznawczej?

1. Administratorzy mogą włączyć metod uwierzytelniania bez hasła dla swojej dzierżawy
1. Administratorzy mogą docelowy wszystkich użytkowników lub Wybieranie użytkowników i grup w ramach ich dzierżawy dla każdej metody
1. Użytkownicy końcowi można zarejestrować i zarządzać nimi tych metod uwierzytelniania bez hasła w portalu konta
1. Użytkownicy końcowi mogą zalogować się przy użyciu tych metod uwierzytelniania bez hasła
   1. Aplikacja Microsoft Authenticator: Będzie pracy w każdym scenariuszu, gdy jest używane uwierzytelnianie usługi Azure AD, we wszystkich przeglądarkach, w tym w systemie Windows 10 się instalacji pole (OOBE) i za pomocą zintegrowanych aplikacji mobilnych w dowolnym systemie operacyjnym.
   1. Klucze zabezpieczeń: Będzie działać na ekranie blokady w systemie Windows 10 w wersji 1809 lub nowszej i w sieci web w obsługiwanych przeglądarek, takich jak Microsoft Edge.

## <a name="next-steps"></a>Kolejne kroki

[Włącz opcje bez hasła w Twojej organizacji](howto-authentication-passwordless-enable.md)

### <a name="external-links"></a>Linki zewnętrzne

[FIDO Alliance](https://fidoalliance.org/)

[Specyfikacja FIDO2 CTAP](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
