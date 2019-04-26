---
title: Informacje o wersji 2.0 | Azure
description: Więcej informacji na temat punktu końcowego i platformy w wersji 2.0.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb269df035bcc11583ebb7cff7d1ee2c3f6d8bca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60250253"
---
# <a name="about-v20"></a>Informacje o wersji 2.0

Punkt końcowy i platforma w wersji 2.0 znajdują się w wersji zapoznawczej i są stale rozbudowywane. Obecnie są gotowe funkcje scenariuszy aplikacji jednostronicowej (SPA) JavaScript. Zachęcamy do tworzenia aplikacji opartych na przeglądarce przy użyciu pliku MSAL.js i przesyłania nam opinii, abyśmy mogli zaktualizować stan z wersji zapoznawczej na ogólną dostępność (GA).

> [!NOTE]
> Niektóre funkcje biblioteki MSAL dla systemów Android i iOS oraz platformy .NET nadal są na etapie tworzenia. Można używać ich do tworzenia aplikacji i przesyłać nam opinie na ich temat.

Środowisko [Rejestracje aplikacji (wersja zapoznawcza)](quickstart-register-app.md) w witrynie Azure Portal zostało znacznie rozbudowane. Obecnie obejmuje wszystkie Twoje aplikacje utworzone za pomocą biblioteki ADAL lub MSAL i jest bardziej użyteczne.

W przeszłości deweloperzy aplikacji, którzy chcieli obsługiwać zarówno osobiste konta Microsoft, jak i konta służbowe z poziomu usługi Azure Active Directory (Azure AD), musieli zintegrować je z dwoma oddzielnymi systemami. Punkt końcowy i platforma w wersji 2.0 zawierają wersję interfejsu API uwierzytelniania, która upraszcza ten proces. Umożliwia on logowanie się z obu typów kont dzięki pojedynczej integracji. Aplikacje, które używają punktu końcowego w wersji 2.0, mogą również korzystać z interfejsów API REST z [interfejsu API programu Microsoft Graph](https://developer.microsoft.com/graph), używając obu typów kont.

## <a name="getting-started"></a>Wprowadzenie

Z poniższej listy wybierz swoją ulubioną platformę, aby utworzyć aplikację przy użyciu bibliotek i platform open source firmy Microsoft:

[!INCLUDE [v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-v20-endpoint-and-platform"></a>Więcej informacji na temat punktu końcowego i platformy w wersji 2.0

Dowiedz się, co możesz zrobić z punktem końcowym usługi Azure AD w wersji 2.0:

* Odkryj [typy aplikacji, które możesz utworzyć przy użyciu punktu końcowego usługi Azure AD w wersji 2.0](v2-app-types.md).
* Poznaj [ograniczenia](active-directory-v2-limitations.md) dotyczące punktu końcowego usługi Azure AD w wersji 2.0.

## <a name="additional-resources"></a>Dodatkowe zasoby

Uzyskaj szczegółowe informacje na temat wersji 2.0:

* [Informacje o platformie tożsamości firmy Microsoft](about-microsoft-identity-platform.md)
* [Dokumentacja protokołów w wersji 2.0](active-directory-v2-protocols.md)
* [Dokumentacja tokenów dostępu](access-tokens.md)
* [Dokumentacja tokenów identyfikatorów](id-tokens.md)
* [Dokumentacja bibliotek uwierzytelniania w wersji 2.0](reference-v2-libraries.md)
* [Uprawnienia i wyrażanie zgody w wersji 2.0](v2-permissions-and-consent.md)
* [Interfejs API programu Microsoft Graph](https://developer.microsoft.com/graph)

> [!NOTE]
> Jeśli musisz logować się tylko do kont służbowych i szkolnych z poziomu usługi Azure Active Directory, zacznij od [przewodnika dewelopera usługi Azure AD](v1-overview.md). Punkt końcowy w wersji 2.0 jest przeznaczony do użycia przez deweloperów, którzy potrzebują logowania się do osobistych kont Microsoft.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
