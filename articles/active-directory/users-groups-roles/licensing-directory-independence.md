---
title: Charakterystyka interakcji z wieloma dzierżawcami — usługa Azure AD | Dokumenty firmy Microsoft
description: Zarządzanie dzierżawami dzierżawy usługi Azure Active przez zrozumienie dzierżaw jako w pełni niezależnych zasobów
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562262"
---
# <a name="understand-how-multiple-azure-active-directory-tenants-interact"></a>Dowiedz się, jak wielu dzierżaw usługi Azure Active Directory wchodzi w interakcje

W usłudze Azure Active Directory (Azure AD) każda dzierżawa jest w pełni niezależnym zasobem: elementem równorzędnym, który jest logicznie niezależny od innych dzierżaw, którymi zarządzasz. Nie ma relacji nadrzędny podrzędny między dzierżawcami. Ta niezależność między najemcami obejmuje niezależność zasobów, niezależność administracyjną i niezależność synchronizacji.

## <a name="resource-independence"></a>Niezależność zasobów
* Jeśli utworzysz lub usuniesz zasób w jednej dzierżawie, nie ma on wpływu na żaden zasób w innej dzierżawie, z częściowym wyjątkiem użytkowników zewnętrznych. 
* Jeśli używasz jednej z nazw domen z jedną dzierżawą, nie można jej używać z żadną inną dzierżawą.

## <a name="administrative-independence"></a>Niezależność administracyjna
Jeśli użytkownik nieadawidacyjny dzierżawy "Contoso" tworzy dzierżawę testową "Test", a następnie:

* Domyślnie użytkownik, który tworzy dzierżawę jest dodawany jako użytkownik zewnętrzny w tej nowej dzierżawy i przypisany rolę administratora globalnego w tej dzierżawie.
* Administratorzy dzierżawy "Contoso" nie mają żadnych bezpośrednich uprawnień administracyjnych do dzierżawy "Test", chyba że administrator "Test" specjalnie przyznaje im te uprawnienia. Jednak administratorzy "Contoso" mogą kontrolować dostęp do dzierżawy "Test", jeśli kontrolują konto użytkownika, który utworzył "Test".
* Jeśli dodasz/usuniesz rolę administratora dla użytkownika w jednej dzierżawie, zmiana nie wpłynie na role administratora, które użytkownik ma w innej dzierżawie.

## <a name="synchronization-independence"></a>Niezależność synchronizacji
Każdej dzierżawy usługi Azure AD można skonfigurować niezależnie, aby uzyskać dane zsynchronizowane z jednego wystąpienia:

* Narzędzie Azure AD Connect do synchronizowania danych z pojedynczym lasem usługi AD.
* Usługa Azure Active dzierżawy łącznika dla menedżera tożsamości forefront, aby zsynchronizować dane z co najmniej jednego lasu lokalnego i/lub nie-Azure AD źródeł danych.

## <a name="add-an-azure-ad-tenant"></a>Dodawanie dzierżawy usługi Azure AD
Aby dodać dzierżawę usługi Azure AD w portalu Azure, zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy za pomocą konta, które jest globalnym administratorem usługi Azure AD, a po lewej stronie wybierz pozycję **Nowy**.

> [!NOTE]
> W przeciwieństwie do innych zasobów platformy Azure dzierżawy nie są podrzędnymi zasobami subskrypcji platformy Azure. Jeśli subskrypcja platformy Azure została anulowana lub wygasła, nadal można uzyskać dostęp do danych dzierżawy przy użyciu programu Azure PowerShell, interfejsu API programu Microsoft Graph lub centrum administracyjnego usługi Microsoft 365. Można również [skojarzyć inną subskrypcję z dzierżawą](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Następne kroki
Aby uzyskać ogólne omówienie problemów z licencjonowaniem usługi Azure AD i najlepszych rozwiązań, zobacz [Co to jest licencjonowanie dzierżawy usługi Azure Active?](../fundamentals/active-directory-licensing-whatis-azure-portal.md).
