---
title: Wdrażanie usługi pliki Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć usługi pliki Azure od początku do końca.
services: storage
documentationcenter: ''
author: wmgries
manager: aungoo
editor: tamram
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2018
ms.author: wgries
ms.openlocfilehash: 38a0e64bf958ef6aebac41136ffcb9c213cc7ec0
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029785"
---
# <a name="how-to-deploy-azure-files"></a>Jak wdrożyć usługę Pliki Azure
[Usługa pliki Azure](storage-files-introduction.md) oferuje pełni zarządzanych udziałów plików w chmurze, które są dostępne przy użyciu standardowego protokołu SMB. W tym artykule opisano, jak praktycznie wdrażania plików Azure w ramach danej organizacji.

Zdecydowanie zaleca się odczytu [planowania wdrożenia usługi pliki Azure](storage-files-planning.md) przed wykonać kroki opisane w tym artykule.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że już zostały wykonane następujące kroki:

- Utworzone konto magazynu platformy Azure za pomocą odpowiednie opcje odporności i szyfrowania, w regionie, w której wymagasz. Zobacz [Utwórz konto magazynu](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) dla wskazówki krok po kroku dotyczące tworzenia konta magazynu.
- Utworzyć udział plików na platformę Azure z żądaną limitu na koncie magazynu. Zobacz [utworzyć udział plików](storage-how-to-create-file-share.md) dla wskazówki krok po kroku dotyczące sposobu tworzenia udziału plików.

## <a name="transfer-data-into-azure-files"></a>Transfer danych do usługi pliki Azure
Możesz przeprowadzić migrację istniejących udziałów plików, takich jak te przechowywane lokalnie, do nowego udziału plików na platformę Azure. W tej sekcji opisano sposób przenoszenia danych do udziału plików na platformę Azure za pośrednictwem kilku popularnych metod szczegółowe z [przewodnik planowania](storage-files-planning.md#data-transfer-method)

### <a name="azure-file-sync-preview"></a>Synchronizacja plików na platformę Azure (wersja zapoznawcza)
Synchronizacji plików platformy Azure (wersja zapoznawcza) umożliwia scentralizowane udziałów plików w organizacji w plikach Azure bez zwiększanie elastyczność, wydajności i zgodności serwera plików lokalnych. Robi to poprzez przekształcanie systemach Windows Servers przeznaczonych do szybkiego pamięci podręcznej udziału plików na platformę Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS) i możesz mieć dowolną potrzebną Ci liczbę pamięci podręcznych na całym świecie.

Azure synchronizacji plików może służyć do migracji danych do udziału plików na platformę Azure, nawet jeśli mechanizmu synchronizacji nie jest potrzebne do długoterminowego użycia. Więcej informacji na temat sposobu synchronizacji plików Azure umożliwia przesyłanie danych do udziału plików na platformę Azure można znaleźć w [planowania wdrożenia synchronizacji plików Azure](storage-sync-files-planning.md) i [Wdrażanie synchronizacji plików Azure](storage-sync-files-deployment-guide.md).

### <a name="azure-importexport"></a>Usługa Azure Import/Export
Usługa Import/Eksport Azure umożliwia bezpieczne transfer dużych ilości danych do udziału plików na platformę Azure poprzez wysyłanie dysków twardych do centrum danych Azure. Zobacz [przesyłanie danych do magazynu Azure za pomocą usługi Import/Eksport Microsoft Azure](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) dla bardziej szczegółowe omówienie usługi.

> [!Note]  
> Usługa Import/Eksport Azure nie obsługuje eksportu pliki z udziału plików na platformę Azure w tej chwili.

Poniższe kroki będą importowane dane z lokalnej lokalizacji do udziału plików na platformę Azure.

1. Uzyskaj wymaganej liczby dysków twardych na pocztę na platformie Azure. Dyski twarde mogą być o dowolnym rozmiarze dysku, ale musi być albo 2,5-calowe lub 3,5" dysków SSD i HDD obsługujące standard SATA II lub SATA III. 

2. Połącz i instalacji każdego dysku na serwerze PC podczas transferu danych. Aby uzyskać optymalną wydajność zaleca się uruchomić zadanie eksportu lokalnymi lokalnie na serwerze, który zawiera dane. W niektórych przypadkach, np. gdy serwer plików, który pełni dane urządzenie NAS to może nie być możliwe. W takim przypadku jest doskonale dopuszczalne instalacji każdego dysku w komputerze.

3. Upewnij się, każdy dysk jest w trybie online, zainicjować i jest przypisany literą dysku. Przełącz dysk do trybu online, zainicjować i przypisać literę dysku, Otwieranie przystawki programu MMC Zarządzanie dyskami (diskmgmt.msc).

    - Aby przywrócić dysk online (Jeśli nie jest jeszcze online), kliknij prawym przyciskiem myszy na dysku w dolnym okienku programu MMC Zarządzanie dyskami i wybierz "Online".
    - Aby zainicjalizować dysk, kliknij prawym przyciskiem myszy dysk w dolnym okienku (po dysk jest w trybie online), a następnie wybierz "Initialize". Należy wybrać "GPT", gdy pojawi się pytanie.

        ![Zrzut ekranu przedstawiający menu Inicjowanie dysku na zarządzanie dyskami programu MMC](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - Aby przypisać literę dysku na dysk, kliknij prawym przyciskiem myszy na "" nieprzydzielonym dysku online, jak i zainicjowane, a następnie kliknij przycisk "Nowy wolumin prosty". Dzięki temu można przypisać literę dysku. Należy pamiętać, że nie trzeba sformatowania woluminu, ponieważ spowoduje to zrobić później.

        ![Zrzut ekranu kreatora nowy wolumin prosty na zarządzanie dyskami programu MMC](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. Utwórz plik CSV zestawu danych. Plik CSV zestawu danych jest mapowanie między ścieżka do danych w sieci lokalnej i udział plików Azure odpowiednie dane powinien zostać skopiowany do. Na przykład następujący plik CSV dataset mapuje lokalnego udziału plików ("F:\shares\scratch") do udziału plików na platformę Azure ("MyAzureFileShare"):
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    Można określić wiele udziałów z kontem magazynu. Zobacz [przygotowania pliku CSV zestawu danych](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-the-dataset-csv-file) Aby uzyskać więcej informacji.

5. Utwórz plik CSV driveset. Driveset pliku CSV znajduje się lista dysków, które są dostępne do lokalnego agenta eksportu. Na przykład następujące driveset CSV pliku listy `X:`, `Y:`, i `Z:` dysków, które mają być używane w lokalnej zadanie eksportowania:

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    Zobacz [przygotowania pliku CSV driveset](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-initialdriveset-or-additionaldriveset-csv-file) Aby uzyskać więcej informacji.

6. Użyj [WAImportExport narzędzia](https://www.microsoft.com/en-us/download/details.aspx?id=55280) można skopiować danych do jednego lub więcej dysków twardych.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > Po zakończeniu przygotowywania dysków nie należy modyfikować dane na dyski twarde lub pliku dziennika.

7. [Utwórz zadanie importu](../common/storage-import-export-data-to-files.md#step-2-create-an-import-job).
    
### <a name="robocopy"></a>Robocopy
ROBOCOPY jest narzędziem dobrze znanych kopiowania jest dostarczana z systemem Windows i Windows Server. ROBOCOPY może służyć do przesyłania danych do usługi pliki Azure instalowanie udziału plików lokalnie, a następnie używając lokalizacji zainstalowanego jako miejsce docelowe polecenia Robocopy. Używanie Robocopy jest bardzo proste:

1. [Instalowanie udziału plików Azure](storage-how-to-use-files-windows.md). Aby uzyskać optymalną wydajność firma Microsoft zaleca instalowanie udziału plików na platformę Azure lokalnie na serwerze, który zawiera dane. W niektórych przypadkach, np. gdy serwer plików, który pełni dane urządzenie NAS to może nie być możliwe. W takim przypadku jest doskonale dopuszczalne, aby zainstalować udział plików na platformę Azure na komputerze. W tym przykładzie `net use` jest używany w wierszu polecenia, aby zainstalować udział plików:

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. Użyj `robocopy` do przenoszenia danych do udziału plików na platformę Azure, w wierszu polecenia:

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    ROBOCOPY ma znacznej liczby opcji, aby zmodyfikować zachowanie kopii zgodnie z potrzebami. Aby uzyskać więcej informacji, Wyświetl [Robocopy](https://technet.microsoft.com/library/cc733145.aspx) strony.

### <a name="azcopy"></a>Narzędzie AzCopy
Narzędzie AzCopy to narzędzie wiersza polecenia przeznaczone do kopiowania danych z magazynu obiektów Blob platformy Azure, a także pliki Azure przy użyciu prostych poleceń z optymalną wydajnością. Używanie narzędzia AzCopy jest prosty:

1. Pobierz [najnowszą wersję programu AzCopy w systemie Windows](http://aka.ms/downloadazcopy) lub [Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#download-and-install-azcopy).
2. Użyj `azcopy` w wierszu polecenia, aby przenieść dane do udziału plików na platformę Azure. W systemie Windows ma następującą składnię: 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    W systemie Linux Składnia polecenia jest nieco inne:

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    Narzędzie AzCopy ma znacznej liczby opcji, aby zmodyfikować zachowanie kopii zgodnie z potrzebami. Aby uzyskać więcej informacji, Wyświetl [AzCopy w systemie Windows](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) i [AzCopy w systemie Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="automatically-mount-on-needed-pcsservers"></a>Automatycznie instalacji na potrzeby komputerów/serwerów
Aby zastąpić lokalnymi udziału plików, warto wstępnie zainstalować na komputerach, które będą używane w akcji. Można to zrobić automatycznie na liście urządzeń.

> [!Note]  
> Instalowanie udziału plików na platformę Azure wymaga jako hasło przy użyciu klucza konta magazynu, w związku z tym tylko zaleca się instalowanie w środowiskach zaufanych. 

### <a name="windows"></a>Windows
PowerShell można uruchomić polecenie instalacji na wielu komputerach. W poniższym przykładzie `$computers` ręcznie zostanie wypełnione, ale można wygenerować listę komputerów, aby automatycznie zainstalować. Na przykład można wypełnić tej zmiennej z wyników z usługi Active Directory.

```PowerShell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
Skrypt proste bash łączone przy użyciu protokołu SSH umożliwia uzyskanie takiego samego wyniku w poniższym przykładzie. `$computer` Zmiennej podobnie pozostało do wypełnienia przez użytkownika:

```
computer = ("MyComputer1" "MyComputer2" "MyComputer3" "MyComputer4")
for item in "${computer[@]}"
do
    ssh $item "sudo bash -c 'echo \"//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino\" >> /etc/fstab'", "sudo mount -a"
done
```

## <a name="next-steps"></a>Kolejne kroki
- [Planowanie wdrożenia synchronizacji plików Azure](storage-sync-files-planning.md)
- [Rozwiązywanie problemów z plików Azure w systemie Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Rozwiązywanie problemów z plików Azure w systemie Linux](storage-troubleshoot-linux-file-connection-problems.md)