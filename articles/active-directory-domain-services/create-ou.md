---
title: Tworzenie jednostki organizacyjnej w usługach domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć niestandardową jednostkę organizacyjną (OU) i zarządzać nią w domenie zarządzanej usług domenowych usługi azure AD.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: iainfou
ms.openlocfilehash: 7abbdf03e85f425f65a45e6640b82529c2b9c84f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614068"
---
# <a name="create-an-organizational-unit-ou-in-an-azure-ad-domain-services-managed-domain"></a>Tworzenie jednostki organizacyjnej w domenie zarządzanej usług domenowych usługi azure AD

Jednostki organizacyjne w Usługach domenowych Active Directory (AD DS) umożliwiają logiczne grupowanie obiektów, takich jak konta użytkowników, konta usług lub konta komputerów. Następnie można przypisać administratorów do określonych procesorów operacyjnych i zastosować zasady grupy, aby wymusić docelowe ustawienia konfiguracji.

Domeny zarządzane usługi Azure AD DS obejmują dwa wbudowane jednostki organizacyjne — *komputery AADDC* i *użytkowników usługi AADDC.* Obiekt organizacyjny *komputerów AADDC* zawiera obiekty komputera dla wszystkich komputerów, które są przyłączone do domeny zarządzanej. *AADDC Użytkownicy OU* obejmuje użytkowników i grup zsynchronizowanych z dzierżawy usługi Azure AD. Podczas tworzenia i uruchamiania obciążeń korzystających z usług Azure AD DS może być konieczne utworzenie kont usług dla aplikacji w celu uwierzytelnienia się. Aby zorganizować te konta usługi, często należy utworzyć niestandardową jednostkę organizacyjną w domenie zarządzanej usług Azure AD DS, a następnie utworzyć konta usługi w tej jednostki organizacyjnej.

W środowisku hybrydowym producenci organizacyjni utworzone w lokalnym środowisku usług AD DS nie są synchronizowane z usługą Azure AD DS. Domeny zarządzane usługi Azure AD DS używają płaskiej struktury jednostki organizacyjnej. Wszystkie konta użytkowników i grupy są przechowywane w kontenerze *Użytkownicy usługi AADDC,* mimo że są synchronizowane z różnych domen lokalnych lub lasów, nawet jeśli skonfigurowano tam hierarchiczną strukturę operacji organizacyjnej.

W tym artykule pokazano, jak utworzyć jednostkę organizacyjną w domenie zarządzanej usług Azure AD DS.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby ukończyć ten artykuł, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem tylko w chmurze.
    * W razie potrzeby [utwórz dzierżawę usługi Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z kontem.][associate-azure-ad-tenant]
* Domena zarządzana usługami domenowymi Usługi Active Directory platformy Azure włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie potrzeby wykonaj samouczek, aby [utworzyć i skonfigurować wystąpienie usług domenowych Usługi domenowe Active Directory][create-azure-ad-ds-instance]platformy Azure .
* Maszyna wirtualna z zarządzaniem systemem Windows Server, która jest przyłączona do domeny zarządzanej usług Azure AD DS.
    * W razie potrzeby wykonaj samouczek, aby [utworzyć maszynę wirtualną do zarządzania][tutorial-create-management-vm].
* Konto użytkownika, które jest członkiem grupy *administratorów usługi Azure AD DC* w dzierżawie usługi Azure AD.

## <a name="custom-ou-considerations-and-limitations"></a>Niestandardowe zagadnienia i ograniczenia dotyczące ou wynajęcie

Podczas tworzenia niestandardowych jednostek organizacyjnych w domenie zarządzanej usług Azure AD DS uzyskujesz dodatkową elastyczność zarządzania dla zarządzania użytkownikami i stosowania zasad grupy. W porównaniu do lokalnego środowiska usług AD DS istnieją pewne ograniczenia i zagadnienia podczas tworzenia niestandardowej struktury instalacji organizacyjnej w usługach Azure AD DS i zarządzania nimi:

* Aby utworzyć niestandardowe instalacje organizacyjne, użytkownicy muszą być członkami grupy *Administratorzy kontrolera domeny usługi AAD.*
* Użytkownik, który tworzy niestandardową instalację organizacyjną, ma uprawnienia administracyjne (pełną kontrolę) nad tą ou i jest właścicielem zasobu.
    * Domyślnie grupa *Administratorzy kontrolera domeny usługi AAD* ma również pełną kontrolę nad niestandardową instalacją organizacyjną.
* Zostanie utworzona domyślna usługa organizacyjna dla *użytkowników usługi AADDC,* która zawiera wszystkie zsynchronizowane konta użytkowników z dzierżawy usługi Azure AD.
    * Nie można przenosić użytkowników ani grup z instalacji organizacyjnej *użytkowników usługi AADDC* do utworzonych niestandardowych instalacji organizacyjnych. Tylko konta użytkowników lub zasoby utworzone w domenie zarządzanej usług Azure AD DS można przenieść do niestandardowych procesorów operacyjnych.
* Konta użytkowników, grupy, konta usług i obiekty komputera utworzone w ramach niestandardowych procesorów organizacyjnych nie są dostępne w dzierżawie usługi Azure AD.
    * Te obiekty nie są wyświetlane przy użyciu interfejsu API programu Microsoft Graph ani interfejsu użytkownika usługi Azure AD; są one dostępne tylko w domenie zarządzanej usług Azure AD DS.

## <a name="create-a-custom-ou"></a>Tworzenie niestandardowej instalacji organizacyjnej

Aby utworzyć niestandardową instalację organizacyjną, należy użyć narzędzi administracyjnych usługi Active Directory z maszyny wirtualnej przyłączone do domeny. Centrum administracyjne usługi Active Directory umożliwia wyświetlanie, edytowanie i tworzenie zasobów w domenie zarządzanej usług Azure AD DS, w tym w jednostkach organizacyjnych.

> [!NOTE]
> Aby utworzyć niestandardową jednostkę organizacyjną w domenie zarządzanej usług Azure AD DS, musisz zalogować się do konta użytkownika, które jest członkiem grupy *Administratorzy kontrolera domeny usługi AAD.*

1. Zaloguj się do maszyny wirtualnej zarządzania. Aby uzyskać instrukcje dotyczące łączenia się przy użyciu portalu Azure portal, zobacz [Łączenie się z maszyną wirtualną systemu Windows Server][connect-windows-server-vm].
1. Na ekranie startowym wybierz pozycję **Narzędzia administracyjne**. Zostanie wyświetlona lista dostępnych narzędzi do zarządzania, które zostały zainstalowane w samouczku w celu [utworzenia maszyny wirtualnej do zarządzania][tutorial-create-management-vm].
1. Aby utworzyć procesory organizacyjne i zarządzać nimi, wybierz pozycję **Centrum administracyjne usługi Active Directory** z listy narzędzi administracyjnych.
1. W lewym okienku wybierz domenę zarządzana usługą Azure AD DS, taką jak *aaddscontoso.com*. Zostanie wyświetlona lista istniejących procesorów operacyjnych i zasobów:

    ![Wybieranie domeny zarządzanej usług Azure AD DS w Centrum administracyjnym usługi Active Directory](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)

1. Okienko **Zadania** jest wyświetlane po prawej stronie Centrum administracyjnego usługi Active Directory. W obszarze domeny, na przykład *aaddscontoso.com*, wybierz pozycję **Nowa > Jednostka organizacyjna**.

    ![Wybierz opcję utworzenia nowej usługi organizacyjnej w Centrum administracyjnym usługi Active Directory](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)

1. W oknie dialogowym **Tworzenie jednostki organizacyjnej** określ **nazwę** nowej jednostki organizacyjnej, taką jak *MyCustomOu*. Podaj krótki opis dla tej instalacji organizacyjnej, na przykład *niestandardowa instalacja organizacyjna dla kont usług.* W razie potrzeby można również ustawić pole **Zarządzane według** jednostki organizacyjnej. Aby utworzyć niestandardową instalację organizacyjną, wybierz przycisk **OK**.

    ![Tworzenie niestandardowej instalacji organizacyjnej z Centrum administracyjnego usługi Active Directory](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)

1. W Centrum administracyjnym usługi Active Directory niestandardowa instalacja organizacyjna jest teraz wyświetlana i jest dostępna do użycia:

    ![Niestandardowa instalacja organizacyjna dostępna do użycia w Centrum administracyjnym usługi Active Directory](./media/active-directory-domain-services-admin-guide/create-ou-done.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat korzystania z narzędzi administracyjnych lub tworzenia i używania kont usług, zobacz następujące artykuły:

* [Centrum administracyjne usługi Active Directory: wprowadzenie](https://technet.microsoft.com/library/dd560651.aspx)
* [Przewodnik krok po kroku dotyczący kont usług](https://technet.microsoft.com/library/dd548356.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm
