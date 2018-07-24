---
title: Tworzenie kopii zapasowych serwerów VMware za pomocą usługi Azure Backup Server
description: Użyj usługi Azure Backup Server do utworzenia kopii zapasowej serwerów vCenter/ESXi VMware do platformy Azure lub dysku. Ten artykuł zawiera krok = instrukcji krok po kroku do tworzenia kopii zapasowych (lub ochronę) obciążeń oprogramowania VMware.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/24/2017
ms.author: adigan
ms.openlocfilehash: ce7b255359c076ddae642ed44f056e444b655e25
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216414"
---
# <a name="back-up-a-vmware-server-to-azure"></a>Tworzenie kopii zapasowej serwera VMware na platformę Azure

W tym artykule opisano sposób konfigurowania usługi Azure Backup Server, aby chronić obciążenia serwera VMware. W tym artykule przyjęto założenie, że masz już zainstalowany serwer usługi Azure Backup. Jeśli nie masz zainstalowany serwer usługi Azure Backup, zobacz [przygotowanie do tworzenia kopii zapasowych obciążeń przy użyciu usługi Azure Backup Server](backup-azure-microsoft-azure-backup.md).

Usługa Azure Backup Server, można utworzyć kopię zapasową lub Łatwiejsza ochrona VMware vCenter Server w wersji 6.5, 6.0 i 5.5.


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Utwórz bezpieczne połączenie z serwerem vcenter

Domyślnie usługi Azure Backup Server komunikuje się z każdego serwera vCenter za pośrednictwem kanału protokołu HTTPS. Aby włączyć bezpiecznej komunikacji, firma Microsoft zaleca instalowanie certyfikatu VMware urząd certyfikacji (CA) na serwer usługi Azure Backup. Jeśli nie wymagają bezpiecznej komunikacji i chcesz użyć wyłączyć wymaganie protokołu HTTPS, zobacz [wyłączanie bezpiecznej komunikacji protokołu](backup-azure-backup-server-vmware.md#disable-secure-communication-protocol). Aby utworzyć bezpieczne połączenie między usługi Azure Backup Server i serwer vCenter, zaimportuj zaufanego certyfikatu w usłudze Azure Backup Server.

Zazwyczaj używasz przeglądarki na komputerze usługi Azure Backup Server do łączenia z programem vCenter Server za pośrednictwem kliencie internetowym vSphere. Po raz pierwszy używasz przeglądarki usługi Azure Backup Server do łączenia z programem vCenter Server, połączenie nie jest bezpieczne. Na poniższej ilustracji przedstawiono niezabezpieczone połączenie.

![Przykład niezabezpieczone połączenie z serwerem VMware](./media/backup-azure-backup-server-vmware/unsecure-url.png)

Aby rozwiązać ten problem i utworzenia bezpiecznego połączenia, Pobierz certyfikatów zaufanego głównego urzędu certyfikacji.

1. W przeglądarce w usłudze Azure Backup Server wprowadź adres URL na kliencie internetowym vSphere. Zostanie wyświetlona strona logowania programu vSphere klienta sieci Web.

    ![Kliencie internetowym vSphere](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

    W dolnej części informacje dla administratorów i deweloperów Znajdź **pobierania zaufane certyfikaty głównego urzędu certyfikacji** łącza.

    ![Link do pobrania certyfikatów zaufanego głównego urzędu certyfikacji](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

  Jeśli nie zostanie wyświetlona strona logowania klienta sieci Web vSphere, sprawdź ustawienia serwera proxy w przeglądarce.

2. Kliknij przycisk **pobierania zaufane certyfikaty głównego urzędu certyfikacji**.

    W programie vCenter Server pobierze plik do komputera lokalnego. Nazwa pliku o nazwie **Pobierz**. W zależności od przeglądarki pojawi się komunikat z pytaniem, czy chcesz otworzyć lub zapisać plik.

    ![Pobierz wiadomość, gdy są pobierane certyfikaty](./media/backup-azure-backup-server-vmware/download-certs.png)

3. Zapisz plik do lokalizacji w usłudze Azure Backup Server. Podczas zapisywania pliku należy dodać rozszerzenie nazwy pliku zip.

    Plik jest plik zip, który zawiera informacje o certyfikatach. Z rozszerzeniem .zip można użyć narzędzia do wyodrębniania.

4. Kliknij prawym przyciskiem myszy **download.zip**, a następnie wybierz pozycję **Wyodrębnij wszystkie** aby wyodrębnić zawartość.

    Plik zip wyodrębni jego zawartość do folderu o nazwie **certyfikatów**. Dwa typy plików są wyświetlane w folderze certyfikatów. Plik certyfikatu głównego ma rozszerzenie rozpoczynające się od numerowane sekwencji, takich jak.0 i.1.
    
    Plik listy CRL ma rozszerzenie, które zaczyna się od sekwencji, takich jak .r0 lub .r1. Plik listy CRL jest skojarzony z certyfikatem.

    ![Pobierz plik wyodrębnione lokalnie ](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. W **certyfikatów** folderu, kliknij prawym przyciskiem myszy plik certyfikatu głównego, a następnie kliknij przycisk **Zmień nazwę**.

    ![Zmień nazwę certyfikatu głównego ](./media/backup-azure-backup-server-vmware/rename-cert.png)

    Zmień rozszerzenie certyfikatu głównego na .crt. Gdy pojawi się pytanie, na pewno chcesz zmienić rozszerzenie, kliknij przycisk **tak** lub **OK**. W przeciwnym razie możesz zmienić funkcji tego pliku. Ikona pliku zmienia się ikona reprezentująca certyfikatu głównego.

6. Kliknij prawym przyciskiem myszy certyfikat główny, a następnie w menu podręcznym wybierz **Zainstaluj certyfikat**.

    **Kreatora importu certyfikatów** pojawi się okno dialogowe.

7. W **Kreatora importu certyfikatów** okno dialogowe, wybierz opcję **komputera lokalnego** jako miejsce docelowe dla certyfikatu, a następnie kliknij **dalej** aby kontynuować.

    ![Opcje docelowy magazyn certyfikatów ](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

    Jeśli zostanie wyświetlony monit, aby zmiany do komputera, kliknij przycisk **tak** lub **OK**, aby wszystkie zmiany.

8. Na **Store certyfikatu** wybierz opcję **Umieść wszystkie certyfikaty w następującym magazynie**, a następnie kliknij przycisk **Przeglądaj** wybrać magazyn certyfikatów.

    ![Umieść certyfikaty w miejscu określonego magazynu](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

    **Store certyfikatu wybierz** pojawi się okno dialogowe.

    ![Hierarchia folderów magazyn certyfikatów](./media/backup-azure-backup-server-vmware/cert-store.png)

9. Wybierz **zaufane główne urzędy certyfikacji** jako folder docelowy dla certyfikatów, a następnie kliknij **OK**.

    ![Folder docelowy certyfikatu](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

    **Zaufane główne urzędy certyfikacji** folder został potwierdzony jako magazynu certyfikatów. Kliknij przycisk **Dalej**.

    ![Folder magazynu certyfikatów](./media/backup-azure-backup-server-vmware/certificate-import-wizard2.png)

10. Na **Kończenie pracy Kreatora importu certyfikatów** strony, sprawdź, czy certyfikat znajduje się w żądany folder, a następnie kliknij **Zakończ**.

    ![Sprawdź, czy certyfikat znajduje się w folderze odpowiednie](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

    Zostanie wyświetlone okno dialogowe, importu certyfikatów pomyślnie został potwierdzony.

11. Zaloguj się do serwera vCenter, aby upewnić się, że połączenie jest bezpieczne.

  Jeśli import certyfikatu nie zakończy się pomyślnie, a nie można ustanowić bezpiecznego połączenia, zapoznaj się z dokumentacją programu VMware vSphere na [uzyskiwanie certyfikatów serwera](http://pubs.vmware.com/vsphere-60/index.jsp#com.vmware.wssdk.dsg.doc/sdk_sg_server_certificate_Appendixes.6.4.html).

  Jeśli mają bezpieczny granice w Twojej organizacji, a nie chcesz włączyć protokół HTTPS, poniższej procedury można wyłączyć bezpieczną komunikację.

### <a name="disable-secure-communication-protocol"></a>Wyłącz protokół bezpiecznej komunikacji

Jeśli Twoja organizacja nie wymaga protokołu HTTPS, wykonaj następujące kroki, aby wyłączyć protokół HTTPS. Aby wyłączyć to zachowanie domyślne, należy utworzyć klucz rejestru, który ignoruje domyślne zachowanie.

1. Skopiuj i wklej następujący tekst do pliku txt.

  ```
  Windows Registry Editor Version 5.00
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
  "IgnoreCertificateValidation"=dword:00000001
  ```

2. Zapisz plik na komputer serwera usługi Azure Backup. Nazwa pliku musi zawierać DisableSecureAuthentication.reg.

3. Kliknij dwukrotnie plik, aby aktywować wpisu rejestru.


## <a name="create-a-role-and-user-account-on-the-vcenter-server"></a>Tworzenie roli i użytkownika konta w programie vcenter Server

W programie vcenter Server Rola to wstępnie zdefiniowany zestaw uprawnień. Administrator serwera vCenter tworzy ról. Aby przypisać uprawnienia, administrator pary kont użytkowników z rolą. Aby ustanowić poświadczenia użytkownika niezbędne do tworzenia kopii zapasowych na komputerze serwera vCenter, Utwórz rolę z uprawnieniami określonego, a następnie skojarzyć konto użytkownika z roli.

Usługa Azure Backup Server używa nazwy użytkownika i hasła do uwierzytelniania przy użyciu programu vCenter Server. Usługa Azure Backup Server przy użyciu tych poświadczeń uwierzytelniania dla wszystkich operacji tworzenia kopii zapasowej.

Aby dodać rolę serwera vCenter i jego uprawnień dla administratora kopii zapasowych:

1. Zaloguj się do serwera vCenter, a następnie w programie vCenter Server **Nawigator** panelu, kliknij przycisk **administracji**.

    ![Opcja administracji w panelu Nawigator serwera vCenter](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

2. W **administracji** wybierz **role**, a następnie w polu **role** panelu kliknij ikonę roli Dodaj (+ symbol).

    ![Dodaj rolę](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

    **Utwórz rolę** pojawi się okno dialogowe.

    ![Tworzenie roli](./media/backup-azure-backup-server-vmware/vmware-define-new-role-priv.png)

3. W **Utwórz rolę** dialogowym **nazwy roli** wprowadź *BackupAdminRole*. Nazwa roli może być dowolną, ale powinien być rozpoznawalną w celu roli.

4. Wybierz uprawnienia dla odpowiedniej wersji programu vCenter, a następnie kliknij przycisk **OK**. W poniższej tabeli przedstawiono wymagane uprawnienia do programu vCenter 6.0 i vCenter 5.5.

  Po wybraniu uprawnień, kliknij ikonę obok etykiecie nadrzędnej, aby rozwinąć element nadrzędny i wyświetlić uprawnienia podrzędnych. Aby wybrać uprawnień VirtualMachine, musisz przejść kilka poziomów w hierarchii nadrzędny-podrzędny. Nie potrzebujesz wybrać wszystkie uprawnienia podrzędnego w obrębie uprawnienie nadrzędne.

  ![Uprawnienie hierarchii nadrzędny-podrzędny](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

  Po kliknięciu **OK**, nowa rola zostanie wyświetlona na liście w panelu ról.

|Uprawnienia dla vCenter 6.0 i 6.5| Uprawnienia dla vCenter 5.5|
|----------------------------------|---------------------------|
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



## <a name="create-a-vcenter-server-user-account-and-permissions"></a>Tworzenie konta użytkownika serwera vCenter i uprawnienia

Po skonfigurowaniu roli z uprawnieniami, Utwórz konto użytkownika. Konto użytkownika ma nazwę i hasło, które podaje poświadczenia, które są używane do uwierzytelniania.

1. Aby utworzyć konto użytkownika w programie vCenter Server **Nawigator** panelu, kliknij przycisk **użytkowników i grup**.

    ![Opcja użytkowników i grup](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    **VCenter użytkowników i grup** zostanie wyświetlony panel.

    ![panel vCenter użytkowników i grup](./media/backup-azure-backup-server-vmware/usersandgroups.png)

2. W **vCenter użytkowników i grup** panelu wybierz **użytkowników** kartę, a następnie kliknij ikonę Dodaj użytkowników (+ symbol).

    **Nowego użytkownika** pojawi się okno dialogowe.

3. W **nowego użytkownika** okna dialogowego pole, Dodaj informacje o użytkowniku, a następnie kliknij przycisk **OK**. W tej procedurze nazwa użytkownika jest BackupAdmin.

    ![Okno dialogowe Nowy użytkownik](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

    Nowe konto użytkownika zostanie wyświetlony na liście.

4. Aby skojarzyć konto użytkownika z roli, w **Nawigator** panelu, kliknij przycisk **uprawnień globalnych**. W **uprawnień globalnych** panelu wybierz **Zarządzaj** kartę, a następnie kliknij ikonę Dodaj (+ symbol).

    ![Globalne panelu uprawnień](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

    **Globalnych uprawnień Root - Dodaj uprawnienia** pojawi się okno dialogowe.

5. W **globalnych uprawnień Root - Dodaj uprawnienia** okno dialogowe, kliknij przycisk **Dodaj** wybrać użytkownika lub grupy.

    ![Wybierz użytkownika lub grupy](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

    **Wybierz użytkowników i grup** pojawi się okno dialogowe.

6. W **Wybierz użytkowników i grup** okna dialogowego wybierz **BackupAdmin** a następnie kliknij przycisk **Dodaj**.

    W **użytkowników**, *domena\nazwa_użytkownika* format jest używany dla konta użytkownika. Jeśli chcesz użyć w innej domenie, wybierz go z **domeny** listy.

    ![Dodaj użytkownika BackupAdmin](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

    Kliknij przycisk **OK** można dodać wybranych użytkowników do **Dodaj uprawnienia** okno dialogowe.

7. Skoro już zidentyfikować użytkownika, należy przypisać użytkownika do roli. W **przypisana rola**, wybierz z listy rozwijanej **BackupAdminRole**, a następnie kliknij przycisk **OK**.

    ![Przypisz użytkownika do roli](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

  Na **Zarządzaj** karcie **uprawnień globalnych** panelu, nowe konto użytkownika i rolę skojarzone są wyświetlane na liście.


## <a name="establish-vcenter-server-credentials-on-azure-backup-server"></a>Ustanowić poświadczenia serwera vCenter w usłudze Azure Backup Server

Przed dodaniem serwera VMware do platformy Azure Backup Server, zainstaluj [aktualizacji 1 dla usługi Azure Backup Server](https://support.microsoft.com/help/3175529/update-1-for-microsoft-azure-backup-server).

1. Aby otworzyć przystawkę usługi Azure Backup Server, kliknij dwukrotnie ikonę na pulpicie serwera usługi Azure Backup.

    ![Ikona usługi Azure Backup Server](./media/backup-azure-backup-server-vmware/mabs-icon.png)

    Jeśli nie możesz znaleźć ikony na pulpicie, Otwórz serwer usługi Azure Backup na liście zainstalowanych aplikacji. Nazwa aplikacji usługi Azure Backup Server nosi nazwę kopia zapasowa Microsoft Azure.

2. Kliknij w konsoli usługi Azure Backup Server **zarządzania**, kliknij przycisk **serwerów produkcyjnych**, a następnie na wstążce narzędzi kliknij polecenie **Zarządzanie VMware**.

    ![Usługa Azure Backup Server konsoli](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

    **Poświadczenia zarządzania** pojawi się okno dialogowe.

    ![Okno dialogowe poświadczenia zarządzania serwera kopii zapasowych platformy Azure](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

3. W **poświadczenia zarządzania** okno dialogowe, kliknij przycisk **Dodaj** otworzyć **Dodaj poświadczenie** okno dialogowe.

4. W **Dodaj poświadczenie** okna dialogowego wprowadź nazwę i opis dla nowego poświadczenia. Następnie określ nazwę użytkownika i hasło. Nazwa, *poświadczeń Contoso Vcenter* służy do identyfikowania poświadczeń w następnej procedurze. Użyj tej samej nazwy użytkownika i hasła, używany w przypadku serwera vCenter. Jeśli programu vCenter Server i serwer usługi Azure Backup nie są w tej samej domenie, w **nazwa_użytkownika**, określ nazwę domeny.

    ![Okno dialogowe Azure Backup serwer Dodaj poświadczenie](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

    Kliknij przycisk **Dodaj** można dodać nowe poświadczenia do usługi Azure Backup Server. Nowe poświadczenie pojawia się na liście w **poświadczenia zarządzania** okno dialogowe.
    
    ![Okno dialogowe poświadczenia zarządzania serwera kopii zapasowych platformy Azure](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

5. Aby zamknąć **poświadczenia zarządzania** okno dialogowe, kliknij przycisk **X** w prawym górnym rogu.


## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Dodawanie serwera vCenter do usługi Azure Backup Server

Kreator dodawania serwerów produkcyjnych służy do dodawania serwera vCenter do serwera usługi Azure Backup.

Aby otworzyć Kreator dodawania serwerów produkcyjnych, wykonaj następującą procedurę:

1. Kliknij w konsoli usługi Azure Backup Server **zarządzania**, kliknij przycisk **serwerów produkcyjnych**, a następnie kliknij przycisk **Dodaj**.

    ![Kreator dodawania serwerów produkcyjnych Otwórz](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

    **Kreator dodawania serwerów produkcyjnych** pojawi się okno dialogowe.

    ![Kreator dodawania serwerów produkcyjnych](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

2. Na **typ serwera produkcyjnego wybierz** wybierz **serwery VMware**, a następnie kliknij przycisk **dalej**.

3. W **nazwa/adres IP serwera**, określ w pełni kwalifikowaną nazwę domeny (FQDN) lub adres IP serwera VMware. Jeśli wszystkie serwery ESXi są zarządzane przez ten sam program vCenter, można użyć nazwy vCenter.

    ![Określ adres FQDN lub adres IP serwera VMware](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. W **SSL Port**, wprowadź numer portu, który jest używany do komunikacji z serwerem VMware. Użyj portu 443, który jest domyślnym portem, jeśli nie masz pewności, że inny port jest wymagane.

5. W **Określ poświadczenia**, wybierz poświadczenie, która została utworzona wcześniej.

    ![Określ poświadczenie](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Kliknij przycisk **Dodaj** dodać serwer VMware do listy **dodane serwery VMware**, a następnie kliknij przycisk **dalej** aby przejść do następnej strony kreatora.

    ![Dodawanie serwera VMWare i poświadczeń](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. W **Podsumowanie** kliknij **Dodaj** można dodać określony serwer VMware do usługi Azure Backup Server.

    ![Dodaj serwer programu VMware do usługi Azure Backup Server](./media/backup-azure-backup-server-vmware/tasks-screen.png)

  Kopia zapasowa serwera VMware jest bez agenta kopii zapasowej, a następnie natychmiast jest dodawany nowy serwer. **Zakończ** strona przedstawia wyniki.

  ![Zakończ stronę](./media/backup-azure-backup-server-vmware/summary-screen.png)

  Aby dodać wiele wystąpień programu vCenter Server do usługi Azure Backup Server, powtórz poprzednie kroki w tej sekcji.

Po dodaniu oprogramowania vCenter Server do usługi Azure Backup Server, następnym krokiem jest utworzenie grupy ochrony. Grupy ochrony określa różne szczegóły krótki i długoterminowego przechowywania i to, gdzie definiowanie i stosowanie zasad tworzenia kopii zapasowej. Zasady kopii zapasowych są harmonogramem gdy wykonywane kopie zapasowe i co to jest wykonywana kopia zapasowa.


## <a name="configure-a-protection-group"></a>Skonfiguruj grupę ochrony

Jeśli nie używasz programu System Center Data Protection Manager lub Azure Backup Server przed, zobacz [Planowanie kopii zapasowych na dyskach](https://technet.microsoft.com/library/hh758026.aspx) przygotować środowisko sprzętowe. Po sprawdzeniu, czy masz odpowiednie magazynu, należy użyć Kreatora tworzenia nowej grupy ochrony można dodać maszyny wirtualne VMware.

1. Kliknij w konsoli usługi Azure Backup Server **ochrony**, a na wstążce narzędzi kliknij **New** aby otworzyć Kreatora tworzenia nowej grupy ochrony.

    ![Otwórz Kreatora tworzenia nowej grupy ochrony](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

    **Utwórz nową grupę ochrony** pojawi się okno dialogowe Kreator.

    ![Okno dialogowe Kreator tworzenia nowej grupy ochrony](./media/backup-azure-backup-server-vmware/protection-wizard.png)

    Kliknij przycisk **dalej** celu przechodzenia do **wybierz typ grupy ochrony** strony.

2. Na **typ grupy ochrony wybrać** wybierz **serwerów** a następnie kliknij przycisk **dalej**. **Wybierz członków grupy** zostanie wyświetlona strona.

3. Na **Wybierz członków grupy** strony, dostępne elementy członkowskie i zaznaczone elementy członkowskie są wyświetlane. Wybierz składowe, które chcesz chronić, a następnie kliknij przycisk **dalej**.

    ![Wybierz członków grupy](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

    Po wybraniu elementu członkowskiego, w przypadku wybrania folderu zawierającego inne foldery lub maszyn wirtualnych, te foldery i maszyny wirtualne zostaną domyślnie zaznaczone. Włączenie folderów i maszyny wirtualne w folderze nadrzędnym jest nazywany ochronę na poziomie folderu. Aby usunąć folder lub maszyny Wirtualnej, wyczyść pole wyboru.

    Jeśli na maszynie Wirtualnej lub folder zawierający Maszynę wirtualną, jest już chronione na platformie Azure, nie można ponownie wybrać tej maszyny Wirtualnej. Oznacza to, że po włączeniu ochrony maszyny Wirtualnej na platformie Azure, nie można chronić ponownie, który uniemożliwia tworzona dla jednej maszyny Wirtualnej przez punkty odzyskiwania zduplikowane. Jeśli chcesz zobaczyć, które wystąpienie serwera usługi Azure Backup chroni już członka, wskaż elementu członkowskiego, aby wyświetlić nazwę serwera, które chroni.

4. Na **wybierz metodę ochrony danych** strony, wprowadź nazwę grupy ochrony. Ochrona krótkoterminowa (na dysku) i ochrony w trybie online są zaznaczone. Jeśli chcesz użyć ochrony w trybie online (na platformie Azure), należy użyć krótkoterminowej ochrony na dysku. Kliknij przycisk **dalej** aby przejść do zakresu ochrony krótkoterminowej.

    ![Wybierz metodę ochrony danych](./media/backup-azure-backup-server-vmware/name-protection-group.png)

5. Na **Określ cele krótkoterminowe** strony, aby uzyskać **zakres przechowywania**, określ liczbę dni, które mają być przechowywane punkty odzyskiwania, które są *przechowywanych na dysku*. Jeśli chcesz zmienić czas i dni w przypadku, gdy punkty odzyskiwania są wykonywane, kliknij przycisk **Modyfikuj**. Punkty odzyskiwania krótkoterminowego są pełne kopie zapasowe. Nie są one przyrostowych kopii zapasowych. Gdy jesteś zadowolony z krótkoterminowych celów w zakresie, kliknij przycisk **dalej**.

    ![Określ cele krótkoterminowe](./media/backup-azure-backup-server-vmware/short-term-goals.png)

6. Na **Przejrzyj przydział dysku** strony, przejrzyj, a w razie potrzeby zmodyfikuj miejsce na dysku dla maszyn wirtualnych. Przydziały dysku zalecane opierają się na zakres przechowywania, który jest określony w **Określ cele krótkoterminowe** strony, typu obciążenia i rozmiaru chronionych danych (określoną w kroku 3).  

  - **Rozmiar danych:** rozmiar danych w grupie ochrony.
  - **Miejsce na dysku:** zalecaną ilość miejsca na dysku dla grupy ochrony. Jeśli chcesz zmodyfikować to ustawienie, należy przydzielić całkowitą ilość miejsca, nieco większa niż ilość oceniasz, że każde źródło danych rośnie.
  - **Kolokacja danych:** po włączeniu kolokacji wiele danych źródła w odniesieniu do ochrony można mapować na pojedynczą replikę i odzyskiwania wolumin punktu. Kolokacja nie jest obsługiwana w przypadku wszystkich obciążeń.
  - **Powiększ automatycznie:** po włączeniu tego ustawienia, jeśli danych w grupie ochrony przekroczy początkowe alokacji, System Center Data Protection Manager podejmie próbę zwiększyć rozmiar dysku o 25 procent.
  - **Szczegóły puli magazynu:** pokazuje stan puli magazynów, w tym całkowity rozmiar dysku i wolnego.

    ![Przejrzyj przydział dysku](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

    Gdy jesteś zadowolony z przydziału miejsca, kliknij przycisk **dalej**.

7. Na **wybierz metodę tworzenia repliki** Określ, jak chcesz wygenerować kopii początkowej lub repliki chronionych danych w usłudze Azure Backup Server.

    Wartość domyślna to **automatycznie przez sieć** i **teraz**. Jeśli użytkownik korzysta z domyślnych, firma Microsoft zaleca, aby określić godzinę poza godzinami szczytu. Wybierz **później** i określ datę i godzinę.

    Dla dużych ilości danych lub mniej niż optymalnych warunkach sieciowych warto rozważyć replikowanie danych w trybie offline przy użyciu nośnika wymiennego.

    Po wybraniu opcji kliknij przycisk **dalej**.

    ![Wybierz metodę tworzenia repliki](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. Na **opcje sprawdzania spójności** strony, wybierz, jak i kiedy automatyzacji sprawdzania spójności. Możesz uruchomić sprawdzanie spójności, gdy dane są niespójne, lub zgodnie z ustalonym harmonogramem.

    Jeśli nie chcesz skonfigurować automatyczne sprawdzenia spójności, możesz uruchomić sprawdzanie ręczne. W obszarze ochrony konsoli serwera usługi Azure Backup, kliknij prawym przyciskiem myszy grupę ochrony, a następnie wybierz **Przeprowadź Sprawdzanie spójności**.

    Kliknij przycisk **dalej** aby przejść do następnej strony.

9. Na **Określ dane ochrony Online** wybierz co najmniej jedno źródło danych, które mają być chronione. Wybierz elementy członkowskie, pojedynczo lub kliknij przycisk **Zaznacz wszystko** aby wybrać wszystkie elementy członkowskie. Po dokonaniu wyboru elementów członkowskich, kliknij przycisk **dalej**.

    ![Określ dane chronione w trybie online](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

10. Na **Określ harmonogram tworzenia kopii zapasowych Online** Określ harmonogram w celu wygenerowania punktów odzyskiwania z kopii zapasowej. Po wygenerowaniu punkt odzyskiwania, jego przeniesienie do magazynu usługi Recovery Services na platformie Azure. Gdy jesteś zadowolony z harmonogramu tworzenia kopii zapasowej online, kliknij przycisk **dalej**.

    ![Określ harmonogram tworzenia kopii zapasowej online](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

11. Na **Określanie zasad przechowywania Online** strony, wskazują, jak długo chcesz przechowywać dane kopii zapasowej na platformie Azure. Po zdefiniowaniu zasad, kliknij przycisk **dalej**.

    ![Określ zasady przechowywania danych online](./media/backup-azure-backup-server-vmware/retention-policy.png)

    Nie ma żadnego limitu czasu, na jak długo można przechowywać dane na platformie Azure. Jeśli przechowujesz dane punktu odzyskiwania na platformie Azure jedynym ograniczeniem jest, że nie może mieć więcej niż 9999 punktów odzyskiwania na chronione wystąpienie. W tym przykładzie chronione wystąpienie jest serwerem VMware.

12. Na **Podsumowanie** strony, przejrzyj szczegóły dla członków grupy ochrony i ustawieniami, a następnie kliknij przycisk **Utwórz grupę**.

    ![Elementu członkowskiego grupy ochrony i ustawienia — podsumowanie](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="next-steps"></a>Kolejne kroki
Jeśli używasz usługi Azure Backup Server do ochrony obciążeń oprogramowania VMware, może Cię zainteresować przy użyciu usługi Azure Backup Server, aby lepiej chronić [programu Microsoft Exchange server](./backup-azure-exchange-mabs.md), [farmy Microsoft SharePoint](./backup-azure-backup-sharepoint-mabs.md), lub [Bazy danych programu SQL Server](./backup-azure-sql-mabs.md).

Informacje na temat problemów z rejestrowaniem agenta można konfigurowania grupy ochrony lub tworzenie kopii zapasowych zadań, zobacz [Rozwiązywanie problemów z usługi Azure Backup Server](./backup-azure-mabs-troubleshoot.md).
