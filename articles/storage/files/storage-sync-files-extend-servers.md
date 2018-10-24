---
title: Samouczek Windows rozszerzyć serwerów plików przy użyciu usługi Azure File Sync | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozszerzyć Windows serwerach plików za pomocą usługi Azure File Sync, od początku do końca.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 10/23/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 4c3ceead792f60ceac7c5eddb64dc4644d662f83
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49960566"
---
<!---Customer intent: As a IT Administrator, I want see how to extend Windows file servers with Azure File Sync, so I can evaluate the process for extending storage capacity of my Windows Servers.
--->

# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>Samouczek: Rozszerzanie Windows serwerów plików przy użyciu usługi Azure File Sync
Scentralizowanie udziałów plików Twojej organizacji i rozszerzyć pojemność magazynu serwera plików w środowisku lokalnym za pomocą usługi Azure File Sync. Usługa Azure File Sync przekształca systemu Windows Server w szybką pamięć podręczną udziału plików platformy Azure.

W tym samouczku pokazano podstawowe kroki do rozszerzania pojemność magazynu w systemie Windows Server przy użyciu usługi Azure File Sync. Firma Microsoft korzysta z maszyny Wirtualnej z systemem Windows Server platformy Azure na potrzeby tego samouczka, ale zazwyczaj jak proces w przypadku serwerów lokalnych. Jeśli wszystko będzie gotowe do wdrożenia usługi Azure File Sync we własnym środowisku, należy użyć [wdrożenia usługi Azure File Sync](storage-sync-files-deployment-guide.md) zamiast tego artykułu.

> [!div class="checklist"]
> * Wdróż usługę synchronizacji magazynu
> * Przygotowanie systemu Windows Server za pomocą usługi Azure File Sync
> * Zainstaluj agenta usługi Azure File Sync
> * Rejestr systemu Windows Server za pomocą usługi synchronizacji magazynu
> * Tworzenie grupy synchronizacji i punktu końcowego w chmurze
> * Tworzenie punktu końcowego serwera

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="prepare-your-environment-for-the-tutorial"></a>Przygotowanie środowiska na potrzeby tego samouczka
Istnieje kilka kwestii, które należy umieścić w miejscu, w tym samouczku, przed wdrożeniem usługi Azure File Sync. Wraz z tworzenia konta usługi Azure Storage i plik udostępnić, można tworzyć maszyny Wirtualnej z systemem Windows Server 2016 Datacenter i przygotowanie tego serwera do usługi Azure File Sync.

### <a name="create-a-folder-and-txt-file"></a>Utwórz plik w folderze, a txt

Na komputerze lokalnym Utwórz nowy folder o nazwie *FilesToSync* i Dodaj plik tekstowy o nazwie *mytestdoc.txt*. w tym samouczku będziesz przekazać ten plik do udziału plików w dalszej części.

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>Tworzenie udziału plików
Następnie należy utworzyć udział plików.

1. Po wdrożeniu konta magazynu platformy Azure kliknij przycisk **przejdź do zasobu**.
1. Kliknij przycisk **pliki** z poziomu okienka konta magazynu.

    ![Kliknij przycisk pliki](./media/storage-sync-files-extend-servers/click-files.png)

1. Kliknij przycisk **+ udział pliku**.

    ![Kliknij przycisk Dodaj udział pliku](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. Nazwa nowego udziału plików *afsfileshare* i wprowadź "1" dla **przydziału**, następnie kliknij przycisk **Utwórz**. Przydział może być maksymalnie 5 TiB, ale potrzebujesz tylko 1 GB na potrzeby tego samouczka.

    ![Podaj nazwę i limit przydziału dla nowego udziału plików](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. Wybierz nowy udział plików, a następnie na lokalizację udziału pliku, kliknij przycisk **przekazywanie**.

    ![Przekazywanie pliku](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. Przejdź do *FilesToSync* folder, w którym zostały utworzone z pliku txt, wybierz opcję *mytestdoc.txt* i kliknij przycisk **przekazywanie**.

    ![Przeglądaj udział plików](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

Na tym etapie utworzono konto magazynu platformy Azure a udziałem plików za pomocą jednego pliku w nim na platformie Azure. Teraz utworzysz maszyny Wirtualnej platformy Azure z systemem Windows Server 2016 Datacenter do reprezentowania na lokalnym serwerze w ramach tego samouczka.

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>Wdrażanie maszyny Wirtualnej i dołączanie dysku danych

1. Następnie rozwiń menu po lewej stronie portalu i wybierz **Utwórz zasób** w lewym górnym rogu witryny Azure portal.
1. W polu wyszukiwania nad listą **portalu Azure Marketplace** zasobów, wyszukiwanie i wybieranie **systemu Windows Server 2016 Datacenter**, następnie wybierz **Utwórz**.
1. W **podstawy** , w obszarze **projektu szczegóły**, wybierz grupę zasobów utworzoną w ramach tego samouczka.

   ![Wprowadzanie podstawowych informacji o maszynie wirtualnej w bloku portalu](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. W obszarze **wystąpienia szczegóły**, podaj nazwę maszyny Wirtualnej, takie jak *myVM*.
1. Pozostaw wartości domyślne dla **Region**, **opcji dostępności**, **obraz**, i **rozmiar**.
1. W obszarze **konta administratora**, podaj **Username** i **hasło** dla maszyny Wirtualnej.
1. W obszarze **Reguły portów wejściowych** wybierz opcję **Zezwalaj na wybrane porty**, a następnie wybierz **RDP (3389)** i **HTTP** z listy rozwijanej.

   Przed utworzeniem maszyny Wirtualnej, musisz utworzyć dysk z danymi.

1. Kliknij przycisk **dalej: dysków**

   ![Dodawanie dysków danych](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

1. W **dysków** , w obszarze **dysku opcje**, pozostaw wartości domyślne. 
1. W obszarze **dysków z danymi**, kliknij przycisk **Utwórz i dołączyć nowy dysk**.

1. Pozostaw wartości domyślne, z wyjątkiem zmiany **rozmiar (GiB)** do **1 GB** na potrzeby tego samouczka.

   ![Szczegóły dysku danych](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

1. Kliknij przycisk **OK**.
1. Kliknij pozycję **Przegląd + utwórz**.
1. Kliknij pozycję **Utwórz**.

   Możesz kliknąć pozycję **powiadomienia** ikonę, aby obejrzeć **postęp wdrażania**. Tworzenie nowej maszyny Wirtualnej potrwa kilka minut.

1. Po zakończeniu wdrożenia maszyny Wirtualnej, kliknij przycisk **przejdź do zasobu**.

   ![Przechodzenie do zasobu](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

   W tym momencie utworzeniu nowej maszyny wirtualnej i dołączony dysk z danymi. Teraz musisz nawiązać połączenie z maszyną Wirtualną.

### <a name="connect-to-your-vm"></a>Łączenie z maszyną wirtualną

1. W witrynie Azure portal kliknij pozycję **Connect** na stronie właściwości maszyny wirtualnej.

   ![Nawiązywanie połączenia z maszyną wirtualną na platformie Azure z portalu](./media/storage-sync-files-extend-servers/connect-vm.png)

1. W **Połącz z maszyną wirtualną** Zachowaj domyślne opcje nawiązywania połączeń przy **adresu IP** ponad portu 3389 i kliknij przycisk **pliku RDP Pobierz**.

   ![Pobierz plik RDP](./media/storage-sync-files-extend-servers/download-rdp.png)

1. Otwórz pobrany plik RDP i kliknij **Połącz**, gdy wyświetli się odpowiedni monit.
1. W oknie **Zabezpieczenia systemu Windows** wybierz pozycję **Więcej opcji**, a następnie pozycję **Użyj innego konta**. Wpisz nazwę użytkownika jako *localhost\username*wprowadź hasło utworzone dla maszyny wirtualnej, a następnie kliknij przycisk **OK**.

   ![Więcej opcji](./media/storage-sync-files-extend-servers/local-host2.png)

1. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Kliknij pozycję **Tak** lub **Kontynuuj**, aby utworzyć połączenie.

### <a name="prepare-the-windows-server"></a>Przygotowanie systemu Windows Server
Aby uzyskać **systemu Windows Server 2016 Datacenter** serwera, wyłącz **Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer**. Ten krok jest wymagany tylko w przypadku rejestracji serwera początkowego. Możesz ją włączyć ponownie po zarejestrowaniu serwera.

W **systemu Windows Server 2016 Datacenter** maszyny Wirtualnej, **Menedżera serwera** zostanie automatycznie otwarta.  Jeśli **Menedżera serwera** nie jest otwierany domyślnie, wyszukaj go w programie Explorer.

1. W **Menedżera serwera** kliknij **lokalnego serwera**.

   !["Serwer lokalny" po lewej stronie interfejsu użytkownika Menedżera serwera](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. Na **właściwości** okienko podrzędne, wybierz link **Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer**.  

    ![W okienku "Konfiguracja zwiększonych zabezpieczeń" w Interfejsie użytkownika Menedżera serwera](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. W **Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer** okno dialogowe, wybierz opcję **poza** dla **Administratorzy** i **użytkowników**:  

    ![Wybrana konfiguracja zwiększonych zabezpieczeń programu Internet Explorer pop — okna z "Wyłączone"](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

   Teraz można dodać dysku danych do maszyny Wirtualnej.

### <a name="add-the-data-disk"></a>Dodawanie dysku danych

1. W **systemu Windows Server 2016 Datacenter** maszyny Wirtualnej, kliknij przycisk **plików i magazynowania** > **woluminów** > **dysków** .

    ![Dysk z danymi](media/storage-sync-files-extend-servers/your-disk.png)

1. Kliknij prawym przyciskiem myszy dysk o pojemności 1 GB o nazwie **dysku wirtualnego Msft** i kliknij przycisk **nowy wolumin**.
1. Wykonaj kroki kreatora, pozostawiając wartości domyślne w miejscu, biorąc pod uwagę przypisana litera dysku i kliknij przycisk **Utwórz**.
1. Kliknij przycisk **Zamknij**.

   W tym momencie zostały przeniesione dysk do trybu online, a utworzony wolumin. Aby potwierdzić, że Dodawanie dysku danych zakończyło się pomyślnie, otwierając Eksploratora na maszynie Wirtualnej i potwierdzenie, że nowy dysk jest obecny.

1. W Eksploratorze na maszynie Wirtualnej, rozwiń **ten komputer** i kliknij dwukrotnie ikonę nowego dysku. Jest napęd F:, w tym przykładzie.
1. Kliknij prawym przyciskiem myszy i wybierz **New** > **folderu**. Nazwa folderu *FilesToSync*.
1. Kliknij dwukrotnie **FilesToSync** folderu.
1. Kliknij prawym przyciskiem myszy i wybierz **New** > **dokument tekstowy**. Nazwa pliku tekstowego *MyTestFile*.

    ![Dodaj nowy plik tekstowy](media/storage-sync-files-extend-servers/new-file.png)

1. Zamknij **Explorer** i **Menedżera serwera**.

### <a name="download-the-azurerm-powershell-module"></a>Pobierz moduł AzureRM PowerShell
Następnie w **systemu Windows Server 2016 Datacenter** maszynę Wirtualną, zainstalować **moduł AzureRM PowerShell** na serwerze.

1. Na maszynie Wirtualnej należy otworzyć podwyższone okno programu PowerShell
1. Uruchom następujące polecenie:

   ```powershell
   Install-Module -Name AzureRM -AllowClobber
   ```

   > [!NOTE]
   > Jeśli masz wersję pakietu NuGet starszą niż 2.8.5.201, zostanie wyświetlony monit o pobranie i zainstalowanie najnowszej wersji pakietu NuGet.

   Galeria programu PowerShell domyślnie nie jest skonfigurowana jako zaufane repozytorium modułu PowerShellGet. Po pierwszym użyciu Galerii programu PowerShell zostanie wyświetlony następujący komunikat:

   ```output
   Untrusted repository

   You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the Set-PSRepository cmdlet.

   Are you sure you want to install the modules from 'PSGallery'?
   [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
   ```

1. Wybierz odpowiedź `Yes` lub `Yes to All`, aby kontynuować instalację.

Moduł `AzureRM` to zbiorczy moduł poleceń cmdlet programu Azure PowerShell. Zamontowany pobiera wszystkie dostępne moduły usługi Azure Resource Manager i udostępnia ich poleceń cmdlet do użycia.

W tym momencie po zakończeniu konfigurowania środowiska na potrzeby samouczka i wszystko będzie gotowe rozpocząć wdrażanie **Usługa synchronizacji magazynu**.

## <a name="deploy-the-storage-sync-service"></a>Wdróż usługę synchronizacji magazynu 
Wdrażanie usługi Azure File Sync zaczyna się od wprowadzania **Usługa synchronizacji magazynu** zasobów w grupie zasobów dla wybranej subskrypcji. Usługa synchronizacji magazynu dziedziczy uprawnienia dostępu z subskrypcji i grupy zasobów, do wdrażanego go do.

1. W witrynie Azure portal kliknij pozycję **Utwórz zasób** a następnie wyszukaj **usługi Azure File Sync**.
1. W wynikach wyszukiwania wybierz **usługi Azure File Sync**.
1. Wybierz **Utwórz** otworzyć **Wdróż synchronizację magazynu** kartę.

   ![Wdróż synchronizację magazynu](media/storage-sync-files-extend-servers/afs-info.png)

   W otwartym okienku wprowadź następujące informacje:

   | Wartość | Opis |
   | ----- | ----- |
   | **Nazwa** | Unikatową nazwę (na subskrypcję) usługa synchronizacji magazynu.<br><br>W tym samouczku używamy *afssyncservice02*. |
   | **Subskrypcja** | Subskrypcja, używane na potrzeby tego samouczka. |
   | **Grupa zasobów** | Grupa zasobów, używane na potrzeby tego samouczka.<br><br>W tym samouczku używamy *afsresgroup101918*. |
   | **Lokalizacja** | Wschodnie stany USA |

1. Gdy skończysz, wybierz pozycję **Utwórz** wdrażania **Usługa synchronizacji magazynu**.
1. Kliknij przycisk **powiadomienia** kartę > **przejdź do zasobu**.

## <a name="install-the-azure-file-sync-agent"></a>Zainstaluj agenta usługi Azure File Sync
Agent usługi Azure File Sync jest dostępna do pobrania pakiet, który umożliwia systemu Windows Server z udziału plików platformy Azure.

1. Przejdź z powrotem do **systemu Windows Server 2016 Datacenter** maszyny Wirtualnej, a następnie otwórz **programu Internet Explorer**
1. Przejdź do [Centrum pobierania Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). Przewiń w dół do **agenta synchronizacji plików Azure** sekcji, a następnie kliknij przycisk **Pobierz**.

   ![Pobieranie agenta synchronizacji](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. Pole wyboru dla **StorageSyncAgent_V3_WS2016. Plik EXE** i kliknij przycisk **dalej**.

   ![Wybierz agenta](media/storage-sync-files-extend-servers/select-agent.png)

1. **Zezwalaj raz** > **Uruchom** > **Otwórz** pliku.
1. Jeśli nie zostało to jeszcze zrobione, zamknij okno programu PowerShell.
1. Zaakceptuj ustawienia domyślne w **Kreatora instalacji agenta synchronizacji magazynu**.
1. Kliknij pozycję **Zainstaluj**.
1. Kliknij przycisk **Zakończ**.

Po wdrożeniu Usługa synchronizacji Azure i agent zainstalowany na **systemu Windows Server 2016 Datacenter** maszyny Wirtualnej. Teraz należy zarejestrować maszynę Wirtualną za pomocą **Usługa synchronizacji magazynu**.

## <a name="register-windows-server-with-storage-sync-service"></a>Rejestr systemu Windows Server za pomocą usługi synchronizacji magazynu
Rejestrowanie serwera z systemem Windows za pomocą usługi synchronizacji magazynu ustanawia relację zaufania między serwerem (lub klastra) i usługę synchronizacji magazynu. Serwer można rejestrować tylko do jednej usługi synchronizacji magazynu i można synchronizować z innymi serwerami i platformy Azure skojarzone z tą samą usługą synchronizacji magazynu udziałów plików.

Interfejs użytkownika rejestracji serwera powinno spowodować otwarcie automatycznie po zainstalowaniu **agenta usługi Azure File Sync**. Jeśli nie, można otworzyć go ręcznie z lokalizacji pliku: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.

1. Po otwarciu w maszynie Wirtualnej znajduje się w Interfejsie użytkownika serwera rejestracji kliknij **OK**.
1. Kliknij przycisk **logowania** do rozpoczęcia.
1. Zaloguj się przy użyciu poświadczeń konta platformy Azure, a następnie kliknij przycisk **logowania**.
1. Podaj następujące informacje:

   ![Zrzut ekranu przedstawiający interfejs użytkownika serwera rejestracji](media/storage-sync-files-extend-servers/signin.png)

   | | |
   | ----- | ----- |
   | Wartość | Opis |
   | **Subskrypcja platformy Azure** | Subskrypcja, która zawiera usługę synchronizacji magazynu na potrzeby tego samouczka. |
   | **Grupa zasobów** | Grupa zasobów, która zawiera usługę synchronizacji magazynu na potrzeby tego samouczka. Firma Microsoft była używana *afsresgroup101918* w tym samouczku. |
   | **Usługa synchronizacji magazynu** | Nazwa usługi synchronizacji magazynu używane na potrzeby tego samouczka. Firma Microsoft była używana *afssyncservice02* w tym samouczku. |

1. Kliknij przycisk **zarejestrować** do ukończenia rejestracji serwera.
1. W ramach procesu rejestracji wyświetlany jest monit o dodatkowe logowanie. Zaloguj się, a następnie kliknij przycisk **dalej**.
1. Kliknij przycisk **OK**.

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Tworzenie grupy synchronizacji i punktu końcowego w chmurze
Grupa synchronizacji definiuje topologia synchronizacji dla zestawu plików. Grupa synchronizacji musi zawierać jeden punkt końcowy w chmurze, który reprezentuje udział plików platformy Azure i punkty końcowe serwera. Punkt końcowy serwera reprezentuje ścieżkę na zarejestrowanego serwera.

1. Aby utworzyć grupy synchronizacji, w [witryny Azure portal](https://portal.azure.com/), wybierz opcję **+ grupa synchronizacji** od usługi synchronizacji magazynu utworzone w ramach tego samouczka. Użyliśmy *afssyncservice02* jako przykład w ramach tego samouczka.

   ![Utwórz nową grupę synchronizacji w witrynie Azure portal](media/storage-sync-files-extend-servers/add-sync-group.png)

1. W otwartym okienku wprowadź następujące informacje, aby utworzyć grupę synchronizacji z punktem końcowym chmury:

   | Wartość | Opis |
   | ----- | ----- |
   | **Nazwa grupy synchronizacji** | Ta nazwa musi być unikatowa w obrębie Usługa synchronizacji magazynu, ale może być dowolna nazwa, która jest logiczną dla Ciebie. W tym samouczku używamy *afssyncgroup*.|
   | **Subskrypcja** | Subskrypcja, w którym wdrożono usługę synchronizacji magazynu na potrzeby tego samouczka. |
   | **Konto magazynu** |Kliknij przycisk **wybierz konto magazynu**. W wyświetlonym okienku wybierz konto magazynu, które zawiera udział plików platformy Azure, utworzony na potrzeby tego samouczka. Użyliśmy *afsstoracct101918*. |
   | **Udział plików platformy Azure** | Nazwa udziału plików platformy Azure utworzone w ramach tego samouczka. Użyliśmy *afsfileshare*. |

1. Kliknij pozycję **Utwórz**.

Jeśli wybierzesz grupy synchronizacji, widać, że teraz masz **punkt końcowy w chmurze**.

## <a name="add-a-server-endpoint"></a>Dodaj punkt końcowy serwera
Punkt końcowy serwera reprezentuje konkretnej lokalizacji na zarejestrowanego serwera, takie jak folder na wolumin serwera.

1. Aby dodać punkt końcowy serwera, wybierz grupę synchronizacji nowo utworzony, a następnie wybierz **Dodaj punkt końcowy serwera**.

   ![Dodawanie nowego punktu końcowego serwera w okienku grupy synchronizacji](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. W **Dodaj punkt końcowy serwera** okienku, wprowadź następujące informacje, aby utworzyć punkt końcowy serwera:

   | | |
   | ----- | ----- |
   | Wartość | Opis |
   | **Zarejestrowany serwer** | Nazwa serwera, została utworzona w ramach tego samouczka. Użyliśmy *afsvm101918* w ramach tego samouczka ||
   | **Ścieżka** | Ścieżka systemu Windows Server na dysku, została utworzona w ramach tego samouczka. W naszym przykładzie jest *f:\filestosync*. |
   | **Obsługa warstw w chmurze** | Pozostaw wyłączone na potrzeby tego samouczka. |
   | **Wolne miejsce w woluminie** | W tym samouczku pozostaw puste. |

1. Kliknij pozycję **Utwórz**.

Pliki są teraz synchronizowane między udziału plików platformy Azure i systemu Windows Server.

![Usługa Azure Storage, które pomyślnie zsynchronizowały](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku przedstawiono podstawowe kroki, aby zwiększyć pojemność magazynu systemu Windows Server przy użyciu usługi Azure File Sync. Kliknij ten link, aby bardziej szczegółowego przyjrzeć się planowanie wdrażania usługi Azure File Sync.

> [!div class="nextstepaction"]
> [Planowanie wdrożenia usługi Azure File Sync](./storage-sync-files-planning.md)