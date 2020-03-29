---
title: Logowanie użytkowników z aplikacji sieci Web — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację internetową, która loguje się do użytkowników (omówienie)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 403f589702fd7142f0515a3b6f19ee1b9bbb6420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701556"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Scenariusz: aplikacja sieci Web, która loguje się do użytkowników

Dowiedz się wszystkiego, czego potrzebujesz do tworzenia aplikacji sieci web, która używa platformy tożsamości firmy Microsoft do logowania użytkowników.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Wprowadzenie

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Jeśli chcesz utworzyć pierwszą przenośną aplikację internetową (ASP.NET Core), która loguje się do użytkowników, wykonaj ten szybki start:

> [!div class="nextstepaction"]
> [Szybki start: aplikacja sieci Web ASP.NET Core, która loguje się do użytkowników](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Jeśli chcesz dowiedzieć się, jak dodać logowanie do istniejącej ASP.NET aplikacji sieci web, wypróbuj następujący szybki start:

> [!div class="nextstepaction"]
> [Szybki start: ASP.NET aplikacja internetowa, która loguje się do użytkowników](quickstart-v2-aspnet-webapp.md)

# <a name="java"></a>[Java](#tab/java)

Jeśli jesteś programistą Java, wypróbuj następujący szybki start:

> [!div class="nextstepaction"]
> [Szybki start: dodawanie logowania za pomocą firmy Microsoft do aplikacji sieci Web Java](quickstart-v2-java-webapp.md)

# <a name="python"></a>[Python](#tab/python)

Jeśli rozwijasz się za pomocą języka Python, wypróbuj następujący szybki start:

> [!div class="nextstepaction"]
> [Szybki start: dodawanie logowania za pomocą firmy Microsoft do aplikacji sieci Web języka Python](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Omówienie

Możesz dodać uwierzytelnianie do aplikacji sieci web, aby mogła logować się do użytkowników. Dodanie uwierzytelniania umożliwia aplikacji sieci web dostęp do ograniczonych informacji o profilu w celu dostosowania środowiska dla użytkowników. 

Aplikacje sieci Web uwierzytelniają użytkownika w przeglądarce internetowej. W tym scenariuszu aplikacja sieci web kieruje przeglądarkę użytkownika, aby zalogować się do usługi Azure Active Directory (Azure AD). Usługa Azure AD zwraca odpowiedź logowania za pośrednictwem przeglądarki użytkownika, która zawiera oświadczenia dotyczące użytkownika w tokenie zabezpieczającym. Logowanie użytkowników korzysta ze standardowego protokołu [Open ID Connect,](./v2-protocols-oidc.md) uproszczonego dzięki wykorzystaniu [bibliotek](scenario-web-app-sign-user-app-configuration.md#libraries-for-protecting-web-apps)oprogramowania pośredniczącego.

![Znaki aplikacji sieci Web w użytkownikach](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

W drugiej fazie można włączyć aplikację do wywoływania interfejsów API sieci web w imieniu zalogowanego użytkownika. Ta następna faza to inny scenariusz, który znajdziesz w [aplikacji sieci Web, która wywołuje internetowe interfejsy API](scenario-web-app-call-api-overview.md).

> [!NOTE]
> Dodawanie logowania do aplikacji sieci web polega na ochronie aplikacji sieci web i sprawdzaniu poprawności tokenu użytkownika, czyli na tym, co robią biblioteki **oprogramowania pośredniczącego.** W przypadku platformy .NET ten scenariusz nie wymaga jeszcze biblioteki uwierzytelniania firmy Microsoft (MSAL), która polega na uzyskiwaniu tokenu do wywoływania chronionych interfejsów API. Biblioteki uwierzytelniania zostaną wprowadzone w scenariuszu uzupełniającym, gdy aplikacja sieci web musi wywołać interfejsy API sieci web.

## <a name="specifics"></a>Specyfiki

- Podczas rejestracji aplikacji należy podać jeden lub kilka (jeśli wdrożysz aplikację do kilku lokalizacji) odpowiedzą identyfikatory URI. W niektórych przypadkach (ASP.NET i ASP.NET Core), należy włączyć token identyfikatora. Na koniec należy skonfigurować identyfikator URI wylogowywania, aby aplikacja reagowała na wylogowywanie się użytkowników.
- W kodzie aplikacji należy podać uprawnienia, do których aplikacja sieci web deleguje logowania. Można dostosować sprawdzanie poprawności tokenu (w szczególności w scenariuszach partnerów).
- Aplikacje sieci Web obsługują wszystkie typy kont. Aby uzyskać więcej informacji, zobacz [Obsługiwane typy kont](v2-supported-account-types.md).

## <a name="next-steps"></a>Następne kroki

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=python)

---
