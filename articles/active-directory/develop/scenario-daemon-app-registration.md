---
title: Rejestrowanie aplikacji demonów, które wywołują internetowe interfejsy API — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację demona, która wywołuje internetowe interfejsy API - rejestracja aplikacji
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 508101ad615dd96559b1c68a61be7c08772545db
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885484"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>Aplikacja Demon, która wywołuje internetowe interfejsy API - rejestracja aplikacji

W przypadku aplikacji demona, oto co musisz wiedzieć podczas rejestracji aplikacji.

## <a name="supported-account-types"></a>Obsługiwane typy konta

Aplikacje demonów mają sens tylko w dzierżawach usługi Azure AD. Dlatego podczas tworzenia aplikacji należy wybrać jedną z następujących opcji:

- **Tylko konta w tym katalogu organizacyjnym**. Ten wybór jest najczęstszy, ponieważ aplikacje demonów są zwykle zapisywane przez programistów biznesowych (LOB).
- **Konta w dowolnym katalogu organizacyjnym**. Dokonasz tego wyboru, jeśli jesteś niezależną instalacją, która dostarcza narzędzie dla swoich klientów. Do zatwierdzenia musisz go zatwierdzić administratorzy dzierżawy klientów.

## <a name="authentication---no-reply-uri-needed"></a>Uwierzytelnianie — nie jest wymagany identyfikator URI odpowiedzi

W przypadku, gdy aplikacja klienta poufne używa *tylko* przepływ poświadczeń klienta, identyfikator URI odpowiedzi nie musi być zarejestrowany. Nie jest to potrzebne do konfiguracji aplikacji lub konstrukcji. Przepływ poświadczeń klienta nie używa go.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>Uprawnienia interfejsu API — uprawnienia aplikacji i zgoda administratora

Aplikacja demona może żądać tylko uprawnień aplikacji do interfejsów API (nie uprawnień delegowanych). Na stronie **Uprawnienia interfejsu API** dla rejestracji aplikacji po wybraniu opcji Dodaj **uprawnienie** i wybraniu rodziny interfejsów API wybierz pozycję **Uprawnienia aplikacji,** a następnie wybierz swoje uprawnienia.

![Uprawnienia aplikacji i zgoda administratora](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> Internetowy interfejs API, który chcesz wywołać, musi definiować *uprawnienia aplikacji (role aplikacji),* a nie uprawnienia delegowane. Aby uzyskać szczegółowe informacje na temat sposobu udostępnienia takiego interfejsu API, zobacz [Chroniony interfejs API sieci Web: Rejestracja aplikacji — gdy internetowy interfejs API jest wywoływany przez aplikację demona.](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app)

Aplikacje demona wymagają, aby administrator dzierżawy wstępnie wyraził zgodę na aplikację wywołującą internetowy interfejs API. Administratorzy dzierżawy udzielają tej zgody na tej samej stronie **uprawnień interfejsu API,** wybierając **opcję Udzielaj zgody administratora *naszej organizacji* **

Jeśli jesteś niezależną aplikacją, która buduje aplikację wielodostępną, przeczytaj sekcję [Wdrażanie — przypadek aplikacji demonów wielodostępnych.](scenario-daemon-production.md#deployment---multitenant-daemon-apps)

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Aplikacja Daemon - konfiguracja kodu aplikacji](./scenario-daemon-app-configuration.md)
