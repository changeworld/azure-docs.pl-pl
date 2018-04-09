---
title: Instalowanie udziału plików platformy Azure i uzyskiwanie dostępu do udziału w systemie Windows | Microsoft Docs
description: Zainstaluj udział plików platformy Azure i uzyskaj dostępu do tego udziału w systemie Windows.
services: storage
documentationcenter: na
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: 8905b708101e78691c14168edf7afd659afa92a4
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="mount-an-azure-file-share-and-access-the-share-in-windows"></a>Instalowanie udziału plików platformy Azure i uzyskiwanie dostępu do udziału w systemie Windows
[Azure Files](storage-files-introduction.md) to łatwy w użyciu system plików w chmurze firmy Microsoft. Udziały plików platformy Azure można instalować w systemie Windows i Windows Server. W tym artykule przedstawiono trzy różne sposoby instalowania udziału plików platformy Azure w systemie Windows: za pomocą interfejsu użytkownika Eksploratora plików, programu PowerShell oraz wiersza polecenia. 

Aby móc zainstalować udział plików platformy Azure poza regionem świadczenia usługi Azure, w którym jest on hostowany, na przykład lokalnie lub w innym regionie świadczenia usługi Azure, system operacyjny musi obsługiwać protokół SMB 3.0. 

Udziały plików platformy Azure można zainstalować w instalacji systemu Windows działającej na maszynie wirtualnej platformy Azure lub lokalnie. W poniższej tabeli pokazano, które wersje systemów operacyjnych obsługują instalowanie udziałów plików w których środowiskach:

| Wersja systemu Windows        | Wersja protokołu SMB | Możliwa instalacja na maszynie wirtualnej platformy Azure | Możliwa instalacja w środowisku lokalnym |
|------------------------|-------------|-----------------------|----------------------|
| Windows Server Semi-Annual Channel<sup>1</sup> | SMB 3.0 | Yes | Yes |
| Windows 10<sup>2</sup>  | SMB 3.0 | Yes | Yes |
| Windows Server 2016    | SMB 3.0     | Yes                   | Yes                  |
| Windows 8.1            | SMB 3.0     | Yes                   | Yes                  |
| Windows Server 2012 R2 | SMB 3.0     | Yes                   | Yes                  |
| Windows Server 2012    | SMB 3.0     | Yes                   | Yes                  |
| Windows 7              | SMB 2.1     | Yes                   | Nie                   |
| Windows Server 2008 R2 | SMB 2.1     | Yes                   | Nie                   |

<sup>1</sup>Windows Server w wersji 1709.  
<sup>2</sup>Windows 10 w wersji 1507, 1607, 1703 i 1709.

> [!Note]  
> Zawsze zalecamy pobranie najnowszej aktualizacji KB dla danej wersji systemu Windows.

## <a name="aprerequisites-for-mounting-azure-file-share-with-windows"></a></a>Wymagania wstępne dotyczące instalowania udziału plików platformy Azure w systemie Windows 
* **Nazwa konta magazynu**: Aby zainstalować udział plików platformy Azure, konieczne będzie podanie nazwy konta magazynu.

* **Klucz konta magazynu**: Aby zainstalować udział plików platformy Azure, konieczne będzie posiadanie podstawowego (lub dodatkowego) klucza magazynu. Klucze sygnatur dostępu współdzielonego nie są aktualnie obsługiwane na potrzeby instalowania.

* **Otwarty port 445**: Usługa Azure Files korzysta z protokołu SMB. Protokół SMB komunikuje się za pośrednictwem portu TCP 445. Upewnij się, że Twoja zapora nie blokuje portów TCP 445 z komputera klienckiego.

## <a name="persisting-connections-across-reboots"></a>Utrwalanie połączeń między ponownymi uruchomieniami
### <a name="cmdkey"></a>CmdKey
Najprostszym sposobem ustanowienia połączeń trwałych jest zapisanie poświadczeń konta magazynu w systemie Windows za pomocą narzędzia wiersza polecenia „CmdKey”. Poniżej znajduje się przykładowy wiersz polecenia na potrzeby utrwalania poświadczeń konta magazynu w maszynie wirtualnej:
```
C:\>cmdkey /add:<yourstorageaccountname>.file.core.windows.net /user:<domainname>\<yourstorageaccountname> /pass:<YourStorageAccountKeyWhichEndsIn==>
```
> [!Note]
> Nazwą domeny w tym miejscu będzie „AZURE”

Narzędzie CmdKey pozwala również wyświetlić listę przechowywanych poświadczeń:

```
C:\>cmdkey /list
```
Dane wyjściowe będą następujące:

```
Currently stored credentials:

Target: Domain:target=<yourstorageaccountname>.file.core.windows.net
Type: Domain Password
User: AZURE\<yourstorageaccountname>
```
Utrwalonych poświadczeń nie trzeba podawać ponownie podczas nawiązywania połączenia z udziałem. Zamiast tego można połączyć się bez podawania poświadczeń.

## <a name="mount-the-azure-file-share-with-file-explorer"></a>Instalowanie udziału plików platformy Azure za pomocą Eksploratora plików
> [!Note]  
> Należy pamiętać, że następujące instrukcje dotyczą systemu Windows 10 i mogą być nieco inne w starszych wersjach. 

1. **Otwórz Eksploratora plików**: Można to zrobić przy użyciu menu Start lub przez naciśnięcie skrótu Win+E.

2. **Przejdź do elementu „Ten komputer” w lewej części okna. Spowoduje to zmianę menu dostępnego na wstążce. W menu Komputer wybierz pozycję „Mapuj dysk sieciowy”**.
    
    ![Zrzut ekranu przedstawiający menu rozwijane „Mapuj dysk sieciowy”](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

3. **Skopiuj ścieżkę UNC z okienka „Połącz” w witrynie Azure Portal.** 

    ![Ścieżka UNC z okienka Połącz usługi Azure Files](./media/storage-how-to-use-files-windows/portal_netuse_connect.png)

4. **Wybierz literę dysku i wprowadź ścieżkę UNC.** 
    
    ![Zrzut ekranu przedstawiający okno dialogowe „Mapowanie dysku sieciowego”](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

5. **Jako nazwy użytkownika użyj nazwy konta magazynu poprzedzonej ciągiem `Azure\`, a jako hasła użyj klucza konta magazynu.**
    
    ![Zrzut ekranu okna dialogowego poświadczeń sieciowych](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

6. **Użyj udziału plików platformy Azure zgodnie z potrzebami**.
    
    ![Udział plików platformy Azure jest teraz zainstalowany](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

7. **Gdy zajdzie potrzeba odinstalowania (lub odłączenia) udziału plików platformy Azure, możesz to zrobić przez kliknięcie prawym przyciskiem myszy wpisu dla udziału w obszarze „Lokalizacje sieciowe” w Eksploratorze plików i wybranie polecenia „Odłącz”**.

## <a name="mount-the-azure-file-share-with-powershell"></a>Instalowanie udziału plików platformy Azure za pomocą programu PowerShell
1. **Użyj następującego polecenia, aby zainstalować udział plików platformy Azure**: Pamiętaj, aby zastąpić ciągi `<storage-account-name>`, `<share-name>`, `<storage-account-key>` i `<desired-drive-letter>` odpowiednimi informacjami.

    ```PowerShell
    $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
    New-PSDrive -Name <desired-drive-letter> -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\<share-name>" -Credential $credential
    ```

2. **Użyj udziału plików platformy Azure zgodnie z potrzebami**.

3. **Gdy skończysz, odinstaluj udział plików platformy Azure przy użyciu następującego polecenia**.

    ```PowerShell
    Remove-PSDrive -Name <desired-drive-letter>
    ```

> [!Note]  
> Możesz użyć parametru `-Persist` dla dysku `New-PSDrive`, aby po zainstalowaniu udział plików platformy Azure był widoczny dla reszty systemu operacyjnego.

## <a name="mount-the-azure-file-share-with-command-prompt"></a>Instalowanie udziału plików platformy Azure za pomocą wiersza polecenia
1. **Użyj następującego polecenia, aby zainstalować udział plików platformy Azure**: Pamiętaj, aby zastąpić ciągi `<storage-account-name>`, `<share-name>`, `<storage-account-key>` i `<desired-drive-letter>` odpowiednimi informacjami.

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. **Użyj udziału plików platformy Azure zgodnie z potrzebami**.

3. **Gdy skończysz, odinstaluj udział plików platformy Azure przy użyciu następującego polecenia**.

    ```
    net use <desired-drive-letter>: /delete
    ```

> [!Note]  
> Poświadczenia dla udziału plików platformy Azure można przechowywać w systemie Windows, dzięki czemu udział może być automatycznie instalowany podczas ponownego uruchamiania komputera. Następujące polecenie spowoduje utrwalenie poświadczeń:
>   ```
>   cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>
>   ```

## <a name="next-steps"></a>Następne kroki
Poniższe linki umożliwiają uzyskanie dodatkowych informacji na temat usługi Azure Files.

* [Często zadawane pytania](../storage-files-faq.md)
* [Rozwiązywanie problemów w systemie Windows](storage-troubleshoot-windows-file-connection-problems.md)      

### <a name="conceptual-articles-and-videos"></a>Artykuły koncepcyjne i filmy
* [Azure Files: a frictionless cloud SMB file system for Windows and Linux (Azure Files: płynnie działający system plików SMB w chmurze dla systemów Windows i Linux)](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [How to use Azure Files with Linux (Jak używać usługi Azure Files z systemem Linux)](../storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-azure-files"></a>Narzędzia dostępne dla usługi Azure Files
* [How to use AzCopy with Microsoft Azure Storage](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (Jak używać narzędzia AzCopy z usługą Microsoft Azure Storage)
* [Używanie interfejsu wiersza polecenia platformy Azure z usługą Azure Storage](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [Troubleshooting Azure Files problems - Windows (Rozwiązywanie problemów z usługą Azure Files — Windows)](storage-troubleshoot-windows-file-connection-problems.md)
* [Troubleshooting Azure Files problems - Linux (Rozwiązywanie problemów z usługą Azure Files — Linux)](storage-troubleshoot-linux-file-connection-problems.md)

### <a name="blog-posts"></a>Wpisy na blogach
* [Azure Files is now generally available (Usługa Azure Files jest teraz ogólnie dostępna)](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Inside Azure Files (Za kulisami usługi Azure Files)](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Introducing Microsoft Azure File Service](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx) (Wprowadzenie do usługi plików platformy Microsoft Azure)
* [Migrowanie danych do usługi Pliki Azure](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/)

### <a name="reference"></a>Dokumentacja
* [Dokumentacja biblioteki klienta usługi Storage dla programu .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Dokumentacja interfejsu API REST usługi plików](http://msdn.microsoft.com/library/azure/dn167006.aspx)
