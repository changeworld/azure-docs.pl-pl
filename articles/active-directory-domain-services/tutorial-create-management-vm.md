---
title: Samouczek — tworzenie maszyny Wirtualnej zarządzania dla usług domenowych Active Directory platformy Azure | Dokumenty firmy Microsoft
description: W tym samouczku dowiesz się, jak utworzyć i skonfigurować maszynę wirtualną systemu Windows używaną do administrowania wystąpieniem usług domenowych Usługi domenowe Active Directory platformy Azure.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 09fcf88c6dfe90380f387c6d72c751634f5b1606
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475734"
---
# <a name="tutorial-create-a-management-vm-to-configure-and-administer-an-azure-active-directory-domain-services-managed-domain"></a>Samouczek: tworzenie maszyny Wirtualnej zarządzania w celu konfigurowania domeny zarządzanej usług domenowych usługi active directory i administrowania nimi

Usługi domenowe Active Directory (AD DS) platformy Azure zapewniają usługi domeny zarządzanej, takie jak dołączanie do domeny, zasady grupy, LDAP i uwierzytelnianie Kerberos/NTLM, które jest w pełni zgodne z usługą Active Directory systemu Windows Server. Ta domena zarządzana jest administrowana przy użyciu tych samych narzędzi administracji serwera zdalnego (RSAT), co w lokalnej domenie Usług domenowych Active Directory. Ponieważ usługi Azure AD DS jest usługą zarządzaną, istnieją pewne zadania administracyjne, których nie można wykonać, takie jak używanie protokołu RDP (Remote desktop Protocol) do łączenia się z kontrolerami domeny.

W tym samouczku pokazano, jak utworzyć maszynę wirtualną systemu Windows Server na platformie Azure i zainstalować wymagane narzędzia do administrowania domeną zarządzaną usługą Azure AD DS.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Opis dostępnych zadań administracyjnych w domenie zarządzanej usług Azure AD DS
> * Instalowanie narzędzi administracyjnych usługi Active Directory na maszynie Wirtualnej systemu Windows Server
> * Wykonywanie typowych zadań za pomocą Centrum administracyjnego usługi Active Directory

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem tylko w chmurze.
    * W razie potrzeby [utwórz dzierżawę usługi Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z kontem.][associate-azure-ad-tenant]
* Domena zarządzana usługami domenowymi Usługi Active Directory platformy Azure włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie potrzeby zobacz pierwszy samouczek, aby [utworzyć i skonfigurować wystąpienie usług domenowych Usługi domenowe Active Directory platformy Azure][create-azure-ad-ds-instance].
* Maszyna wirtualna systemu Windows Server, która jest przyłączona do domeny zarządzanej usług Azure AD DS.
    * W razie potrzeby zobacz poprzedni samouczek, aby [utworzyć maszynę wirtualną systemu Windows Server i dołączyć ją do domeny zarządzanej][create-join-windows-vm].
* Konto użytkownika, które jest członkiem grupy *administratorów usługi Azure AD DC* w dzierżawie usługi Azure AD.
* Host bastionu platformy Azure wdrożony w sieci wirtualnej usług Ad DS platformy Azure.
    * W razie potrzeby [utwórz hosta Bastionu Platformy Azure][azure-bastion].

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

W tym samouczku można utworzyć i skonfigurować maszynę wirtualną zarządzania przy użyciu witryny Azure portal. Aby rozpocząć, najpierw zaloguj się do [witryny Azure portal](https://portal.azure.com).

## <a name="available-administrative-tasks-in-azure-ad-ds"></a>Dostępne zadania administracyjne w usługach Azure AD DS

Usługa Azure AD DS udostępnia domenę zarządzaną dla użytkowników, aplikacji i usług do wykorzystania. To podejście zmienia niektóre dostępne zadania zarządzania, które można wykonać, oraz uprawnienia, które masz w domenie zarządzanej. Te zadania i uprawnienia mogą być inne niż te, które występują w zwykłym lokalnym środowisku usług domenowych Active Directory. Nie można również połączyć się z kontrolerami domeny w domenie zarządzanej usług Azure AD DS przy użyciu pulpitu zdalnego.

### <a name="administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain"></a>Zadania administracyjne, które można wykonywać w domenie zarządzanej usług Ad DS

Członkowie grupy *Administratorzy kontrolera domeny usługi AAD* mają uprawnienia do domeny zarządzanej usług Azure AD DS, która umożliwia im wykonywanie zadań, takich jak:

* Skonfiguruj wbudowany obiekt zasad grupy (GPO) dla *kontenerów komputerów AADDC* i *użytkowników usługi AADDC* w domenie zarządzanej.
* Administrowanie systemem DNS w domenie zarządzanej.
* Tworzenie niestandardowych jednostek organizacyjnych (OU) w domenie zarządzanej i administrowanie nimi.
* Uzyskiwanie dostępu administracyjnego do komputerów przyłączonych do domeny zarządzanej.

### <a name="administrative-privileges-you-dont-have-on-an-azure-ad-ds-managed-domain"></a>Uprawnienia administracyjne, których nie masz w domenie zarządzanej usług Azure AD DS

Domena zarządzana usługą Azure AD DS jest zablokowana, więc nie masz uprawnień do wykonywania niektórych zadań administracyjnych w domenie. Oto zadania, których nie można wykonać:

* Rozszerz schemat domeny zarządzanej.
* Łączenie się z kontrolerami domeny dla domeny zarządzanej przy użyciu pulpitu zdalnego.
* Dodawanie kontrolerów domeny do domeny zarządzanej.
* Nie masz uprawnień *Administratora domeny* ani *Administratora przedsiębiorstwa* dla domeny zarządzanej.

## <a name="sign-in-to-the-windows-server-vm"></a>Logowanie się do maszyny Wirtualnej systemu Windows Server

W poprzednim samouczku maszyna wirtualna systemu Windows Server została utworzona i przyłączona do domeny zarządzanej usług Azure AD DS. Użyjmy tej maszyny Wirtualnej do zainstalowania narzędzi do zarządzania. W razie potrzeby [wykonaj kroki opisane w samouczku, aby utworzyć maszynę wirtualną systemu Windows Server i dołączyć go do domeny zarządzanej][create-join-windows-vm].

> [!NOTE]
> W tym samouczku używasz maszyny Wirtualnej systemu Windows Server na platformie Azure, która jest przyłączona do domeny zarządzanej usług Azure AD DS. Można również użyć klienta systemu Windows, takiego jak Windows 10, który jest przyłączony do domeny zarządzanej.
>
> Aby uzyskać więcej informacji na temat instalowania narzędzi administracyjnych na kliencie systemu Windows, zobacz [instalowanie narzędzi administracji zdalnej (RSAT)](https://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx)

Aby rozpocząć, połącz się z maszyną wirtualną systemu Windows Server w następujący sposób:

1. W witrynie Azure portal wybierz **grup zasobów** po lewej stronie. Wybierz grupę zasobów, w której utworzono maszynę wirtualną, taką jak *myResourceGroup,* a następnie wybierz maszynę wirtualną, taką jak *myVM*.
1. W okienku **Przegląd** maszyny Wirtualnej wybierz pozycję **Połącz**, a następnie **bastion**.

    ![Łączenie się z maszyną wirtualną systemu Windows przy użyciu bastionu w witrynie Azure portal](./media/join-windows-vm/connect-to-vm.png)

1. Wprowadź poświadczenia maszyny Wirtualnej, a następnie wybierz pozycję **Połącz**.

   ![Łączenie się za pośrednictwem hosta bastionu w witrynie Azure portal](./media/join-windows-vm/connect-to-bastion.png)

W razie potrzeby zezwól przeglądarce internetowej na otwieranie wyskakujących okienków dla połączenia Bastion, które ma być wyświetlane. Nawiązanie połączenia z maszyną wirtualną zajmuje kilka sekund.

## <a name="install-active-directory-administrative-tools"></a>Instalowanie narzędzi administracyjnych usługi Active Directory

Domeny zarządzane przez usługi Azure AD DS są zarządzane przy użyciu tych samych narzędzi administracyjnych, co lokalne środowiska usług AD DS, takie jak Centrum administracyjne usługi Active Directory (ADAC) lub program AD PowerShell. Narzędzia te można zainstalować jako część funkcji Narzędzia administracji zdalnej serwera (RSAT) na komputerach z systemem Windows Server i na komputerach klienckich. Członkowie grupy *Administratorzy kontrolera domeny kontrolera domeny usługi AAD* mogą następnie zdalnie administrować domenami zarządzanymi usług Azure AD DS przy użyciu tych narzędzi administracyjnych usługi AD z komputera przyłączanego do domeny zarządzanej.

Aby zainstalować narzędzia administracji usługi Active Directory na maszynie wirtualnej przyłączone do domeny, wykonaj następujące czynności:

1. Jeśli **Menedżer serwera** nie otwiera się domyślnie po zalogowaniu się do maszyny Wirtualnej, wybierz menu **Start,** a następnie wybierz pozycję **Menedżer serwera**.
1. W okienku *Pulpit nawigacyjny* okna **Menedżer serwera** wybierz pozycję Dodaj role **i funkcje**.
1. Na stronie **Przed rozpoczęciem** *Kreatora dodawania ról i funkcji*wybierz pozycję **Dalej**.
1. W przypadku *typu instalacji*pozostaw zaznaczoną opcję **instalacji opartą na rolach lub funkcjach** i wybierz pozycję **Dalej**.
1. Na stronie **Wybór serwera** wybierz bieżącą maszynę wirtualną z puli serwerów, taką jak *myvm.aaddscontoso.com*, a następnie wybierz pozycję **Dalej**.
1. Na stronie **Role serwera** kliknij przycisk **Dalej**.
1. Na stronie **Funkcje** rozwiń węzeł **Narzędzia administracji zdalnej serwera,** a następnie rozwiń węzeł **Narzędzia administrowania rolami.**

    Z listy narzędzi administracyjnych ról wybierz pozycję **Ad DS i Ad LDS,** a następnie wybierz pozycję **Dalej**.

    ![Instalowanie "Narzędzi usług AD DS i ad lds" na stronie Funkcje](./media/tutorial-create-management-vm/install-features.png)

1. Na stronie **Potwierdzenie** wybierz pozycję **Zainstaluj**. Zainstalowanie narzędzi administracyjnych może potrwać minutę lub dwie.
1. Po zakończeniu instalacji funkcji wybierz pozycję **Zamknij,** aby zakończyć pracę **kreatora Dodawanie ról i funkcji.**

## <a name="use-active-directory-administrative-tools"></a>Korzystanie z narzędzi administracyjnych usługi Active Directory

Po zainstalowaniu narzędzi administracyjnych zobaczmy, jak używać ich do administrowania domeną zarządzana usługą Azure AD DS. Upewnij się, że zalogowano się do maszyny Wirtualnej przy za pomocą konta użytkownika, które jest członkiem grupy *Administratorzy kontrolera domeny usługi AAD.*

1. Z menu **Start** wybierz polecenie **Narzędzia administracyjne systemu Windows**. Zostaną wyświetlone narzędzia administracyjne usługi AD zainstalowane w poprzednim kroku.

    ![Lista narzędzi administracyjnych zainstalowanych na serwerze](./media/tutorial-create-management-vm/list-admin-tools.png)

1. Wybierz **Centrum administracyjne usługi Active Directory**.
1. Aby eksplorować domenę zarządzana usługą Azure AD DS, wybierz nazwę domeny w lewym okienku, na przykład *aaddscontoso.com*. Dwa kontenery o nazwie *Komputery AADDC* i *użytkownicy AADDC* znajdują się na górze listy.

    ![Wyświetlanie listy dostępnych kontenerów należących do domeny zarządzanej usług Azure AD DS](./media/tutorial-create-management-vm/active-directory-administrative-center.png)

1. Aby wyświetlić użytkowników i grupy należące do domeny zarządzanej usługi Azure AD DS, wybierz kontener **Użytkownicy usługi AADDC.** Konta użytkowników i grupy z dzierżawy usługi Azure AD są wymienione w tym kontenerze.

    W poniższym przykładzie danych wyjściowych w tym kontenerze wyświetlane jest konto użytkownika o nazwie *Contoso Admin* i grupa *administratorów kontrolera domeny usługi AAD.*

    ![Wyświetlanie listy użytkowników domeny usług Ad DS w Centrum administracyjnym usługi Active Directory](./media/tutorial-create-management-vm/list-azure-ad-users.png)

1. Aby wyświetlić komputery, które są przyłączone do domeny zarządzanej usługi Azure AD DS, wybierz kontener **Komputery usługi AADDC.** Wpis dla bieżącej maszyny wirtualnej, takiej jak *myVM,* znajduje się na liście. Konta komputerów dla wszystkich komputerów, które są przyłączone do domeny zarządzanej usługi Azure AD DS są przechowywane w tym kontenerze *komputerów AADDC.*

Dostępne są typowe akcje Centrum administracyjnego usługi Active Directory, takie jak resetowanie hasła do konta użytkownika lub zarządzanie członkostwem w grupie. Te akcje działają tylko dla użytkowników i grup utworzonych bezpośrednio w domenie zarządzanej usług Azure AD DS. Informacje o tożsamości synchronizuje tylko *z* usługi Azure AD do usług Azure AD DS. Nie ma żadnych odpisów z usług Azure AD DS do usługi Azure AD. Nie można zmienić haseł ani członkostwa w grupie zarządzanej dla użytkowników zsynchronizowanych z usługi Azure AD i zsynchronizować te zmiany.

Modułu usługi *Active Directory dla programu Windows PowerShell*, zainstalowanego jako część narzędzi administracyjnych, można również użyć do zarządzania typowymi akcjami w domenie zarządzanej usług Azure AD DS.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Opis dostępnych zadań administracyjnych w domenie zarządzanej usług Azure AD DS
> * Instalowanie narzędzi administracyjnych usługi Active Directory na maszynie Wirtualnej systemu Windows Server
> * Wykonywanie typowych zadań za pomocą Centrum administracyjnego usługi Active Directory

Aby bezpiecznie współdziałać z domeną zarządzana usługą Azure AD DS, włącz bezpieczny protokół LDAPS (Lightweight Directory Access Protocol).

> [!div class="nextstepaction"]
> [Konfigurowanie bezpiecznego protokołu LDAP dla domeny zarządzanej](tutorial-configure-ldaps.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
