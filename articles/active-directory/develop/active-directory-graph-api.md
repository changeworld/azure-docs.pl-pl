---
title: Azure Active Directory interfejs API programu Graph | Microsoft Docs
description: Przewodnik przegląd i szybki start dotyczący usługi Azure AD interfejs API programu Graph, który umożliwia programistyczny dostęp do usługi Azure AD za pomocą punktów końcowych interfejsu API REST.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 5471ad74-20b3-44df-a2b5-43cde2c0a045
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: dkershaw, sureshja
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: ef042b9eb625a0d0de5d5dcb883b823c3a499aa9
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76698921"
---
# <a name="azure-active-directory-graph-api"></a>Interfejs API programu Graph usługi Azure Active Directory

> [!IMPORTANT]
> Zdecydowanie zalecamy używanie [Microsoft Graph](https://developer.microsoft.com/graph) zamiast usługi Azure AD interfejs API programu Graph do uzyskiwania dostępu do zasobów Azure Active Directory (Azure AD). Obecnie koncentrujemy nasze działania deweloperskie na programie Microsoft Graph i nie planujemy żadnych dodatkowych rozszerzeń dla interfejsu API funkcji Azure AD Graph. Istnieje bardzo ograniczona liczba scenariuszy, w których usługa Azure AD interfejs API programu Graph nadal może być odpowiednia; Aby uzyskać więcej informacji, zapoznaj się z wpisami w blogu [Microsoft Graph lub Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) i [Migrowanie aplikacji Azure ad Graph do Microsoft Graph](https://docs.microsoft.com/graph/migrate-azure-ad-graph-overview).

Ten artykuł ma zastosowanie do usługi Azure AD interfejs API programu Graph. Aby poznać podobne informacje dotyczące interfejsu API Microsoft Graph, zobacz [Korzystanie z interfejsu api Microsoft Graph](https://docs.microsoft.com/graph/use-the-api).

Interfejs API usługi Azure Active Directory Graph zapewnia dostęp programistyczny do usługi Azure AD za pomocą punktów końcowych interfejsu API REST. Aplikacje mogą używać usługi Azure AD interfejs API programu Graph do wykonywania operacji tworzenia, odczytu, aktualizacji i usuwania (CRUD) na danych i obiektach katalogu. Na przykład usługa Azure AD interfejs API programu Graph obsługuje następujące typowe operacje dla obiektu użytkownika:

* Tworzenie nowego użytkownika w katalogu
* Uzyskiwanie szczegółowych właściwości użytkownika, takich jak ich grupy
* Aktualizowanie właściwości użytkownika, takich jak lokalizacja i numer telefonu, lub zmiana hasła
* Sprawdź członkostwo w grupie użytkownika w celu uzyskania dostępu opartego na rolach
* Wyłącz konto użytkownika lub usuń je całkowicie

Ponadto można wykonywać podobne operacje na innych obiektach, takich jak grupy i aplikacje. Aby wywoływać interfejs API programu Graph usługi Azure AD w katalogu, aplikacja musi być zarejestrowana w usłudze Azure AD. Do aplikacji należy również uzyskać dostęp do usługi Azure AD interfejs API programu Graph. Ten dostęp zazwyczaj uzyskuje się za pomocą przepływu zgody użytkownika lub administratora.

Aby rozpocząć korzystanie z interfejs API programu Graph Azure Active Directory, zapoznaj się z [przewodnikiem Szybki Start dotyczącym usługi Azure ad interfejs API programu Graph](active-directory-graph-api-quickstart.md)lub Wyświetl [dokumentację referencyjną usługi Azure AD interfejs API programu Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="features"></a>Funkcje

Usługa Azure AD interfejs API programu Graph udostępnia następujące funkcje:

* **Punkty końcowe interfejsu API REST**: usługa Azure AD interfejs API programu Graph jest usługą RESTfulą obejmującą punkty końcowe, do których uzyskuje się dostęp przy użyciu standardowych żądań HTTP. Usługa Azure AD interfejs API programu Graph obsługuje typy zawartości XML lub JavaScript (JSON) dla żądań i odpowiedzi. Aby uzyskać więcej informacji, zobacz [Dokumentacja interfejsu API REST usługi Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* **Uwierzytelnianie za pomocą usługi Azure AD**: każde żądanie do usługi azure AD interfejs API programu Graph musi zostać uwierzytelnione przez dołączenie tokenu w sieci Web JSON (JWT) w nagłówku autoryzacji żądania. Ten token jest uzyskiwany przez utworzenie żądania punktu końcowego tokenu usługi Azure AD i podanie prawidłowych poświadczeń. Możesz użyć przepływu poświadczeń klienta OAuth 2,0 lub przepływu przydzielenia kodu autoryzacji, aby uzyskać token do wywołania grafu. Aby uzyskać więcej informacji, [OAuth 2,0 w usłudze Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* **Autoryzacja oparta na rolach (RBAC)** : grupy zabezpieczeń służą do przeprowadzania RBAC w usłudze Azure AD interfejs API programu Graph. Na przykład jeśli chcesz określić, czy użytkownik ma dostęp do określonego zasobu, aplikacja może wywołać operację [Sprawdź członkostwo w grupie (przechodnie)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/functions-and-actions#checkMemberGroups) , która zwraca wartość PRAWDA lub FAŁSZ.
* **Zapytanie różnicowe**: zapytanie różnicowe umożliwia śledzenie zmian w katalogu między dwoma okresami bez konieczności częstego wykonywania zapytań w usłudze Azure AD interfejs API programu Graph. Ten typ żądania zwróci tylko zmiany wprowadzone między poprzednim żądaniem zapytania różnicowego a bieżącym żądaniem. Aby uzyskać więcej informacji, zobacz [zapytanie różnicowe usługi Azure AD interfejs API programu Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).
* **Rozszerzenia katalogów**: można dodawać niestandardowe właściwości do obiektów katalogu bez konieczności zewnętrznego magazynu danych. Na przykład jeśli aplikacja wymaga właściwości identyfikatora Skype dla każdego użytkownika, można zarejestrować nową właściwość w katalogu i będzie ona dostępna do użycia dla każdego obiektu użytkownika. Aby uzyskać więcej informacji, zobacz [rozszerzenia schematu katalogu usługi Azure AD interfejs API programu Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).
* **Zabezpieczone przez zakresy uprawnień**: usługa Azure AD interfejs API programu Graph uwidacznia zakresy uprawnień, które umożliwiają bezpieczny dostęp do danych usługi Azure AD przy użyciu protokołu OAuth 2,0. Obsługuje różne typy aplikacji klienckich, w tym:
  
  * interfejsy użytkownika, które mają delegowany dostęp do danych za pośrednictwem autoryzacji z zalogowanego użytkownika (delegowany)
  * service/daemon applications that operate in the background without a signed-in user being present and use application-defined role-based access control
    
    Both delegated and application permissions represent a privilege exposed by the Azure AD Graph API and can be requested by client applications through application registration permissions features in the [Azure portal](https://portal.azure.com). [Azure AD Graph API permission scopes](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes) provides information on what's available for use by your client application.

## <a name="scenarios"></a>Scenariusze

Azure AD Graph API enables many application scenarios. The following scenarios are the most common:

* **Line of Business (Single Tenant) Application**: In this scenario, an enterprise developer works for an organization that has an Office 365 subscription. The developer is building a web application that interacts with Azure AD to perform tasks such as assigning a license to a user. This task requires access to the Azure AD Graph API, so the developer registers the single tenant application in Azure AD and configures read and write permissions for Azure AD Graph API. Then the application is configured to use either its own credentials or those of the currently sign-in user to acquire a token to call the Azure AD Graph API.
* **Software as a Service Application (Multi-Tenant)** : In this scenario, an independent software vendor (ISV) is developing a hosted multi-tenant web application that provides user management features for other organizations that use Azure AD. These features require access to directory objects, so the application needs to call the Azure AD Graph API. The developer registers the application in Azure AD, configures it to require read and write permissions for Azure AD Graph API, and then enables external access so that other organizations can consent to use the application in their directory. When a user in another organization authenticates to the application for the first time, they are shown a consent dialog with the permissions the application is requesting. Granting consent will then give the application those requested permissions to Azure AD Graph API in the user’s directory. For more information on the consent framework, see [Overview of the consent framework](consent-framework.md).

## <a name="next-steps"></a>Następne kroki

To begin using the Azure Active Directory Graph API, see the following topics:

* [Azure AD Graph API quickstart guide](active-directory-graph-api-quickstart.md)
* [Azure AD Graph REST documentation](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
