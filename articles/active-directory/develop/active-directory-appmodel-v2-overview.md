---
title: Punkt końcowy v2.0 usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Wprowadzenie do tworzenia aplikacji za pomocą logowania Account Microsoft i usługi Azure Active Directory.
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
ms.openlocfilehash: 3d71e889fccaa6de59d9a528754a5e11d0220bef
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39577139"
---
# <a name="sign-in-microsoft-account-and-azure-active-directory-users-in-a-single-application"></a>Zaloguj się Account firmy Microsoft i usługi Azure Active Directory użytkownikom w jednej aplikacji
W przeszłości deweloperzy aplikacji, którzy chcieli obsługuje zarówno osobistych kont Microsoft i działać kont z usługi Azure Active Directory było zintegrować z dwóch oddzielnych systemach. Punktu końcowego v2.0 usługi Azure Active Directory (Azure AD) wprowadzono nową wersję interfejsu API uwierzytelniania, która upraszcza ten proces. Punktu końcowego v2.0 usługi Azure AD umożliwia logowanie z oboma typami kont za pomocą pojedynczego integracji. Aplikacje, które używają punktu końcowego v2.0 usługi Azure AD mogą również wykorzystywać interfejsy API REST firmy [interfejsu API Microsoft Graph](https://graph.microsoft.io) przy użyciu dowolnego typu konta.

## <a name="getting-started"></a>Wprowadzenie
Wybierz Twojej ulubionej platformie z następującej listy, aby skompilować aplikację przy użyciu programu Microsoft Otwórz źródło bibliotek i platform. Protokoły OAuth 2.0 i OpenID Connect umożliwia również wysyłanie i odbieranie wiadomości protokołu bezpośrednio, bez przy użyciu biblioteki uwierzytelniania.
<br />

[!INCLUDE [Azure AD v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-azure-ad-v20-endpoint"></a>Dowiedz się więcej na temat punktu końcowego v2.0 usługi Azure AD
Dowiedz się więcej na temat działania z punktem końcowym v2.0 usługi Azure AD:

* Odkryj [typów aplikacji, które można tworzyć przy użyciu punktu końcowego v2.0 usługi Azure AD](active-directory-v2-flows.md).
* Zrozumienie [ograniczenia, ograniczenia i ograniczenia](active-directory-v2-limitations.md) z punktem końcowym v2.0 usługi Azure AD.
* Obejrzyj ten film wideo z omówieniem punktu końcowego v2.0 usługi Azure AD:

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="additional-resources"></a>Zasoby dodatkowe
Poznaj szczegółowe informacje o platformie punktu końcowego v2.0 usługi Azure AD:

* [Odwoływać się do usługi Azure AD v2.0 protokołów](active-directory-v2-protocols.md)
* [Usługa Azure AD v2.0 tokenów odwołania](v2-id-and-access-tokens.md)
* [Odwoływać się do bibliotek uwierzytelniania w wersji 2.0 w usłudze Azure AD](active-directory-v2-libraries.md)
* [Zakresy i zgody w punkcie końcowym usługi Azure AD w wersji 2.0](v2-permissions-and-consent.md)
* [Interfejs API programu Microsoft Graph](https://graph.microsoft.io)

> [!NOTE]
> Jeśli musisz zarejestrować się w oraz do kont służbowych z usługi Azure Active Directory, skorzystaj z [przewodnik dewelopera usługi Azure AD](azure-ad-developers-guide.md). Punktu końcowego v2.0 usługi Azure AD jest przeznaczona do użycia przez deweloperów, którzy jawnie musisz się zarejestrować w osobistych kont Microsoft.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
