---
title: Wysyłanie danych do warstwy gorąca, zimna i archiwizowana warstwa obiektów blob za pośrednictwem usługi Azure Data Box/Azure Data Box Heavy
description: W tym artykule opisano sposób wysyłania danych do odpowiedniej warstwy magazynu obiektów blob, takiej jak hot, cold lub archive, za pomocą usługi Azure Data Box lub Azure Data Box Heavy.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 05/24/2019
ms.author: alkohli
ms.openlocfilehash: 31178284313ad7dafb094b109a75d4550cabd39c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560375"
---
# <a name="use-azure-data-box-or-azure-data-box-heavy-to-send-data-to-appropriate-azure-storage-blob-tier"></a>Wysyłanie danych do odpowiedniej warstwy obiektów blob usługi Azure Storage za pomocą usługi Azure Data Box lub Azure Data Box Heavy

Usługa Azure Data Box przenosi duże ilości danych na platformę Azure, wysyłając własne urządzenie magazynujące. Wypełniasz urządzenie danymi i zwracasz go. Dane z pola danych są przekazywane do domyślnej warstwy skojarzonej z kontem magazynu. Następnie można przenieść dane do innej warstwy magazynu.

W tym artykule opisano, jak dane przekazywane przez pole danych można przenieść do warstwy obiektów blob gorąca, zimna lub archiwum. Ten artykuł dotyczy zarówno usługi Azure Data Box, jak i Azure Data Box Heavy.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="choose-the-correct-storage-tier-for-your-data"></a>Wybierz właściwą warstwę pamięci masowej dla danych

Usługa Azure Storage umożliwia trzy różne warstwy do przechowywania danych w sposób najbardziej opłacalne - Hot, Cold lub Archive. Warstwa magazynu gorąca jest zoptymalizowana pod kątem przechowywania danych, do których często uzyskuje się dostęp. Magazyn na gorąco ma wyższe koszty pamięci masowej niż przechowywanie fajne i archiwalne, ale najniższe koszty dostępu.

Warstwa chłodnego magazynu jest dla rzadko dostępnych danych, które muszą być przechowywane przez co najmniej 30 dni. Koszt magazynowania dla warstwy zimnej warstwy jest niższa niż w warstwie magazynu gorąca, ale opłaty za dostęp do danych są wysokie w porównaniu do warstwy Hot.

Warstwa Usługi Azure Archive jest w trybie offline i oferuje najniższe koszty magazynowania, ale także najwyższe koszty dostępu. Ta warstwa jest przeznaczona dla danych, które pozostają w magazynie archiwalnym przez co najmniej 180 dni. Aby uzyskać szczegółowe informacje na temat każdej z tych warstw i modelu cenowego, przejdź do [porównywania warstw magazynu](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).

Dane z pola danych lub data box grube są przekazywane do warstwy magazynu, która jest skojarzona z kontem magazynu. Podczas tworzenia konta magazynu można określić warstwę dostępu jako gorąca lub zimna. W zależności od wzorca dostępu obciążenia i kosztów można przenieść te dane z domyślnej warstwy do innej warstwy magazynu.

Na kontach magazynu obiektów w obiekcie lub ogólnego przeznaczenia (GPv2) można posiądać tylko dane magazynu obiektów. Konta ogólnego przeznaczenia w wersji 1 (GPv1) nie obsługują warstw. Aby wybrać właściwą warstwę magazynu dla danych, zapoznaj się z zagadnieniami opisanymi w [warstwie magazyn obiektów Blob platformy Azure: Warstwa magazynu Premium, Hot, Cool i Archive.](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)

## <a name="set-a-default-blob-tier"></a>Ustawianie domyślnej warstwy obiektów blob

Domyślna warstwa obiektów blob jest określona podczas tworzenia konta magazynu w witrynie Azure portal. Po wybraniu typu magazynu jako magazynu GPv2 lub blob można określić atrybut warstwy dostęp. Domyślnie wybrano warstwę Gorąca.

Nie można określić warstw, jeśli próbujesz utworzyć nowe konto podczas zamawiania pola danych lub danych. Po utworzeniu konta można zmodyfikować konto w portalu, aby ustawić domyślną warstwę dostępu.

Alternatywnie należy najpierw utworzyć konto magazynu z atrybutem określonej warstwy dostępu. Podczas tworzenia zamówienia Data Box lub Data Box Heavy wybierz istniejące konto magazynu. Aby uzyskać więcej informacji na temat ustawiania domyślnej warstwy obiektów blob podczas tworzenia konta magazynu, przejdź do [tematu Tworzenie konta magazynu w witrynie Azure portal](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=portal).

## <a name="move-data-to-a-non-default-tier"></a>Przenoszenie danych do warstwy nieobe domyślnej

Po przekazaniu danych z urządzenia Data Box do domyślnej warstwy można przenieść dane do warstwy nieobekonicznego. Istnieją dwa sposoby przenoszenia tych danych do warstwy nieobekonicznego.

- **Zarządzanie cyklem życia magazynu obiektów blob platformy Azure** — można użyć podejścia opartego na zasadach, aby automatycznie warstwy danych lub wygaśnie po zakończeniu jego cyklu życia. Aby uzyskać więcej informacji, przejdź do [cyklu życia magazynu obiektów Blob platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-lifecycle-managment-concepts).
- **Skrypty** — można użyć podejścia skryptowego za pośrednictwem programu Azure PowerShell, aby włączyć warstwy na poziomie obiektów blob. Można wywołać `SetBlobTier` operację, aby ustawić warstwę na obiekcie blob.

## <a name="use-azure-powershell-to-set-the-blob-tier"></a>Ustawianie warstwy obiektów blob za pomocą programu Azure PowerShell

W poniższych krokach opisano, jak można ustawić warstwę obiektów blob na Archiwum przy użyciu skryptu programu Azure PowerShell.

1. Otwórz sesję programu Windows PowerShell z podwyższonym poziomem uprawnień. Upewnij się, że uruchomiony program PowerShell 5.0 lub nowszy. Wpisz:

   `$PSVersionTable.PSVersion`     

2. Zaloguj się do programu Azure PowerShell. 

   `Login-AzAccount`  

3. Zdefiniuj zmienne dla konta magazynu, klucza dostępu, kontenera i kontekstu magazynu.

    ```powershell
    $StorageAccountName = "<enter account name>"
    $StorageAccountKey = "<enter account key>"
    $ContainerName = "<enter container name>"
    $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    ```

4. Pobierz wszystkie obiekty blob w kontenerze.

    `$blobs = Get-AzStorageBlob -Container "<enter container name>" -Context $ctx`
 
5. Ustaw warstwę wszystkich obiektów blob w kontenerze na Archiwum.

    ```powershell
    Foreach ($blob in $blobs) {
    $blob.ICloudBlob.SetStandardBlobTier("Archive")
    }
    ```

    Poniżej przedstawiono przykładowe dane wyjściowe:

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    PS C:\WINDOWS\system32> $PSVersionTable.PSVersion

    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      17763  134
    PS C:\WINDOWS\system32> Login-AzAccount

    Account          : gus@contoso.com
    SubscriptionName : MySubscription
    SubscriptionId   : subscription-id
    TenantId         : tenant-id
    Environment      : AzureCloud

    PS C:\WINDOWS\system32> $StorageAccountName = "mygpv2storacct"
    PS C:\WINDOWS\system32> $StorageAccountKey = "mystorageacctkey"
    PS C:\WINDOWS\system32> $ContainerName = "test"
    PS C:\WINDOWS\system32> $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    PS C:\WINDOWS\system32> $blobs = Get-AzStorageBlob -Container "test" -Context $ctx
    PS C:\WINDOWS\system32> Foreach ($blob in $blobs) {
    >> $blob.ICloudBlob.SetStandardBlobTier("Archive")
    >> }
    PS C:\WINDOWS\system32>
    ```
   > [!TIP]
   > Jeśli chcesz, aby dane były archiwizowane przy pozyskiwania, ustaw domyślną warstwę konta na Gorącą. Jeśli domyślna warstwa jest Cool, a następnie istnieje 30-dniowa kara za wcześniejsze usunięcie, jeśli dane natychmiast przenoszą się do archiwum.

## <a name="next-steps"></a>Następne kroki

-  Dowiedz się, jak rozwiązać [typowe scenariusze warstw danych za pomocą reguł zasad cyklu życia](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts#examples)

