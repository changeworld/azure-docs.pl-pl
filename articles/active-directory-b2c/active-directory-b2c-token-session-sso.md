---
title: Tokenów, sesji i konfiguracji pojedynczego logowania jednokrotnego w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Tokenów, sesji i konfiguracji pojedynczego logowania jednokrotnego w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/16/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 35210a8e93b8437ea4d8c3b5f002c81c549d3afe
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444835"
---
# <a name="azure-active-directory-b2c-token-session-and-single-sign-on-configuration"></a>Usługa Azure Active Directory B2C: Tokenów, sesji i konfiguracji rejestracji jednokrotnej

Ta funkcja umożliwia szczegółową kontrolę na [-policy podstawę](active-directory-b2c-reference-policies.md), programu:

1. Okresy istnienia tokenów zabezpieczających wyemitowane przez usługi Azure Active Directory (Azure AD) B2C.
2. Okres istnienia sesji aplikacji sieci web zarządzanych przez usługę Azure AD B2C.
3. Formaty ważne oświadczenia w tokeny zabezpieczające emitowane przez usługę Azure AD B2C.
4. Logowanie jednokrotne (SSO) zachowanie w wielu aplikacji i zasad w swojej dzierżawy usługi B2C.

Wbudowane zasady umożliwia tej funkcji w katalogu usługi Azure AD B2C w następujący sposób:

1. Wykonaj następujące kroki, aby [przejdź do menu funkcji B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) w witrynie Azure portal.
2. Kliknij przycisk **zasady rejestracji lub logowania**. * Uwaga: Możesz użyć tej funkcji z każdym typem zasad, nie tylko w ** rejestracji lub logowania zasady ***.
3. Otwórz zasady, klikając go. Na przykład kliknąć **B2C_1_SiUpIn**.
4. Kliknij przycisk **Edytuj** w górnej części menu.
5. Kliknij przycisk **tokenu, sesji i konfiguracji rejestracji jednokrotnej**.
6. Wprowadź żądane zmiany. Więcej informacji na temat dostępnych właściwości w kolejnych sekcjach.
7. Kliknij przycisk **OK**.
8. Kliknij przycisk **Zapisz** górnej części menu.

## <a name="token-lifetimes-configuration"></a>Konfiguracja okresów istnienia tokenu

Usługa Azure AD B2C obsługuje [Protokół autoryzacji OAuth 2.0](active-directory-b2c-reference-protocols.md) umożliwiające bezpieczny dostęp do chronionych zasobów. Aby zaimplementować tę obsługę, usługi Azure AD B2C emituje różnych [tokenów zabezpieczających](active-directory-b2c-reference-tokens.md). Poniżej przedstawiono właściwości, których można użyć do zarządzania okresy istnienia tokenów zabezpieczających emitowane przez usługę Azure AD B2C:

* **Dostęp czas życia tokenu Identyfikatora (w minutach)**: okres istnienia tokenu elementu nośnego OAuth 2.0 używane do uzyskiwania dostępu do chronionego zasobu.
  * Domyślne = 60 minut.
  * Minimum (włącznie) = 5 minut.
  * Maksymalna (włącznie) = 1440 minut.
* **Czas życia tokenu odświeżania (dni)**: maksymalny okres, przed którym token odświeżania może służyć do uzyskania nowego dostępu lub tokenu Identyfikacyjnego (i opcjonalnie, nowego tokena odświeżania, jeśli aplikacja została udzielona `offline_access` zakresu).
  * Domyślne = 14 dni.
  * Minimum (włącznie) = 1 dzień.
  * Maksymalna (włącznie) = 90 dni.
* **Czas życia okna przewijania tokenu odświeżania (dni)**: po upłynięciu tego okresu użytkownik jest zmuszony do ponownego uwierzytelnienia, niezależnie od ostatniego okresu ważności tokenu odświeżania uzyskanego przez aplikację. Może być udostępniony tylko, jeśli przełącznik jest równa **powiązana**. Musi być większa lub równa **czas życia tokenu odświeżania (dni)** wartość. Jeśli przełącznik jest równa **niepowiązane**, nie może dostarczyć określonej wartości.
  * Domyślna = 90 dni.
  * Minimum (włącznie) = 1 dzień.
  * Maksymalna (włącznie) = 365 dni.

Poniżej przedstawiono kilka przypadków użycia, które można włączyć za pomocą tych właściwości:

* Pozwala użytkownikowi na czas nieokreślony, pozostanie zalogowany do aplikacji mobilnej, tak długo, jak jest ciągle aktywne w aplikacji. Można to zrobić, ustawiając **przesuwanego okna czas życia tokenu odświeżania (dni)** przełączyć się do **niepowiązane** zasad logowania.
* Spełnia wymagania dotyczące zgodności w branży zabezpieczeń i ustawiając okresów istnienia tokenu odpowiedni dostęp.

    > [!NOTE]
    > Te ustawienia nie są dostępne w przypadku zasad resetowania haseł.
    > 
    > 

## <a name="token-compatibility-settings"></a>Ustawienia zgodności tokenu

Wprowadziliśmy zmiany formatowania ważne oświadczenia w tokeny zabezpieczające emitowane przez usługę Azure AD B2C. Zostało to zrobione, aby poprawić obsługę standardowy protokół i lepsze współdziałanie z biblioteki tożsamości innych firm. Jednak aby uniknąć dzielenia istniejących aplikacji, utworzyliśmy następujące właściwości, aby umożliwić klientom na uczestnictwo zgodnie z potrzebami:

* **Oświadczenie wystawcy (iss)**: identyfikuje dzierżawy usługi Azure AD B2C, który wystawił token.
  * `https://login.microsoftonline.com/{B2C tenant GUID}/v2.0/`: To jest wartością domyślną.
  * `https://login.microsoftonline.com/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/`: Ta wartość obejmuje identyfikatory dla dzierżawy usługi B2C i zasad stosowanych w żądania tokenu. Jeśli aplikacji lub biblioteki musi być zgodne z usługi Azure AD B2C [specyfikacji protokołu OpenID Connect 1.0 odnajdywania](http://openid.net/specs/openid-connect-discovery-1_0.html), użyj tej wartości.
* **Oświadczenia podmiotu (pod)**: identyfikuje jednostkę, czyli użytkownik, dla której token określa informacje.
  * **Identyfikator obiektu**: jest to wartość domyślna. Wypełnia identyfikator obiektu użytkownika w katalogu do `sub` oświadczenia w tokenie.
  * **Nieobsługiwane**: jest to świadczona wyłącznie dla zgodności z poprzednimi wersjami, a firma Microsoft zaleca, aby przełączyć się do **ObjectID** jak tylko można.
* **Oświadczenie reprezentujące identyfikator zasad**: identyfikuje typ oświadczenia, do którego jest wypełniana identyfikator zasad używany w żądania tokenu.
  * **tfp**: jest to wartość domyślna.
  * **rejestru Azure container Registry**: jest to świadczona wyłącznie dla zgodności z poprzednimi wersjami, a firma Microsoft zaleca, aby przełączyć się do `tfp` jak tylko można.

## <a name="session-behavior"></a>Zachowanie sesji

Usługa Azure AD B2C obsługuje [protokołu uwierzytelniania OpenID Connect](active-directory-b2c-reference-oidc.md) włączania bezpiecznego logowania do aplikacji sieci web. Poniżej przedstawiono właściwości, których można użyć do zarządzania sesji aplikacji sieci web:

* **Aplikacja sieci Web okres istnienia sesji (w minutach)**: okres istnienia przechowywanych w przeglądarce użytkownika po pomyślnym uwierzytelnieniu pliku cookie sesji usługi Azure AD B2C.
  * Domyślne = 1440 minut.
  * Minimum (włącznie) = 15 minut.
  * Maksymalna (włącznie) = 1440 minut.
* **Limit czasu sesji aplikacji sieci Web**: Jeśli ten przełącznik jest równa **bezwzględne**, użytkownik jest zmuszony do ponownego uwierzytelnienia po upływie czasu określonego przez **aplikacji sieci Web okres istnienia sesji (w minutach)** upływa. Jeśli ten przełącznik jest równa **stopniowe** (ustawienie domyślne), użytkownik pozostaje zalogowany, tak długo, jak użytkownik jest stale aktywny w aplikacji sieci web.

Poniżej przedstawiono kilka przypadków użycia, które można włączyć za pomocą tych właściwości:

* Spełnia wymagania dotyczące zabezpieczeń i zgodności w branży, ustawiając sesji aplikacji sieci web odpowiednią okresy istnienia.
* Wymuś ponowne uwierzytelnianie po upływie określonego czasu ustawione podczas interakcji z części o wysokim poziomie zabezpieczeń aplikacji sieci web. 

    > [!NOTE]
    > Te ustawienia nie są dostępne w przypadku zasad resetowania haseł.
    > 
    > 

## <a name="single-sign-on-sso-configuration"></a>Konfiguracja logowania jednokrotnego (SSO)
Jeśli masz wiele aplikacji i zasad dzierżawy usługi B2C, możesz zarządzać interakcje użytkownika między nimi przy użyciu **konfiguracji rejestracji jednokrotnej** właściwości. Właściwość można ustawić na jeden z następujących ustawień:

* **Dzierżawy**: to ustawienie domyślne. Przy użyciu tego ustawienia umożliwia wielu aplikacji i zasad w swojej dzierżawy usługi B2C do udostępniania tej samej sesji użytkownika. Na przykład po użytkownik zaloguje się do aplikacji, zakupy firmy Contoso dany użytkownik może również bezproblemowo zalogować się do innego jeden, farmacji firmy Contoso, podczas uzyskiwania dostępu do jej.
* **Aplikacja**: dzięki temu można zachować sesję użytkownika wyłącznie dla aplikacji, niezależnie od innych aplikacji. Na przykład jeśli chcesz to użytkownikowi logowanie się farmacji Contoso (przy użyciu tych samych poświadczeń), nawet wtedy, gdy użytkownik jest już zalogowali się do zakupów firmy Contoso, inną aplikację na tym samym B2C dzierżawy. 
* **Zasady**: dzięki temu można zachować sesję użytkownika wyłącznie do zasad, niezależnie od aplikacji korzystania z niego. Na przykład jeśli użytkownik ma już zalogowany i ukończyć krok multi factor authentication (MFA), użytkownik może mieć dostęp do lepsze zabezpieczenia części wielu aplikacji tak długo, jak sesji powiązane z zasadami nie wygasa.
* **Wyłączone**: wymusza użytkownika ma być uruchamiana podróży całego użytkownika przy każdym wykonaniu zasad. Na przykład ta opcja pozwala wielu użytkowników zarejestrować się do aplikacji (w przypadku klasycznych udostępnionego), nawet podczas jeden użytkownik pozostaje zalogowany przez cały czas.

    > [!NOTE]
    > Te ustawienia nie są dostępne w przypadku zasad resetowania haseł.
    > 
    > 

