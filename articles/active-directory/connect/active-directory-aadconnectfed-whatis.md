---
title: Azure AD Connect i Federacji | Dokumentacja firmy Microsoft
description: Ta strona jest centralną lokalizację dla wszystkich dokumentację dotyczącą operacji usług AD FS, które używają usługi Azure AD Connect.
services: active-directory
documentationcenter: ''
author: anandyadavmsft
manager: mtillman
editor: ''
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.component: hybrid
ms.author: anandy
ms.openlocfilehash: 6c14b673bd9664aad54ea8002175999470661e34
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593857"
---
# <a name="azure-ad-connect-and-federation"></a>Program Azure AD Connect a federacja
Azure umożliwia połączenia usługi Active Directory (Azure AD), konfigurowanie federacji z lokalnej usługi Active Directory Federation Services (AD FS) i Azure AD. Z federacyjnego logowania można umożliwić użytkownikom zalogować się do usług platformy Azure AD na podstawie ich hasłami lokalnymi--i, znajduje się w sieci firmowej, bez konieczności ponownego wprowadzania haseł. Przy użyciu opcji federacyjnego z usługami AD FS, można wdrażać nowej instalacji usług AD FS, lub można określić istniejącą instalację w farmie programu Windows Server 2012 R2.

Ten temat jest stroną główną dla informacji o funkcji związanych z federacyjnych Azure AD Connect. Zawiera także łącza do wszystkie tematy pokrewne. Łącza do usługi Azure AD Connect, zobacz [integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).

## <a name="azure-ad-connect-federation-topics"></a>Azure AD Connect: tematy federacyjnych
| Temat | Co obejmuje i kiedy do jego odczytu |
|:--- |:--- |
| **Azure AD Connect użytkownika opcje logowania** | |
| [Zrozumienie opcje logowania użytkowników](active-directory-aadconnect-user-signin.md) |Poznaj różne opcje logowania użytkowników i ich wpływ na środowisko Azure logowania użytkownika. |
| **Instalowanie usług AD FS przy użyciu usługi Azure AD Connect** | |
| [Wymagania wstępne](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) |Zobacz wymagania wstępne dla pomyślnej instalacji usług AD FS za pomocą usługi Azure AD Connect. |
| [Konfigurowanie farmy usług AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) |Instalowanie nowej farmy usług AD FS przy użyciu usługi Azure AD Connect. |
| [Utworzenie federacji z usługą Azure AD przy użyciu alternatywnego Identyfikatora logowania ](active-directory-aadconnect-federation-management.md#alternateid) | Konfigurowanie Federacji przy użyciu alternatywnego Identyfikatora logowania  |
| **Modyfikowanie konfiguracji usług AD FS** | |
| [Napraw zaufania](active-directory-aadconnect-federation-management.md#repairthetrust) |Naprawy bieżącej relacji zaufania między lokalnymi usług AD FS i Office 365/Azure. |
| [Dodawanie nowego serwera usług AD FS](active-directory-aadconnect-federation-management.md#addadfsserver) |Po wstępnej instalacji, rozwiń węzeł farmy usług AD FS jest dodatkowy serwer usług AD FS. |
| [Dodaj nowy serwer AD FS WAP](active-directory-aadconnect-federation-management.md#addwapserver) |Rozwiń węzeł farmy usług AD FS jest dodatkowy serwer Proxy aplikacji sieci Web (WAP) po wstępnej instalacji. |
| [Dodaj nową domenę federacyjnych](active-directory-aadconnect-federation-management.md#addfeddomain) |Dodaj inną domenę federacyjną z usługą Azure AD. |
| [Aktualizuj certyfikat protokołu SSL](active-directory-aadconnectfed-ssl-update.md)| Aktualizuj certyfikat protokołu SSL dla farmy usług AD FS. |
| [Odnawianie certyfikatów Federacji dla usługi Office 365 i Azure AD](active-directory-aadconnect-o365-certs.md)|Odnawianie certyfikatu usługi Office 365 z usługą Azure AD.|
| **Inna konfiguracja federacyjna** | |
| [Federowanie wielu wystąpień usługi Azure AD przy użyciu jednego wystąpienia usług AD FS](active-directory-aadconnectfed-single-adfs-multitenant-federation.md) | Federację wielu usługi Azure AD z jednym farmy usług AD FS| 
| [Dodać logo firmy/ilustracji](active-directory-aadconnect-federation-management.md#customlogo) |Zmodyfikuj środowiska logowania, określając niestandardowe logo, które jest wyświetlany na stronie logowania usług AD FS. |
| [Dodaj opis logowania](active-directory-aadconnect-federation-management.md#addsignindescription) |Zmień opis logowania na stronie logowania usług AD FS. |
| [Modyfikowanie reguł oświadczeń usług AD FS](active-directory-aadconnect-federation-management.md#modclaims) |Modyfikowanie lub dodawanie reguł oświadczeń w usługach AD FS, który odpowiada konfiguracji synchronizacji usługi Azure AD Connect. |


## <a name="additional-resources"></a>Zasoby dodatkowe
* [Federowania dwie usługi Azure AD z jednym usług AD FS](active-directory-aadconnectfed-single-adfs-multitenant-federation.md)
* [Wdrażanie usług AD FS w systemie Azure](active-directory-aadconnect-azure-adfs.md)
* [Wysokiej dostępności geograficznej między AD FS wdrożenia na platformie Azure z usługą Azure Traffic Manager](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)
