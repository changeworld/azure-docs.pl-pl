---
title: Bash w powłoce chmury Azure są przechowywane pliki | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące sposobu Bash w powłoce chmury Azure będzie się powtarzał plików.
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
ms.date: 01/30/2018
ms.author: juluk
ms.openlocfilehash: d8188634846a7ce75b5294cb3012069d9eafafc1
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2018
ms.locfileid: "28919546"
---
[!INCLUDE [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-bash-in-cloud-shell-storage-works"></a>Jak działa Bash w magazynie w chmurze powłoki 
Bash w powłoce chmurze będzie się powtarzał plików za pomocą obu z następujących metod: 
* Tworzenie obrazu dysku z Twojej `$Home` katalogu, aby zachować całą zawartość w katalogu. Obraz dysku jest zapisywany w udziale określony plik jako `acc_<User>.img` w `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`, i automatycznie synchronizuje zmiany. 
* Instalowanie udziału plików określony jako `clouddrive` w Twojej `$Home` katalogu interakcji bezpośredniego udziału plików. `/Home/<User>/clouddrive`jest mapowany na `fileshare.storage.windows.net/fileshare`.
 
> [!NOTE]
> Wszystkie pliki w Twojej `$Home` katalogu, takiego jak kluczy SSH są zachowywane w obrazie dysku użytkownika, który jest przechowywany w udziale zainstalowanego pliku. Zastosowanie najlepszych rozwiązań podczas utrwalania informacji w sieci `$Home` katalogu i udziału zainstalowanego pliku.

## <a name="use-the-clouddrive-command"></a>Użyj `clouddrive` polecenia
Za pomocą Bash w chmurze powłoki, można uruchomić polecenie o nazwie `clouddrive`, co umożliwia ręcznie zaktualizować udział plików, który jest zamontowany powłoki chmury.
![Uruchomienie polecenia "clouddrive"](media/persisting-shell-storage/clouddrive-h.png)

## <a name="mount-a-new-clouddrive"></a>Zainstaluj nowy clouddrive

### <a name="prerequisites-for-manual-mounting"></a>Wymagania wstępne dotyczące ręcznego instalowania
Można aktualizować udziału plików, który jest skojarzony z powłoki chmury przy użyciu `clouddrive mount` polecenia.

W przypadku instalowania istniejącego udziału pliku, muszą być kont magazynu:
* Magazyn lokalnie nadmiarowy lub magazyn geograficznie nadmiarowy do obsługi udziałów plików.
* Znajduje się w danym regionie przypisane. Podczas pracy dołączania są przypisane do regionu ma na liście Nazwa grupy zasobów `cloud-shell-storage-<region>`.

### <a name="the-clouddrive-mount-command"></a>Polecenie instalacji clouddrive

> [!NOTE]
> Jeśli w przypadku instalowania nowego udziału plików, nowy obraz użytkownika jest tworzona dla Twojego `$Home` katalogu. Poprzedniego `$Home` obrazu jest przechowywany w poprzednim udziału plików.

Uruchom `clouddrive mount` polecenia z następującymi parametrami:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Aby wyświetlić więcej szczegółów, uruchom `clouddrive mount -h`, jak pokazano poniżej:

![Uruchomiona "clouddrive mount'command](media/persisting-shell-storage/mount-h.png)

## <a name="unmount-clouddrive"></a>Odinstaluj clouddrive
Można odinstalować udział pliku, który jest zamontowany do chmury powłoki w dowolnym momencie. Ponieważ powłoki chmury wymaga udział zainstalowanego pliku do użycia, wyświetli się monit o utworzy i zainstaluje inny udział plików na następnej sesji.

1. Uruchom polecenie `clouddrive unmount`.
2. Potwierdzić oraz potwierdzić monity.

Udziału plików będzie nadal istnieje, jeśli nie można usunąć ręcznie. Chmura powłoki wyszuka już ten udział plików w kolejnych sesjach. Aby wyświetlić więcej szczegółów, uruchom `clouddrive unmount -h`, jak pokazano poniżej:

![Uruchomiona "clouddrive unmount'command](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Mimo że wykonanie tego polecenia nie usunie żadnych zasobów, ręczne usunięcie grupy zasobów, konta magazynu lub udziału plików, który jest zamapowany na chmurze powłoki na partycje powoduje usunięcie z `$Home` obrazu dysku katalogu oraz wszystkich plików w udziale plików. Nie można cofnąć tej akcji.

## <a name="list-clouddrive"></a>Lista`clouddrive`
Aby dowiedzieć się, które udziału plików jest zainstalowany jako `clouddrive`Uruchom `df` polecenia. 

Ścieżka pliku do clouddrive pokazuje Twojej nazwy konta magazynu i udziału pliku w adresie URL. Na przykład: `//storageaccountname.file.core.windows.net/filesharename`

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

[!INCLUDE [features-introblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>Kolejne kroki
[Bash w chmurze powłoki — Szybki Start](quickstart.md) <br>
[Informacje na temat magazynowania pliki programu Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[Więcej informacji na temat tagów magazynu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
