---
title: Kopia zapasowa offline przy użyciu Azure Data Box
description: Dowiedz się, w jaki sposób można użyć Azure Data Box, aby wypełniać duże dane początkowej kopii zapasowej w trybie offline z agenta MARS do magazynu usługi Azure Recovery Services.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 553939df8aa7a1b097b2cd6c7f29365302101324
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027032"
---
# <a name="azure-backup-offline-backup-using-azure-data-box"></a>Azure Backup kopii zapasowej offline przy użyciu Azure Data Box

Za pomocą usługi [Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) można wypełniać duże początkowe kopie zapasowe Mars w trybie offline (bez użycia sieci) do magazynu usługi Azure Recovery Services.  Pozwala to zaoszczędzić czas i przepustowość sieci, które w przeciwnym razie byłyby wykorzystane do przeniesienia dużych ilości danych kopii zapasowej w trybie online w sieci o dużej opóźnieniu. To ulepszenie jest obecnie dostępne w wersji zapoznawczej. Kopia zapasowa w trybie offline oparta na Azure Data Box ma dwie różne zalety w odniesieniu do [kopii zapasowej offline usługi Azure Import/Export](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export).

1. Nie ma potrzeby pozyskiwania własnych dysków i łączników zgodnych z platformą Azure. Usługa Azure Data Box dostarcza dyski skojarzone z wybraną jednostką [SKU urządzenie Data Box](https://azure.microsoft.com/services/databox/data/)

2. Azure Backup (Agent MARS) może bezpośrednio zapisywać dane kopii zapasowej na obsługiwanych jednostkach SKU Azure Data Box. Eliminuje to konieczność aprowizacji lokalizacji tymczasowej dla danych początkowej kopii zapasowej i wymaganie, aby narzędzia do formatowania i kopiowania tych danych na dyski.

## <a name="azure-data-box-with-mars-agent"></a>Azure Data Box z agentem MARS

W tym artykule wyjaśniono, jak można użyć Azure Data Box, aby wypełniać duże dane początkowej kopii zapasowej w trybie offline z agenta MARS do magazynu usługi Azure Recovery Services. Artykuł jest podzielony na następujące części:

* Obsługiwane platformy
* Obsługiwane urządzenie Data Box jednostki SKU
* Wymagania wstępne
* Skonfiguruj agenta MARS
* Azure Data Box Instalatora
* Transfer danych kopii zapasowej do Azure Data Box
* Kroki po wykonaniu kopii zapasowej

## <a name="supported-platforms"></a>Obsługiwane platformy

Proces umieszczania danych inicjatora agenta MARS przy użyciu Azure Data Box jest obsługiwany przez następujące jednostki SKU systemu Windows:

| **OS**                                 | **SKU**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **Stacja**                        |                                                              |
| Windows 10 64 bit                     | Enterprise, Pro, Home                                       |
| Windows 8.1 64 bit                    | Enterprise, Pro                                             |
| Windows 8 64 bit                      | Enterprise, Pro                                             |
| Windows 7 64 bit                      | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| **Serwer**                             |                                                              |
| Windows Server 2019 64 bit            | Standard, Datacenter, Essentials                            |
| Windows Server 2016 64 bit            | Standard, Datacenter, Essentials                            |
| Windows Server 2012 R2 64 — bit         | Standard, Datacenter, Foundation                            |
| Windows Server 2012 64 bit            | Datacenter, Foundation, standard                            |
| Windows Storage Server 2016 64 bit    | Standard, Grupa robocza                                         |
| Windows Storage Server 2012 R2 64 — bit | Standard, Grupa robocza, podstawowe                              |
| Windows Storage Server 2012 64 bit    | Standard, Grupa robocza                                         |
| Windows Server 2008 R2 z dodatkiem SP1 64 bit     | Standard, Enterprise, Datacenter, Foundation                |
| Windows Server 2008 z dodatkiem SP2 64 bit        | Standard, Enterprise i Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Rozmiar danych kopii zapasowych i obsługiwane urządzenie Data Box jednostek SKU

| Rozmiar danych kopii zapasowej (po kompresji przez MARS) * na serwer | Obsługiwana Azure Data Box jednostka SKU                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| < = 7,2 TB                                                    | [Azure Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| > 7,2 TB i < = 80 TB * *                                      | [Azure Data Box (100 TB)](https://docs.microsoft.com/azure/databox/data-box-overview) |

\* Typowe szybkości kompresji różnią się w zależności od 10-20% <br>
\* * Skontaktuj się z [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) , jeśli zamierzasz mieć więcej niż 80 TB początkowej kopii zapasowej danych dla jednego serwera MARS.

>[!IMPORTANT]
>Początkowe dane kopii zapasowej z pojedynczego serwera muszą być zawarte w obrębie jednego Azure Data Box lub Azure Data Box Disk i nie mogą być współużytkowane przez wiele urządzeń z tymi samymi lub różnymi jednostkami SKU. Jednak urządzenie Azure Data Box może zawierać początkowe kopie zapasowe z wielu serwerów.

## <a name="pre-requisites"></a>Wymagania wstępne

### <a name="azure-subscription-and-required-permissions"></a>Subskrypcja platformy Azure i wymagane uprawnienia

* Proces wymaga subskrypcji platformy Azure
* Proces wymaga, aby użytkownik wyznaczył przeprowadzenie zasad kopii zapasowych w trybie offline to "właściciel" subskrypcji platformy Azure
* W ramach tych samych subskrypcji wymagane jest zadanie urządzenie Data Box i magazyn Recovery Services (do którego dane muszą zostać umieszczone w magazynie).
* Zaleca się, aby docelowe konto magazynu skojarzone z zadaniem Azure Data Box i magazynem Recovery Services znajdować się w tym samym regionie. Nie jest to jednak konieczne.

### <a name="get-azure-powershell-370"></a>Pobierz Azure PowerShell 3.7.0

**Jest to najważniejszy warunek wstępny dla procesu**. Przed zainstalowaniem Azure PowerShell (Ver. 3.7.0), wykonaj następujące sprawdzenia * *

#### <a name="step-1-check-powershell-version"></a>Krok 1. sprawdzenie wersji programu PowerShell

* Uruchom program Windows PowerShell i uruchom następujące polecenie:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

* Jeśli dane wyjściowe są wyświetlane w wersji nowszej niż **3.7.0**, wykonaj krok 2 poniżej. W przeciwnym razie przejdź do kroku 3.

#### <a name="step-2-uninstall-the-powershell-version"></a>Krok 2. Odinstalowywanie wersji programu PowerShell

Odinstaluj bieżącą wersję programu PowerShell, wykonując następujące czynności:

* Usuń moduły zależne, uruchamiając następujące polecenie w programie PowerShell:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

* Uruchom następujące polecenie, aby upewnić się, że wszystkie moduły zależne zostały pomyślnie usunięte:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>Krok 3. instalacja programu PowerShell w wersji 3.7.0

Po sprawdzeniu, czy nie ma żadnych modułów AzureRM, Zainstaluj wersję 3.7.0 przy użyciu jednej z następujących metod:

* Z serwisu GitHub, [link](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)

LUB

* Uruchom następujące polecenie w oknie programu PowerShell:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell można również zainstalować przy użyciu pliku msi. Aby go usunąć, odinstaluj go przy użyciu opcji Odinstaluj programy w panelu sterowania.

### <a name="order-and-receive-the-data-box-device"></a>Zamówienie i odbieranie urządzenia urządzenie Data Box

Proces tworzenia kopii zapasowej w trybie offline przy użyciu usług MARS i Azure Data Box wymaga, aby wymagane urządzenia urządzenie Data Box były w stanie "dostarczone" przed wyzwoleniem kopii zapasowej offline przy użyciu agenta MARS. Zapoznaj się z [rozmiarem danych kopii zapasowych i obsługiwanymi urządzenie Data Box](#backup-data-size-and-supported-data-box-skus) jednostkami SKU, aby zamówić najbardziej odpowiednią jednostkę SKU dla danego wymagania. Wykonaj kroki opisane w [tym artykule](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered) , aby zamówić i odbierać urządzenie Data Box urządzenia.

>[!IMPORTANT]
>Nie wybieraj BlobStorage dla rodzaju konta. Agent MARS wymaga konta, które obsługuje stronicowe obiekty blob, które nie są obsługiwane, gdy wybrano BlobStorage. Zdecydowanie zalecamy wybranie opcji *Storage v2* (*ogólnego przeznaczenia w wersji 2*) jako rodzaj konta podczas tworzenia docelowego konta magazynu dla zadania Azure Data Box.

![Wybierz rodzaj konta w szczegółach wystąpienia](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-setup-the-mars-agent"></a>Instalowanie i Konfigurowanie agenta MARS

* Upewnij się, że odinstalowano wszystkie poprzednie instalacje agenta MARS.

* Pobierz najnowszego agenta MARS z tego [miejsca](https://aka.ms/azurebackup_agent).

* Uruchom program *plik marsagentinstaller. exe* i wykonaj następujące**czynności,** aby [zainstalować i zarejestrować agenta](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent) w magazynie Recovery Services, do którego mają być przechowywane kopie zapasowe.

  >[!NOTE]
  > Magazyn Recovery Services musi znajdować się w tej samej subskrypcji co zadanie Azure Data Box.

* Po zarejestrowaniu agenta w magazynie Recovery Services wykonaj kroki opisane w poniższych sekcjach.

## <a name="setup-azure-data-box-devices"></a>Skonfiguruj urządzenia Azure Data Box

W zależności od zamówionej jednostki SKU Azure Data Box wykonaj kroki opisane w odpowiednich sekcjach poniżej, aby skonfigurować i przygotować urządzenia urządzenie Data Box dla agenta MARS do identyfikowania i przesyłania początkowych danych kopii zapasowej.

### <a name="setup-azure-data-box-disk"></a>Azure Data Box Disk Instalatora

Jeśli zostały uporządkowane co najmniej jeden Azure Data Box dysków (do 8 TB), wykonaj kroki opisane tutaj, aby [rozpakować, połączyć i odblokować Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up).

>[!NOTE]
>Istnieje możliwość, że serwer z agentem MARS nie ma portu USB. W takiej sytuacji można połączyć Azure Data Box Disk z innym serwerem/klientem i uwidocznić katalog główny urządzenia jako udział sieciowy.

### <a name="setup-azure-data-box"></a>Azure Data Box Instalatora

Jeśli użytkownik zamówił Azure Data Box (do 100 TB), wykonaj kroki opisane tutaj, [Aby skonfigurować urządzenie Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up).

#### <a name="mount-your-azure-data-box-as-local-system"></a>Zainstaluj Azure Data Box jako system lokalny

Agent MARS działa w kontekście systemu lokalnego i w związku z tym wymaga podania tego samego poziomu uprawnień do ścieżki instalacji, w której Azure Data Box jest połączony. Wykonaj poniższe kroki, aby upewnić się, że urządzenie urządzenie Data Box można zainstalować jako system lokalny przy użyciu protokołu NFS:

* Włącz opcję Klient dla systemu plików NFS na serwerze z systemem Windows (z zainstalowanym agentem MARS).<br>
  Określ alternatywne źródło: *wim: D: \Sources\Install.wim: 4*

* Pobierz PSExec z <https://download.sysinternals.com/files/PSTools.zip> na serwer z zainstalowanym agentem MARS.

* Otwórz wiersz polecenia z podwyższonym poziomem uprawnień i uruchom następujące polecenie z katalogiem zawierającym PSExec. exe jako bieżący katalog:

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

* Okno polecenia otwierane w wyniku powyższego polecenia znajduje się w kontekście systemu lokalnego. Za pomocą tego okna polecenia można wykonać kroki instalacji udziału usługi Azure Page BLOB jako dysku sieciowego na serwerze z systemem Windows.

* Wykonaj kroki opisane [tutaj](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box) , aby połączyć serwer z AGENTem Mars z urządzeniem urządzenie Data Box za pośrednictwem systemu plików NFS, a następnie uruchom następujące polecenie w wierszu polecenia systemu lokalnego, aby zainstalować udział obiektów BLOB na stronie platformy Azure:

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

* Po zainstalowaniu Sprawdź, czy możesz uzyskać dostęp do X: z serwera. Jeśli tak, przejdź do następnej sekcji tego artykułu.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Prześlij dane początkowej kopii zapasowej do urządzeń Azure Data Box

* Otwórz aplikację **Microsoft Azure Backup** na serwerze.

* Kliknij pozycję **Zaplanuj kopię zapasową** w okienku **Akcje** .

    ![Kliknij pozycję Planowanie kopii zapasowej](./media/offline-backup-azure-data-box/schedule-backup.png)

* Postępuj zgodnie z instrukcjami w **Kreatorze harmonogramu tworzenia kopii zapasowych**

* **Dodaj elementy** w taki sposób, aby łączny rozmiar elementów przekracza [limity rozmiaru obsługiwane przez](#backup-data-size-and-supported-data-box-skus) zamówione i odebrane jednostki SKU Azure Data Box.

    ![Dodaj elementy do kopii zapasowej](./media/offline-backup-azure-data-box/add-items.png)

* Wybierz odpowiednie harmonogramy i zasady przechowywania kopii zapasowych dla plików i folderów oraz stanu systemu (stan systemu dotyczy tylko serwerów z systemem Windows, a nie dla klientów systemu Windows)

* Na ekranie **Wybierz typ początkowej kopii zapasowej (pliki i foldery)** kreatora wybierz opcję **Transfer przy użyciu Microsoft Azure urządzenie Data Box dyski** i kliknij przycisk **dalej** .

    ![Wybierz typ początkowej kopii zapasowej](./media/offline-backup-azure-data-box/initial-backup-type.png)

* Zaloguj się do platformy Azure po wyświetleniu monitu przy użyciu poświadczeń użytkownika mających dostęp właściciela do subskrypcji platformy Azure. Po pomyślnym wykonaniu tej czynności powinien zostać wyświetlony ekran podobny do przedstawionego poniżej:

    ![Tworzenie zasobów i stosowanie wymaganych uprawnień](./media/offline-backup-azure-data-box/creating-resources.png)

* Następnie Agent MARS pobierze zadania urządzenie Data Box w subskrypcji, która jest w stanie "dostarczone".

    ![Pobieranie zadań DATAbox dla identyfikatora subskrypcji](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

* Wybierz prawidłową kolejność pól danych, dla której zostało rozpakowane, połączone i odblokowane urządzenie Data Box dysk. Kliknij przycisk **Dalej**.

    ![Wybierz zamówienia urządzenie Data Box](./media/offline-backup-azure-data-box/select-databox-order.png)

* Kliknij pozycję **Wykryj urządzenie** na ekranie **Wykrywanie urządzenia urządzenie Data Box** . Dzięki temu Agent MARS skanuje w poszukiwaniu lokalnie dołączonych dysków Azure Data Box i wykrywa je jak pokazano poniżej:

    ![Wykrywanie urządzenia urządzenie Data Box](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Jeśli Azure Data Box jako udział sieciowy (z powodu niedostępności portów USB lub zamówione i zainstalowane urządzenie o pojemności 100 TB urządzenie Data Box), wykrywanie zakończy się niepowodzeniem, ale zapewnia opcję wprowadzenia ścieżki sieciowej do urządzenia urządzenie Data Box jako s Hown poniżej:

    ![Wprowadź ścieżkę sieciową](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Podaj ścieżkę sieciową do katalogu głównego dysku Azure Data Box. Ten katalog musi zawierać katalog o nazwie *PageBlob* , jak pokazano poniżej:
    >
    >![Katalog główny Azure Data Box dysku](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Na przykład jeśli ścieżka dysku jest `\\mydomain\myserver\disk1\` a *disk1* zawiera katalog o nazwie *PageBlob*, ścieżka do podanego w kreatorze agenta Mars jest `\\mydomain\myserver\disk1\`
    >
    >W przypadku [skonfigurowania urządzenia z Azure Data Box 100 TB](#setup-azure-data-box)należy podać następujące elementy jako ścieżkę sieciową do urządzenia `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob`

* Kliknij przycisk **dalej** , a następnie kliknij przycisk **Zakończ** na następnym ekranie, aby zapisać zasady tworzenia kopii zapasowych i przechowywania z konfiguracją kopii zapasowej offline przy użyciu Azure Data Box.

* Poniższy ekran potwierdza, że zasady zostały pomyślnie zapisane:

    ![Zasady zostały pomyślnie zapisane](./media/offline-backup-azure-data-box/policy-saved.png)

* Kliknij przycisk **Zamknij** na powyższym ekranie.

* Kliknij pozycję ***Wykonaj kopię zapasową teraz** w okienku **Akcje** konsoli agenta Mars i kliknij pozycję **Utwórz kopię zapasową** na ekranie kreatora, jak pokazano poniżej:

    ![Kreator tworzenia kopii zapasowej teraz](./media/offline-backup-azure-data-box/backup-now.png)

* Agent MARS rozpocznie tworzenie kopii zapasowych wybranych danych na urządzeniu Azure Data Box. Może to potrwać od kilku godzin do kilku dni, w zależności od liczby plików i szybkości połączenia między serwerem a agentem MARS i Azure Data Box Disk.

* Po zakończeniu tworzenia kopii zapasowej danych na agencie MARS zostanie wyświetlony ekran podobny do przedstawionego poniżej:

    ![Pokazany postęp tworzenia kopii zapasowej](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Kroki po wykonaniu kopii zapasowej

W tej sekcji opisano kroki, które należy wykonać po pomyślnym wykonaniu kopii zapasowej danych Azure Data Box Disk.

* Wykonaj kroki opisane w tym artykule, aby [dostarczyć Azure Data Box dysk do platformy Azure](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up). Jeśli używasz urządzenia z systemem Azure Data Box 100 TB, wykonaj następujące kroki, aby [dostarczyć Azure Data Box do platformy Azure](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up).

* [Monitoruj zadanie urządzenie Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify) w Azure Portal. Gdy zadanie Azure Data Box zostanie ukończone, Agent MARS automatycznie przeniesie dane z konta magazynu do magazynu Recovery Services w czasie następnej zaplanowanej kopii zapasowej. Spowoduje to oznaczenie zadania tworzenia kopii zapasowej jako "ukończono zadanie", jeśli punkt odzyskiwania zostanie pomyślnie utworzony.

    >[!NOTE]
    >Agent MARS będzie wyzwalał kopie zapasowe w planowanych godzinach podczas tworzenia zasad. Jednak te zadania spowodują Oflagowanie "Oczekiwanie na ukończenie zadania Azure Data Boxgo" do czasu ukończenia zadania.

* Po pomyślnym utworzeniu przez agenta MARS punktu odzyskiwania odpowiadającego początkowej kopii zapasowej można usunąć konto magazynu (lub określoną zawartość) skojarzone z zadaniem Azure Data Box.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Agent Microsoft Azure Backup (MAB) tworzy aplikację usługi Azure AD dla Ciebie w dzierżawie. Ta aplikacja wymaga certyfikatu do uwierzytelnienia, które jest tworzone i przekazywane podczas konfigurowania zasad umieszczania w trybie offline. Używamy Azure PowerShell do tworzenia i przekazywania certyfikatu do aplikacji usługi Azure AD.

### <a name="issue"></a>Problem

Podczas konfigurowania kopii zapasowej w trybie offline może wystąpić problem, na przykład z powodu błędu w poleceniu cmdlet Azure PowerShell nie można dodać wielu certyfikatów do tej samej aplikacji usługi Azure AD utworzonej przez agenta MAB. Będzie to miało wpływ na to, jeśli skonfigurowano zasady wypełniania w trybie offline dla tego samego lub innego serwera.

### <a name="how-to-verify-if-the-issue-is-caused-by-this-specific-root-cause"></a>Jak sprawdzić, czy problem jest spowodowany przez tę konkretną przyczynę główną

Aby upewnić się, że przyczyną błędu jest ten problem, wykonaj jedną z następujących czynności:

#### <a name="step-1"></a>Krok 1

Sprawdź, czy podczas konfigurowania kopii zapasowej offline w konsoli MAB zostanie wyświetlony następujący komunikat o błędzie:

![Nie można utworzyć zasad kopii zapasowych offline dla bieżącego konta platformy Azure](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2"></a>Krok 2

* Otwórz folder **temp** w ścieżce instalacji (domyślna ścieżka folderu tymczasowego to *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*). Wyszukaj plik **CBUICurr** i Otwórz plik.

* W pliku **CBUICurr** przewiń do ostatniego wiersza i sprawdź, czy przyczyną błędu jest `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed`.

### <a name="workaround"></a>Obejście

Aby rozwiązać ten problem, należy wykonać następujące czynności, a następnie ponownie wykonać konfigurację zasad.

#### <a name="first-step"></a>Pierwszy krok

Zaloguj się do programu PowerShell, który jest wyświetlany w interfejsie użytkownika usługi MAB przy użyciu innego konta z dostępem administratora w ramach subskrypcji, która ma utworzone zadanie importowania eksportu.

#### <a name="second-step"></a>Drugi krok

Jeśli żaden inny serwer nie ma skonfigurowanych wypełnień w trybie offline i żaden inny serwer nie jest zależny od aplikacji `AzureOfflineBackup_<Azure User Id>`, Usuń tę aplikację z **Azure Portal** > **Azure Active Directory** > **rejestracje aplikacji.**

>[!NOTE]
> Sprawdź, czy aplikacja `AzureOfflineBackup_<Azure User Id>` nie ma skonfigurowanych żadnych innych rozliczeń w trybie offline, a także inny serwer nie jest zależny od tej aplikacji. Przejdź do pozycji **ustawienia** > **klucze** w sekcji **klucze publiczne** nie należy dodawać żadnych innych kluczy publicznych. Aby uzyskać informacje, zobacz następujący zrzut ekranu:
>
>![Klucze publiczne](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="third-step"></a>Trzeci krok

Na serwerze, który próbujesz skonfigurować kopię zapasową offline, wykonaj następujące czynności:

1. Otwórz kartę **Zarządzanie aplikacjami certyfikatów komputerów** > **osobista** i poszukaj certyfikatu z nazwą `CB_AzureADCertforOfflineSeeding_<ResourceId>`

2. Wybierz powyższy certyfikat, kliknij prawym przyciskiem myszy **wszystkie zadania** i **Eksportuj** bez klucza prywatnego w formacie CER.

3. Przejdź do aplikacji usługi Azure offline Backup wymienionej w **punkcie 2**. W obszarze **ustawienia** > **klucze** > **Przekaż klucz publiczny** Przekaż certyfikat wyeksportowany w powyższym kroku.

    ![Przekaż klucz publiczny](./media/offline-backup-azure-data-box/upload-public-key.png)

4. Na serwerze otwórz rejestr, wpisując **regedit** w oknie uruchamiania.

5. Przejdź do rejestru *komputer \ HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\Config\CloudBackupProvider.* Kliknij prawym przyciskiem myszy pozycję **CloudBackupProvider** i Dodaj nową wartość ciągu o nazwie `AzureADAppCertThumbprint_<Azure User Id>`

    >[!NOTE]
    > Aby uzyskać identyfikator użytkownika platformy Azure, wykonaj jedną z następujących czynności:
    >
    >1. Na platformie Azure połączonej z programem PowerShell uruchom polecenie `Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as defined in the portal”`.
    > 2. Przejdź do ścieżki rejestru: *Computer \ HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\DbgSettings\OnlineBackup*; Nazwa: *CurrentUserId*

6. Kliknij prawym przyciskiem myszy ciąg dodany w powyższym kroku i wybierz polecenie **Modyfikuj**. W polu wartość Podaj odcisk palca certyfikatu wyeksportowanego w **punkcie 2** i kliknij przycisk **OK**.

7. Aby uzyskać wartość odcisku palca, kliknij dwukrotnie certyfikat, a następnie wybierz kartę **szczegóły** i przewiń w dół do momentu wyświetlenia pola odcisk palca. Kliknij pozycję **odcisk palca** i skopiuj wartość.

    ![Pole odcisku palca certyfikatu](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Pytania

Aby dowiedzieć się więcej na temat wszelkich pytań lub wyjaśnień, skontaktuj się z [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)
