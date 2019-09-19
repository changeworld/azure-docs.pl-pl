---
title: Aplikacja internetowa, która loguje użytkowników (Rejestracja aplikacji) — platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację internetową, która loguje się do użytkowników (Rejestracja aplikacji)
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
ms.openlocfilehash: 0bdf04014d7b0382913c0a4094f7474686658441
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086701"
---
# <a name="web-app-that-signs-in-users---app-registration"></a>Aplikacja sieci Web, która loguje się do użytkowników — Rejestracja aplikacji

Ta strona zawiera wyjaśnienia dotyczące rejestracji aplikacji dla aplikacji sieci Web, które logują się do użytkowników.

Aby zarejestrować aplikację, możesz użyć:

- Funkcja [szybkiego startu aplikacji sieci Web](#register-an-app-using-the-quickstarts) — Oprócz tworzenia aplikacji, Przewodniki Szybki start w Azure Portal zawierają przycisk o nazwie **Utwórz tę zmianę dla mnie**. Ten przycisk służy do ustawiania właściwości, które są potrzebne, nawet w przypadku istniejącej aplikacji. Należy dostosować wartości tych właściwości do własnego przypadku. W szczególności adres URL internetowego interfejsu API dla aplikacji prawdopodobnie będzie inny niż proponowana wartość domyślna, która również wpłynie na identyfikator URI wylogowania.
- Azure Portal, aby [ręcznie zarejestrować aplikację](#register-an-app-using-azure-portal)
- Program PowerShell i narzędzia wiersza polecenia

## <a name="register-an-app-using-the-quickstarts"></a>Rejestrowanie aplikacji przy użyciu przewodników Szybki Start

Jeśli przejdziesz do tego linku, możesz utworzyć uruchomienie aplikacji sieci Web przy użyciu narzędzia Bootstrap:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

### <a name="register-an-app-using-azure-portal"></a>Rejestrowanie aplikacji przy użyciu Azure Portal

> [!NOTE]
> Portal, który ma być używany, różni się w zależności od tego, czy aplikacja działa w Microsoft Azureej chmurze publicznej czy w chmurze krajowej lub suwerennej. Aby uzyskać więcej informacji, zobacz [chmury narodowe](./authentication-national-cloud.md#app-registration-endpoints)

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft. Alternatywnie możesz zalogować się do Azure Portal w chmurze krajowej.
1. Jeśli Twoje konto zapewnia dostęp do więcej niż jednej dzierżawy, wybierz swoje konto w prawym górnym rogu, a następnie ustaw sesję portalu na żądaną dzierżawę usługi Azure AD.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** a następnie wybierz pozycję **rejestracje aplikacji** > **Nowa rejestracja**.
1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:
   1. Wybierz obsługiwane typy kont dla aplikacji (zobacz [obsługiwane typy kont](./v2-supported-account-types.md))
   1. W sekcji **Nazwa** podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład `AspNetCore-WebApp`.
   1. W obszarze **Identyfikator URI przekierowania**Dodaj typ aplikacji oraz miejsce docelowe identyfikatora URI, które będą akceptować zwrócone odpowiedzi na tokeny po pomyślnym uwierzytelnieniu. Na przykład `https://localhost:44321/`.  Wybierz pozycję **Zarejestruj**.
1. Wybierz menu **Uwierzytelnianie**, a następnie dodaj następujące informacje:
   1. W polu **adres URL odpowiedzi**Dodaj `https://localhost:44321/signin-oidc`.
   1. W sekcji **Ustawienia zaawansowane** ustaw pole **Adres URL wylogowywania** na wartość `https://localhost:44321/signout-oidc`.
   1. W obszarze **Niejawne przyznanie** zaznacz pole wyboru **Tokeny Identyfikatorów**.
   1. Wybierz pozycję **Zapisz**.

### <a name="register-an-app-using-powershell"></a>Rejestrowanie aplikacji przy użyciu programu PowerShell

> [!NOTE]
> Obecnie program PowerShell usługi Azure AD tworzy tylko aplikacje z następującymi obsługiwanymi typami kont:
>
> - MyOrg (konta tylko w tym katalogu organizacji)
> - AnyOrg (konta w dowolnym katalogu organizacyjnym).
>
> Jeśli chcesz utworzyć aplikację, która umożliwia użytkownikom logowanie się przy użyciu osobistych kont Microsoft (np. Skype, XBox, Outlook.com), można najpierw utworzyć aplikację wielodostępną (obsługiwane typy kont = konta w dowolnym katalogu organizacyjnym), a następnie zmienić `signInAudience` właściwość w manifeście aplikacji z Azure Portal. Opisano to szczegółowo w kroku [1,3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) samouczka ASP.NET Core (i może być uogólniony do aplikacji sieci Web w dowolnym języku).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfiguracja kodu aplikacji](scenario-web-app-sign-user-app-configuration.md)
