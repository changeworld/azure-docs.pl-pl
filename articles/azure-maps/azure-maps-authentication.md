---
title: Uwierzytelnianie w usłudze Azure Maps | Microsoft Docs
description: Uwierzytelnianie na potrzeby używania usług Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b97c303433eb8fadcda51257d37447f052ce4a3b
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118848"
---
# <a name="authentication-with-azure-maps"></a>Uwierzytelnianie w usłudze Azure Maps

Usługa Azure Maps obsługuje dwa sposoby uwierzytelniania żądań. Klucz współużytkowany lub usługa Azure Active Directory (Azure AD) oferują różne metody autoryzowania poszczególnych żądań wysyłanych do usługi Azure Maps. W tym artykule objaśniono obie metody uwierzytelniania, aby pomóc w ich implementacji.

## <a name="shared-key-authentication"></a>Uwierzytelnianie klucza współużytkowanego

Uwierzytelnianie klucza współużytkowanego polega na przekazywaniu kluczy wygenerowanych przez konto usługi Azure Maps dla każdego żądania do usługi Azure Maps.  Podczas tworzenia konta usługi Azure Maps generowane są dwa klucze.  Każde żądanie wysłane do usług Azure Maps wymaga dodania klucza subskrypcji jako parametru do adresu URL.

> [!Tip]
> Zaleca się regularne ponowne generowanie kluczy. Otrzymujesz dwa klucze, aby móc obsługiwać połączenia przy użyciu jednego klucza, a w tym czasie ponownie generować drugi. Po ponownym wygenerowaniu kluczy należy zaktualizować wszystkie aplikacje uzyskujące dostęp do tego konta pod kątem używania nowych kluczy.

Aby wyświetlić klucze, zobacz [szczegóły uwierzytelniania](https://aka.ms/amauthdetails).

## <a name="authentication-with-azure-active-directory-preview"></a>Uwierzytelnianie w usłudze Azure Active Directory (wersja zapoznawcza)

Usługa Azure Maps oferuje teraz integrację z [usługą Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) na potrzeby uwierzytelniania żądań dotyczących usług Azure Maps.  Usługa Azure AD udostępnia uwierzytelnianie oparte na identyfikacji, w tym [kontrolę dostępu na podstawie ról](https://docs.microsoft.com/azure/role-based-access-control/overview), co umożliwia nadawanie dostępu do zasobów usługi Azure Maps na poziomie użytkownika lub aplikacji. Ten artykuł ma na celu ułatwienie zrozumienia pojęć i składników integracji usługi Azure Maps z usługą Azure AD.

## <a name="authentication-with-oauth-access-tokens"></a>Uwierzytelnianie przy użyciu tokenów dostępu OAuth

Usługa Azure Maps akceptuje tokeny dostępu **OAuth 2.0** na potrzeby dzierżaw usługi Azure AD skojarzonych z subskrypcją platformy Azure, która zawiera konto usługi Azure Maps.  Usługa Azure Maps akceptuje tokeny dla następujących elementów:

* Użytkownicy usługi Azure AD 
* Aplikacje innych firm korzystające z uprawnień delegowanych przez użytkowników
* Tożsamości zarządzane dla zasobów platformy Azure

Usługa Azure Maps generuje identyfikator `unique identifier (client ID)` dla każdego konta usługi Azure Maps.  Gdy identyfikator klienta zostanie połączony z dodatkowymi parametrami, można zażądać tokenów z usługi Azure AD, określając poniższą wartość:

```
https://login.microsoftonline.com
```
Aby uzyskać więcej informacji na temat sposobu konfigurowania usługi Azure AD i żądania tokenów dla usługi Azure Maps, zobacz [How To Manage Authentication (Jak zarządzać uwierzytelnianiem)](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Aby uzyskać ogólne informacje na temat żądania tokenów z usługi Azure AD, zobacz [Podstawy uwierzytelniania w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="requesting-azure-map-resources-with-oauth-tokens"></a>Żądanie zasobów usługi Azure Maps przy użyciu tokenów OAuth

Po uzyskaniu tokenu z usługi Azure AD do usługi Azure Maps można wysłać żądanie z ustawionymi dwoma następującymi wymaganymi nagłówkami:

| Nagłówek żądania    |    Wartość    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| Autoryzacja     | Bearer eyJ0e….HNIVN |

> [!Note]
> Wartość `x-ms-client-id` to identyfikator GUID oparty na koncie usługi Azure Maps wyświetlany na stronie uwierzytelniania usługi Azure Maps

Poniżej przedstawiono przykładowe żądanie trasy usługi Azure Maps przy użyciu tokenu OAuth:

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Aby wyświetlić identyfikator klienta, zobacz [szczegóły uwierzytelniania](https://aka.ms/amauthdetails).

## <a name="control-access-with-role-based-access-control-rbac"></a>Kontrola dostępu przy użyciu funkcji kontroli dostępu na podstawie ról

Kluczową funkcją usługi Azure AD jest kontrolowanie dostępu do zabezpieczonych zasobów przy użyciu kontroli dostępu na podstawie ról. Po utworzeniu konta usługi Azure Maps i zarejestrowaniu aplikacji usługi Azure Maps zintegrowanej z usługą Azure AD w ramach dzierżawy usługi Azure AD możesz skonfigurować kontrolę dostępu na podstawie ról dla użytkownika, aplikacji lub zasobów platformy Azure z poziomu strony portalu konta usługi Azure Maps. 

Usługa Azure Maps obecnie obsługuje kontrolę dostępu do odczytu dla poszczególnych użytkowników usługi Azure AD, aplikacji lub usług platformy Azure za pośrednictwem tożsamości zarządzanych na potrzeby zasobów platformy Azure.

![pojęcie](./media/azure-maps-authentication/concept.png)

Aby wyświetlić ustawienia kontroli dostępu na podstawie ról, zobacz [How To configure RBAC for Azure Maps (Jak konfigurować kontrolę dostępu na podstawie ról dla usługi Azure Maps)](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Tożsamości zarządzane dla zasobów platformy Azure i usługi Azure Maps

[Tożsamości zarządzane dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) udostępniają usługom platformy Azure (Azure App Service, Azure Functions, Virtual Machines itp.) automatycznie zarządzaną tożsamość, którą można autoryzować w celu uzyskania dostępu do usługi Azure Maps.  

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o uwierzytelnianiu aplikacji za pomocą usługi Azure AD i usługi Azure Maps, zobacz [How To Manage Authentication (Jak zarządzać uwierzytelnianiem)](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Aby dowiedzieć się więcej o uwierzytelnianiu kontrolki mapy usługi Azure Maps za pomocą usługi Azure AD, zobacz [Azure AD and Azure Maps Map Control (Usługa Azure AD i kontrolka mapy usługi Azure Maps)](https://aka.ms/amaadmc).