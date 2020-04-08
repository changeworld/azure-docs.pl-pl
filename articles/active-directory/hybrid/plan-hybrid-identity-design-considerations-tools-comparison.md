---
title: 'Tożsamość hybrydowa: porównanie narzędzi do integracji katalogów | Microsoft Docs'
description: Na tej stronie udostępniona jest kompleksowa tabela zawierająca porównanie różnych narzędzi integracji katalogów, których można użyć do integracji katalogów.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/07/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 052d99a819aee415d5e7ad6dc00b8c786af0f636
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811098"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Tożsamość hybrydowa: porównanie narzędzi do integracji katalogów
Narzędzia do integracji katalogów były rozbudowywane i rozwijane przez wiele lat.  


- [FIM](https://docs.microsoft.com/previous-versions/windows/desktop/forefront-2010/ff182370%28v%3dvs.100%29) i [MIM](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016) są nadal obsługiwane i przede wszystkim umożliwiają synchronizację między systemami lokalnymi.   [Łącznik USŁUGI FIM Windows Azure AD Connector](https://docs.microsoft.com/previous-versions/mim/dn511001(v=ws.10)?redirectedfrom=MSDN) jest obsługiwany zarówno w programie FIM, jak i MIM, ale nie jest zalecany w przypadku nowych wdrożeń — klienci korzystający ze źródeł lokalnych, takich jak Uwagi lub SAP HCM, powinni używać programu MIM do wypełniania usług domenowych Active Directory (AD DS), a następnie używać synchronizacji usługi Azure AD Connect lub inicjowania obsługi administracyjnej usługi Azure AD Connect w celu synchronizacji z usług AD DS na platformie Azure AD.
- [Synchronizacja usługi Azure AD Connect](how-to-connect-sync-whatis.md) zawiera składniki i funkcje wcześniej wydane w DirSync i usłudze Azure AD Sync do synchronizacji między lasami usług AD DS a usługą Azure AD.  
- [Inicjowanie obsługi administracyjnej w chmurze usługi Azure AD Connect](../cloud-provisioning/what-is-cloud-provisioning.md) to nowy agent firmy Microsoft do synchronizowania z usług AD DO usługi Azure AD, przydatny w scenariuszach, takich jak fuzja i przejęcie, w których lasy usługi AD przejętej firmy są izolowane od lasów usługi AD firmy nadrzędnej.

Aby dowiedzieć się więcej na temat różnic między synchronizacją usługi Azure AD Connect a aprowewaniem w chmurze usługi Azure AD Connect, zobacz artykuł [Co to jest inicjowanie obsługi administracyjnej w chmurze usługi Azure AD Connect?](../cloud-provisioning/what-is-cloud-provisioning.md)

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).

