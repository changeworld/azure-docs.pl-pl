---
title: Tworzenie kopii zapasowej w trybie offline przy użyciu usługi Azure Data Box
description: Dowiedz się, jak za pomocą usługi Azure Data Box do wysiewu dużych początkowych danych kopii zapasowej w trybie offline z agenta MARS do magazynu usług odzyskiwania.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: a031a8cac357e7d212f8f6a3a5dbec749fbccc21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672963"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>Tworzenie kopii zapasowej w trybie online usługi Azure przy użyciu usługi Azure Data Box

Za pomocą [usługi Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) można zasiewać duże początkowe kopie zapasowe usług odzyskiwania platformy Microsoft Azure (MARS) w trybie offline (bez użycia sieci) w magazynie usług odzyskiwania. Ten proces oszczędza czas i przepustowość sieci, które w przeciwnym razie byłyby zużywane przenoszenie dużych ilości kopii zapasowych danych w trybie online za pośrednictwem sieci o dużym opóźnieniu. To ulepszenie jest obecnie w wersji zapoznawczej. Kopia zapasowa w trybie offline oparta na usłudze Azure Data Box zapewnia dwie wyraźne zalety w stosunku [do kopii zapasowej w trybie offline na podstawie usługi Import/Eksport platformy Azure:](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)

* Nie ma potrzeby zamawiania własnych dysków i łączników zgodnych z platformą Azure. Usługa Azure Data Box wysyła dyski skojarzone z wybraną jednostką [SKU pola danych](https://azure.microsoft.com/services/databox/data/).
* Usługa Azure Backup (MARS Agent) może bezpośrednio zapisywać dane kopii zapasowej na obsługiwanych jednostkach SKU platformy Azure Data Box. Ta funkcja eliminuje potrzebę aprowizowania lokalizacji tymczasowej dla początkowych danych kopii zapasowej. Nie są również potrzebne narzędzia do formatowania i kopiowania tych danych na dyski.

## <a name="azure-data-box-with-the-mars-agent"></a>Skrzynka danych platformy Azure z agentem MARS

W tym artykule wyjaśniono, jak można użyć usługi Azure Data Box do wysiewu dużych początkowych danych kopii zapasowej w trybie offline z agenta MARS do magazynu usług odzyskiwania.

## <a name="supported-platforms"></a>Obsługiwane platformy

Proces wysiewu danych z agenta MARS przy użyciu usługi Azure Data Box jest obsługiwany na następujących jednostkach SKU systemu Windows.

| **System operacyjny**                                 | **Numer jednostki magazynowej**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **Stacja robocza**                        |                                                              |
| Windows 10 (wersja 64-bitowa)                     | Enterprise, Pro, Home                                       |
| Windows 8.1 (wersja 64-bitowa)                    | Enterprise, Pro                                             |
| Windows 8 (wersja 64-bitowa)                      | Enterprise, Pro                                             |
| Windows 7 (wersja 64-bitowa)                      | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| **Serwer**                             |                                                              |
| Windows Server 2019 (wersja 64-bitowa)            | Standard, Datacenter, Essentials                            |
| Windows Server 2016 (wersja 64-bitowa)            | Standard, Datacenter, Essentials                            |
| Windows Server 2012 R2 (wersja 64-bitowa)         | Standard, Datacenter, Foundation                            |
| Windows Server 2012 (wersja 64-bitowa)            | Datacenter, Foundation, Standard                            |
| Windows Storage Server 2016 (wersja 64-bitowa)    | Standard, Workgroup                                         |
| Windows Storage Server 2012 R2 (wersja 64-bitowa) | Standard, Workgroup, Essential                              |
| Windows Storage Server 2012 (wersja 64-bitowa)    | Standard, Workgroup                                         |
| Windows Server 2008 R2 z dodatkiem SP1 (wersja 64-bitowa)     | Standard, Enterprise, Datacenter, Foundation                |
| Windows Server 2008 z dodatku SP2 64 bit        | Standard, Enterprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Tworzenie kopii zapasowych rozmiarów danych i obsługiwane jednostki SKU data box

| Rozmiar danych kopii zapasowej (po kompresji przez MARS)* na serwer | Obsługiwane jednostki SKU pola danych platformy Azure                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <=7,2 TB                                                    | [Dysk skrzynki danych platformy Azure](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| >7,2 TB i <=80 TB**                                      | [Skrzynka danych platformy Azure (100 TB)](https://docs.microsoft.com/azure/databox/data-box-overview) |

*Typowe współczynniki kompresji wahają się od 10% do 20%. <br>
**Jeśli spodziewasz się mieć więcej niż 80 TB początkowych [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)danych kopii zapasowej dla jednego serwera MARS, skontaktuj się z .

>[!IMPORTANT]
>Początkowe dane kopii zapasowej z jednego serwera muszą znajdować się w jednym wystąpieniu usługi Azure Data Box lub dysku usługi Azure Data Box i nie mogą być współużytkowane między wieloma urządzeniami tego samego lub innego jednostki SKU. Ale urządzenie usługi Azure Data Box może zawierać początkowe kopie zapasowe z wielu serwerów.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-subscription-and-required-permissions"></a>Subskrypcja platformy Azure i wymagane uprawnienia

* Proces wymaga subskrypcji platformy Azure.
* Proces wymaga, aby użytkownik wyznaczony do wykonywania zasad tworzenia kopii zapasowych w trybie offline był właścicielem subskrypcji platformy Azure.
* Zadanie Data Box i magazyn usług odzyskiwania (do którego dane muszą być rozstawione) muszą być w tych samych subskrypcji.
* Firma Microsoft zaleca, aby konto magazynu docelowego skojarzone z zadaniem usługi Azure Data Box i magazynem usług odzyskiwania znajdują się w tym samym regionie. Nie jest to jednak konieczne.

### <a name="get-azure-powershell-370"></a>Pobierz program Azure PowerShell 3.7.0

*Jest to najważniejszy warunek wstępny procesu.* Przed zainstalowaniem programu Azure PowerShell w wersji 3.7.0 należy przeprowadzić następujące kontrole.

#### <a name="step-1-check-the-powershell-version"></a>Krok 1: Sprawdź wersję programu PowerShell

1. Otwórz program Windows PowerShell i uruchom następujące polecenie:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1.  Jeśli na wyjściu wyświetlana jest wersja wyższa niż 3.7.0, wykonaj "Krok 2". W przeciwnym razie przejdź do "Kroku 3".

#### <a name="step-2-uninstall-the-powershell-version"></a>Krok 2: Odinstalowanie wersji programu PowerShell

Odinstaluj bieżącą wersję programu PowerShell.

1. Usuń moduły zależne, uruchamiając następujące polecenie w programie PowerShell:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. Aby zapewnić pomyślne usunięcie wszystkich modułów zależnych, uruchom następujące polecenie:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>Krok 3: Instalowanie programu PowerShell w wersji 3.7.0

Po sprawdzeniu, że nie ma żadnych modułów usługi AzureRM, zainstaluj wersję 3.7.0 przy użyciu jednej z następujących metod:

* Użyj [tego linku](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)w usłudze GitHub .

Możesz też:

* Uruchom następujące polecenie w oknie programu PowerShell:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Program Azure PowerShell mógł również zostać zainstalowany przy użyciu pliku msi. Aby go usunąć, odinstaluj go za pomocą opcji **Odinstaluj programy** w Panelu sterowania.

### <a name="order-and-receive-the-data-box-device"></a>Zamawianie i odbieranie urządzenia Data Box

Proces tworzenia kopii zapasowej w trybie offline przy użyciu mars i usługi Azure Data Box wymaga urządzeń Data Box być w stanie dostarczone przed wyzwoleniem kopii zapasowej w trybie offline przy użyciu agenta MARS. Aby zamówić najbardziej odpowiednią jednostkę SKU dla danego zapotrzebowania, zobacz [Rozmiar danych kopii zapasowej i obsługiwane jednostki SKU data box](#backup-data-size-and-supported-data-box-skus). Wykonaj kroki opisane w [samouczku: Zamów dysk usługi Azure Data Box,](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered) aby zamówić i odebrać urządzenia data box.

> [!IMPORTANT]
> Nie wybieraj *blobStorage* dla **rodzaju konta**. Agent MARS wymaga konta, które obsługuje obiekty blob strony, który nie jest obsługiwany po *wybraniu blobStorage.* Wybierz **magazyn v2 (ogólnego przeznaczenia w wersji 2)** jako **rodzaj konta** podczas tworzenia konta magazynu docelowego dla zadania usługi Azure Data Box.

![Wybieranie rodzaju konta w szczegółach wystąpienia](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>Instalowanie i konfigurowanie agenta MARS

1. Upewnij się, że odinstalowałeś wszystkie poprzednie instalacje agenta MARS.
1. Pobierz najnowszy MARS Agent z [tej strony](https://aka.ms/azurebackup_agent).
1. Uruchom *program MARSAgentInstaller.exe*i wykonaj *tylko* te czynności, aby zainstalować [i zarejestrować agenta](https://docs.microsoft.com/azure/backup/install-mars-agent#install-and-register-the-agent) w magazynie usług odzyskiwania, w którym mają być przechowywane kopie zapasowe.

   > [!NOTE]
   > Magazyn usług odzyskiwania musi znajdować się w tej samej subskrypcji co zadanie azure data box.

   Po zarejestrowaniu agenta w magazynie usług odzyskiwania wykonaj kroki opisane w następnych sekcjach.

## <a name="set-up-azure-data-box-devices"></a>Konfigurowanie urządzeń usługi Azure Data Box

W zależności od zamówionej jednostki SKU usługi Azure Data Box wykonaj kroki opisane w odpowiednich sekcjach, które należy wykonać. Kroki pokazują, jak skonfigurować i przygotować urządzenia Data Box dla agenta MARS do identyfikacji i przesyłania początkowych danych kopii zapasowej.

### <a name="set-up-azure-data-box-disks"></a>Konfigurowanie dysków usługi Azure Data Box

Jeśli zamówiłeś jeden lub więcej dysków usługi Azure Data Box (do 8 TB każdy), wykonaj kroki wymienione tutaj, aby [rozpakować, połączyć i odblokować dysk Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up).

>[!NOTE]
>Możliwe, że serwer z agentem MARS nie ma portu USB. W takiej sytuacji można połączyć dysk usługi Azure Data Box z innym serwerem lub klientem i udostępnić katalog główny urządzenia jako udział sieciowy.

### <a name="set-up-azure-data-box"></a>Konfigurowanie skrzynki danych platformy Azure

Jeśli zamówiłeś wystąpienie usługi Azure Data Box (do 100 TB), wykonaj kroki opisane w tym miejscu, [aby skonfigurować wystąpienie pola danych](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up).

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>Instalowanie wystąpienia usługi Azure Data Box jako systemu lokalnego

Agent MARS działa w kontekście systemu lokalnego, więc wymaga tego samego poziomu uprawnień, które mają być dostarczone do ścieżki instalacji, gdzie jest połączone wystąpienie usługi Azure Data Box. 

Aby upewnić się, że urządzenie Data Box można zamontować jako system lokalny przy użyciu protokołu NFS:

1. Włącz klienta dla funkcji NFS na serwerze Windows, który ma zainstalowany agent MARS. Określ alternatywne źródło *WIM:D:\Sources\Install.wim:4*.
1. Pobierz PSExec <https://download.sysinternals.com/files/PSTools.zip> z serwera z zainstalowanym agentem MARS.
1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień i uruchom następujące polecenie z katalogiem zawierającym *plik PSExec.exe* jako bieżący katalog.

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   Okno polecenia, które zostanie otwarte w wyniku poprzedniego polecenia, znajduje się w kontekście systemu lokalnego. To okno polecenia służy do wykonywania kroków w celu zainstalowania udziału obiektów blob strony platformy Azure jako dysku sieciowego na serwerze Windows.
1. Wykonaj kroki opisane w [obszarze Połącz z polem danych,](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box) aby połączyć serwer z agentem MARS z urządzeniem Data Box za pośrednictwem systemu plików NFS. Uruchom następujące polecenie w wierszu polecenia system lokalny, aby zainstalować udział obiektów blob strony platformy Azure.

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   Po zamontowaniu udziału sprawdź, czy można uzyskać dostęp do X: z serwera. Jeśli to możliwe, przejdź do następnej sekcji tego artykułu.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Przenoszenie danych początkowej kopii zapasowej na urządzenia usługi Azure Data Box

1. Otwórz aplikację **Microsoft Azure Backup** na serwerze.
1. W okienku **Akcje** wybierz pozycję **Zaplanuj tworzenie kopii zapasowej**.

    ![Wybierz pozycję Zaplanuj tworzenie kopii zapasowych](./media/offline-backup-azure-data-box/schedule-backup.png)

1. Postępuj zgodnie z instrukcjami **kreatora planowania tworzenia kopii zapasowych**.

1. Dodaj elementy, zaznaczając przycisk **Dodaj elementy.** Zachowaj całkowity rozmiar elementów w [granicach rozmiaru obsługiwanych przez jednostkę SKU usługi Azure Data Box,](#backup-data-size-and-supported-data-box-skus) którą zamówiłeś i odebrano.

    ![Dodawanie elementów do kopii zapasowej](./media/offline-backup-azure-data-box/add-items.png)

1. Wybierz odpowiedni harmonogram tworzenia kopii zapasowych i zasady przechowywania **plików i folderów** oraz **stanu systemu**. Stan systemu ma zastosowanie tylko do serwerów systemu Windows, a nie dla klientów systemu Windows.
1. Na stronie **Kreatora wybieranie początkowego typu kopii zapasowej (pliki i foldery)** wybierz opcję **Transfer przy użyciu dysków microsoft azure data box** i wybierz pozycję **Dalej**.

    ![Wybieranie początkowego typu kopii zapasowej](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. Zaloguj się na platformie Azure, gdy zostanie wyświetlony monit przy użyciu poświadczeń użytkownika, które mają dostęp właściciela w ramach subskrypcji platformy Azure. Po osiągnięciu tego celu powinna zostać wyświetlona strona podobna do tej.

    ![Tworzenie zasobów i stosowanie wymaganych uprawnień](./media/offline-backup-azure-data-box/creating-resources.png)

   Agent MARS następnie pobiera zadania data box w subskrypcji, które są w stanie Dostarczone.

    ![Pobieranie zadań pola danych dla identyfikatora subskrypcji](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. Wybierz prawidłową kolejność pól danych, dla której został rozpakowany, połączony i odblokowany dysk data box. Wybierz **pozycję Dalej**.

    ![Wybieranie zamówień pól danych](./media/offline-backup-azure-data-box/select-databox-order.png)

1. Wybierz **pozycję Wykryj urządzenie** na stronie **Wykrywanie urządzenia pola danych.** Ta akcja sprawia, że agent MARS skanowania lokalnie dołączone dyski azure data box i wykryć je.

    ![Wykrywanie urządzeń pola danych](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Jeśli wystąpienie usługi Azure Data Box został połączony jako udział sieciowy (z powodu niedostępności portów USB lub zamówionych i zamontowanych urządzenia data box o pojemności 100 TB), wykrywanie na początku kończy się niepowodzeniem. Możesz wprowadzić ścieżkę sieciową do urządzenia Data Box.

    ![Wprowadzanie ścieżki sieciowej](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Podaj ścieżkę sieciową do katalogu głównego dysku usługi Azure Data Box. Ten katalog musi zawierać katalog o nazwie *PageBlob*.
    >
    >![Katalog główny dysku usługi Azure Data Box](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Jeśli na przykład ścieżka dysku `\\mydomain\myserver\disk1\` jest i *disk1* zawiera katalog o nazwie *PageBlob*, ścieżka `\\mydomain\myserver\disk1\`wprowadzona na stronie Kreatora agenta MARS jest .
    >
    >Jeśli [skonfigurowanie urządzenia usługi Azure Data Box 100 TB,](#set-up-azure-data-box-devices)wprowadź `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` jako ścieżkę sieciową do urządzenia.

1. Wybierz **pozycję Dalej**i wybierz pozycję **Zakończ** na następnej stronie, aby zapisać zasady tworzenia kopii zapasowych i przechowywania z konfiguracją kopii zapasowej w trybie offline przy użyciu usługi Azure Data Box.

   Na poniższej stronie potwierdzono, że zasady zostały zapisane pomyślnie.

    ![Zasady zostały zapisane pomyślnie](./media/offline-backup-azure-data-box/policy-saved.png)

1. Wybierz **pozycję Zamknij** na poprzedniej stronie.

1. Wybierz **pozycję Sprzedaj teraz** w okienku **Akcje** konsoli agenta MARS. Wybierz **pozycję Zapas zapasowego** na stronie kreatora.

    ![Kreator tworzenia kopii zapasowej teraz](./media/offline-backup-azure-data-box/backup-now.png)

Agent MARS uruchamia wykonywanie kopii zapasowej danych wybranych na urządzeniu usługi Azure Data Box. Ten proces może potrwać od kilku godzin do kilku dni. Czas zależy od liczby plików i szybkości połączenia między serwerem z agentem MARS i dyskiem usługi Azure Data Box.

Po zakończeniu tworzenia kopii zapasowej danych na marsjańskim agencie zostanie wyświetlona strona podobna do tej.

![Pokazano postęp tworzenia kopii zapasowej](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Kroki po wykonaniu kopii zapasowej

W tej sekcji opisano kroki, które należy wykonać po wykonaniu kopii zapasowej danych na dysku azure data box.

* Wykonaj kroki opisane w tym artykule, aby [wysłać dysk usługi Azure Data Box na platformę Azure](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up). Jeśli użyto urządzenia usługi Azure Data Box o pojemności 100 TB, wykonaj następujące kroki, aby [wysłać urządzenie usługi Azure Data Box na platformę Azure.](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)

* [Monitorowanie zadania data box](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify) w witrynie Azure portal. Po zakończeniu zadania usługi Azure Data Box agent MARS automatycznie przenosi dane z konta magazynu do magazynu usług odzyskiwania w czasie następnej zaplanowanej kopii zapasowej. Następnie oznacza zadanie kopii zapasowej jako *Zadanie zakończone,* jeśli punkt odzyskiwania został pomyślnie utworzony.

    >[!NOTE]
    >Agent MARS wyzwala kopie zapasowe w czasie zaplanowanym podczas tworzenia zasad. Te zadania flagi "Oczekiwanie na zadanie usługi Azure Data Box do ukończenia" do czasu zakończenia zadania.

* Po mars agent pomyślnie tworzy punkt odzyskiwania, który odpowiada początkowej kopii zapasowej, można usunąć konto magazynu lub określonej zawartości skojarzone z zadania Azure Data Box.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Agent kopii zapasowej platformy Microsoft Azure (MAB) tworzy aplikację usługi Azure Active Directory (Azure AD) dla Ciebie w dzierżawie. Ta aplikacja wymaga certyfikatu do uwierzytelniania, który jest tworzony i przekazywał podczas konfigurowania zasad rozmieszczania w trybie offline. Używamy programu Azure PowerShell do tworzenia i przekazywania certyfikatu do aplikacji usługi Azure AD.

### <a name="problem"></a>Problem

Podczas konfigurowania kopii zapasowej w trybie offline, może napotkać problem z powodu błędu w powiększącym pliku cmdlet programu Azure PowerShell. Dodawanie wielu certyfikatów do tej samej aplikacji usługi Azure AD utworzonej przez agenta MAB może być niemożna dodać wielu certyfikatów. Ten problem będzie miał wpływ na ciebie, jeśli skonfigurowano zasady rozmieszczania w trybie offline dla tego samego lub innego serwera.

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>Sprawdź, czy problem jest spowodowany przez tę konkretną przyczynę

Aby sprawdzić, czy problem jest taki sam jak poprzednio opisany, wykonaj jedną z następujących czynności.

#### <a name="step-1"></a>Krok 1

Sprawdź, czy podczas konfigurowania kopii zapasowej w trybie offline w konsoli MAB jest wyświetlany następujący komunikat o błędzie.

![Nie można utworzyć zasad tworzenia kopii zapasowej w trybie offline dla bieżącego konta platformy Azure](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2"></a>Krok 2

1. Otwórz folder **Temp** w ścieżce instalacji. Domyślną ścieżką folderu tymczasowego jest *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*. Poszukaj pliku *CBUICurr* i otwórz plik.

1. W pliku *CBUICurr* przewiń do ostatniego wiersza i sprawdź, czy problem jest `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed`taki sam jak ten w tym komunikacie o błędzie: .

### <a name="workaround"></a>Obejście

Aby rozwiązać ten problem, wykonaj następujące czynności i spróbuj ponowić próbę konfiguracji zasad.

#### <a name="step-1"></a>Krok 1

Zaloguj się do programu PowerShell, który pojawia się w interfejsie użytkownika mab przy użyciu innego konta z dostępem administratora w subskrypcji, która będzie miała zadania importu lub eksportu utworzone.

#### <a name="step-2"></a>Krok 2

Jeśli żaden inny serwer nie ma skonfigurowania rozmieszczenia `AzureOfflineBackup_<Azure User Id>` w trybie offline i żaden inny serwer nie jest zależny od aplikacji, usuń tę aplikację. Wybierz **pozycję Rejestracje** > **aplikacji**usługi**Azure Portal Azure Active Directory** > .

>[!NOTE]
> Sprawdź, czy `AzureOfflineBackup_<Azure User Id>` aplikacja nie ma żadnych innych rozmieszczenia w trybie offline skonfigurowane, a także jeśli żaden inny serwer nie jest zależny od tej aplikacji. Przejdź do**pozycji Klawisze** **ustawień** > w sekcji **Klucze publiczne.** Nie powinien mieć żadnych innych kluczy publicznych dodane. Zobacz poniższy zrzut ekranu w celu uzyskania odwołania.
>
>![Klucze publiczne](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>Krok 3

Na serwerze, który próbujesz skonfigurować do wykonywania kopii zapasowej w trybie offline, wykonaj następujące akcje.

1. Przejdź do karty **Zarządzanie aplikacją** > certyfikatu komputera**i** `CB_AzureADCertforOfflineSeeding_<ResourceId>`poszukaj certyfikatu o nazwie .

2. Wybierz certyfikat, kliknij prawym przyciskiem myszy **pozycję Wszystkie zadania**i wybierz pozycję **Eksportuj** bez klucza prywatnego w formacie cer.

3. Przejdź do aplikacji do tworzenia kopii zapasowych w trybie offline platformy Azure, o których mowa w kroku 2. Wybierz pozycję **Ustawienia** > **Przekaż klucz** > **publiczny**. Przekaż certyfikat wyeksportowany w poprzednim kroku.

    ![Przekazywanie klucza publicznego](./media/offline-backup-azure-data-box/upload-public-key.png)

4. Na serwerze otwórz rejestr, wprowadzając **regedit** w oknie uruchomienia.

5. Przejdź do rejestru *Komputer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider.* Kliknij prawym przyciskiem myszy **pozycję CloudBackupProvider**i `AzureADAppCertThumbprint_<Azure User Id>`dodaj nową wartość ciągu o nazwie .

    >[!NOTE]
    > Aby uzyskać identyfikator użytkownika platformy Azure, wykonaj jedną z następujących akcji:
    >
    >* Z programu PowerShell połączonej `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` z platformą Azure uruchom polecenie.
    > * Przejdź do ścieżki rejestru *Komputer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup* o nazwie *CurrentUserId*.

6. Kliknij prawym przyciskiem myszy ciąg dodany w poprzednim kroku, a następnie wybierz polecenie **Modyfikuj**. W wartości podaj odcisk palca certyfikatu wyeksportowanego w kroku 2. Kliknij przycisk **OK**.

7. Aby uzyskać wartość odcisku palca, kliknij dwukrotnie certyfikat. Wybierz kartę **Szczegóły** i przewiń w dół, aż zobaczysz pole odcisku palca. Wybierz **opcję Odcisk palca**i skopiuj wartość.

    ![Pole certyfikatu odcisk palca](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Pytania

W przypadku jakichkolwiek pytań lub wyjaśnień [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)dotyczących jakichkolwiek problemów, z którymi się borykasz, skontaktuj się z .
