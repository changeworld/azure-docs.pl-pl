---
title: 'Azure Active Directory Domain Services: Administrowanie zasady grupy | Microsoft Docs'
description: Administrowanie zasady grupy w Azure Active Directory Domain Services domenach zarządzanych
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: iainfou
ms.openlocfilehash: c7b32885fdb3cf4f3e584c916d6b234fff54bfc4
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234042"
---
# <a name="administer-group-policy-on-an-azure-ad-domain-services-managed-domain"></a>Administrowanie zasady grupy w Azure AD Domain Servicesej domenie zarządzanej
Azure Active Directory Domain Services obejmuje wbudowane obiekty zasady grupy (GPO) dla kontenerów "AADDC users" i "AADDC Computers". Można dostosować te wbudowane obiekty zasad grupy w celu skonfigurowania zasady grupy w domenie zarządzanej. Ponadto członkowie grupy "Administratorzy usługi AAD DC" mogą tworzyć własne niestandardowe jednostki organizacyjne w domenie zarządzanej. Mogą również tworzyć niestandardowe obiekty zasad grupy i łączyć je z tymi niestandardowymi jednostkami organizacyjnymi. Użytkownicy należący do grupy "Administratorzy usługi AAD DC" otrzymują zasady grupy uprawnienia administracyjne w domenie zarządzanej.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby wykonać zadania wymienione w tym artykule, potrzebne są:

1. Prawidłowa **subskrypcja platformy Azure**.
2. **Katalog usługi Azure AD** — zsynchronizowany z katalogiem lokalnym lub katalogiem w chmurze.
3. Należy włączyć **Azure AD Domain Services** dla katalogu usługi Azure AD. Jeśli nie zostało to zrobione, postępuj zgodnie ze wszystkimi zadaniami opisanymi w [przewodniku wprowadzenie](create-instance.md).
4. Przyłączona do **domeny maszyna wirtualna** , z której administrujesz Azure AD Domain Services domeny zarządzanej. Jeśli nie masz takiej maszyny wirtualnej, postępuj zgodnie ze wszystkimi zadaniami opisanymi w artykule zatytułowanym [Dołącz maszynę wirtualną z systemem Windows do domeny zarządzanej](active-directory-ds-admin-guide-join-windows-vm.md).
5. Do administrowania zasady grupy dla domeny zarządzanej są potrzebne poświadczenia **konta użytkownika należącego do grupy "Administratorzy usługi AAD DC"** w katalogu.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-group-policy-for-the-managed-domain"></a>Zadanie 1 — Inicjowanie obsługi administracyjnej maszyny wirtualnej przyłączonego do domeny w celu zdalnego administrowania zasady grupyą dla domeny zarządzanej
Azure AD Domain Services domen zarządzanych można zarządzać zdalnie przy użyciu znanych Active Directory narzędzi administracyjnych, takich jak Centrum administracyjne usługi Active Directory (usługach ADAC) lub AD PowerShell. Podobnie zasady grupy dla domeny zarządzanej można administrować zdalnie przy użyciu narzędzi administracyjnych zasady grupy.

Administratorzy w katalogu usługi Azure AD nie mają uprawnień do łączenia się z kontrolerami domeny w domenie zarządzanej za pośrednictwem Pulpit zdalny. Członkowie grupy "Administratorzy usługi AAD DC" mogą zdalnie administrować zasady grupy dla zarządzanych domen. Mogą oni korzystać z narzędzi zasady grupy na komputerze klienckim z systemem Windows lub w systemie, przyłączonym do domeny zarządzanej. Narzędzia zasady grupy można zainstalować jako część opcjonalnej funkcji zarządzania zasady grupy w systemie Windows Server i na komputerach klienckich dołączonych do domeny zarządzanej.

Pierwszym zadaniem jest zainicjowanie obsługi administracyjnej maszyny wirtualnej z systemem Windows Server, która jest przyłączona do domeny zarządzanej. Aby uzyskać instrukcje, zobacz artykuł zatytułowany dołączanie [maszyny wirtualnej z systemem Windows Server do domeny zarządzanej Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-group-policy-tools-on-the-virtual-machine"></a>Zadanie 2 — Instalowanie narzędzi zasady grupy na maszynie wirtualnej
Wykonaj następujące kroki, aby zainstalować narzędzia do administrowania zasady grupy na maszynie wirtualnej przyłączonej do domeny.

1. Przejdź do Azure Portal. Na panelu po lewej stronie kliknij pozycję **wszystkie zasoby** . Znajdź i kliknij maszynę wirtualną utworzoną w zadaniu 1.
2. Kliknij przycisk **Połącz** na karcie Przegląd. Zostanie utworzony i pobrany plik Remote Desktop Protocol (RDP).

    ![Połącz z maszyną wirtualną z systemem Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Aby połączyć się z maszyną wirtualną, otwórz pobrany plik RDP. Jeśli zostanie wyświetlony monit, kliknij przycisk **Połącz**. W wierszu logowania Użyj poświadczeń użytkownika należących do grupy "Administratorzy usługi AAD DC". Na przykład używamy "bob@domainservicespreview.onmicrosoft.com" w naszym przypadku. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Kliknij przycisk Tak lub Kontynuuj, aby kontynuować połączenie.
4. Na ekranie startowym Otwórz **Menedżer serwera**. Kliknij pozycję **Dodaj role i funkcje** w środkowym okienku okna Menedżer serwera.

    ![Uruchom Menedżer serwera na maszynie wirtualnej](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. Na stronie **zanim rozpoczniesz** **Kreatora dodawania ról i funkcji**kliknij przycisk **dalej**.

    ![Przed rozpoczęciem](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. Na stronie **Typ instalacji** pozostaw zaznaczoną opcję **Instalacja oparta na rolach lub** oparta na funkcjach, a następnie kliknij przycisk **dalej**.

    ![Strona typu instalacji](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. Na stronie **Wybór serwera** wybierz bieżącą maszynę wirtualną z puli serwerów, a następnie kliknij przycisk **dalej**.

    ![Strona wybór serwera](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. Na stronie **role serwera** kliknij przycisk **dalej**. Pomijamy Tę stronę, ponieważ nie instalujemy żadnych ról na serwerze.
9. Na stronie **funkcje** wybierz funkcję **zarządzania zasady grupy** .

    ![Strona funkcje](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)
10. Na stronie **potwierdzenie** kliknij przycisk **Zainstaluj** , aby zainstalować funkcję zarządzania zasady grupy na maszynie wirtualnej. Po pomyślnym zakończeniu instalacji funkcji kliknij przycisk **Zamknij** , aby zamknąć kreatora **dodawania ról i funkcji** .

    ![Strona potwierdzenia](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management-confirmation.png)

## <a name="task-3---launch-the-group-policy-management-console-to-administer-group-policy"></a>Zadanie 3 — Uruchom konsolę zarządzania zasady grupy, aby administrować zasady grupy
Za pomocą konsoli zarządzania zasady grupy na maszynie wirtualnej przyłączonej do domeny można administrować zasady grupy w domenie zarządzanej.

> [!NOTE]
> Aby administrować zasady grupy w domenie zarządzanej, musisz być członkiem grupy administratorów domeny usługi AAD.
>
>

1. Na ekranie startowym kliknij pozycję **Narzędzia administracyjne**. Powinna zostać wyświetlona konsola **zarządzania zasady grupy** zainstalowana na maszynie wirtualnej.

    ![Uruchom zarządzanie zasady grupy](./media/active-directory-domain-services-admin-guide/gp-management-installed.png)
2. Kliknij przycisk **zasady grupy zarządzanie** , aby uruchomić konsolę zarządzania zasady grupy.

    ![Konsola zasady grupy](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

## <a name="task-4---customize-built-in-group-policy-objects"></a>Zadanie 4 — Dostosowywanie wbudowanych obiektów zasady grupy
Istnieją dwa wbudowane obiekty zasady grupy (GPO) — jeden dla kontenerów "AADDC Computers" i "AADDC users" w domenie zarządzanej. Można je dostosować, aby skonfigurować zasady grupy w domenie zarządzanej.

1. W konsoli **zarządzania zasady grupy** kliknij, aby rozwinąć węzeł **lasu: contoso100.com** i **domen** , aby wyświetlić zasady grupy dla domeny zarządzanej.

    ![Wbudowane obiekty zasad grupy](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)
2. Można dostosować te wbudowane obiekty zasad grupy w celu skonfigurowania zasad grup w domenie zarządzanej. Kliknij prawym przyciskiem myszy obiekt zasad grupy, a następnie kliknij przycisk **Edytuj...** , aby dostosować wbudowany obiekt zasad grupy. Zasady grupy narzędzie Edytor konfiguracji umożliwia dostosowanie obiektu zasad grupy.

    ![Edytowanie wbudowanego obiektu zasad grupy](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)
3. Teraz można użyć konsoli **Edytor zarządzania zasadami grupy** do edycji wbudowanego obiektu zasad grupy. Na przykład poniższy zrzut ekranu przedstawia sposób dostosowywania wbudowanego obiektu zasad grupy "AADDC komputery".

    ![Dostosuj obiekt zasad grupy](./media/active-directory-domain-services-admin-guide/gp-editor.png)

## <a name="task-5---create-a-custom-group-policy-object-gpo"></a>Zadanie 5 — Tworzenie niestandardowego obiektu zasady grupy (GPO)
Można utworzyć lub zaimportować własne niestandardowe obiekty zasad grupy. Niestandardowe obiekty zasad grupy można także połączyć z niestandardową jednostką organizacyjną utworzoną w domenie zarządzanej. Aby uzyskać więcej informacji na temat tworzenia niestandardowych jednostek organizacyjnych, zobacz [Tworzenie niestandardowej jednostki organizacyjnej w domenie zarządzanej](create-ou.md).

> [!NOTE]
> Aby administrować zasady grupy w domenie zarządzanej, musisz być członkiem grupy administratorów domeny usługi AAD.
>
>

1. W konsoli **zarządzania zasady grupy** kliknij, aby wybrać niestandardową jednostkę organizacyjną (OU). Kliknij prawym przyciskiem myszy jednostkę organizacyjną, a następnie kliknij pozycję **Utwórz obiekt zasad grupy w tej domenie i umieść tu link.**

    ![Tworzenie niestandardowego obiektu zasad grupy](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)
2. Określ nazwę dla nowego obiektu zasad grupy, a następnie kliknij przycisk **OK**.

    ![Określ nazwę obiektu zasad grupy](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)
3. Nowy obiekt zasad grupy jest tworzony i połączony z niestandardową jednostką organizacyjną. Kliknij prawym przyciskiem myszy obiekt zasad grupy, a następnie kliknij polecenie **Edytuj...** z menu.

    ![Nowo utworzony obiekt zasad grupy](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)
4. Nowo utworzony obiekt zasad grupy można dostosować przy użyciu **Edytor zarządzania zasadami grupy**.

    ![Dostosuj nowy obiekt zasad grupy](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)


Więcej informacji na temat korzystania z [Konsola zarządzania zasadami grupy](https://technet.microsoft.com/library/cc753298.aspx) jest dostępnych w witrynie TechNet.

## <a name="related-content"></a>Powiązana zawartość
* [Przewodnik po Wprowadzenie Azure AD Domain Services](create-instance.md)
* [Przyłączanie maszyny wirtualnej z systemem Windows Server do domeny zarządzanej Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Zarządzanie domeną Azure AD Domain Services](manage-domain.md)
* [Konsola zarządzania zasadami grupy](https://technet.microsoft.com/library/cc753298.aspx)
