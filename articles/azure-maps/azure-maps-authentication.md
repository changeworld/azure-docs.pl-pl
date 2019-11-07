---
title: Uwierzytelnianie w usłudze Azure Maps | Microsoft Docs
description: Uwierzytelnianie na potrzeby używania usług Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 10/24/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 84af496a92bd3c7b30062e965335782f7661aa4a
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73575650"
---
# <a name="authentication-with-azure-maps"></a>Uwierzytelnianie w usłudze Azure Maps

Azure Maps obsługuje dwa sposoby uwierzytelniania żądań: klucz współużytkowany i Azure Active Directory (Azure AD). W tym artykule opisano te metody uwierzytelniania, które ułatwiają wdrożenie.

## <a name="shared-key-authentication"></a>Uwierzytelnianie klucza wspólnego

Uwierzytelnianie klucza wspólnego przekazuje klucze generowane przez konto Azure Maps przy każdym żądaniu do Azure Maps.  Podczas tworzenia konta Azure Maps są generowane dwa klucze. Dla każdego żądania do Azure Maps usług należy dodać klucz subskrypcji jako parametr do adresu URL.

> [!Tip]
> Zaleca się regularne ponowne generowanie kluczy. Podano dwa klucze, aby można było zachować połączenia z jednym kluczem przy ponownym generowaniu innych. Po ponownym wygenerowaniu kluczy należy zaktualizować wszystkie aplikacje, które uzyskują dostęp do konta, aby użyć nowych kluczy.

Aby uzyskać informacje o wyświetlaniu kluczy, zobacz [Wyświetlanie szczegółów uwierzytelniania](https://aka.ms/amauthdetails).

## <a name="authentication-with-azure-active-directory-preview"></a>Uwierzytelnianie w usłudze Azure Active Directory (wersja zapoznawcza)

Azure Maps teraz oferuje integrację [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) do uwierzytelniania żądań dla usług Azure Maps. Usługa Azure AD zapewnia uwierzytelnianie oparte na tożsamościach, w tym [kontroli dostępu opartej na rolach (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview), w celu udzielania dostępu Azure Maps do zasobów na poziomie użytkownika, na poziomie grupy i na poziomie aplikacji. Poniższe sekcje mogą pomóc zrozumieć koncepcje i składniki Azure Maps integrację z usługą Azure AD.

## <a name="authentication-with-oauth-access-tokens"></a>Uwierzytelnianie przy użyciu tokenów dostępu OAuth

Azure Maps akceptuje tokeny dostępu **OAuth 2,0** dla dzierżawców usługi Azure AD skojarzonych z subskrypcją platformy Azure, która zawiera konto Azure Maps. Usługa Azure Maps akceptuje tokeny dla następujących elementów:

* Użytkownicy usługi Azure AD. 
* Aplikacje partnerskie korzystające z uprawnień delegowanych przez użytkowników.
* Zarządzane tożsamości dla zasobów platformy Azure.

Azure Maps generuje *unikatowy identyfikator (identyfikator klienta)* dla każdego konta Azure Maps. Łącząc ten identyfikator klienta z dodatkowymi parametrami, można zażądać tokenów z usługi Azure AD, określając wartości w poniższej tabeli w zależności od środowiska platformy Azure.

| Środowisko platformy Azure   | Punkt końcowy tokenu usługi Azure AD |
| --------------------|-------------------------|
| Usługa Azure Public        | https://login.microsoftonline.com |
| Azure Government    | https://login.microsoftonline.us |


Aby uzyskać więcej informacji na temat sposobu konfigurowania usługi Azure AD i tokenów żądań dla Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w programie Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Aby uzyskać ogólne informacje na temat żądania tokenów z usługi Azure AD, zobacz [co to jest uwierzytelnianie?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Żądaj zasobów usługi Azure map przy użyciu tokenów OAuth

Po odebraniu tokenu z usługi Azure AD żądanie może zostać wysłane do Azure Maps przy użyciu następujących dwóch wymaganych nagłówków żądań:

| Nagłówek żądania    |    Wartość    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| Autoryzacja     | Bearer eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id` to identyfikator GUID oparty na koncie Azure Maps, który pojawia się na stronie uwierzytelniania Azure Maps.

Oto przykład żądania Azure Maps trasy, które używa tokenu OAuth:

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Informacje o wyświetlaniu identyfikatora klienta znajdują się w temacie [Wyświetlanie szczegółów uwierzytelniania](https://aka.ms/amauthdetails).

## <a name="control-access-with-rbac"></a>Kontrola dostępu za pomocą RBAC

Usługa Azure AD pozwala kontrolować dostęp do zabezpieczonych zasobów przy użyciu RBAC. Po utworzeniu konta Azure Maps i zarejestrowaniu aplikacji Azure Maps usługi Azure AD w ramach dzierżawy usługi Azure AD możesz skonfigurować kontrolę RBAC dla użytkownika, grupy, aplikacji lub zasobu platformy Azure na stronie portalu konta Azure Maps.

Azure Maps obsługuje kontrolę dostępu do odczytu dla indywidualnych użytkowników usługi Azure AD, grup, aplikacji i usług platformy Azure za pośrednictwem zarządzanych tożsamości dla zasobów platformy Azure.

![Czytnik danych Azure Maps (wersja zapoznawcza)](./media/azure-maps-authentication/concept.png)

Aby uzyskać informacje na temat wyświetlania ustawień RBAC, zobacz [How to configure RBAC for Azure Maps](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Zarządzane tożsamości dla zasobów platformy Azure i Azure Maps

[Zarządzane tożsamości dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) zapewniają usługi platformy azure (Azure App Service, Azure Functions, Azure Virtual Machines itd.) z automatycznie zarządzaną tożsamością, która może być autoryzowana do uzyskiwania dostępu do usług Azure Maps.  

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o uwierzytelnianiu aplikacji za pomocą usługi Azure AD i Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w programie Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Aby dowiedzieć się więcej na temat uwierzytelniania kontrolka mapy Azure Maps i usługi Azure AD, zobacz [Korzystanie z kontrolka mapy Azure Maps](https://aka.ms/amaadmc).
