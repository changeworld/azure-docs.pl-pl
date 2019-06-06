---
title: Użyj usługi Azure Data Box, Azure Data Box mocno do wysyłania danych na gorącą zimne, archiwum warstwy obiektu blob | Microsoft Docs danych
description: Opisuje sposób używania urządzenia Azure Data Box lub Azure Data Box mocno do wysyłania danych do odpowiedniego bloku warstwy magazynowania obiektu blob np. gorąca, zimnego lub archiwum
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 05/24/2019
ms.author: alkohli
ms.openlocfilehash: ea208c395e2ef69ce8f28052351643e963cceb05
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427880"
---
# <a name="use-azure-data-box-or-azure-data-box-heavy-to-send-data-to-appropriate-azure-storage-blob-tier"></a>Wysyłanie danych do odpowiedniej warstwy obiektu blob usługi Azure Storage za pomocą usługi Azure Data Box lub Azure Data Box duże

Urządzenie Azure Data Box przeniesieniem urządzenia magazynującego własności przenosi duże ilości danych na platformie Azure. Wypełnij w górę urządzenia przy użyciu danych i przywrócić go. Dane z urządzenia Data Box jest przekazywany do domyślnej warstwy skojarzone z kontem magazynu. Dane można następnie przenieść do innej warstwy magazynu.

W tym artykule opisano, jak dane, które zostanie przekazany przez urządzenie Data Box można przenieść na gorąca, zimnego lub archiwalna warstwy obiektu blob. Ten artykuł dotyczy zarówno usługi Azure Data Box, jak i duże pole danych platformy Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="choose-the-correct-storage-tier-for-your-data"></a>Wybierz warstwę magazynu poprawną danych

Usługa Azure storage umożliwia trzy różne warstwy do przechowywania danych w sposób najbardziej efektywny — gorąca, zimnego lub archiwum. Warstwa magazynu gorącego jest zoptymalizowana pod kątem magazynowania danych, z których korzysta się często. Magazynu gorącego ma wyższe koszty magazynowania, niż w warstwach chłodna i archiwum magazynu, ale najniższe koszty dostępu.

Warstwa magazynu chłodnego jest dla rzadko używanych danych, które muszą być przechowywane za okres co najmniej 30 dni. Koszt magazynu na potrzeby zimnych warstwa jest niższa niż w przypadku warstwy magazynu gorącego, ale opłaty za dostęp do danych jest wysokie, w porównaniu do warstwy gorąca i używanie.

Warstwę archiwum platformy Azure jest w trybie offline i oferuje najniższe koszty magazynowania, ale także najwyższe koszty dostępu. Ta warstwa jest przeznaczona dla danych, która pozostaje w magazynie archiwizacji za okres co najmniej 180 dni. Aby uzyskać szczegółowe informacje o każdej z tych warstw i model cenowy, przejdź do [Porównanie warstw magazynowania](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).

Duże pole danych lub danych z urządzenia Data Box jest przekazywany do warstwy magazynowania, który jest skojarzony z kontem magazynu. Podczas tworzenia konta magazynu, można określić warstwy dostępu jako gorąca lub zimnego. W zależności od wzorca dostępu kosztów i obciążeń, można przenieść dane z domyślnej warstwy do innej warstwy magazynu.

Może być tylko warstwy dane magazynu obiektów Blob storage lub ogólnego przeznaczenia w wersji 2 (GPv2) konta. Konta ogólnego przeznaczenia w wersji 1 (GPv1) nie obsługują warstw. Aby wybrać warstwę magazynowania poprawne dla Twoich danych, zapoznaj się z uwagami szczegółowo opisane w [usługi Azure Blob storage: — Wersja Premium, warstwy magazynowania gorąca, chłodna i archiwalna](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).

## <a name="set-a-default-blob-tier"></a>Ustaw domyślną warstwę obiektu blob

Domyślną warstwę obiektu blob jest określona, po utworzeniu konta magazynu w witrynie Azure portal. Po wybraniu typu magazynu jako konta GPv2 lub Blob storage można można określić atrybutu warstwy dostępu. Warstwy gorąca i używanie jest zaznaczone domyślnie.

Warstw nie może być określona, jeśli chcesz utworzyć nowe konto w przypadku porządkowania pola danych lub duże pole danych. Po utworzeniu konta można zmodyfikować konto w portalu, aby ustawić domyślną warstwę dostępu.

Alternatywnie utworzyć konta magazynu najpierw za pomocą atrybutu warstwy dostępu określony. Podczas tworzenia zamówienie urządzenia Data Box lub duże pole danych, wybierz istniejące konto magazynu. Aby uzyskać więcej informacji na temat sposobu ustawiania domyślnej warstwy obiektu blob podczas tworzenia konta magazynu, przejdź do [Tworzenie konta magazynu w witrynie Azure portal](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=portal).

## <a name="move-data-to-a-non-default-tier"></a>Przenoszenie danych do warstwy innych niż domyślne

Po przekazaniu danych z urządzenia Data Box do warstwy domyślne można przenieść dane do warstwy innych niż domyślne. Istnieją dwa sposoby, w celu przeniesienia tych danych do warstwy innych niż domyślne.

- **Zarządzanie cyklem życia magazynu w usłudze Azure Blob** — podejście oparte na zasadach można użyć do automatycznego warstwy danych lub wygaśnie z końcem jej cyklu projektowania. Aby uzyskać więcej informacji, przejdź do [zarządzania cyklem życia magazynu obiektów Blob platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-lifecycle-managment-concepts).
- **Obsługa skryptów** — podejście inicjowanych przez skrypty za pomocą programu Azure PowerShell można użyć w celu włączenia obsługi warstw na poziomie obiektu blob. Możesz wywołać `SetBlobTier` operację, aby ustawić warstwę w obiekcie blob.

## <a name="use-azure-powershell-to-set-the-blob-tier"></a>Ustawianie warstwy obiektu blob przy użyciu programu Azure PowerShell

Poniższe kroki opisują, jak można ustawić warstwy obiektu blob archiwum przy użyciu skryptu programu Azure PowerShell.

1. Otwórz sesję środowiska Windows PowerShell z podwyższonym poziomem uprawnień. Upewnij się, że uruchomione PowerShell 5.0 lub nowszy. Wpisz:

   `$PSVersionTable.PSVersion`     

2. Zaloguj się do programu Azure PowerShell. 

   `Login-AzAccount`  

3. Zdefiniuj zmienne do konta magazynu, klucz dostępu, kontenerów i kontekst magazynu.

    ```powershell
    $StorageAccountName = "<enter account name>"
    $StorageAccountKey = "<enter account key>"
    $ContainerName = "<enter container name>"
    $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    ```

4. Pobierz wszystkie obiekty BLOB w kontenerze.

    `$blobs = Get-AzStorageBlob -Container "<enter container name>" -Context $ctx`
 
5. Ustawić warstwę wszystkich obiektów blob w kontenerze do archiwum.

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
   > Jeśli chcesz, aby dane można zarchiwizować na pozyskiwania, należy ustawić domyślną warstwę konta gorąca. Jeśli domyślna warstwa chłodna, to ma 30-dniowej wczesne usunięcie karę Jeśli dane są przenoszone do archiwum natychmiast.

## <a name="next-steps"></a>Kolejne kroki

-  Dowiedz się, jak adres [typowych scenariuszy warstw danych z regułami zasad cyklu życia](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts#examples)

