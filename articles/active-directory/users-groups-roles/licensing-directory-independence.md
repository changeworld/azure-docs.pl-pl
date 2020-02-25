---
title: Charakterystyki wielu interakcji dzierżawców — Azure AD | Microsoft Docs
description: Zarządzanie dzierżawami usługi Azure Active dzierżawców przez zrozumienie dzierżawców jako w pełni niezależnych zasobów
services: active-tenant
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4eb09ab7fa31af5edf14b113a6a88e08df2d115
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562262"
---
# <a name="understand-how-multiple-azure-active-directory-tenants-interact"></a>Zrozumienie, jak wiele dzierżawców Azure Active Directory

W Azure Active Directory (Azure AD) Każda dzierżawa jest w pełni niezależnym zasobem: element równorzędny logicznie niezależny od innych dzierżawców, którymi zarządzasz. Między dzierżawcami nie ma relacji nadrzędny-podrzędny. Ta niezależność między dzierżawcami obejmuje niezależność zasobów, niezależność administracyjną i niezależność synchronizacji.

## <a name="resource-independence"></a>Niezależność zasobów
* Jeśli utworzysz lub usuniesz zasób w jednej dzierżawie, nie ma to wpływu na żaden zasób w innej dzierżawie, z częściowym wyjątkiem użytkowników zewnętrznych. 
* Jeśli używasz jednej z nazw domen z jedną dzierżawą, nie można jej używać z żadną inną dzierżawą.

## <a name="administrative-independence"></a>Niezależność administracyjna
Jeśli użytkownik niebędący administratorami dzierżawy "contoso" utworzy dzierżawę testową, wówczas:

* Domyślnie użytkownik tworzący dzierżawcę jest dodawany jako użytkownik zewnętrzny w tej nowej dzierżawie i przypisał rolę administratora globalnego w tej dzierżawie.
* Administratorzy dzierżawy "contoso" nie mają bezpośrednich uprawnień administracyjnych do "test" w dzierżawie ", chyba że administrator" test "jawnie przyznał im te uprawnienia. Jednak administratorzy "contoso" mogą kontrolować dostęp do dzierżawy ", jeśli kontrolują konto użytkownika, które utworzyło" test ".
* Jeśli dodasz/usuniesz rolę administratora dla użytkownika w jednej dzierżawie, zmiana nie wpłynie na role administratorów, które użytkownik ma w innej dzierżawie.

## <a name="synchronization-independence"></a>Niezależność synchronizacji
Każdą dzierżawę usługi Azure AD można skonfigurować niezależnie, aby pobierać dane zsynchronizowane z jednego wystąpienia:

* Narzędzie Azure AD Connect do synchronizowania danych z pojedynczym lasem usługi AD.
* Łącznik usługi Azure Active dzierżawców dla programu Forefront Identity Manager, służący do synchronizowania danych z co najmniej jednymi lokalnymi lasami i/lub źródłami danych spoza usługi Azure AD.

## <a name="add-an-azure-ad-tenant"></a>Dodawanie dzierżawy usługi Azure AD
Aby dodać dzierżawę usługi Azure AD do Azure Portal, zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym usługi Azure AD, a następnie po lewej stronie wybierz pozycję **Nowy**.

> [!NOTE]
> W przeciwieństwie do innych zasobów platformy Azure, dzierżawcy nie są zasobami podrzędnymi subskrypcji platformy Azure. Jeśli subskrypcja platformy Azure została anulowana lub wygasła, nadal możesz uzyskać dostęp do danych dzierżawy za pomocą Azure PowerShell, interfejsu API Microsoft Graph lub centrum administracyjnego Microsoft 365. Możesz również [skojarzyć kolejną subskrypcję z dzierżawcą](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Następne kroki
Aby uzyskać szczegółowe omówienie problemów z licencjonowaniem usługi Azure AD i najlepszych rozwiązań, zobacz [co to jest Licencjonowanie usługi Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md).
