---
title: Utrwalanie plików w usłudze Azure Cloud Shell | Dokumenty firmy Microsoft
description: Przewodnik po tym, jak usługa Azure Cloud Shell będzie się powtarzać.
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
ms.date: 02/24/2020
ms.author: damaerte
ms.openlocfilehash: 37005a722d4a1962b4f6e1ddb8bb1c7a1229d28a
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273294"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Utrwalanie plików w usłudze Azure Cloud Shell
Usługa Cloud Shell wykorzystuje usługę Azure File Storage do utrwalania plików w sesjach. Po początkowym uruchomieniu usługa Cloud Shell monituje o skojarzenie nowego lub istniejącego udziału plików w celu utrwalenia plików w sesjach.

> [!NOTE]
> Bash i PowerShell współużytkuje ten sam udział plików. Tylko jeden udział plików może być skojarzony z automatycznym montażem w usłudze Cloud Shell.

> [!NOTE]
> Zapora magazynu platformy Azure nie jest obsługiwana dla kont magazynu powłoki w chmurze.

## <a name="create-new-storage"></a>Tworzenie nowego magazynu

Jeśli używasz podstawowych ustawień i wybierasz tylko subskrypcję, usługa Cloud Shell tworzy trzy zasoby w Twoim imieniu w regionie, który jest najbliżej Ciebie:
* Grupa zasobów: `cloud-shell-storage-<region>`
* Konto magazynu:`cs<uniqueGuid>`
* Udział plików:`cs-<user>-<domain>-com-<uniqueGuid>`

![Ustawienie subskrypcji](media/persisting-shell-storage/basic-storage.png)

Udział plików jest `clouddrive` instalowyny `$Home` jak w katalogu. Jest to jednorazowa akcja, a udział plików jest montowane automatycznie w kolejnych sesjach. 

Udział plików zawiera również obraz o rozmiarze 5 GB, który jest `$Home` tworzony dla Ciebie, który automatycznie utrwala dane w katalogu. Dotyczy to zarówno Bash i PowerShell.

## <a name="use-existing-resources"></a>Korzystanie z istniejących zasobów

Za pomocą opcji zaawansowanej można skojarzyć istniejące zasoby. Wybierając region usługi Cloud Shell, należy wybrać konto magazynu zapasowego znajdujące się w tym samym regionie. Na przykład jeśli przypisany region jest zachodnie stany USA, należy skojarzyć udział plików, który znajduje się w usłudze West US, jak również.

Po wyświetleniu monitu o ustawienie magazynu wybierz pozycję **Pokaż ustawienia zaawansowane,** aby wyświetlić dodatkowe opcje. Filtr opcji zapełniania magazynu dla kont magazynu lokalnie nadmiarowego (LRS), magazynu geograficznie nadmiarowego (GRS) i magazynu strefowego (ZRS). 

> [!NOTE]
> Korzystanie z kont magazynu GRS lub ZRS są zalecane dla dodatkowej odporności dla tworzenia kopii zapasowej udziału plików. Rodzaj redundancji zależy od twoich celów i preferencji cenowych. [Dowiedz się więcej o opcjach replikacji dla kont usługi Azure Storage](../storage/common/storage-redundancy.md).

![Ustawienie Grupa zasobów](media/persisting-shell-storage/advanced-storage.png)

## <a name="securing-storage-access"></a>Zabezpieczanie dostępu do pamięci masowej
Ze względów bezpieczeństwa każdy użytkownik powinien aprowizować własne konto magazynu.  W przypadku kontroli dostępu opartej na rolach (RBAC) użytkownicy muszą mieć dostęp współautora lub wyższy na poziomie konta magazynu.

Usługa Cloud Shell używa udziału plików platformy Azure na koncie magazynu w ramach określonej subskrypcji. Ze względu na uprawnienia dziedziczone użytkownicy z wystarczającymi prawami dostępu do subskrypcji będą mogli uzyskać dostęp do wszystkich kont magazynu i udziałów plików zawartych w subskrypcji.

Użytkownicy powinni zablokować dostęp do swoich plików, ustawiając uprawnienia na koncie magazynu lub na poziomie subskrypcji.

## <a name="supported-storage-regions"></a>Obsługiwane regiony magazynu
Aby znaleźć bieżący region, można uruchomić `env` `ACC_LOCATION`w bash i zlokalizować zmienną lub z programu PowerShell run `$env:ACC_LOCATION`. Udziały plików otrzymują obraz o rozmiarze 5 GB utworzony w celu utrwalenia `$Home` katalogu.

Maszyny Cloud Shell istnieją w następujących regionach:

|Obszar|Region|
|---|---|
|Ameryki|Wschodnie stany USA, południowo-środkowe stany USA, zachodnie stany USA|
|Europa|Europa Północna, Europa Zachodnia|
|Azja i Pacyfik|Indie Środkowe, Azja Południowo-Wschodnia|

Klienci powinni wybrać region podstawowy, chyba że mają wymóg, aby ich dane w spoczynku były przechowywane w określonym regionie. Jeśli mają takie wymagania, należy użyć dodatkowego regionu magazynowania.

### <a name="secondary-storage-regions"></a>Dodatkowe regiony magazynowania
Jeśli używany jest pomocniczy region magazynu, skojarzone konto magazynu platformy Azure znajduje się w innym regionie, jak komputer usługi Cloud Shell, do którego są montowane. Na przykład Jane może ustawić swoje konto magazynu na Kanada Wschód, region pomocniczy, ale maszyna, na którą jest zamontowana, nadal znajduje się w regionie podstawowym. Jej dane w stanie spoczynku znajdują się w Kanadzie, ale są przetwarzane w Stanach Zjednoczonych.

> [!NOTE]
> Jeśli używany jest region pomocniczy, dostęp do plików i czas uruchamiania usługi Cloud Shell mogą być wolniejsze.

Użytkownik może `(Get-CloudDrive | Get-AzStorageAccount).Location` uruchomić w programie PowerShell, aby zobaczyć lokalizację swojego udziału plików.

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Ograniczanie tworzenia zasobów za pomocą zasad zasobów platformy Azure
Konta magazynu utworzone w usłudze Cloud `ms-resource-usage:azure-cloud-shell`Shell są oznaczone tagiem . Jeśli chcesz uniemożliwić użytkownikom tworzenie kont magazynu w usłudze Cloud Shell, utwórz [zasady zasobów platformy Azure dla tagów,](../azure-policy/json-samples.md) które są wyzwalane przez ten konkretny tag.

## <a name="how-cloud-shell-storage-works"></a>Jak działa pamięć masowa Cloud Shell 
Usługa Cloud Shell utrwala pliki za pomocą obu następujących metod: 
* Tworzenie obrazu dysku katalogu, `$Home` aby utrwalić całą zawartość w katalogu. Obraz dysku jest zapisywany w `acc_<User>.img` określonym `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`udziale plików zgodnie z programem i automatycznie synchronizuje zmiany. 
* Montaż określonego udziału `clouddrive` plików `$Home` w katalogu w celu bezpośredniej interakcji z udostępnianiem plików. `/Home/<User>/clouddrive`jest mapowany `fileshare.storage.windows.net/fileshare`na .
 
> [!NOTE]
> Wszystkie pliki `$Home` w katalogu, takie jak klucze SSH, są zachowywane w obrazie dysku użytkownika, który jest przechowywany w zainstalowanym udziale plików. Zastosuj najlepsze rozwiązania podczas utrwalania `$Home` informacji w katalogu i zainstalowanego udziału plików.

## <a name="clouddrive-commands"></a>polecenia clouddrive

### <a name="use-the-clouddrive-command"></a>Użyj `clouddrive` polecenia
W usłudze Cloud Shell można `clouddrive`uruchomić polecenie o nazwie , które umożliwia ręczne aktualizowanie udziału plików, który jest zamontowany w usłudze Cloud Shell.

![Uruchamianie polecenia "clouddrive"](media/persisting-shell-storage/clouddrive-h.png)

### <a name="list-clouddrive"></a>Listy`clouddrive`
Aby dowiedzieć się, `clouddrive`który udział `df` pliku jest zamontowany jako , uruchom polecenie. 

Ścieżka pliku do clouddrive pokazuje nazwę konta magazynu i udział plików w adresie URL. Na przykład: `//storageaccountname.file.core.windows.net/filesharename`

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

### <a name="mount-a-new-clouddrive"></a>Montuj nowy clouddrive

#### <a name="prerequisites-for-manual-mounting"></a>Wymagania wstępne dotyczące montażu ręcznego
Możesz zaktualizować udział plików skojarzony z usługą Cloud `clouddrive mount` Shell za pomocą polecenia.

Jeśli zainstalujesz istniejący udział plików, konta magazynu muszą znajdować się w wybranym regionie cloud shell. Pobierz lokalizację, uruchamiając `env` i `ACC_LOCATION`sprawdzając .

#### <a name="the-clouddrive-mount-command"></a>Polecenie `clouddrive mount`

> [!NOTE]
> Jeśli montujesz nowy udział plików, dla katalogu `$Home` zostanie utworzony nowy obraz użytkownika. Poprzedni `$Home` obraz jest przechowywany w poprzednim udziale plików.

Uruchom `clouddrive mount` polecenie z następującymi parametrami:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Aby wyświetlić więcej `clouddrive mount -h`szczegółów, uruchom , jak pokazano tutaj:

![Uruchamianie polecenia 'clouddrive mount'](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>Odinstalowywzdrowuj chmurę
W dowolnym momencie można odinstalować udział plików zainstalowany w usłudze Cloud Shell. Ponieważ usługa Cloud Shell wymaga użycia zainstalowanego udziału plików, podczas następnej sesji zostanie wyświetlony monit o utworzenie i zainstalowanie innego udziału plików.

1. Uruchom polecenie `clouddrive unmount`.
2. Potwierdzanie i potwierdzanie monitów.

Udział plików będzie nadal istnieć, chyba że usuniesz go ręcznie. Usługa Cloud Shell nie będzie już wyszukiwać tego udziału plików w kolejnych sesjach. Aby wyświetlić więcej `clouddrive unmount -h`szczegółów, uruchom , jak pokazano tutaj:

![Uruchamianie polecenia 'clouddrive unmount'](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Chociaż uruchomienie tego polecenia nie spowoduje usunięcia żadnych zasobów, ręczne usunięcie grupy zasobów, konta magazynu lub udziału `$Home` plików mapowanych na usługę Cloud Shell spowoduje wymazanie obrazu dysku katalogu i wszelkich plików w udziale plików. Tej akcji nie można cofnąć.
## <a name="powershell-specific-commands"></a>Polecenia specyficzne dla programu PowerShell

### <a name="list-clouddrive-azure-file-shares"></a>Lista `clouddrive` udziałów plików platformy Azure
Polecenie `Get-CloudDrive` cmdlet pobiera informacje o współdziale plików `clouddrive` platformy Azure aktualnie zainstalowane przez powłokę w chmurze. <br>
![Uruchamianie usługi Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>Odmontować`clouddrive`
W dowolnym momencie można odinstalować udział plików platformy Azure, który jest instalowany w usłudze Cloud Shell. Jeśli udział plików platformy Azure został usunięty, zostanie wyświetlony monit o utworzenie i zainstalowanie nowego udziału plików platformy Azure w następnej sesji.

Polecenie `Dismount-CloudDrive` cmdlet odinstalowuje udział plików platformy Azure z bieżącego konta magazynu. Odinstalowanie `clouddrive` kończy bieżącą sesję. Użytkownik zostanie poproszony o utworzenie i zainstalowanie nowego udziału plików platformy Azure podczas następnej sesji.
![Uruchamianie usługi Odinstalowywszał clouddrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

Uwaga: Jeśli chcesz zdefiniować funkcję w pliku i wywołać ją z poleceń cmdlet programu PowerShell, operator kropki musi zostać uwzględniony. Na przykład: . .\MyFunctions.ps1

## <a name="next-steps"></a>Następne kroki
[Szybki start powłoki chmury](quickstart.md) <br>
[Dowiedz się więcej o magazynie plików platformy Microsoft Azure](../storage/files/storage-files-introduction.md) <br>
[Dowiedz się więcej o tagach magazynu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
