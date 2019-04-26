---
title: Udostępnianie pulpitów nawigacyjnych portalu platformy Azure przy użyciu RBAC | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono, jak udostępnianie pulpitu nawigacyjnego w witrynie Azure portal przy użyciu kontroli dostępu opartej na rolach.
services: azure-portal
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/01/2016
ms.author: tomfitz
ms.openlocfilehash: fbbc8a4f636a95d18baa0dc5de541279ce36789b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60551996"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Udostępnianie pulpitów nawigacyjnych platformy Azure przy użyciu kontroli dostępu opartej na rolach
Po skonfigurowaniu pulpitu nawigacyjnego, można opublikować ją i udostępniać je innym użytkownikom w organizacji. Zezwalaj na inne osoby do wyświetlania pulpitu nawigacyjnego, korzystając z platformy Azure [kontroli dostępu opartej na rolach](../role-based-access-control/role-assignments-portal.md). Przypisywanie użytkownika lub grupę użytkowników do roli, a ta rola definiuje, czy tych użytkowników, można wyświetlać lub modyfikować opublikowany pulpit nawigacyjny. 

Wszystkie opublikowane pulpity nawigacyjne są implementowane jako zasobów platformy Azure, oznacza to, istnieje jako elementy zarządzane w ramach subskrypcji i są zawarte w grupie zasobów.  Z perspektywy kontroli dostępu pulpity nawigacyjne są nie różni się od innych zasobów, takich jak maszyny wirtualnej lub na koncie magazynu.

> [!TIP]
> Poszczególnych kafelków na pulpicie nawigacyjnym wymuszać swoje własne wymagania dotyczące kontroli dostępu na podstawie zasobów, które są wyświetlane.  W związku z tym można zaprojektować pulpit nawigacyjny, który jest udostępniany szeroko przy zapewnieniu ochrony danych dla poszczególnych kafelków.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Opis kontroli dostępu dla pulpitów nawigacyjnych
Za pomocą opartej na rolach kontrola dostępu (RBAC), można przypisać użytkowników do ról w trzech różnych poziomach zakresu:

* subskrypcja
* grupa zasobów
* zasób

Uprawnienia, które można przypisać są dziedziczone z subskrypcji w dół do zasobu. Opublikowany pulpit nawigacyjny jest zasobem. W związku z tym może już być użytkowników przypisanych do ról dla subskrypcji, które działa także w przypadku opublikowany pulpit nawigacyjny. 

Oto przykład.  Załóżmy, że masz subskrypcję platformy Azure i różnymi członkami Twojego zespołu zostały przypisane role **właściciela**, **Współautor**, lub **czytnika** dla subskrypcji. Użytkownicy, którzy są właściciele lub współautorzy mogą się do listy, wyświetlanie, tworzenie, modyfikowanie lub usuwanie pulpitów nawigacyjnych w ramach subskrypcji.  Użytkownicy, którzy są czytelnicy będą mogli listy i widok pulpitów nawigacyjnych, ale nie je modyfikować lub usuwać.  Użytkownicy z dostępem do czytnika będą mogli lokalne zmiany edycyjne opublikowany pulpit nawigacyjny (takich jak podczas rozwiązywania problemu), ale nie będą mogli publikować te zmiany na serwerze.  Mają one możliwość prywatną kopię pulpitu nawigacyjnego dla siebie

Jednak można można także przypisać uprawnienia do grupy zasobów, która zawiera kilka pulpitów nawigacyjnych lub pojedynczego pulpitu nawigacyjnego. Może na przykład można określić, że grupy użytkowników powinny mają ograniczone uprawnienia w subskrypcji, ale lepszy dostęp do określonego pulpitu nawigacyjnego. Przypisujesz użytkowników do roli dla tego pulpitu nawigacyjnego. 

## <a name="publish-dashboard"></a>Publikowanie pulpitu nawigacyjnego
Załóżmy, że zakończono konfigurowanie pulpitu nawigacyjnego, który chcesz udostępnić grupę użytkowników w Twojej subskrypcji. Poniższe kroki przedstawiać dostosowane grupę o nazwie menedżerów magazynu, ale można nazwę grupy niezależnie od rodzaju chcesz. Aby uzyskać informacje o tworzeniu grupy usługi Active Directory i dodawanie użytkowników do tej grupy, zobacz [Zarządzanie grupami w usłudze Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

1. Na pulpicie nawigacyjnym wybierz **udziału**.
   
     ![Wybierz udział](./media/azure-portal-dashboard-share-access/select-share.png)
2. Przed udzieleniem im dostępu, możesz opublikować pulpitu nawigacyjnego. Domyślnie, pulpit nawigacyjny zostanie opublikowana w grupie zasobów o nazwie **pulpity nawigacyjne**. Wybierz pozycję **Publikuj**.
   
     ![publish](./media/azure-portal-dashboard-share-access/publish.png)

Pulpit nawigacyjny jest obecnie publikowany. Jeśli odpowiednie uprawnienia odziedziczone z subskrypcji, nie musisz robić nic więcej. Inni użytkownicy w Twojej organizacji będzie dostępu i modyfikowania pulpitu nawigacyjnego na podstawie ich ról poziomu subskrypcji. Jednak w tym samouczku teraz przypisać grupy użytkowników do roli dla tego pulpitu nawigacyjnego.

## <a name="assign-access-to-a-dashboard"></a>Przypisywanie dostępu do pulpitu nawigacyjnego
1. Po opublikowaniu na pulpicie nawigacyjnym, wybierz **Zarządzanie użytkownikami**.
   
     ![zarządzanie użytkownikami](./media/azure-portal-dashboard-share-access/manage-users.png)
2. Zostanie wyświetlona lista istniejących użytkowników, którzy są już przypisane roli dla tego pulpitu nawigacyjnego. Lista istniejących użytkowników będą inne niż na poniższym obrazie. Najprawdopodobniej przydziały są dziedziczone z subskrypcji. Aby dodać nowego użytkownika lub grupy, wybierz **Dodaj**.
   
     ![Dodaj użytkownika](./media/azure-portal-dashboard-share-access/existing-users.png)
3. Wybierz rolę, którą reprezentuje uprawnienia, które chcesz udzielić. W tym przykładzie wybierz **Współautor**.
   
     ![Wybierz rolę](./media/azure-portal-dashboard-share-access/select-role.png)
4. Wybierz użytkownika lub grupę, którą chcesz przypisać do roli. Jeśli nie widzisz, użytkownika lub grupę, których szukasz na liście, użyj pola wyszukiwania. Lista dostępnych grup będzie zależeć od grupy, które zostały utworzone w usłudze Active Directory.
   
     ![Wybierz użytkownika](./media/azure-portal-dashboard-share-access/select-user.png) 
5. Po zakończeniu dodawania użytkowników lub grup wybierz **OK**. 
6. Nowe przypisanie zostanie dodany do listy użytkowników. Należy zauważyć, że jego **dostępu** jest wymieniony jako **przypisane** zamiast **dziedziczone**.
   
     ![przypisane role](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać listę ról, zobacz [RBAC: Built-in roles](../role-based-access-control/built-in-roles.md) (Kontrola dostępu oparta na rolach: role wbudowane).
* Aby dowiedzieć się o zarządzanie zasobami, zobacz [zarządzanie zasobami platformy Azure za pośrednictwem portalu](resource-group-portal.md).

