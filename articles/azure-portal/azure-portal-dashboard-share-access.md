---
title: Udostępnianie pulpitów nawigacyjnych Azure Portal przy użyciu RBAC | Microsoft Docs
description: W tym artykule opisano sposób udostępniania pulpitu nawigacyjnego w Azure Portal przy użyciu Access Control opartych na rolach.
services: azure-portal
documentationcenter: ''
author: mgblythe
manager: mtillman
editor: tysonn
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/01/2016
ms.author: mblythe
ms.openlocfilehash: ce94a35ebcbf5d8cf3d176f05ac75ea5da5d8d99
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310751"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Udostępnianie pulpitów nawigacyjnych platformy Azure przy użyciu kontroli dostępu opartej na rolach

Po skonfigurowaniu pulpitu nawigacyjnego można go opublikować i udostępnić innym użytkownikom w organizacji. Możesz zezwolić innym osobom na wyświetlanie pulpitu nawigacyjnego za pomocą [Access Control opartych na rolach](../role-based-access-control/role-assignments-portal.md)platformy Azure. Przypisujesz użytkownika lub grupę użytkowników do roli, a ta rola określa, czy użytkownicy mogą wyświetlać lub modyfikować opublikowane pulpity nawigacyjne. 

Wszystkie opublikowane pulpity nawigacyjne są implementowane jako zasoby platformy Azure, co oznacza, że istnieją jako elementy do zarządzania w ramach subskrypcji i znajdują się w grupie zasobów.  Z perspektywy kontroli dostępu pulpity nawigacyjne nie różnią się od innych zasobów, takich jak maszyna wirtualna lub konto magazynu.

> [!TIP]
> Poszczególne kafelki na pulpicie nawigacyjnym wymuszają własne wymagania dotyczące kontroli dostępu w zależności od zasobów, które są wyświetlane.  W związku z tym można zaprojektować pulpit nawigacyjny, który jest udostępniany szeroko, przy jednoczesnym ochronie danych na poszczególnych kafelkach.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Informacje o kontroli dostępu dla pulpitów nawigacyjnych
Za pomocą Access Control opartej na rolach (RBAC) można przypisywać użytkowników do ról na trzech różnych poziomach zakresu:

* subskrypcja
* grupa zasobów
* resource

Przypisane uprawnienia są dziedziczone z subskrypcji do zasobu. Opublikowany pulpit nawigacyjny jest zasobem. W związku z tym użytkownik może mieć już przypisanych do ról role subskrypcji, które również działają dla opublikowanego pulpitu nawigacyjnego. 

Oto przykład.  Załóżmy, że masz subskrypcję platformy Azure i różnym członkom zespołu przypisano role **właściciela**, **współautora**lub **czytelnika** subskrypcji. Użytkownicy będący właścicielami lub współautorami mogą wyświetlać, przeglądać, tworzyć, modyfikować i usuwać pulpity nawigacyjne w ramach subskrypcji.  Użytkownicy będący czytelnikami mogą wyświetlać listy pulpitów nawigacyjnych, ale nie mogą ich modyfikować ani usuwać.  Użytkownicy z dostępem do czytnika mogą wprowadzać lokalne zmiany do opublikowanego pulpitu nawigacyjnego (na przykład w przypadku rozwiązywania problemu), ale nie mogą publikować tych zmian z powrotem na serwerze.  Będą oni mogli wybrać prywatną kopię pulpitu nawigacyjnego

Można jednak przypisać uprawnienia do grupy zasobów zawierającej kilka pulpitów nawigacyjnych lub do poszczególnych pulpitów nawigacyjnych. Na przykład można zdecydować, że grupa użytkowników powinna mieć ograniczone uprawnienia w ramach subskrypcji, ale większy dostęp do określonego pulpitu nawigacyjnego. Przypisujesz tych użytkowników do roli dla tego pulpitu nawigacyjnego. 

## <a name="publish-dashboard"></a>Publikowanie pulpitu nawigacyjnego
Załóżmy, że zakończysz Konfigurowanie pulpitu nawigacyjnego, który chcesz udostępnić grupie użytkowników w Twojej subskrypcji. W poniższych krokach przedstawiono dostosowaną grupę o nazwie menedżerowie magazynu, ale można nazwać grupę w dowolnej postaci. Aby uzyskać informacje na temat tworzenia grupy Active Directory i dodawania użytkowników do tej grupy, zobacz [Zarządzanie grupami w Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

1. Na pulpicie nawigacyjnym wybierz pozycję **Udostępnij**.
   
     ![Wybierz pozycję Udostępnij](./media/azure-portal-dashboard-share-access/select-share.png)
2. Przed przypisaniem dostępu należy opublikować pulpit nawigacyjny. Domyślnie pulpit nawigacyjny zostanie opublikowany w grupie zasobów o nazwie **pulpity nawigacyjne**. Wybierz pozycję **Publikuj**.
   
     ![publish](./media/azure-portal-dashboard-share-access/publish.png)

Pulpit nawigacyjny jest teraz opublikowany. Jeśli uprawnienia Odziedziczone z subskrypcji są odpowiednie, nie musisz robić nic więcej. Inni użytkownicy w organizacji będą mogli uzyskiwać dostęp i modyfikować pulpit nawigacyjny na podstawie ich roli na poziomie subskrypcji. Jednak w tym samouczku przypiszesz grupie użytkowników do roli dla tego pulpitu nawigacyjnego.

## <a name="assign-access-to-a-dashboard"></a>Przypisywanie dostępu do pulpitu nawigacyjnego
1. Po opublikowaniu pulpitu nawigacyjnego wybierz pozycję **Zarządzaj użytkownikami**.
   
     ![zarządzanie użytkownikami](./media/azure-portal-dashboard-share-access/manage-users.png)
2. Zostanie wyświetlona lista istniejących użytkowników, którym przypisano już rolę dla tego pulpitu nawigacyjnego. Lista istniejących użytkowników różni się od poniższej ilustracji. Najprawdopodobniej te przypisania są dziedziczone z subskrypcji. Aby dodać nowego użytkownika lub grupę, wybierz pozycję **Dodaj**.
   
     ![Dodaj użytkownika](./media/azure-portal-dashboard-share-access/existing-users.png)
3. Wybierz rolę reprezentującą uprawnienia, które chcesz udzielić. Na potrzeby tego przykładu wybierz opcję **współautor**.
   
     ![Wybierz rolę](./media/azure-portal-dashboard-share-access/select-role.png)
4. Wybierz użytkownika lub grupę, która ma zostać przypisana do roli. Jeśli nie widzisz użytkownika lub grupy, której szukasz na liście, użyj pola wyszukiwania. Lista dostępnych grup będzie zależeć od grup utworzonych w Active Directory.
   
     ![Wybieranie użytkownika](./media/azure-portal-dashboard-share-access/select-user.png) 
5. Po zakończeniu dodawania użytkowników lub grup kliknij **przycisk OK**. 
6. Nowe przypisanie zostanie dodane do listy użytkowników. Należy zauważyć, że jego **dostęp** jest wymieniony jako **przypisany** , a nie **Dziedziczony**.
   
     ![przypisane role](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać listę ról, zobacz [RBAC: role wbudowane](../role-based-access-control/built-in-roles.md).
* Aby dowiedzieć się więcej o zarządzaniu zasobami, zobacz [Zarządzanie zasobami platformy Azure za pomocą portalu](resource-group-portal.md).

