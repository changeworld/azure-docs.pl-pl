---
title: Jak przypisać użytkowników do aplikacji | Dokumenty firmy Microsoft
description: Dowiedz się, jak użytkownicy są przypisywani do aplikacji w dzierżawie
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: b818fe1d8b6bbc9d2d8c5b460b4d71dccdd39366
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65825986"
---
# <a name="how-to-assign-users-to-applications"></a>Jak przypisać użytkowników do aplikacji

Ten artykuł ułatwia zrozumienie, jak użytkownicy są przypisywani do aplikacji w dzierżawie.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Jak użytkownicy są przypisywani do aplikacji w usłudze Azure AD?

Aby użytkownik miał dostęp do aplikacji, należy najpierw przypisać do niej w jakiś sposób. Przypisanie może być wykonywane przez administratora, pełnomocnika biznesowego, a czasami samego użytkownika. Poniżej opisano sposoby przypisywania użytkowników do aplikacji:

1.  Administrator [przypisuje użytkownika](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) bezpośrednio do aplikacji

2.  Administrator przypisuje do aplikacji [grupę,](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) do których użytkownik jest członkiem, w tym:

    * Grupa, która została zsynchronizowana z lokalnego

    * Statyczna grupa zabezpieczeń utworzona w chmurze

    * [Dynamiczna grupa zabezpieczeń](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal) utworzona w chmurze

    * Grupa usługi Office 365 utworzona w chmurze

    * Grupa [Wszyscy użytkownicy](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups)

3.  Administrator umożliwia [samoobsługowy dostęp](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) do aplikacji, aby umożliwić użytkownikowi dodawanie aplikacji przy użyciu funkcji Dodaj **aplikację** z panelu dostępu [do aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) bez zgody **firmy**

4.  Administrator umożliwia [samoobsługowy dostęp](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) do aplikacji, aby umożliwić użytkownikowi dodawanie aplikacji za pomocą funkcji Dodaj **aplikację** panelu dostępu [do aplikacji,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) ale tylko w**przypadku uprzedniej zgody wybranego zestawu osób zatwierdzających dane biznesowe**

5.  Administrator umożliwia [samoobsługowe zarządzanie grupami,](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) aby umożliwić użytkownikowi dołączenie do grupy, do których aplikacja jest przypisana **bez zgody firmy**

6.  Administrator umożliwia [samoobsługowe zarządzanie grupami,](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) aby umożliwić użytkownikowi dołączenie do grupy, do których aplikacja jest przypisana, ale tylko **za uprzednią zgodą wybranego zestawu osób zatwierdzających firmy**

7.  Administrator przypisuje licencję użytkownikowi bezpośrednio dla aplikacji innej firmy, takiej jak [Microsoft Office 365](https://products.office.com/)

8.  Administrator przypisuje licencję do grupy, do których użytkownik jest członkiem aplikacji innej firmy, takiej jak [Microsoft Office 365](https://products.office.com/)

9.  [Administrator wyraża zgodę na](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) korzystanie z aplikacji przez wszystkich użytkowników, a następnie użytkownik loguje się do aplikacji

10. Użytkownik [wyraża zgodę na samodzielne zgłoszenie,](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) logując się do aplikacji

## <a name="next-steps"></a>Następne kroki
[Zarządzanie aplikacjami za pomocą usługi Azure Active Directory](what-is-application-management.md)
