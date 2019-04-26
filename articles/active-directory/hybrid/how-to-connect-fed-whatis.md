---
title: Usługa Azure AD Connect a Federacja | Dokumentacja firmy Microsoft
description: Ta strona jest centralnym miejscem służącym całej dokumentacji dotyczące operacji usług AD FS, korzystających z usługi Azure AD Connect.
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
origin.date: 10/09/2018
ms.date: 03/15/2019
ms.subservice: hybrid
ms.author: v-junlch
ms.collection: M365-identity-device-management
ms.openlocfilehash: 67ae5d2661371c256f753d05eb496d2cd53a0017
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60350494"
---
# <a name="azure-ad-connect-and-federation"></a>Program Azure AD Connect a federacja
Platformy Azure umożliwia usługi Active Directory (Azure AD) Connect skonfigurowania federacji z usługą Active Directory Federation Services (AD FS) lokalną i usługą Azure AD. Za pomocą logowania federacyjnego można umożliwić użytkownikom Zaloguj się do usług platformy Azure na podstawie usługi AD za pomocą swoich haseł lokalnych — oraz znajduje się w sieci firmowej, bez konieczności ponownego wprowadzania haseł. Przy użyciu opcji federacji z usługami AD FS, można wdrożyć nowej instalacji usług AD FS, lub można określić istniejącą instalację w farmie programu Windows Server 2012 R2.

Ten temat jest miejsce, w którym informacji na temat funkcji związanych z federacyjnego programu Azure AD Connect. Zawiera także łącza do wszystkich powiązanych tematów. Aby uzyskać łącza do programu Azure AD Connect, zobacz [integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).

## <a name="azure-ad-connect-federation-topics"></a>Azure AD Connect: tematy federacyjnych
| Temat | Co obejmuje i kiedy należy go odczytać |
|:--- |:--- |
| **Opcje logowania użytkowników w programie Azure AD Connect** | |
| [Omówienie opcji logowania użytkownika](plan-connect-user-signin.md) |Poznaj różne opcje logowania użytkowników i ich wpływ na środowisko logowania użytkownika usługi Azure. |
| **Instalowanie usług AD FS za pomocą usługi Azure AD Connect** | |
| [Wymagania wstępne](how-to-connect-install-custom.md#ad-fs-configuration-pre-requisites) |Zobacz wymagania wstępne dotyczące pomyślnej instalacji usług AD FS za pomocą usługi Azure AD Connect. |
| [Konfigurowanie farmy usług AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs) |Zainstaluj nową farmę usług AD FS za pomocą usługi Azure AD Connect. |
| [Utworzyć Federację z usługą Azure AD przy użyciu Identyfikatora logowania alternatywnej](how-to-connect-fed-management.md#alternateid) | Konfigurowanie Federacji przy użyciu Identyfikatora logowania alternatywnej  |
| **Modyfikowanie konfiguracji usług AD FS** | |
| [Napraw zaufanie](how-to-connect-fed-management.md#repairthetrust) |Napraw bieżącej relacji zaufania między lokalnych usług AD FS i Office 365/Azure. |
| [Dodawanie nowego serwera usług AD FS](how-to-connect-fed-management.md#addadfsserver) |Po wstępnej instalacji, rozwiń węzeł farmy usług AD FS jest dodatkowy serwer usług AD FS. |
| [Dodaj nowy serwer proxy aplikacji sieci Web programu AD FS](how-to-connect-fed-management.md#addwapserver) |Po wstępnej instalacji, rozwiń węzeł farmy usług AD FS jest dodatkowy serwer Proxy aplikacji sieci Web (WAP). |
| [Dodawanie nowej domeny federacyjnej](how-to-connect-fed-management.md#addfeddomain) |Dodaj inną domenę do sfederowania z usługą Azure AD. |
| [Aktualizuj certyfikat SSL](how-to-connect-fed-ssl-update.md)| Aktualizuj certyfikat SSL dla farmy usług AD FS. |
| [Odnawianie certyfikatów Federacji dla usługi Office 365 i Azure AD](how-to-connect-fed-o365-certs.md)|Odnawianie certyfikatu usługi Office 365 z usługą Azure AD.|
| **Inna konfiguracja Federacji** | |
| [Federowanie wielu wystąpień usługi Azure AD przy użyciu jednego wystąpienia usług AD FS](how-to-connect-fed-single-adfs-multitenant-federation.md) | Utworzyć Federację wielu usługi Azure AD za pomocą pojedynczej farmy usług AD FS| 
| [Dodaj niestandardowe logo/ilustracji](how-to-connect-fed-management.md#customlogo) |Zmodyfikuj środowisko logowania, określając niestandardowe logo, które są wyświetlane na stronie logowania usług AD FS. |
| [Dodaj opis logowanie](how-to-connect-fed-management.md#addsignindescription) |Zmień opis logowania na stronie logowania usług AD FS. |
| [Modyfikowanie reguł oświadczeń usług AD FS](how-to-connect-fed-management.md#modclaims) |Modyfikowanie lub dodawanie reguł oświadczeń w usługach AD FS, które odpowiadają na konfigurację synchronizacji programu Azure AD Connect. |


## <a name="additional-resources"></a>Dodatkowe zasoby
* [Federowanie dwie usługi Azure AD z jednym usług AD FS](how-to-connect-fed-single-adfs-multitenant-federation.md)
* [Wdrożenie usług AD FS na platformie Azure](how-to-connect-fed-azure-adfs.md)
* [Wysoka dostępność geograficznie rozproszonych AD FS wdrożenie na platformie Azure przy użyciu usługi Azure Traffic Manager](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)

<!-- Update_Description: update metedata properties -->