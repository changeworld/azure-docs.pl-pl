---
title: Migracja magazynu platformy Azure — często zadawane pytania | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące migracji usługi Azure Storage
services: storage
author: genlin
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.subservice: common
ms.openlocfilehash: cf1cba6f6d26d66fc560c86ea42459fa276cc880
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66114901"
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Często zadawane pytania dotyczące migracji usługi Azure Storage

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące migracji usługi Azure Storage. 

## <a name="faq"></a>Często zadawane pytania

**Jak utworzyć skrypt, aby skopiować pliki z jednego kontenera do innej?**

Aby skopiować pliki między kontenerów, można użyć narzędzia AzCopy. Zobacz poniższy przykład:

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

Korzysta z narzędzia AzCopy [API obiektu Blob kopiowania](https://docs.microsoft.com/rest/api/storageservices/copy-blob) skopiuj każdy plik w kontenerze.  
  
Można użyć dowolnej maszyny wirtualnej lub komputerze lokalnym, który ma dostęp do Internetu, aby uruchomić narzędzia AzCopy. Harmonogram usługi Azure Batch można również użyć, się to automatycznie, ale jest bardziej skomplikowane.  
  
Skrypt automatyzacji jest przeznaczony do wdrażania usługi Azure Resource Manager zamiast manipulowanie zawartością magazynu. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i programu Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**Są naliczane opłaty, kopiowania danych między dwoma udziałami plików w tym samym koncie magazynu, w tym samym regionie?**

Nie. Nie ma opłat dla tego procesu.

**Jak wykonać kopię zapasową moich całe konto magazynu do innego konta magazynu?**

Nie ma opcji do utworzenia kopii zapasowej całe konto magazynu bezpośrednio. Jednak można ręcznie przenieść kontener na tym koncie magazynu do innego konta za pomocą narzędzia AzCopy lub Eksploratora usługi Storage. Poniższe kroki pokazują jak przenieść kontener za pomocą narzędzia AzCopy:  
 

1.  Zainstaluj [AzCopy](storage-use-azcopy.md) narzędzie wiersza polecenia. To narzędzie ułatwia przenoszenie pliku wirtualnego dysku twardego między kontami magazynu.

2.  Po zainstalowaniu narzędzia AzCopy na Windows za pomocą Instalatora programu Otwórz okno wiersza polecenia, a następnie przejdź do folderu instalacyjnego programu AzCopy na tym komputerze. Domyślnie narzędzie AzCopy jest zainstalowane na **% ProgramFiles (x86) %\Microsoft SDKs\Azure\AzCopy** lub **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**.

3.  Uruchom następujące polecenie, aby przenieść kontener. Należy zastąpić tekst przy użyciu rzeczywistych wartości.   
     
            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

    - `/Source`: Podaj identyfikator URI dla źródłowego konta magazynu (maksymalnie kontenera).  
    - `/Dest`: Podaj identyfikator URI dla docelowego konta magazynu (maksymalnie kontenera).  
    - `/SourceKey`: Podaj klucz podstawowy dla źródłowego konta magazynu. Możesz skopiować ten klucz w witrynie Azure portal, wybierając konto magazynu.  
    - `/DestKey`: Podaj klucz podstawowy dla docelowego konta magazynu. Możesz skopiować ten klucz z portalu, wybierając konto magazynu.

Po uruchomieniu tego polecenia, pliki kontenera zostaną przeniesione na docelowym koncie magazynu.

> [!NOTE]
> Interfejsu wiersza polecenia narzędzia AzCopy nie działa razem z **wzorzec** Przełącz podczas kopiowania z platformy Azure w jeden obiekt blob do innego.
>
> Możesz bezpośrednio skopiować i Edycja — polecenie narzędzia AzCopy i zweryfikować, aby upewnić się, że **wzorzec** pasuje do źródła. Ponadto upewnij się, że **/S** symbole wieloznaczne są stosowane. Aby uzyskać więcej informacji, zobacz [parametrów narzędzia AzCopy](storage-use-azcopy.md).

**Jak przenieść dane z jednego kontenera magazynu do innego**

Wykonaj następujące kroki:

1.  Tworzenie kontenera (folder) w docelowym obiekcie blob.

2.  Użyj [AzCopy](https://azure.microsoft.com/blog/azcopy-5-1-release/) skopiować zawartość z oryginalnym kontenerze obiektów blob do kontenera obiektów blob innego.

**Jak utworzyć skrypt programu PowerShell, aby przenieść dane z udziału plików platformy Azure z jednego do drugiego w usłudze Azure Storage?**

Narzędzia AzCopy można użyć, aby przenieść dane z jednego udziału plików platformy Azure do innego w usłudze Azure Storage. Aby uzyskać więcej informacji, zobacz [transferowanie danych za pomocą narzędzia AzCopy w Windows](storage-use-azcopy.md) i [transferu danych za pomocą narzędzia AzCopy w systemie Linux](storage-use-azcopy-linux.md).

**Jak przekazywanie dużych plików CSV do usługi Azure Storage?**

Użyj narzędzia AzCopy do przekazania dużych plików CSV do usługi Azure Storage. Aby uzyskać więcej informacji, zobacz [transferowanie danych za pomocą narzędzia AzCopy w Windows](storage-use-azcopy.md) i [transferu danych za pomocą narzędzia AzCopy w systemie Linux](storage-use-azcopy-linux.md).

**Muszę przenieść dzienniki z dysku D z moim kontem magazynu platformy Azure każdego dnia. Jak zautomatyzować to**

Można użyć narzędzia AzCopy i Utwórz zadanie w harmonogramie zadań. Przekazywanie plików do konta usługi Azure storage przy użyciu narzędzia AzCopy skryptu wsadowego. Aby uzyskać więcej informacji, zobacz [sposób konfigurowania i uruchamiania zadania uruchamiania dla usługi w chmurze](../../cloud-services/cloud-services-startup-tasks.md).

**Jak przenieść Moje konto magazynu między subskrypcjami?**

Narzędzia AzCopy można użyć, aby przenieść swoje konto magazynu między subskrypcjami. Aby uzyskać więcej informacji, zobacz [transferowanie danych za pomocą narzędzia AzCopy w Windows](storage-use-azcopy.md) i [transferu danych za pomocą narzędzia AzCopy w systemie Linux](storage-use-azcopy-linux.md).

**Jak przenieść 10 TB danych do magazynu w innym regionie?**

Narzędzia AzCopy można użyć, aby przenieść dane. Aby uzyskać więcej informacji, zobacz [transferowanie danych za pomocą narzędzia AzCopy w Windows](storage-use-azcopy.md) i [transferu danych za pomocą narzędzia AzCopy w systemie Linux](storage-use-azcopy-linux.md).

**Jak skopiować dane ze środowiska lokalnego do usługi Azure Storage?**

Narzędzia AzCopy można użyć do kopiowania danych. Aby uzyskać więcej informacji, zobacz [transferowanie danych za pomocą narzędzia AzCopy w Windows](storage-use-azcopy.md) i [transferu danych za pomocą narzędzia AzCopy w systemie Linux](storage-use-azcopy-linux.md).

**Jak przenosić dane ze środowiska lokalnego do usługi Azure Files?**

Narzędzia AzCopy można użyć w celu przeniesienia danych. Aby uzyskać więcej informacji, zobacz [transferowanie danych za pomocą narzędzia AzCopy w Windows](storage-use-azcopy.md) i [transferu danych za pomocą narzędzia AzCopy w systemie Linux](storage-use-azcopy-linux.md).

**Sposób mapowania folderu kontenerów na maszynę wirtualną?**

Użyj udziału plików platformy Azure.

**Jak wykonać kopię zapasową danych usługi Azure file storage?**

Istnieje rozwiązanie tworzenia kopii zapasowej. Usługi Azure Files obsługuje również asynchronicznych kopii. Tak można było skopiować pliki:

- Z udziału do innego udziału w ramach konta magazynu lub do innego konta magazynu.

- Z udziału do kontenera obiektów blob na koncie magazynu lub do innego konta magazynu.

Aby uzyskać więcej informacji, zobacz [transferowanie danych za pomocą narzędzia AzCopy w Windows](storage-use-azcopy.md).

**Jak przenieść dysków zarządzanych do innego konta magazynu?**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Wykonaj następujące kroki:

1.  Zatrzymaj maszynę wirtualną, dołączonego do dysków zarządzanych.

2.  Skopiuj dysk zarządzany wirtualnego dysku twardego z jednego obszaru do innego, uruchamiając następujący skrypt programu Azure PowerShell:

    ```
    Connect-AzAccount

    Select-AzSubscription -SubscriptionId <ID>

    $sas = Grant-AzDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  Tworzenie dysku zarządzanego przy użyciu pliku wirtualnego dysku twardego w innym regionie, do którego została skopiowana wirtualnego dysku twardego. Aby to zrobić, uruchom następujący skrypt programu Azure PowerShell:  

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

Aby uzyskać więcej informacji na temat sposobu wdrażania maszyny wirtualnej na podstawie dysku zarządzanego, zobacz [CreateVmFromManagedOsDisk.ps1](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/blob/master/CreateVmFromManagedOsDisk.ps1).

**Jak pobrać 1 – 2 TB danych w witrynie Azure portal?**

Narzędzia AzCopy można użyć, aby pobrać dane. Aby uzyskać więcej informacji, zobacz [transferowanie danych za pomocą narzędzia AzCopy w Windows](storage-use-azcopy.md) i [transferu danych za pomocą narzędzia AzCopy w systemie Linux](storage-use-azcopy-linux.md).

**Jak zmienić lokalizacji pomocniczej w regionie Europa dla konta magazynu?**

Podczas tworzenia konta magazynu, możesz wybrać region podstawowy dla konta. Wybór w regionie pomocniczym zależy od regionu podstawowego, a nie można jej zmienić. Aby uzyskać więcej informacji, zobacz [magazyn geograficznie nadmiarowy (GRS): Replikacji między regionami dla usługi Azure Storage](storage-redundancy.md).

**Gdzie można uzyskać więcej informacji na temat szyfrowania usługi Storage (SSE) Azure?**  
  
Zobacz następujące artykuły:

-  [Azure Storage security guide (Przewodnik po zabezpieczeniach usługi Azure Storage)](storage-security-guide.md)

-  [Szyfrowanie usługi Azure Storage dla danych magazynowanych](storage-service-encryption.md)

**Jak przenieść lub pobrać dane z konta magazynu?**

Narzędzia AzCopy można użyć, aby pobrać dane. Aby uzyskać więcej informacji, zobacz [transferowanie danych za pomocą narzędzia AzCopy w Windows](storage-use-azcopy.md) i [transferu danych za pomocą narzędzia AzCopy w systemie Linux](storage-use-azcopy-linux.md).


**Jak zaszyfrować danych w ramach konta magazynu?**

Po włączeniu szyfrowania w ramach konta magazynu, istniejące dane nie są szyfrowane. Aby zaszyfrować istniejących danych, należy przekazujesz go ponownie do konta magazynu.

Narzędzia AzCopy można użyć, aby skopiować dane do innego konta magazynu, a następnie przenieść dane z powrotem. Można również użyć [szyfrowanie w spoczynku](storage-service-encryption.md).

**Jak pobrać wirtualnego dysku twardego na komputerze lokalnym, inne niż przy użyciu opcji pobierania w portalu?**

Możesz użyć [Eksploratora usługi Storage](https://azure.microsoft.com/features/storage-explorer/) pobierania wirtualnego dysku twardego.

**Czy istnieją wymagania wstępne związane z zmiana replikacji konta magazynu z magazynu geograficznie nadmiarowego magazynu lokalnie nadmiarowego?**

Nie. 

**Jak uzyskać dostęp do magazynu nadmiarowego usługi Azure Files?**

Magazyn geograficznie nadmiarowy z dostępem do odczytu jest wymagany dostęp do magazynu nadmiarowego. Jednak usługi pliki Azure obsługuje tylko lokalnie nadmiarowy magazyn i standardowy magazyn geograficznie nadmiarowy, który nie zezwala na dostęp tylko do odczytu. 

**Jak przenieść z konta usługi premium storage na konto magazynu w warstwie standardowa?**

Wykonaj następujące kroki:

1.  Utwórz konto magazynu w warstwie standardowa. (Lub użyć istniejącego konta magazynu w warstwie standardowa w ramach subskrypcji).

2.  Pobierz narzędzia AzCopy. Uruchom jedno z następujących poleceń narzędzia AzCopy.
      
    Aby skopiować cały dysków w ramach konta magazynu:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S 

    Aby skopiować tylko jeden dysk, podaj nazwę dysku **wzorzec**:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

   
Operacja może potrwać kilka godzin.

Aby upewnić się, że transfer zakończyło się pomyślnie, sprawdź docelowy kontener konta magazynu w witrynie Azure portal. Po skopiowaniu dyski na konto magazynu w warstwie standardowa, można dołączyć do maszyny wirtualnej, jako istniejącego dysku. Aby uzyskać więcej informacji, zobacz [jak dołączyć dysk danych zarządzanych na maszynę wirtualną Windows w witrynie Azure portal](../../virtual-machines/windows/attach-managed-disk-portal.md).  
  
**Jak przekonwertować usługi Azure Premium Storage dla udziału plików**

Usługa Premium Storage nie jest dozwolona w udziale plików platformy Azure.

**Jak uaktualnić z konta magazynu w warstwie standardowa na koncie magazynu w warstwie premium? Jak obniżyć z konta usługi premium storage na konto magazynu w warstwie standardowa?**

Musi utworzyć konto magazynu docelowego, kopiowanie danych z konta źródłowego do docelowego konta, a następnie usuń konto źródłowe. Można użyć narzędzia, takiego jak narzędzie AzCopy do kopiowania danych.

Jeśli masz maszyny wirtualne, należy wykonać dodatkowe kroki przed migracją danych konta magazynu. Aby uzyskać więcej informacji, zobacz [migracji do usługi Azure Premium Storage (dysków niezarządzanych)](storage-migration-to-premium-storage.md).

**Jak przenieść z klasycznego konta magazynu na konto magazynu usługi Azure Resource Manager?**

Możesz użyć **AzStorageAccount przenoszenia** polecenia cmdlet. To polecenie cmdlet ma wiele kroków (Sprawdzanie poprawności, przygotowywanie, Zatwierdź). Można sprawdzić przeniesienie, przed jej wprowadzeniem.

Jeśli masz maszyny wirtualne, należy wykonać dodatkowe kroki przed migracją danych konta magazynu. Aby uzyskać więcej informacji, zobacz [migrację zasobów IaaS z wersji klasycznej do usługi Azure Resource Manager przy użyciu programu Azure PowerShell](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md).

**Jak mogę pobrać dane na komputerze z systemem Linux z konta usługi Azure storage, lub przekazania danych z maszyny z systemem Linux?**

Można użyć wiersza polecenia platformy Azure.

- Pobierz pojedynczy obiekt blob:

      azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

- Przekaż jeden obiekt blob: 

      azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**Jak można I umożliwić innym osobom dostęp do Moje zasoby magazynu?**

Aby umożliwić innym osobom dostęp do zasobów magazynu:

-   Zapewnienie dostępu do zasobu, należy użyć token (SAS) sygnatury dostępu współdzielonego. 

-   Podaj użytkownika z kluczem podstawowym lub pomocniczym dla konta magazynu. Aby uzyskać więcej informacji, zobacz [Zarządzanie kontem magazynu](storage-account-manage.md#access-keys).

-   Zmień zasady dostępu, aby zezwolić na dostęp anonimowy. Aby uzyskać więcej informacji, zobacz [przyznawanie uprawnień użytkownikom anonimowym do kontenerów i obiektów blob](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs).

**Gdy narzędzie AzCopy jest zainstalowane**

-   Jeśli uzyskujesz dostęp do narzędzia AzCopy z wiersza polecenia usługi Microsoft Azure Storage, wpisz **AzCopy**. W wierszu polecenia jest instalowany wraz z narzędzia AzCopy.

-   Jeśli zainstalowana wersja 32-bitowa jest znajduje się w tym miejscu: **% ProgramFiles(x86) %\\Microsoft SDKs\\Azure\\AzCopy**.

-   Jeśli zainstalowana wersja 64-bitowych jest znajduje się w tym miejscu: **% ProgramFiles %\\Microsoft SDKs\\Azure\\AzCopy**.

**Konto replikowanego magazynu (na przykład magazyn strefowo nadmiarowy, Magazyn geograficznie nadmiarowy lub magazyn geograficznie nadmiarowy z dostępem do odczytu) jak uzyskać dostęp do danych przechowywanych w regionie pomocniczym?**

-   Jeśli używasz magazynu strefowo nadmiarowego lub magazynu geograficznie nadmiarowego, nie możesz uzyskać dostępu danych z regionu pomocniczego, chyba że Zainicjuj tryb failover do tego regionu. Aby uzyskać więcej informacji na temat procesu pracy awaryjnej, zobacz [awaryjnego odzyskiwania i przechowywania konta pracy awaryjnej (wersja zapoznawcza) w usłudze Azure Storage](storage-disaster-recovery-guidance.md).

-   Jeśli używasz magazynu geograficznie nadmiarowego do odczytu, w dowolnym momencie za dostępne dane z regionu pomocniczego. Użyj jednej z następujących metod:  
      
    - **AzCopy**: Dołącz **-dodatkowej** do nazwy konta magazynu w adresie URL, dostęp do pomocniczego punktu końcowego. Na przykład:  
     
      https://storageaccountname-secondary.blob.core.windows.net/vhds/BlobName.vhd

    - **Token sygnatury dostępu Współdzielonego**: Użyj tokenu sygnatury dostępu Współdzielonego na dostęp do danych z punktu końcowego. Aby uzyskać więcej informacji, zobacz [używanie sygnatury dostępu współdzielonego](storage-dotnet-shared-access-signature-part-1.md).

**Jak używać protokołu HTTPS domeny niestandardowej z moim kontem magazynu? Na przykład, jak zrobić "https:\//mystorageaccountname.blob.core.windows.net/images/image.gif" są wyświetlane jako "https:\//www.contoso.com/images/image.gif"?**

Protokół SSL nie jest obecnie obsługiwane dla kont magazynu z zastosowaniem domen niestandardowych.
Jednak można użyć innych niż HTTPS domen niestandardowych. Aby uzyskać więcej informacji, zobacz [Konfigurowanie niestandardowej nazwy domeny dla punktu końcowego usługi Blob storage](../blobs/storage-custom-domain-name.md).

**Jak używać protokołu FTP na dostęp do danych, który jest w ramach konta magazynu?**

Nie ma możliwości dostęp do konta magazynu bezpośrednio przy użyciu protokołu FTP. Można jednak skonfigurować maszynę wirtualną platformy Azure, a następnie zainstaluj serwer FTP na maszynie wirtualnej. Program może przechowywać pliki w udziale plików platformy Azure lub na dysku danych, która jest dostępna dla maszyny wirtualnej serwera FTP.

Jeśli chcesz tylko po to pobrać dane bez konieczności używania Eksploratora usługi Storage lub podobnej aplikacji, można użyć tokenu sygnatury dostępu Współdzielonego. Aby uzyskać więcej informacji, zobacz [używanie sygnatury dostępu współdzielonego](storage-dotnet-shared-access-signature-part-1.md).

**Jak migrować obiekty BLOB z jednego konta magazynu do innego?**

 Można to zrobić za pomocą naszych [obiektu Blob skryptu migracji](../scripts/storage-common-transfer-between-storage-accounts.md).

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.

Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.
