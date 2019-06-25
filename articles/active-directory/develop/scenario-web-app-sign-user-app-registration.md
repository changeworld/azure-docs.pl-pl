---
title: Aplikacja sieci Web, logujący się użytkownicy (rejestracji aplikacji) — Platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację sieci web, logujący się użytkownicy (rejestracji aplikacji)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ae638f8cbef29c5d167a3ab59188169cbd934ef
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67150226"
---
# <a name="web-app-that-signs-in-users---app-registration"></a>Aplikacja sieci Web, logujący się użytkownicy — rejestrowanie aplikacji

Tej stronie wyjaśniamy specyfiki rejestracji aplikacji dla aplikacji sieci web logowania przez użytkowników.

Aby zarejestrować swoją aplikację, można użyć:

- [Web app przewodników Szybki Start](#register-an-app-using-the-quickstarts) — oprócz doskonałe pierwsze doświadczenie w tworzeniu aplikacji, przewodniki Szybki Start w witrynie Azure portal zawiera przycisk o nazwie **wprowadzić tę zmianę dla mnie**. Ten przycisk służy do ustawiania właściwości, których potrzebujesz, nawet w przypadku istniejącej aplikacji. Należy dostosować wartości tych właściwości na własny litery. W szczególności adresu URL interfejsu API sieci web dla aplikacji prawdopodobnie będzie się różnił się od proponowane domyślna, która wpływa także na logowanie się do identyfikatora URI.
- Portalu Azure w celu [ręcznie zarejestrować swoją aplikację](#register-an-app-using-azure-portal)
- Program PowerShell i narzędzia wiersza polecenia

## <a name="register-an-app-using-the-quickstarts"></a>Rejestrowanie aplikacji przy użyciu przewodników Szybki Start

Jeśli przejdziesz do tego linku, można utworzyć ładowania początkowego tworzenia aplikacji sieci web:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

### <a name="register-an-app-using-azure-portal"></a>Rejestrowanie aplikacji przy użyciu witryny Azure portal

> [!NOTE]
> w portalu w celu użycia różni się w zależności od Jeśli aplikacja działa w chmurze publicznej Microsoft Azure lub w chmurze krajowej lub suwerennych. Aby uzyskać więcej informacji, zobacz [chmur krajowych](./authentication-national-cloud.md#app-registration-endpoints)

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft. Alternatywnie Zaloguj się do chmury krajowe witryny Azure portal z wybraną.
1. Jeśli dzierżawa usługi zapewnia konta dostępu do więcej niż jednej dzierżawy, wybierz swoje konto w prawym górnym rogu i ustaw sesję portalu do żądanej usługi Azure AD.
1. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi, a następnie wybierz **rejestracje aplikacji** > **nowej rejestracji**.
1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:
   1. Wybierz typy obsługiwanych kont, dla aplikacji (zobacz [typy obsługiwanych kont](./v2-supported-account-types.md))
   1. W sekcji **Nazwa** podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład `AspNetCore-WebApp`.
   1. W **identyfikator URI przekierowania**Dodaj typ aplikacji i miejsce docelowe URI, które będzie akceptować zwrócił odpowiedzi z tokenem po pomyślnym uwierzytelnieniu. Na przykład `https://localhost:44321/`.  Wybierz pozycję **Zarejestruj**.
1. Wybierz menu **Uwierzytelnianie**, a następnie dodaj następujące informacje:
   1. W **adres URL odpowiedzi**, Dodaj `https://localhost:44321/signin-oidc`.
   1. W sekcji **Ustawienia zaawansowane** ustaw pole **Adres URL wylogowywania** na wartość `https://localhost:44321/signout-oidc`.
   1. W obszarze **Niejawne przyznanie** zaznacz pole wyboru **Tokeny Identyfikatorów**.
   1. Wybierz pozycję **Zapisz**.

### <a name="register-an-app-using-powershell"></a>Rejestrowanie aplikacji przy użyciu programu PowerShell

> [!NOTE]
> Obecnie usługi Azure AD PowerShell tylko tworzy aplikacje z następujących typów obsługiwanych konta:
>
> - MyOrg (konta w tym katalogu organizacji tylko)
> - AnyOrg (konta w dowolnym katalogu organizacji).
>
> Jeśli chcesz utworzyć aplikację logowania przez użytkowników z ich osobistych Accounts Microsoft (np. Skype, XBox, Outlook.com), należy najpierw utworzyć aplikację wielodostępną (obsługiwane typy kont = kont w dowolnym katalogu organizacji), a następnie zmień `signInAudience` właściwości w manifeście aplikacji w witrynie Azure portal. Wyjaśnienie jest zawarte w szczegółach w kroku [1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) samouczka platformy ASP.NET Core (i mogą być uogólniony, aby aplikacje sieci web w dowolnym języku).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Konfiguracja kodu aplikacji](scenario-web-app-sign-user-app-configuration.md)
