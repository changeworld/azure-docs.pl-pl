---
title: Zmienianie planów licencji dla użytkowników i grup — Usługa Azure AD | Dokumenty firmy Microsoft
description: Jak migrować użytkowników w grupie do różnych planów usług przy użyciu licencjonowania grup w usłudze Azure Active Directory
services: active-directory
keywords: Zarządzanie licencjonowaniem w usłudze Azure AD
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf2f04e1728f94c89bddcc31c287cc017a79020f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025905"
---
# <a name="change-license-assignments-for-a-user-or-group-in-azure-active-directory"></a>Zmienianie przypisań licencji dla użytkownika lub grupy w usłudze Azure Active Directory

W tym artykule opisano sposób przenoszenia użytkowników i grup między planami licencji usługi w usłudze Azure Active Directory (Azure AD). Celem podejścia usługi Azure AD jest zapewnienie, że nie ma utraty usługi lub danych podczas zmiany licencji. Użytkownicy powinni bezproblemowo przełączać się między usługami. Kroki przypisywania planu licencji opisane w tym artykule opisują zmianę użytkownika lub grupy w usłudze Office 365 E1 na Office 365 E3, ale te kroki dotyczą wszystkich planów licencji. Podczas aktualizowania przypisań licencji dla użytkownika lub grupy, usunięcia przypisania licencji i nowe przypisania są dokonywane jednocześnie, tak aby użytkownicy nie utracili dostępu do swoich usług podczas zmian licencji lub zobaczyli konflikty licencji między planami.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed zaktualizowaniem przypisań licencji należy sprawdzić, czy niektóre założenia są spełnione, aby wszyscy użytkownicy lub grupy zostały zaktualizowane. Jeśli założenia nie są prawdziwe dla wszystkich użytkowników w grupie, migracja może zakończyć się niepowodzeniem dla niektórych. W rezultacie niektórzy użytkownicy mogą utracić dostęp do usług lub danych. Upewnij się, że:

- Użytkownicy mają bieżący plan licencji (w tym przypadku usługi Office 365 E1), który jest przypisany do grupy i dziedziczone przez użytkownika i nie przypisane bezpośrednio.

- Masz wystarczająco dużo dostępnych licencji dla przypisywany plan licencji. Jeśli nie masz wystarczającej liczby licencji, niektórzy użytkownicy mogą nie być przypisani do nowego planu licencji. Można sprawdzić liczbę dostępnych licencji.

- Użytkownicy nie mają innych przypisanych licencji na usługi, które mogą kolidować z żądaną licencją lub uniemożliwiać usunięcie bieżącej licencji. Na przykład licencja z usługi, takiej jak Workplace Analytics lub Project Online, która ma zależność od innych usług.

- Jeśli zarządzasz grupami lokalnie i synchronizujesz je z usługą Azure AD za pośrednictwem usługi Azure AD Connect, dodajesz lub usuwasz użytkowników przy użyciu systemu lokalnego. Synchronizacja zmian z usługą Azure AD może zająć trochę czasu, aby je uzyskać w ramach licencjonowania grupowego.

- Jeśli używasz członkostwa w grupach dynamicznych usługi Azure AD, możesz dodać lub usunąć użytkowników, zmieniając ich atrybuty, ale proces aktualizacji przypisań licencji pozostaje taki sam.

## <a name="change-user-license-assignments"></a>Zmienianie przypisań licencji użytkowników

Na stronie **Aktualizowanie przypisań licencji,** jeśli widzisz, że niektóre pola wyboru są niedostępne, wskazuje usługi, których nie można zmienić, ponieważ są dziedziczone z licencji grupy.

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com/) przy użyciu konta administratora licencji w organizacji usługi Azure AD.
1. Wybierz **pozycję Użytkownicy usługi Azure Active Directory** > **Users**, a następnie otwórz stronę **profilu** dla użytkownika.
1. Wybierz **licencje**.
1. Wybierz **pozycję Przydziały,** aby edytować przypisanie licencji dla użytkownika lub grupy. Na stronie Przydziały można rozwiązywać **konflikty** przypisania licencji.
1. Zaznacz pole wyboru dla usługi Office 366 E3 i upewnij się, że są zaznaczone co najmniej wszystkie usługi E1 przypisane do użytkownika.
1. Wyczyść pole wyboru dla usługi Office 365 E1.

    ![strona przypisań licencji dla użytkownika z wyczyszczoną pocztą Office 365 E1 i wybraną wybraną pozycją Office 365 E3](media/licensing-groups-change-licenses/update-user-license-assignments.png)

1. Wybierz **pozycję Zapisz**.

Usługa Azure AD stosuje nowe licencje i usuwa stare licencje jednocześnie w celu zapewnienia ciągłości usług.

## <a name="change-group-license-assignments"></a>Zmienianie przypisań licencji grupowych

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com/) przy użyciu konta administratora licencji w organizacji usługi Azure AD.
1. Wybierz **pozycję Grupy usługi Azure Active Directory** > **Groups**, a następnie otwórz stronę **Przegląd** dla grupy.
1. Wybierz **licencje**.
1. Wybierz polecenie **Przydziały,** aby edytować przypisanie licencji dla użytkownika lub grupy.
1. Zaznacz pole wyboru dla usługi Office 366 E3. Aby zachować ciągłość usługi, upewnij się, że wybierzesz wszystkie usługi E1, które są już przypisane do użytkownika.
1. Wyczyść pole wyboru dla usługi Office 365 E1.

    ![Wybieranie polecenia Przydziały na stronie Licencje użytkownika lub grupy](media/licensing-groups-change-licenses/update-group-license-assignments.png)

1. Wybierz **pozycję Zapisz**.

Aby zapewnić ciągłość usług, usługa Azure AD stosuje nowe licencje i usuwa stare licencje jednocześnie dla wszystkich użytkowników w grupie.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o innych scenariuszach zarządzania licencjami za pośrednictwem grup w następujących artykułach:

- [Przypisywanie licencji do grupy w usłudze Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)
- [Identyfikowanie i rozwiązywanie problemów z licencją dla grupy w usłudze Azure Active Directory](../users-groups-roles/licensing-groups-resolve-problems.md)
- [Jak przeprowadzić migrację poszczególnych licencjonowanych użytkowników do grupowania licencjonowania w usłudze Azure Active Directory](../users-groups-roles/licensing-groups-migrate-users.md)
- [Licencjonowanie grup usługi Azure Active Directory dodatkowe scenariusze](../users-groups-roles/licensing-group-advanced.md)
- [Przykłady programu PowerShell dotyczące licencjonowania grup w usłudze Azure Active Directory](../users-groups-roles/licensing-ps-examples.md)
