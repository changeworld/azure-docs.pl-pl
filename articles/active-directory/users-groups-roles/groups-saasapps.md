---
title: Używanie grupy do zarządzania dostępem do aplikacji SaaS — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak używać grup w usłudze Azure Active Directory można przypisać dostępu do aplikacji SaaS, które są zintegrowane z usługą Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 873863016d6dc54c7439ec1f46180b3c063bda19
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60470419"
---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>Używanie grupy do zarządzania dostępem do aplikacji SaaS

Za pomocą usługi Azure Active Directory (Azure AD) z licencją Azure AD Premium lub usługi Azure AD podstawowa, można użyć grup do udzielania dostępu do aplikacji SaaS, która jest zintegrowana z usługą Azure AD. Na przykład, jeśli chcesz przypisać dostęp dla działu marketingu, użyj pięć różnych aplikacji SaaS, należy można utworzyć grupę, która zawiera użytkowników w dziale marketingu, a następnie przypisz tej grupy do tych pięciu aplikacji SaaS, które są wymagane przez Dział marketingu. W ten sposób możesz zaoszczędzić czas dzięki zarządzaniu członkostwa w dziale marketingu w jednym miejscu. Następnie przypisywania użytkowników do aplikacji podczas zostaną dodane jako członkowie grupy marketing, i ich przypisania usunięte z aplikacji w przypadku usunięcia ich z grupy marketing. Ta funkcja może służyć wraz z setkami aplikacji, które można dodać z w galerii aplikacji usługi Azure AD.

> [!IMPORTANT]
> Ta funkcja służy tylko wtedy, gdy można uruchomić wersji próbnej usługi Azure AD Premium lub kupić licencje usługi Azure AD Premium lub usługi Azure AD podstawowa. Przypisanie oparte na grupach jest obsługiwana tylko dla grup zabezpieczeń. Aktualnie w ramach przypisywania do aplikacji na podstawie grup nie jest obsługiwane członkostwo w grupach zagnieżdżonych.

**Można przypisać dostępu dla użytkownika lub grupy do aplikacji SaaS**

1. W [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com), wybierz opcję **aplikacje dla przedsiębiorstw**.
2. Wybierz aplikację dodano z galerii aplikacji, aby go otworzyć.
3. Wybierz **użytkowników i grup**, a następnie wybierz pozycję **Dodaj użytkownika**.
4. Na **Dodaj przydziału**, wybierz opcję **użytkowników i grup** otworzyć **użytkowników i grup** listy wyboru.
6. Wybierz dowolną liczbę grup lub użytkowników, jak chcesz, następnie kliknij lub naciśnij **wybierz** Aby dodać je do **Dodaj przydziału** listy. Na tym etapie można także przypisać rolę użytkownikowi.
7. Wybierz **przypisać** przypisać użytkowników lub grup do aplikacji wybranej przedsiębiorstwa.

### <a name="next-steps"></a>Kolejne kroki
Te artykuły zawierają dodatkowe informacje o usłudze Azure Active Directory.

* [Zarządzanie dostępem do zasobów za pomocą grup usługi Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Zarządzanie aplikacjami w usłudze Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](groups-settings-cmdlets.md)
* [Co to jest usługa Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
