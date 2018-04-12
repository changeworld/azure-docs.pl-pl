---
title: Zarządzaj dostępem i uprawnieniami RBAC - Azure RBAC | Dokumentacja firmy Microsoft
description: Rozpocznij zarządzanie dostępem przy użyciu kontroli dostępu opartej na rolach na platformie Azure w portalu Azure. Przypisz uprawnienia w katalogu za pomocą przypisań ról.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8f8aadeb-45c9-4d0e-af87-f1f79373e039
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/02/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.openlocfilehash: d6911b8deb92fe6781bf79b4d5794101b266c63e
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2018
---
# <a name="get-started-with-role-based-access-control-in-the-azure-portal"></a>Rozpoczynanie pracy z opartej na rolach kontroli dostępu w portalu Azure
Nastawionych zabezpieczeń należy skoncentrować się na zapewniając pracownikom dokładne uprawnienia potrzebne. Za dużo uprawnienia mogą uwidaczniać konta na ataki. Za mało uprawnienia oznacza, że pracownicy nie można pobrać ich pracować wydajnie. Azure opartej na rolach kontroli dostępu (RBAC) pomaga rozwiązać ten problem, oferując precyzyjne zarządzanie dostępem dla platformy Azure.

Przy użyciu funkcji RBAC, można rozdzielenie obowiązków w obrębie organizacji i udzielić tylko takiego dostępu dla użytkowników, które są niezbędne do wykonywania swoich zadań. Zamiast nadanie każdy nieograniczonych uprawnień w Twojej subskrypcji platformy Azure lub zasobów, można zezwolić tylko pewne akcje. Na przykład użycie funkcji RBAC, aby umożliwić jednego pracownika zarządzać maszyn wirtualnych w ramach subskrypcji, gdy inny można zarządzać baz danych w ramach tej samej subskrypcji.

## <a name="basics-of-access-management-in-azure"></a>Podstawy zarządzania dostępem w systemie Azure
Każda subskrypcja platformy Azure jest skojarzony z jednego katalogu usługi Azure Active Directory (AD). Użytkownicy, grupy i aplikacje z katalogu mogą zarządzać zasobami w subskrypcji platformy Azure. Przypisz te prawa dostępu przy użyciu portalu Azure, narzędzi wiersza polecenia platformy Azure i interfejsów API zarządzania platformy Azure.

Udziel dostępu, przypisując odpowiednie role RBAC do użytkowników, grup i aplikacji w określonego zakresu. Zakres przypisania roli może być pojedynczego zasobu, grupy zasobów lub subskrypcji. Rola przypisana w zakresie nadrzędnej również udziela dostępu do podrzędnych w nim zawarte. Na przykład użytkownik z dostępem do grupy zasobów można zarządzać wszystkie zasoby, które zawiera, takie jak witryny sieci Web, maszyn wirtualnych i podsieci.

![Relacja między elementami usługi Azure Active Directory — diagram](./media/role-based-access-control-what-is/rbac_aad.png)

Przypisania roli RBAC mówią, jakie zasoby użytkownika, grupy lub aplikacji można zarządzać w ramach tego zakresu.

## <a name="built-in-roles"></a>Wbudowane role
Azure RBAC ma trzy podstawowe role, które są stosowane do wszystkich typów zasobów:

* **Właściciel** ma pełny dostęp do wszystkich zasobów łącznie z prawem delegować dostęp do innych użytkowników.
* **Współautor** można tworzyć i zarządzania wszystkimi typami zasobów platformy Azure, ale nie może udzielić dostępu do innych użytkowników.
* **Czytnik** można wyświetlić istniejących zasobów platformy Azure.

Pozostałe role RBAC na platformie Azure umożliwiają zarządzanie określonych zasobów platformy Azure. Na przykład Rola współautora maszyny wirtualnej zezwala użytkownikowi na tworzenie i zarządzanie maszynami wirtualnymi. Nie daje im dostępu do sieci wirtualnej lub podsieci, która łączy się z maszyny wirtualnej. 

[Wbudowane role RBAC](role-based-access-built-in-roles.md) Wyświetla listę ról, które są dostępne w systemie Azure. Określa operacje i zakresem, który każdego wbudowana rola przyznaje użytkownikom. Jeśli szukasz definiować własne role, aby uzyskać większą kontrolę, zobacz sposób tworzenia [niestandardowych ról w Azure RBAC](role-based-access-control-custom-roles.md).

## <a name="resource-hierarchy-and-access-inheritance"></a>Dziedziczenie hierarchii i dostęp do zasobów
* Każdy **subskrypcji** Azure należy tylko jeden katalog. (Ale każdego katalogu mogą mieć więcej niż jedną subskrypcję).
* Każdy **grupy zasobów** należy do tylko jednej subskrypcji.
* Każdy **zasobów** należy do tylko jednej grupy zasobów.

Przyznaj na nadrzędne zakresy dostępu jest dziedziczone na zakresy podrzędne. Na przykład:

* Należy przypisać rolę czytelnika do grupy usługi Azure AD w zakresie subskrypcji. Członkowie tej grupy można wyświetlić co zasobu i grupy zasobów w subskrypcji.
* Przypisanie roli współautora do aplikacji w zakresie grupy zasobów. Może on zarządzać zasoby wszystkich typów w tej grupie zasobów, ale nie innych grup zasobów w subskrypcji.

## <a name="azure-rbac-vs-classic-subscription-administrators"></a>Azure RBAC, a administratorzy subskrypcji klasycznego
[Klasyczni Administratorzy i współadministratorzy](../billing/billing-add-change-azure-subscription-administrator.md) mają pełny dostęp do subskrypcji platformy Azure. Mogą zarządzać zasobów przy użyciu [portalu Azure](https://portal.azure.com), interfejsów API usługi Azure Resource Manager i klasycznym modelu wdrażania interfejsów API. W modelu RBAC klasycznego Administratorzy mają przypisaną rolę właściciela w zakresie subskrypcji.

Portalu Azure i nowych interfejsów API usługi Azure Resource Manager obsługuje Azure RBAC. Użytkownicy i aplikacje, które są przypisane role RBAC nie można użyć Azure Klasyczny model wdrażania przy użyciu interfejsów API.

## <a name="authorization-for-management-vs-data-operations"></a>Autoryzacji do zarządzania, a operacje na danych
Azure RBAC obsługuje tylko operacje zarządzania zasobami Azure w portalu Azure i interfejsów API usługi Azure Resource Manager. Nie można go autoryzacji wszystkie operacje poziomu danych zasobów platformy Azure. Na przykład ktoś może zarządzać kontami magazynu autoryzacji, ale nie do obiektów blob lub tabel w ramach konta magazynu. Podobnie bazy danych SQL mogą być zarządzane, ale nie tabele w niej.

## <a name="next-steps"></a>Następne kroki
* Rozpoczynanie pracy z [opartej na rolach kontroli dostępu w portalu Azure](role-based-access-control-configure.md).
* Zobacz [Wbudowane role RBAC](role-based-access-built-in-roles.md)
* Definiowanie własnych [niestandardowych ról dla kontroli dostępu opartej na rolach na platformie Azure](role-based-access-control-custom-roles.md)
