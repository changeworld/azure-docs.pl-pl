---
title: Właściwości wielu dzierżawców interakcji — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Zarządzanie dzierżawcy dzierżawy usługi Azure Active dzięki zrozumieniu dzierżawcy jako całkowicie niezależne zasoby
services: active-tenant
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 01/31/2019
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45f48b6d8ef29d14606f18d4ccee77bd742a670a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60470076"
---
# <a name="understand-how-multiple-azure-active-directory-tenants-interact"></a>Zrozumieć interakcję wielu dzierżaw usługi Azure Active Directory

W usłudze Azure Active Directory (Azure AD), każda dzierżawa jest w pełni niezależnym zasobem: równorzędnej, który jest logicznie niezależny od pozostałych dzierżawców, którymi zarządzasz. Nie ma żadnych relacji nadrzędny podrzędny między dzierżawami. Ta niezależność między dzierżawami obejmuje niezależność zasobów, niezależność administracyjną i niezależność synchronizacji.

## <a name="resource-independence"></a>Niezależność zasobów
* Jeśli utworzysz lub usuniesz zasób w jednej dzierżawy, go nie ma wpływu na żaden zasób w innej dzierżawie z częściowym wyjątkiem dotyczącym zewnętrznych użytkowników. 
* Jeśli używasz nazwy domeny za pomocą jednej dzierżawy nie można używać z innymi dzierżawami.

## <a name="administrative-independence"></a>Niezależność administracyjna
Jeśli użytkownik niebędący administratorem dzierżawy "Contoso" utworzy to dzierżawa testowa "Test", wtedy:

* Domyślnie użytkownik tworzący dzierżawy jest dodawany jako użytkownik zewnętrzny w tej nowej dzierżawy i z przypisaną rolą administratora globalnego w tej dzierżawie.
* Administratorzy dzierżawy "Contoso" ma nie bezpośrednich uprawnień administracyjnych do dzierżawy "Test", chyba że administrator "Test" jawnie nada im tych uprawnień. Jednak Administratorzy "Contoso" mogą kontrolować dostęp do dzierżawy "Test", jeśli ich kontroli konta użytkownika, który utworzono katalog "Test".
* Możesz dodać/usunąć rolę administratora dla użytkownika w jednej dzierżawy, zmiany nie wpływa na ról administratora, które użytkownik ma w innej dzierżawie.

## <a name="synchronization-independence"></a>Niezależność synchronizacji
Każda dzierżawa usługi Azure AD niezależnie w celu synchronizowania danych z jednego wystąpienia można skonfigurować:

* Narzędzie Azure AD Connect do synchronizowania danych z pojedynczym lasem usługi AD.
* Dzierżawca usługi Azure Active łącznik dla programu Forefront Identity Manager, aby synchronizować dane z jednego lub więcej lokalnymi lasami i/lub źródeł danych AD spoza platformy Azure.

## <a name="add-an-azure-ad-tenant"></a>Dodawanie dzierżawy usługi Azure AD
Aby dodać dzierżawę usługi Azure AD w witrynie Azure portal, zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta administratora globalnego usługi Azure AD, a po lewej stronie, wybierz **New**.

> [!NOTE]
> W przeciwieństwie do innych zasobów platformy Azure dzierżawcy nie są zasobami podrzędnymi subskrypcji platformy Azure. Jeśli Twoja subskrypcja platformy Azure jest anulowane lub wygasła, mogą nadal uzyskiwać dostęp do danych dzierżawy przy użyciu programu Azure PowerShell, interfejsu API programu Graph usługi Azure lub w Centrum administracyjnym usługi Microsoft 365. Możesz również [skojarzyć inną subskrypcję z dzierżawą](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Kolejne kroki
Zawiera ogólne omówienie zagadnień licencjonowania usługi Azure AD i najlepsze rozwiązania, można zobaczyć [co to jest dzierżawa usługi Azure Active licencjonowania?](../fundamentals/active-directory-licensing-whatis-azure-portal.md).
