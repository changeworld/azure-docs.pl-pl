---
title: Aplikacja demona wywołująca interfejsy API sieci Web (Rejestracja aplikacji) — platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację demona, która wywołuje interfejsy API sieci Web — Rejestracja aplikacji
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f590010a655fb01529a4a59b5540cc03068f2b8
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056474"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>Aplikacja demona, która wywołuje interfejsy API sieci Web — Rejestracja aplikacji

Poniżej przedstawiono informacje o tym, co należy wiedzieć podczas rejestrowania aplikacji.

## <a name="supported-account-types"></a>Obsługiwane typy kont

Uwzględniając, że aplikacje demona mają sens tylko w dzierżawie usługi Azure AD, podczas tworzenia aplikacji należy wybrać następujące opcje:

- **tylko konta w tym katalogu organizacji**. Jest to najbardziej typowy przypadek, ponieważ aplikacje demona są zwykle zapisywane przez deweloperów branżowych.
- lub **konta w dowolnym katalogu organizacyjnym**. Możesz wybrać tę opcję, jeśli jesteś dostawcą niezależnego dostawcy oprogramowania. Aby to zatwierdzić, będziesz potrzebować administratorów dzierżawy klienta.

## <a name="authentication---no-reply-uri-needed"></a>Uwierzytelnianie — nie jest wymagany identyfikator URI odpowiedzi

W przypadku, gdy poufna aplikacja kliencka używa **tylko** przepływu poświadczeń klienta, adres URL odpowiedzi nie musi być zarejestrowany. Nie jest to konieczne w przypadku konfiguracji/konstrukcji aplikacji. Przepływ poświadczeń klienta nie używa tego programu.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>Uprawnienia interfejsu API — uprawnienia aplikacji i zgoda administratora

Aplikacja demona może żądać uprawnień aplikacji tylko do interfejsów API (nie delegowanych uprawnień). Na stronie **uprawnienia interfejsu API** do rejestracji aplikacji po wybraniu opcji **Dodaj uprawnienie** i wybraniu rodziny interfejsów API wybierz pozycję **uprawnienia aplikacji**, a następnie wybierz swoje uprawnienia.

![Uprawnienia aplikacji i zgoda administratora](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

Aplikacje demona wymagają, aby administrator dzierżawy wstępnie wyraził zgodę na aplikację wywołującą internetowy interfejs API. Ta zgoda jest świadczona na tej samej stronie **uprawnień interfejsu API** , przez administratora dzierżawy, wybierając pozycję **Udziel zgody administratorowi na swoją *organizację***

Jeśli jesteś niezależnym dostawcą oprogramowania, Kompilując aplikację z wieloma dzierżawcami, warto sprawdzić, czy w [przypadku wdrożenia aplikacji z wieloma dzierżawcami](scenario-daemon-production.md#deployment---case-of-multi-tenant-daemon-apps) jest używany akapit.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Aplikacja demona — konfiguracja kodu aplikacji](./scenario-daemon-app-configuration.md)
