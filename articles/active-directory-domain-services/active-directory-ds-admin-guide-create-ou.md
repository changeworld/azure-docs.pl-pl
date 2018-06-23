---
title: 'Usługami domenowymi Azure Active Directory: Przewodnik administrowania | Dokumentacja firmy Microsoft'
description: Tworzenie jednostki organizacyjnej (OU) w domenach zarządzanych usług domenowych Azure AD
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: 15bd837149b9856897eb83f86052a26b24a21fb0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36334313"
---
# <a name="create-an-organizational-unit-ou-on-an-azure-ad-domain-services-managed-domain"></a>Tworzenie jednostki organizacyjnej (OU) w domenie zarządzanej usług domenowych Azure AD
Azure domen zarządzanych w usługach domenowych AD to dwa wbudowane kontenery odpowiednio o nazwie "AADDC komputery" i "AADDC użytkowników". Kontener "Komputery AADDC" ma obiektów komputera dla wszystkich komputerów, które są przyłączone do domeny zarządzanej. Kontener "Użytkownicy AADDC" obejmuje użytkowników i grup w dzierżawie usługi Azure AD. Czasami może być konieczne utworzenie kont usług zarządzanych domeny do wdrażania obciążeń. W tym celu można utworzyć niestandardowy jednostki organizacyjnej (OU) w tej domenie zarządzanych i tworzenie konta usług w tej jednostce Organizacyjnej. W tym artykule przedstawiono sposób tworzenia jednostki Organizacyjnej w domenie zarządzanej.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby wykonać zadania opisane w tym artykule, należy:

1. Prawidłowy **subskrypcji platformy Azure**.
2. **Katalog usługi Azure AD** -albo synchronizowane z katalogu lokalnego lub w katalogu tylko w chmurze.
3. **Usługi domenowe Azure AD** musi być włączona dla katalogu usługi Azure AD. Jeśli nie zostało to jeszcze zrobione, należy wykonać wszystkie zadania opisane w temacie [Przewodnik wprowadzający](active-directory-ds-getting-started.md).
4. Maszynę wirtualną przyłączonych do domeny, z którego administrujesz domeny usług domenowych Azure AD zarządzane. Jeśli nie masz maszynę wirtualną, należy wykonać wszystkie zadania opisane w artykule zatytułowany [Dołącz maszynę wirtualną systemu Windows do domeny zarządzanej](active-directory-ds-admin-guide-join-windows-vm.md).
5. Wymagane poświadczenia **konta użytkownika należącego do grupy "Administratorzy kontrolera domeny usługi AAD"** w katalogu, aby utworzyć niestandardowe jednostki Organizacyjnej w domenie zarządzanej.

## <a name="install-ad-administration-tools-on-a-domain-joined-virtual-machine-for-remote-administration"></a>Zainstalować narzędzia administracji AD na maszynie wirtualnej przyłączonych do domeny dla administracji zdalnej
Azure domen zarządzanych w usługach domenowych AD można zarządzać zdalnie za pomocą znanych narzędzi administracyjnych usługi Active Directory takie jak Centrum administracyjne Active Directory (ADAC) lub AD PowerShell. Administratorzy dzierżawy nie masz uprawnień do połączenia się z kontrolerami domeny w domenie zarządzanych za pomocą pulpitu zdalnego. Aby administrować domeny zarządzanej, zainstaluj funkcję narzędzia administracji AD na maszynie wirtualnej przyłączony do domeny zarządzanej. Zapoznaj się z artykułem zatytułowany [administrowania domeny zarządzanej usług domenowych Azure AD](active-directory-ds-admin-guide-administer-domain.md) instrukcje.

## <a name="create-an-organizational-unit-on-the-managed-domain"></a>Utwórz jednostkę organizacyjną na domeny zarządzanej
Teraz, narzędzi administracyjnych AD są zainstalowane na przyłączonym do domeny maszyny wirtualnej, możemy użyć tych narzędzi można utworzyć jednostki organizacyjnej w domenie zarządzanej. Wykonaj następujące czynności:

> [!NOTE]
> Tylko członkowie grupy "Administratorzy kontrolera domeny usługi AAD" mają wymagane uprawnienia do tworzenia niestandardowych jednostki Organizacyjnej. Upewnij się, wykonaj następujące kroki jako użytkownik, który należy do tej grupy.
>
>

1. Na ekranie startowym kliknij **narzędzia administracyjne**. Narzędzia administracyjne AD zainstalowany na maszynie wirtualnej powinny być widoczne.

    ![Narzędzia administracyjne zainstalowane na serwerze](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. Kliknij przycisk **Centrum administracyjne usługi Active Directory**.

    ![Centrum administracyjne usługi Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. Aby wyświetlić domeny, kliknij nazwę domeny w okienku po lewej stronie (na przykład "contoso100.com").

    ![ADAC — widok domeny](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)
4. Po prawej stronie **zadania** okienku, kliknij przycisk **nowy** w węźle nazwy domeny. W tym przykładzie mamy kliknij **nowy** po prawej stronie w węźle "contoso100(local)" **zadania** okienka.

    ![ADAC - nową jednostkę Organizacyjną](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)
5. Powinny pojawić się opcję, aby utworzyć jednostkę organizacyjną. Kliknij przycisk **jednostki organizacyjnej** można uruchomić **Utwórz jednostkę organizacyjną** okna dialogowego.
6. W **Utwórz jednostkę organizacyjną** okna dialogowego, określ **nazwa** nowej jednostki organizacyjnej. Podaj krótki opis jednostki organizacyjnej. Można również ustawić **zarządzany przez** pole jednostki organizacyjnej. Aby utworzyć niestandardowe jednostki Organizacyjnej, kliknij przycisk **OK**.

    ![ADAC — Tworzenie jednostki Organizacyjnej w oknie dialogowym](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)
7. Nowo utworzona jednostka Organizacyjna powinien zostać wyświetlony w AD Centrum administracyjne (ADAC).

    ![ADAC - utworzyć jednostkę Organizacyjną](./media/active-directory-domain-services-admin-guide/create-ou-done.png)

## <a name="permissionssecurity-for-newly-created-ous"></a>Uprawnienia/zabezpieczeń dla nowo utworzonej jednostki organizacyjne
Domyślnie użytkownika (członkiem grupy "Administratorzy kontrolera domeny usługi AAD"), który utworzył niestandardowej jednostki Organizacyjnej uzyska uprawnienia administracyjne (Pełna kontrola) w jednostce Organizacyjnej. Użytkownik może następnie przejdź dalej i przyznać uprawnienia do innych użytkowników lub grupy "Administratorzy kontrolera domeny usługi AAD" zgodnie z potrzebami. Jak pokazano na poniższym zrzucie ekranu użytkownika "bob@domainservicespreview.onmicrosoft.com" twórcę nowej jednostki organizacyjnej "MyCustomOU" jest uprawnienie do pełnej kontroli nad nim.

 ![ADAC - nowych zabezpieczeń jednostki Organizacyjnej](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)

## <a name="notes-on-administering-custom-ous"></a>Uwagi dotyczące administrowania niestandardowych jednostek organizacyjnych
Teraz, po utworzeniu niestandardowej jednostki Organizacyjnej, można teraz i tworzenie użytkowników, grup komputerów i kont usług w tej jednostce Organizacyjnej. Nie można przenieść użytkowników lub grup z jednostki Organizacyjnej 'AADDC użytkownicy' do niestandardowych jednostek organizacyjnych.

> [!WARNING]
> Konta użytkowników, grup, kont usług i obiektów komputerów, utworzone w obszarze niestandardowych jednostek organizacyjnych nie są dostępne w dzierżawie usługi Azure AD. Innymi słowy te obiekty nie są wyświetlane przy użyciu interfejsu API programu Azure AD Graph lub w interfejsie użytkownika programu Azure AD. Te obiekty są dostępne tylko w domenie zarządzanej usług domenowych Azure AD.
>
>

## <a name="related-content"></a>Powiązana zawartość
* [Administrowanie domeną zarządzaną usług Azure AD Domain Services](active-directory-ds-admin-guide-administer-domain.md)
* [Konfigurowanie zasad grupy w domenie zarządzanej](active-directory-ds-admin-guide-administer-group-policy.md)
* [Centrum administracyjne usługi Active Directory: Wprowadzenie](https://technet.microsoft.com/library/dd560651.aspx)
* [Przewodnik krok po kroku dotyczący kont usług](https://technet.microsoft.com/library/dd548356.aspx)
