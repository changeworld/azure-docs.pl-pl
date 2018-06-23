---
title: 'Azure Active Directory Domain Services: Administrowanie DNS w domenach zarządzanych | Dokumentacja firmy Microsoft'
description: Administrowanie systemem DNS w domenach zarządzanych usług domenowych Azure Active Directory
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: d8559df366bdd9c1439f2ff8c7b7ebc1a7c66960
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36334194"
---
# <a name="administer-dns-on-an-azure-ad-domain-services-managed-domain"></a>Administrowanie systemem DNS w domenie zarządzanej usług domenowych Azure AD
Azure Active Directory Domain Services zawiera serwer DNS (rozpoznawanie nazw domen), który udostępnia rozpoznawanie nazw DNS dla domeny zarządzanej. Czasami może być konieczne skonfigurowanie DNS dla domeny zarządzanej. Może być konieczne, Utwórz rekordy DNS dla komputerów, które nie są przyłączone do domeny, skonfigurować wirtualne adresy IP usługi równoważenia obciążenia lub skonfigurować zewnętrznego usług przesyłania dalej DNS. Z tego powodu użytkownicy, którzy należą do grupy "Administratorzy kontrolera domeny usługi AAD" są przyznawane uprawnienia administracyjne DNS domeny zarządzanej.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby wykonać zadania opisane w tym artykule, należy:

1. Prawidłowy **subskrypcji platformy Azure**.
2. **Katalog usługi Azure AD** -albo synchronizowane z katalogu lokalnego lub w katalogu tylko w chmurze.
3. **Usługi domenowe Azure AD** musi być włączona dla katalogu usługi Azure AD. Jeśli nie zostało to jeszcze zrobione, należy wykonać wszystkie zadania opisane w temacie [Przewodnik wprowadzający](active-directory-ds-getting-started.md).
4. A **przyłączonych do domeny maszyny wirtualnej** z którego administrujesz domeny zarządzanej usług domenowych Azure AD. Jeśli nie masz maszynę wirtualną, należy wykonać wszystkie zadania opisane w artykule zatytułowany [Dołącz maszynę wirtualną systemu Windows do domeny zarządzanej](active-directory-ds-admin-guide-join-windows-vm.md).
5. Wymagane poświadczenia **konta użytkownika należącego do grupy "Administratorzy kontrolera domeny usługi AAD"** w katalogu, do administrowania systemem DNS dla domeny zarządzanej.

<br>

## <a name="task-1---create-a-domain-joined-virtual-machine-to-remotely-administer-dns-for-the-managed-domain"></a>Zadanie 1 — Utwórz maszynę wirtualną przyłączonych do domeny, aby zdalnie administrować DNS dla domeny zarządzanej
Azure domen zarządzanych w usługach domenowych AD można zarządzać zdalnie za pomocą znanych narzędzi administracyjnych usługi Active Directory takie jak Centrum administracyjne Active Directory (ADAC) lub AD PowerShell. Podobnie systemu DNS dla domeny zarządzanej można administrować zdalnie za pomocą narzędzia administracji serwera DNS.

Administratorzy w katalogu usługi Azure AD nie masz uprawnień do połączenia się z kontrolerami domeny w domenie zarządzanych za pomocą pulpitu zdalnego. Członkowie grupy "Administratorzy kontrolera domeny usługi AAD" można administrować DNS dla domen zarządzanych zdalnie przy użyciu narzędzia serwera DNS z komputera klienta serwera systemu Windows, który jest przyłączony do domeny zarządzanej. Narzędzia serwera DNS są częścią opcjonalna funkcja narzędzi administracji zdalnej serwera (RSAT).

Pierwszym zadaniem jest Utwórz maszynę wirtualną systemu Windows Server, który jest przyłączony do domeny zarządzanej. Aby uzyskać instrukcje, zobacz artykuł zatytułowany [Dołącz maszynę wirtualną systemu Windows Server do domeny zarządzanej usług domenowych Azure AD](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-dns-server-tools-on-the-virtual-machine"></a>Zadanie 2 — narzędzia do instalacji serwera DNS na maszynie wirtualnej
Wykonaj poniższe kroki, aby zainstalować narzędzia administracji DNS na maszynie wirtualnej przyłączony do domeny. Aby uzyskać więcej informacji na temat [instalowaniu i używaniu narzędzi administracji zdalnej serwera](https://technet.microsoft.com/library/hh831501.aspx), zobacz temat Technet.

1. Przejdź do portalu Azure. Kliknij przycisk **wszystkie zasoby** w lewym panelu. Zlokalizuj i kliknij utworzony w zadaniu 1 maszynę wirtualną.
2. Kliknij przycisk **Connect** przycisk na karcie Przegląd. Plik protokołu Remote Desktop Protocol (RDP) jest utworzony i pobrane.

    ![Połącz z maszyną wirtualną systemu Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Aby połączyć się z maszyną wirtualną, otwórz pobrany plik RDP. Jeśli zostanie wyświetlony monit, kliknij przycisk **Połącz**. Użyj poświadczeń użytkownika należącego do grupy "Administratorzy usługi AAD kontrolera domeny". Na przykład "bob@domainservicespreview.onmicrosoft.com". Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Kliknij przycisk Tak, czy kontynuować nawiązywania połączenia.

4. Na ekranie startowym Otwórz **Menedżera serwera**. Kliknij przycisk **Dodaj role i funkcje** w okienku centralnym okna Menedżera serwera.

    ![Uruchom Menedżera serwera na maszynie wirtualnej](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. Na **przed rozpoczęciem** strony **Kreatora dodawania ról i funkcji**, kliknij przycisk **dalej**.

    ![Przed rozpoczęciem strony](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. Na **typu instalacji** zostaw **Instalacja roli lub funkcji** zaznaczoną opcją i kliknij przycisk **dalej**.

    ![Strona Typ instalacji](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. Na **wybór serwera** strony, wybierz bieżącej maszyny wirtualnej z puli serwerów, a następnie kliknij przycisk **dalej**.

    ![Strona wybierania serwera](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. Na **ról serwera** kliknij przycisk **dalej**.
9. Na **funkcje** strony, kliknij, aby rozwinąć **narzędzi administracji zdalnej serwera** węzeł, a następnie kliknij przycisk aby rozwinąć **narzędzia do administrowania rolami** węzła. Wybierz **narzędzia serwera DNS** funkcję z listy narzędzia do administrowania rolami.

    ![Strona funkcji](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)
10. Na **potwierdzenie** kliknij przycisk **zainstalować** do zainstalowania funkcji narzędzia serwera DNS na maszynie wirtualnej. Po pomyślnym zakończeniu instalacji funkcji, kliknij przycisk **Zamknij** aby zakończyć **Dodaj role i funkcje** kreatora.

    ![Strona potwierdzenia](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-confirmation.png)

## <a name="task-3---launch-the-dns-management-console-to-administer-dns"></a>Zadanie 3 — Uruchom konsolę zarządzania DNS do administrowania systemem DNS
Teraz służy narzędzia DNS systemu Windows Server do administrowania systemem DNS dla domeny zarządzanej.

> [!NOTE]
> Musisz być członkiem grupy "Administratorzy kontrolera domeny usługi AAD", do administrowania systemem DNS dla domeny zarządzanej.
>
>

1. Na ekranie startowym kliknij **narzędzia administracyjne**. Powinny pojawić się **DNS** Konsola zainstalowana na maszynie wirtualnej.

    ![Narzędzia administracyjne — konsoli DNS](./media/active-directory-domain-services-admin-guide/install-rsat-dns-tools-installed.png)
2. Kliknij przycisk **DNS** można uruchomić konsoli zarządzania DNS.
3. W **Połącz z serwerem DNS** okna dialogowego, kliknij przycisk **następującego komputera**, a następnie wprowadź nazwę domeny DNS domeny zarządzanej (na przykład "contoso100.com").

    ![Konsolę DNS - Połącz się z domeną](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)
4. Konsolę DNS łączy do domeny zarządzanej.

    ![Konsolę DNS - administrowania domeny](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)
5. Można teraz używać konsoli DNS można dodać wpisów DNS dla komputerów w sieci wirtualnej, w której włączono usługi domenowe w usłudze AAD.

> [!WARNING]
> Należy zachować ostrożność podczas administrowania DNS dla domeny zarządzanej przy użyciu narzędzi administracyjnych DNS. Upewnij się, że **nie należy usuwać ani modyfikować wbudowanych rekordy DNS, które są używane przez usługi domenowe w domenie**. Wbudowane rekordy DNS obejmują rekordów DNS domeny, rekordy serwera nazw i inne rekordy lokalizacji kontrolera domeny. Jeśli zmodyfikujesz te rekordy, usług domenowych w usłudze są zakłócone w sieci wirtualnej.
>
>

Aby uzyskać więcej informacji o zarządzaniu DNS, zobacz [narzędzia DNS artykuł w witrynie Technet](https://technet.microsoft.com/library/cc753579.aspx).

## <a name="related-content"></a>Powiązana zawartość
* [Usługi domenowe AD Azure - Przewodnik wprowadzający](active-directory-ds-getting-started.md)
* [Dołącz maszynę wirtualną systemu Windows Server do domeny zarządzanej usług domenowych Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Administrowanie domeną zarządzaną usług Azure AD Domain Services](active-directory-ds-admin-guide-administer-domain.md)
* [Narzędzia administracji systemu DNS](https://technet.microsoft.com/library/cc753579.aspx)
