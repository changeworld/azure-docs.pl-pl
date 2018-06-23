---
title: Punktu końcowego v2.0 usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Wprowadzenie do tworzenia aplikacji z logowaniem zarówno Account firmy Microsoft, jak i usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 2dee579f-fdf6-474b-bc2c-016c931eaa27
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: celested
ms.reviewer: hirsin, jmprieur, elisol, dastrock
ms.custom: aaddev
ms.openlocfilehash: 1c91c1ed8358f58ab7a4d9a697ec2d7933c4f137
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316742"
---
# <a name="sign-in-microsoft-account-and-azure-active-directory-users-in-a-single-application"></a>Zaloguj się Account Microsoft i Azure Active Directory użytkowników w jednej aplikacji
W przeszłości deweloperzy aplikacji, którzy chcieli obsługuje oba osobistego konta Microsoft i działać kont z usługi Azure Active Directory musiał zintegrować z dwóch oddzielnych systemach. Punktu końcowego v2.0 usługi Azure Active Directory (Azure AD) wprowadziła nową wersję uwierzytelniania interfejsu API, które upraszcza ten proces. Punktu końcowego v2.0 usługi Azure AD umożliwia logowanie z obu typów kont za pomocą pojedynczego integracji. Aplikacje używające punktu końcowego v2.0 usługi Azure AD może również używać interfejsów API REST z [interfejsu API programu Microsoft Graph](https://graph.microsoft.io) przy użyciu dowolnego typu konta.

## <a name="getting-started"></a>Wprowadzenie
Wybierz preferowaną platformę z poniższej listy, aby utworzyć aplikację przy użyciu programu Microsoft otworzyć źródła bibliotek i platform. Umożliwia także protokołów OAuth 2.0 i OpenID Connect do wysyłania i odbierania wiadomości protokołu bezpośrednio, bez przy użyciu biblioteki uwierzytelniania.
<br />

[!INCLUDE [Azure AD v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-azure-ad-v20-endpoint"></a>Dowiedz się więcej na temat punktu końcowego v2.0 usługi Azure AD
Dowiedz się więcej na temat działania z punktem końcowym v2.0 usługi Azure AD:

* Odnajdywanie [typów aplikacji, które można tworzyć z punktem końcowym v2.0 usługi Azure AD](active-directory-v2-flows.md).
* Zrozumienie [ograniczenia, ograniczenia i ograniczenia](active-directory-v2-limitations.md) z punktem końcowym v2.0 usługi Azure AD.
* Obejrzyj ten film omówienie punktu końcowego v2.0 usługi Azure AD:

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="additional-resources"></a>Zasoby dodatkowe
Poznaj szczegółowe informacje na temat platformy punktu końcowego v2.0 usługi Azure AD:

* [Odwołanie protokołów v2.0 usługi Azure AD](active-directory-v2-protocols.md)
* [Azure AD 2.0 tokeny odwołania](active-directory-v2-tokens.md)
* [Odwołanie bibliotek uwierzytelniania v2.0 usługi Azure AD](active-directory-v2-libraries.md)
* [Zakresy i zgody w punkcie końcowym v2.0 usługi Azure AD](active-directory-v2-scopes.md)
* [Program Microsoft Graph API](https://graph.microsoft.io)

> [!NOTE]
> Jeśli wymagane jest tylko do logowania do kont służbowych z usługi Azure Active Directory, Rozpocznij od [przewodnik dewelopera usługi Azure AD](active-directory-developers-guide.md). Punktu końcowego v2.0 usługi Azure AD jest przeznaczony do użycia przez deweloperów, którzy jawnie konieczne logowanie w osobistego konta Microsoft.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
