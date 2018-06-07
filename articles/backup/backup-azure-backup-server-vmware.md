---
title: Wykonaj kopię zapasową serwerów VMware z serwera usługi Kopia zapasowa Azure
description: Serwer kopii zapasowej Azure umożliwia tworzenie kopii zapasowych serwery vCenter/ESXi VMware Azure lub dysku. W tym artykule przedstawiono krok = instrukcje krok po kroku dla tworzenia kopii zapasowych (lub ochrony) obciążeń VMware.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/24/2017
ms.author: adigan
ms.openlocfilehash: 9cf3c9d5df11e19045cd47a41d7ab9ac93bdf700
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605427"
---
# <a name="back-up-a-vmware-server-to-azure"></a>Utwórz kopię zapasową serwera VMware do platformy Azure

W tym artykule opisano sposób konfigurowania serwera kopii zapasowej Azure w celu ochrony obciążeń serwera VMware. W tym artykule przyjęto założenie, że masz już zainstalowany serwer kopii zapasowej Azure. Jeśli nie masz zainstalowany serwer kopii zapasowej Azure, zobacz [przygotowanie do tworzenia kopii zapasowych obciążeń przy użyciu serwera usługi Kopia zapasowa Azure](backup-azure-microsoft-azure-backup.md).

Serwer kopii zapasowej systemu Azure można utworzyć kopię zapasową lub lepiej chronić VMware vCenter Server w wersji 6.5, 6.0 i 5.5.


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Tworzenie bezpiecznego połączenia z serwerem vCenter

Domyślnie serwer kopii zapasowej Azure komunikuje się z każdym vCenter Server za pośrednictwem kanału HTTPS. Aby włączyć bezpiecznej komunikacji, zaleca się zainstalować certyfikat VMware urzędu certyfikacji na serwerze kopii zapasowej Azure. Jeśli nie wymagają bezpiecznej komunikacji i chcesz użyć wyłączyć wymaganie protokołu HTTPS, zobacz [wyłączanie bezpiecznej komunikacji protokołu](backup-azure-backup-server-vmware.md#disable-secure-communication-protocol). Do utworzenia bezpiecznego połączenia między serwerem kopia zapasowa Azure i programu vCenter Server, należy zaimportować zaufany certyfikat na serwerze kopii zapasowej Azure.

Zazwyczaj używasz przeglądarki na komputerze z serwerem kopia zapasowa Azure nawiązać połączenia z programem vCenter Server za pośrednictwem vSphere klienta sieci Web. Po raz pierwszy używasz przeglądarki serwer kopii zapasowej Azure nawiązać połączenia z programem vCenter Server, połączenie nie jest bezpieczne. Na poniższej ilustracji przedstawiono niezabezpieczone połączenie.

![Przykład niezabezpieczone połączenie z serwerem VMware](./media/backup-azure-backup-server-vmware/unsecure-url.png)

Aby rozwiązać ten problem, a utworzenia bezpiecznego połączenia, Pobierz certyfikaty zaufanego głównego urzędu certyfikacji.

1. Wprowadź adres URL vSphere klienta sieci Web w przeglądarce na serwerze kopii zapasowej Azure. Zostanie wyświetlona strona logowania klienta sieci Web vSphere.

    ![vSphere klienta sieci Web](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

    W dolnej części informacje dla administratorów i deweloperów, zlokalizuj **pobierania zaufane certyfikaty głównego urzędu certyfikacji** łącza.

    ![Łącze do pobrania certyfikatów zaufanego głównego urzędu certyfikacji](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

  Jeśli widzisz stronę logowania klienta sieci Web vSphere, sprawdź ustawienia serwera proxy przeglądarki.

2. Kliknij przycisk **pobierania zaufane certyfikaty głównego urzędu certyfikacji**.

    Serwer vCenter pobierze plik do komputera lokalnego. Nazwa pliku o nazwie **Pobierz**. W zależności od przeglądarki pojawi się komunikat z pytaniem, czy można otworzyć lub zapisać plik.

    ![Pobierz wiadomość, gdy są pobierane certyfikaty](./media/backup-azure-backup-server-vmware/download-certs.png)

3. Zapisz plik do lokalizacji na serwerze kopii zapasowej Azure. Podczas zapisywania pliku należy dodać rozszerzenie nazwy pliku zip.

    Plik jest plik zip, który zawiera informacje o certyfikatach. Z rozszerzeniem zip można użyć narzędzia wyodrębniania.

4. Kliknij prawym przyciskiem myszy **download.zip**, a następnie wybierz **Wyodrębnij wszystkie** wyodrębnienie zawartości.

    Plik zip wyodrębni jego zawartość do folderu o nazwie **certyfikaty**. Dwa typy plików znajdują się w folderze Certyfikaty. Plik certyfikatu głównego ma rozszerzenie, który rozpoczyna się od numeru sekwencji.0 i.1.
    
    Plik listy CRL ma rozszerzenie zaczyna się od sekwencji, takich jak .r0 lub .r1. Plik listy CRL jest skojarzony z certyfikatem.

    ![Pobierz plik wyodrębnione lokalnie ](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. W **certyfikaty** folderu, kliknij prawym przyciskiem myszy plik certyfikatu głównego, a następnie kliknij przycisk **zmienić**.

    ![Zmień nazwę certyfikatu głównego ](./media/backup-azure-backup-server-vmware/rename-cert.png)

    Zmień rozszerzenie certyfikatu głównego na .crt. Pytanie, jeśli masz pewności, aby zmienić rozszerzenie, kliknij przycisk **tak** lub **OK**. W przeciwnym razie możesz zmienić funkcji pliku. Ikona zmiany pliku ikony reprezentującej certyfikatu głównego.

6. Kliknij prawym przyciskiem myszy certyfikat główny i wybierz z menu podręcznego **Zainstaluj certyfikat**.

    **Kreatora importu certyfikatów** zostanie wyświetlone okno dialogowe.

7. W **Kreatora importu certyfikatów** okno dialogowe, wybierz opcję **komputera lokalnego** jako miejsce docelowe dla certyfikatu, a następnie kliknij przycisk **dalej** aby kontynuować.

    ![Opcje docelowy magazyn certyfikatów ](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

    Jeśli użytkownik jest proszony do umożliwienia wprowadzania zmian na komputerze, kliknij przycisk **tak** lub **OK**, aby wszystkie zmiany.

8. Na **magazyn certyfikatów** wybierz pozycję **Umieść wszystkie certyfikaty w następującym magazynie**, a następnie kliknij przycisk **Przeglądaj** wybierz magazyn certyfikatów.

    ![Umieść w miejscu określonego magazynu certyfikatów](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

    **Wybierz magazyn certyfikatów** zostanie wyświetlone okno dialogowe.

    ![Hierarchia folderów magazyn certyfikatów](./media/backup-azure-backup-server-vmware/cert-store.png)

9. Wybierz **zaufane główne urzędy certyfikacji** jako folder docelowy dla certyfikatów, a następnie kliknij przycisk **OK**.

    ![Folder docelowy certyfikatu](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

    **Zaufane główne urzędy certyfikacji** folder został potwierdzony jako magazyn certyfikatów. Kliknij przycisk **Dalej**.

    ![Folder magazynu certyfikatów](./media/backup-azure-backup-server-vmware/certificate-import-wizard2.png)

10. Na **Kończenie pracy Kreatora importu certyfikatów** , sprawdź, czy certyfikat odpowiedni folder, a następnie kliknij przycisk **Zakończ**.

    ![Sprawdź, czy certyfikat znajduje się w folderze właściwego](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

    Zostanie wyświetlone okno dialogowe, potwierdzono importu certyfikatów powiodło się.

11. Zaloguj się do programu vCenter Server, aby upewnić się, że połączenie jest bezpieczne.

  Jeśli import certyfikatu nie zostanie ukończone pomyślnie, a nie można ustanowić bezpiecznego połączenia, w dokumentacji programu VMware vSphere na [uzyskiwanie certyfikatów serwera](http://pubs.vmware.com/vsphere-60/index.jsp#com.vmware.wssdk.dsg.doc/sdk_sg_server_certificate_Appendixes.6.4.html).

  Jeśli masz bezpiecznych granic w ramach danej organizacji, a nie chcesz włączyć protokół HTTPS, użyj poniższej procedury można wyłączyć bezpiecznej komunikacji.

### <a name="disable-secure-communication-protocol"></a>Wyłącz protokół bezpiecznej komunikacji

Jeśli Twoja organizacja nie wymaga protokołu HTTPS, wykonaj następujące kroki, aby wyłączyć protokół HTTPS. Aby wyłączyć domyślne zachowanie, należy utworzyć klucz rejestru, który ignoruje domyślne zachowanie.

1. Skopiuj i wklej następujący tekst w pliku txt.

  ```
  Windows Registry Editor Version 5.00
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
  "IgnoreCertificateValidation"=dword:00000001
  ```

2. Zapisz plik na komputerze serwera usługi Kopia zapasowa Azure. Nazwa pliku można użyć DisableSecureAuthentication.reg.

3. Kliknij dwukrotnie plik, aby aktywować wpis rejestru.


## <a name="create-a-role-and-user-account-on-the-vcenter-server"></a>Utwórz konto rolę i użytkownika w programie vcenter Server

W programie vcenter Server Rola to wstępnie zdefiniowane uprawnienia. Administrator serwera vCenter tworzy ról. Aby przypisać uprawnienia, administrator pary kont użytkowników z roli. W celu ustalenia poświadczeń użytkownika niezbędne do tworzenia kopii zapasowych komputera serwera vCenter, utworzyć rolę z określone uprawnienia, a następnie skojarzyć konto użytkownika z rolą.

Serwer kopii zapasowej systemu Azure używa nazwy użytkownika i hasła do uwierzytelniania przez serwer vCenter. Serwer kopii zapasowej systemu Azure używa tych poświadczeń uwierzytelniania dla wszystkich operacji tworzenia kopii zapasowej.

Aby dodać rolę serwera vCenter i jego uprawnień administratora kopii zapasowych:

1. Zaloguj się do serwera vCenter, a następnie w programie vCenter Server **Nawigator** panelu, kliknij przycisk **administracji**.

    ![Opcja administracji w panelu Nawigator serwera vCenter](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

2. W **administracji** wybierz **ról**, a następnie w **ról** panelu kliknij ikonę roli Dodaj (+ symbol).

    ![Dodaj rolę](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

    **Utwórz rolę** zostanie wyświetlone okno dialogowe.

    ![Tworzenie roli](./media/backup-azure-backup-server-vmware/vmware-define-new-role-priv.png)

3. W **Utwórz rolę** okna dialogowego, **nazwy roli** wprowadź *BackupAdminRole*. Nazwa roli może być dowolne, ale powinien być rozpoznawalną w celu roli.

4. Wybierz uprawnienia do odpowiedniej wersji programu vCenter, a następnie kliknij przycisk **OK**. Poniższa tabela zawiera uprawnienia wymagane dla vCenter 6.0 i vCenter 5.5.

  Po wybraniu uprawnienia, kliknij ikonę obok etykiety nadrzędnego rozwiń węzeł nadrzędny i wyświetlić uprawnienia podrzędnych. Aby wybrać uprawnienia maszyny wirtualnej, musisz przejść kilka poziomów w hierarchii nadrzędny-podrzędny. Nie musisz wybrać wszystkie uprawnienia podrzędnego w obrębie uprawnienie nadrzędne.

  ![Uprawnienie hierarchii nadrzędny-podrzędny](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

  Po kliknięciu **OK**, nowej roli zostanie wyświetlony na liście w panelu ról.

|Uprawnienia dla serwera vCenter 6.0| Uprawnienia dla serwera vCenter 5,5|
|--------------------------|---------------------------|
|Datastore.AllocateSpace   | Datastore.AllocateSpace|
|Global.ManageCustomFields | Global.ManageCustomerFields|
|Global.SetCustomFields    |   |
|Host.Local.CreateVM       | Network.Assign |
|Network.Assign            |  |
|Resource.AssignVMToPool   |  |
|VirtualMachine.Config.AddNewDisk  | VirtualMachine.Config.AddNewDisk   |
|VirtualMachine.Config.AdvanceConfig| VirtualMachine.Config.AdvancedConfig|
|VirtualMachine.Config.ChangeTracking| VirtualMachine.Config.ChangeTracking |
|VirtualMachine.Config.HostUSBDevice||
|VirtualMachine.Config.QueryUnownedFiles|    |
|VirtualMachine.Config.SwapPlacement| VirtualMachine.Config.SwapPlacement |
|VirtualMachine.Interact.PowerOff| VirtualMachine.Interact.PowerOff |
|VirtualMachine.Inventory.Create| VirtualMachine.Inventory.Create |
|VirtualMachine.Provisioning.DiskRandomAccess| |
|VirtualMachine.Provisioning.DiskRandomRead|VirtualMachine.Provisioning.DiskRandomRead |
|VirtualMachine.State.CreateSnapshot| VirtualMachine.State.CreateSnapshot|
|VirtualMachine.State.RemoveSnapshot|VirtualMachine.State.RemoveSnapshot |
</br>



## <a name="create-a-vcenter-server-user-account-and-permissions"></a>Tworzenie konta użytkownika serwera vCenter i uprawnień

Po skonfigurowaniu roli z uprawnieniami, Utwórz konto użytkownika. Konto użytkownika ma nazwę i hasło, co zapewnia poświadczenia, które są używane do uwierzytelniania.

1. Aby utworzyć konto użytkownika w programie vCenter Server **Nawigator** panelu, kliknij przycisk **użytkowników i grup**.

    ![Opcja użytkowników i grup](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    **VCenter użytkowników i grup** pojawi się panel.

    ![panel vCenter użytkowników i grup](./media/backup-azure-backup-server-vmware/usersandgroups.png)

2. W **vCenter użytkowników i grup** panelu, wybierz opcję **użytkowników** , a następnie kliknij ikonę Dodaj użytkowników (+ symbol).

    **Nowego użytkownika** zostanie wyświetlone okno dialogowe.

3. W **nowego użytkownika** okna dialogowego, Dodaj informacje o użytkowniku a następnie kliknij przycisk **OK**. W tej procedurze nazwa użytkownika jest BackupAdmin.

    ![Okno dialogowe Nowy użytkownik](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

    Nowe konto użytkownika zostanie wyświetlony na liście.

4. Aby skojarzyć konto użytkownika z roli, w **Nawigator** panelu, kliknij przycisk **uprawnień globalnych**. W **uprawnień globalnych** panelu, wybierz opcję **Zarządzaj** , a następnie kliknij ikonę Dodaj (+ symbol).

    ![Panel uprawnień globalnych](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

    **Globalnych uprawnień Root - Dodaj uprawnienie** zostanie wyświetlone okno dialogowe.

5. W **globalnych uprawnień Root - Dodaj uprawnienie** okno dialogowe, kliknij przycisk **Dodaj** można wybrać użytkownika lub grupę.

    ![Wybierz użytkownika lub grupy](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

    **Użytkowników/grupy wybierz** zostanie wyświetlone okno dialogowe.

6. W **użytkowników/grupy wybierz** oknie dialogowym wybierz **BackupAdmin** , a następnie kliknij przycisk **Dodaj**.

    W **użytkowników**, *domena azwa_użytkownika* format jest używany dla konta użytkownika. Jeśli chcesz użyć innej domeny, wybierz go z **domeny** listy.

    ![Dodaj użytkownika BackupAdmin](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

    Kliknij przycisk **OK** można dodać wybranych użytkowników **dodać uprawnienia** okno dialogowe.

7. Teraz, gdy wyłaniają się użytkownik, przypisz użytkownika do roli. W **przypisana rola**, z listy rozwijanej wybierz **BackupAdminRole**, a następnie kliknij przycisk **OK**.

    ![Przypisz użytkownika do roli](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

  Na **Zarządzaj** karcie **uprawnień globalnych** panelu, nowe konto użytkownika i roli skojarzone są wyświetlane na liście.


## <a name="establish-vcenter-server-credentials-on-azure-backup-server"></a>Ustanowić poświadczenia serwera vCenter na serwerze kopia zapasowa Azure

Przed dodaniem serwera VMware do serwera usługi Kopia zapasowa Azure, zainstaluj [aktualizacji 1 dla serwera usługi Kopia zapasowa Azure](https://support.microsoft.com/help/3175529/update-1-for-microsoft-azure-backup-server).

1. Aby otworzyć serwer kopii zapasowej Azure, kliknij dwukrotnie ikonę na pulpicie serwera kopii zapasowej Azure.

    ![Ikona serwera kopia zapasowa Azure](./media/backup-azure-backup-server-vmware/mabs-icon.png)

    Jeśli nie możesz znaleźć ikony na pulpicie, należy otworzyć serwer kopii zapasowej Azure z listy zainstalowanych aplikacji. Nazwa aplikacji serwera usługi Kopia zapasowa Azure jest nazywany kopia zapasowa Microsoft Azure.

2. W konsoli serwera usługi Kopia zapasowa Azure, kliknij **zarządzania**, kliknij przycisk **serwerów produkcyjnych**, a następnie na wstążce narzędzi kliknij przycisk **Zarządzanie VMware**.

    ![Konsoli serwera kopii zapasowej platformy Azure](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

    **Poświadczenia zarządzania** zostanie wyświetlone okno dialogowe.

    ![Okno dialogowe poświadczenia zarządzania serwera kopii zapasowej systemu Azure](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

3. W **poświadczenia zarządzania** okno dialogowe, kliknij przycisk **Dodaj** otworzyć **Dodaj poświadczenie** okno dialogowe.

4. W **Dodaj poświadczenie** okna dialogowego wprowadź nazwę i opis nowych poświadczeń. Następnie określ nazwę użytkownika i hasło. Nazwa, *poświadczeń Contoso Vcenter* służy do identyfikowania poświadczeń w następnej procedurze. Użyj tej samej nazwy użytkownika i hasło, które jest używane dla programu vCenter Server. Jeśli programu vCenter Server i serwer kopii zapasowej Azure nie są w tej samej domenie, w **nazwy użytkownika**, określ nazwę domeny.

    ![Okno dialogowe poświadczeń dodawania serwera kopii zapasowej platformy Azure](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

    Kliknij przycisk **Dodaj** do dodania nowych poświadczeń do serwera usługi Kopia zapasowa Azure. Nowe poświadczenie pojawia się na liście w **poświadczenia zarządzania** okno dialogowe.
    
    ![Okno dialogowe poświadczenia zarządzania serwera kopii zapasowej systemu Azure](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

5. Aby zamknąć **poświadczenia zarządzania** okno dialogowe, kliknij przycisk **X** w prawym górnym rogu.


## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Dodaj serwer vCenter do serwera usługi Kopia zapasowa Azure

Kreator dodawania serwera produkcyjnego jest używany, aby dodać serwer vCenter do serwera usługi Kopia zapasowa Azure.

Aby otworzyć Kreatora dodawania serwera produkcyjnego, wykonaj następującą procedurę:

1. W konsoli serwera usługi Kopia zapasowa Azure kliknij **zarządzania**, kliknij przycisk **serwerów produkcyjnych**, a następnie kliknij przycisk **Dodaj**.

    ![Kreator dodawania serwera Otwórz produkcji](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

    **Kreator dodawania serwera produkcyjnego** zostanie wyświetlone okno dialogowe.

    ![Kreator dodawania serwera produkcyjnego](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

2. Na **typu wybierz serwer produkcyjny** wybierz **serwerów VMware**, a następnie kliknij przycisk **dalej**.

3. W **adres IP/nazwa serwera**, określ w pełni kwalifikowaną nazwę domeny (FQDN) lub adres IP serwera VMware. Jeśli wszystkie serwery ESXi są zarządzane przez ten sam program vCenter, można użyć nazwy vCenter.

    ![Określ adres FQDN lub adres IP serwera VMware](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. W **SSL Port**, wprowadź port, który jest używany do komunikacji z serwerem VMware. Korzysta z portu 443, który jest domyślnym portem, jeśli nie wiadomo, że inny port jest wymagana.

5. W **Określ poświadczenia**, wybierz poświadczenie, który został utworzony wcześniej.

    ![Określ poświadczenia](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Kliknij przycisk **Dodaj** Aby dodać serwer VMware do listy **dodane serwery VMware**, a następnie kliknij przycisk **dalej** przejście do następnej strony kreatora.

    ![Dodawanie serwera VMWare i poświadczenia](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. W **Podsumowanie** kliknij przycisk **Dodaj** można dodać określony serwer VMware do serwera usługi Kopia zapasowa Azure.

    ![Dodaj serwer VMware do platformy Azure kopii zapasowej serwera](./media/backup-azure-backup-server-vmware/tasks-screen.png)

  Kopia zapasowa serwera VMware jest bez agenta kopii zapasowej i natychmiast jest dodawany nowy serwer. **Zakończ** strony pokazuje wyniki.

  ![Zakończ strony](./media/backup-azure-backup-server-vmware/summary-screen.png)

  Aby dodać wiele wystąpień programu vCenter Server do serwera usługi Kopia zapasowa Azure, powtórz poprzednie kroki w tej sekcji.

Po dodaniu oprogramowania vCenter Server do serwera usługi Kopia zapasowa Azure, następnym krokiem jest utworzenie grupy ochrony. Grupy ochrony określa różne szczegóły krótki i długoterminowego przechowywania i gdzie Zdefiniuj i zastosowania zasad tworzenia kopii zapasowej. Zasady tworzenia kopii zapasowej jest harmonogram, gdy kopie zapasowe są wykonywane i kopii zapasowej.


## <a name="configure-a-protection-group"></a>Skonfiguruj grupę ochrony

Jeśli nie używasz programu System Center Data Protection Manager lub serwer kopii zapasowej Azure przed, zobacz [Planowanie kopii zapasowych na dyskach](https://technet.microsoft.com/library/hh758026.aspx) przygotować środowisko sprzętowe. Po sprawdzeniu, czy masz odpowiednie magazynu, użyj Kreatora tworzenia nowej grupy ochrony można dodać maszyny wirtualne VMware.

1. W konsoli serwera usługi Kopia zapasowa Azure kliknij **ochrony**i na wstążce narzędzi kliknij **nowy** aby otworzyć Kreatora tworzenia nowej grupy ochrony.

    ![Otwórz Kreatora tworzenia nowej grupy ochrony](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

    **Tworzenia nowej grupy ochrony** zostanie wyświetlone okno dialogowe Kreator.

    ![Okno dialogowe Kreator tworzenia nowej grupy ochrony](./media/backup-azure-backup-server-vmware/protection-wizard.png)

    Kliknij przycisk **dalej** aby przejść do **wybierz typ grupy ochrony** strony.

2. Na **typ grupy ochrony wybierz** wybierz **serwerów** , a następnie kliknij przycisk **dalej**. **Wybierz członków grupy** zostanie wyświetlona strona.

3. Na **Wybierz członków grupy** strony i dostępne elementy członkowskie zaznaczone elementy członkowskie są wyświetlane. Wybierz elementy członkowskie, które chcesz chronić, a następnie kliknij przycisk **dalej**.

    ![Wybierz członków grupy](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

    Po wybraniu członka, w przypadku wybrania folderu zawierającego inne foldery lub maszyn wirtualnych, wybrane zostaną także te foldery i maszyn wirtualnych. Włączenie folderów i maszyn wirtualnych w folderze nadrzędnym jest nazywany ochrony na poziomie folderu. Aby usunąć folder lub maszyny Wirtualnej, wyczyść pole wyboru.

    Jeśli maszyna wirtualna lub folder zawierający maszyny Wirtualnej, jest już chronione na platformie Azure, nie możesz wybrać tej maszyny Wirtualnej ponownie. Oznacza to, że po włączeniu ochrony maszyny Wirtualnej na platformie Azure, nie można go chronić ponownie, która zapobiega utworzeniu dla jednej maszyny Wirtualnej przez punkty odzyskiwania zduplikowane. Jeśli chcesz zobaczyć, których wystąpienia serwera usługi Kopia zapasowa Azure chroni już element członkowski, wskaż polecenie element członkowski, aby wyświetlić nazwę serwera, ochrona.

4. Na **wybierz metodę ochrony danych** strony, wprowadź nazwę grupy ochrony. Wybrano ochrony krótkoterminowej (na dysku) i ochrony w trybie online. Jeśli chcesz użyć ochrony w trybie online (na platformie Azure), należy użyć krótkoterminowej ochrony na dysku. Kliknij przycisk **dalej** do przejścia do zakresu ochrony krótkoterminowej.

    ![Wybierz metodę ochrony danych](./media/backup-azure-backup-server-vmware/name-protection-group.png)

5. Na **Określ cele krótkoterminowe** strony, aby uzyskać **zakres przechowywania**, określ liczbę dni, które chcesz zachować punktów odzyskiwania, które są *przechowywanych na dysku*. Jeśli chcesz zmienić czas i dni, po pobierane są punkty odzyskiwania, kliknij przycisk **Modyfikuj**. Krótkoterminowe punkty odzyskiwania są pełne kopie zapasowe. Nie są one przyrostowych kopii zapasowych. Po zakończeniu krótkoterminowych celów w zakresie kliknij **dalej**.

    ![Określanie celów krótkoterminowych](./media/backup-azure-backup-server-vmware/short-term-goals.png)

6. Na **Przejrzyj przydział dysku** Przejrzyj i w razie potrzeby zmodyfikuj miejsce na dysku dla maszyn wirtualnych. Zalecane przydziały dysków są oparte na zakres przechowywania, która została określona w **Określ cele krótkoterminowe** stron, typu obciążenia i rozmiaru chronionych danych (określoną w kroku 3).  

  - **Rozmiar danych:** rozmiar danych w grupie ochrony.
  - **Miejsce na dysku:** zalecana ilość miejsca na dysku dla grupy ochrony. Jeśli chcesz zmodyfikować to ustawienie, należy przydzielić całkowita ilość miejsca nieznacznie przekraczającą kwota oceniasz, że rozwoju każdego źródła danych.
  - **Przekazywanie danych do wspólnej lokalizacji:** po włączeniu kolokacji wolumin punktu danych w wielu źródeł w zakresie ochrony można mapować na pojedynczą replikę i odzyskiwania. Kolokacja nie jest obsługiwana dla wszystkich obciążeń.
  - **Automatycznie rozszerzaj:** po włączeniu tego ustawienia, jeśli dane w grupie ochrony przekroczy początkowe alokacji, System Center Data Protection Manager próbuje zwiększyć rozmiar dysku o 25 procent.
  - **Szczegóły puli magazynu:** pokazuje stan puli magazynu, w tym całkowity rozmiar dysku i wolnego.

    ![Przejrzyj przydział dysku](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

    Po zakończeniu przydziału miejsca, kliknij przycisk **dalej**.

7. Na **wybierz metodę tworzenia repliki** Określ, jak chcesz wygenerować kopii początkowej lub repliki chronionych danych na serwerze kopii zapasowej Azure.

    Wartość domyślna to **automatycznie przez sieć** i **teraz**. Jeśli użytkownik korzysta z domyślnych, zaleca się określenie poza godzinami szczytu. Wybierz **później** i określ dzień i godzinę.

    Dla dużych ilości danych lub mniej niż optymalnych warunkach sieci należy wziąć pod uwagę replikację danych w trybie offline przy użyciu nośnika wymiennego.

    Po dokonaniu wybrane opcje, kliknij przycisk **dalej**.

    ![Wybierz metodę tworzenia repliki](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. Na **opcje sprawdzania spójności** strony, wybierz, jak i kiedy automatyzacji sprawdzania spójności. Można uruchomić sprawdzania spójności, gdy dane są niespójne, lub zgodnie z ustalonym harmonogramem.

    Jeśli nie chcesz skonfigurować automatycznej kontroli spójności, możesz uruchomić sprawdzanie ręczne. W obszarze ochrony konsoli serwera usługi Kopia zapasowa Azure, kliknij prawym przyciskiem myszy grupę ochrony, a następnie wybierz **Przeprowadź Sprawdzanie spójności**.

    Kliknij przycisk **dalej** przejście do następnej strony.

9. Na **Określ dane chronione Online** wybierz jeden lub więcej źródeł danych, które chcesz chronić. Wybierz elementy członkowskie, pojedynczo lub kliknij przycisk **Zaznacz wszystko** aby wybrać wszystkie elementy członkowskie. Po wybraniu elementów członkowskich, kliknij przycisk **dalej**.

    ![Określ dane chronione w trybie online](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

10. Na **określić harmonogram tworzenia kopii zapasowych Online** Określ harmonogram, aby wygenerować punkty odzyskiwania z kopii zapasowej. Po wygenerowaniu punkt odzyskiwania, są przesyłane do magazynu usług odzyskiwania na platformie Azure. Po zakończeniu harmonogram tworzenia kopii zapasowej online, kliknij przycisk **dalej**.

    ![Określ harmonogram tworzenia kopii zapasowej online](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

11. Na **Określanie zasad przechowywania Online** wskaż, jak długo mają być przechowywane dane kopii zapasowej na platformie Azure. Po zdefiniowaniu zasad, kliknij przycisk **dalej**.

    ![Określ zasady przechowywania danych online](./media/backup-azure-backup-server-vmware/retention-policy.png)

    Nie ma żadnego limitu czasu na jak długo dane można zachować na platformie Azure. Danych punktu odzyskiwania są przechowywane na platformie Azure, tylko limit to, że nie może mieć więcej niż 9999 punkty odzyskiwania dla każdego chronionego wystąpienia. W tym przykładzie chronione wystąpienia jest serwer VMware.

12. Na **Podsumowanie** , przejrzyj szczegóły dla członków grupy ochrony i ustawienia, a następnie kliknij przycisk **Utwórz grupę**.

    ![Członka grupy ochrony i ustawienia — podsumowanie](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="next-steps"></a>Kolejne kroki
Jeśli serwer kopii zapasowej Azure umożliwia ochronę obciążeń VMware, może Cię zainteresować za pomocą serwera usługi Kopia zapasowa Azure w celu ochrony [programu Microsoft Exchange server](./backup-azure-exchange-mabs.md), [farmy Microsoft SharePoint](./backup-azure-backup-sharepoint-mabs.md), lub [bazy danych programu SQL Server](./backup-azure-sql-mabs.md).

Informacje na problemy z zarejestrowaniem agenta, konfigurowania grupy ochrony lub tworzenie kopii zapasowych zadań, zobacz [Rozwiązywanie problemów z serwera usługi Kopia zapasowa Azure](./backup-azure-mabs-troubleshoot.md).
