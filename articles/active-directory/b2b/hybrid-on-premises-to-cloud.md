---
title: Synchronizowanie kont lokalnych partnerów w chmurze jako użytkownicy B2B — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Zapewniają partnerom zewnętrznym zarządzane lokalnie, dostęp do lokalnego i zasobów w chmurze przy użyciu tych samych poświadczeń przy użyciu funkcji współpracy B2B usługi Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ae75311ab61449f37ccea15a0bcb88fed80c3ed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65767351"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Udzielanie lokalnie zarządzanych przez partnera kont dostępu do zasobów w chmurze przy użyciu współpracy B2B usługi Azure AD

Przed Azure Active Directory (Azure AD) za pomocą lokalnych systemów tożsamości organizacjach konta partnera tradycyjnie zarządzanych w jego katalogu lokalnego. W organizacji po uruchomieniu przenoszenia aplikacji do usługi Azure AD chcesz upewnij się, że partnerów można uzyskiwać dostęp do zasobów, które są im potrzebne. Nie ma to znaczenie, czy zasoby są w środowisku lokalnym lub w chmurze. Ponadto należy użytkownikom partnera, aby można było używać tych samych poświadczeń logowania, zarówno lokalnych, jak i zasobów usługi Azure AD. 

Jeśli tworzenie kont dla usługi partnerom zewnętrznym w Twoim katalogu w środowisku lokalnym (na przykład możesz utworzyć konto przy użyciu nazwy logowania "wmoran" dla użytkownika zewnętrznego o nazwie została Wendy w domenie partners.contoso.com), teraz możesz synchronizować tych kont do w chmurze. W szczególności program Azure AD Connect umożliwia synchronizowanie kont partnerów w chmurze jako użytkownicy usługi Azure AD B2B (oznacza to, że użytkownicy z wartością UserType = gościa). Dzięki temu partnera użytkownikom dostępu do zasobów w chmurze przy użyciu tych samych poświadczeń jako kont lokalnych bez udzielania im szerszy dostęp niż jest to wymagane. 

## <a name="identify-unique-attributes-for-usertype"></a>Identyfikowanie unikatowe atrybuty dla UserType

Przed włączeniem synchronizacji atrybut UserType, najpierw należy zdecydować, jak utworzyć pochodne atrybut UserType z usługi Active Directory w środowisku lokalnym. Innymi słowy parametry w danym środowisku lokalnych są unikatowe dla Twojej zewnętrznych współpracowników? Określ parametr, który rozróżnia tych zewnętrznych współpracowników z członkami danej organizacji.

Są dwa podejścia typowych dla tego:

- Należy określić atrybut usługi Active Directory nieużywane w środowisku lokalnym (na przykład extensionAttribute1) do użycia jako atrybutu źródłowego. 
- Alternatywnie dziedziczyć wartość atrybutu UserType inne właściwości. Na przykład chcesz synchronizować wszystkich użytkowników jako gościa, gdy jego atrybut UserPrincipalName usługi Active Directory w środowisku lokalnym, który kończy się z domeną  *\@partners.contoso.com*.
 
Atrybut szczegółowe wymaganiami, zobacz [Włącz synchronizację UserType](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Skonfiguruj program Azure AD Connect, aby zsynchronizować użytkowników z chmurą

Po zidentyfikowaniu unikatowy atrybut, można skonfigurować program Azure AD Connect do synchronizowania tych użytkowników w chmurze jako użytkownicy usługi Azure AD B2B (oznacza to, że użytkownicy z wartością UserType = gościa). Z punktu widzenia autoryzacji Ci użytkownicy są nie do odróżnienia od użytkowników B2B utworzonymi za pomocą procesu zapraszania współpracy B2B usługi Azure AD.

Aby uzyskać instrukcje dotyczące wdrażania, zobacz [Włącz synchronizację UserType](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Kolejne kroki

- [Współpracy usługi Azure Active Directory B2B dla organizacji hybrydowych](hybrid-organizations.md)
- [Użytkowników B2B przydział w usłudze Azure AD dostęp do aplikacji w środowisku lokalnym](hybrid-cloud-to-on-premises.md)
- Omówienie programu Azure AD Connect, zobacz [integrowanie katalogów lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

