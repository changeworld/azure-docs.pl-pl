---
title: Tworzenie kopii zapasowych maszyn wirtualnych VMware za pomocą usługi Azure Backup Server
description: Tworzenie kopii zapasowych maszyn wirtualnych VMware na serwerze vCenter/ESXi VMware przy użyciu usługi Azure Backup Server.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: raynew
ms.openlocfilehash: 6fb95d43bff79ca91988549114daa91e5d41f358
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60650151"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>Tworzenie kopii zapasowych maszyn wirtualnych VMware za pomocą usługi Azure Backup Server

W tym artykule wyjaśniono, jak utworzyć kopię zapasową maszyn wirtualnych VMware uruchomionych na hostach VMware ESXi/serwer vCenter na platformie Azure przy użyciu usługi Azure Backup Server.

W tym artykule opisano sposób:

- Konfigurowanie bezpiecznego kanału, aby serwer usługi Azure Backup może komunikować się z serwerami VMware przy użyciu protokołu HTTPS.
- Konfigurowanie konta VMware, korzystającą z usługi Azure Backup Server w celu uzyskania dostępu do serwera VMware.
- Dodaj poświadczenia konta do usługi Azure Backup.
- Dodaj vCenter lub ESXi server do usługi Azure Backup Server.
- Skonfiguruj grupę ochrony, która zawiera maszyny wirtualne VMware, które chcesz utworzyć kopię zapasową, określ ustawienia kopii zapasowej i zaplanować tworzenie kopii zapasowej.

## <a name="before-you-start"></a>Przed rozpoczęciem
- Sprawdź, czy jest używana wersja programu vCenter/ESXi, która jest obsługiwana dla kopii zapasowej — wersji 6.5, 6.0 i 5.5.
- Upewnij się, że po skonfigurowaniu serwera usługi Azure Backup. Jeśli jeszcze tego nie, [zrobić](backup-azure-microsoft-azure-backup.md) przed rozpoczęciem. Usługi Azure Backup Server powinna być uruchomiona z najnowszymi aktualizacjami.


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Utwórz bezpieczne połączenie z serwerem vcenter

Domyślnie usługi Azure Backup Server komunikuje się z serwerami VMware przy użyciu protokołu HTTPS. Aby skonfigurować połączenie HTTPS, Pobierz certyfikat VMware urząd certyfikacji (CA) i zaimportuj go na serwerze usługi Azure Backup.


### <a name="before-you-start"></a>Przed rozpoczęciem

- Jeśli nie chcesz używać protokołu HTTPS można [wyłączyć domyślne ustawienie](backup-azure-backup-server-vmware.md).
- Zazwyczaj połączyć się za pomocą przeglądarki na komputerze serwera usługi Azure Backup na serwerze vCenter/ESXi, przy użyciu kliencie internetowym vSphere. Po raz pierwszy w tym połączenia nie jest bezpieczne i będzie można zobaczyć następujące informacje.
- Należy zrozumieć, jak serwer usługi Azure Backup obsługuje tworzenie kopii zapasowych.
    - Pierwszym krokiem serwera usługi Azure Backup tworzy kopię zapasową danych na magazyn na dysku lokalnym. Usługa Azure Backup Server korzysta z puli magazynów, zestaw dysków i woluminów, na których serwer usługi Azure Backup przechowuje punkty odzyskiwania na dysku dla chronionych danych. Pula magazynu może być bezpośrednio dołączonym magazynem (DAS), urządzenia magazynującego fiber channel SAN lub interfejsu iSCSI lub sieci SAN. Koniecznie upewnij się, że wystarczającej ilości miejsca do lokalnego tworzenia kopii danych maszyny Wirtualnej VMware.
    - Usługa Azure Backup Server, a następnie tworzy kopię zapasową z dysku lokalnego magazynu na platformie Azure.
    - [Uzyskaj Pomoc](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1807#figure-out-how-much-storage-space-you-need) Aby ustalić, ile miejsca do magazynowania należy. Informacje są dla programu DPM, ale może służyć do usługi Azure Backup Server zbyt.

### <a name="set-up-the-certificate"></a>Konfigurowanie certyfikatu

Konfigurowanie bezpiecznego kanału w następujący sposób:

1. W przeglądarce usługi Azure Backup Server wprowadź vSphere adres URL klienta sieci Web. Jeśli nie pojawia się na stronie logowania, sprawdź ustawienia serwera proxy połączenia i przeglądarki.

    ![vSphere Web Client](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. Na stronie logowania w sieci Web klienta vSphere kliknij **pobierania zaufane certyfikaty głównego urzędu certyfikacji**.

    ![Pobierz certyfikat zaufanego głównego urzędu certyfikacji](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. Plik o nazwie **Pobierz** zostanie pobrana. W zależności od przeglądarki pojawi się komunikat z pytaniem, czy chcesz otworzyć lub zapisać plik.

    ![Pobierz certyfikat urzędu certyfikacji](./media/backup-azure-backup-server-vmware/download-certs.png)

4. Zapisz plik na komputerze serwera usługi Azure Backup z rozszerzeniem .zip.

5. Kliknij prawym przyciskiem myszy **download.zip** > **wyodrębnić wszystkie**. Plik zip wyodrębni jego zawartość do **certyfikatów** folder, który zawiera:
   - Plik certyfikatu głównego z rozszerzeniem rozpoczynającą się od numerowane sekwencji, takich jak.0 i.1.
   - Plik listy CRL ma rozszerzenie, które zaczyna się od sekwencji, takich jak .r0 lub .r1. Plik listy CRL jest skojarzony z certyfikatem.

     ![Pobrany certyfikatów](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. W **certyfikatów** folderu, kliknij prawym przyciskiem myszy plik certyfikatu głównego > **Zmień nazwę**.

    ![Zmień nazwę certyfikatu głównego](./media/backup-azure-backup-server-vmware/rename-cert.png)

6. Zmień rozszerzenie certyfikatu głównego na CRT i potwierdź. Ikony pliku zmieni się na taki, który reprezentuje certyfikatu głównego.

7. Kliknij prawym przyciskiem myszy certyfikat główny, a następnie w menu podręcznym wybierz **Zainstaluj certyfikat**.

8. W **Kreatora importu certyfikatów**, wybierz opcję **komputera lokalnego** jako miejsce docelowe dla certyfikatu, a następnie kliknij **dalej**. Upewnij się, jeśli pojawi się prośba mają do umożliwienia wprowadzania zmian do komputera.

    ![Witamy w Kreatorze](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)


9. Na **Store certyfikatu** wybierz opcję **Umieść wszystkie certyfikaty w następującym magazynie**, a następnie kliknij przycisk **Przeglądaj** wybrać magazyn certyfikatów.

     ![Magazyn certyfikatów](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

10. W **Store certyfikatu wybierz**, wybierz opcję **zaufane główne urzędy certyfikacji** jako folder docelowy dla certyfikatów, a następnie kliknij **OK**.

    ![Folder docelowy certyfikatu](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

11. W **Kończenie pracy Kreatora importu certyfikatów**, sprawdź folder, a następnie kliknij **Zakończ**.

    ![Sprawdź, czy certyfikat znajduje się w folderze odpowiednie](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)


12. Po potwierdzeniu importu certyfikatów, zaloguj się do programu vCenter Server, aby upewnić się, że połączenie jest bezpieczne.




### <a name="disable-default-https"></a>Wyłącz domyślnego protokołu HTTPS

Jeśli mają bezpieczny granice w Twojej organizacji, a nie chcesz używać protokołu HTTPS między serwerami VMware i maszyny serwera usługi Azure Backup, wyłączanie protokołu HTTPS w następujący sposób: u
1. Skopiuj i wklej następujący tekst do pliku txt.

      ```
      Windows Registry Editor Version 5.00
      [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
      "IgnoreCertificateValidation"=dword:00000001
      ```

2. Zapisz plik na komputerze usługi Azure Backup Server o nazwie **DisableSecureAuthentication.reg**.

3. Kliknij dwukrotnie plik, aby aktywować wpisu rejestru.


## <a name="create-a-vmware-role"></a>Utwórz rolę programu VMware

Usługi Azure Backup Server należy konto użytkownika z uprawnieniami dostępu do serwera Vcenter/hostem ESXi. Utwórz rolę programu VMware przy użyciu określonych uprawnień, a następnie skojarzyć konto użytkownika z rolą.

1. Zaloguj się do serwera vCenter (lub hoście ESXi, jeśli nie używasz programu vCenter Server).
2. W **Nawigator** panelu, kliknij przycisk **administracji**.

    ![Administracja](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. W **administracji** > **role**, kliknij ikonę roli Dodaj (+ symbol).

    ![Dodaj rolę](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)


4. W **Utwórz rolę** > **nazwy roli**, wprowadź *BackupAdminRole*. Nazwa roli może być dowolną, ale powinien być rozpoznawalną w celu roli.

5. Wybierz uprawnienia, zgodnie z opisem w poniższej tabeli, a następnie kliknij przycisk **OK**.  Nowa rola zostanie wyświetlona na liście w **role** panelu.
   - Kliknij ikonę obok etykiecie nadrzędnej, aby rozwinąć element nadrzędny i wyświetlić uprawnienia podrzędnych.
   - Aby wybrać uprawnień VirtualMachine, musisz przejść kilka poziomów w hierarchii nadrzędny-podrzędny.
   - Nie potrzebujesz wybrać wszystkie uprawnienia podrzędnego w obrębie uprawnienie nadrzędne.

     ![Uprawnienie hierarchii nadrzędny-podrzędny](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>Uprawnienia ról
**6.5/6.0** | **5.5**
--- | ---
Datastore.AllocateSpace | Datastore.AllocateSpace
Global.ManageCustomFields | Global.ManageCustomFields
Global.SetCustomField |
Host.Local.CreateVM | Network.Assign
Network.Assign |
Resource.AssignVMToPool |
VirtualMachine.Config.AddNewDisk  | VirtualMachine.Config.AddNewDisk   
VirtualMachine.Config.AdvancedConfig| VirtualMachine.Config.AdvancedConfig
VirtualMachine.Config.ChangeTracking| VirtualMachine.Config.ChangeTracking
VirtualMachine.Config.HostUSBDevice |
VirtualMachine.Config.QueryUnownedFiles |
VirtualMachine.Config.SwapPlacement| VirtualMachine.Config.SwapPlacement
VirtualMachine.Interact.PowerOff| VirtualMachine.Interact.PowerOff
VirtualMachine.Inventory.Create| VirtualMachine.Inventory.Create
VirtualMachine.Provisioning.DiskRandomAccess |
VirtualMachine.Provisioning.DiskRandomRead | VirtualMachine.Provisioning.DiskRandomRead
VirtualMachine.State.CreateSnapshot | VirtualMachine.State.CreateSnapshot
VirtualMachine.State.RemoveSnapshot | VirtualMachine.State.RemoveSnapshot




## <a name="create-a-vmware-account"></a>Utwórz konto VMware

1. W programie vCenter Server **Nawigator** panelu, kliknij przycisk **użytkowników i grup**. Jeśli nie korzystasz z programu vCenter Server, należy utworzyć konto na odpowiedniego hosta ESXi.

    ![Opcja użytkowników i grup](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    **VCenter użytkowników i grup** są wyświetlane w panelu.


2. W **vCenter użytkowników i grup** panelu wybierz **użytkowników** kartę, a następnie kliknij ikonę Dodaj użytkowników (+ symbol).

     ![panel vCenter użytkowników i grup](./media/backup-azure-backup-server-vmware/usersandgroups.png)


3. W **nowego użytkownika** okna dialogowego Dodaj informacje o użytkowniku > **OK**. W tej procedurze nazwa użytkownika jest BackupAdmin.

    ![Okno dialogowe Nowy użytkownik](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)


4. Aby skojarzyć konto użytkownika z roli, w **Nawigator** panelu, kliknij przycisk **uprawnień globalnych**. W **uprawnień globalnych** panelu wybierz **Zarządzaj** kartę, a następnie kliknij ikonę Dodaj (+ symbol).

    ![Globalne panelu uprawnień](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)


5. W **globalnych uprawnień Root - Dodaj uprawnienia**, kliknij przycisk **Dodaj** wybrać użytkownika lub grupy.

    ![Wybierz użytkownika lub grupy](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. W **Wybierz użytkowników i grup**, wybierz **BackupAdmin** > **Dodaj**. W **użytkowników**, *domena\nazwa_użytkownika* format jest używany dla konta użytkownika. Jeśli chcesz użyć w innej domenie, wybierz go z **domeny** listy. Kliknij przycisk **OK** można dodać wybranych użytkowników do **Dodaj uprawnienia** okno dialogowe.

    ![Dodaj użytkownika BackupAdmin](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)


7.  W **przypisana rola**, wybierz z listy rozwijanej **BackupAdminRole** > **OK**.

    ![Przypisz użytkownika do roli](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)


Na **Zarządzaj** karcie **uprawnień globalnych** panelu, nowe konto użytkownika i rolę skojarzone są wyświetlane na liście.


## <a name="add-the-account-on-azure-backup-server"></a>Dodaj konto w usłudze Azure Backup Server


1. Otwórz usługę Azure Backup Server. Jeśli nie możesz znaleźć ikony na pulpicie, otwórz kopia zapasowa Microsoft Azure, z listy aplikacji.

    ![Ikona usługi Azure Backup Server](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. Kliknij w konsoli usługi Azure Backup Server **zarządzania** >  **serwerów produkcyjnych** > **Zarządzanie VMware**.

    ![Usługa Azure Backup Server konsoli](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)


3. W **poświadczenia zarządzania** okno dialogowe, kliknij przycisk **Dodaj**.

    ![Okno dialogowe poświadczenia zarządzania serwera kopii zapasowych platformy Azure](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. W **Dodaj poświadczenie** , wprowadź nazwę i opis dla nowego poświadczenia i określ nazwę użytkownika i hasło, zdefiniowanych na serwerze VMware. Nazwa, *poświadczeń Contoso Vcenter* służy do identyfikowania poświadczenia w ramach tej procedury. Jeśli serwer programu VMware i serwera usługi Azure Backup nie są w tej samej domenie, określ domenę, nazwę użytkownika.

    ![Okno dialogowe Azure Backup serwer Dodaj poświadczenie](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. Kliknij przycisk **Dodaj** można dodać nowe poświadczenie.

    ![Okno dialogowe poświadczenia zarządzania serwera kopii zapasowych platformy Azure](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)


## <a name="add-the-vcenter-server"></a>Dodawanie serwera vCenter Server

Dodawanie serwera vCenter do serwera usługi Azure Backup.


1. Kliknij w konsoli usługi Azure Backup Server **zarządzania** > **serwerów produkcyjnych** > **Dodaj**.

    ![Kreator dodawania serwerów produkcyjnych Otwórz](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)


2. W **Kreator dodawania serwerów produkcyjnych** > **typ serwera produkcyjnego wybierz** wybierz **serwery VMware**, a następnie kliknij przycisk **dalej**.

     ![Kreator dodawania serwerów produkcyjnych](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. W **wybierz komputery****nazwa/adres IP serwera**, określ nazwę FQDN lub adres IP serwera VMware. Jeśli wszystkie serwery ESXi są zarządzane przez ten sam program vCenter, należy określić nazwę vCenter. W przeciwnym razie Dodaj hosta ESXi.

    ![Określ serwer VMware](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. W **SSL Port**, wprowadź numer portu, który jest używany do komunikacji z serwerem VMware. 443 jest domyślnym portem, ale można go zmienić, jeśli serwer VMware nasłuchuje na innym porcie.

5. W **Określ poświadczenia**, wybierz poświadczenie, która została utworzona wcześniej.

    ![Określ poświadczenie](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Kliknij przycisk **Dodaj** dodać serwer VMware do listy serwerów. Następnie kliknij przycisk **Next** (Dalej).

    ![Dodawanie serwera VMWare i poświadczeń](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. W **Podsumowanie** kliknij **Dodaj** dodać serwer VMware do usługi Azure Backup Server. Nowy serwer jest dodawany natychmiast, agent nie jest wymagane na serwerze VMware.

    ![Dodaj serwer programu VMware do usługi Azure Backup Server](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. Sprawdź ustawienia na **Zakończ** strony.

   ![Zakończ stronę](./media/backup-azure-backup-server-vmware/summary-screen.png)

Jeśli masz wiele hostów ESXi, które nie są zarządzane przez serwer vCenter lub istnieje wiele wystąpień programu vCenter Server, należy ponownie uruchomić kreatora, aby dodać serwery.




## <a name="configure-a-protection-group"></a>Skonfiguruj grupę ochrony

Dodaj maszyny wirtualne programu VMware do utworzenia kopii zapasowej. Grup ochrony zebrać wiele maszyn wirtualnych i zastosować ten sam retencji danych i ustawień kopii zapasowej do wszystkich maszyn wirtualnych w grupie.


1. Kliknij w konsoli usługi Azure Backup Server **ochrony**, > **New**.

    ![Otwórz Kreatora tworzenia nowej grupy ochrony](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. W **Utwórz nową grupę ochrony** strona powitalna kreatora, kliknij przycisk **dalej**.

    ![Okno dialogowe Kreator tworzenia nowej grupy ochrony](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Na **typ grupy ochrony wybrać** wybierz **serwerów** a następnie kliknij przycisk **dalej**. **Wybierz członków grupy** zostanie wyświetlona strona.

1. W **Wybierz członków grupy** > Wybierz maszyny wirtualne (lub foldery z maszyny Wirtualnej), którą chcesz utworzyć kopię zapasową. Następnie kliknij przycisk **Next** (Dalej).

    - Jeśli został wskazany folder lub maszyn wirtualnych lub foldery wewnątrz tego folderu wybrane zostaną także do tworzenia kopii zapasowych. Możesz usunąć zaznaczenie folderów lub nie chcesz, aby utworzyć kopię zapasową maszyn wirtualnych.
1. Jeśli maszyna wirtualna lub folder jest już tworzona kopia zapasowa, nie możesz wybrać go. To zagwarantować, że dla maszyny Wirtualnej nie są tworzone punkty odzyskiwania duplikatów. .

     ![Wybierz członków grupy](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)


1. W **wybierz metodę ochrony danych** strony, wprowadź nazwę grupy ochrony, a ustawienia ochrony. Tworzenie kopii zapasowej na platformie Azure, ustawić krótkoterminowej **dysku** i włącz ochronę w trybie online. Następnie kliknij przycisk **Next** (Dalej).

    ![Wybierz metodę ochrony danych](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. W **Określ cele krótkoterminowe**, określ, jak długo chcesz przechowywać dane kopii zapasowej na dysku.
   - W **zakres przechowywania**, określ liczbę dni, które powinny być przechowywane punkty odzyskiwania na dysku.
   - W **częstotliwość synchronizacji**, określ, jak często wykonywane są punkty odzyskiwania na dysku.
       - Jeśli nie chcesz ustawiać kopii zapasowych interwał można sprawdzić **zaraz przed punktem odzyskiwania** tak, aby tworzenie kopii zapasowej jest uruchamiana bezpośrednio przed zaplanowanym każdego punktu odzyskiwania.
       - Krótkoterminowe kopie zapasowe są pełne kopie zapasowe a nie przyrostowe.
       - Kliknij przycisk **Modyfikuj** zmienić godziny/daty po wystąpieniu krótkoterminowe kopie zapasowe.

     ![Określ cele krótkoterminowe](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. W **Przejrzyj przydział dysku**, przejrzyj miejsce na dysku, podany dla kopii zapasowych maszyn wirtualnych. w przypadku maszyn wirtualnych.

   - Przydziały dysku zalecane są oparte na zakres przechowywania, który określiłeś, typu obciążenia i rozmiaru chronionych danych. Wprowadź wymagane zmiany, a następnie kliknij przycisk **dalej**.
   - **Rozmiar danych:** Rozmiar danych w grupie ochrony.
   - **Miejsce na dysku:** Zalecana ilość miejsca na dysku dla grupy ochrony. Jeśli chcesz zmodyfikować to ustawienie, należy przydzielić całkowitą ilość miejsca, nieco większa niż ilość oceniasz, że każde źródło danych rośnie.
   - **Kolokacja danych:** Po włączeniu kolokacji wiele źródeł danych w zakresie ochrony można mapować na pojedynczą replikę i wolumin punktu odzyskiwania. Kolokacja nie jest obsługiwana w przypadku wszystkich obciążeń.
   - **Powiększ automatycznie:** Jeśli włączysz to ustawienie, jeśli danych w grupie ochrony przekroczy początkowe alokacji, usługi Azure Backup Server próbuje zwiększyć rozmiar dysku o 25 procent.
   - **Szczegóły puli magazynu:** Pokazuje stan puli magazynów, w tym całkowity rozmiar dysku i wolnego.

     ![Przejrzyj przydział dysku](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. W **wybierz metodę tworzenia repliki** Określ, jak chcesz wykonać początkową kopię zapasową, a następnie kliknij przycisk **dalej**.
   - Wartość domyślna to **automatycznie przez sieć** i **teraz**.
   - Jeśli użytkownik korzysta z domyślnych, firma Microsoft zaleca, aby określić godzinę poza godzinami szczytu. Wybierz **później** i określ datę i godzinę.
   - Dla dużych ilości danych lub mniej niż optymalnych warunkach sieciowych warto rozważyć replikowanie danych w trybie offline przy użyciu nośnika wymiennego.

     ![Wybierz metodę tworzenia repliki](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. W **opcje sprawdzania spójności**, zdecyduj, jak i kiedy automatyzacji sprawdzania spójności. Następnie kliknij przycisk **Next** (Dalej).
      - Możesz uruchomić sprawdzanie spójności, gdy dane są niespójne, lub zgodnie z ustalonym harmonogramem.
      - Jeśli nie chcesz skonfigurować automatyczne sprawdzenia spójności, możesz uruchomić sprawdzanie ręczne. Aby to zrobić, kliknij prawym przyciskiem myszy grupę ochrony > **Przeprowadź Sprawdzanie spójności**.

1. W **Określ dane ochrony Online** wybierz maszyny wirtualne lub maszyny Wirtualnej, foldery, które chcesz utworzyć kopię zapasową. Wybierz elementy członkowskie, pojedynczo lub kliknij przycisk **Zaznacz wszystko** aby wybrać wszystkie elementy członkowskie. Następnie kliknij przycisk **Next** (Dalej).

      ![Określ dane chronione w trybie online](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Na **Określ harmonogram tworzenia kopii zapasowych Online** Określ, jak często chcesz utworzyć kopię zapasową danych z magazynu lokalnego do platformy Azure.

    - Punkty odzyskiwania w chmurze dla danych będą generowane zgodnie z harmonogramem. Następnie kliknij przycisk **Next** (Dalej).
    - Po wygenerowaniu punkt odzyskiwania, jego przeniesienie do magazynu usługi Recovery Services na platformie Azure.

      ![Określ harmonogram tworzenia kopii zapasowej online](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Na **Określanie zasad przechowywania Online** strony, wskazują, jak długo chcesz przechowywać punktów odzyskiwania, które są tworzone na podstawie kopii zapasowych co dzień/tydzień/miesiąc/rok na platformie Azure. Następnie kliknij przycisk **dalej**.

    - Nie ma żadnego limitu czasu, na jak długo można przechowywać dane na platformie Azure.
    - Jedynym ograniczeniem jest to, że nie może mieć więcej niż 9999 punktów odzyskiwania na chronione wystąpienie. W tym przykładzie chronione wystąpienie jest serwerem VMware.

      ![Określ zasady przechowywania danych online](./media/backup-azure-backup-server-vmware/retention-policy.png)


1. Na **Podsumowanie** strony, przejrzyj ustawienia, a następnie kliknij przycisk **Utwórz grupę**.

     ![Elementu członkowskiego grupy ochrony i ustawienia — podsumowanie](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6.7

Do tworzenia kopii zapasowej vSphere 6.7 wykonaj następujące czynności:

- Włącz szyfrowanie TLS 1.2 na serwerze programu DPM
  >[!Note]
  >6.7 VMWare lub nowszym ma włączony TLS jako protokołu komunikacyjnego.

- Ustawić klucze rejestru w następujący sposób:  

  Edytor rejestru Windows wersja 5.00

  [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v2.0.50727] "SystemDefaultTlsVersions"=dword:00000001 "SchUseStrongCrypto"=dword:00000001

  [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v4.0.30319] "SystemDefaultTlsVersions"=dword:00000001 "SchUseStrongCrypto"=dword:00000001

  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v2.0.50727] "SystemDefaultTlsVersions"=dword:00000001 "SchUseStrongCrypto"=dword:00000001

  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v4.0.30319] "SystemDefaultTlsVersions"=dword:00000001 s"SchUseStrongCrypto"=dword:00000001


## <a name="next-steps"></a>Kolejne kroki

Do rozwiązywania problemów, podczas konfigurowania kopii zapasowych, przejrzyj [przewodnik rozwiązywania problemów dla usługi Azure Backup Server](./backup-azure-mabs-troubleshoot.md).
