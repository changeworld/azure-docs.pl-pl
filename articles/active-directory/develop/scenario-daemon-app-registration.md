---
title: Demon aplikacji wywoływania interfejsów API sieci web (rejestracji aplikacji) — Platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację demona, który wywołuje interfejsy API sieci web — rejestrowanie aplikacji
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79a355ab226e56a3dde1df5369deda5142d47848
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65076243"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>Demon aplikacji czy interfejsy API — rejestrowanie aplikacji sieci web wywołania

W przypadku aplikacji demona Oto się, co należy wiedzieć podczas rejestrowania aplikacji.

## <a name="supported-account-types"></a>Obsługiwane typy konta

Biorąc pod uwagę, że demon aplikacji tylko ma sensu w dzierżawie usługi Azure AD podczas tworzenia aplikacji należy wybrać:

- albo **kont w tym katalogu organizacji tylko**. Ten wybór jest najbardziej typowych przypadkach jako demon aplikacji są zwykle pisane przez line-of-business (LOB) deweloperów.
- lub **kont w dowolnym katalogu organizacji**. Jeśli jesteś niezależnym dostawcą oprogramowania i zapewniając narzędzia narzędzie do klientów, będzie dokonać tego wyboru. Należy zatwierdzić administratorom dzierżawy klienta.

## <a name="authentication---no-reply-uri-needed"></a>Uwierzytelnianie — nie potrzeby identyfikator URI odpowiedzi

W przypadku, gdy aplikacja poufne klienta używa **tylko** przepływ poświadczeń klienta, adres URL odpowiedzi nie musi być zarejestrowana. Nie ma potrzebne do konfiguracji/budowy aplikacji. Przepływ poświadczeń klienta nie używa go.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>Uprawnienia do interfejsu API — uprawnienia aplikacji i zgody administratora

Aplikacji demona może żądać jedynie uprawnień aplikacji do interfejsów API (bez delegowania uprawnień). W **uprawnień interfejsu API** strony rejestracji aplikacji, po wybraniu **Dodaj uprawnienia** i wybrana rodzina interfejsu API, wybierz **uprawnienia aplikacji**, a następnie wybierz swoje uprawnienia

![Uprawnienia aplikacji i zgody administratora](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

Demon aplikacje wymagają mają administratora dzierżawy, wstępnie wyrazić zgodę na aplikacja wywołuje internetowy interfejs API. To wyrażenie zgody znajduje się w tej samej **uprawnień interfejsu API** strony przez wybranie administratora dzierżawy **udzielić zgody administratora, aby *naszej organizacji***

Jeśli jesteś niezależnym dostawcą oprogramowania i tworzenie aplikacji z wieloma dzierżawami, czy chcesz sprawdzić [wdrażanie — wielkość liter w aplikacji wielodostępnej demona](scenario-daemon-production.md#deployment---case-of-multi-tenant-daemon-apps) akapitu.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Demon aplikacji — konfiguracji kodu aplikacji](./scenario-daemon-app-configuration.md)
