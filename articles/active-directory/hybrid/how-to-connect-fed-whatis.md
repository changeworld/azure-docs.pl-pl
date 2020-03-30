---
title: Usługa Azure AD Connect i federacja | Dokumenty firmy Microsoft
description: Ta strona jest centralną lokalizacją dla całej dokumentacji dotyczącej operacji usług AD FS korzystających z usługi Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0518c58abf156c718ee083ffadb0ef8e0a590252
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331542"
---
# <a name="azure-ad-connect-and-federation"></a>Program Azure AD Connect a federacja
Usługa Azure Active Directory (Azure AD) Connect umożliwia skonfigurowanie federacji przy pomocy lokalnych usług federacyjnych Active Directory (AD FS) i usługi Azure AD. Za pomocą logowania federacyjnego można umożliwić użytkownikom logowanie się do usług opartych na usłudze Azure AD przy ich hasłach lokalnych — i podczas gdy w sieci firmowej, bez konieczności ponownego wprowadzania haseł. Korzystając z opcji federacyjnej z usługą AD FS, można wdrożyć nową instalację usług AD FS lub określić istniejącą instalację w farmie systemu Windows Server 2012 R2.

W tym temacie znajdują się informacje o funkcjach związanych z federacją usługi Azure AD Connect. Zawiera listę linków do wszystkich powiązanych tematów. Aby uzyskać łącza do usługi Azure AD Connect, zobacz [Integrowanie tożsamości lokalnych z usługą Azure Active Directory.](whatis-hybrid-identity.md)

## <a name="azure-ad-connect-federation-topics"></a>Usługa Azure AD Connect: tematy federacji
| Temat | Co obejmuje i kiedy go przeczytać |
|:--- |:--- |
| **Opcje logowania użytkownika usługi Azure AD Connect** | |
| [Opis opcji logowania użytkownika](plan-connect-user-signin.md) |Dowiedz się więcej o różnych opcjach logowania użytkownika i ich wpływ na środowisko logowania do logowania platformy Azure. |
| **Instalowanie usług AD FS przy użyciu usługi Azure AD Connect** | |
| [Wymagania wstępne](how-to-connect-install-custom.md#ad-fs-configuration-pre-requisites) |Zobacz wymagania wstępne pomyślnej instalacji usług AD FS za pośrednictwem usługi Azure AD Connect. |
| [Konfigurowanie farmy usług AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs) |Zainstaluj nową farmę usług AD FS przy użyciu usługi Azure AD Connect. |
| [Sedanowanie z usługą Azure AD przy użyciu alternatywnego identyfikatora logowania](how-to-connect-fed-management.md#alternateid) | Konfigurowanie federacji przy użyciu alternatywnego identyfikatora logowania  |
| **Modyfikowanie konfiguracji usług AD FS** | |
| [Naprawianie zaufania](how-to-connect-fed-management.md#repairthetrust) |Napraw bieżące zaufanie między lokalnymi plikami AD FS a usługą Office 365/Azure. |
| [Dodawanie nowego serwera usług AD FS](how-to-connect-fed-management.md#addadfsserver) |Rozwiń farmę usług AD FS o dodatkowy serwer usług AD FS po wstępnej instalacji. |
| [Dodawanie nowego serwera WAP usług AD FS](how-to-connect-fed-management.md#addwapserver) |Rozwiń farmę usług AD FS za pomocą dodatkowego serwera proxy aplikacji sieci Web (WAP) po wstępnej instalacji. |
| [Dodawanie nowej domeny federacyjnej](how-to-connect-fed-management.md#addfeddomain) |Dodaj inną domenę, która ma być sfederowana z usługą Azure AD. |
| [Aktualizowanie certyfikatu TLS/SSL](how-to-connect-fed-ssl-update.md)| Aktualizowanie certyfikatu TLS/SSL dla farmy usług AD FS. |
| [Odnawianie certyfikatów federacyjnych dla usługi Office 365 i usługi Azure AD](how-to-connect-fed-o365-certs.md)|Odnawianie certyfikatu usługi O365 za pomocą usługi Azure AD.|
| **Inna konfiguracja federacji** | |
| [Federowanie wielu wystąpień usługi Azure AD przy użyciu jednego wystąpienia usługi AD FS](how-to-connect-fed-single-adfs-multitenant-federation.md) | Sfowaj wiele usług Azure AD za pomocą jednej farmy usług AD FS| 
| [Dodawanie niestandardowego logo/ilustracji firmy](how-to-connect-fed-management.md#customlogo) |Zmodyfikuj środowisko logowania, określając niestandardowe logo wyświetlane na stronie logowania usług AD FS. |
| [Dodawanie opisu logowania](how-to-connect-fed-management.md#addsignindescription) |Zmień opis logowania na stronie logowania usług AD FS. |
| [Modyfikowanie reguł oświadczeń usług AD FS](how-to-connect-fed-management.md#modclaims) |Modyfikowanie lub dodawanie reguł oświadczeń w usługach AD FS, które odpowiadają konfiguracji synchronizacji usługi Azure AD Connect. |


## <a name="additional-resources"></a>Zasoby dodatkowe
* [Federacja dwóch usług Azure AD za pomocą pojedynczych usług AD FS](how-to-connect-fed-single-adfs-multitenant-federation.md)
* [Wdrożenie usług AD FS na platformie Azure](how-to-connect-fed-azure-adfs.md)
* [Wdrażanie usług AD FS o wysokiej dostępności na platformie Azure za pomocą usługi Azure Traffic Manager](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)
