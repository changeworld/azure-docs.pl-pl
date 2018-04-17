---
title: Udostępnić pulpity nawigacyjne portalu Azure za pomocą RBAC | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono, jak udostępniać pulpitu nawigacyjnego w portalu Azure przy użyciu kontroli dostępu opartej na rolach.
services: azure-portal
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/01/2016
ms.author: tomfitz
ms.openlocfilehash: 75f06e49aa317fc887831a9e04861d9a7989bdb2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Udostępnianie pulpitów nawigacyjnych Azure przy użyciu kontroli dostępu opartej na rolach
Po skonfigurowaniu pulpitu nawigacyjnego, można ją opublikować i udostępnić go innym użytkownikom w organizacji. Możesz zezwolić na wyświetlanie pulpitu nawigacyjnego za pomocą usługi Azure [kontroli dostępu opartej na rolach](../role-based-access-control/role-assignments-portal.md). Przypisanie użytkownika lub grupy użytkowników do roli, a ta rola definiuje, czy tym użytkownikom można wyświetlać lub modyfikować opublikowane pulpitu nawigacyjnego. 

Wszystkie opublikowane pulpity nawigacyjne są zaimplementowane jako zasobów platformy Azure, co oznacza istnieje jako elementy można zarządzać w ramach subskrypcji i znajdują się w grupie zasobów.  Z perspektywy kontroli dostępu pulpity nawigacyjne są nie różni się od innych zasobów, takich jak maszyny wirtualnej lub konto magazynu.

> [!TIP]
> Poszczególne Kafelki na pulpicie nawigacyjnym wymusić własne wymagania dotyczące kontroli dostępu na podstawie zasobów, które wyświetlają.  W związku z tym można zaprojektować pulpit nawigacyjny, który jest współużytkowany szeroko przy zapewnieniu ochrony danych na kafelkach poszczególnych.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Opis kontroli dostępu dla pulpitów nawigacyjnych
Z opartej na rolach kontroli dostępu (RBAC), można przypisać użytkowników do ról na trzy różne poziomy zakresu:

* subskrypcja
* grupa zasobów
* zasób

Uprawnienia, które można przypisać są dziedziczone z subskrypcji do zasobu. Opublikowane pulpit nawigacyjny jest zasobem. W związku z tym mogą już mieć użytkowników przypisanych do ról dla subskrypcji, które również działać opublikowanych pulpitu nawigacyjnego. 

Oto przykład.  Załóżmy, że masz subskrypcję platformy Azure i różnych członkowie zespołu są przypisane role **właściciela**, **współautora**, lub **czytnika** dla subskrypcji. Użytkownicy, którzy są właściciele i współautorzy mogą się na liście, wyświetlanie, tworzenie, modyfikowanie lub usuwanie pulpitów nawigacyjnych w ramach subskrypcji.  Użytkownicy, którzy są czytników są w stanie listy i widok pulpity nawigacyjne, ale nie można je modyfikować lub usuwać.  Użytkownikom z dostępem do czytnika są w stanie wprowadzać zmian lokalnych do opublikowanych pulpitu nawigacyjnego (takich jak podczas rozwiązywania problemu), ale nie będą mogły publikować tych zmian na serwerze.  Mają one opcję, aby utworzyć kopię prywatnej pulpitu nawigacyjnego dla siebie

Jednak można również przypisaniem uprawnień do grupy zasobów, która zawiera kilka pulpity nawigacyjne lub pojedynczego pulpitu nawigacyjnego. Na przykład może zdecydować, że grupy użytkowników powinny mieć ograniczone uprawnienia w subskrypcji, ale szerszy dostęp do określonego pulpitu nawigacyjnego. Tych użytkowników, możesz przypisać rolę dla tego pulpitu nawigacyjnego. 

## <a name="publish-dashboard"></a>Publikowanie pulpitu nawigacyjnego
Załóżmy, że zakończono konfigurowanie pulpitu nawigacyjnego, który chcesz udostępnić grupy użytkowników w ramach subskrypcji. Poniższe kroki przedstawiać dostosowane grupę o nazwie menedżerów magazynu, ale niezależnie od chcesz można nazwę grupy. Aby uzyskać informacje o tworzeniu grupy usługi Active Directory i dodawanie użytkowników do tej grupy, zobacz [Zarządzanie grupami w usłudze Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

1. Na pulpicie nawigacyjnym, wybierz **udziału**.
   
     ![Wybierz udział](./media/azure-portal-dashboard-share-access/select-share.png)
2. Przed udzieleniem im dostępu, należy opublikować pulpitu nawigacyjnego. Domyślnie pulpitu nawigacyjnego zostaną opublikowane na grupę zasobów o nazwie **pulpity nawigacyjne**. Wybierz **publikowania**.
   
     ![publish](./media/azure-portal-dashboard-share-access/publish.png)

Pulpit nawigacyjny zostały opublikowane. Jeśli uprawnienia dziedziczone z subskrypcji są odpowiednie, żadne czynności nie jest konieczne. Inni użytkownicy w organizacji będzie dostępu i modyfikowania pulpitu nawigacyjnego na podstawie ich ról poziomu subskrypcji. Jednak w tym samouczku umożliwia przypisywanie grupy użytkowników do roli dla tego pulpitu nawigacyjnego.

## <a name="assign-access-to-a-dashboard"></a>Przypisywanie dostępu do pulpitu nawigacyjnego
1. Po opublikowaniu pulpitu nawigacyjnego, wybierz **Zarządzanie użytkownikami**.
   
     ![zarządzanie użytkownikami](./media/azure-portal-dashboard-share-access/manage-users.png)
2. Zostanie wyświetlona lista istniejących użytkowników, które już przypisano rolę do tego pulpitu nawigacyjnego. Listę istniejących użytkowników mogą być inne niż obraz poniżej. Prawdopodobnie przypisania są dziedziczone z subskrypcji. Aby dodać nowego użytkownika lub grupę, wybierz **Dodaj**.
   
     ![Dodaj użytkownika](./media/azure-portal-dashboard-share-access/existing-users.png)
3. Wybierz rolę, którą reprezentuje uprawnienia, które chcesz udzielić. Na przykład wybierz **współautora**.
   
     ![Wybierz rolę](./media/azure-portal-dashboard-share-access/select-role.png)
4. Wybierz użytkownika lub grupę, którą chcesz przypisać do roli. Jeśli nie ma użytkownika lub grupę, których szukasz na liście, użyj pola wyszukiwania. Z listy dostępnych grup będzie zależał od grupy, które zostały utworzone w usłudze Active Directory.
   
     ![Wybierz użytkownika](./media/azure-portal-dashboard-share-access/select-user.png) 
5. Po zakończeniu dodawania użytkowników lub grup, wybierz **OK**. 
6. Nowe przypisanie jest dodawany do listy użytkowników. Zwróć uwagę, że jego **dostępu** jest wymienione jako **przypisane** zamiast **dziedziczonych**.
   
     ![przypisane role](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać listę ról, zobacz [RBAC: role wbudowane](../role-based-access-control/built-in-roles.md).
* Aby uzyskać informacje dotyczące zarządzania zasobami, zobacz [zasobów Azure zarządzania za pośrednictwem portalu](resource-group-portal.md).

