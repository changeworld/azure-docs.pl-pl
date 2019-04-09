---
title: Zarządzanie uprawnieniami do zasobów dla każdego użytkownika w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Jako administrator usługi lub dzierżawca Dowiedz się, jak zarządzać uprawnień RBAC.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: 58c16b8a102ea27499fc464c209d4ca1c0d4db33
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59264713"
---
# <a name="manage-access-to-resources-with-azure-stack-role-based-access-control"></a>Zarządzanie dostępem do zasobów przy użyciu kontroli dostępu Azure Stack Role-Based

*Dotyczy Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Usługa Azure Stack obsługuje kontroli dostępu opartej na rolach (RBAC), w taki sam [model zabezpieczeń do zarządzania dostępem do](https://docs.microsoft.com/azure/role-based-access-control/overview) używającej Microsoft Azure. RBAC można użyć do zarządzania użytkownika, grupy lub aplikacji dostęp do subskrypcji, zasobów i usług.

## <a name="basics-of-access-management"></a>Podstawowe informacje dotyczące zarządzania dostępem

Kontrola dostępu oparta na rolach zapewnia bardziej precyzyjnej kontroli dostępu, można użyć, aby zabezpieczyć środowisku. Użytkownikom dokładnie uprawnienia, które są im potrzebne, Przypisz rolę RBAC, w określonym zakresie. Zakres przypisania roli może być subskrypcją, grupą zasobów lub pojedynczy zasób. Odczyt [opartej na rolach kontrola dostępu w witrynie Azure portal](https://docs.microsoft.com/azure/role-based-access-control/overview) artykułu, aby uzyskać bardziej szczegółowe informacje dotyczące zarządzania dostępem.

### <a name="built-in-roles"></a>Wbudowane role

Usługa Azure Stack ma trzy podstawowe role, które można zastosować do wszystkich typów zasobów:

* **Właściciel** mogą zarządzać wszystkim łącznie z dostępem do zasobów.
* **Współautor** może zarządzać wszystkim oprócz dostępu do zasobów.
* **Czytnik** mogą przeglądać wszystko, ale nie może wprowadzać żadnych zmian.

### <a name="resource-hierarchy-and-inheritance"></a>Hierarchia zasobów i dziedziczenie

Usługa Azure Stack ma następującej hierarchii zasobów:

* Każda subskrypcja należy do jednego katalogu.
* Każdej z grup zasobów należy do jednej subskrypcji.
* Każdy zasób należy do jednej grupy zasobów.

Prawa dostępu, który można przyznać w zakresie nadrzędnej jest dziedziczona na zakresy podrzędne. Na przykład:

* Możesz przypisać **czytnika** roli do grupy usługi Azure AD w zakresie subskrypcji. Członkowie tej grupy mogą wyświetlić każdą grupę zasobów i zasobów w subskrypcji.
* Możesz przypisać **Współautor** roli do aplikacji w zakresie grupy zasobów. Aplikację można zarządzać zasoby wszystkich typów w danej grupie zasobów, ale nie innych grupach zasobów w subskrypcji.

### <a name="assigning-roles"></a>Przypisywanie ról

Można przypisać więcej niż jednej roli do użytkownika, a każda rola może być skojarzony z innym zakresie. Na przykład:

* Można przypisać roli Czytelnik TestUser-A z subskrypcją 1.
* Przypisz do TestVM 1 rolę właściciela TestUser-A.

Azure [przypisań ról](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) artykuł zawiera szczegółowe informacje dotyczące wyświetlania, przypisywania i usuwania ról.

## <a name="set-access-permissions-for-a-user"></a>Ustaw uprawnienia dostępu dla użytkownika

Poniżej opisano sposób konfigurowania uprawnień dla użytkownika.

1. Zaloguj się przy użyciu konta, które ma uprawnienia właściciela zasobu, z którym chcesz zarządzać.
2. W lewym okienku nawigacji wybierz pozycję **Grupy zasobów**.
3. Wybierz nazwę, którą chcesz ustawić uprawnienia dla grupy zasobów.
4. W okienku nawigacji w grupy zasobów wybierz **kontrola dostępu (IAM)**. **Przypisań ról** widok zawiera elementy, które mają dostęp do grupy zasobów. Można filtrować i grupować wyniki.
5. Na **kontroli dostępu** menu paska, wybierz polecenie **Dodaj**.
6. Na **Dodaj uprawnienia** okienka:

   * Wybierz rolę, którą chcesz przypisać z **roli** listy rozwijanej.
   * Wybierz zasób, którego chcesz przypisać z **Przypisz dostęp do** listy rozwijanej.
   * Wybierz użytkownika, grupę lub aplikację w katalogu, którym chcesz udzielić dostępu. Możesz przeszukiwać katalog przy użyciu nazw wyświetlanych, adresów e-mail i identyfikatorów obiektów.

7. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Kolejne kroki

[Tworzenie jednostek usługi](azure-stack-create-service-principals.md)
