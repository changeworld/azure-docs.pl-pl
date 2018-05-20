---
title: Udziel zarządzane lokalnie partnera kont dostępu do zasobów w chmurze jako użytkowników B2B usługi Azure AD | Dokumentacja firmy Microsoft
description: Nadaj zarządzane lokalnie partnerami zewnętrznymi, dostęp do lokalnego i zasobów w chmurze przy użyciu tych samych poświadczeń przy współpracy B2B usługi Azure AD.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/24/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 71d5ce8728d876740d6ef00b55ecdc9232a06f80
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Udziel zarządzane lokalnie partnera kont dostępu do zasobów w chmurze przy użyciu współpracy B2B usługi Azure AD

Przed Azure Active Directory (Azure AD) organizacje z lokalnych systemów tożsamości ma tradycyjnie zarządzanych partnera kont w ich katalogu lokalnego. W organizacji podczas przenoszenia aplikacji do usługi Azure AD ma zostać upewnij się, że partnerów dostęp do zasobów, które są im potrzebne. Nie należy go znaczenia, czy zasoby są lokalnie lub w chmurze. Ponadto chcesz użytkowników partnera, aby móc używać tych samych poświadczeń logowania zarówno lokalnie, jak i zasobów usługi Azure AD. 

Po utworzeniu konta dla partnerów zewnętrznych, w katalogu lokalnego (na przykład możesz utworzyć konto przy użyciu nazwy logowania "wmoran" dla użytkownika zewnętrznego o nazwie Wendy Moran w domenie partners.contoso.com), mogą teraz synchronizować tych kont Chmura. W szczególności Azure AD Connect umożliwia synchronizowanie konta partnera w chmurze jako użytkowników B2B usługi Azure AD (to znaczy użytkownikom UserType = gościa). Dzięki temu partnera użytkownikom dostępu do zasobów w chmurze przy użyciu tych samych poświadczeń jako kont lokalnych, bez udzielania dostępu więcej niż jest to wymagane. 

## <a name="identify-unique-attributes-for-usertype"></a>Identyfikowanie unikatowych atrybutach UserType

Przed włączeniem synchronizacji atrybut UserType, najpierw należy zdecydować, jak pochodzi ten atrybut UserType z lokalnej usługi Active Directory. Innymi słowy jakich parametrów w danym środowisku lokalnych są unikatowe dla sieci zewnętrznych współpracowników? Określ parametr, który odróżnia tych zewnętrznych współpracowników od członków organizacji.

Typowe są dwa podejścia dla tego celu:

- Należy określić atrybut usługi Active Directory nieużywane lokalnymi (na przykład extensionAttribute1) do użycia jako atrybutu źródłowego. 
- Można również pochodzić atrybut UserType od innych właściwości. Na przykład chcesz synchronizować wszystkich użytkowników jako Gość, jeśli ich lokalne atrybut UserPrincipalName usługi Active Directory kończy się z domeną *@partners.contoso.com*.
 
Atrybut szczegółowe wymagania można znaleźć [Włącz synchronizację UserType](../connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Konfigurowanie usługi Azure AD Connect do synchronizacji użytkowników w chmurze

Po zidentyfikowaniu unikatowy atrybut można skonfigurować usługi Azure AD Connect do synchronizowania tych użytkowników zdalnych do chmury jako użytkowników B2B usługi Azure AD (to znaczy użytkownikom UserType = gościa). Z punktu widzenia autoryzacji użytkownicy są nierozróżnialne od użytkowników B2B została utworzona za pośrednictwem procesu zaproszenia współpracy B2B usługi Azure AD.

Aby uzyskać instrukcje dotyczące wdrażania, zobacz [Włącz synchronizację UserType](../connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Kolejne kroki

- [Azure współpracy B2B usługi Active Directory dla organizacji hybrydowego](hybrid-organizations.md)
- [Użytkownicy GRANT B2B w usłudze Azure AD dostęp do aplikacji lokalnych](hybrid-cloud-to-on-premises.md)
- Omówienie programu Azure AD Connect zobacz [integrację katalogów lokalnych z usługą Azure Active Directory](../connect/active-directory-aadconnect.md).

