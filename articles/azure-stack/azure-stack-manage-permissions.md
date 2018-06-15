---
title: Zarządzanie uprawnieniami do zasobów dla każdego użytkownika w stosie Azure (administrator usługi i dzierżawcy) | Dokumentacja firmy Microsoft
description: Jako administrator usługi lub dzierżawy informacje o sposobie zarządzania uprawnieniami RBAC.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: fenila
editor: ''
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: mabrigg
ms.reviewer: thomas.roettinger
ms.openlocfilehash: 0e50ea44ebb0b0a7285dab04666dd55cad480c6a
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29385640"
---
# <a name="manage-role-based-access-control"></a>Zarządzanie kontrolą dostępu opartą na rolach

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Użytkownik w stosie Azure może być czytnika, właściciela lub współautora dla każdego wystąpienia subskrypcji, grupy zasobów lub usługi. Na przykład użytkownik A może czytnika uprawnień do jednej subskrypcji, ale mieć uprawnienia właściciela do siedmiu maszyny wirtualnej.

 - Czytnik: Użytkownika mogą przeglądać wszystko, ale nie można wprowadzać żadnych zmian.
 - Współautor: Użytkownik może zarządzać wszystkim poza dostępem do zasobów.
 - Właściciel: Użytkownik może zarządzać wszystkim łącznie z dostępem do zasobów.

## <a name="set-access-permissions-for-a-user"></a>Ustaw uprawnienia dostępu dla użytkownika

1. Zaloguj się przy użyciu konta mającego uprawnienia właściciela zasobu, z którym chcesz zarządzać.
2. W bloku zasobu, kliknij **dostępu** ikona ![](media/azure-stack-manage-permissions/image1.png).
3. W **użytkowników** bloku, kliknij przycisk **ról**.
4. W **ról** bloku, kliknij przycisk **Dodaj** się dodanie zezwoleń użytkownika.

## <a name="set-access-permissions-for-a-universal-group"></a>Ustaw uprawnienia dostępu dla grup uniwersalnych 

> [!Note]  
Zastosowanie tylko do usługi Active Directory federacyjnych Services (AD FS).

1. Zaloguj się przy użyciu konta mającego uprawnienia właściciela zasobu, z którym chcesz zarządzać.
2. W bloku zasobu, kliknij **dostępu** ikona ![](media/azure-stack-manage-permissions/image1.png).
3. W **użytkowników** bloku, kliknij przycisk **ról**.
4. W **ról** bloku, kliknij przycisk **Dodaj** Dodawanie uprawnień do uniwersalnej grupy grupy usługi Active Directory.

## <a name="next-steps"></a>Kolejne kroki
[Dodawanie dzierżawy usługi Azure Stack](azure-stack-add-new-user-aad.md)

