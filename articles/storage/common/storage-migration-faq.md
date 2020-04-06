---
title: Często zadawane pytania dotyczące migracji usługi Azure Storage | Dokumenty firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące migracji usługi Azure Storage
services: storage
author: genlin
manager: dcscontentpm
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.subservice: common
ms.openlocfilehash: 39f1137638f9cd4926b712bdd18e681d90adcdc4
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668546"
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Często zadawane pytania dotyczące migracji usługi Azure Storage

Ten artykuł zawiera odpowiedzi na typowe pytania dotyczące migracji usługi Azure Storage.

## <a name="copy-upload-or-download"></a>Kopiowanie, przesyłanie lub pobieranie

**Jak utworzyć skrypt do kopiowania plików z jednego kontenera do drugiego?**

Aby skopiować pliki między kontenerami, można użyć AzCopy. Zobacz poniższy przykład:

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

AzCopy używa [interfejsu API kopiowania obiektów blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) do kopiowania każdego pliku w kontenerze.  

Do uruchomienia azcopy można użyć dowolnej maszyny wirtualnej lub maszyny lokalnej, która ma dostęp do Internetu. Można również użyć harmonogramu usługi Azure Batch, aby to zrobić automatycznie, ale jest to bardziej skomplikowane.  

Skrypt automatyzacji jest przeznaczony do wdrażania usługi Azure Resource Manager zamiast manipulowania zawartością magazynu. Aby uzyskać więcej informacji, zobacz [Wdrażanie zasobów za pomocą szablonów Usługi Resource Manager i programu Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md).

**Czy istnieje opłata za kopiowanie danych między dwoma udziałami plików na tym samym koncie magazynu w tym samym regionie?**

Nie. Nie ma żadnych opłat za ten proces.

**Jak pobrać 1-2 TB danych z witryny Azure portal?**

Użyj AzCopy, aby pobrać dane. Aby uzyskać więcej informacji, zobacz [Transfer danych z AzCopy w systemie Windows](storage-use-azcopy.md) i Transfer danych z [AzCopy na Linuksie](storage-use-azcopy-linux.md).

**Jak mogę pobrać dysk VHD na komputer lokalny, inny niż za pomocą opcji pobierania w portalu?**

Za pomocą [Eksploratora pamięci masowej](https://azure.microsoft.com/features/storage-explorer/) można pobrać dysk VHD.

**Jak pobrać dane na komputer z systemem Linux z konta magazynu platformy Azure lub przesłać dane z komputera z systemem Linux?**

Można użyć interfejsu wiersza polecenia platformy Azure.

- Pobierz pojedynczy obiekt blob:

      azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

- Prześlij pojedynczy obiekt blob:

      azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**Jak przeprowadzić migrację obiektów Blob z jednego konta magazynu do innego?**

 Można to zrobić za pomocą naszego [skryptu migracji obiektów Blob](../scripts/storage-common-transfer-between-storage-accounts.md).
 
## <a name="migration-or-backup"></a>Migracja lub tworzenie kopii zapasowych

**Jak przenieść dane z jednego kontenera magazynu do drugiego?**

Wykonaj następujące kroki:

1.  Utwórz kontener (folder) w docelowym obiekcie blob.

2.  Użyj [AzCopy](https://azure.microsoft.com/blog/azcopy-5-1-release/) skopiować zawartość z oryginalnego kontenera obiektów blob do innego kontenera obiektu blob.

**Jak utworzyć skrypt programu PowerShell, aby przenieść dane z jednego udziału plików platformy Azure do innego w usłudze Azure Storage?**

Użyj AzCopy, aby przenieść dane z jednego udziału plików platformy Azure do innego w usłudze Azure Storage. Aby uzyskać więcej informacji, zobacz [Transfer danych z AzCopy w systemie Windows](storage-use-azcopy.md) i Transfer danych z [AzCopy na Linuksie](storage-use-azcopy-linux.md).

**Jak przekazać duże pliki csv do usługi Azure Storage?**

Przekazywanie dużych plików csv do usługi Azure Storage za pomocą programu AzCopy. Aby uzyskać więcej informacji, zobacz [Transfer danych z AzCopy w systemie Windows](storage-use-azcopy.md) i Transfer danych z [AzCopy na Linuksie](storage-use-azcopy-linux.md).

**Muszę codziennie przenosić dzienniki z dysku D na moje konto magazynu platformy Azure. Jak to zautomatyzować?**

Można użyć AzCopy i utworzyć zadanie w Harmonogramie zadań. Przekazywanie plików do konta magazynu platformy Azure przy użyciu skryptu wsadowego AzCopy. Aby uzyskać więcej informacji, zobacz [Jak skonfigurować i uruchomić zadania uruchamiania usługi w chmurze](../../cloud-services/cloud-services-startup-tasks.md).

**Jak przenieść konto magazynu między subskrypcjami?**

Użyj AzCopy, aby przenieść konto magazynu między subskrypcjami. Aby uzyskać więcej informacji, zobacz [Transfer danych z AzCopy w systemie Windows](storage-use-azcopy.md) i Transfer danych z [AzCopy na Linuksie](storage-use-azcopy-linux.md).

**Jak przenieść około 10 TB danych do magazynu w innym regionie?**

Użyj AzCopy, aby przenieść dane. Aby uzyskać więcej informacji, zobacz [Transfer danych z AzCopy w systemie Windows](storage-use-azcopy.md) i Transfer danych z [AzCopy na Linuksie](storage-use-azcopy-linux.md).

**Jak skopiować dane z lokalnego do usługi Azure Storage?**

Użyj AzCopy, aby skopiować dane. Aby uzyskać więcej informacji, zobacz [Transfer danych z AzCopy w systemie Windows](storage-use-azcopy.md) i Transfer danych z [AzCopy na Linuksie](storage-use-azcopy-linux.md).

**Jak przenieść dane z lokalnego do usługi Azure Files?**

Użyj AzCopy, aby przenieść dane. Aby uzyskać więcej informacji, zobacz [Transfer danych z AzCopy w systemie Windows](storage-use-azcopy.md) i Transfer danych z [AzCopy na Linuksie](storage-use-azcopy-linux.md).

**Jak przenieść dyski zarządzane na inne konto magazynu?**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Wykonaj następujące kroki:

1.  Zatrzymaj maszynę wirtualną, do których jest dołączony dysk zarządzany.

2.  Skopiuj dysk zarządzany VHD z jednego obszaru do drugiego, uruchamiając następujący skrypt programu Azure PowerShell:

    ```
    Connect-AzAccount

    Select-AzSubscription -SubscriptionId <ID>

    $sas = Grant-AzDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  Utwórz dysk zarządzany przy użyciu pliku VHD w innym regionie, do którego skopiowano dysk VHD. Aby to zrobić, uruchom następujący skrypt programu Azure PowerShell:  

    ```
    $resourceGroupName = 'MDDemo'

    $diskName = 'contoso\_os\_disk1'

    $vhdUri = 'https://contoso.storageaccou.com.vhd

    $storageId = '/subscriptions/<ID>/resourceGroups/<RG name>/providers/Microsoft.Storage/storageAccounts/contosostorageaccount1'

    $location = 'westus'

    $storageType = 'StandardLRS'

    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Import -SourceUri $vhdUri -StorageAccountId $storageId -DiskSizeGB 128

    $osDisk = New-AzDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
    ```

Aby uzyskać więcej informacji na temat wdrażania maszyny wirtualnej z dysku zarządzanego, zobacz [CreateVmFromManagedOsDisk.ps1](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/blob/master/CreateVmFromManagedOsDisk.ps1).

**Jak przenieść lub pobrać dane z konta magazynu?**

Użyj AzCopy, aby pobrać dane. Aby uzyskać więcej informacji, zobacz [Transfer danych z AzCopy w systemie Windows](storage-use-azcopy.md) i Transfer danych z [AzCopy na Linuksie](storage-use-azcopy-linux.md).

**Jak przenieść konto magazynu w wersji premium na standardowe konto magazynu?**

Wykonaj następujące kroki:

1.  Utwórz standardowe konto magazynu. (Lub użyj istniejącego standardowego konta magazynu w ramach subskrypcji).

2.  Pobierz AzCopy. Uruchom jedno z następujących poleceń AzCopy.

    Aby skopiować całe dyski na koncie magazynu:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S

    Aby skopiować tylko jeden dysk, podaj nazwę dysku w **wzorzec:**

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

Operacja może potrwać kilka godzin.

Aby upewnić się, że transfer zakończył się pomyślnie, sprawdź kontener konta magazynu docelowego w witrynie Azure portal. Po skopiowaniu dysków do standardowego konta magazynu można dołączyć je do maszyny wirtualnej jako istniejącego dysku. Aby uzyskać więcej informacji, zobacz [Jak dołączyć zarządzany dysk danych do maszyny wirtualnej systemu Windows w witrynie Azure portal](../../virtual-machines/windows/attach-managed-disk-portal.md).  

**Jak przenieść konto magazynu klasycznego na konto magazynu usługi Azure Resource Manager?**

Można użyć polecenia cmdlet **Move-AzureStorageAccount.** To polecenie cmdlet ma wiele kroków (sprawdzanie poprawności, przygotowanie, zatwierdzanie). Możesz sprawdzić poprawność przeniesienia przed jego dokonaniem.

Jeśli masz maszyny wirtualne, należy podjąć dodatkowe kroki przed migracją danych konta magazynu. Aby uzyskać więcej informacji, zobacz [Migrowanie zasobów IaaS z klasycznego do usługi Azure Resource Manager przy użyciu programu Azure PowerShell](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md).

**Jak wykonać pełną akces do innego konta magazynu?**

Nie ma opcji bezpośredniego utworzenia kopii zapasowej całego konta magazynu. Ale można ręcznie przenieść kontenera na tym koncie magazynu do innego konta przy użyciu AzCopy lub Eksploratora magazynu. Poniższe kroki pokazują, jak przenieść kontener za pomocą programu AzCopy:  

1.  Zainstaluj narzędzie wiersza polecenia [AzCopy.](storage-use-azcopy.md) To narzędzie pomaga przenosić plik VHD między kontami magazynu.

2.  Po zainstalowaniu programu AzCopy w systemie Windows przy użyciu instalatora otwórz okno wiersza polecenia, a następnie przejdź do folderu instalacyjnego AzCopy na komputerze. Domyślnie AzCopy jest instalowany w **%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy** lub **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**.

3.  Uruchom następujące polecenie, aby przenieść kontener. Należy zastąpić tekst wartościami rzeczywistymi.   

            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

    - `/Source`: Podaj identyfikator URI dla konta magazynu źródłowego (do kontenera).  
    - `/Dest`: Podaj identyfikator URI dla docelowego konta magazynu (do kontenera).  
    - `/SourceKey`: Podaj klucz podstawowy dla konta magazynu źródłowego. Ten klucz można skopiować z witryny Azure portal, wybierając konto magazynu.  
    - `/DestKey`: Podaj klucz podstawowy dla docelowego konta magazynu. Ten klucz można skopiować z portalu, wybierając konto magazynu.

Po uruchomieniu tego polecenia pliki kontenera są przenoszone na docelowe konto magazynu.

> [!NOTE]
> AzCopy CLI nie działa razem z **przełącznikiem wzorzec** podczas kopiowania z jednego obiektu blob platformy Azure do innego.
>
> Można bezpośrednio skopiować i edytować polecenie AzCopy i sprawdzić krzyżowo, aby upewnić się, że **wzorzec** pasuje do źródła. Upewnij się również, że **/S** symbole wieloznaczne są w mocy. Aby uzyskać więcej informacji, zobacz [parametry AzCopy](storage-use-azcopy.md).

**Jak wykonać zapasowy magazynu plików platformy Azure?**

Nie ma rozwiązania do tworzenia kopii zapasowych. Jednak usługa Azure Files obsługuje również kopię asynchronizacjową. Tak, można kopiować pliki:

- Od udziału do innego udziału na koncie magazynu lub do innego konta magazynu.

- Z udziału do kontenera obiektu blob w ramach konta magazynu lub do innego konta magazynu.

Aby uzyskać więcej informacji, zobacz [Przenoszenie danych za pomocą programu AzCopy w systemie Windows](storage-use-azcopy.md).
## <a name="configuration"></a>Konfigurowanie

**Jak zmienić lokalizację pomocniczą na region Europy dla konta magazynu?**

Podczas tworzenia konta magazynu należy wybrać region podstawowy dla konta. Wybór regionu pomocniczego jest oparty na regionie podstawowym i nie można go zmienić. Aby uzyskać więcej informacji, zobacz [Magazyn geograficznie nadmiarowy (GRS): Replikacja międzyregionalna dla usługi Azure Storage](storage-redundancy.md).

**Gdzie można uzyskać więcej informacji na temat szyfrowania usługi Azure Storage Service (SSE)?**  

Zobacz następujące artykuły:

-  [Przewodnik po zabezpieczeniach usługi Azure Storage](../blobs/security-recommendations.md)

-  [Szyfrowanie usługi Usługi Azure Storage dla danych w spoczynku](storage-service-encryption.md)

**Jak zaszyfrować dane na koncie magazynu?**

Po włączeniu szyfrowania na koncie magazynu istniejące dane nie są szyfrowane. Aby zaszyfrować istniejące dane, należy przekazać je ponownie na konto magazynu.

Użyj AzCopy, aby skopiować dane do innego konta magazynu, a następnie przenieść dane z powrotem. Można również użyć [szyfrowania w spoczynku](storage-service-encryption.md).

**Czy istnieją wymagania wstępne dotyczące zmiany replikacji konta magazynu z magazynu geograficznie nadmiarowego na magazyn lokalnie nadmiarowy?**

Nie.

**Jak przekonwertować na usługę Azure Premium Storage dla udziału plików?**

Magazyn w wersji Premium nie jest dozwolony w udziale plików platformy Azure.

**Jak uaktualnić standardowe konto magazynu do konta magazynu w wersji Premium? Jak przejść z konta magazynu w wersji premium na standardowe konto magazynu?**

Należy utworzyć konto magazynu docelowego, skopiować dane z konta źródłowego do konta docelowego, a następnie usunąć konto źródłowe. Do skopiowania danych można użyć narzędzia, takiego jak AzCopy.

Jeśli masz maszyny wirtualne, należy podjąć dodatkowe kroki przed migracją danych konta magazynu. Aby uzyskać więcej informacji, zobacz [Migracja do usługi Azure Premium Storage (dyski niezarządzane).](storage-migration-to-premium-storage.md)

**Jak przyznać innym osobom dostęp do moich zasobów pamięci masowej?**

Aby zapewnić innym osobom dostęp do zasobów magazynu:

-   Użyj tokenu sygnatury dostępu współdzielonego (SAS), aby zapewnić dostęp do zasobu.

-   Podaj użytkownikowi klucz podstawowy lub pomocniczy dla konta magazynu. Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami dostępu do konta magazynu](storage-account-keys-manage.md).

-   Zmień zasady dostępu, aby zezwolić na dostęp anonimowy. Aby uzyskać więcej informacji, zobacz [Udzielanie użytkownikom anonimowym uprawnień do kontenerów i obiektów blob.](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs)

**Gdzie jest zainstalowany AzCopy?**

-   Jeśli uzyskujesz dostęp do programu AzCopy z wiersza polecenia usługi Microsoft Azure Storage, wpisz **AzCopy**. Wiersz polecenia jest zainstalowany obok AzCopy.

-   Jeśli zainstalowano wersję 32-bitową, znajduje się tutaj: **\\%ProgramFiles(x86)%\\\\Microsoft SDKs Azure AzCopy**.

-   Jeśli zainstalowano wersję 64-bitową, znajduje się tutaj: **\\%ProgramFiles%\\\\Microsoft SDKs Azure AzCopy**.

**Jak używać domeny niestandardowej HTTPS z kontem magazynu? Na przykład, jak sprawić,\/aby "https: /mystorageaccountname.blob.core.windows.net/images/image.gif" były\/wyświetlane jako "https: /www.contoso.com/images/image.gif"?**

TLS/SSL nie jest obecnie obsługiwany na kontach magazynu z domenami niestandardowymi.
Można jednak używać domen niestandardowych innych niż HTTPS. Aby uzyskać więcej informacji, zobacz [Konfigurowanie niestandardowej nazwy domeny dla punktu końcowego magazynu obiektów Blob](../blobs/storage-custom-domain-name.md).

## <a name="access-to-storage"></a>Dostęp do pamięci masowej

**Jak zamapować folder kontenera na maszynie wirtualnej?**

Użyj udziału plików platformy Azure.

**Jak uzyskać dostęp do nadmiarowego magazynu usługi Azure Files?**

Dostęp do odczytu magazynu geograficznego nadmiarowego jest wymagany do uzyskania dostępu do magazynu nadmiarowego. Jednak usługa Azure Files obsługuje tylko lokalnie nadmiarowy magazyn i standardowy magazyn geograficznie nadmiarowy, który nie zezwala na dostęp tylko do odczytu.

**W przypadku konta magazynu replikowanego (takiego jak magazyn strefowy nadmiarowy, magazyn geograficznie nadmiarowy lub magazyn geograficzny dostępu do odczytu) mogę uzyskać dostęp do danych przechowywanych w regionie pomocniczym?**

-   Jeśli używasz magazynu strefowo nadmiarowego lub magazynu geograficznie nadmiarowego, nie możesz uzyskać dostępu do danych z regionu pomocniczego, chyba że zainicjujesz pracę awaryjną w tym regionie. Aby uzyskać więcej informacji na temat procesu pracy awaryjnej, zobacz [Odzyskiwanie po awarii i przetwarzanie awaryjne konta magazynu (w wersji zapoznawczej) w usłudze Azure Storage](storage-disaster-recovery-guidance.md).

-   Jeśli używasz magazynu geograficznego dostępu do odczytu, możesz w dowolnym momencie uzyskać dostęp do danych z regionu pomocniczego. Użyj jednej z następujących metod:  

    - **AzCopy**: Dołącz **-secondary** do nazwy konta magazynu w adresie URL, aby uzyskać dostęp do pomocniczego punktu końcowego. Przykład:  

      https://storageaccountname-secondary.blob.core.windows.net/vhds/BlobName.vhd

    - **Sygnatura dostępu**Współdzielonego: Użyj tokenu sygnatury dostępu, aby uzyskać dostęp do danych z punktu końcowego. Aby uzyskać więcej informacji, zobacz [Korzystanie z podpisów dostępu współdzielonego](storage-sas-overview.md).

**Jak korzystać z protokołu FTP w celu uzyskania dostępu do danych, które są na koncie magazynu?**

Nie ma możliwości bezpośredniego dostępu do konta magazynu przy użyciu protokołu FTP. Można jednak skonfigurować maszynę wirtualną platformy Azure, a następnie zainstalować serwer FTP na maszynie wirtualnej. Pliki magazynu serwera FTP mogą mieć w udziale usługi Azure Files lub na dysku danych, który jest dostępny dla maszyny wirtualnej.

Jeśli chcesz pobierać tylko dane bez konieczności korzystania z Eksploratora magazynu lub podobnej aplikacji, możesz użyć tokenu sygnatury dostępu Współdzielonego. Aby uzyskać więcej informacji, zobacz [Korzystanie z podpisów dostępu współdzielonego](storage-sas-overview.md).

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.

Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), aby szybko rozwiązać problem.
