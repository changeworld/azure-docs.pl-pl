---
title: Tworzenie jednostki organizacyjnej (OU) w Azure AD Domain Services | Microsoft Docs "
description: Dowiedz się, jak utworzyć niestandardową jednostkę organizacyjną (OU) w Azure AD Domain Services domenie zarządzanej i zarządzać nią.
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
ms.openlocfilehash: 4db6ad83c44e0c811df0a3b91a473861e4e1ab87
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367144"
---
# <a name="create-an-organizational-unit-ou-in-an-azure-ad-domain-services-managed-domain"></a>Tworzenie jednostki organizacyjnej (OU) w Azure AD Domain Services domenie zarządzanej

Jednostki organizacyjne (OU) w Active Directory Domain Services (AD DS) umożliwiają logiczne grupowanie obiektów, takich jak konta użytkowników, konta usług lub konta komputerów. Następnie można przypisać administratorów do określonych jednostek organizacyjnych i zastosować zasady grupy w celu wymuszenia ustawień konfiguracji.

Domeny zarządzane AD DS platformy Azure obejmują dwa wbudowane jednostki organizacyjne — *komputery AADDC* i *AADDC użytkowników*. Jednostki organizacyjne *komputerów AADDC* zawierają obiekty komputerów dla wszystkich komputerów, które są przyłączone do domeny zarządzanej. Jednostka organizacyjna *AADDC użytkownicy* obejmuje użytkowników i grupy synchronizowane w ramach dzierżawy usługi Azure AD. Podczas tworzenia i uruchamiania obciążeń korzystających z usługi Azure AD DS może być konieczne utworzenie kont usługi dla aplikacji w celu samodzielnego uwierzytelnienia. Aby zorganizować te konta usług, często należy utworzyć niestandardową jednostkę organizacyjną w domenie zarządzanej AD DS platformy Azure, a następnie utworzyć konta usług w tej jednostce organizacyjnej.

W środowisku hybrydowym jednostki organizacyjne utworzone w środowisku lokalnym AD DS nie są zsynchronizowane z usługą Azure AD DS. Domeny zarządzane AD DS platformy Azure używają płaskiej struktury jednostki organizacyjnej. Wszystkie konta użytkowników i grupy są przechowywane w kontenerze *AADDC users* , mimo że są synchronizowane z różnych domen lokalnych lub lasów, nawet jeśli skonfigurowano hierarchiczną strukturę jednostki organizacyjnej.

W tym artykule opisano sposób tworzenia jednostki organizacyjnej w domenie zarządzanej AD DS platformy Azure.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby wykonać ten artykuł, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem w chmurze.
    * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].
* Azure Active Directory Domain Services zarządzana domena włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie potrzeby Uzupełnij samouczek, aby [utworzyć i skonfigurować wystąpienie Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Maszyna wirtualna zarządzania systemem Windows Server, która jest dołączona do domeny zarządzanej AD DS platformy Azure.
    * W razie potrzeby Ukończ samouczek, aby [utworzyć maszynę wirtualną zarządzania][tutorial-create-management-vm].
* Konto użytkownika, które jest członkiem grupy *administratorów DC usługi Azure AD* w dzierżawie usługi Azure AD.

## <a name="custom-ou-considerations-and-limitations"></a>Niestandardowe zagadnienia i ograniczenia jednostki organizacyjnej

Gdy tworzysz niestandardowe jednostki organizacyjne w domenie zarządzanej AD DS platformy Azure, uzyskasz dodatkową elastyczność zarządzania dla zarządzania użytkownikami i stosowania zasad grupy. W porównaniu do lokalnego środowiska AD DS, istnieją pewne ograniczenia i zagadnienia dotyczące tworzenia niestandardowej struktury jednostki organizacyjnej w usłudze Azure AD DS i zarządzania nią:

* Aby utworzyć niestandardowe jednostki organizacyjne, użytkownicy muszą być członkami grupy *administratorów kontrolera domeny usługi AAD* .
* Użytkownik, który tworzy niestandardową jednostkę organizacyjną, otrzymuje uprawnienia administracyjne (Pełna kontrola) w tej jednostce organizacyjnej i jest właścicielem zasobu.
    * Domyślnie grupa Administratorzy usługi *AAD DC* ma także pełną kontrolę nad niestandardową jednostką organizacyjną.
* Zostanie utworzona domyślna jednostka organizacyjna dla *użytkowników AADDC* , która zawiera wszystkie synchronizowane konta użytkowników z dzierżawy usługi Azure AD.
    * Nie można przenieść użytkowników ani grup z jednostki organizacyjnej *Użytkownicy AADDC* do niestandardowych jednostek organizacyjnych, które tworzysz. Tylko konta użytkowników lub zasoby utworzone w domenie zarządzanej AD DS platformy Azure można przenieść do niestandardowych jednostek organizacyjnych.
* Konta użytkowników, grupy, konta usług i obiekty komputerów tworzone w obszarze niestandardowe jednostki organizacyjne nie są dostępne w dzierżawie usługi Azure AD.
    * Te obiekty nie są wyświetlane przy użyciu interfejsu API Microsoft Graph ani w interfejsie użytkownika usługi Azure AD; są one dostępne tylko w domenie zarządzanej AD DS platformy Azure.

## <a name="create-a-custom-ou"></a>Tworzenie niestandardowej jednostki organizacyjnej

Aby utworzyć niestandardową jednostkę organizacyjną, należy użyć Active Directory narzędzi administracyjnych z maszyny wirtualnej przyłączonej do domeny. Centrum administracyjne usługi Active Directory umożliwia wyświetlanie, edytowanie i tworzenie zasobów w domenie zarządzanej AD DS platformy Azure, w tym jednostek organizacyjnych.

> [!NOTE]
> Aby utworzyć niestandardową jednostkę organizacyjną w domenie zarządzanej AD DS platformy Azure, musisz zalogować się na konto użytkownika, które jest członkiem grupy *administratorów kontrolera domeny usługi AAD* .

1. Zaloguj się do maszyny wirtualnej zarządzania. Aby uzyskać instrukcje dotyczące sposobu nawiązywania połączenia przy użyciu Azure Portal, zobacz [nawiązywanie połączenia z maszyną wirtualną z systemem Windows Server][connect-windows-server-vm].
1. Na ekranie startowym wybierz pozycję **Narzędzia administracyjne**. Zostanie wyświetlona lista dostępnych narzędzi do zarządzania, które zostały zainstalowane w samouczku, aby [utworzyć maszynę wirtualną zarządzania][tutorial-create-management-vm].
1. Aby utworzyć jednostki organizacyjne i zarządzać nimi, wybierz **Centrum administracyjne usługi Active Directory** z listy narzędzi administracyjnych.
1. W lewym okienku wybierz domenę zarządzaną platformy Azure AD DS, na przykład *aadds.contoso.com*. Zostanie wyświetlona lista istniejących jednostek organizacyjnych i zasobów:

    ![Wybierz domenę zarządzaną platformy Azure AD DS w Centrum administracyjne usługi Active Directory](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)

1. Okienko **zadania** zostanie wyświetlone po prawej stronie Centrum administracyjne usługi Active Directory. W obszarze domeny, takiej jak *aadds.contoso.com*, wybierz pozycję **Nowa > jednostki organizacyjnej**.

    ![Wybierz opcję utworzenia nowej jednostki organizacyjnej w Centrum administracyjne usługi Active Directory](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)

1. W oknie dialogowym **Tworzenie jednostki organizacyjnej** Podaj **nazwę** nowej jednostki organizacyjnej, na przykład *MyCustomOu*. Podaj krótki opis jednostki organizacyjnej, na przykład *niestandardowa jednostka organizacyjna dla kont usług*. W razie potrzeby można również ustawić pole **zarządzane przez** dla jednostki organizacyjnej. Aby utworzyć niestandardową jednostkę organizacyjną, wybierz **przycisk OK**.

    ![Tworzenie niestandardowej jednostki organizacyjnej na podstawie Centrum administracyjne usługi Active Directory](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)

1. W Centrum administracyjne usługi Active Directory, niestandardowa jednostka organizacyjna jest teraz wyświetlana i jest dostępna do użycia:

    ![Niestandardowa jednostka organizacyjna dostępna do użycia w Centrum administracyjne usługi Active Directory](./media/active-directory-domain-services-admin-guide/create-ou-done.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat korzystania z narzędzi administracyjnych lub tworzenia i używania kont usług, zobacz następujące artykuły:

* [Centrum administracyjne usługi Active Directory: Wprowadzenie](https://technet.microsoft.com/library/dd560651.aspx)
* [Przewodnik krok po kroku dotyczący kont usług](https://technet.microsoft.com/library/dd548356.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm
