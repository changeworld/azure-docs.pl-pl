---
title: 'Azure Active Directory Domain Services: Administrowanie zasad grupy w domenach zarządzanych | Dokumentacja firmy Microsoft'
description: Domeny zarządzane przez zasady grupy Administruj w usługach domenowych Azure Active Directory
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maheshu
ms.openlocfilehash: d9c4e33ffdb95d81660a92e0f43bac36702882a3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34587008"
---
# <a name="administer-group-policy-on-an-azure-ad-domain-services-managed-domain"></a>Administrowanie zasad grupy w domenie zarządzanej usług domenowych Azure AD
Azure Active Directory Domain Services zawiera wbudowane obiekty zasad grupy (GPO) dla kontenerów "Użytkownicy AADDC" i "Komputery AADDC". Można dostosować te wbudowane obiekty zasad grupy do konfigurowania zasad grupy w domenie zarządzanej. Ponadto członkowie grupy "Administratorzy kontrolera domeny usługi AAD" mogą tworzyć własne niestandardowe jednostek organizacyjnych w domenie zarządzanej. Mogą także tworzyć niestandardowe obiekty zasad grupy oraz łączenia ich z tych niestandardowych jednostek organizacyjnych. Użytkownicy, którzy należą do grupy "Administratorzy kontrolera domeny usługi AAD" udzielono uprawnień administracyjnych zasad grupy do domeny zarządzanej.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby wykonać zadania opisane w tym artykule, należy:

1. Prawidłowy **subskrypcji platformy Azure**.
2. **Katalog usługi Azure AD** -albo synchronizowane z katalogu lokalnego lub w katalogu tylko w chmurze.
3. **Usługi domenowe Azure AD** musi być włączona dla katalogu usługi Azure AD. Jeśli nie zostało to jeszcze zrobione, należy wykonać wszystkie zadania opisane w temacie [Przewodnik wprowadzający](active-directory-ds-getting-started.md).
4. A **przyłączonych do domeny maszyny wirtualnej** z którego administrujesz domeny zarządzanej usług domenowych Azure AD. Jeśli nie masz maszynę wirtualną, należy wykonać wszystkie zadania opisane w artykule zatytułowany [Dołącz maszynę wirtualną systemu Windows do domeny zarządzanej](active-directory-ds-admin-guide-join-windows-vm.md).
5. Wymagane poświadczenia **konta użytkownika należącego do grupy "Administratorzy kontrolera domeny usługi AAD"** w katalogu, do administrowania zasad grupy dla domeny zarządzanej.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-group-policy-for-the-managed-domain"></a>Zadanie 1 — Aprowizowanie maszyny wirtualnej z domeny w celu zdalnego administrowania zasad grupy dla domeny zarządzanej
Azure domen zarządzanych w usługach domenowych AD można zarządzać zdalnie za pomocą znanych narzędzi administracyjnych usługi Active Directory takie jak Centrum administracyjne Active Directory (ADAC) lub AD PowerShell. Podobnie zasady grupy dla domeny zarządzanej można administrować zdalnie przy użyciu narzędzi administracyjnych zasad grupy.

Administratorzy w katalogu usługi Azure AD nie masz uprawnień do połączenia się z kontrolerami domeny w domenie zarządzanych za pomocą pulpitu zdalnego. Członkowie grupy "Administratorzy kontrolera domeny usługi AAD" można zasad grupy dla domen zarządzanych zdalnie administrować. Mogą oni za pomocą narzędzi zasad grupy na komputerze klienta serwera systemu Windows przyłączone do domeny zarządzanej. Narzędzia zasad grupy można zainstalować jako część opcjonalna funkcja zarządzania zasadami grupy systemu Windows Server i komputerów klienckich przyłączony do domeny zarządzanej.

Pierwszym zadaniem jest aprowizowanie maszyny wirtualnej systemu Windows Server, który jest przyłączony do domeny zarządzanej. Aby uzyskać instrukcje, zobacz artykuł zatytułowany [Dołącz maszynę wirtualną systemu Windows Server do domeny zarządzanej usług domenowych Azure AD](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-group-policy-tools-on-the-virtual-machine"></a>Zadanie 2 — narzędzia zasad grupy do instalacji na maszynie wirtualnej
Wykonaj poniższe kroki, aby zainstalować narzędzia administracyjne zasad grupy na maszynie wirtualnej przyłączony do domeny.

1. Przejdź do portalu Azure. Kliknij przycisk **wszystkie zasoby** w lewym panelu. Zlokalizuj i kliknij utworzony w zadaniu 1 maszynę wirtualną.
2. Kliknij przycisk **Connect** przycisk na karcie Przegląd. Plik protokołu Remote Desktop Protocol (RDP) jest utworzony i pobrane.

    ![Połącz z maszyną wirtualną systemu Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Aby połączyć się z maszyną wirtualną, otwórz pobrany plik RDP. Jeśli zostanie wyświetlony monit, kliknij przycisk **Połącz**. W wierszu logowania Użyj poświadczeń użytkownika należącego do grupy "Administratorzy usługi AAD kontrolera domeny". Na przykład używamy "bob@domainservicespreview.onmicrosoft.com" w tym przypadku. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Kliknij przycisk Tak, lub Kontynuuj, aby nawiązać połączenie.
4. Na ekranie startowym Otwórz **Menedżera serwera**. Kliknij przycisk **Dodaj role i funkcje** w okienku centralnym okna Menedżera serwera.

    ![Uruchom Menedżera serwera na maszynie wirtualnej](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. Na **przed rozpoczęciem** strony **Kreatora dodawania ról i funkcji**, kliknij przycisk **dalej**.

    ![Przed rozpoczęciem strony](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. Na **typu instalacji** zostaw **Instalacja roli lub funkcji** zaznaczoną opcją i kliknij przycisk **dalej**.

    ![Strona Typ instalacji](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. Na **wybór serwera** strony, wybierz bieżącej maszyny wirtualnej z puli serwerów, a następnie kliknij przycisk **dalej**.

    ![Strona wybierania serwera](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. Na **ról serwera** kliknij przycisk **dalej**. Ponieważ firma Microsoft nie zainstalowano żadnych ról na serwerze, Pomiń tę stronę.
9. Na **funkcje** wybierz pozycję **Zarządzanie zasadami grupy** funkcji.

    ![Strona funkcji](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)
10. Na **potwierdzenie** kliknij przycisk **zainstalować** zainstalować funkcję Zarządzanie zasadami grupy na maszynie wirtualnej. Po pomyślnym zakończeniu instalacji funkcji, kliknij przycisk **Zamknij** aby zakończyć **Dodaj role i funkcje** kreatora.

    ![Strona potwierdzenia](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management-confirmation.png)

## <a name="task-3---launch-the-group-policy-management-console-to-administer-group-policy"></a>Zadanie 3 — uruchamianie konsoli zarządzania zasadami grupy do zarządzania zasadami grupy
Konsolę zarządzania zasadami grupy na maszynie wirtualnej przyłączonych do domeny umożliwia administrowanie zasad grupy w domenie zarządzanej.

> [!NOTE]
> Musisz być członkiem grupy "Administratorzy kontrolera domeny usługi AAD", do administrowania zasad grupy w domenie zarządzanej.
>
>

1. Na ekranie startowym kliknij **narzędzia administracyjne**. Powinny pojawić się **Zarządzanie zasadami grupy** Konsola zainstalowana na maszynie wirtualnej.

    ![Zarządzanie zasadami grupy uruchamiania](./media/active-directory-domain-services-admin-guide/gp-management-installed.png)
2. Kliknij przycisk **Zarządzanie zasadami grupy** można uruchomić konsoli zarządzania zasadami grupy.

    ![Konsola zasad grupy](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

## <a name="task-4---customize-built-in-group-policy-objects"></a>Zadanie 4 — Dostosowywanie wbudowanej obiektów zasad grupy
Istnieją dwa wbudowane obiekty zasad grupy (GPO) — jeden dla kontenerów "AADDC komputery" i "AADDC użytkowników" w domenie zarządzanej. Można dostosować te obiekty zasad grupy, aby skonfigurować zasady grupy dla domeny zarządzanej.

1. W **Zarządzanie zasadami grupy** konsoli, kliknij, aby rozwinąć **lasu: contoso100.com** i **domen** węzłów, aby wyświetlić zasady grupy dla domeny zarządzanej.

    ![Wbudowane obiekty zasad grupy](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)
2. Można dostosować te wbudowane obiekty zasad grupy do konfigurowania zasad grupy w domenie zarządzanej. Kliknij prawym przyciskiem myszy obiekt zasad grupy, a następnie kliknij przycisk **edycji...**  dostosować wbudowanego obiektu zasad grupy. Narzędzie edytora konfiguracji zasad grupy umożliwia dostosowanie obiektu zasad grupy.

    ![Edytowanie obiektu zasad grupy wbudowane](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)
3. Można teraz używać **Edytor zarządzania zasadami grupy** konsoli do edycji obiektu zasad grupy wbudowane. Na przykład poniższy zrzut ekranu przedstawia sposób dostosowywania wbudowanego obiektu zasad grupy "Komputery AADDC".

    ![Dostosowywanie obiektów zasad grupy](./media/active-directory-domain-services-admin-guide/gp-editor.png)

## <a name="task-5---create-a-custom-group-policy-object-gpo"></a>Zadanie 5 — Tworzenie niestandardowego obiektu zasad grupy (GPO)
Można utworzyć lub zaimportować własne obiektów zasad grupy niestandardowe. Niestandardowe obiekty zasad grupy można także połączyć z niestandardowej jednostce Organizacyjnej zostały utworzone w domeny zarządzanej. Aby uzyskać więcej informacji na temat tworzenia niestandardowych jednostkach organizacyjnych, zobacz [Tworzenie niestandardowej jednostki Organizacyjnej w domenie zarządzanej](active-directory-ds-admin-guide-create-ou.md).

> [!NOTE]
> Musisz być członkiem grupy "Administratorzy kontrolera domeny usługi AAD", do administrowania zasad grupy w domenie zarządzanej.
>
>

1. W **Zarządzanie zasadami grupy** konsoli, kliknij, aby wybrać niestandardowy jednostki organizacyjnej (OU). Kliknij prawym przyciskiem myszy jednostkę Organizacyjną, a następnie kliknij przycisk **Utwórz obiekt GPO w tej domenie i umieść tu łącze...** .

    ![Utworzenie niestandardowego obiektu zasad grupy](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)
2. Określ nazwę dla nowego obiektu zasad grupy i kliknij przycisk **OK**.

    ![Określ nazwę dla obiektu zasad grupy](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)
3. Nowy obiekt zasad grupy jest tworzony i powiązany z niestandardowej jednostki Organizacyjnej. Kliknij prawym przyciskiem myszy obiekt zasad grupy, a następnie kliknij przycisk **edycji...**  z menu.

    ![Nowo utworzony obiekt zasad grupy](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)
4. Można dostosować w nowo utworzony obiekt zasad grupy przy użyciu **Edytor zarządzania zasadami grupy**.

    ![Dostosuj nowy obiekt zasad grupy](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)


Więcej informacji o używaniu [konsoli zarządzania zasadami grupy](https://technet.microsoft.com/library/cc753298.aspx) jest dostępna w witrynie Technet.

## <a name="related-content"></a>Powiązana zawartość
* [Usługi domenowe AD Azure - Przewodnik wprowadzający](active-directory-ds-getting-started.md)
* [Dołącz maszynę wirtualną systemu Windows Server do domeny zarządzanej usług domenowych Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Administrowanie domeną zarządzaną usług Azure AD Domain Services](active-directory-ds-admin-guide-administer-domain.md)
* [Konsola zarządzania zasadami grupy](https://technet.microsoft.com/library/cc753298.aspx)
