---
title: Zarządzanie dostępem do aplikacji SaaS za pomocą grupy — Azure AD | Dokumenty firmy Microsoft
description: Jak używać grup w usłudze Azure Active Directory do przypisywania dostępu do aplikacji SaaS zintegrowanych z usługą Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51375f057543c86fe021822eb9722ffd1be16804
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74026858"
---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>Używanie grupy do zarządzania dostępem do aplikacji SaaS

Korzystając z usługi Azure Active Directory (Azure AD) z planem licencji usługi Azure AD Premium, można użyć grup, aby przypisać dostęp do aplikacji SaaS zintegrowanej z usługą Azure AD. Na przykład, jeśli chcesz przypisać dostęp do działu marketingu, aby użyć pięciu różnych aplikacji SaaS, możesz utworzyć grupę zawierającą użytkowników w dziale marketingu, a następnie przypisać tę grupę do tych pięciu aplikacji SaaS, które są potrzebne marketingu. W ten sposób możesz zaoszczędzić czas, zarządzając członkostwem w dziale marketingu w jednym miejscu. Użytkownicy są następnie przypisywani do aplikacji, gdy są dodawane jako członkowie grupy marketingowej i ich przypisania są usuwane z aplikacji, gdy są usuwane z grupy marketingowej. Tej funkcji można używać z setkami aplikacji, które można dodać z poziomu galerii aplikacji usługi Azure AD.

> [!IMPORTANT]
> Tej funkcji można używać tylko po uruchomieniu wersji próbnej usługi Azure AD Premium lub zakupie planu licencji usługi Azure AD Premium.
> Przypisanie oparte na grupach jest obsługiwane tylko dla grup zabezpieczeń.
> Aktualnie w ramach przypisywania do aplikacji na podstawie grup nie jest obsługiwane członkostwo w grupach zagnieżdżonych.

## <a name="to-assign-access-for-a-user-or-group-to-a-saas-application"></a>Aby przypisać dostęp dla użytkownika lub grupy do aplikacji SaaS

1. W [centrum administracyjnym usługi Azure AD](https://aad.portal.azure.com)wybierz pozycję **Aplikacje przedsiębiorstwa**.
2. Wybierz aplikację dodaną z Galerii aplikacji, aby ją otworzyć.
3. Wybierz **pozycję Użytkownicy i grupy**, a następnie wybierz pozycję Dodaj **użytkownika**.
4. W **obszarze Dodawanie przypisania**wybierz **pozycję Użytkownicy i grupy,** aby otworzyć listę wyboru **Użytkownicy i grupy.**
6. Zaznacz dowolną liczbę grup lub użytkowników, a następnie kliknij lub naciśnij pozycję **Zaznacz,** aby dodać je do listy **Dodaj przypisanie.** Na tym etapie można również przypisać rolę do użytkownika.
7. Wybierz **pozycję Przypisz,** aby przypisać użytkowników lub grupy do wybranej aplikacji przedsiębiorstwa.

## <a name="next-steps"></a>Następne kroki
Te artykuły zawierają dodatkowe informacje o usłudze Azure Active Directory.

* [Zarządzanie dostępem do zasobów za pomocą grup usługi Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Zarządzanie aplikacjami w usłudze Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](groups-settings-cmdlets.md)
* [Co to jest usługa Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
