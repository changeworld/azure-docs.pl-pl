---
title: Utrwalanie plików w Azure Cloud Shell | Microsoft Docs
description: Wskazówki dotyczące sposobu, w jaki Azure Cloud Shell utrwalać pliki.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: damaerte
ms.openlocfilehash: b2823c935d11ae99ab1d87ae708945721820ad8c
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306733"
---
[!INCLUDE [PersistingStorage-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-cloud-shell-storage-works"></a>Jak działa Cloud Shell Storage 
Cloud Shell utrzymuje pliki przy użyciu obu następujących metod: 
* Tworzenie obrazu `$Home` dysku katalogu w celu utrwalenia całej zawartości w katalogu. Obraz dysku jest zapisywany w określonym udziale plików jako `acc_<User>.img` o godzinie `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`i automatycznie synchronizuje zmiany. 
* Zainstalowanie określonego udziału plików jako `clouddrive` `$Home` katalogu w celu bezpośredniej interakcji z udziałem plików. `/Home/<User>/clouddrive`jest mapowany na `fileshare.storage.windows.net/fileshare`.
 
> [!NOTE]
> Wszystkie pliki w `$Home` katalogu, takie jak klucze SSH, są utrwalane w obrazie dysku użytkownika, który jest przechowywany w zainstalowanym udziale plików. Stosuj najlepsze rozwiązania w przypadku utrwalania informacji w `$Home` katalogu i zainstalowanego udziału plików.

## <a name="clouddrive-commands"></a>polecenia CloudDrive

### <a name="use-the-clouddrive-command"></a>`clouddrive` Użyj polecenia
W Cloud Shell można uruchomić polecenie o nazwie `clouddrive`, które pozwala ręcznie zaktualizować udział plików zainstalowany w programie Cloud Shell.
![Uruchamianie polecenia "CloudDrive"](media/persisting-shell-storage/clouddrive-h.png)

### <a name="list-clouddrive"></a>Staw`clouddrive`
Aby wykryć, który udział plików został zainstalowany `clouddrive`jako, `df` Uruchom polecenie. 

Ścieżka do pliku CloudDrive zawiera nazwę konta magazynu i udział plików w adresie URL. Na przykład: `//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```

### <a name="mount-a-new-clouddrive"></a>Zainstaluj nowy CloudDrive

#### <a name="prerequisites-for-manual-mounting"></a>Wymagania wstępne dotyczące ręcznego instalowania
Udział plików skojarzony z Cloud Shell można zaktualizować przy użyciu `clouddrive mount` polecenia.

W przypadku instalowania istniejącego udziału plików konta magazynu muszą znajdować się w regionie wybierania Cloud Shell. Pobierz lokalizację, uruchamiając `env` i `ACC_LOCATION`sprawdzając.

#### <a name="the-clouddrive-mount-command"></a>`clouddrive mount` Polecenie

> [!NOTE]
> Jeśli instalujesz nowy udział plików, dla `$Home` katalogu zostanie utworzony nowy obraz użytkownika. Poprzedni `$Home` obraz jest przechowywany w poprzednim udziale plików.

`clouddrive mount` Uruchom polecenie z następującymi parametrami:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Aby wyświetlić więcej szczegółów, uruchom `clouddrive mount -h`polecenie, jak pokazano poniżej:

![Uruchamianie CloudDrive mount'command](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>Odinstaluj CloudDrive
Można odinstalować udział plików, który jest zainstalowany do Cloud Shell w dowolnym momencie. Ponieważ Cloud Shell wymaga użycia zainstalowanego udziału plików, zostanie wyświetlony monit o utworzenie i zainstalowanie kolejnego udziału plików podczas następnej sesji.

1. Uruchom polecenie `clouddrive unmount`.
2. Potwierdzanie i Potwierdzanie zatwierdzeń.

Udział plików będzie nadal istnieć, chyba że zostanie usunięty ręcznie. Cloud Shell nie będzie już wyszukiwać tego udziału plików na kolejnych sesjach. Aby wyświetlić więcej szczegółów, uruchom `clouddrive unmount -h`polecenie, jak pokazano poniżej:

![Uruchamianie CloudDrive unmount'command](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Chociaż uruchomienie tego polecenia nie spowoduje usunięcia żadnych zasobów, ręczne usunięcie grupy zasobów, konta magazynu lub udziału plików, który jest mapowany do Cloud Shell wymazuje `$Home` obraz dysku katalogu i wszystkie pliki w udziale plików. Tej akcji nie można cofnąć.
## <a name="powershell-specific-commands"></a>Polecenia specyficzne dla programu PowerShell

### <a name="list-clouddrive-azure-file-shares"></a>Wyświetl `clouddrive` listę udziałów plików platformy Azure
Polecenie cmdlet pobiera informacje o udziale plików platformy Azure aktualnie zainstalowane `clouddrive` przez program w Cloud Shell. `Get-CloudDrive` <br>
![Running Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>Odinstaluj`clouddrive`
Można odinstalować udział plików platformy Azure, który jest instalowany w Cloud Shell w dowolnym momencie. Jeśli udział plików platformy Azure został usunięty, zostanie wyświetlony monit o utworzenie i zainstalowanie nowego udziału plików platformy Azure podczas kolejnej sesji.

`Dismount-CloudDrive` Polecenie cmdlet Odinstalowuje udział plików platformy Azure z bieżącego konta magazynu. Odinstalowanie `clouddrive` kończy bieżącą sesję. Użytkownik zostanie poproszony o utworzenie i zainstalowanie nowego udziału plików platformy Azure podczas kolejnej sesji.
![Running Dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

Uwaga: Jeśli musisz zdefiniować funkcję w pliku i wywołać ją z poleceń cmdlet programu PowerShell, należy uwzględnić operator kropki. Na przykład:. .\MyFunctions.ps1

## <a name="next-steps"></a>Następne kroki
[Cloud Shell — Szybki Start](quickstart.md) <br>
[Dowiedz się więcej o usłudze Microsoft Azure Files Storage](https://docs.microsoft.com/azure/storage/storage-introduction) <br>
[Informacje o tagach magazynu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
