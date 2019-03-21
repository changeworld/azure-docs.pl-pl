---
title: Zarządzanie uprawnieniami do zasobów dla każdego użytkownika w usłudze Azure Stack (administrator usługi i dzierżawy) | Dokumentacja firmy Microsoft
description: Jako administrator usługi lub dzierżawca Dowiedz się, jak zarządzać uprawnień RBAC.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: acec53a99fd6d809dc01ce12b02987d66579b0c5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "58118293"
---
# <a name="manage-role-based-access-control"></a>Zarządzanie kontrolą dostępu opartej na rolach

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Użytkownik w usłudze Azure Stack można czytnika, właściciela lub współautora dla każdego wystąpienia subskrypcji, grupy zasobów lub usługi. Na przykład użytkownik A może czytnika uprawnień do jednej subskrypcji, ale mają uprawnienia właściciela do siedmiu maszyny wirtualnej.

 - Czytnik: Użytkownik może przeglądać wszystko, ale nie może wprowadzać żadnych zmian.
 - Współautor: Użytkownik może zarządzać wszystkim oprócz dostępu do zasobów.
 - Właściciel: Użytkownik może zarządzać wszystkim łącznie z dostępem do zasobów.

## <a name="set-access-permissions-for-a-user"></a>Ustaw uprawnienia dostępu dla użytkownika

1. Zaloguj się przy użyciu konta, które ma uprawnienia właściciela zasobu, z którym chcesz zarządzać.
2. W bloku zasobu kliknij **dostępu** ikonę ![](media/azure-stack-manage-permissions/image1.png).
3. W **użytkowników** bloku kliknij **role**.
4. W **role** bloku kliknij **Dodaj** Aby dodać uprawnienia dla użytkownika.

## <a name="set-access-permissions-for-a-universal-group"></a>Ustaw uprawnienia dostępu dla grup uniwersalnych 

> [!Note]
> Dotyczy tylko usługi Active Directory Sfederowana Services (AD FS).

1. Zaloguj się przy użyciu konta, które ma uprawnienia właściciela zasobu, z którym chcesz zarządzać.
2. W bloku zasobu kliknij **dostępu** ikonę ![](media/azure-stack-manage-permissions/image1.png).
3. W **użytkowników** bloku kliknij **role**.
4. W **role** bloku kliknij **Dodaj** Aby dodać uprawnienia dla uniwersalnej grupy grupy usługi Active Directory.

## <a name="next-steps"></a>Kolejne kroki
[Dodawanie dzierżawy usługi Azure Stack](azure-stack-add-new-user-aad.md)

