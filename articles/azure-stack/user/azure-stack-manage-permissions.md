---
title: Zarządzanie uprawnieniami do zasobów dla każdego użytkownika w stosie Azure | Dokumentacja firmy Microsoft
description: Jako administrator usługi lub dzierżawy informacje o sposobie zarządzania uprawnieniami RBAC.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 026c686b1d5654aa50dd63b9addd619dd5322da0
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808149"
---
# <a name="manage-access-to-resources-with-azure-stack-role-based-access-control"></a>Zarządzanie dostępem do zasobów przy użyciu kontroli dostępu Azure Stack Role-Based

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Stos Azure obsługuje kontroli dostępu opartej na rolach (RBAC), takie same [model zabezpieczeń dla zarządzania dostępem](https://docs.microsoft.com/azure/role-based-access-control/overview) używającej Microsoft Azure. RBAC można użyć do zarządzania użytkownika, grupy lub aplikacji dostęp do subskrypcji, zasobów i usług.

## <a name="basics-of-access-management"></a>Podstawy zarządzania dostępem

Kontrola dostępu oparta na rolach zapewnia szczegółowej kontroli dostępu, który służy do zabezpieczania środowiska. Użytkownikom należy nadać uprawnienia dokładne potrzebnych im przez przypisanie roli RBAC w określonego zakresu. Zakres przypisania roli może być pojedynczego zasobu, grupy zasobów lub subskrypcji. Odczyt [opartej na rolach kontroli dostępu w portalu Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) artykułu, aby uzyskać bardziej szczegółowe informacje dotyczące zarządzania dostępem.

### <a name="built-in-roles"></a>Wbudowane role

Stos Azure ma trzy podstawowe role, które można zastosować do wszystkich typów zasobów:

* **Właściciel** mogą zarządzać wszystkim łącznie z dostępem do zasobów.
* **Współautor** mogą zarządzać wszystkim poza dostępem do zasobów.
* **Czytnik** mogą przeglądać wszystko, ale nie można wprowadzać żadnych zmian.

### <a name="resource-hierarchy-and-inheritance"></a>Hierarchia zasobów i dziedziczenie

Azure stos nie zawiera następująca hierarchia zasobów:

* Każda subskrypcja należy do jednego katalogu.
* Każdej z grup zasobów należy do jednej subskrypcji.
* Każdy zasób należy do grupy zasobów.

Dostępu, który można przyznać w zakresie nadrzędnej jest dziedziczona w zakresy podrzędne. Na przykład:

* Należy przypisać rolę czytelnika do grupy usługi Azure AD w zakresie subskrypcji. Członkowie tej grupy można wyświetlić co zasobu i grupy zasobów w subskrypcji.
* Przypisanie roli współautora do aplikacji w zakresie grupy zasobów. Aplikację można zarządzać zasoby wszystkich typów w tej grupie zasobów, ale nie innych grup zasobów w subskrypcji.

### <a name="assigning-roles"></a>Przypisywanie ról

Można przypisać więcej niż jedną rolę dla użytkownika, a każda rola może być skojarzony z innym zakresie. Na przykład:

* Należy przypisać rolę czytelnika TestUser-A do subskrypcji-1.
* Możesz przypisać rolę właściciela TestUser-A TestVM-1.

Azure [przypisań ról](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) artykuł zawiera szczegółowe informacje na temat wyświetlania, przypisywania i usuwania ról.

### <a name="resource-hierarchy-and-inheritance"></a>Hierarchia zasobów i dziedziczenie

Azure stos nie zawiera następująca hierarchia zasobów:

* Każda subskrypcja należy do jednego katalogu.
* Każdej z grup zasobów należy do jednej subskrypcji.
* Każdy zasób należy do grupy zasobów.

Dostępu, który można przyznać w zakresie nadrzędnej jest dziedziczona w zakresy podrzędne. Na przykład:

* Należy przypisać **czytnika** roli do grupy usługi Azure AD w zakresie subskrypcji. Członkowie tej grupy można wyświetlić co zasobu i grupy zasobów w subskrypcji.
* Należy przypisać **współautora** roli do aplikacji w zakresie grupy zasobów. Aplikację można zarządzać zasoby wszystkich typów w tej grupie zasobów, ale nie innych grup zasobów w subskrypcji.

### <a name="assigning-roles"></a>Przypisywanie ról

Można przypisać więcej niż jedną rolę dla użytkownika, a każda rola może być skojarzony z innym zakresie. Na przykład:

* Należy przypisać rolę czytelnika TestUser-A do subskrypcji-1.
* Możesz przypisać rolę właściciela TestUser-A TestVM-1.

Azure [przypisań ról](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) artykuł zawiera szczegółowe informacje na temat wyświetlania, przypisywania i usuwania ról.

## <a name="set-access-permissions-for-a-user"></a>Ustaw uprawnienia dostępu dla użytkownika

W poniższych krokach opisano sposób konfigurowania uprawnień dla użytkownika.

1. Zaloguj się przy użyciu konta mającego uprawnienia właściciela zasobu, z którym chcesz zarządzać.
2. W lewym okienku nawigacji wybierz pozycję **Grupy zasobów**.
3. Wybierz nazwę grupy zasobów, który ma uprawnienia.
4. W okienku nawigacji grupy zasobów, wybierz **(IAM) kontroli dostępu**. **Kontrola dostępu** widoku znajduje się lista elementów, które mają dostęp do grupy zasobów. Możesz filtrować te wyniki i użyć paska menu, aby dodać lub usunąć uprawnienia.
5. Na **kontrola dostępu** menu wybierz pozycję pasek **+ Dodaj**.
6. Na **dodać uprawnienia**:

   * Wybierz rolę, którego chcesz przypisać z **roli** listy rozwijanej.
   * Wybierz zasób, którego chcesz przypisać z **przypisany dostęp** listy rozwijanej.
   * Wybierz użytkownika, grupę lub aplikację w katalogu, którym chcesz udzielić dostępu. Możesz przeszukiwać katalog przy użyciu nazw wyświetlanych, adresów e-mail i identyfikatorów obiektów.

7. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Kolejne kroki

[Tworzenie jednostek usługi](azure-stack-create-service-principals.md)