---
title: Utrwalanie plików w programie Bash w usłudze Azure Cloud Shell | Dokumentacja firmy Microsoft
description: Przewodnik dotyczący jak powłoka Bash w usłudze Azure Cloud Shell będzie się powtarzał plików.
services: azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: juluk
ms.openlocfilehash: 9a22b14df18e10342bb2a872b82b94ab4ea62d0a
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2018
ms.locfileid: "37859871"
---
[!INCLUDE [PersistingStorage-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-cloud-shell-storage-works"></a>Jak działa magazyn usługi Cloud Shell 
Usługa cloud Shell będzie się powtarzał plików za pomocą obu z następujących metod: 
* Tworzenie obrazu dysku z Twojej `$Home` katalogu, aby zachować całą zawartość w katalogu. Obraz dysku są zapisywane w udziale plików określony jako `acc_<User>.img` na `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`, i automatycznie synchronizuje zmiany. 
* Instalowanie udziału plików w określonym jako `clouddrive` w swojej `$Home` katalogu do interakcji z bezpośredniego udziału plików. `/Home/<User>/clouddrive` jest mapowany na `fileshare.storage.windows.net/fileshare`.
 
> [!NOTE]
> Wszystkie pliki w Twojej `$Home` katalogu, takie jak klucze SSH są zachowywane w obrazie dysku użytkownika, który jest przechowywany w udziale zainstalowanego pliku. Najlepsze rozwiązania są stosowane, gdy utrwalić informacje zawarte w Twojej `$Home` katalogu i zainstalowanym udziałem sieciowym.

## <a name="bash-specific-commands"></a>Polecenia specyficzne dla powłoki bash

### <a name="use-the-clouddrive-command"></a>Użyj `clouddrive` polecenia
Przy użyciu programu Bash w usłudze Cloud Shell, można uruchomić polecenie o nazwie `clouddrive`, co pozwala ręcznie zaktualizować udziału plików który jest zainstalowany, do usługi Cloud Shell.
![Uruchamiając polecenie "clouddrive"](media/persisting-shell-storage/clouddrive-h.png)

### <a name="mount-a-new-clouddrive"></a>Zainstaluj nowy clouddrive

#### <a name="prerequisites-for-manual-mounting"></a>Wymagania wstępne dotyczące ręcznego instalowania
Można zaktualizować udziału plików który jest skojarzony z usługi Cloud Shell przy użyciu `clouddrive mount` polecenia.

W przypadku instalowania istniejący udział plików, kont magazynu muszą być:
* Magazyn lokalnie nadmiarowy lub magazyn geograficznie nadmiarowy do obsługi udziałów plików.
* Znajduje się w Twoim regionie przypisane. Kiedy dołączania, region, są przypisane do znajduje się w nazwę grupy zasobów `cloud-shell-storage-<region>`.

#### <a name="the-clouddrive-mount-command"></a>`clouddrive mount` Polecenia

> [!NOTE]
> Jeśli w przypadku instalowania nowego udziału plików, nowy obraz użytkownika zostanie utworzone z `$Home` katalogu. Twoje poprzednie `$Home` obraz jest przechowywany w poprzednim udziału plików.

Uruchom `clouddrive mount` polecenia z następującymi parametrami:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Aby wyświetlić więcej szczegółów, uruchom `clouddrive mount -h`, jak pokazano poniżej:

![Uruchamianie "clouddrive mount'command](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>Odinstaluj clouddrive
Można odinstalować udziału plików, który jest zainstalowany do usługi Cloud Shell w dowolnym momencie. Ponieważ usługa Cloud Shell wymaga udziału zainstalowanych plików do użycia, użytkownik zostanie wyświetlony monit tworzenie i instalowanie innego udziału plików w następnej sesji.

1. Uruchom polecenie `clouddrive unmount`.
2. Użytkownik potwierdza i monity o potwierdzenie.

Udział plików będzie nadal istniał, chyba że usuniesz je ręcznie. Usługa cloud Shell wyszuka już ten udział plików na udział podczas kolejnych sesji. Aby wyświetlić więcej szczegółów, uruchom `clouddrive unmount -h`, jak pokazano poniżej:

![Uruchamianie "clouddrive unmount'command](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Mimo że uruchomieniu tego polecenia nie spowoduje usunięcia wszelkich zasobów, ręczne usunięcie grupy zasobów, konto magazynu lub udziału plików, który jest mapowany do usługi Cloud Shell na partycje powoduje usunięcie Twojego `$Home` obrazu dysku w katalogu i wszystkich plików w udziale plików. Tej akcji nie można cofnąć.

### <a name="list-clouddrive"></a>Lista `clouddrive`
Aby dowiedzieć się, udział pliku, który jest zainstalowany jako `clouddrive`Uruchom `df` polecenia. 

Ścieżka pliku do clouddrive pokazuje, że Twoja nazwa konta magazynu i udziału plików w adresie URL. Na przykład: `//storageaccountname.file.core.windows.net/filesharename`

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
## <a name="powershell-specific-commands"></a>Polecenia specyficzne dla programu PowerShell

### <a name="list-clouddrive-azure-file-shares"></a>Lista `clouddrive` udziałów plików platformy Azure
`Get-CloudDrive` Polecenie cmdlet pobiera informacje udziału plików platformy Azure, które są aktualnie instalowane przez `clouddrive` w usłudze Cloud Shell. <br>
![Running Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>Odinstaluj `clouddrive`
Można odinstalować udziału plików platformy Azure, który jest zainstalowany do usługi Cloud Shell w dowolnym momencie. Usunięcie udziału plików platformy Azure użytkownik jest monitowany do tworzenia i zainstaluj nowy udział plików platformy Azure w następnej sesji.

`Dismount-CloudDrive` Polecenie cmdlet umożliwia odinstalowanie udziału plików platformy Azure z bieżącego konta magazynu. Odinstalowywanie `clouddrive` kończy bieżącą sesję. Użytkownik jest monitowany do tworzenia i zainstaluj nowy udział plików platformy Azure podczas następnej sesji.
![Running Dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>Kolejne kroki
[Powłoka bash w przewodniku Szybki Start Cloud Shell](quickstart.md) <br>
[Program PowerShell w przewodniku Szybki Start Cloud Shell](quickstart-powershell.md) <br>
[Informacje na temat magazynowania plików pakietu Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[Dowiedz się więcej na temat tagów magazynu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
