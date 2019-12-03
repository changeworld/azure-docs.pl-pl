---
title: Zarządzanie usługą DNS dla Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, jak zainstalować narzędzia serwera DNS w celu zarządzania systemem DNS w Azure Active Directory Domain Services domenie zarządzanej.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: iainfou
ms.openlocfilehash: f47f112991a4fe2e5b245920db98e5ae7617161a
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74704929"
---
# <a name="administer-dns-in-an-azure-ad-domain-services-managed-domain"></a>Administrowanie systemem DNS w Azure AD Domain Services domenie zarządzanej

W Azure Active Directory Domain Services (Azure AD DS), składnik klucza to DNS (rozpoznawanie nazw domen). Usługa Azure AD DS obejmuje serwer DNS, który zapewnia rozpoznawanie nazw dla domeny zarządzanej. Ten serwer DNS zawiera wbudowane rekordy DNS i aktualizacje dla kluczowych składników, które umożliwiają uruchomienie usługi.

Podczas uruchamiania własnych aplikacji i usług może być konieczne utworzenie rekordów DNS dla komputerów, które nie są przyłączone do domeny, skonfigurować wirtualne adresy IP dla modułów równoważenia obciążenia lub skonfigurować zewnętrzne usługi przesyłania dalej DNS. Użytkownicy, którzy należą do grupy *administratorów DC usługi AAD* , mają uprawnienia do administrowania systemem DNS w domenie zarządzanej usługi Azure AD DS i mogą tworzyć i edytować niestandardowe rekordy DNS.

W środowisku hybrydowym strefy i rekordy DNS skonfigurowane w środowisku lokalnym AD DS nie są synchronizowane z usługą Azure AD DS. Aby zdefiniować i użyć własnych wpisów DNS, należy utworzyć rekordy na serwerze DNS usługi Azure AD DS lub użyć usług przesyłania dalej warunkowego, które wskazują istniejące serwery DNS w danym środowisku.

W tym artykule opisano sposób instalowania narzędzi serwera DNS, a następnie używania konsoli DNS do zarządzania rekordami w usłudze Azure AD DS.

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
    * W razie potrzeby uzupełnij ten samouczek, aby [utworzyć maszynę wirtualną z systemem Windows Server i dołączyć ją do domeny zarządzanej][create-join-windows-vm].
* Konto użytkownika, które jest członkiem grupy *administratorów DC usługi Azure AD* w dzierżawie usługi Azure AD.

## <a name="install-dns-server-tools"></a>Instalowanie narzędzi serwera DNS

Aby tworzyć i modyfikować rekordy DNS w usłudze Azure AD DS, należy zainstalować narzędzia serwera DNS. Te narzędzia można zainstalować jako funkcję systemu Windows Server. Aby uzyskać więcej informacji na temat instalowania narzędzi administracyjnych na kliencie systemu Windows, zobacz Install [Narzędzia administracji zdalnej serwera (RSAT)][install-rsat].

1. Zaloguj się do maszyny wirtualnej zarządzania. Aby uzyskać instrukcje dotyczące sposobu nawiązywania połączenia przy użyciu Azure Portal, zobacz [nawiązywanie połączenia z maszyną wirtualną z systemem Windows Server][connect-windows-server-vm].
1. Jeśli **Menedżer serwera** nie zostanie otwarta domyślnie po zalogowaniu się do maszyny wirtualnej, wybierz menu **Start** , a następnie wybierz pozycję **Menedżer serwera**.
1. W okienku *pulpit nawigacyjny* okna **Menedżer serwera** wybierz pozycję **Dodaj role i funkcje**.
1. Na stronie **zanim rozpoczniesz** *Kreatora dodawania ról i funkcji*wybierz pozycję **dalej**.
1. W polu *Typ instalacji*pozostaw zaznaczoną opcję **Instalacja oparta na rolach lub oparta na funkcjach** , a następnie wybierz pozycję **dalej**.
1. Na stronie **Wybór serwera** wybierz bieżącą maszynę wirtualną z puli serwerów, takiej jak *MyVM.aadds.contoso.com*, a następnie wybierz przycisk **dalej**.
1. Na stronie **role serwera** kliknij przycisk **dalej**.
1. Na stronie **funkcje** rozwiń węzeł **Narzędzia administracji zdalnej serwera** , a następnie rozwiń węzeł Narzędzia do **administrowania rolami** . Wybierz funkcję **Narzędzia serwera DNS** z listy narzędzi do administrowania rolami.

    ![Wybierz, aby zainstalować narzędzia serwera DNS z listy dostępnych narzędzi do administrowania rolami](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)

1. Na stronie **potwierdzenie** wybierz pozycję **Zainstaluj**. Zainstalowanie zasady grupy narzędzi do zarządzania może potrwać minutę lub dwa.
1. Po zakończeniu instalacji funkcji wybierz pozycję **Zamknij** , aby zakończyć działanie kreatora **dodawania ról i funkcji** .

## <a name="open-the-dns-management-console-to-administer-dns"></a>Otwórz konsolę zarządzania DNS w celu administrowania usługą DNS

Za pomocą zainstalowanych narzędzi serwera DNS można administrować rekordami DNS w domenie zarządzanej AD DS platformy Azure.

> [!NOTE]
> Aby administrować systemem DNS w domenie zarządzanej AD DS platformy Azure, musisz zalogować się na konto użytkownika, które jest członkiem grupy *administratorów kontrolera domeny usługi AAD* .

1. Na ekranie startowym wybierz pozycję **Narzędzia administracyjne**. Zostanie wyświetlona lista dostępnych narzędzi do zarządzania, w tym **usługi DNS** zainstalowane w poprzedniej sekcji. Wybierz pozycję **DNS** , aby uruchomić konsolę zarządzania DNS.
1. W oknie dialogowym **łączenie z serwerem DNS** wybierz **następujący komputer**, a następnie wprowadź nazwę domeny DNS domeny zarządzanej, taką jak *aadds.contoso.com*:

    ![Nawiązywanie połączenia z domeną zarządzaną platformy Azure AD DS w konsoli DNS](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)

1. Konsola DNS nawiązuje połączenie z określoną domeną zarządzaną AD DS platformy Azure. Rozwiń **strefy wyszukiwania do przodu** lub **strefy wyszukiwania wstecznego** , aby utworzyć wymagane wpisy DNS, lub edytuj istniejące rekordy stosownie do potrzeb.

    ![Konsola DNS-Administruj domeną](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)

> [!WARNING]
> Podczas zarządzania rekordami przy użyciu narzędzi serwera DNS upewnij się, że nie usunięto ani nie modyfikujesz wbudowanych rekordów DNS, które są używane przez usługę Azure AD DS. Wbudowane rekordy DNS obejmują rekordy DNS domeny, rekordy serwera nazw i inne rekordy używane do lokalizacji kontrolera domeny. W przypadku zmodyfikowania tych rekordów usługi domenowe są zakłócone w sieci wirtualnej.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zarządzania usługą DNS, zobacz [artykuł dotyczący narzędzi DNS w witrynie TechNet](https://technet.microsoft.com/library/cc753579.aspx).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
