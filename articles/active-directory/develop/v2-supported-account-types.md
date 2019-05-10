---
title: Obsługiwane konta w aplikacji (odbiorcy) — Platforma tożsamości usługi Microsoft
description: Dokumentacja koncepcyjna o odbiorców i typy obsługiwanych kont w aplikacjach
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d751e28859a3ae1104b28c76d0edfedb2a859eb4
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406511"
---
# <a name="supported-account-types"></a>Obsługiwane typy kont

W tym artykule opisano, jakie typy kont (czasami nazywany odbiorców) są obsługiwane w aplikacjach

<!-- This section can be in an include for many of the scenarios (SPA, Web App signing-in users, protecting a Web API, Desktop (depending on the flows), Mobile -->

## <a name="supported-accounts-types-in-microsoft-identity-platform-applications"></a>Typy obsługiwanych kont w aplikacji platformy Microsoft Identity

W chmurze publicznej Microsoft Azure większość typów aplikacji można logowania użytkowników przy użyciu dowolnych odbiorców:

- Jeśli piszesz aplikację Line of Business (LOB), można się zalogować użytkowników w Twojej organizacji. Czasami o nazwie takiej aplikacji **pojedynczej dzierżawy**.
- Jeśli jesteś niezależnym dostawcą oprogramowania, można napisać aplikację użytkowników, którzy logowania:

  - W każdej organizacji. Taką aplikację o nazwie **wielodostępnych** aplikacji sieci web. Czasami przeczytasz jej logowania użytkowników przy użyciu konta służbowego lub szkolnego.
  - Za pomocą ich pracy lub szkoły lub osobiste konto Microsoft.
  - Za pomocą tylko osobistego konta Microsoft.
    > [!NOTE]
    > Platforma tożsamości firmy Microsoft obsługuje obecnie osobistych kont Microsoft rejestrując aplikację **pracy lub szkoły lub osobistych kont Microsoft**, a następnie ograniczyć logowania w kodzie aplikacji, określając Urząd usługi Azure AD podczas tworzenia aplikacji, takich jak `https://login.onmicrosoftonline.com/consumers`.

- Jeśli piszesz biznesowych do użytkowników aplikacji, możesz też zarejestrować użytkowników przy użyciu ich tożsamości społecznościowych, przy użyciu usługi Azure AD B2C.

## <a name="certain-authentication-flows-dont-support-all-the-account-types"></a>Niektóre przepływy uwierzytelniania nie obsługują wszystkich typów kont

Nie można używać niektórych typów kont z niektórych przepływów uwierzytelniania. Na przykład w pulpitu, aplikacje platformy uniwersalnej systemu Windows lub demon aplikacje:

- Demon aplikacji należy używać tylko z organizacji w usłudze Azure Active Directory. Nie ma sensu próbuje użyć aplikacji demona do manipulowania osobistych kont Microsoft (zgoda administratora nigdy nie zostanie udzielony).  
- Przepływ zintegrowane uwierzytelnianie Windows można używać tylko przy użyciu konta służbowego lub szkolnego, (w Twojej organizacji lub dowolnej organizacji). W rzeczywistości zintegrowane uwierzytelnianie Windows działa z kontami domeny i wymaga maszyn przyłączonych do domeny lub przyłączony Azure AD. Ten przepływ nie ma sensu dla osobistych Accounts firmy Microsoft.
- [Grant hasła właściciela zasobu](./v2-oauth-ropc.md) (nazwy użytkownika/hasła), nie można używać z osobistych kont Microsoft. W rzeczywistości osobistych kont Microsoft wymagają, że użytkownik wyraża zgodę na uzyskiwanie dostępu do osobistych zasoby w każdej sesji logowania. To dlatego, to zachowanie nie jest zgodny z nieinterakcyjnych przepływów.
- Przepływ kodu urządzenia jeszcze nie działa z osobistych kont Microsoft.

## <a name="supported-account-types-in-national-clouds"></a>Typy obsługiwanych kont w chmurach krajowych

 Aplikacje można też zarejestrować się w użytkowników w [chmur krajowych](authentication-national-cloud.md). Jednak osobistych kont Microsoft nie są obsługiwane w tych chmurach (zgodnie z definicją tych chmur). Dlatego są mniejsze typy obsługiwanych kont w tych chmurach, do Twojej organizacji (pojedyncza dzierżawa) lub organizacje (aplikacji z wieloma dzierżawami).

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [dzierżawy usługi Azure Active Directory](./single-and-multi-tenant-apps.md)
- Dowiedz się więcej o [chmur krajowych](./authentication-national-cloud.md)