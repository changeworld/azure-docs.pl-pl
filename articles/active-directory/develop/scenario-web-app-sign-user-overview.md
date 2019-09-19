---
title: Aplikacja internetowa, która loguje się do użytkowników (omówienie) — platforma tożsamości firmy Microsoft
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f9b6d4ce4048b31f17b50184f90aed4d2a8ba81
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086571"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Scenariusz: Aplikacja internetowa z możliwością logowania użytkowników

Dowiedz się, co należy zrobić, aby utworzyć aplikację sieci Web, która umożliwia użytkownikom logowanie się przy użyciu platformy tożsamości firmy Microsoft.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Wprowadzenie

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Jeśli chcesz utworzyć pierwsze przenośne aplikacje sieci Web (ASP.NET Core), które logują użytkowników, wykonaj następujące czynności:

> [!div class="nextstepaction"]
> [Szybki start: ASP.NET Core aplikacji sieci Web, która loguje się do użytkowników](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

f chcesz zrozumieć, jak dodać logowanie do starszej aplikacji sieci Web ASP.NET, wypróbuj następujący samouczek:

> [!div class="nextstepaction"]
> [Szybki start: Aplikacja internetowa ASP.NET, która loguje użytkowników](quickstart-v2-aspnet-webapp.md)

# <a name="javatabjava"></a>[Java](#tab/java)

Jeśli jesteś deweloperem języka Java, wypróbuj następujący Przewodnik Szybki Start:

> [!div class="nextstepaction"]
> [Szybki start: Dodawanie logowania do aplikacji sieci Web w języku Java firmy Microsoft](quickstart-v2-java-webapp.md)

# <a name="pythontabpython"></a>[Python](#tab/python)

Jeśli opracowujesz przy użyciu języka Python, wypróbuj:

> [!div class="nextstepaction"]
> [Szybki start: Dodawanie logowania z firmą Microsoft do aplikacji sieci Web w języku Python](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Omówienie

Należy dodać uwierzytelnianie do aplikacji sieci Web, aby umożliwić użytkownikom logowanie się. Dodanie uwierzytelniania umożliwia aplikacji sieci Web uzyskanie dostępu do ograniczonych informacji o profilu, a na przykład dostosowanie środowiska, które oferuje jego użytkownicy. Aplikacje sieci Web uwierzytelniają użytkownika w przeglądarce internetowej. W tym scenariuszu aplikacja sieci Web kieruje przeglądarkę użytkownika do podpisania ich w usłudze Azure AD. Usługa Azure AD zwraca odpowiedź logowania za pomocą przeglądarki użytkownika, która zawiera oświadczenia dotyczące użytkownika w tokenie zabezpieczającym. Logowanie użytkowników przy użyciu protokołu [Open ID Connect](./v2-protocols-oidc.md) Standard jest uproszczone przez użycie [bibliotek](scenario-web-app-sign-user-app-configuration.md#libraries-used-to-protect-web-apps)pośredniczących.

![Użytkownicy z logowaniem aplikacji sieci Web](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

W drugiej fazie można także umożliwić aplikacji wywoływanie interfejsów API sieci Web w imieniu zalogowanego użytkownika. Ta kolejna faza jest innym scenariuszem, który można znaleźć w [internetowych interfejsach API wywołań aplikacji sieci Web](scenario-web-app-call-api-overview.md)

> [!NOTE]
> Dodawanie logowania do aplikacji sieci Web polega na ochronie aplikacji sieci Web i weryfikacji tokenu użytkownika, który jest biblioteką **oprogramowania pośredniczącego** . W przypadku platformy .NET ten scenariusz nie wymaga jeszcze bibliotek uwierzytelniania firmy Microsoft (MSAL), które są związane z uzyskaniem tokenu do wywoływania chronionych interfejsów API. Biblioteki uwierzytelniania będą wprowadzane tylko w scenariuszu monitowania, gdy aplikacja sieci Web wymaga wywołania interfejsów API sieci Web.

## <a name="specifics"></a>Szczegółowych informacji

- Podczas rejestracji aplikacji należy podać jedną lub kilka (Jeśli aplikacja jest wdrażana w kilku lokalizacjach) identyfikatorów URI odpowiedzi. W niektórych przypadkach (ASP.NET/ASP.NET rdzeń) musisz włączyć token identyfikatora. Na koniec należy skonfigurować identyfikator URI, aby aplikacja działała w celu wylogowania użytkowników.
- W kodzie aplikacji należy podać uprawnienia do logowania delegatów aplikacji sieci Web. Możesz chcieć dostosować sprawdzanie poprawności tokenów (w szczególności w scenariuszach niezależnych dostawców oprogramowania).
- Aplikacje sieci Web obsługują wszystkie typy kont. Aby uzyskać więcej informacji, zobacz [obsługiwane typy kont](v2-supported-account-types.md).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji](scenario-web-app-sign-user-app-registration.md)
