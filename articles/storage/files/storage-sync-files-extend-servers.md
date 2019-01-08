---
title: 'Samouczek: rozszerzanie serwerów plików systemu Windows przy użyciu usługi Azure File Sync | Microsoft Docs'
description: Dowiedz się, jak rozszerzyć serwery plików systemu Windows przy użyciu usługi Azure File Sync — od początku do końca.
services: storage
author: wmgries
ms.service: storage
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 3ebf450f4e84fed572307a18f20f36013e32c7a5
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/19/2018
ms.locfileid: "53630703"
---
# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>Samouczek: Rozszerzanie serwerów plików systemu Windows przy użyciu usługi Azure File Sync
W tym samouczku przedstawimy podstawowe kroki rozszerzania pojemności magazynu w systemie Windows Server przy użyciu usługi Azure File Sync. Mimo że w tym samouczku używamy maszyny wirtualnej platformy Azure z systemem Windows Server, ten proces jest zazwyczaj wykonywany na serwerach lokalnych. Jeśli wszystko będzie gotowe do wdrożenia usługi Azure File Sync w Twoim środowisku, skorzystaj w zamian z artykułu [Deploy Azure File Sync (Wdrażanie usługi Azure File Sync)](storage-sync-files-deployment-guide.md).

> [!div class="checklist"]
> * Wdrażanie usługi synchronizacji magazynu
> * Przygotowywanie systemu Windows Server do używania z usługą Azure File Sync
> * Pobieranie agenta usługi Azure File Sync
> * Rejestrowanie systemu Windows Server za pomocą usługi synchronizacji magazynu
> * Tworzenie grupy synchronizacji i punktu końcowego w chmurze
> * Tworzenie punktu końcowego serwera

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="prepare-your-environment"></a>Przygotowywanie środowiska
Przed rozpoczęciem wdrażania usługi Azure File Sync należy skonfigurować kilka elementów na potrzeby tego samouczka. Wraz z tworzeniem konta usługi Azure Storage i udostępnianiem plików utworzysz maszynę wirtualną z systemem Windows Server 2016 Datacenter i przygotujesz ten serwera do użycia usługi Azure File Sync.

### <a name="create-a-folder-and-txt-file"></a>Tworzenie folderu i pliku TXT

Na komputerze lokalnym utwórz nowy folder o nazwie *FilesToSync* i dodaj plik tekstowy o nazwie *mytestdoc.txt*. W dalszej części tego samouczka przekażesz ten plik do udziału plików.

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>Tworzenie udziału plików
Następnie utwórz udział plików.

1. Po wdrożeniu konta magazynu platformy Azure kliknij pozycję **Przejdź do zasobu**.
1. Kliknij pozycję **Pliki** w okienku konta magazynu.

    ![Klikanie pozycji Pliki](./media/storage-sync-files-extend-servers/click-files.png)

1. Kliknij pozycję **+ Udział plików**.

    ![Klikanie przycisku dodawania udziału plików](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. Nadaj nowemu udziałowi plików nazwę *afsfileshare* i wprowadź wartość „1” w obszarze **Limit przydziału**, a następnie kliknij pozycję **Utwórz**. Maksymalny limit przydziału to 5 TiB, ale w tym samouczku potrzebujesz tylko 1 GB.

    ![Podawanie nazwy i limitu przydziału dla nowego udziału plików](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. Wybierz nowy udział plików, a następnie w lokalizacji udziału plików kliknij pozycję **Przekaż**.

    ![Przekazywanie pliku](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. Przejdź do folderu *FilesToSync*, w którym został utworzony plik TXT, wybierz plik *mytestdoc.txt* i kliknij pozycję **Przekaż**.

    ![Przeglądanie w celu wyszukania udziału plików](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

Na tym etapie na platformie Azure utworzono konto usługi Azure Storage i udział plików zawierający jeden plik. Teraz utworzysz maszynę wirtualną platformy Azure z systemem Windows Server 2016 Datacenter do reprezentowania serwera lokalnego w ramach tego samouczka.

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>Wdrażanie maszyny wirtualnej i dołączanie dysku danych

1. Następnie rozwiń menu po lewej stronie portalu i wybierz pozycję **Utwórz zasób** w lewym górnym rogu witryny Azure Portal.
1. W polu wyszukiwania nad listą zasobów **Azure Marketplace** wpisz **Windows Server 2016 Datacenter**, wybierz odpowiednią pozycję i kliknij pozycję **Utwórz**.
1. Na karcie **Podstawy** w obszarze **Szczegóły projektu** wybierz grupę zasobów utworzoną w ramach tego samouczka.

   ![Wprowadzanie podstawowych informacji o maszynie wirtualnej w bloku portalu](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. W obszarze **Szczegóły wystąpienia** podaj nazwę maszyny wirtualnej, taką jak *myVM*.
1. Pozostaw wartości domyślne w polach **Region**, **Opcje dostępności**, **Obraz** i **Rozmiar**.
1. W obszarze **Konto administratora** podaj **nazwę użytkownika** i **hasło** dla maszyny wirtualnej.
1. W obszarze **Reguły portów wejściowych** wybierz opcję **Zezwalaj na wybrane porty**, a następnie wybierz **RDP (3389)** i **HTTP** z listy rozwijanej.

   Przed utworzeniem maszyny wirtualnej musisz utworzyć dysk z danymi.

1. Kliknij pozycję **Dalej: Dyski**

   ![Dodawanie dysków danych](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

1. Na karcie **Dyski** w obszarze **Opcje dysku** pozostaw wartości domyślne.
1. W obszarze **DYSKI Z DANYMI** kliknij pozycję **Utwórz i dołącz nowy dysk**.

1. Pozostaw wartości domyślne, zmieniając tylko wartość pola **Rozmiar (GiB)** na **1 GB** na potrzeby tego samouczka.

   ![Szczegóły dysku z danymi](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

1. Kliknij przycisk **OK**.
1. Kliknij pozycję **Przegląd + utwórz**.
1. Kliknij pozycję **Utwórz**.

   Możesz kliknąć ikonę **Powiadomienia**, aby sprawdzić **postęp wdrażania**. Tworzenie nowej maszyny wirtualnej potrwa kilka minut.

1. Po zakończeniu wdrażania maszyny wirtualnej kliknij pozycję **Przejdź do zasobu**.

   ![Przechodzenie do zasobu](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

   Na tym etapie utworzono nową maszynę wirtualną i dołączono dysk z danymi. Teraz musisz nawiązać połączenie z maszyną wirtualną.

### <a name="connect-to-your-vm"></a>Łączenie z maszyną wirtualną

1. W witrynie Azure Portal kliknij pozycję **Połącz** na stronie właściwości maszyny wirtualnej.

   ![Nawiązywanie połączenia z maszyną wirtualną na platformie Azure z portalu](./media/storage-sync-files-extend-servers/connect-vm.png)

1. Na stronie **Nawiązywanie połączenia z maszyną wirtualną** zostaw opcje domyślne, aby połączyć się za pomocą **adresu IP** przez port 3389 i kliknij pozycję **Pobierz plik RDP**.

   ![Pobieranie pliku RDP](./media/storage-sync-files-extend-servers/download-rdp.png)

1. Otwórz pobrany plik RDP i kliknij **Połącz**, gdy wyświetli się odpowiedni monit.
1. W oknie **Zabezpieczenia systemu Windows** wybierz pozycję **Więcej opcji**, a następnie pozycję **Użyj innego konta**. Wpisz nazwę użytkownika w formacie *localhost\nazwa_użytkownika* oraz hasło utworzone dla maszyny wirtualnej, a następnie kliknij pozycję **OK**.

   ![Więcej opcji](./media/storage-sync-files-extend-servers/local-host2.png)

1. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Kliknij pozycję **Tak** lub **Kontynuuj**, aby utworzyć połączenie.

### <a name="prepare-the-windows-server"></a>Przygotowywanie systemu Windows Server
W przypadku systemu **Windows Server 2016 Datacenter** wyłącz pozycję **Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer**. Ten krok jest wymagany tylko w przypadku początkowej rejestracji serwera. Tę pozycję można włączyć ponownie po zarejestrowaniu serwera.

Na maszynie wirtualnej z systemem **Windows Server 2016 Datacenter** zostanie automatycznie otwarty **Menedżer serwera**.  Jeśli **Menedżer serwera** nie otworzy się domyślnie, wyszukaj go w programie Explorer.

1. W **Menedżerze serwera** kliknij pozycję **Serwer lokalny**.

   ![Pozycja „Serwer lokalny” po lewej stronie interfejsu użytkownika Menedżera serwera](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. W okienku podrzędnym **Właściwości** wybierz link do funkcji **Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer**.  

    ![Okienko „Konfiguracja zwiększonych zabezpieczeń” w interfejsie użytkownika Menedżera serwera](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. W oknie dialogowym **Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer** wybierz pozycję **Wyłączono** dla pozycji **Administratorzy** i **Użytkownicy**.

    ![Okno podręczne „Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer” z wybraną pozycją „Wyłączono”](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

   Teraz można dodać dysk z danymi do maszyny wirtualnej.

### <a name="add-the-data-disk"></a>Dodawanie dysku z danymi

1. W maszynie wirtualnej z systemem **Windows Server 2016 Datacenter** kliknij kolejno pozycje **Pliki i usługi magazynowania** > **Wolumin** > **Dyski**.

    ![Dysk z danymi](media/storage-sync-files-extend-servers/your-disk.png)

1. Kliknij prawym przyciskiem myszy dysk o pojemności 1 GB o nazwie **Msft Virtual Disk** i kliknij pozycję **Nowy wolumin**.
1. Wykonaj kroki kreatora, pozostawiając wartości domyślne na miejscu, zapisz przypisaną literę dysku i kliknij pozycję **Utwórz**.
1. Kliknij przycisk **Zamknij**.

   Na tym etapie przeniesiono dysk do trybu online i utworzono wolumin. Aby potwierdzić, że dodawanie dysku danych zakończyło się pomyślnie, możesz otworzyć Eksploratora na maszynie wirtualnej i sprawdzić, czy nowy dysk jest widoczny.

1. W Eksploratorze na maszynie Wirtualnej rozwiń pozycję **Ten komputer** i kliknij dwukrotnie nowy dysk. W tym przykładzie jest to dysk F:.
1. Kliknij prawym przyciskiem myszy i wybierz kolejno pozycje **Nowy** > **Folder**. Nadaj folderowi nazwę *FilesToSync*.
1. Kliknij dwukrotnie folder **FilesToSync**.
1. Kliknij prawym przyciskiem myszy i wybierz kolejno pozycje **Nowy** > **Dokument tekstowy**. Nadaj plikowi tekstowemu nazwę *MyTestFile*.

    ![Dodawanie nowego pliku tekstowego](media/storage-sync-files-extend-servers/new-file.png)

1. Zamknij **Eksploratora** i **Menedżera serwera**.

### <a name="download-the-azure-powershell-module"></a>Pobieranie modułu programu Azure PowerShell
Następnie na maszynie wirtualnej z systemem **Windows Server 2016 Datacenter** zainstaluj **moduł programu Azure PowerShell** na serwerze.

1. Na maszynie wirtualnej otwórz okno programu PowerShell z podwyższonym poziomem uprawnień.
1. Uruchom następujące polecenie:

   ```powershell
   Install-Module -Name Az -AllowClobber
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

Moduł `Az` to zbiorczy moduł poleceń cmdlet programu Azure PowerShell. Po jego zainstalowaniu są pobierane wszystkie dostępne moduły usługi Azure Resource Manager i są udostępniane do użycia ich polecenia cmdlet.

Na tym etapie zakończono konfigurowanie środowiska na potrzeby samouczka i wszystko jest gotowe do rozpoczęcia wdrażania **usługi synchronizacji magazynu**.

## <a name="deploy-the-service"></a>Wdrażanie usługi 
Wdrażanie usługi Azure File Sync zaczyna się od umieszczenia zasobu **usługi synchronizacji magazynu** w grupie zasobów dla wybranej subskrypcji. Usługa synchronizacji magazynu dziedziczy uprawnienia dostępu z subskrypcji i grupy zasobów, w których odbywa się wdrażanie.

1. W witrynie Azure Portal kliknij pozycję **Utwórz zasób**, a następnie wyszukaj usługę **Azure File Sync**.
1. W wynikach wyszukiwania wybierz usługę **Azure File Sync**.
1. Wybierz pozycję **Utwórz**, aby otworzyć kartę **Wdrażanie synchronizacji magazynu**.

   ![Wdrażanie synchronizacji magazynu](media/storage-sync-files-extend-servers/afs-info.png)

   W otwartym okienku wprowadź następujące informacje:

   | Wartość | Opis |
   | ----- | ----- |
   | **Nazwa** | Unikatowa nazwa (na subskrypcję) dla usługi synchronizacji magazynu.<br><br>W tym samouczku używamy nazwy *afssyncservice02*. |
   | **Subskrypcja** | Subskrypcja używana na potrzeby tego samouczka. |
   | **Grupa zasobów** | Grupa zasobów używana na potrzeby tego samouczka.<br><br>W tym samouczku używamy grupy zasobów *afsresgroup101918*. |
   | **Lokalizacja** | Wschodnie stany USA |

1. Po zakończeniu wybierz pozycję **Utwórz** w celu wdrożenia **usługi synchronizacji magazynu**.
1. Kliknij kartę **Powiadomienia**, a następnie pozycję **Przejdź do zasobu**.

## <a name="install-the-agent"></a>Instalowanie agenta
Agent usługi Azure File Sync to możliwy do pobrania pakiet, który umożliwia synchronizowanie systemu Windows Server z udziałem plików platformy Azure.

1. Przejdź z powrotem do maszyny wirtualnej z systemem **Windows Server 2016 Datacenter**, a następnie otwórz program **Internet Explorer**.
1. Przejdź do [Centrum pobierania Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). Przewiń w dół do sekcji **Agent usługi Azure File Sync**, a następnie kliknij pozycję **Pobierz**.

   ![Pobieranie agenta synchronizacji](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. Zaznacz pole wyboru dla pliku **StorageSyncAgent_V3_WS2016.EXE** i kliknij pozycję **Dalej**.

   ![Wybieranie agenta](media/storage-sync-files-extend-servers/select-agent.png)

1. Wybierz kolejno pozycje **Zezwalaj raz** > **Uruchom** > **Otwórz** plik.
1. Jeśli okno programu PowerShell nie zostało jeszcze zamknięte, wykonaj tę czynność.
1. Zaakceptuj wartości domyślne w **Kreatorze instalacji agenta synchronizacji magazynu**.
1. Kliknij pozycję **Zainstaluj**.
1. Kliknij przycisk **Zakończ**.

Wdrożono usługę synchronizacji z platformą Azure i zainstalowano agenta na maszynie wirtualnej z systemem **Windows Server 2016 Datacenter**. Teraz należy zarejestrować maszynę wirtualną przy użyciu **usługi synchronizacji magazynu**.

## <a name="register-windows-server"></a>Rejestrowanie systemu Windows Server
Zarejestrowanie systemu Windows Server za pomocą usługi synchronizacji magazynu powoduje ustanowienie relacji zaufania między serwerem (lub klastrem) oraz usługą synchronizacji magazynu. Serwer można zarejestrować tylko w jednej usłudze synchronizacji magazynu i można go synchronizować z innymi serwerami i udziałami plików platformy Azure skojarzonymi z tą samą usługą synchronizacji magazynu.

Interfejs użytkownika rejestracji serwera powinien zostać otwarty automatycznie po zainstalowaniu **agenta usługi Azure File Sync**. Jeśli tak się nie stanie, można otworzyć go ręcznie z lokalizacji pliku: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.

1. Po otwarciu interfejsu użytkownika rejestracji serwera na maszynie wirtualnej kliknij przycisk **OK**.
1. Kliknij pozycję **Zaloguj się**, aby rozpocząć.
1. Zaloguj się przy użyciu poświadczeń konta platformy Azure, a następnie kliknij pozycję **Zaloguj się**.
1. Podaj następujące informacje:

   ![Zrzut ekranu przedstawiający interfejs użytkownika rejestracji serwera](media/storage-sync-files-extend-servers/signin.png)

   | | |
   | ----- | ----- |
   | Wartość | Opis |
   | **Subskrypcja platformy Azure** | Subskrypcja, która zawiera usługę synchronizacji magazynu na potrzeby tego samouczka. |
   | **Grupa zasobów** | Grupa zasobów, która zawiera usługę synchronizacji magazynu na potrzeby tego samouczka. W tym samouczku używaliśmy grupy *afsresgroup101918*. |
   | **Usługa synchronizacji magazynu** | Nazwa usługi synchronizacji magazynu używana na potrzeby tego samouczka. W tym samouczku używaliśmy nazwy *afssyncservice02*. |

1. Kliknij pozycję **Zarejestruj**, aby ukończyć rejestrację serwera.
1. W ramach procesu rejestracji zostanie wyświetlony monit o dodatkowe logowanie. Zaloguj się, a następnie kliknij przycisk **Dalej**.
1. Kliknij przycisk **OK**.

## <a name="create-a-sync-group"></a>Tworzenie grupy synchronizacji
Grupa synchronizacji definiuje topologię synchronizacji dla zestawu plików. Grupa synchronizacji musi zawierać jeden punkt końcowy chmury, który reprezentuje udział plików platformy Azure, i co najmniej jeden punkt końcowy serwera. Punkt końcowy serwera reprezentuje ścieżkę na zarejestrowanym serwerze.

1. Aby utworzyć grupę synchronizacji, w witrynie [Azure Portal](https://portal.azure.com/) wybierz pozycję **+ Grupa synchronizacji** z poziomu usługi synchronizacji magazynu utworzonej w ramach tego samouczka. Jako przykłady w tym samouczku użyliśmy usługi *afssyncservice02*.

   ![Tworzenie nowej grupy synchronizacji w witrynie Azure Portal](media/storage-sync-files-extend-servers/add-sync-group.png)

1. W otwartym okienku wprowadź następujące informacje, aby utworzyć grupę synchronizacji z punktem końcowym chmury:

   | Wartość | Opis |
   | ----- | ----- |
   | **Nazwa grupy synchronizacji** | Ta nazwa musi być unikatowa w obrębie usługi synchronizacji magazynu, ale może być to dowolna nazwa logiczna z Twojego punktu widzenia. W tym samouczku używamy nazwy *afssyncgroup*.|
   | **Subskrypcja** | Subskrypcja, w której wdrożono usługę synchronizacji magazynu na potrzeby tego samouczka. |
   | **Konto magazynu** |Kliknij pozycję **Wybierz konto magazynu**. W wyświetlonym okienku wybierz konto magazynu, które zawiera udział plików platformy Azure utworzony na potrzeby tego samouczka. My użyliśmy konta *afsstoracct101918*. |
   | **Udział plików platformy Azure** | Nazwa udziału plików platformy Azure utworzonego na potrzeby tego samouczka. My użyliśmy nazwy *afsfileshare*. |

1. Kliknij pozycję **Utwórz**.

Jeśli wybierzesz grupę synchronizacji, zobaczysz, że masz teraz jeden **punkt końcowy w chmurze**.

## <a name="add-a-server-endpoint"></a>Dodawanie punktu końcowego serwera
Punkt końcowy serwera reprezentuje określoną lokalizację na zarejestrowanym serwerze, taką jak folder na woluminie serwera.

1. Aby dodać punkt końcowy serwera, wybierz nowo utworzoną grupę synchronizacji, a następnie wybierz pozycję **Dodaj punkt końcowy serwera**.

   ![Dodawanie nowego punktu końcowego serwera w okienku grupy synchronizacji](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. W okienku **Dodawanie punktu końcowego serwera** wprowadź następujące informacje, aby utworzyć punkt końcowy serwera:

   | | |
   | ----- | ----- |
   | Wartość | Opis |
   | **Zarejestrowany serwer** | Nazwa serwera utworzonego na potrzeby tego samouczka. My użyliśmy nazwy *afsvm101918*. |
   | **Ścieżka** | Ścieżka systemu Windows Server do dysku utworzonego na potrzeby tego samouczka. W naszym przykładzie jest to ścieżka *f:\filestosync*. |
   | **Obsługa warstw w chmurze** | Pozostaw tę funkcję wyłączoną na potrzeby tego samouczka. |
   | **Wolne miejsce w woluminie** | Pozostaw to pole puste na potrzeby tego samouczka. |

1. Kliknij pozycję **Utwórz**.

Pliki zostały teraz zsynchronizowane między udziałem plików platformy Azure i systemem Windows Server.

![Pomyślnie zsynchronizowana usługa Azure Storage](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Następne kroki
W tym samouczku przedstawiono podstawowe kroki rozszerzania pojemności magazynu w systemie Windows Server przy użyciu usługi Azure File Sync. Kliknij ten link, aby bardziej szczegółowo zapoznać się z planowaniem wdrażania usługi Azure File Sync.

> [!div class="nextstepaction"]
> [Planowanie wdrażania usługi Azure File Sync](./storage-sync-files-planning.md)
