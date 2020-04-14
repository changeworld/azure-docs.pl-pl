---
title: Wymagania wstępne API | Azure Marketplace
description: Wymagania wstępne przy użyciu interfejsów API portalu partnerów w chmurze.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: df94cba1f77ae3ea8cf595e7c651af7a69108bb6
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255978"
---
<a name="api-prerequisites"></a>Wymagania wstępne interfejsu API
================

> [!NOTE]
> Interfejsy API portalu partnerów w chmurze są zintegrowane z centrum partnerów i będą nadal działać po migracji ofert do Centrum partnerów. Integracja wprowadza niewielkie zmiany. Przejrzyj zmiany wymienione w [aplikacji Cloud Partner Portal API Reference,](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) aby upewnić się, że kod będzie nadal działać po migracji do Centrum partnerów.

Istnieją dwa wymagane zasoby programowe, które należy użyć interfejsów API portalu partnerów w chmurze: podmiot usługi i token dostępu usługi Azure Active Directory (Azure AD).


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Tworzenie jednostki usługi w dzierżawie usługi Azure Active Directory
----------------------------------------------------------------

Najpierw należy utworzyć jednostkę usługi w dzierżawie usługi Azure AD. Dzierżawcy zostanie przypisany własny zestaw uprawnień w portalu cloud partner. Kod będzie wywoływać interfejsy API przy użyciu jako dzierżawy zamiast przy użyciu poświadczeń osobistych.  Aby uzyskać pełne wyjaśnienie tworzenia jednostki usługi, zobacz [Korzystanie z portalu do tworzenia aplikacji i jednostki usługi Azure Active Directory, która może uzyskiwać dostęp do zasobów.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)


<a name="add-the-service-principal-to-your-account"></a>Dodawanie jednostki usługi do konta
-----------------------------------------

Po utworzeniu jednostki usługi w dzierżawie można dodać go jako użytkownika do konta portalu programu Cloud Partner Portal. Podobnie jak użytkownik, podmiot usługi może być właścicielem lub współautorem portalu.

Aby dodać jednostkę usługi, należy wykonać następujące czynności:

1. Zaloguj się do portalu cloud partner. 
2. Kliknij **na Użytkownicy** na lewym pasku menu i wybierz **pozycję Dodaj użytkownika**.

   ![Dodawanie użytkownika do portalu](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. Z **listy** rozwijanej Typ wybierz **pozycję Główny serwis** i dodaj następujące szczegóły:

-   Przyjazna **nazwa** dla jednostki `spAccount`usługi, na przykład .
-   **Identyfikator aplikacji**. Aby znaleźć ten identyfikator, przejdź do [witryny Azure portal](https://portal.azure.com), kliknij pozycję **Azure Active Directory**, wybierz pozycję **Rejestracje aplikacji**i kliknij aplikację.
-   **Identyfikator dzierżawy**, znany również jako **identyfikator katalogu,** dla dzierżawy usługi Azure AD. Ten identyfikator można znaleźć na stronie Usługi Azure Active Directory w [witrynie Azure portal](https://portal.azure.com)w obszarze **Właściwości**.
-   **Identyfikator obiektu** dla obiektu jednostkowego usługi. Ten identyfikator można uzyskać z witryny Azure portal. Przejdź do **usługi Azure Active Directory**, wybierz **rejestracje aplikacji**, kliknij aplikację i kliknij nazwę aplikacji w obszarze Aplikacja **zarządzana w katalogu lokalnym**. Następnie przejdź do strony **Właściwości,** aby znaleźć identyfikator obiektu. Upewnij się, że nie są chwytając początkowy identyfikator obiektu, który znajduje się w aplikacji, ale zamiast identyfikator obiektu w aplikacji zarządzanej.
-   **Rola** skojarzona z kontem, która będzie używana dla RBAC.

     ![Dodawanie zarządzanej aplikacji do portalu](./media/cloud-partner-portal-api-prerequisites/managedapp.png)

1. Kliknij **przycisk Dodaj,** aby dodać jednostkę usługi do konta.

   ![Dodawanie jednostki usługi](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Uzyskiwanie tokenu dostępu usługi Azure AD
----------------------------

Interfejsy API portalu partnerów w chmurze używają następujących zasobów i protokołów podczas uwierzytelniania:

- Token nośny tokenu JSON Web Token (JWT) w celu żądania dostępu do zasobów
- Protokół [OpenID Connect](https://openid.net/connect/) (OIDC) do weryfikacji tożsamości
- [Usługa Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) jako urząd tożsamości

Istnieją dwa podstawowe podejścia do programowego pozyskiwania tokenu JWT:

- Użyj biblioteki uwierzytelniania firmy Microsoft dla platformy .NET[(MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)).  To podejście wyższego poziomu jest zalecane dla deweloperów platformy .NET. 
- Złożyć żądanie **HTTP POST** do punktu końcowego **tokenu usługi** Azure AD oauth, który przyjmuje formularz:

``` HTTP
POST https://login.microsoftonline.com/<tenant-id>/oauth2/token
    client_id: <application-id>
    client_secret:<application-secret>
    grant_type: client_credentials
    resource: https://cloudpartner.azure.com
```

Teraz można przekazać ten token jako część nagłówka autoryzacji dla żądań interfejsu API.

``` HTTP
GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview 
    Accept: application/json
    Authorization: Bearer <access-token>
```
> [!NOTE]
> Dla wszystkich interfejsów API w tym odwołaniu nagłówek autoryzacji jest zawsze zakłada, przekazywane, więc nie jest jawnie wymienione.

Jeśli w żądaniu wystąpią błędy uwierzytelniania, zobacz [Rozwiązywanie problemów z błędami uwierzytelniania](./cloud-partner-portal-api-troubleshooting-authentication-errors.md).
