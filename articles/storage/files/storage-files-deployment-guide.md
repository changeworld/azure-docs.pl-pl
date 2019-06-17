---
title: Wdrażanie usługi Azure Files | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć usługę pliki Azure od początku do końca.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 05/22/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 416040d91da2f6346027f738de507777f804fd1f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66237733"
---
# <a name="how-to-deploy-azure-files"></a>Jak wdrożyć usługę Pliki Azure
[Usługa Azure Files](storage-files-introduction.md) oferuje w pełni zarządzane udziały plików w chmurze, które są dostępne za pośrednictwem standardowego protokołu SMB. W tym artykule Pokaż praktycznie wdrażanie usługi Azure Files w Twojej organizacji.

Zdecydowanie zalecamy przeczytanie [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md) przed czynności opisane w tym artykule.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że zostały już wykonane następujące czynności:

- Utworzone konto usługi Azure Storage z odpowiednie opcje odporności i szyfrowania, w regionie, w której wymagasz. Zobacz [Tworzenie konta magazynu](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) dla wskazówki krok po kroku dotyczące sposobu tworzenia konta magazynu.
- Utworzony udział plików platformy Azure z odpowiednią limitu przydziału w ramach konta magazynu. Zobacz [Utwórz udział plików](storage-how-to-create-file-share.md) dla wskazówki krok po kroku dotyczące sposobu tworzenia udziału plików.

## <a name="transfer-data-into-azure-files"></a>Przesyłanie danych do usługi Azure Files
Możesz przeprowadzić migrację istniejących udziałów plików, na przykład tych przechowywanych lokalnie, do nowego udziału plików platformy Azure. W tej sekcji opisano, jak do przenoszenia danych do udziału plików platformy Azure za pomocą kilku popularnych metod szczegółowe z [przewodnik planowania](storage-files-planning.md#data-transfer-method)

### <a name="azure-file-sync"></a>Azure File Sync
Usługa Azure File Sync umożliwia scentralizowanie udziałów plików Twojej organizacji w usłudze Azure Files bez rezygnacji z elastyczności, wydajności i zgodności lokalnego serwera plików. Robi to poprzez przekształcenie serwerów Windows w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS) i możesz mieć dowolną potrzebną Ci liczbę pamięci podręcznych na całym świecie.

Usługa Azure File Sync mogą służyć do migrowania danych do udziału plików platformy Azure, nawet jeśli mechanizm synchronizacji nie jest konieczne dla długoterminowego użytkowania. Więcej informacji na temat korzystania z usługi Azure File Sync na przesyłanie danych do udziału plików platformy Azure można znaleźć w [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md) i [sposób wdrażania usługi Azure File Sync](storage-sync-files-deployment-guide.md).

### <a name="azure-importexport"></a>Usługa Azure Import/Export
Usługa Azure Import/Export umożliwia bezpieczne przesyłanie dużych ilości danych do udziału plików platformy Azure przez wysyłanie dysków twardych do centrum danych platformy Azure. Zobacz [przesyłanie danych do usługi Azure storage za pomocą usługi Microsoft Azure Import/Export](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) dla bardziej szczegółowym omówieniem usługi.

> [!Note]  
> Usługa Azure Import/Export nie obsługuje eksportowania plików z udziału plików platformy Azure w tej chwili.

Poniższe kroki będą importowane dane z lokalizacji lokalnego do udziału plików platformy Azure.

1. Pozyskiwanie wymaganą liczbę dysków twardych do wiadomości e-mail na platformie Azure. Dyski twarde mogą być dowolnego rozmiaru dysku, ale musi być albo 2,5" lub 3,5" SSD lub HDD Obsługa standardu SATA II i SATA III. 

2. Connect and mount i każdego dysku na serwerze PC podczas transferu danych. Uzyskania optymalnej wydajności zaleca się uruchamiania zadania eksportu w środowisku lokalnym lokalnie na serwerze, który zawiera dane. W niektórych przypadkach, np. gdy serwer plików, która obsługuje dane jest urządzenie NAS to może nie być możliwe. W takiej sytuacji jest idealnie zgodna z instalacji każdego dysku na komputerze.

3. Upewnij się, każdy dysk jest w trybie online, inicjowany i jest przypisanej litery dysku. Aby przenieść dysk w trybie online, zainicjować i przypisać jej literę dysku, otwórz przystawkę programu MMC Zarządzanie dyskami (diskmgmt.msc).

    - Aby przywrócić dysk w trybie online (Jeśli nie jest jeszcze online), kliknij prawym przyciskiem myszy na dysku w dolnym okienku programu MMC Zarządzanie dyskami i wybierz "Online".
    - Aby zainicjować dysk, kliknij prawym przyciskiem myszy dysk w dolnym okienku (po dysk jest w trybie online) i wybierz pozycję "Initialize". Pamiętaj wybrać "GPT", po wyświetleniu monitu.

        ![Zrzut ekranu przedstawiający menu Zainicjuj dysk, w przystawce MMC Zarządzanie dyskami](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - Aby przypisać literę dysku do dysku, kliknij prawym przyciskiem myszy w obszarze "nieprzydzielone" dysku online i zainicjowany, a następnie kliknij przycisk "Nowy wolumin prosty". Pozwoli to przypisać literę dysku. Należy pamiętać, że nie trzeba sformatowania woluminu, jak zostanie to wykonane w dalszej części.

        ![Zrzut ekranu przedstawiający kreatora nowy wolumin prosty w przystawce MMC Zarządzanie dyskami](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. Utwórz plik CSV zestawu danych. Plik CSV zestaw danych jest mapowanie między ścieżka do danych w sieci lokalnej i udziału plików platformy Azure żądane dane powinny zostać skopiowane do. Na przykład następujący plik zestawu danych w pliku CSV mapuje udziału plików w środowisku lokalnym ("F:\shares\scratch") do udziału plików platformy Azure ("MyAzureFileShare"):
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    Można określić wiele udziałów z kontem magazynu. Zobacz [przygotowania pliku CSV zestawu danych](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-the-dataset-csv-file) Aby uzyskać więcej informacji.

5. Utwórz plik CSV driveset. Plik CSV driveset Wyświetla listę dysków, które są dostępne dla lokalnego agenta eksportu. Na przykład, poniższy driveset CSV pliku listy `X:`, `Y:`, i `Z:` dysków, które mają być używane w lokalnej Eksportuj zadanie:

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    Zobacz [przygotowania pliku CSV driveset](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-initialdriveset-or-additionaldriveset-csv-file) Aby uzyskać więcej informacji.

6. Użyj [narzędzie WAImportExport](https://www.microsoft.com/download/details.aspx?id=55280) można skopiować danych do jednej lub więcej dysków twardych.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > Po zakończeniu przygotowania dysku nie należy modyfikować dane w pliku dziennika lub dysków twardych.

7. [Tworzenie zadania importu](../common/storage-import-export-data-to-files.md#step-2-create-an-import-job).
    
### <a name="robocopy"></a>Robocopy
ROBOCOPY jest narzędziem dobrze znanych kopiowania, które jest dostarczany z programem Windows i Windows Server. ROBOCOPY mogą służyć do przenoszenia danych do usługi pliki Azure przez zainstalowanie udziału plików lokalnie, a następnie użycie lokalizacji zainstalowanego jako miejsce docelowe polecenia Robocopy. Korzystanie z narzędzia Robocopy jest bardzo proste:

1. [Instalowanie udziału plików platformy Azure](storage-how-to-use-files-windows.md). Aby uzyskać optymalną wydajność zaleca się instalowanie udziału plików platformy Azure lokalnie na serwerze, który zawiera dane. W niektórych przypadkach, np. gdy serwer plików, która obsługuje dane jest urządzenie NAS to może nie być możliwe. W takiej sytuacji jest idealnie dopuszczalne, aby zainstalować udział plików platformy Azure na komputerze. W tym przykładzie `net use` jest używany w wierszu polecenia, aby zainstalować udział plików:

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. Użyj `robocopy` do przenoszenia danych do udziału plików platformy Azure, w wierszu polecenia:

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    Operacja ROBOCOPY ma szereg istotnych opcji, aby zmodyfikować działanie kopiowania, zgodnie z potrzebami. Aby uzyskać więcej informacji, Wyświetl [Robocopy](https://technet.microsoft.com/library/cc733145.aspx) ręczny.

### <a name="azcopy"></a>Narzędzie AzCopy
AzCopy to narzędzie wiersza polecenia przeznaczone do kopiowania danych do i z usługi Azure Files, a także usługi Azure Blob storage przy użyciu prostych poleceń z optymalną wydajnością. Narzędzie AzCopy jest prosty w użyciu:

1. Pobierz [najnowszą wersję programu AzCopy na Windows](https://aka.ms/downloadazcopy) lub [Linux](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux#download-and-install-azcopy).
2. Użyj `azcopy` do przenoszenia danych do udziału plików platformy Azure, w wierszu polecenia. Składnia na Windows jest w następujący sposób: 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    W systemie Linux Składnia polecenia jest nieco inne:

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    Narzędzie AzCopy jest znaczna liczba opcji do modyfikowania zachowania dotyczącego kopiowania zgodnie z potrzebami. Aby uzyskać więcej informacji, Wyświetl [narzędzie AzCopy w Windows](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) i [narzędzia AzCopy w systemie Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="automatically-mount-on-needed-pcsservers"></a>Automatycznie instalacji na potrzeby komputerów/serwerów
Aby zastąpić udziału plików w środowisku lokalnym, warto wstępnie Zainstaluj udziały na maszynach, które będą używane w. Można to zrobić automatycznie na liście maszyn.

> [!Note]  
> Instalowanie udziału plików platformy Azure wymaga przy użyciu klucza konta magazynu jako hasło, w związku z tym tylko zaleca się zainstalowanie w środowiskach zaufanych. 

### <a name="windows"></a>Windows
Programu PowerShell można uruchomić polecenie instalacji na wielu komputerach z. W poniższym przykładzie `$computers` ręcznie zostanie wypełnione, ale możesz generować listę komputerów, aby automatycznie zainstalować. Na przykład można wypełnić tej zmiennej z wynikami z usługi Active Directory.

```powershell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
Skrypt proste bash połączone przy użyciu protokołu SSH może przynieść ten sam wynik w poniższym przykładzie. `$computer` Zmiennej podobnie pozostało do wypełnienia przez użytkownika:

```
computer = ("MyComputer1" "MyComputer2" "MyComputer3" "MyComputer4")
for item in "${computer[@]}"
do
    ssh $item "sudo bash -c 'echo \"//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino\" >> /etc/fstab'", "sudo mount -a"
done
```

## <a name="next-steps"></a>Kolejne kroki
- [Planowanie wdrożenia usługi Azure File Sync](storage-sync-files-planning.md)
- [Rozwiązywanie problemów z usługi Azure Files na Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Rozwiązywanie problemów z usługi Azure Files w systemie Linux](storage-troubleshoot-linux-file-connection-problems.md)
