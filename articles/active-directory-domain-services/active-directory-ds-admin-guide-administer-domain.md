---
title: 'Azure Active Directory Domain Services: Administrowanie domeną zarządzaną | Dokumentacja firmy Microsoft'
description: Administrowanie domeny zarządzanej usług domenowych Azure Active Directory
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: d4fdbc75-3e6b-4e20-8494-5dcc3bf2220a
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: maheshu
ms.openlocfilehash: 68f963b7f3107bad58edb183ec104777903b20d1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34588526"
---
# <a name="administer-an-azure-active-directory-domain-services-managed-domain"></a>Administrowanie domeną zarządzaną usług domenowych Azure Active Directory
W tym artykule przedstawiono sposób administrowania domeny zarządzanej usług domenowych w usłudze Azure Active Directory (AD).

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby wykonać zadania opisane w tym artykule, należy:

1. Prawidłowy **subskrypcji platformy Azure**.
2. **Katalog usługi Azure AD** -albo synchronizowane z katalogu lokalnego lub w katalogu tylko w chmurze.
3. **Usługi domenowe Azure AD** musi być włączona dla katalogu usługi Azure AD. Jeśli nie zostało to jeszcze zrobione, należy wykonać wszystkie zadania opisane w temacie [Przewodnik wprowadzający](active-directory-ds-getting-started.md).
4. A **przyłączonych do domeny maszyny wirtualnej** z którego administrujesz domeny zarządzanej usług domenowych Azure AD. Jeśli nie masz maszynę wirtualną, należy wykonać wszystkie zadania opisane w artykule zatytułowany [Dołącz maszynę wirtualną systemu Windows do domeny zarządzanej](active-directory-ds-admin-guide-join-windows-vm.md).
5. Wymagane poświadczenia **konta użytkownika należącego do grupy "Administratorzy kontrolera domeny usługi AAD"** w katalogu, do administrowania domeny zarządzanej.

<br>

## <a name="administrative-tasks-you-can-perform-on-a-managed-domain"></a>Zadania administracyjne, które można wykonywać na domeny zarządzanej
Członkowie grupy "Administratorzy kontrolera domeny usługi AAD" udzielono uprawnień na domeny zarządzanej, umożliwiających im wykonywanie zadań takich jak:

* Dołącz maszyny do domeny zarządzanej.
* Konfigurowanie wbudowanego obiektu zasad grupy dla kontenerów „Komputery usługi AAD DC” i „Użytkownicy usługi AAD DC” w domenie zarządzanej.
* Administrowanie systemem DNS w domenie zarządzanej.
* Utwórz i administrować niestandardowych jednostek organizacyjnych (OU) w domenie zarządzanej.
* Uzyskiwanie dostępu administracyjnego do komputerów przyłączonych do domeny zarządzanej.

## <a name="administrative-privileges-you-do-not-have-on-a-managed-domain"></a>Uprawnienia administracyjne, które nie znajdują się w domeny zarządzanej
Domena jest zarządzany przez firmę Microsoft, w tym działania, takie jak stosowanie poprawek, monitorowania i wykonywania kopii zapasowych. W związku z tym domeny jest zablokowana i nie masz uprawnień do wykonania niektórych zadań administracyjnych w domenie. Przykładowe zadania, którego nie można wykonać to poniżej.

* Nie udzielono uprawnień administratora domeny lub administratora przedsiębiorstwa dla domeny zarządzanej.
* Nie można rozszerzyć schemat domeny zarządzanej.
* Nie można nawiązać połączenia kontrolery domeny dla domeny zarządzanej przy użyciu pulpitu zdalnego.
* Nie można dodać kontrolerów domeny do domeny zarządzanej.

## <a name="task-1---provision-a-domain-joined-windows-server-virtual-machine-to-remotely-administer-the-managed-domain"></a>Zadanie 1 - udostępnić maszynie wirtualnej przyłączonych do domeny systemu Windows Server do zdalnego administrowania domeny zarządzanej
Azure domen zarządzanych w usługach domenowych AD mogą być zarządzane przy użyciu znanych narzędzi administracyjnych usługi Active Directory takie jak Centrum administracyjne Active Directory (ADAC) lub AD PowerShell. Administratorzy dzierżawy nie masz uprawnień do połączenia się z kontrolerami domeny w domenie zarządzanych za pomocą pulpitu zdalnego. W związku z tym członków grupy "Administratorzy kontrolera domeny usługi AAD" można administrować domen zarządzanych zdalnie przy użyciu narzędzi administracyjnych AD z komputera klienta serwera systemu Windows, który jest przyłączony do domeny zarządzanej. Narzędzia administracyjne AD można zainstalować jako część opcjonalna funkcja narzędzi administracji zdalnej serwera (RSAT) systemu Windows Server i komputerów klienckich przyłączony do domeny zarządzanej.

Pierwszym krokiem jest ustanowienie maszynę wirtualną systemu Windows Server, który jest przyłączony do domeny zarządzanej. Aby uzyskać instrukcje, zobacz artykuł zatytułowany [Dołącz maszynę wirtualną systemu Windows Server do domeny zarządzanej usług domenowych Azure AD](active-directory-ds-admin-guide-join-windows-vm.md).

### <a name="remotely-administer-the-managed-domain-from-a-client-computer-for-example-windows-10"></a>Zdalne administrowanie domeny zarządzanej przez komputer kliencki (na przykład system Windows 10)
Instrukcje w tym artykule maszynę wirtualną systemu Windows Server do administrowania AAD DS zarządzane domeny. Jednak również można użyć maszyny wirtualnej klienta (na przykład system Windows 10) systemu Windows, aby to zrobić.

Możesz [zainstalować narzędzia administracji zdalnej serwera (RSAT)](http://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx) na maszynie wirtualnej Windows klienta zgodnie z instrukcjami w witrynie TechNet.

## <a name="task-2---install-active-directory-administration-tools-on-the-virtual-machine"></a>Zadanie 2 — narzędzia administracyjne usługi Active Directory instalacji na maszynie wirtualnej
Wykonaj następujące kroki, aby zainstalować narzędzia do administrowania Active Directory na maszynie wirtualnej przyłączony do domeny. Aby uzyskać więcej informacji zobacz temat Technet [informacji o instalowaniu i używaniu narzędzi administracji zdalnej serwera](https://technet.microsoft.com/library/hh831501.aspx).

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
9. Na **funkcje** strony, kliknij, aby rozwinąć **narzędzi administracji zdalnej serwera** węzeł, a następnie kliknij przycisk aby rozwinąć **narzędzia do administrowania rolami** węzła. Wybierz **usług AD DS i AD LDS narzędzia** funkcję z listy narzędzia do administrowania rolami.

    ![Strona funkcji](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-ad-tools.png)
10. Na **potwierdzenie** kliknij przycisk **zainstalować** do instalacji usług AD i funkcja narzędzi usług AD LDS na maszynie wirtualnej. Po pomyślnym zakończeniu instalacji funkcji, kliknij przycisk **Zamknij** aby zakończyć **Dodaj role i funkcje** kreatora.

    ![Strona potwierdzenia](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-confirmation.png)

## <a name="task-3---connect-to-and-explore-the-managed-domain"></a>Zadanie 3 - nawiązać połączenie i Eksploruj domeny zarządzanej
Teraz, narzędzi administracyjnych AD są zainstalowane na przyłączonym do domeny maszyny wirtualnej, możemy użyć tych narzędzi do eksplorowania i administrowania domeny zarządzanej.

> [!NOTE]
> Musisz być członkiem grupy "Administratorzy kontrolera domeny usługi AAD", aby administrować domeny zarządzanej.
>
>

1. Na ekranie startowym kliknij **narzędzia administracyjne**. Narzędzia administracyjne AD zainstalowany na maszynie wirtualnej powinny być widoczne.

    ![Narzędzia administracyjne zainstalowane na serwerze](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. Kliknij przycisk **Centrum administracyjne usługi Active Directory**.

    ![Centrum administracyjne usługi Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. Aby zapoznać się z domeny, kliknij nazwę domeny w okienku po lewej stronie (na przykład "contoso100.com"). Zwróć uwagę dwa kontenery odpowiednio o nazwie "AADDC komputery" i "AADDC użytkowników".

    ![ADAC — widok domeny](./media/active-directory-domain-services-admin-guide/adac-domain-view.png)
4. Kliknij kontener o nazwie **użytkowników AADDC** wyświetlić wszystkich użytkowników i grup należących do domeny zarządzanej. Powinny pojawić się konta użytkowników i grup z usługi Azure AD dzierżawy Pokaż się w tym kontenerze. W tym przykładzie, konto użytkownika dla użytkownika o nazwie "bob" i grupę o nazwie "Administratorzy kontrolera domeny usługi AAD" są dostępne w tym kontenerze.

    ![ADAC - użytkownicy domeny](./media/active-directory-domain-services-admin-guide/adac-aaddc-users.png)
5. Kliknij kontener o nazwie **komputerów AADDC** widoczne komputery przyłączone do tej domeny zarządzanej. Powinny pojawić się wpis dla bieżącej maszyny wirtualnej, który jest przyłączony do domeny. Konta komputerów dla wszystkich komputerów, które są przyłączone do domeny zarządzanej usług domenowych Azure AD są przechowywane w tym kontenerze "Komputery AADDC".

    ![ADAC — komputery przyłączone do domeny](./media/active-directory-domain-services-admin-guide/adac-aaddc-computers.png)

<br>

## <a name="related-content"></a>Powiązana zawartość
* [Usługi domenowe AD Azure - Przewodnik wprowadzający](active-directory-ds-getting-started.md)
* [Dołącz maszynę wirtualną systemu Windows Server do domeny zarządzanej usług domenowych Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Wdrażanie narzędzi administracji zdalnej serwera](https://technet.microsoft.com/library/hh831501.aspx)
