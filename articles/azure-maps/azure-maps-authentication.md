---
title: Metody uwierzytelniania | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się o usłudze Azure Active Directory (Azure AD) i uwierzytelnianiu klucza udostępnionego. Oba są używane w usługach Microsoft Azure Maps. Dowiedz się, jak uzyskać klucz subskrypcji usługi Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 21d29cba85adfc147ec9deb6ab362a5da943bf10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335700"
---
# <a name="authentication-with-azure-maps"></a>Uwierzytelnianie w usłudze Azure Maps

Usługa Azure Maps obsługuje dwa sposoby uwierzytelniania żądań: uwierzytelnianie klucza udostępnionego i uwierzytelnianie usługi Azure Active Directory. W tym artykule opisano te metody uwierzytelniania, aby pomóc w implementacji usług Azure Maps.

> [!NOTE]
> Aby poprawić bezpieczną komunikację z usługą Azure Maps, obsługujemy teraz usługę Transport Layer Security (TLS) 1.2 i wycofujemy obsługę protokołu TLS 1.0 i 1.1. Aby uniknąć przerw w świadczeniu usług, **zaktualizuj serwery i aplikacje, aby używały protokołu TLS 1.2 przed 2 kwietnia 2020 r.**  Jeśli obecnie używasz protokołu TLS 1.x, oceń gotowość do korzystania z protokołu TLS 1.2 i opracuj plan migracji za pomocą testów opisanych w [rozwiązaniu problemu protokołu TLS 1.0](https://docs.microsoft.com/security/solving-tls1-problem).

## <a name="shared-key-authentication"></a>Uwierzytelnianie klucza udostępnionego

 Klucze podstawowe i pomocnicze są generowane po utworzeniu konta usługi Azure Maps. Zachęcamy do używania klucza podstawowego jako klucza subskrypcji podczas wywoływania usługi Azure Maps przy użyciu uwierzytelniania klucza udostępnionego. Uwierzytelnianie klucza udostępnionego przekazuje klucz wygenerowany przez konto usługi Azure Maps do usługi Azure Maps. Dla każdego żądania do usług Usługi Azure Maps dodaj *klucz subskrypcji* jako parametr do adresu URL. Klucz pomocniczy może służyć w scenariuszach, takich jak zmiany klucza stopniowego.  

Aby uzyskać informacje dotyczące wyświetlania kluczy w witrynie Azure portal, zobacz [Zarządzanie uwierzytelnianiem](https://aka.ms/amauthdetails).

> [!Tip]
> Zaleca się regularne ponowne generowanie kluczy. Są dostarczane z dwoma kluczami, dzięki czemu można zachować połączenia z jednym kluczem podczas ponownego generowania drugiego. Podczas ponownego generowania kluczy, należy zaktualizować wszystkie aplikacje, które uzyskują dostęp do konta, z nowymi kluczami.

## <a name="authentication-with-azure-active-directory-preview"></a>Uwierzytelnianie w usłudze Azure Active Directory (wersja zapoznawcza)

Usługa Azure Maps oferuje teraz uwierzytelnianie żądań dla usług Azure Maps przy użyciu [usługi Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis). Usługa Azure AD zapewnia uwierzytelnianie oparte na tożsamości, w tym [kontrolę dostępu opartą na rolach (RBAC).](https://docs.microsoft.com/azure/role-based-access-control/overview) RBAC służy do udzielania dostępu na poziomie użytkownika, na poziomie grupy lub aplikacji do zasobów usługi Azure Maps. W następnych sekcjach omówiono pojęcia i składniki integracji usługi Azure Maps z usługą Azure AD.

## <a name="authentication-with-oauth-access-tokens"></a>Uwierzytelnianie przy użyciu tokenów dostępu OAuth

Usługa Azure Maps akceptuje tokeny dostępu **OAuth 2.0** dla dzierżaw usługi Azure AD skojarzonych z subskrypcją platformy Azure zawierającą konto usługi Azure Maps. Usługa Azure Maps akceptuje również tokeny dla:

* Użytkownicy usługi Azure AD
* Aplikacje partnerskie korzystające z uprawnień delegowanych przez użytkowników
* Tożsamości zarządzane dla zasobów platformy Azure

Usługa Azure Maps generuje *unikatowy identyfikator (identyfikator klienta)* dla każdego konta usługi Azure Maps. Tokeny usługi Azure AD można zażądać po połączeniu tego identyfikatora klienta z dodatkowymi parametrami. Aby zażądać tokenu, określ wartości w poniższej tabeli na podstawie środowiska platformy Azure.

| Środowisko platformy Azure   | Punkt końcowy tokenu usługi Azure AD |
| --------------------|-------------------------|
| Azure — publiczna        | https://login.microsoftonline.com |
| Azure Government    | https://login.microsoftonline.us |


Aby uzyskać więcej informacji na temat konfigurowania usługi Azure AD i żądania tokenów dla usługi Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w usłudze Azure Maps.](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)

Aby uzyskać ogólne informacje dotyczące żądania tokenów z usługi Azure AD, zobacz [Co to jest uwierzytelnianie?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Żądanie zasobów mapy platformy Azure za pomocą tokenów OAuth

Po odebraniu tokenu usługi Azure AD usługa Azure Maps wysyła żądanie z następującym zestawem wymaganych nagłówków żądań:

| Nagłówek żądania    |    Wartość    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| Autoryzacja     | Bearer eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id`to identyfikator GUID oparty na koncie usługi Azure Maps, który pojawia się na stronie uwierzytelniania usługi Azure Maps.

Oto przykład żądania trasy usługi Azure Maps, które używa tokenu OAuth:

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Aby uzyskać informacje dotyczące wyświetlania identyfikatora klienta, zobacz [Wyświetlanie szczegółów uwierzytelniania](https://aka.ms/amauthdetails).

## <a name="control-access-with-rbac"></a>Kontroluj dostęp dzięki RBAC

W usłudze Azure AD użyj funkcji RBAC, aby kontrolować dostęp do zabezpieczonych zasobów. Skonfiguruj swoje konto usługi Azure Maps i zarejestruj dzierżawę usługi Azure Maps Azure AD. Usługa Azure Maps obsługuje kontrolę dostępu do odczytu dla poszczególnych użytkowników, grup, aplikacji, zasobów platformy Azure i usług platformy Azure za pośrednictwem zarządzanych tożsamości zasobów platformy Azure. Na stronie portalu usługi Azure Maps można skonfigurować rbac dla wybranych ról.

![Czytnik danych usługi Azure Maps (wersja zapoznawcza)](./media/azure-maps-authentication/concept.png)

Aby uzyskać informacje dotyczące wyświetlania ustawień RBAC, zobacz [Jak skonfigurować rbac dla usługi Azure Maps](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Tożsamości zarządzane dla zasobów platformy Azure i usługi Azure Maps

[Tożsamości zarządzane dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) zapewniają usługom platformy Azure automatycznie zarządzaną tożsamość, która może być autoryzowana do uzyskiwania dostępu do usług Usługi Azure Maps. Oto kilka przykładów tożsamości zarządzanych, w tym: usługa Azure App Service, usługi Azure Functions i maszyny wirtualne platformy Azure.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o uwierzytelnianiu aplikacji za pomocą usługi Azure AD i usługi Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w usłudze Azure Maps.](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)

* Aby dowiedzieć się więcej o uwierzytelnianiu kontroli map usługi Azure Maps i usługi Azure AD, zobacz [Korzystanie z formantu mapy usługi Azure Maps](https://aka.ms/amaadmc).
