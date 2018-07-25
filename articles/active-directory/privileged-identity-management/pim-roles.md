---
title: Role w usłudze Azure AD Privileged Identity Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jakie role są używane dla uprzywilejowanych tożsamości z rozszerzeniem Azure Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 07/23/2018
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.openlocfilehash: 6553fdba463144c6eda1e35c0967e92a3c44aff6
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225580"
---
# <a name="directory-roles-you-can-manage-using-azure-ad-pim"></a>Role w katalogu, którymi można zarządzać za pomocą usługi Azure AD PIM
<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

Można przypisać użytkowników w organizacji, aby różne role administracyjne w usłudze Azure AD. Te przypisania roli kontrolować, które zadania, takie jak dodawanie lub usuwanie użytkowników lub zmiana ustawień usługi Użytkownicy są możliwe do wykonania w usłudze Azure AD, Office 365 i innych Microsoft Online Services i połączonych aplikacji.  

Administrator globalny może aktualizować, których użytkownicy **trwale** przypisane do ról w usłudze Azure AD za pośrednictwem portalu, zgodnie z opisem w [przypisywanie ról administratorów w usłudze Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md) lub za pomocą [ Polecenia programu PowerShell](/powershell/module/azuread#directory_roles).

Usługa Azure AD Privileged Identity Management (PIM) zarządza zasadami uprzywilejowanego dostępu dla użytkowników w usłudze Azure AD. PIM przypisuje użytkowników do co najmniej jedną rolę w usłudze Azure AD i można przypisać ktoś ma być trwałe w roli lub uprawnieni do roli. Gdy użytkownik jest trwale przypisana do roli lub aktywuje kwalifikujące się przypisanie roli, a następnie ich zarządzania usługi Azure Active Directory, usługi Office 365 i innych aplikacji za pomocą uprawnień przypisanych do ich ról.

Nie ma żadnej różnicy w programie access poświęcić osobę z trwałe i kwalifikujące się przypisanie roli. Jedyna różnica polega na tym, że niektórzy użytkownicy nie wymagają tego dostępu przez cały czas. Będą uprawnieni do roli i można ją włączyć i wyłączyć zawsze, gdy muszą one.

## <a name="roles-managed-in-pim"></a>Role zarządzane w usłudze PIM
Privileged Identity Management umożliwia przypisywanie użytkowników do typowych ról administratora, w tym:

* **Administrator globalny** (znanego również jako administrator firmy) ma dostęp do wszystkich funkcji administracyjnych. Może mieć więcej niż jednego administratora globalnego w organizacji. Osoba, która zarejestruje się w celu zakupu usługi Office 365, automatycznie staje się administratorem globalnym.
* **Administrator ról uprzywilejowanych** zarządza usługa Azure AD PIM i aktualizuje przypisania roli dla innych użytkowników.  
* **Administrator rozliczeń** dokonuje zakupów, zarządza subskrypcjami, zarządza biletami pomocy technicznej i monitoruje kondycję usługi.
* **Administrator haseł** Resetuje hasła, zarządza żądaniami obsługi i monitoruje kondycję usługi. Administratorzy haseł mogą jedynie resetować hasła użytkowników.
* **Administrator usługi** zarządza żądaniami obsługi i monitoruje kondycję usługi.
  
  > [!NOTE]
  > Jeśli używasz usługi Office 365, następnie przed przypisaniem roli administratora usługi dla użytkownika, najpierw Przypisz użytkownika uprawnień administracyjnych do usługi, takich jak Exchange Online.
  > 
  > 
* **Administrator zarządzający użytkownikami** Resetuje hasła, monitoruje kondycję usługi oraz zarządza kontami użytkowników, grup użytkowników i żądania obsługi. Administrator zarządzający użytkownikami nie można usunąć administratora globalnego, tworzyć innych ról administratora ani resetować haseł rozliczeń, globalnych i administratorów usługi.
* **Administrator programu Exchange** ma dostęp administracyjny do usługi Exchange Online za pośrednictwem Centrum administracyjnego programu Exchange (EAC) i mogą wykonywać niemal dowolne zadanie w usłudze Exchange Online.
* **Administrator programu SharePoint (wersja zapoznawcza)** ma dostęp administracyjny do usługi SharePoint Online za pośrednictwem Centrum administracyjnego programu SharePoint Online i mogą wykonywać niemal dowolne zadanie w usłudze SharePoint Online. Ta rola jest obecnie w wersji zapoznawczej. Uprawnieni użytkownicy mogą wystąpić opóźnienia, po aktywowaniu w usłudze PIM przy użyciu tej roli w programie SharePoint.
* **Skype dla firm administratora** ma dostęp administracyjny do usługi Skype dla firm za pomocą programu Skype dla firm Centrum administracyjnego i może wykonywać niemal wszystkich zadań w programie Skype dla firm Online.

Przeczytaj następujące artykuły, aby uzyskać więcej informacji o [przypisywanie ról administratorów w usłudze Azure AD](../users-groups-roles/directory-assign-admin-roles.md) i [przypisywanie ról administratora w usłudze Office 365](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504).

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


Przy użyciu usługi PIM, możesz [Przypisz te role dla użytkownika](pim-how-to-add-role-to-user.md) tak, aby użytkownik może [aktywować rolę w razie](pim-how-to-activate-role.md).

Jeśli chcesz nadać inny dostęp użytkownika do zarządzania w usłudze PIM sam, ról, które PIM wymaga od użytkownika posiadania są dokładniejszym opisem zawartym w [udzielanie dostępu do usługi PIM](pim-how-to-give-access-to-pim.md).

<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## <a name="roles-not-managed-in-pim"></a>Role, które nie są zarządzane w usłudze PIM
Role w usłudze Exchange Online lub SharePoint Online, z wyjątkiem tych wymienionych powyżej, nie są reprezentowane w usłudze Azure AD i dlatego nie są widoczne w usłudze PIM. Aby uzyskać więcej informacji na temat zmieniania przypisań ról szczegółowych w tych usługach Office 365, zobacz [uprawnień w usłudze Office 365](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d).

Subskrypcje platformy Azure i grup zasobów również nie są reprezentowane w usłudze Azure AD. Aby zarządzać subskrypcjami platformy Azure, zobacz [jak dodać lub zmienić role administratora platformy Azure](../../billing/billing-add-change-azure-subscription-administrator.md) i aby uzyskać więcej informacji na temat RBAC platformy Azure, zobacz [kontroli dostępu opartej na rolach na platformie Azure](../../role-based-access-control/role-assignments-portal.md).

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## <a name="user-roles-and-signing-in"></a>Role użytkowników i logowanie
Niektóre usługi firmy Microsoft i aplikacji przypisanie użytkownika do roli może nie być wystarczające, aby umożliwić użytkownikowi uprawnienia administratora.

Dostęp do portalu Azure wymaga użytkownik być administratorem usługi lub administrator współpracujący dla subskrypcji platformy Azure, nawet wtedy, gdy użytkownik nie musi zarządzać subskrypcjami platformy Azure.  Na przykład aby zarządzać ustawieniami konfiguracji dla usługi Azure AD, użytkownik musi być administratorem globalnym w usłudze Azure AD i subskrypcji, administrator współpracujący dla subskrypcji platformy Azure.  Aby dowiedzieć się, jak dodać użytkowników do subskrypcji platformy Azure, zobacz [jak dodać lub zmienić role administratora platformy Azure](../../billing/billing-add-change-azure-subscription-administrator.md).

Dostęp do Microsoft Online Services może wymagać użytkownika również posiadać licencję przed Otwórz portal usługi lub wykonywania zadań administracyjnych.

## <a name="assign-a-license-to-a-user-in-azure-ad"></a>Przypisywanie licencji do użytkownika w usłudze Azure AD
1. Zaloguj się do [witryny Azure portal](http://portal.azure.com) przy użyciu konta administratora globalnego lub administratora współpracującego konta.
3. Wybieranie usługi Azure AD i katalogu w którym chcesz pracować z i który ma licencje skojarzonych z nim.
4. Wybierz **licencji** po lewej stronie. Zostanie wyświetlona lista dostępnych licencji.
5. Wybierz plan licencjonowania, który zawiera licencji, które chcesz rozesłać.
6. Wybierz **przypisywać użytkowników**.
7. Wybierz, którą chcesz przypisać licencję do użytkownika.
8. Kliknij przycisk **przypisać** przycisku.  Użytkownik może teraz zalogować do platformy Azure.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]

