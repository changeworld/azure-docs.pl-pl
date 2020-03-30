---
title: Jak wdrożyć pliki platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak wdrażać pliki azure od początku do końca.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 38339defc9d06f3e809bc24f957ebbb30abb46d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598786"
---
# <a name="how-to-deploy-azure-files"></a>Jak wdrożyć usługę Pliki Azure
[Usługa Azure Files](storage-files-introduction.md) oferuje w pełni zarządzane udziały plików w chmurze, które są dostępne za pośrednictwem standardowego protokołu SMB. W tym artykule pokazano, jak praktycznie wdrożyć usługi Azure Files w organizacji.

Zdecydowanie zaleca się [przeczytanie planowania dla wdrożenia usługi Azure Files](storage-files-planning.md) przed wykonaniem kroków w tym artykule.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że wykonaliśmy już następujące kroki:

- Utworzono konto usługi Azure Storage z żądaną odpornością i opcjami szyfrowania w żądanym regionie. Aby uzyskać szczegółowe wskazówki dotyczące tworzenia konta magazynu, zobacz [Tworzenie konta magazynu.](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
- Utworzono udział plików platformy Azure z żądanym przydziałem na koncie magazynu. Zobacz [Tworzenie udziału plików,](storage-how-to-create-file-share.md) aby uzyskać szczegółowe wskazówki dotyczące tworzenia udziału plików.

## <a name="transfer-data-into-azure-files"></a>Przenoszenie danych do plików platformy Azure
Można przeprowadzić migrację istniejących udziałów plików, takich jak te przechowywane lokalnie, do nowego udziału plików platformy Azure. W tej sekcji pokazano, jak przenieść dane do udziału plików platformy Azure za pomocą kilku popularnych metod wyszczególnionych w [przewodniku planowania](storage-files-planning.md#migration)

### <a name="azure-file-sync"></a>Azure File Sync
Usługa Azure File Sync umożliwia scentralizowanie udziałów plików Twojej organizacji w usłudze Azure Files bez rezygnacji z elastyczności, wydajności i zgodności lokalnego serwera plików. W tym celu przekształcając serwery systemu Windows w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS) i możesz mieć dowolną potrzebną Ci liczbę pamięci podręcznych na całym świecie.

Usługa Azure File Sync może służyć do migracji danych do udziału plików platformy Azure, nawet jeśli mechanizm synchronizacji nie jest pożądany do długotrwałego użytku. Więcej informacji na temat sposobu przesyłania danych do udziału plików platformy Azure za pomocą usługi Azure File Sync można znaleźć w [obszarze Planowanie wdrożenia usługi Azure File Sync](storage-sync-files-planning.md) oraz jak [wdrożyć usługę Azure File Sync](storage-sync-files-deployment-guide.md).

### <a name="azure-importexport"></a>Usługa Azure Import/Export
Usługa importu/eksportu platformy Azure umożliwia bezpieczne przesyłanie dużych ilości danych do udziału plików platformy Azure przez wysyłanie dysków twardych do centrum danych platformy Azure. Aby uzyskać bardziej szczegółowe omówienie usługi, zobacz [Korzystanie z usługi importu/eksportowania platformy Microsoft Azure w celu przesyłania danych do magazynu platformy Azure.](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

> [!Note]  
> Usługa Azure Import/Export nie obsługuje eksportowania plików z udziału plików platformy Azure w tej chwili.

Poniższe kroki spowoduje zaimportować dane z lokalizacji lokalnej do udziału plików platformy Azure.

1. Zaopatrywać się w wymaganą liczbę dysków twardych do wysłania pocztą na platformę Azure. Dyski twarde mogą mieć dowolny rozmiar dysku, ale muszą być dyskami SSD 2,5" lub 3,5" lub dyskami twardymi obsługującymi standard SATA II lub SATA III. 

2. Połącz i zamontuj każdy dysk na serwerze/komputerze podczas przesyłania danych. Aby uzyskać optymalną wydajność, zaleca się uruchamianie lokalnego zadania eksportu lokalnego na serwerze, który zawiera dane. W niektórych przypadkach, na przykład gdy serwer plików, który obsługuje dane, jest urządzeniem NAS, może to nie być możliwe. W takim przypadku jest całkowicie dopuszczalne, aby zamontować każdy dysk na komputerze.

3. Upewnij się, że każdy dysk jest w trybie online, zainicjowany i jest przypisany literę dysku. Aby przywrócić dysk do trybu online, zainicjować i przypisać literę dysku, otwórz przystawkę MMC zarządzania dyskami (diskmgmt.msc).

    - Aby przesunąć dysk do trybu online (jeśli nie jest jeszcze w trybie online), kliknij prawym przyciskiem myszy na dysku w dolnym okienku programu MMC zarządzania dyskami i wybierz "Online".
    - Aby zainicjować dysk, kliknij prawym przyciskiem myszy dysk w dolnym okienku (po tym, jak dysk jest w trybie online), a następnie wybierz "Inicjuj". Pamiętaj, aby wybrać "GPT" na pytanie.

        ![Zrzut ekranu przedstawiający menu Inicjowanie dysku w programie MMC zarządzania dyskami](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - Aby przypisać literę dysku do dysku, kliknij prawym przyciskiem myszy "nieprzydzielone" miejsce na dysku online i zainicjowanym, a następnie kliknij "Nowy wolumin prosty". Pozwoli to przypisać literę dysku. Należy pamiętać, że nie trzeba formatować woluminu, ponieważ zostanie to zrobione później.

        ![Zrzut ekranu przedstawiający kreatora nowego woluminu prostego w programie MMC zarządzania dyskami](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. Utwórz plik CSV zestawu danych. Plik CSV zestawu danych jest mapowaniem między ścieżką do danych lokalnie i żądanego udziału pliku platformy Azure, do których dane powinny zostać skopiowane. Na przykład następujący zestaw danych CSV mapuje lokalny udział plików ("F:\shares\scratch") na udział plików platformy Azure ("MyAzureFileShare"):
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    Można określić wiele udziałów z kontem magazynu. Aby uzyskać więcej [informacji, zobacz Przygotowywanie pliku CSV zestawu danych.](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

5. Utwórz plik CSV zestawu dysków. Plik CSV zestawu dysków zawiera listę dysków dostępnych dla lokalnego agenta eksportu. Na przykład następujące listy `X:` `Y:`plików CSV zestawu `Z:` dysków i dyski, które mają być używane w zadaniu eksportu lokalnego:

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    Aby uzyskać więcej [informacji, zobacz Przygotowywanie pliku CSV zestawu dysków.](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

6. Użyj [narzędzia WAImportExport,](https://www.microsoft.com/download/details.aspx?id=55280) aby skopiować dane na jeden lub więcej dysków twardych.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > Nie należy modyfikować danych na dyskach twardych ani w pliku dziennika po zakończeniu przygotowania dysku.

7. [Tworzenie zadania importu](../common/storage-import-export-data-to-files.md#step-2-create-an-import-job).
    
### <a name="robocopy"></a>Rozszerzenie Robocopy
Robocopy to dobrze znane narzędzie do kopiowania, które jest dostarczane z systemami Windows i Windows Server. Robocopy może służyć do przesyłania danych do usługi Azure Files przez zamontowanie udziału plików lokalnie, a następnie przy użyciu zainstalowanej lokalizacji jako miejsca docelowego w robocopy polecenia. Korzystanie Robocopy jest dość proste:

1. [Zamontuj swój udział plików platformy Azure](storage-how-to-use-files-windows.md). Aby uzyskać optymalną wydajność, zaleca się instalowanie udziału plików platformy Azure lokalnie na serwerze, który zawiera dane. W niektórych przypadkach, na przykład gdy serwer plików, który obsługuje dane, jest urządzeniem NAS, może to nie być możliwe. W takim przypadku jest całkowicie dopuszczalne, aby zainstalować udział plików platformy Azure na komputerze. W tym `net use` przykładzie jest używany w wierszu polecenia do zainstalowania udziału plików:

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. Użyj `robocopy` w wierszu polecenia, aby przenieść dane do udziału plików platformy Azure:

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    Robocopy ma znaczną liczbę opcji, aby zmodyfikować zachowanie kopiowania zgodnie z potrzebami. Aby uzyskać więcej informacji, zobacz stronę podręcznika [Robocopy.](https://technet.microsoft.com/library/cc733145.aspx)

### <a name="azcopy"></a>Narzędzie AzCopy
AzCopy to narzędzie wiersza polecenia przeznaczone do kopiowania danych do i z usługi Azure Files, a także magazynu obiektów Blob platformy Azure przy użyciu prostych poleceń o optymalnej wydajności. Korzystanie z AzCopy jest łatwe:

1. Pobierz [najnowszą wersję AzCopy na Windows](https://aka.ms/downloadazcopy) lub [Linux](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux#download-and-install-azcopy).
2. Użyj `azcopy` w wierszu polecenia, aby przenieść dane do udziału plików platformy Azure. Składnia systemu Windows jest następująca: 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    W systemie Linux składnia poleceń jest nieco inna:

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    AzCopy ma znaczną liczbę opcji, aby zmodyfikować zachowanie kopiowania zgodnie z potrzebami. Aby uzyskać więcej informacji, zobacz [AzCopy w systemie Windows](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) i [AzCopy na Linuksie](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="automatically-mount-on-needed-pcsservers"></a>Automatyczne montowanie na potrzebnych komputerach/serwerach
Aby zastąpić lokalny udział plików, warto wstępnie zainstalować udziały na komputerach, na których będzie używany. Można to zrobić automatycznie na liście maszyn.

> [!Note]  
> Montaż udziału plików platformy Azure wymaga użycia klucza konta magazynu jako hasła, dlatego zaleca się montowanie tylko w zaufanych środowiskach. 

### <a name="windows"></a>Windows
Program PowerShell może być używany uruchom polecenie mount na wielu komputerach. W poniższym `$computers` przykładzie jest wypełniany ręcznie, ale można wygenerować listę komputerów do zainstalowania automatycznie. Na przykład można wypełnić tę zmienną wynikami z usługi Active Directory.

```powershell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
Prosty skrypt bash w połączeniu z SSH może przynieść ten sam wynik w poniższym przykładzie. Zmienna `$computer` jest podobnie pozostawiona do zapełnienia przez użytkownika:

```
computer = ("MyComputer1" "MyComputer2" "MyComputer3" "MyComputer4")
for item in "${computer[@]}"
do
    ssh $item "sudo bash -c 'echo \"//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino\" >> /etc/fstab'", "sudo mount -a"
done
```

## <a name="next-steps"></a>Następne kroki
- [Planowanie wdrożenia usługi Azure File Sync](storage-sync-files-planning.md)
- [Rozwiązywanie problemów z usługą Azure Files w systemie Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Rozwiązywanie problemów z usługą Azure Files w systemie Linux](storage-troubleshoot-linux-file-connection-problems.md)
