---
title: Role w programie Azure AD Privileged Identity Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jakie role są używane dla tożsamości uprzywilejowanych z rozszerzeniem Azure Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 03/04/2018
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.openlocfilehash: 48ff4b4957ac1f9f8f0d6737335811a3e2df0b68
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34698309"
---
# <a name="different-administrative-role-in-azure-active-directory-pim"></a>Innej roli administracyjnej w usłudze Azure Active Directory PIM
<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

Można przypisywać użytkowników w organizacji, aby różne role administracyjne w usłudze Azure AD. Te przypisania ról kontrolować, które zadania, takie jak dodawanie lub usuwanie użytkowników i zmiana ustawień usługi użytkownicy będą mogli wykonać w usłudze Azure AD, Office 365 i innych usług Microsoft Online Services i połączonych aplikacji.  

> [!IMPORTANT]
> Firma Microsoft zaleca, które są zarządzane przy użyciu usługi Azure AD [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) w portalu Azure.

Administrator globalny może aktualizować której użytkownicy są **trwale** przypisane do ról w usłudze Azure AD przy użyciu poleceń cmdlet programu PowerShell, takich jak `Add-MsolRoleMember` i `Remove-MsolRoleMember`, lub za pośrednictwem portalu zgodnie z opisem w [przypisywanie Role administratora w usłudze Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md).

Azure AD Privileged Identity Management (PIM) zarządza zasadami uprzywilejowanego dostępu dla użytkowników w usłudze Azure AD. PIM przypisuje użytkowników do co najmniej jedną rolę w usłudze Azure AD i można przypisać komuś trwale do roli lub kwalifikuje się do roli. Jeśli użytkownik jest trwale przypisana do roli lub aktywuje przypisania roli kwalifikujących się, a następnie ich zarządzania usługi Azure Active Directory, usługi Office 365 i innych aplikacji z uprawnienia przypisane do ich ról.

Nie ma żadnej różnicy w dostępie do osoby na stałe i przypisanie roli kwalifikujących się. Jedyna różnica polega na tym, że niektóre osoby nie wymagają dostępu przez cały czas. Są uprawnione do roli i można ją włączyć i wyłączyć po każdej zmianie muszą.

## <a name="roles-managed-in-pim"></a>Role zarządzane w PIM
Zarządzanie tożsamościami uprzywilejowanymi umożliwia przypisywanie użytkowników do typowych ról administratora, w tym:

* **Administrator globalny** (znanego również jako administrator firmy) ma dostęp do wszystkich funkcji administracyjnych. Może mieć więcej niż jednego administratora globalnego w organizacji. Osoba, która zarejestruje się w celu zakupu usługi Office 365 automatycznie staje się globalnego administratora.
* **Administrator ról uprzywilejowanych** zarządza Azure AD PIM i aktualizuje przypisania roli dla innych użytkowników.  
* **Administrator rozliczeń** dokonuje zakupów, zarządza subskrypcjami, zarządza biletami pomocy technicznej i monitoruje kondycję usługi.
* **Administrator haseł** Resetuje hasła, zarządza żądaniami obsługi i monitoruje kondycję usługi. Administratorzy haseł są ograniczone do resetowania haseł dla użytkowników.
* **Administrator usługi** zarządza żądaniami obsługi i monitoruje kondycję usługi.
  
  > [!NOTE]
  > Jeśli używasz usługi Office 365, następnie przed przypisaniem rolę administratora usługi do użytkownika, najpierw Przypisz użytkownika uprawnień administracyjnych do usług, takich jak Exchange Online.
  > 
  > 
* **Administrator zarządzania użytkownikami** Resetuje hasła, monitoruje kondycję usługi oraz zarządza kontami użytkowników, grup użytkowników i żądań obsługi. Administrator zarządzania użytkownika nie można usunąć administratora globalnego, tworzenia innych ról administratora ani resetowania haseł dla rozliczeń, globalnych i administratorów usługi.
* **Administrator programu Exchange** ma dostęp administracyjny do usługi Exchange Online za pośrednictwem Centrum administracyjnego programu Exchange (EAC) i można wykonać prawie wszystkie zadania w usłudze Exchange Online.
* **Administrator programu SharePoint (wersja zapoznawcza)** ma dostęp administracyjny do usługi SharePoint Online za pomocą Centrum administracyjnego usługi SharePoint Online i można wykonać prawie wszystkie zadania w usłudze SharePoint Online. Ta rola jest obecnie w przeglądzie. Uprawnionych użytkowników mogą wystąpić opóźnienia, po aktywowaniu w PIM za pomocą tej roli w ramach programu SharePoint.
* **Skype dla firm administratora** ma dostęp administracyjny do usługi Skype dla firm za pomocą programu Skype dla firm Centrum administracyjnego, a można wykonać prawie wszystkie zadania w programie Skype dla firm Online.

Przeczytaj następujące artykuły, aby uzyskać więcej informacji na temat [przypisywanie ról administratorów w usłudze Azure AD](active-directory-assign-admin-roles-azure-portal.md) i [przypisywanie ról administratora w usłudze Office 365](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504).

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


Za pomocą usługi PIM, można [przypisać tych ról do użytkownika](active-directory-privileged-identity-management-how-to-add-role-to-user.md) , dzięki czemu użytkownik może [Uaktywnij rolę w razie potrzeby](active-directory-privileged-identity-management-how-to-activate-role.md).

Jeśli chcesz udostępnić innego użytkownika do zarządzania w PIM sam ról, które PIM wymaga od użytkownika posiadania opisano dalsze w [sposób udzielić dostępu do usługi PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## <a name="roles-not-managed-in-pim"></a>Role nie są zarządzane w PIM
Role w ramach usługi Exchange Online lub SharePoint Online, z wyjątkiem wymienionych powyżej, nie są reprezentowane w usłudze Azure AD i dlatego nie są widoczne w PIM. Aby uzyskać więcej informacji na temat zmieniania przypisań ról szczegółowych w tych usług Office 365, zobacz [uprawnienia w usłudze Office 365](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d).

Subskrypcje platformy Azure i grup zasobów również nie są reprezentowane w usłudze Azure AD. Aby zarządzać subskrypcjami platformy Azure, zobacz [jak dodać lub zmienić role administratora platformy Azure](../billing/billing-add-change-azure-subscription-administrator.md) i uzyskać więcej informacji o Azure RBAC, zobacz [kontroli dostępu](../role-based-access-control/role-assignments-portal.md).

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## <a name="user-roles-and-signing-in"></a>Role użytkownika i logowanie
Przypadku pewnych usług firmy Microsoft i aplikacji przypisanie użytkownika do roli może nie być wystarczające, aby umożliwić użytkownikowi mieć uprawnienia administratora.

Dostęp do portalu Azure wymaga się, że użytkownik jest administratorem usługi ani współadministratorem subskrypcji platformy Azure, nawet jeśli użytkownik nie musi zarządzać subskrypcjami platformy Azure.  Na przykład do zarządzania ustawieniami konfiguracji dla usługi Azure AD, użytkownik musi być zarówno w usłudze Azure AD administratora globalnego, jak i subskrypcji współadministratorem subskrypcji platformy Azure.  Aby dowiedzieć się, jak dodać użytkowników do subskrypcji platformy Azure, zobacz [jak dodać lub zmienić role administratora platformy Azure](../billing/billing-add-change-azure-subscription-administrator.md).

Dostęp do usług Online firmy Microsoft mogą wymagać użytkownika również należy przypisać im licencję przed ich Otwórz portal usługi lub wykonywać zadania administracyjne.

## <a name="assign-a-license-to-a-user-in-azure-ad"></a>Przypisywanie licencji do użytkownika w usłudze Azure AD
1. Zaloguj się do [portalu Azure](http://portal.azure.com) z konta administratora globalnego lub konto administratora współpracującego.
3. Wybierz usługi Azure AD i katalogu, w którym ma pracować z, a ma licencji skojarzonych z nim.
4. Wybierz **licencji** po lewej stronie. Zostanie wyświetlona lista dostępnych licencji.
5. Wybierz planu licencjonowania, który zawiera licencji, które chcesz dystrybuować.
6. Wybierz **przypisywać użytkowników**.
7. Wybierz użytkownika, którego chcesz przypisać licencję.
8. Kliknij przycisk **przypisać** przycisku.  Użytkownik może teraz logować się do platformy Azure.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

