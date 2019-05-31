---
title: Uwierzytelnianie w usłudze Azure Maps | Microsoft Docs
description: Uwierzytelnianie na potrzeby używania usług Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 22aba19e16e4349a5b495b307c9906f7ded5a636
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393677"
---
# <a name="authentication-with-azure-maps"></a>Uwierzytelnianie w usłudze Azure Maps

Usługi Azure Maps obsługuje dwa sposoby uwierzytelniania żądań: Klucz współużytkowany i Azure Active Directory (Azure AD). W tym artykule opisano te metody uwierzytelniania jako przewodnik wdrożenia.

## <a name="shared-key-authentication"></a>Uwierzytelniania klucza wspólnego

Uwierzytelniania klucza wspólnego przekazuje klucze generowane przez konto usługi Azure Maps z każdym żądaniem do usługi Azure Maps.  Podczas tworzenia konta usługi Azure Maps generowane są dwa klucze. Dla każdego żądania do usługi Azure Maps klucz subskrypcji musi zostać dodane jako parametr do adresu URL.

> [!Tip]
> Zaleca się regularne ponowne generowanie kluczy. Otrzymasz dwa klucze, aby obsługiwać połączenia przy użyciu jednego klucza podczas ponownego generowania drugiego. Podczas ponownego generowania kluczy, należy zaktualizować wszystkie aplikacje, które dostęp do konta w celu używania nowych kluczy.

Informacje o wyświetlaniu kluczy, zobacz [wyświetlanie szczegółów uwierzytelniania](https://aka.ms/amauthdetails).

## <a name="authentication-with-azure-active-directory-preview"></a>Uwierzytelnianie w usłudze Azure Active Directory (wersja zapoznawcza)

Platforma Azure oferuje teraz mapy [usługi Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) integracji na potrzeby uwierzytelniania żądań dotyczących usługi Azure Maps. Usługa Azure AD zapewnia uwierzytelnianie na podstawie tożsamości, w tym [kontroli dostępu opartej na rolach (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview), aby udzielić dostępu użytkownika lub na poziomie aplikacji do zasobów usługi Azure Maps. W kolejnych sekcjach ułatwia zrozumienie pojęcia i składniki usługi Azure Maps integracji z usługą Azure AD.

## <a name="authentication-with-oauth-access-tokens"></a>Uwierzytelnianie przy użyciu tokenów dostępu OAuth

Usługi Azure Maps akceptuje **OAuth 2.0** tokeny dostępu dla dzierżaw usługi Azure AD skojarzonego z subskrypcją platformy Azure, która zawiera konto usługi Azure Maps. Usługa Azure Maps akceptuje tokeny dla następujących elementów:

* Użytkownicy usługi Azure AD. 
* Aplikacje partnerów korzystających z uprawnień delegowanych przez użytkowników.
* Zarządzanych tożsamości dla zasobów platformy Azure.

Usługi Azure Maps generuje *unikatowego identyfikatora (Identyfikatora klienta)* dla każdego konta usługi Azure Maps. Podczas łączenia tego Identyfikatora klienta z dodatkowymi parametrami, może żądać tokenów z usługi Azure AD, określając następującą wartość:

```
https://login.microsoftonline.com
```
Aby uzyskać więcej informacji na temat sposobu konfigurowania usługi Azure AD i żądać tokenów dla usługi Azure Maps, zobacz [Zarządzaj uwierzytelniania usługi Azure Maps](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Aby uzyskać ogólne informacje dotyczące żądania tokenów z usługi Azure AD, zobacz [co to jest uwierzytelnianie?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Zażądaj zasobów mapy platformy Azure przy użyciu tokenów protokołu OAuth

Po odebraniu tokenu z usługi Azure AD, żądania mogą być wysyłane do usługi Azure Maps z następujący zestaw nagłówków dwa żądania wymagane:

| Nagłówek żądania    |    Wartość    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| Autoryzacja     | Bearer eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id` jest usługi Azure Maps oparte na koncie identyfikator GUID, który pojawia się na stronie uwierzytelniania usługi Azure Maps.

Oto przykład z usługi Azure Maps skierować żądania, który używa tokenu OAuth:

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Aby uzyskać informacje o wyświetlaniu Identyfikatora klienta, zobacz [wyświetlanie szczegółów uwierzytelniania](https://aka.ms/amauthdetails).

## <a name="control-access-with-rbac"></a>Kontroluj dostęp za pomocą funkcji RBAC

System Azure AD pozwala na kontrolowanie dostępu do zabezpieczonych zasobów przy użyciu RBAC. Po utworzeniu konta usługi Azure Maps i Zarejestruj swoją aplikację do usługi Azure Maps usługi Azure AD w ramach dzierżawy usługi Azure AD, możesz skonfigurować RBAC dla użytkownika, aplikacji lub zasobów platformy Azure na stronie portalu konta usługi Azure Maps.

Usługi Azure Maps obsługuje kontrolę dostępu do odczytu dla poszczególnych użytkowników usługi Azure AD, aplikacji i usług platformy Azure za pomocą tożsamości zarządzanych zasobów platformy Azure.

![Czytnik danych usługi Azure Maps (wersja zapoznawcza)](./media/azure-maps-authentication/concept.png)

Aby uzyskać informacje o wyświetlaniu ustawień kontroli RBAC można zobaczyć [sposobu konfigurowania funkcji RBAC dla usługi Azure Maps](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Zarządzanych tożsamości dla zasobów platformy Azure i usługi Azure Maps

[Zarządzane tożsamości dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) zapewniają usług platformy Azure (Azure App Service, Azure Functions, Azure Virtual Machines i tak dalej) przy użyciu automatycznie zarządzanych tożsamości, która może być autoryzowane do uzyskiwania dostępu do usługi Azure Maps.  

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej o uwierzytelnianiu aplikacji za pomocą usługi Azure AD i usługi Azure Maps, zobacz [Zarządzaj uwierzytelniania usługi Azure Maps](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Aby dowiedzieć się więcej o uwierzytelnianiu kontrolek mapy mapy platformy Azure i usługą Azure AD, zobacz [używać kontrolki mapy platformy Azure Maps](https://aka.ms/amaadmc).