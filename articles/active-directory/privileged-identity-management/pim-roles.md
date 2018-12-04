---
title: Role katalogu usługi AD systemu Azure można zarządzać w usłudze PIM | Dokumentacja firmy Microsoft
description: W tym artykule opisano role katalogu usługi Azure AD, którymi można zarządzać w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 07/23/2018
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.openlocfilehash: 531d19925d24930b6a2bd642a8ff0ec55bd6d16f
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52841578"
---
# <a name="azure-ad-directory-roles-you-can-manage-in-pim"></a>Usługa Azure ról katalogu usługi AD, którymi można zarządzać w usłudze PIM
<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

Można przypisać użytkowników w organizacji, aby różne role administracyjne w usłudze Azure AD. Te przypisania roli kontrolować, które zadania, takie jak dodawanie lub usuwanie użytkowników lub zmiana ustawień usługi Użytkownicy są możliwe do wykonania w usłudze Azure AD, Office 365 i innych Microsoft Online Services i połączonych aplikacji.  

Administrator globalny może aktualizować, których użytkownicy **trwale** przypisane do ról w usłudze Azure AD za pośrednictwem portalu, zgodnie z opisem w [przypisywanie ról administratorów w usłudze Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md) lub za pomocą [ Polecenia programu PowerShell](/powershell/module/azuread#directory_roles).

Usługa Azure AD Privileged Identity Management (PIM) zarządza zasadami uprzywilejowanego dostępu dla użytkowników w usłudze Azure AD. PIM przypisuje użytkowników do co najmniej jedną rolę w usłudze Azure AD i można przypisać ktoś ma być trwałe w roli lub uprawnieni do roli. Gdy użytkownik jest trwale przypisana do roli lub aktywuje kwalifikujące się przypisanie roli, a następnie ich zarządzania usługi Azure Active Directory, usługi Office 365 i innych aplikacji za pomocą uprawnień przypisanych do ich ról.

Nie ma żadnej różnicy w programie access poświęcić osobę z trwałe i kwalifikujące się przypisanie roli. Jedyna różnica polega na tym, że niektórzy użytkownicy nie wymagają tego dostępu przez cały czas. Będą uprawnieni do roli i można ją włączyć i wyłączyć zawsze, gdy muszą one.

## <a name="roles-managed-in-pim"></a>Role zarządzane w usłudze PIM
Privileged Identity Management umożliwia przypisywanie użytkowników do typowych ról administratora, w tym:

* **Administrator globalny** (znanego również jako Administrator firmy) ma dostęp do wszystkich funkcji administracyjnych. Może mieć więcej niż jednego administratora globalnego w organizacji. Osoba, która zarejestruje się w celu zakupu usługi Office 365, automatycznie staje się administratorem globalnym.
* **Administrator ról uprzywilejowanych** zarządza usługa Azure AD PIM i aktualizuje przypisania roli dla innych użytkowników.  
* **Administrator rozliczeń** dokonuje zakupów, zarządza subskrypcjami, zarządza biletami pomocy technicznej i monitoruje kondycję usługi.
* **Administrator haseł** Resetuje hasła, zarządza żądaniami obsługi i monitoruje kondycję usługi. Administratorzy haseł są ograniczone do resetowania haseł dla użytkowników.
* **Administrator usługi** zarządza żądaniami obsługi i monitoruje kondycję usługi.
  
  > [!NOTE]
  > Jeśli używasz usługi Office 365, następnie przed przypisaniem roli administratora usługi dla użytkownika, najpierw przypisać uprawnienia administracyjne do usługi, takich jak Exchange Online.
  > 
  > 
* **Administrator użytkowników** Resetuje hasła, monitoruje kondycję usługi oraz zarządza kontami użytkowników, grup użytkowników i żądania obsługi. Administrator użytkowników nie może usunąć administratora globalnego, tworzyć innych ról administratora lub resetowanie haseł, rozliczeń, globalnych i administratorów usług.
* **Administrator programu Exchange** ma dostęp administracyjny do usługi Exchange Online za pośrednictwem Centrum administracyjnego programu Exchange (EAC) i mogą wykonywać niemal dowolne zadanie w usłudze Exchange Online.
* **Administrator usługi SharePoint** ma dostęp administracyjny do usługi SharePoint Online za pośrednictwem Centrum administracyjnego programu SharePoint Online i mogą wykonywać niemal dowolne zadanie w usłudze SharePoint Online. Uprawnieni użytkownicy mogą wystąpić opóźnienia, po aktywowaniu w usłudze PIM przy użyciu tej roli w programie SharePoint.
* **Skype dla administratora firmy** ma dostęp administracyjny do usługi Skype dla firm za pomocą programu Skype dla firm Centrum administracyjnego i może wykonywać niemal wszystkich zadań w programie Skype dla firm Online.

Przeczytaj następujące artykuły, aby uzyskać więcej informacji o [przypisywanie ról administratorów w usłudze Azure AD](../users-groups-roles/directory-assign-admin-roles.md) i [przypisywanie ról administratora w usłudze Office 365](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504).

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


Przy użyciu usługi PIM, możesz [Przypisz te role dla użytkownika](pim-how-to-add-role-to-user.md) tak, aby użytkownik może [aktywować rolę w razie](pim-how-to-activate-role.md).

Jeśli chcesz nadać inny dostęp użytkownika do zarządzania w usłudze PIM sam, ról, które PIM wymaga od użytkownika posiadania są dokładniejszym opisem zawartym w [udzielanie dostępu do usługi PIM](pim-how-to-give-access-to-pim.md).

<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## <a name="roles-not-managed-in-pim"></a>Role, które nie są zarządzane w usłudze PIM
Role w usłudze Exchange Online lub SharePoint Online, z wyjątkiem tych wymienionych powyżej, nie są reprezentowane w usłudze Azure AD i dlatego nie są widoczne w usłudze PIM. Aby uzyskać więcej informacji na temat zmieniania przypisań ról szczegółowych w tych usługach Office 365, zobacz [uprawnień w usłudze Office 365](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d).

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## <a name="user-roles-and-signing-in"></a>Role użytkowników i logowanie
Niektóre usługi firmy Microsoft i aplikacji przypisanie użytkownika do roli może nie być wystarczające, aby umożliwić użytkownikowi uprawnienia administratora.

Dostęp do portalu Azure wymaga użytkownik jest właścicielem subskrypcji platformy Azure, nawet wtedy, gdy użytkownik nie musi zarządzać subskrypcjami platformy Azure.  Na przykład, aby zarządzać ustawieniami konfiguracji dla usługi Azure AD, użytkownik musi być zarówno administratora globalnego w usłudze Azure AD i właściciel subskrypcji platformy Azure.  Aby dowiedzieć się, jak dodać użytkowników do subskrypcji platformy Azure, zobacz [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](../..//role-based-access-control/role-assignments-portal.md).

Dostęp do Microsoft Online Services może wymagać użytkownika również posiadać licencję przed Otwórz portal usługi lub wykonywania zadań administracyjnych.

## <a name="assign-a-license-to-a-user-in-azure-ad"></a>Przypisywanie licencji do użytkownika w usłudze Azure AD

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) z rolą administratora globalnego lub właściciela.

1. Wybierz katalog usługi Azure AD, którą chcesz pracować, i ma licencje skojarzonych z nim.

1. Na lewym pasku nawigacyjnym kliknij **usługi Azure Active Directory**.

1. Kliknij przycisk **licencji**. Zostanie wyświetlona lista dostępnych licencji.

    ![Licencje usługi Azure Active Directory](./media/pim-roles/licenses-overview.png)

1. Kliknij przycisk z **produktu**.

1. Kliknij plan licencjonowania, który zawiera licencji, które chcesz rozesłać.

    ![Licencje produktów](./media/pim-roles/licenses-products.png)

1. Kliknij przycisk **przypisać** aby otworzyć okienko przypisywanie licencji.

    ![Licencjonowani użytkownicy](./media/pim-roles/licenses-licensed-users.png)

1. Wybierz użytkownika lub grupę, którą chcesz przypisać licencję.

    ![Przypisywanie licencji](./media/pim-roles/licenses-assign-license.png)

1. Kliknij przycisk **opcje przydziału** skonfigurować opcje przydziału.

    ![Opcje przypisania](./media/pim-roles/licenses-assignment-options.png)

1. Kliknij przycisk **przypisać** można przypisać licencji. Użytkownik ma teraz licencji.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Kolejne kroki

- [Rozpoczynanie korzystania z usługi PIM](pim-getting-started.md)
- [Przypisywanie ról katalogu usługi Azure AD w usłudze PIM](pim-how-to-add-role-to-user.md)

