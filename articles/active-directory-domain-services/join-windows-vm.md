---
title: Dołączanie maszyny Wirtualnej systemu Windows Server do domeny zarządzanej | Dokumenty firmy Microsoft
description: W tym samouczku dowiesz się, jak dołączyć maszynę wirtualną systemu Windows Server do domeny zarządzanej usług domenowych usługi active directory platformy Azure.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 1ac508fc9fee07482e475c46e1db262c8bfa1a12
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476266"
---
# <a name="tutorial-join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Samouczek: Dołączanie maszyny wirtualnej systemu Windows Server do domeny zarządzanej

Usługi domenowe Usługi Active Directory (Azure AD DS) zapewniają usługi domeny zarządzanej, takie jak dołączanie do domeny, zasady grupy, uwierzytelnianie LDAP, Kerberos/NTLM, które jest w pełni zgodne z usługą Active Directory systemu Windows Server. Za pomocą domeny zarządzanej usług Azure AD DS można udostępniać funkcje dołączania do domeny i zarządzanie maszynami wirtualnymi na platformie Azure. W tym samouczku pokazano, jak utworzyć maszynę wirtualną systemu Windows Server, a następnie dołączyć ją do domeny zarządzanej usług Ad DS platformy Azure.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie maszyny Wirtualnej systemu Windows Server
> * Łączenie maszyny Wirtualnej systemu Windows Server z siecią wirtualną platformy Azure
> * Dołączanie maszyny Wirtualnej do domeny zarządzanej usług Azure AD DS

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, potrzebne są następujące zasoby:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem tylko w chmurze.
    * W razie potrzeby [utwórz dzierżawę usługi Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z kontem.][associate-azure-ad-tenant]
* Domena zarządzana usługami domenowymi Usługi Active Directory platformy Azure włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie potrzeby [utwórz i skonfiguruj wystąpienie usług domenowych Active Directory platformy Azure][create-azure-ad-ds-instance].
* Konto użytkownika, które jest częścią domeny zarządzanej usług Azure AD DS.
    * Upewnij się, że została wykonana synchronizacja skrótu haseł usługi Azure AD Connect lub samoobsługowe resetowanie hasła, aby konto mogło zalogować się do domeny zarządzanej usługi Azure AD DS.
* Host bastionu platformy Azure wdrożony w sieci wirtualnej usług Ad DS platformy Azure.
    * W razie potrzeby [utwórz hosta Bastionu Platformy Azure][azure-bastion].

Jeśli masz już maszynę wirtualną, do której chcesz dołączyć do domeny, przejdź do sekcji, aby [dołączyć do maszyny Wirtualnej do domeny zarządzanej usług Azure AD DS](#join-the-vm-to-the-azure-ad-ds-managed-domain).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

W tym samouczku utworzysz maszynę wirtualną systemu Windows Server, aby dołączyć do domeny zarządzanej usług Azure AD DS przy użyciu witryny Azure portal. Aby rozpocząć, najpierw zaloguj się do [witryny Azure portal](https://portal.azure.com).

## <a name="create-a-windows-server-virtual-machine"></a>Tworzenie maszyny wirtualnej systemu Windows Server

Aby zobaczyć, jak dołączyć komputer do domeny zarządzanej usług Azure AD DS, utwórzmy maszynę wirtualną systemu Windows Server. Ta maszyna wirtualna jest połączona z siecią wirtualną platformy Azure, która zapewnia łączność z domeną zarządzaną usługą Azure AD DS. Proces dołączania do domeny zarządzanej usług Azure AD DS jest taki sam jak dołączanie do zwykłej lokalnej domeny Usług domenowych Active Directory.

Jeśli masz już maszynę wirtualną, do której chcesz dołączyć do domeny, przejdź do sekcji, aby [dołączyć do maszyny Wirtualnej do domeny zarządzanej usług Azure AD DS](#join-the-vm-to-the-azure-ad-ds-managed-domain).

1. Z menu portalu platformy Azure lub na stronie **głównej** wybierz pozycję **Utwórz zasób**.

1. Wprowadzenie **wybierz**pozycję **Centrum danych systemu Windows Server 2016**.

    ![Wybieranie utworzenia maszyny Wirtualnej centrum danych systemu Windows Server 2016 w portalu azure](./media/join-windows-vm/select-vm-image.png)

1. W oknie **Podstawy** skonfiguruj podstawowe ustawienia maszyny wirtualnej. Pozostaw ustawienia domyślne *dla opcji Dostępność*, *Obraz*i *Rozmiar*.

    | Parametr            | Sugerowana wartość   |
    |----------------------|-------------------|
    | Grupa zasobów       | Zaznaczanie lub tworzenie grupy zasobów, takiej jak *myResourceGroup* |
    | Nazwa maszyny wirtualnej | Wprowadź nazwę maszyny Wirtualnej, taką jak *myVM* |
    | Region               | Wybierz region, w jakim chcesz utworzyć maszynę wirtualną, na przykład *wschodnie stany USA* |
    | Nazwa użytkownika             | Wprowadź nazwę użytkownika dla lokalnego konta administratora do utworzenia na maszynie Wirtualnej, na przykład *azureuser* |
    | Hasło             | Wprowadź, a następnie potwierdź bezpieczne hasło dla administratora lokalnego do utworzenia na maszynie wirtualnej. Nie określaj poświadczeń konta użytkownika domeny. |

1. Domyślnie maszyny wirtualne utworzone na platformie Azure są dostępne z Internetu przy użyciu protokołu RDP. Gdy protokół RDP jest włączony, mogą wystąpić ataki automatycznego logowania, które mogą wyłączyć konta o nazwach pospolitych, takich jak *administrator* lub *administrator* z powodu wielu nieudanych kolejnych prób logowania.

    Protokół RDP powinien być włączony tylko wtedy, gdy jest to wymagane i ograniczony do zestawu autoryzowanych zakresów adresów IP. Ta konfiguracja pomaga poprawić bezpieczeństwo maszyny Wirtualnej i zmniejsza obszar potencjalnego ataku. Lub utworzyć i używać hosta Bastion platformy Azure, który umożliwia dostęp tylko za pośrednictwem witryny Azure portal za pośrednictwem protokołu TLS. W następnym kroku tego samouczka używasz hosta bastionu platformy Azure, aby bezpiecznie połączyć się z maszyną wirtualną.

    W **obszarze Publiczne porty przychodzące**wybierz pozycję *Brak*.

1. Po zakończeniu wybierz **przycisk Dalej: Dyski**.
1. Z menu rozwijanego typu **dysku systemu operacyjnego**wybierz pozycję *Standardowy dysk SSD*, a następnie wybierz pozycję **Dalej: Sieć**.
1. Maszyna wirtualna musi połączyć się z podsiecią sieci wirtualnej platformy Azure, która może komunikować się z podsiecią, w których wdrożona jest domena zarządzana usługą Azure AD DS. Zaleca się, że domena zarządzana usługą Azure AD DS jest wdrażana we własnej dedykowanej podsieci. Nie wdrażaj maszyny Wirtualnej w tej samej podsieci co domena zarządzana usługą Azure AD DS.

    Istnieją dwa główne sposoby wdrażania maszyny Wirtualnej i łączenia się z odpowiednią podsiecią sieci wirtualnej:
    
    * Utwórz lub wybierz istniejącą podsieć w tej samej sieci wirtualnej, co wdrożona jest domena zarządzana usługą Azure AD DS.
    * Wybierz podsieć w sieci wirtualnej platformy Azure, która jest z nią połączona przy użyciu [komunikacji równorzędnej sieci wirtualnej platformy Azure][vnet-peering].
    
    Jeśli wybierzesz podsieć sieci wirtualnej, która nie jest połączona z podsiecią wystąpienia usług Azure AD DS, nie możesz dołączyć maszyny Wirtualnej do domeny zarządzanej. W tym samouczku utwórzmy nową podsieć w sieci wirtualnej platformy Azure.

    W okienku **Sieć** wybierz sieć wirtualną, w której jest wdrażana domena zarządzana usługą Azure AD DS, na przykład *aaads-vnet*
1. W tym przykładzie *istniejąca podsieć aaads jest wyświetlana,* z którą jest połączona domena zarządzana usługą Azure AD DS. Nie podłączaj maszyny Wirtualnej do tej podsieci. Aby utworzyć podsieć dla maszyny Wirtualnej, wybierz pozycję **Zarządzaj konfiguracją podsieci**.

    ![Wybierz, aby zarządzać konfiguracją podsieci w witrynie Azure portal](./media/join-windows-vm/manage-subnet.png)

1. W menu po lewej stronie okna sieci wirtualnej wybierz pozycję **Przestrzeń adresowa**. Sieć wirtualna jest tworzona z pojedynczą przestrzenią adresową *10.0.2.0/24*, która jest używana przez domyślną podsieć. Inne podsieci, takie jak *dla obciążeń* lub Bastion platformy Azure może już istnieć.

    Dodaj dodatkowy zakres adresów IP do sieci wirtualnej. Rozmiar tego zakresu adresów i rzeczywistego zakresu adresów IP, którego należy użyć, zależy od innych już wdrożonych zasobów sieciowych. Zakres adresów IP nie powinien pokrywać się z istniejącymi zakresami adresów w środowisku platformy Azure lub lokalnym. Upewnij się, że rozmiar zakresu adresów IP wystarczająco duży dla liczby maszyn wirtualnych, które mają być wdrażane w podsieci.

    W poniższym przykładzie dodano dodatkowy zakres adresów IP *10.0.5.0/24.* Gdy będzie gotowy, wybierz pozycję **Zapisz**.

    ![Dodawanie dodatkowego zakresu adresów IP sieci wirtualnej w witrynie Azure portal](./media/join-windows-vm/add-vnet-address-range.png)

1. Następnie w menu po lewej stronie okna sieci wirtualnej wybierz pozycję **Podsieci**, a następnie wybierz + **Podsieć,** aby dodać podsieć.

1. Wybierz **+ Podsieć**, a następnie wprowadź nazwę podsieci, na przykład *zarządzanie*. Podaj **zakres adresów (blok CIDR),** na przykład *10.0.5.0/24*. Upewnij się, że ten zakres adresów IP nie pokrywa się z innymi istniejącymi zakresami adresów platformy Azure lub lokalnymi. Pozostaw inne opcje jako wartości domyślne, a następnie wybierz **przycisk OK**.

    ![Tworzenie konfiguracji podsieci w witrynie Azure portal](./media/join-windows-vm/create-subnet.png)

1. Utworzenie podsieci zajmuje kilka sekund. Po utworzeniu wybierz *x,* aby zamknąć okno podsieci.
1. W okienku **Sieć,** aby utworzyć maszynę wirtualną, wybierz podsieć utworzoną z menu rozwijanego, na przykład *zarządzanie*. Ponownie upewnij się, że wybierzesz poprawną podsieć i nie wdrażasz maszyny Wirtualnej w tej samej podsieci co domena zarządzana usługą Azure AD DS.
1. W przypadku **publicznego adresu IP**wybierz *opcję Brak* z menu rozwijanego, ponieważ używasz usługi Azure Bastion do łączenia się z zarządzaniem i nie potrzebujesz przypisanego publicznego adresu IP.
1. Pozostaw inne opcje jako wartości domyślne, a następnie wybierz pozycję **Zarządzanie**.
1. Ustaw **diagnostykę rozruchu** na *Wyłącz*. Pozostaw inne opcje jako wartości domyślne, a następnie wybierz **pozycję Przejrzyj + utwórz**.
1. Przejrzyj ustawienia maszyny Wirtualnej, a następnie wybierz pozycję **Utwórz**.

Utworzenie maszyny Wirtualnej zajmuje kilka minut. Portal platformy Azure pokazuje stan wdrożenia. Gdy maszyna wirtualna będzie gotowa, wybierz pozycję **Przejdź do zasobu**.

![Przejdź do zasobu maszyny Wirtualnej w witrynie Azure portal po pomyślnym utworzeniu](./media/join-windows-vm/vm-created.png)

## <a name="connect-to-the-windows-server-vm"></a>Łączenie się z maszyną wirtualną systemu Windows Server

Aby bezpiecznie połączyć się z maszynami wirtualnymi, użyj hosta Bastion platformy Azure. Dzięki usłudze Azure Bastion host zarządzany jest wdrażany w sieci wirtualnej i zapewnia oparte na sieci Web połączenia RDP lub SSH z maszynami wirtualnymi. Nie są wymagane żadne publiczne adresy IP dla maszyn wirtualnych i nie trzeba otwierać reguł sieciowej grupy zabezpieczeń dla zewnętrznego ruchu zdalnego. Łączysz się z maszynami wirtualnymi przy użyciu portalu Azure za pomocą przeglądarki sieci Web.

Aby połączyć się z maszyną wirtualną za pomocą hosta bastionu, wykonaj następujące czynności:

1. W okienku **Przegląd** maszyny Wirtualnej wybierz pozycję **Połącz**, a następnie **bastion**.

    ![Łączenie się z maszyną wirtualną systemu Windows przy użyciu bastionu w witrynie Azure portal](./media/join-windows-vm/connect-to-vm.png)

1. Wprowadź poświadczenia maszyny Wirtualnej określone w poprzedniej sekcji, a następnie wybierz pozycję **Połącz**.

   ![Łączenie się za pośrednictwem hosta bastionu w witrynie Azure portal](./media/join-windows-vm/connect-to-bastion.png)

W razie potrzeby zezwól przeglądarce internetowej na otwieranie wyskakujących okienków dla połączenia Bastion, które ma być wyświetlane. Nawiązanie połączenia z maszyną wirtualną zajmuje kilka sekund.

## <a name="join-the-vm-to-the-azure-ad-ds-managed-domain"></a>Dołączanie maszyny Wirtualnej do domeny zarządzanej usług Azure AD DS

Po utworzeniu maszyny wirtualnej i połączeniu RDP opartym na sieci Web nawiązanym przy użyciu usługi Azure Bastion dołączmy do maszyny wirtualnej systemu Windows Server do domeny zarządzanej usług Azure AD DS. Ten proces jest taki sam jak komputer łączący się ze zwykłą lokalną domeną Usług domenowych Active Directory.

1. Jeśli **Menedżer serwera** nie otwiera się domyślnie po zalogowaniu się do maszyny Wirtualnej, wybierz menu **Start,** a następnie wybierz pozycję **Menedżer serwera**.
1. W lewym okienku okna **Menedżer serwera** wybierz pozycję **Serwer lokalny**. W obszarze **Właściwości** w prawym okienku wybierz pozycję **Grupa robocza**.

    ![Otwórz Menedżera serwera na maszynie Wirtualnej i edytuj właściwość grupy roboczej](./media/join-windows-vm/server-manager.png)

1. W oknie **Właściwości systemu** wybierz pozycję **Zmień,** aby dołączyć do domeny zarządzanej usług Azure AD DS.

    ![Aby zmienić właściwości grupy roboczej lub domeny](./media/join-windows-vm/change-domain.png)

1. W polu **Domain** określ nazwę domeny zarządzanej usług Azure AD DS, na przykład *aaddscontoso.com*, a następnie wybierz przycisk **OK**.

    ![Określanie domeny zarządzanej usług Azure AD DS do dołączenia](./media/join-windows-vm/join-domain.png)

1. Wprowadź poświadczenia domeny, aby dołączyć do domeny. Użyj poświadczeń dla użytkownika, który jest częścią domeny zarządzanej usług Azure AD DS. Konto musi być częścią domeny zarządzanej usługi Azure AD DS lub dzierżawy usługi Azure AD — konta z katalogów zewnętrznych skojarzonych z dzierżawą usługi Azure AD nie mogą poprawnie uwierzytelniać się podczas procesu dołączania do domeny. Poświadczenia konta można określić w jeden z następujących sposobów:

    * **Format nazwy UPN** (zalecane) — wprowadź sufiks głównej nazwy użytkownika (UPN) dla konta użytkownika, zgodnie z konfiguracją w usłudze Azure AD. Na przykład sufiks nazwy UPN użytkownika *contosoadmin* będzie `contosoadmin@aaddscontoso.onmicrosoft.com`. Istnieje kilka typowych przypadków użycia, w których format nazwy UPN może być niezawodnie używany do logowania się do domeny, a nie w formacie *SAMAccountName:*
        * Jeśli prefiks nazwy UPN użytkownika jest długi, na przykład *deehasareallylongname*, *SAMAccountName* może być automatyczniegenerowany.
        * Jeśli wielu użytkowników ma ten sam prefiks nazwy UPN w dzierżawie usługi Azure AD, takich jak *dee,* ich format *SAMAccountName* może być automatyczniegenerowany.
    * **SAMAccountName format** — wprowadź nazwę konta w formacie *SAMAccountName.* Na przykład *SAMAccountName* użytkownika *contosoadmin* `AADDSCONTOSO\contosoadmin`będzie .

1. To trwa kilka sekund, aby dołączyć do domeny zarządzanej usług Azure AD DS. Po zakończeniu następujący komunikat zostanie powitany w domenie:

    ![Witamy w domenie](./media/join-windows-vm/join-domain-successful.png)

    Kliknij przycisk **OK**, aby kontynuować.

1. Aby zakończyć proces dołączania do domeny zarządzanej usług Azure AD DS, uruchom ponownie maszynę wirtualną.

> [!TIP]
> Maszynę wirtualną można dołączyć do domeny przy użyciu programu PowerShell za pomocą polecenia cmdlet [Dodaj komputer.][add-computer] Poniższy przykład łączy domenę *AADDSCONTOSO,* a następnie ponownie uruchamia maszynę wirtualną. Po wyświetleniu monitu wprowadź poświadczenia dla użytkownika, który jest częścią domeny zarządzanej usług Azure AD DS:
>
> `Add-Computer -DomainName AADDSCONTOSO -Restart`
>
> Aby dołączyć do domeny maszyny Wirtualnej bez łączenia się z nim i ręcznie konfigurowania połączenia, można użyć polecenia cmdlet [Set-AzVmAdDomainExtension][set-azvmaddomainextension] Azure PowerShell.

Po ponownym uruchomieniu maszyny Wirtualnej systemu Windows Server wszystkie zasady stosowane w domenie zarządzanej usług Azure AD DS są wypychane do maszyny Wirtualnej. Można również teraz zalogować się do maszyny Wirtualnej systemu Windows Server przy użyciu odpowiednich poświadczeń domeny.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W następnym samouczku używasz tej maszyny Wirtualnej systemu Windows Server do zainstalowania narzędzi do zarządzania, które umożliwiają administrowanie domeną zarządzaną usługą Azure AD DS. Jeśli nie chcesz kontynuować w tej serii samouczków, zapoznaj się z następującymi krokami [oczyszczania, aby usunąć maszynę wirtualną](#delete-the-vm). W przeciwnym razie [przejdź do następnego samouczka](#next-steps).

### <a name="un-join-the-vm-from-azure-ad-ds-managed-domain"></a>Odłączenie maszyny wirtualnej z domeny zarządzanej usług Azure AD DS

Aby usunąć maszynę wirtualną z domeny zarządzanej usług Azure AD DS, wykonaj ponownie kroki, aby [dołączyć do maszyny Wirtualnej do domeny](#join-the-vm-to-the-azure-ad-ds-managed-domain). Zamiast dołączać do domeny zarządzanej usług Azure AD DS, wybierz opcję dołączenia do grupy roboczej, takiej jak *domyślna grupa robocza*. Po ponownym uruchomieniu maszyny Wirtualnej obiekt komputera jest usuwany z domeny zarządzanej usług Azure AD DS.

Jeśli [usuniesz maszynę wirtualną](#delete-the-vm) bez odłączenia się od domeny, oddzielony obiekt komputera pozostanie w usługach Azure AD DS.

### <a name="delete-the-vm"></a>Usuwanie maszyny wirtualnej

Jeśli nie zamierzasz używać tej maszyny Wirtualnej systemu Windows Server, usuń maszynę wirtualną, wykonując następujące czynności:

1. Z menu po lewej stronie wybierz **pozycję Grupy zasobów**
1. Wybierz grupę zasobów, taką jak *myResourceGroup*.
1. Wybierz maszynę wirtualną, na przykład *myVM,* a następnie wybierz pozycję **Usuń**. Wybierz **pozycję Tak,** aby potwierdzić usunięcie zasobu. Usunięcie maszyny wirtualnej zajmuje kilka minut.
1. Po usunięciu maszyny Wirtualnej wybierz dysk systemu operacyjnego, kartę interfejsu sieciowego i inne zasoby za pomocą prefiksu *myVM i* usuń je.

## <a name="troubleshoot-domain-join-issues"></a>Rozwiązywanie problemów z przyłączaniem do domeny

Maszyna wirtualna systemu Windows Server powinna pomyślnie dołączyć do domeny zarządzanej usługi Azure AD DS, tak samo jak zwykły komputer lokalny dołączy do domeny Usług domenowych Active Directory. Jeśli maszyna wirtualna systemu Windows Server nie może dołączyć do domeny zarządzanej usługi Azure AD DS, oznacza to, że występuje problem z łącznością lub poświadczeniami. Przejrzyj następujące sekcje rozwiązywania problemów, aby pomyślnie dołączyć do domeny zarządzanej.

### <a name="connectivity-issues"></a>Problemy z łącznością

Jeśli nie zostanie wyświetlony monit z prośbą o podanie poświadczeń do przyłączenia się do domeny, występuje problem z łącznością. Maszyna wirtualna nie może dotrzeć do domeny zarządzanej usług Azure AD DS w sieci wirtualnej.

Po wypróbowaniu każdego z tych kroków rozwiązywania problemów spróbuj ponownie dołączyć maszynę wirtualną systemu Windows Server do domeny zarządzanej.

* Sprawdź, czy maszyna wirtualna jest połączona z tą samą siecią wirtualną, wążej usługi Azure AD DS lub ma równorzędne połączenie sieciowe.
* Spróbuj wykonać ping do nazwy domeny DNS `ping aaddscontoso.com`domeny zarządzanej, takiej jak .
    * Jeśli żądanie ping nie powiedzie się, spróbuj wykonać ping `ping 10.0.0.4`do adresów IP domeny zarządzanej, takich jak . Adres IP dla twojego środowiska jest wyświetlany na stronie *Właściwości* po wybraniu domeny zarządzanej usług Azure AD DS z listy zasobów platformy Azure.
    * Jeśli można pingować adres IP, ale nie domenę, system DNS może być niepoprawnie skonfigurowany. Upewnij się, że adresy IP domeny zarządzanej są skonfigurowane jako serwery DNS dla sieci wirtualnej.
* Spróbuj opróżnić pamięć podręczną programu rozpoznawania `ipconfig /flushdns` nazw DNS na maszynie wirtualnej za pomocą polecenia.

### <a name="credentials-related-issues"></a>Problemy związane z poświadczeniami

Jeśli zostanie wyświetlony monit z prośbą o poświadczenia do przyłączenia się do domeny, ale następnie błąd po wprowadzeniu tych poświadczeń, maszyna wirtualna może połączyć się z domeną zarządzaną usługą Azure AD DS. Podane poświadczenia nie pozwalają maszynie wirtualnej dołączyć do domeny zarządzanej usług Azure AD DS.

Po wypróbowaniu każdego z tych kroków rozwiązywania problemów spróbuj ponownie dołączyć maszynę wirtualną systemu Windows Server do domeny zarządzanej.

* Upewnij się, że określone konto użytkownika należy do domeny zarządzanej usług Azure AD DS.
* Upewnij się, że konto jest częścią domeny zarządzanej usług Azure AD DS lub dzierżawy usługi Azure AD. Konta z katalogów zewnętrznych skojarzonych z dzierżawą usługi Azure AD nie mogą poprawnie uwierzytelniać się podczas procesu dołączania do domeny.
* Spróbuj użyć formatu UPN, aby `contosoadmin@aaddscontoso.onmicrosoft.com`określić poświadczenia, takie jak . Jeśli w dzierżawie jest wielu użytkowników z tym samym prefiksem nazwy UPN lub jeśli prefiks nazwy UPN jest zbyt długi, *nazwa SAMAccountname* dla twojego konta może zostać automatycznie wygenerowana. W takich przypadkach format *SAMAccountName* dla twojego konta może się różnić od tego, czego oczekujesz lub który jest używany w domenie lokalnej.
* Sprawdź, czy [synchronizacja haseł została włączona][password-sync] do domeny zarządzanej. Bez tego kroku konfiguracji skróty wymagane hasła nie będą obecne w domenie zarządzanej usług Azure AD DS, aby poprawnie uwierzytelnić znak podczas próby.
* Poczekaj na zakończenie synchronizacji haseł. Po zmianie hasła konta użytkownika automatyczna synchronizacja w tle z usługi Azure AD aktualizuje hasło w usługach Azure AD DS. Potrzeba trochę czasu, aby hasło było dostępne do użycia przy sprzężeniu domeny.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie maszyny Wirtualnej systemu Windows Server
> * Łączenie się z maszyną wirtualną systemu Windows Server z siecią wirtualną platformy Azure
> * Dołączanie maszyny Wirtualnej do domeny zarządzanej usług Azure AD DS

Aby administrować domeną zarządzaną usługą Azure AD DS, skonfiguruj maszynę wirtualną zarządzania przy użyciu Centrum administracyjnego usługi Active Directory (ADAC).

> [!div class="nextstepaction"]
> [Instalowanie narzędzi administracyjnych na maszynie Wirtualnej zarządzania](tutorial-create-management-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[password-sync]: active-directory-ds-getting-started-password-sync.md
[add-computer]: /powershell/module/microsoft.powershell.management/add-computer
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[set-azvmaddomainextension]: /powershell/module/az.compute/set-azvmaddomainextension
