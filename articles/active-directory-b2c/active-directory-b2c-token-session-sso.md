---
title: Token, sesji i pojedynczego logowania jednokrotnego konfiguracji — usługi Azure AD B2C | Dokumentacja firmy Microsoft
description: Token, sesji i Konfiguracja pojedynczego logowania jednokrotnego w usłudze Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.openlocfilehash: 925313b6f2a00826f2ec8086457315c60f70b007
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="azure-active-directory-b2c-token-session-and-single-sign-on-configuration"></a>Usługa Azure Active Directory B2C: Tokenu sesji i konfiguracji rejestracji jednokrotnej

Ta funkcja umożliwia szczegółową kontrolę na [podstawy-policy](active-directory-b2c-reference-policies.md), z:

1. Okres istnienia tokenów zabezpieczających emitowane przez usługi Azure Active Directory (Azure AD) B2C.
2. Okresy istnienia sesji aplikacji sieci web, zarządzane przez usługę Azure AD B2C.
3. Formaty ważne oświadczenia w tokenach zabezpieczających emitowane przez usługę Azure AD B2C.
4. Logowanie jednokrotne (SSO) zachowanie na różnych aplikacji i zasad w dzierżawcy usługi B2C.

Wbudowane zasady służy tej funkcji w katalogu usługi Azure AD B2C w następujący sposób:

1. Wykonaj następujące kroki, aby [przejdź do menu funkcji B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) w portalu Azure.
2. Kliknij przycisk **zasad rejestracji i logowania**. * Uwaga: Możesz użyć tej funkcji na każdego typu zasad, nie tylko w ** rejestracji i logowania zasady ***.
3. Otwórz zasadę, klikając go. Na przykład kliknięcie **B2C_1_SiUpIn**.
4. Kliknij przycisk **Edytuj** w górnej części menu.
5. Kliknij przycisk **Token, sesji i konfiguracji rejestracji jednokrotnej**.
6. Wprowadź żądane zmiany. Więcej informacji na temat dostępnych właściwości w kolejnych sekcjach.
7. Kliknij przycisk **OK**.
8. Kliknij przycisk **zapisać** górnej części menu.

## <a name="token-lifetimes-configuration"></a>Konfiguracja okresy istnienia tokenu

Usługa Azure AD B2C obsługuje [protokół OAuth 2.0](active-directory-b2c-reference-protocols.md) umożliwiający bezpieczny dostęp do chronionych zasobów. Aby zaimplementować tę obsługę, usługi Azure AD B2C emituje różnych [tokeny zabezpieczające](active-directory-b2c-reference-tokens.md). Są to właściwości, których można użyć do zarządzania okresy istnienia tokenów zabezpieczających emitowane przez usługę Azure AD B2C:

* **Dostęp do & Identyfikator tokenu okresy istnienia (w minutach)**: okres istnienia tokenu elementu nośnego OAuth 2.0 używane do uzyskiwania dostępu do chronionych zasobów.
  * Domyślne = 60 minut.
  * Minimalna (włącznie) = 5 minut.
  * Maksymalna (włącznie) = 1440 minut.
* **Okres istnienia tokenu odświeżania (dni)**: okres maksymalny czas, przed którym token odświeżania może służyć do uzyskania dostępu do nowych lub identyfikator tokenu (i opcjonalnie nowy token odświeżania, jeśli przyznano aplikacji `offline_access` zakresu).
  * Domyślne = 14 dni.
  * Minimalna (włącznie) = 1 dzień.
  * Maksymalna (włącznie) = 90 dni.
* **Odśwież okres istnienia tokenu przesuwanego okna (w dniach)**: po tym okresie użytkownik będzie zmuszony do ponownego uwierzytelnienia, niezależnie od okresu ważności ostatniego odświeżenia tokenu uzyskaną przez aplikację. Może być udostępniony tylko, jeśli przełącznik ma ustawioną wartość **Bounded**. Musi być większa lub równa **okres istnienia tokenu odświeżania (dni)** wartość. Jeśli przełącznik ma ustawioną wartość **Unbounded**, nie można podać określoną wartość.
  * Domyślna = 90 dni.
  * Minimalna (włącznie) = 1 dzień.
  * Maksymalna (włącznie) = 365 dni.

Oto kilka przypadków użycia, które można włączyć za pomocą tych właściwości:

* Umożliwia użytkownikowi być zarejestrowany w aplikacji mobilnej, jak długo użytkownik jest ciągle aktywne w aplikacji. Można to zrobić przez ustawienie **odświeżania przesuwanego okna okres istnienia tokenu (dni)** przełączyć się do **Unbounded** w zasadach rejestracji.
* Spełnia wymagania dotyczące zgodności z branży zabezpieczeń i ustawiając okresy istnienia tokenu dostępu.

    > [!NOTE]
    > Te ustawienia nie są dostępne dla zasady resetowania hasła.
    > 
    > 

## <a name="token-compatibility-settings"></a>Ustawienia zgodności tokenu

Wprowadziliśmy zmiany formatowania do ważne oświadczenia w tokenach zabezpieczających emitowane przez usługę Azure AD B2C. To zostało zrobione poprawy naszych Obsługa standardowego protokołu i lepsze współdziałanie z bibliotek tożsamości innych firm. Jednak aby uniknąć dzielenia istniejących aplikacji, utworzyliśmy umożliwiają klientom w razie potrzeby następujących właściwości:

* **Oświadczenia wystawcy (iss)**: identyfikuje dzierżawy usługi Azure AD B2C, która wystawiła token.
  * `https://login.microsoftonline.com/{B2C tenant GUID}/v2.0/`: Jest to wartość domyślna.
  * `https://login.microsoftonline.com/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/`: Ta wartość zawiera identyfikatory dla dzierżawy B2C i zasad stosowanych w żądania tokenu. Jeśli dana aplikacja lub biblioteka musi usługi Azure AD B2C, aby było zgodne z [spec OpenID Connect 1.0 odnajdywania](http://openid.net/specs/openid-connect-discovery-1_0.html), użyj tej wartości.
* **Oświadczenia podmiotu (sub)**: identyfikuje jednostki, np. użytkownika, dla którego token deklaracji rozkazujących informacji.
  * **Identyfikator obiektu**: jest to wartość domyślna. Wypełnia identyfikator obiektu użytkownika w katalogu do `sub` oświadczenia w tokenie.
  * **Nieobsługiwane**: jest to dostępna wyłącznie dla zgodności z poprzednimi wersjami, a firma Microsoft zaleca, aby przełączyć się do **ObjectID** jak tylko można.
* **Oświadczenia reprezentujący identyfikator zasad**: identyfikuje typ oświadczenia, do którego jest wypełniana identyfikator zasad używana w żądania tokenu.
  * **tfp**: jest to wartość domyślna.
  * **acr**: jest to dostępna wyłącznie dla zgodności z poprzednimi wersjami, a firma Microsoft zaleca, aby przełączyć się do `tfp` jak tylko można.

## <a name="session-behavior"></a>Zachowanie sesji

Usługa Azure AD B2C obsługuje [protokołu uwierzytelniania OpenID Connect](active-directory-b2c-reference-oidc.md) umożliwiających bezpieczne logowanie do aplikacji sieci web. Są to właściwości, które można użyć do zarządzania sesjami aplikacji sieci web:

* **Aplikacja sieci Web okres istnienia sesji (w minutach)**: czas istnienia pliku cookie sesji usługi Azure AD B2C przechowywanych w przeglądarce po pomyślnym uwierzytelnieniu.
  * Domyślne = 1440 minut.
  * Minimalna (włącznie) = 15 minut.
  * Maksymalna (włącznie) = 1440 minut.
* **Limit czasu sesji aplikacji sieci Web**: Jeśli ten parametr ma ustawioną wartość **bezwzględną**, użytkownik będzie zmuszony do ponownego uwierzytelnienia po upływie czasu określonego przez **aplikacji sieci Web okres istnienia sesji (w minutach)** upływa. Jeśli ten parametr ma ustawioną wartość **stopniowych** (ustawienie domyślne), użytkownik pozostanie zalogowany, tak długo, jak długo użytkownik jest ciągle aktywne w aplikacji sieci web.

Oto kilka przypadków użycia, które można włączyć za pomocą tych właściwości:

* Spełnia wymagania dotyczące zabezpieczeń i zgodności z branży przez ustawienie sesji aplikacji sieci web odpowiednie okresy istnienia.
* Wymusić ponowne uwierzytelnianie po ustawionym okresie podczas interakcji z częścią określającą wysokich zabezpieczeń aplikacji sieci web. 

    > [!NOTE]
    > Te ustawienia nie są dostępne dla zasady resetowania hasła.
    > 
    > 

## <a name="single-sign-on-sso-configuration"></a>Konfiguracja rejestracji jednokrotnej (SSO)
Jeśli masz wiele aplikacji i zasad w dzierżawie usługi B2C można zarządzać interakcji użytkowników między nimi przy użyciu **konfiguracji rejestracji jednokrotnej** właściwości. Można ustawić właściwości do jednego z następujących ustawień:

* **Dzierżawy**: jest to ustawienie domyślne. Za pomocą tego ustawienia umożliwia wiele aplikacji i zasad w dzierżawie usługi B2C na współużytkowanie tej samej sesji użytkownika. Na przykład gdy użytkownik zaloguje się do aplikacji, zakupów firmy Contoso dany użytkownik może również bezproblemowo Zaloguj się do innego jeden, farmacji firmy Contoso, podczas dostępu do niego.
* **Aplikacja**: pozwala to zachować sesję użytkownika wyłącznie dla aplikacji, niezależnie od innych aplikacji. Na przykład jeśli chcesz użytkownika do logowania się na farmacji Contoso (z tymi samymi poświadczeniami), nawet jeśli użytkownik jest już zarejestrowany do zakupów firmy Contoso, inną aplikację na tym samym B2C dzierżawy. 
* **Zasady**: pozwala to zachować sesję użytkownika wyłącznie do zasad, niezależnie od aplikacji za jego pomocą. Na przykład jeśli użytkownik ma już zalogowany i ukończyć kroku multi Multi-Factor authentication (MFA), użytkownik może uzyskać dostęp do wyższy poziom zabezpieczeń części wiele aplikacji tak długo, jak sesji powiązane z zasadami nie wygasa.
* **Wyłączone**: wymusza użytkownika ma być uruchamiana w podróży całego użytkownika przy każdym wykonaniu zasad. Na przykład dzięki temu wielu użytkownikom logowanie do aplikacji (w scenariuszu z udostępnionego pulpitu), nawet podczas jeden użytkownik pozostanie zalogowany podczas przez cały czas.

    > [!NOTE]
    > Te ustawienia nie są dostępne dla zasady resetowania hasła.
    > 
    > 

