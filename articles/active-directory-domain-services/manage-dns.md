---
title: Zarządzanie usługami domenowymi usługi USŁUGI AZURE AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak zainstalować narzędzia serwera DNS do zarządzania systemem DNS dla domeny zarządzanej usług domenowych Usługi domenowe Active Directory azure.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: iainfou
ms.openlocfilehash: 2694a5f250b746748a1b42ac4d211aa28ef1ebad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613695"
---
# <a name="administer-dns-in-an-azure-ad-domain-services-managed-domain"></a>Administrowanie systemem DNS w domenie zarządzanej usług domenowych usługi azure AD

W usługach domenowych Usługi active directory platformy Azure (Azure AD DS) kluczowym składnikiem jest DNS (Domain Name Resolution). Usługi Azure AD DS zawiera serwer DNS, który zapewnia rozpoznawanie nazw dla domeny zarządzanej. Ten serwer DNS zawiera wbudowane rekordy DNS i aktualizacje dla kluczowych składników, które umożliwiają uruchomienie usługi.

Podczas uruchamiania własnych aplikacji i usług może być konieczne utworzenie rekordów DNS dla maszyn, które nie są przyłączone do domeny, skonfigurowanie wirtualnych adresów IP dla modułów równoważenia obciążenia lub skonfigurowanie zewnętrznych usług przesyłania dalej DNS. Użytkownicy należący do grupy *Administratorzy kontrolera domeny kontrolera domeny usługi AAD* otrzymują uprawnienia administracyjne DNS w domenie zarządzanej usług Azure AD DS i mogą tworzyć i edytować niestandardowe rekordy DNS.

W środowisku hybrydowym strefy i rekordy DNS skonfigurowane w lokalnym środowisku usług AD DS nie są synchronizowane z usługą Azure AD DS. Aby zdefiniować i używać własnych wpisów DNS, należy utworzyć rekordy na serwerze DNS usług Azure AD DS lub użyć usług przesyłania dalej warunkowego, które wskazują istniejące serwery DNS w twoim środowisku.

W tym artykule pokazano, jak zainstalować narzędzia serwera DNS, a następnie użyć konsoli DNS do zarządzania rekordami w usługach Azure AD DS.

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
    * W razie potrzeby wykonaj samouczek, aby [utworzyć maszynę wirtualną systemu Windows Server i dołączyć ją do domeny zarządzanej][create-join-windows-vm].
* Konto użytkownika, które jest członkiem grupy *administratorów usługi Azure AD DC* w dzierżawie usługi Azure AD.

## <a name="install-dns-server-tools"></a>Instalowanie narzędzi serwera DNS

Aby tworzyć i modyfikować rekordy DNS w usługach Azure AD DS, należy zainstalować narzędzia serwera DNS. Narzędzia te można zainstalować jako funkcję w systemie Windows Server. Aby uzyskać więcej informacji na temat instalowania narzędzi administracyjnych na kliencie systemu Windows, zobacz [instalowanie narzędzi administracji zdalnej (RSAT)][install-rsat].

1. Zaloguj się do maszyny wirtualnej zarządzania. Aby uzyskać instrukcje dotyczące łączenia się przy użyciu portalu Azure portal, zobacz [Łączenie się z maszyną wirtualną systemu Windows Server][connect-windows-server-vm].
1. Jeśli **Menedżer serwera** nie otwiera się domyślnie po zalogowaniu się do maszyny Wirtualnej, wybierz menu **Start,** a następnie wybierz pozycję **Menedżer serwera**.
1. W okienku *Pulpit nawigacyjny* okna **Menedżer serwera** wybierz pozycję Dodaj role **i funkcje**.
1. Na stronie **Przed rozpoczęciem** *Kreatora dodawania ról i funkcji*wybierz pozycję **Dalej**.
1. W przypadku *typu instalacji*pozostaw zaznaczoną opcję **instalacji opartą na rolach lub funkcjach** i wybierz pozycję **Dalej**.
1. Na stronie **Wybór serwera** wybierz bieżącą maszynę wirtualną z puli serwerów, taką jak *myvm.aaddscontoso.com*, a następnie wybierz pozycję **Dalej**.
1. Na stronie **Role serwera** kliknij przycisk **Dalej**.
1. Na stronie **Funkcje** rozwiń węzeł **Narzędzia administracji zdalnej serwera,** a następnie rozwiń węzeł **Narzędzia administrowania rolami.** Wybierz funkcję **Narzędzia serwera DNS** z listy narzędzi do administrowania rolami.

    ![Aby zainstalować narzędzia serwera DNS z listy dostępnych narzędzi administracyjnych ról](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)

1. Na stronie **Potwierdzenie** wybierz pozycję **Zainstaluj**. Zainstalowanie narzędzi zarządzania zasadami grupy może potrwać minutę lub dwie.
1. Po zakończeniu instalacji funkcji wybierz pozycję **Zamknij,** aby zakończyć pracę **kreatora Dodawanie ról i funkcji.**

## <a name="open-the-dns-management-console-to-administer-dns"></a>Otwórz konsolę zarządzania DNS, aby administrować systemem DNS

Po zainstalowaniu narzędzi serwera DNS można administrować rekordami DNS w domenie zarządzanej usług Azure AD DS.

> [!NOTE]
> Aby administrować usługą DNS w domenie zarządzanej usług Azure AD DS, musisz zalogować się do konta użytkownika, które jest członkiem grupy *Administratorzy kontrolera domeny kontrolera domeny usługi AAD.*

1. Na ekranie startowym wybierz pozycję **Narzędzia administracyjne**. Zostanie wyświetlona lista dostępnych narzędzi do zarządzania, w tym **system DNS** zainstalowany w poprzedniej sekcji. Wybierz **pozycję DNS,** aby uruchomić konsolę Zarządzania DNS.
1. W oknie dialogowym **Połącz z serwerem DNS** wybierz pozycję **Następujący komputer**, a następnie wprowadź nazwę domeny DNS domeny zarządzanej, taką jak *aaddscontoso.com:*

    ![Łączenie się z domeną zarządzałą usługą Azure AD DS w konsoli DNS](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)

1. Konsola DNS łączy się z określoną domeną zarządzana usługą Azure AD DS. Rozwiń **strefy wyszukiwania do przodu** lub strefy **odnośnego wstecznego,** aby utworzyć wymagane wpisy DNS lub w razie potrzeby edytować istniejące rekordy.

    ![Konsola DNS — administrowanie domeną](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)

> [!WARNING]
> Podczas zarządzania rekordami przy użyciu narzędzi serwera DNS upewnij się, że nie usuwasz ani nie modyfikujesz wbudowanych rekordów DNS używanych przez usługi Azure AD DS. Wbudowane rekordy DNS obejmują rekordy DNS domeny, rekordy serwera nazw i inne rekordy używane dla lokalizacji kontrolera domeny. Jeśli zmodyfikujesz te rekordy, usługi domeny zostaną zakłócone w sieci wirtualnej.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zarządzania systemem DNS, zobacz [artykuł o narzędziach DNS w serwisie Technet](https://technet.microsoft.com/library/cc753579.aspx).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
