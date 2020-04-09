---
title: Charakterystyka interakcji z wieloma dzierżawcami — usługa Azure AD | Dokumenty firmy Microsoft
description: Opis dzierżaw usługi Azure Active Directory jako w pełni niezależnych organizacji
services: active-tenant
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 04/07/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: 175d9ce7db1657e0e654f46adaf8a8d8ef28c25e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878123"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>Dowiedz się, jak wiele organizacji usługi Azure Active Directory współdziała

W usłudze Azure Active Directory (Azure AD) każda dzierżawa jest w pełni niezależną organizacją: element równorzędny, który jest logicznie niezależny od innych organizacji usługi Azure AD, którymi zarządzasz. Ta niezależność między organizacjami obejmuje niezależność zasobów, niezależność administracyjną i niezależność synchronizacji. Nie ma relacji nadrzędny podrzędny między organizacjami.

## <a name="resource-independence"></a>Niezależność zasobów

* Jeśli utworzysz lub usuniesz zasób usługi Azure AD w jednej organizacji, nie ma on wpływu na żaden zasób w innej organizacji, z częściowym wyjątkiem użytkowników zewnętrznych.
* Jeśli zarejestrujesz jedną z nazw domen w jednej organizacji, nie będzie mogła być używana przez żadną inną organizację.

## <a name="administrative-independence"></a>Niezależność administracyjna

Jeśli użytkownik nieadawidacyjny organizacji "Contoso" utworzy organizację testową "Test", wówczas:

* Domyślnie użytkownik tworzący organizację jest dodawany jako użytkownik zewnętrzny w tej nowej organizacji i przypisywany roli administratora globalnego w tej organizacji.
* Administratorzy organizacji "Contoso" nie mają bezpośrednich uprawnień administracyjnych do organizacji "Test", chyba że administrator "Testuj" wyraźnie przyznaje im te uprawnienia. Jednak administratorzy "Contoso" mogą kontrolować dostęp do organizacji "Test", jeśli kontrolują konto użytkownika, które utworzyło "Test".
* Jeśli dodasz lub usuniesz rolę usługi Azure AD dla użytkownika w jednej organizacji, zmiana nie wpłynie na role, które użytkownik jest przypisany w innej organizacji usługi Azure AD.

## <a name="synchronization-independence"></a>Niezależność synchronizacji

Każdą organizację usługi Azure AD można skonfigurować niezależnie, aby uzyskać dane zsynchronizowane z jednego wystąpienia:

* Narzędzie Azure AD Connect do synchronizowania danych z pojedynczym lasem usługi AD.
* Usługa Azure Active Directory Connector for Forefront Identity Manager, aby zsynchronizować dane z co najmniej jednym lasem lokalnym i/lub źródłami danych usługi AD innej niż azure.

## <a name="add-an-azure-ad-organization"></a>Dodawanie organizacji usługi Azure AD

Aby dodać organizację usługi Azure AD w witrynie Azure portal, zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy za pomocą konta, które jest globalnym administratorem usługi Azure AD, i wybierz pozycję **Nowy**.

> [!NOTE]
> W przeciwieństwie do innych zasobów platformy Azure organizacje usługi Azure AD nie są zasobami podrzędnymi subskrypcji platformy Azure. Jeśli subskrypcja platformy Azure została anulowana lub wygasła, nadal można uzyskać dostęp do danych organizacji usługi Azure AD przy użyciu programu Azure PowerShell, interfejsu API programu Microsoft Graph lub centrum administracyjnego usługi Microsoft 365. Można również [skojarzyć inną subskrypcję z organizacją](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z zagadnieniami i najlepszymi rozwiązaniami w zakresie licencjonowania usługi Azure AD, zobacz [Co to jest licencjonowanie usługi Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md).
