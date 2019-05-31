---
title: Dowiedz się więcej o protokoły autoryzacji, obsługiwane przez platformę tożsamości firmy Microsoft | Azure
description: Przewodnik dotyczący protokołu OAuth 2.0 i OpenID Connect protokołów, które są obsługiwane przez punkt końcowy platforma tożsamości firmy Microsoft.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 5fb4fa1b-8fc4-438e-b3b0-258d8c145f22
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cfc9c027806cb1a3f65e67eda771894a7250ee67
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417648"
---
# <a name="microsoft-identity-platform-protocols"></a>Protokoły platforma tożsamości firmy Microsoft

Punkt końcowy platforma tożsamości firmy Microsoft tożsamości as-a-service za pomocą ze standardowych protokołów uwierzytelniania OpenID Connect i OAuth 2.0. Gdy usługa jest zgodny ze standardami, może to być niewielkich różnic między implementacjami dwóch tych protokołów. W tym miejscu informacje będą przydatne, jeśli istnieje możliwość pisania kodu przez bezpośrednie wysyłanie i obsługa żądań HTTP lub użyć innej biblioteki typu open source, a nie przy użyciu jednej z naszych [bibliotek typu open-source](reference-v2-libraries.md).

> [!NOTE]
> Nie wszystkie scenariusze usługi Azure AD i funkcje są obsługiwane przez punkt końcowy platforma tożsamości firmy Microsoft. Aby ustalić, należy użyć programu Microsoft platformy tożsamości z punktu końcowego, przeczytaj temat [ograniczenia dotyczące programu Microsoft identity platformy](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Podstawy

W prawie wszystkie przepływy protokołu OAuth 2.0 i OpenID Connect istnieją cztery strony związane z programu exchange:

![Role uwierzytelniania OAuth 2.0](./media/active-directory-v2-flows/protocols-roles.svg)

* **Serwera autoryzacji** jest punktem końcowym platforma tożsamości firmy Microsoft i odpowiada za zagwarantowanie, tożsamość użytkownika, przyznawania i odbieranie prawa dostępu do zasobów i wydawania tokenów. Serwer autoryzacji, znany także jako dostawcy tożsamości — bezpieczną obsługę nic wspólnego z informacji o użytkowniku, ich dostęp i relacje zaufania między stronami w przepływie.
* **Właściciel zasobu** jest zazwyczaj użytkownika końcowego. To strona, która jest właścicielem danych i zasilania, aby umożliwić firmom dostęp do tych danych lub zasobu.
* **Klienta OAuth** aplikacja, identyfikowane przez jego identyfikator aplikacji. Klient uwierzytelniania OAuth jest zazwyczaj innych firm, które użytkownik końcowy korzysta z, a żądania tokenów z serwera autoryzacji. Klient musi otrzymać uprawnienia dostępu do zasobu przez właściciela zasobów.
* **Serwer zasobów** jest, gdzie znajduje się zasób lub danych. Zaufany serwer autoryzacji do bezpiecznego uwierzytelniania i autoryzacji klienta OAuth i używa tokenów dostępu do elementu nośnego, aby upewnić się, że można udzielić dostępu do zasobu.

## <a name="app-registration"></a>Rejestracja aplikacji

Każda aplikacja, która chce zaakceptować zarówno osobistych i kont służbowych muszą być zarejestrowane przy użyciu **rejestracje aplikacji** środowiska w [witryny Azure portal](https://aka.ms/appregistrations) przed jego zaakceptowaniem tych użytkowników przy użyciu protokołu OAuth 2.0 lub OpenID Connect. Proces rejestracji aplikacji będzie zbierać i przypisać kilka wartości do swojej aplikacji:

* **Identyfikator aplikacji** , który jednoznacznie identyfikuje aplikację
* A **identyfikator URI przekierowania** (opcjonalnie) który może służyć do kierowania odpowiedzi z powrotem do aplikacji
* Kilka innych wartości specyficznych dla danego scenariusza.

Aby uzyskać więcej szczegółowych informacji, zapoznaj się z procedurą [rejestracji aplikacji](quickstart-register-app.md).

## <a name="endpoints"></a>Punkty końcowe

Po zarejestrowaniu aplikacji komunikuje się z platformą Microsoft identity, wysyłając żądania do punktu końcowego:

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
> Dowolnej aplikacji zarejestrowanych w usłudze Azure AD można użyć punktu końcowego platformy tożsamości firmy Microsoft, nawet jeśli nie zarejestruje się na kontach osobistych.  Dzięki temu można migrować istniejące aplikacje na platformie tożsamości firmy Microsoft i [MSAL](reference-v2-libraries.md) bez potrzeby ponownego tworzenia aplikacji.  

## <a name="tokens"></a>Tokeny

Implementacja platforma tożsamości firmy Microsoft protokołu OAuth 2.0 i OpenID Connect Wykorzystaj rozbudowane tokenów elementu nośnego, łącznie z tokenów elementu nośnego reprezentowane jako tokenów Jwt. Token elementu nośnego jest tokenem zabezpieczającym uproszczone, która udziela dostępu "bearer" do chronionego zasobu. W tym sensie "bearer" to każda strona, która może powodować tokenu. Chociaż strona muszą najpierw zostać uwierzytelnione z platformą tożsamości Microsoft do odbierania token elementu nośnego, jeśli nie podejmuje kroki wymagane do zabezpieczania token w transmisji i przechowywania, można przechwycony i używane przez niezamierzone innych firm. Podczas gdy niektóre tokeny zabezpieczające mają wbudowany mechanizm uniemożliwia ich użycie przez osoby nieupoważnione, tokenów elementu nośnego nie mają ten mechanizm i musi być transportowane bezpiecznego kanału, takie jak transport layer security (HTTPS). Jeśli token elementu nośnego jest przekazywane w zabezpieczeniu, złośliwa strona umożliwia atak typu man-in--middle uzyskania tokenu i użyć jej do nieautoryzowanego dostępu do chronionego zasobu. Te same zasady zabezpieczeń stosowane, gdy przechowywania lub buforowanie tokenów elementu nośnego do późniejszego użycia. Zawsze upewnij się, że Twoja aplikacja przesyła i przechowuje tokenów elementu nośnego w bezpieczny sposób. Aby uzyskać więcej zagadnienia dotyczące zabezpieczeń na tokenów elementu nośnego, zobacz [RFC 6750 sekcji 5](https://tools.ietf.org/html/rfc6750).

Dalsze szczegóły różnego rodzaju tokeny używane w programie Microsoft endpoint platformy tożsamości jest dostępna w [odwołania Microsoft tożsamości platformy punktu końcowego tokenu](v2-id-and-access-tokens.md).

## <a name="protocols"></a>Protokoły

Gdy wszystko będzie gotowe wyświetlić niektóre przykładowe żądania, wprowadzenie do jednej z poniższych samouczków. Każdy z nich odnosi się do scenariusza określonego uwierzytelniania. Jeśli potrzebujesz pomocy przy ustaleniu, czyli przepływ odpowiednie dla Ciebie, zapoznaj się z [typy aplikacji, można tworzyć z platformą tożsamości Microsoft](v2-app-types.md).

* [Tworzenie aplikacji mobilnych i natywnych przy użyciu protokołu OAuth 2.0](v2-oauth2-auth-code-flow.md)
* [Tworzenie aplikacji sieci web z OpenID Connect](v2-protocols-oidc.md)
* [Tworzenie aplikacji jednostronicowej przy użyciu protokołu OAuth 2.0 niejawny przepływ](v2-oauth2-implicit-grant-flow.md)
* [Tworzenie demonów i realizowania innych procesów po stronie serwera przy użyciu przepływu poświadczeń klienta OAuth 2.0](v2-oauth2-client-creds-grant-flow.md)
* [Uzyskiwanie tokenów w interfejsie web API za pomocą protokołu OAuth 2.0 w imieniu użytkownika z usługi Flow](v2-oauth2-on-behalf-of-flow.md)
