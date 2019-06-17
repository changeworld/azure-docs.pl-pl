---
title: Wymagania wstępne dotyczące interfejsu API | Portal Azure Marketplace
description: Wymagania wstępne dla przy użyciu interfejsów API z portalu Cloud Partner.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: a973ab0a406168756af61900fd35947c8be6d03b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935388"
---
<a name="api-prerequisites"></a>Wymagania wstępne dotyczące interfejsu API
================

Istnieją dwa wymagane zasoby programowe, które należy użyć interfejsów API portalu dla partnerów chmury: token dostępu z nazwy głównej usługi i Azure Active Directory (Azure AD).


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Tworzenie jednostki usługi w dzierżawie usługi Azure Active Directory
----------------------------------------------------------------

Najpierw musisz utworzyć nazwę główną usługi w dzierżawie usługi Azure AD. Ta dzierżawa zostanie przypisany swój własny zestaw uprawnień w portalu Cloud Partner. Twój kod będzie wywoływać interfejsy API przy użyciu tej dzierżawy, a nie przy użyciu osobistych poświadczeń.  Aby uzyskać pełne wyjaśnienie tworzenia jednostki usługi, zobacz [w obsłudze portalu do tworzenia aplikacji i usługi jednostki, które mogą uzyskiwać dostęp do zasobów usługi Azure Active Directory](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).


<a name="add-the-service-principal-to-your-account"></a>Dodaj nazwę główną usługi do swojego konta
-----------------------------------------

Teraz, po utworzeniu usługi głównej w swojej dzierżawie, możesz dodać go jako użytkownika do swojego konta w portalu Cloud Partner. Podobnie jak użytkownik jednostka usługi może być właściciela lub współautora do portalu.

Aby dodać jednostkę usługi, wykonaj następujące kroki:

1. Logowanie do portalu Cloud Partner. 
2. Kliknij pozycję **użytkowników** na pasku menu po lewej stronie i wybierz polecenie **Dodaj użytkownika**.

   ![Dodawanie użytkownika do portalu](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. Z **typu** listy rozwijanej wybierz **nazwy głównej usługi** i dodaj następujące informacje:

-   A **przyjazną nazwę** jednostki usługi, na przykład `spAccount`.
-   **Identyfikator aplikacji**. Aby znaleźć ten identyfikator, przejdź do [witryny Azure Portal](https://portal.azure.com), kliknij przycisk **usługi Azure Active Directory**, wybierz **rejestracje aplikacji**i kliknij przycisk w aplikacji.
-   **Identyfikator dzierżawy**, znane również jako **identyfikator katalogu**, dla dzierżawy usługi Azure AD. Ten identyfikator można znaleźć na stronie usługi Azure Active Directory w [witryny Azure portal](https://portal.azure.com)w obszarze **właściwości**.
-   **Obiektu o identyfikatorze** obiektu nazwy głównej usługi. Ten identyfikator może pobrać z witryny Azure portal. Przejdź do **usługi Azure Active Directory**, wybierz **rejestracje aplikacji**, kliknij aplikację i kliknij nazwę aplikacji, w obszarze **aplikacja zarządzana w katalogu lokalnym**. Następnie przejdź do **właściwości** strony, aby znaleźć identyfikatora obiektu. Upewnij się, że nie są Przechwytywanie początkowego Identyfikatora obiektu, który znajduje się w swojej aplikacji, ale zamiast tego Identyfikatora obiektu w aplikacji zarządzanej.
-   **Roli** skojarzone z kontem, które będzie używane dla RBAC.

     ![Dodawanie aplikacji zarządzanej do portalu](./media/cloud-partner-portal-api-prerequisites/managedapp.png)

1. Kliknij przycisk **Dodaj** można dodać jednostki usługi do Twojego konta.

   ![Dodawanie jednostki usługi](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Uzyskiwanie tokenu dostępu usługi Azure AD
----------------------------

Interfejsów API portalu dla partnerów chmury Użyj następujących zasobów i protokoły podczas uwierzytelniania:

- Token elementu nośnego tokenu sieci Web JSON (JWT), aby zażądać dostępu do zasobów
- [OpenID Connect](https://openid.net/connect/) protokołu (OIDC) w celu zweryfikowania tożsamości
- [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) jako źródło tożsamości

Dostępne są dwie opcje zasadę, aby programowo pobierania tokenu JWT:

- Korzystanie z biblioteki uwierzytelniania firmy Microsoft dla platformy .NET ([platformy MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)).  Takie podejście wyższego poziomu jest zalecane dla deweloperów platformy .NET. 
- Wprowadź **żądania HTTP POST** żądania oauth usługi Azure AD **tokenu** punktu końcowego, który ma postać:

``` HTTP
POST https://login.microsoftonline.com/<tenant-id>/oauth2/token
    client_id: <application-id>
    client_secret:<application-secret>
    grant_type: client_credentials
    resource: https://cloudpartner.azure.com
```

Teraz możesz przekazać ten token jako części nagłówka autoryzacji dla żądań interfejsu API.

``` HTTP
GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview 
    Accept: application/json
    Authorization: Bearer <access-token>
```
> [!NOTE]
> Dla wszystkich interfejsów API w ramach tego odwołania nagłówek autoryzacji jest zawsze zakłada, że zakończonych powodzeniem, dzięki czemu nie zostały jawnie wymienione.

Jeśli wystąpią błędy uwierzytelniania w żądaniu, zobacz [Rozwiązywanie problemów z błędami uwierzytelniania](./cloud-partner-portal-api-troubleshooting-authentication-errors.md).
