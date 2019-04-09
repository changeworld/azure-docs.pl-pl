---
title: Dowiedz się więcej o protokoły autoryzacji obsługiwanych przez usługę Azure AD w wersji 2.0 | Dokumentacja firmy Microsoft
description: Przewodnik dotyczący protokołów obsługiwanych przez punkt końcowy usługi Azure AD w wersji 2.0.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5fb4fa1b-8fc4-438e-b3b0-258d8c145f22
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c56970091da74cfc389d60ad91f430fcb64d4bba
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59266974"
---
# <a name="v20-protocols---oauth-20-and-openid-connect"></a>w wersji 2.0 protokołów: OAuth 2.0 i OpenID Connect

Punktu końcowego v2.0, można użyć usługi Azure Active Directory (Azure AD) dla tożsamości as-a-service za pomocą ze standardowych protokołów uwierzytelniania OpenID Connect i OAuth 2.0. Gdy usługa jest zgodny ze standardami, może to być niewielkich różnic między implementacjami dwóch tych protokołów. W tym miejscu informacje będą przydatne, jeśli istnieje możliwość pisania kodu przez bezpośrednie wysyłanie i obsługa żądań HTTP lub użyć innej biblioteki typu open source, a nie przy użyciu jednej z naszych [bibliotek typu open-source](reference-v2-libraries.md).

> [!NOTE]
> Nie wszystkie scenariusze i funkcje usługi Azure AD są obsługiwane przez punkt końcowy w wersji 2.0. Aby ustalić, należy użyć punktu końcowego v2.0, przeczytaj temat [ograniczenia v2.0](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Podstawy

W prawie wszystkie przepływy protokołu OAuth 2.0 i OpenID Connect istnieją cztery strony związane z programu exchange:

![Role uwierzytelniania OAuth 2.0](../../media/active-directory-v2-flows/protocols_roles.png)

* **Serwera autoryzacji** jest punktem końcowym v2.0 i odpowiada za zagwarantowanie, tożsamość użytkownika, przyznawania i odbieranie prawa dostępu do zasobów i wydawania tokenów. Serwer autoryzacji, znany także jako dostawcy tożsamości — bezpieczną obsługę nic wspólnego z informacji o użytkowniku, ich dostęp i relacje zaufania między stronami w przepływie.
* **Właściciel zasobu** jest zazwyczaj użytkownika końcowego. To strona, która jest właścicielem danych i zasilania, aby umożliwić firmom dostęp do tych danych lub zasobu.
* **Klienta OAuth** aplikacja, identyfikowane przez jego identyfikator aplikacji. Klient uwierzytelniania OAuth jest zazwyczaj innych firm, które użytkownik końcowy korzysta z, a żądania tokenów z serwera autoryzacji. Klient musi otrzymać uprawnienia dostępu do zasobu przez właściciela zasobów.
* **Serwer zasobów** jest, gdzie znajduje się zasób lub danych. Zaufany serwer autoryzacji do bezpiecznego uwierzytelniania i autoryzacji klienta OAuth i używa tokenów dostępu do elementu nośnego, aby upewnić się, że można udzielić dostępu do zasobu.

## <a name="app-registration"></a>Rejestracja aplikacji

Każda aplikacja, która chce zaakceptować zarówno osobistych i kont służbowych musi być zarejestrowana w nowej **rejestracje aplikacji (wersja zapoznawcza)** środowiska w [witryny Azure portal](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) przed jego zaakceptowaniem tych użytkowników przy użyciu protokołu OAuth 2.0 lub OpenID Connect. Proces rejestracji aplikacji będzie zbierać i przypisać kilka wartości do swojej aplikacji:

* **Identyfikator aplikacji** , który jednoznacznie identyfikuje aplikację
* A **identyfikator URI przekierowania** lub **identyfikator pakietu** który może służyć do kierowania odpowiedzi z powrotem do aplikacji
* Kilka innych wartości specyficznych dla danego scenariusza.

Aby uzyskać więcej szczegółowych informacji, zapoznaj się z procedurą [rejestracji aplikacji](quickstart-v2-register-an-app.md).

## <a name="endpoints"></a>Punkty końcowe

Po zarejestrowaniu aplikacji komunikuje się z usługą Azure AD, wysyłając żądania do punktu końcowego v2.0:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Gdzie `{tenant}` może mieć jedną z czterech różnych wartości:

| Wartość | Opis |
| --- | --- |
| `common` | Pozwala użytkownikom z osobistych kont Microsoft i konto służbowe z usługi Azure AD, aby zalogować się do aplikacji. |
| `organizations` | Umożliwia tylko użytkownicy z konto służbowe z usługi Azure AD, aby zalogować się do aplikacji. |
| `consumers` | Umożliwia tylko użytkownicy z osobistych kont Microsoft (MSA), aby zalogować się do aplikacji. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` lub `contoso.onmicrosoft.com` | Umożliwia tylko użytkownicy z kontami służbowe z określonej usługi Azure AD dzierżawy, aby zalogować się do aplikacji. Przyjazna nazwa domeny dzierżawy usługi Azure AD albo identyfikator GUID dzierżawy może służyć. |

Aby dowiedzieć się, jak korzystać z tych punktów końcowych, wybierz typ danej aplikacji w [protokołów](#protocols) sekcji i skorzystaj z linków, aby uzyskać więcej informacji.

> [!TIP]
> Dowolna aplikacja zarejestrowana w usłudze Azure AD można używać punktu końcowego v2.0, nawet, jeśli nie zarejestruje się na kontach osobistych.  Dzięki temu można migrować istniejące aplikacje, aby w wersji 2.0 i [MSAL](reference-v2-libraries.md) bez potrzeby ponownego tworzenia aplikacji.  

## <a name="tokens"></a>Tokeny

Implementacja v2.0 OAuth 2.0 i OpenID Connect, należy zwiększone użycie tokenów elementu nośnego, łącznie z tokenów elementu nośnego reprezentowane jako tokenów Jwt. Token elementu nośnego jest tokenem zabezpieczającym uproszczone, która udziela dostępu "bearer" do chronionego zasobu. W tym sensie "bearer" to każda strona, która może powodować tokenu. Chociaż strona muszą najpierw zostać uwierzytelnione za pomocą usługi Azure AD, aby otrzymać token elementu nośnego, jeśli nie podejmuje kroki wymagane do zabezpieczania token w transmisji i przechowywania, można przechwycony i używane przez niezamierzone innych firm. Podczas gdy niektóre tokeny zabezpieczające mają wbudowany mechanizm uniemożliwia ich użycie przez osoby nieupoważnione, tokenów elementu nośnego nie mają ten mechanizm i musi być transportowane bezpiecznego kanału, takie jak transport layer security (HTTPS). Jeśli token elementu nośnego jest przekazywane w zabezpieczeniu, złośliwa strona umożliwia atak typu man-in--middle uzyskania tokenu i użyć jej do nieautoryzowanego dostępu do chronionego zasobu. Te same zasady zabezpieczeń stosowane, gdy przechowywania lub buforowanie tokenów elementu nośnego do późniejszego użycia. Zawsze upewnij się, że Twoja aplikacja przesyła i przechowuje tokenów elementu nośnego w bezpieczny sposób. Aby uzyskać więcej zagadnienia dotyczące zabezpieczeń na tokenów elementu nośnego, zobacz [RFC 6750 sekcji 5](https://tools.ietf.org/html/rfc6750).

Dalsze szczegóły różnego rodzaju tokeny używane w punkcie końcowym v2.0 jest dostępna w [odwołania do tokenu punktu końcowego v2.0](v2-id-and-access-tokens.md).

## <a name="protocols"></a>Protokoły

Gdy wszystko będzie gotowe wyświetlić niektóre przykładowe żądania, wprowadzenie do jednej z poniższych samouczków. Każdy z nich odnosi się do scenariusza określonego uwierzytelniania. Jeśli potrzebujesz pomocy przy ustaleniu, czyli przepływ odpowiednie dla Ciebie, zapoznaj się z [typy aplikacji, które można skompilować przy użyciu v2.0](v2-app-types.md).

* [Tworzenie aplikacji mobilnych i natywnych przy użyciu protokołu OAuth 2.0](v2-oauth2-auth-code-flow.md)
* [Tworzenie aplikacji sieci web z OpenID Connect](v2-protocols-oidc.md)
* [Tworzenie aplikacji jednostronicowej przy użyciu protokołu OAuth 2.0 niejawny przepływ](v2-oauth2-implicit-grant-flow.md)
* [Tworzenie demonów i realizowania innych procesów po stronie serwera przy użyciu przepływu poświadczeń klienta OAuth 2.0](v2-oauth2-client-creds-grant-flow.md)
* [Uzyskiwanie tokenów w interfejsie web API za pomocą protokołu OAuth 2.0 w imieniu użytkownika z usługi Flow](v2-oauth2-on-behalf-of-flow.md)
