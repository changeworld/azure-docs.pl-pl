---
title: Synchronizowanie kont partnerów lokalnych z chmurą jako użytkownicy B2B — Usługa Azure AD
description: Zapewnij lokalnym partnerom zewnętrznym dostęp do zasobów lokalnych i chmurowych przy użyciu tych samych poświadczeń dzięki współpracy usługi Azure AD B2B.
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
ms.openlocfilehash: dcc8c0538bb3362818a4172dd42905fd72b19812
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272612"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Udzielanie dostępu do zasobów w chmurze zarządzanych lokalnie przez konta partnerów przy użyciu współpracy usługi Azure AD B2B

Przed usłudze Azure Active Directory (Azure AD) organizacje z lokalnymi systemami tożsamości tradycyjnie zarządzały kontami partnerów w swoim katalogu lokalnym. W takiej organizacji po rozpoczęciu przenoszenia aplikacji do usługi Azure AD chcesz upewnić się, że twoi partnerzy mogą uzyskiwać dostęp do potrzebnych zasobów. Nie powinno mieć znaczenia, czy zasoby są lokalne lub w chmurze. Ponadto chcesz, aby użytkownicy partnera mogli używać tych samych poświadczeń logowania zarówno dla zasobów lokalnych, jak i usługi Azure AD. 

Jeśli tworzysz konta dla partnerów zewnętrznych w katalogu lokalnym (na przykład tworzysz konto o nazwie logowania "wmoran" dla użytkownika zewnętrznego o nazwie Wendy Moran w domenie partners.contoso.com), możesz teraz zsynchronizować te konta z Chmurze. W szczególności można użyć usługi Azure AD Connect do synchronizacji kont partnerów do chmury jako użytkowników usługi Azure AD B2B (czyli użytkowników z UserType = Guest). Dzięki temu użytkownicy partnerów mogą uzyskiwać dostęp do zasobów w chmurze przy użyciu tych samych poświadczeń co ich konta lokalne, bez udzielania im większego dostępu, niż potrzebują. 

## <a name="identify-unique-attributes-for-usertype"></a>Identyfikowanie unikatowych atrybutów dla typu użytkownika

Przed włączeniem synchronizacji atrybutu UserType należy najpierw zdecydować, jak wyprowadzić atrybut UserType z lokalnej usługi Active Directory. Innymi słowy, jakie parametry w środowisku lokalnym są unikatowe dla zewnętrznych współpracowników? Określ parametr, który odróżnia tych zewnętrznych współpracowników od członków własnej organizacji.

Dwa wspólne podejścia do tego celu to:

- Wyznacz nieużywane lokalne atrybuty usługi Active Directory (na przykład extensionAttribute1), aby użyć go jako atrybutu źródłowego. 
- Alternatywnie należy wyprowadzić wartość dla UserType atrybut z innych właściwości. Na przykład chcesz zsynchronizować wszystkich użytkowników jako gość, jeśli ich lokalny atrybut UserPrincipalName usługi Active Directory kończy się * \@partners.contoso.com*domeny .
 
Aby uzyskać szczegółowe wymagania dotyczące [atrybutów, zobacz Włączanie synchronizacji typu użytkownika](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Konfigurowanie usługi Azure AD Connect w celu synchronizowania użytkowników z chmurą

Po zidentyfikowaniu unikatowego atrybutu można skonfigurować usługę Azure AD Connect, aby zsynchronizować tych użytkowników z chmurą jako użytkownicy usługi Azure AD B2B (czyli użytkownicy z usertype = gość). Z punktu widzenia autoryzacji tych użytkowników są nie do odróżnienia od użytkowników B2B utworzonych za pośrednictwem procesu zaproszenia do współpracy usługi Azure AD B2B.

Aby uzyskać instrukcje implementacji, zobacz [Włączanie synchronizacji typu użytkownika](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Następne kroki

- [Współpraca b2b usługi Azure Active Directory dla organizacji hybrydowych](hybrid-organizations.md)
- [Przyznawanie użytkownikom B2B w usłudze Azure AD dostępu do aplikacji lokalnych](hybrid-cloud-to-on-premises.md)
- Aby zapoznać się z omówieniem usługi Azure AD Connect, zobacz [Integrowanie katalogów lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

