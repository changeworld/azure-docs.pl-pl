---
title: Co to jest inicjowanie obsługi administracyjnej w chmurze usługi Azure AD Connect. | Microsoft Docs
description: W tym artykule opisano inicjowanie obsługi administracyjnej w chmurze usługi Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a86d34fca9a88b0df601533a0f3de1cc97ad1a2f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80050595"
---
# <a name="what-is-azure-ad-connect-cloud-provisioning"></a>Co to jest aprowizacja w chmurze programu Azure AD Connect?
Inicjowanie obsługi administracyjnej w chmurze usługi Azure AD Connect to nowy agent firmy Microsoft zaprojektowany w celu osiągnięcia i osiągnięcia celów tożsamości hybrydowej w celu synchronizacji użytkowników, grup i kontaktów z usługą Azure AD.  Może być używany wraz z synchronizacją usługi Azure AD Connect i zapewnia następujące korzyści:
    
- Obsługa synchronizacji z dzierżawą usługi Azure AD z wielolesowego środowiska lasu usługi Active Directory: Typowe scenariusze obejmują fuzję & nabycia, w których lasy ad przejętej firmy są izolowane od lasów ad firmy nadrzędnej i firm, które w przeszłości miały wiele lasów usługi AD.
- Uproszczona instalacja z lekkimi agentami inicjowania obsługi administracyjnej: agenci działają jako mostek z usługi AD do usługi Azure AD, przy czym cała konfiguracja synchronizacji jest zarządzana w chmurze. 
- Wielu agentów inicjowania obsługi administracyjnej może służyć do upraszczania wdrożeń o wysokiej dostępności, szczególnie krytyczne dla organizacji korzystających z synchronizacji skrótów haseł z usługi AD do usługi Azure AD.


![Co to jest program Azure AD Connect](media/what-is-cloud-provisioning/architecture.png)

## <a name="how-is-azure-ad-connect-cloud-provisioning-different-from-azure-ad-connect-sync"></a>Czym różni się inicjowanie obsługi administracyjnej w chmurze usługi Azure AD Connect od synchronizacji usługi Azure AD Connect?
Dzięki inicjowaniu obsługi administracyjnej w chmurze usługi Azure AD Connect inicjowanie obsługi administracyjnej z usługi AD na usługę Azure AD jest aranżowane w usługach Microsoft Online Services. Organizacja musi tylko wdrożyć w środowisku lokalnym i hostowanym przez usługę IaaS lekki agent, który działa jako most między usługą Azure AD i AD. Konfiguracja inicjowania obsługi administracyjnej jest przechowywana w usłudze Azure AD i zarządzana jako część usługi.

Poniższa tabela zawiera porównanie między uaszczeniem obsługi administracyjnej w chmurze usługi Azure AD Connect i usługi Azure AD Connect:

| Funkcja | Synchronizacja łączy usługi Azure Active Directory| Inicjowanie obsługi administracyjnej w chmurze usługi Azure Active Directory Connect |
|:--- |:---:|:---:|
|Połączenie z pojedynczym lokalnym lasem usługi AD|● |● |
| Połączenie z wieloma lokalnymi lasami usługi AD |● |● |
| Łączenie się z wieloma odłączonych lokalnych lasami usługi AD | |● |
| Lekki model instalacji agenta | |● |
| Wiele aktywnych agentów o wysokiej dostępności | |● |
| Łączenie się z katalogami LDAP|●| | 
| Obsługa obiektów użytkowników |● |● |
| Obsługa obiektów grupy |● |● |
| Obsługa obiektów kontaktów |● |● |
| Obsługa obiektów urządzenia |● | |
| Zezwalaj na podstawowe dostosowywanie przepływów atrybutów |● |● |
| Atrybuty online programu Sychronize Exchange |● |● |
| Synchronizowanie atrybutów rozszerzenia 1-15 |● |● |
| Synchronizowanie atrybutów AD zdefiniowanych przez klienta (rozszerzenia katalogów) |● | |
| Obsługa synchronizacji skrótów haseł |●|●|
| Obsługa uwierzytelniania przekazywanego |●||
| Wsparcie dla federacji |●|●|
| Bezproblemowe logowanie jednokrotne|● |●|
| Obsługuje instalację na kontrolerze domeny |● |● |
| Obsługa systemu Windows Server 2012 i Windows Server 2012 R2 |● |● |
| Filtrowanie domen/u/grup/grup |● |● |
| Filtrowanie wartości atrybutów obiektów |● | |
| Umożliwia synchronizowanie minimalnego zestawu atrybutów (MinSync) |● |● |
| Umożliwia usuwanie atrybutów z przepływu z usługi AD do usługi Azure AD |● |● |
| Umożliwia zaawansowane dostosowywanie przepływów atrybutów |● | |
| Obsługa stornia zwrotnego (hasła, urządzenia, grupy) |● | |
| Obsługa usług domenowych usługi Azure AD|● | |
| [Hybrydowy odpis programu Exchange](../hybrid/reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) |● | |
| Obsługa ponad 50 000 obiektów na domenę usługi AD |● | |

## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Instalowanie inicjowania obsługi administracyjnej w chmurze](how-to-install.md)
