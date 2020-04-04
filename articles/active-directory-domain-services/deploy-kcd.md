---
title: Delegowanie z ograniczeniami protokołu Kerberos dla usług domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak włączyć delegowanie ograniczone do protokołu Kerberos oparte na zasobach w domenie zarządzanej Usług domenowych Usługi domenowe Active Directory platformy Azure.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 07aa9ade25d1d986833b6da2f3907d07b752b662
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655441"
---
# <a name="configure-kerberos-constrained-delegation-kcd-in-azure-active-directory-domain-services"></a>Konfigurowanie delegowania z ograniczeniami protokołu Kerberos (KCD) w usługach domenowych usługi active directory platformy Azure

Podczas uruchamiania aplikacji może istnieć potrzeba, aby te aplikacje uzyskiwać dostęp do zasobów w kontekście innego użytkownika. Usługi domenowe Active Directory (AD DS) obsługują mechanizm o nazwie *delegowanie protokołu Kerberos,* który umożliwia ten przypadek użycia. Delegowanie *ograniczone* kerberos (KCD) następnie opiera się na tym mechanizmie, aby zdefiniować określone zasoby, które mogą być dostępne w kontekście użytkownika. Domeny zarządzane usług domenowych usługi Active Directory (Azure AD DS) są bezpieczniej blokowane niż tradycyjne lokalne środowiska usług AD DS, więc użyj bezpieczniejszego kcd *opartego na zasobach.*

W tym artykule pokazano, jak skonfigurować delegowanie ograniczone do protokołu Kerberos oparte na zasobach w domenie zarządzanej usług Azure AD DS.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten artykuł, potrzebne są następujące zasoby:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem tylko w chmurze.
    * W razie potrzeby [utwórz dzierżawę usługi Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z kontem.][associate-azure-ad-tenant]
* Domena zarządzana usługami domenowymi Usługi Active Directory platformy Azure włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie potrzeby [utwórz i skonfiguruj wystąpienie usług domenowych Active Directory platformy Azure][create-azure-ad-ds-instance].
* Maszyna wirtualna z zarządzaniem systemem Windows Server, która jest przyłączona do domeny zarządzanej usług Azure AD DS.
    * W razie potrzeby wykonaj samouczek, aby [utworzyć maszynę wirtualną systemu Windows Server i dołączyć ją do domeny zarządzanej,][create-join-windows-vm] a następnie [zainstaluj narzędzia do zarządzania usługami AD DS][tutorial-create-management-vm].
* Konto użytkownika, które jest członkiem grupy *administratorów usługi Azure AD DC* w dzierżawie usługi Azure AD.

## <a name="kerberos-constrained-delegation-overview"></a>Omówienie delegowania z ograniczeniami protokołu Kerberos

Delegowanie protokołu Kerberos umożliwia jedno konto personifikacji innego konta, aby uzyskać dostęp do zasobów. Na przykład aplikacja sieci web, która uzyskuje dostęp do składnika sieci web zaplecza można personifikować się jako inne konto użytkownika, gdy tworzy połączenie zaplecza. Delegowanie protokołu Kerberos jest niebezpieczne, ponieważ nie ogranicza zasobów, do które ma dostęp konto personifikujące.

Delegowanie ograniczone kerberos (KCD) ogranicza usługi lub zasoby, które określony serwer lub aplikacja może połączyć podczas personifikacji innej tożsamości. Tradycyjne KCD wymaga uprawnień administratora domeny, aby skonfigurować konto domeny dla usługi i ogranicza konto do uruchamiania w jednej domenie.

Tradycyjne KCD ma również kilka problemów. Na przykład we wcześniejszych systemach operacyjnych administrator usługi nie miał użytecznego sposobu, aby wiedzieć, które usługi front-end zostały delegowane do usług zasobów, które posiadali. Każda usługa front-end, która może delegować do usługi zasobów, była potencjalnym punktem ataku. Jeśli serwer, który hostował usługę front-end skonfigurowaną do delegowania do usług zasobów, może również zostać naruszony.

W domenie zarządzanej usług Azure AD DS nie masz uprawnień administratora domeny. W rezultacie tradycyjnych opartych na koncie KCD nie można skonfigurować w domenie zarządzanej usługi Azure AD DS. Zamiast tego można użyć kcd opartego na zasobach, który jest również bezpieczniejszy.

### <a name="resource-based-kcd"></a>KCD oparte na zasobach

System Windows Server 2012 i nowsze umożliwia administratorom usług konfigurowanie ograniczonego delegowania dla ich usługi. Ten model jest znany jako KCD oparte na zasobach. Dzięki takiemu podejściu administrator usługi zaplecza może zezwolić lub odmówić określonych usług front-end z wykorzystaniem KCD.

KCD oparte na zasobach jest konfigurowany przy użyciu programu PowerShell. Polecenia cmdlet [Set-ADComputer][Set-ADComputer] lub [Set-ADUser][Set-ADUser] są używane w zależności od tego, czy konto personifikujące jest kontem komputera, czy kontem użytkownika/kontem usługi.

## <a name="configure-resource-based-kcd-for-a-computer-account"></a>Konfigurowanie kcd opartego na zasobach dla konta komputera

W tym scenariuszu załóżmy, że masz aplikację sieci web, która działa na komputerze o nazwie *contoso-webapp.aaddscontoso.com*. Aplikacja sieci web musi uzyskać dostęp do internetowego interfejsu API, który działa na komputerze o nazwie *contoso-api.aaddscontoso.com* w kontekście użytkowników domeny. Wykonaj następujące kroki, aby skonfigurować ten scenariusz:

1. [Utwórz niestandardową instalację organizacyjną](create-ou.md). Uprawnienia do zarządzania tą niestandardową jednostką organizacyjną można delegować użytkownikom w domenie zarządzanej usług Azure AD DS.
1. [Dołącz maszyny wirtualne][create-join-windows-vm]do domeny , zarówno tej, która uruchamia aplikację sieci web, jak i tej, która uruchamia internetowy interfejs API, do domeny zarządzanej usług Azure AD DS. Utwórz te konta komputera w niestandardowej instalacji organizacyjnej z poprzedniego kroku.

    > [!NOTE]
    > Konta komputera dla aplikacji sieci web i internetowego interfejsu API muszą znajdować się w niestandardowej instalacji organizacyjnej, w której masz uprawnienia do konfigurowania kcd opartego na zasobach. Nie można skonfigurować kcd opartych na zasobach dla konta komputera we wbudowanym kontenerze *komputerów kontrolera domeny usługi AAD.*

1. Na koniec skonfiguruj kcd oparte na zasobach przy użyciu polecenia cmdlet programu PowerShell [set-ADComputer.][Set-ADComputer] Na maszynie wirtualnej do zarządzania przyłączony do domeny i zalogowany jako konto użytkownika, który jest członkiem grupy *administratorów usługi Azure AD DC,* uruchom następujące polecenia cmdlet. W razie potrzeby podaj własne nazwy komputerów:
    
    ```powershell
    $ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.aaddscontoso.com
    Set-ADComputer contoso-api.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="configure-resource-based-kcd-for-a-user-account"></a>Konfigurowanie kcd opartego na zasobach dla konta użytkownika

W tym scenariuszu załóżmy, że masz aplikację sieci web, która działa jako konto usługi o nazwie *appsvc*. Aplikacja sieci web musi uzyskać dostęp do internetowego interfejsu API, który działa jako konto usługi o nazwie *backendsvc* w kontekście użytkowników domeny. Wykonaj następujące kroki, aby skonfigurować ten scenariusz:

1. [Utwórz niestandardową instalację organizacyjną](create-ou.md). Uprawnienia do zarządzania tą niestandardową jednostką organizacyjną można delegować użytkownikom w domenie zarządzanej usług Azure AD DS.
1. [Przyłącz się do domeny do maszyn wirtualnych,][create-join-windows-vm] które uruchamiają internetowy interfejs API/zasób wewnętrznej bazy danych do domeny zarządzanej usług Azure AD DS. Utwórz swoje konto komputera w niestandardowej instalacji organizacyjnej.
1. Utwórz konto usługi (na przykład "appsvc") używane do uruchamiania aplikacji sieci web w niestandardowej usłudze organizacyjnej.

    > [!NOTE]
    > Ponownie konto komputera dla maszyny Wirtualnej interfejsu API sieci web i konta usługi dla aplikacji sieci web, musi znajdować się w niestandardowej instalacji organizacyjnej, gdzie masz uprawnienia do konfigurowania kcd opartych na zasobach. Nie można skonfigurować kcd opartych na zasobach dla kont we *wbudowanych komputerach kontrolera domeny AAD* lub w kontenerach *użytkowników kontrolera domeny AAD.* Oznacza to również, że nie można użyć kont użytkowników zsynchronizowanych z usługi Azure AD do konfigurowania kcd opartych na zasobach. Należy utworzyć i używać kont usług utworzonych specjalnie w usługach Azure AD DS.

1. Na koniec skonfiguruj kcd oparte na zasobach przy użyciu polecenia cmdlet programu PowerShell [set-ADUser.][Set-ADUser] Na maszynie wirtualnej do zarządzania przyłączony do domeny i zalogowany jako konto użytkownika, który jest członkiem grupy *administratorów usługi Azure AD DC,* uruchom następujące polecenia cmdlet. W razie potrzeby podaj własne nazwy usług:

    ```powershell
    $ImpersonatingAccount = Get-ADUser -Identity appsvc
    Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o tym, jak działa delegowanie w Usługach domenowych Active Directory, zobacz [Omówienie ograniczonego delegowania protokołu Kerberos][kcd-technet].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
[Set-ADUser]: /powershell/module/addsadministration/set-aduser

<!-- EXTERNAL LINKS -->
[kcd-technet]: https://technet.microsoft.com/library/jj553400.aspx
