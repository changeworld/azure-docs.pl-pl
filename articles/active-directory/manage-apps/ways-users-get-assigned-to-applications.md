---
title: Jak przypisać użytkowników do aplikacji | Dokumentacja firmy Microsoft
description: Zrozumienie, jak użytkownicy zostaną przypisane do aplikacji w dzierżawie
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: be36bfeb0991dc4a4b7fce723cadb6b508c0d6a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60291028"
---
# <a name="how-to-assign-users-to-applications"></a>Jak przypisać użytkowników do aplikacji

Ten artykuł ułatwia zrozumienie, jak użytkownicy zostaną przypisane do aplikacji w dzierżawie.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Jak użytkownicy zostaną przypisane do aplikacji w usłudze Azure AD?

Użytkownikowi dostęp do aplikacji ich należy przypisać do niej w jakiś sposób. Przypisania można wykonać przez administratora, obiekt delegowany biznesowych lub czasami użytkownik samodzielnie. Poniżej opisano, jak, które użytkownicy mogą zostać przypisani do aplikacji:

1.  Administrator [przypisuje użytkownika](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) bezpośrednio w aplikacji

2.  Administrator [przypisuje grupę](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) czy użytkownik jest członkiem do aplikacji, w tym:

    * Grupy, która była synchronizowana ze środowiska lokalnego

    * Grupy zabezpieczeń statyczne utworzone w chmurze

    * A [grupy zabezpieczeń dynamicznych](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal) utworzone w chmurze

    * Grupy usługi Office 365 utworzone w chmurze

    * [Wszyscy użytkownicy](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups) grupy

3.  Administrator włączy [samoobsługowego dostępu do aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) umożliwia użytkownikowi dodanie aplikacji za pomocą [panelu dostępu do aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **Dodaj aplikację** funkcji **bez zgody firmy**

4.  Administrator włączy [samoobsługowego dostępu do aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) umożliwia użytkownikowi dodanie aplikacji za pomocą [panelu dostępu do aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **Dodaj aplikację** funkcji, ale tylko w**ię od wcześniejszego zatwierdzenia z wybranym zestawem osoby zatwierdzające w firmie**

5.  Administrator włączy [Samoobsługowe zarządzanie grupami](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) zezwalający użytkownikowi dołączenia do grupy, która aplikacja jest przypisana do **bez zgody firmy**

6.  Administrator włączy [Samoobsługowe zarządzanie grupami](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) zezwalający użytkownikowi do dołączenia do grupy, która aplikacja jest przypisany do, ale tylko **przy pobieraniu z wybranym zestawem osoby zatwierdzające w firmie**

7.  Administrator przypisuje licencji użytkownika bezpośrednio do pierwszej aplikacji innych firm, takie jak [Microsoft Office 365](https://products.office.com/)

8.  Administrator przypisuje licencji do grupy, czy użytkownik jest członkiem do pierwszej aplikacji innych firm, takie jak [Microsoft Office 365](https://products.office.com/)

9.  [Administratora, który wyraża zgodę na aplikację](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) ma być używany przez wszystkich użytkowników, a następnie użytkownik loguje się do aplikacji

10. Użytkownik [wyraża zgodę na aplikację](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) samodzielnie, logując się do aplikacji

## <a name="next-steps"></a>Kolejne kroki
[Managing Applications with Azure Active Directory (Zarządzanie aplikacjami za pomocą usługi Azure Active Directory)](what-is-application-management.md)
