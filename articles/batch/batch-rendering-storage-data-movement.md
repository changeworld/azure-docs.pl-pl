---
title: Przenoszenie magazynu i danych do renderowania — Azure Batch
description: Opcje przenoszenia magazynu i danych dla obciążenia renderowania
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 5a0d4dc82995e63697cc673bc54695c9c6d586df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773999"
---
# <a name="storage-and-data-movement-options-for-rendering-asset-and-output-files"></a>Opcje przenoszenia magazynu i danych renderowania plików zasobów i danych wyjściowych

Istnieje wiele opcji udostępniania plików scen i zasobów do aplikacji renderowanie w puli maszyn wirtualnych:

* [Usługa Azure blob storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction):
  * Pliki sceny i zasobów są przekazywane do usługi blob storage w lokalnym systemie plików. Gdy aplikacja jest uruchamiana przez zadanie, wymagane pliki są kopiowane z magazynu obiektów blob na maszynie Wirtualnej, dzięki czemu są one dostępne przez aplikację renderowania. Pliki wyjściowe są zapisywane przez aplikację renderowania na dysku maszyny Wirtualnej i następnie kopiowane do magazynu obiektów blob.  Jeśli to konieczne, można pobrać pliki wyjściowe z magazynu obiektów blob do lokalnego systemu plików.
  * Usługa Azure blob storage jest to proste i ekonomiczne rozwiązanie dla mniejsze projekty.  Wszystkich zasobów, które są wymagane pliki dla każdej puli maszyn wirtualnych, następnie po zwiększa liczbę i rozmiar plików zasobów jak najbardziej efektywne są opieki musi podjąć w celu zapewnienia transferu plików.  
* Usługa Azure storage jako systemu plików przy użyciu [blobfuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux):
  * W przypadku maszyn wirtualnych systemu Linux na koncie magazynu można udostępniane i używany jako system plików, stosowania sterownik systemu plików wirtualnych blobfuse.
  * Ta opcja ma tę zaletę, że jest to bardzo ekonomiczna jako żadnych maszyn wirtualnych są wymagane przez system plików, a także blobfuse buforowanie na maszynach wirtualnych pozwala uniknąć wielokrotnego pliki do pobrania tych samych plików dla wielu zadań i zadań podrzędnych.  Przenoszenie danych również jest proste, jak pliki są po prostu obiektów blob i standardowych interfejsów API i narzędzi, takich jak narzędzia azcopy, może służyć do skopiowania plików między lokalnym systemem plików i magazynu platformy Azure.
* System plików lub udział plików:
  * W zależności od systemu operacyjnego maszyny Wirtualnej i wydajność/skalowania wymagań, następnie opcje obejmują [usługi Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction), za pomocą maszyny Wirtualnej z dyskami dołączonymi systemu plików NFS, przy użyciu wielu maszyn wirtualnych z dyskami dołączonymi systemu plików rozproszonych, takich jak GlusterFS, lub Korzystanie z oferty innej firmy.
  * [Systemy Avere](https://www.averesystems.com/) jest teraz częścią firmy Microsoft i będzie miał w najbliższej przyszłości rozwiązania, które są idealne dla renderowanie na dużą skalę, wysokiej wydajności.  Spowoduje to włączenie rozwiązania Avere systemu plików NFS opartych na platformie Azure lub pamięć podręczną protokołu SMB, aby utworzyć, która działa w połączeniu z usługą blob storage lub urządzeniach NAS w środowisku lokalnym.
  * W systemie plików, pliki można je odczytać lub zapisywane bezpośrednio w systemie plików lub można kopiować między systemem plików i puli maszyn wirtualnych.
  * Udostępniony system plików umożliwia dużą liczbę zasobów współużytkowanych przez projekty i zadania można używać z renderowania zadania tylko dostęp, co jest wymagane.

## <a name="using-azure-blob-storage"></a>Za pomocą usługi Azure blob storage

Należy używać konta usługi blob storage lub konta magazynu ogólnego przeznaczenia w wersji 2.  Te dwa typy kont magazynu można skonfigurować za pomocą znacznie wyższe limity w porównaniu do konta magazynu ogólnego przeznaczenia w wersji 1, zgodnie z opisem w [ten wpis w blogu](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).  Po skonfigurowaniu wyższe limity umożliwi znacznie lepszą wydajność i skalowalność, szczególnie w przypadku, gdy istnieje wiele puli maszyn wirtualnych, uzyskiwania dostępu do konta magazynu.

### <a name="copying-files-between-client-and-blob-storage"></a>Kopiowanie plików między klienta i blob storage

Aby skopiować pliki do i z usługi Azure storage, różne mechanizmy służy obiekt blob magazynu interfejsu API, w tym [Biblioteka przenoszenia danych usługi Azure Storage](https://github.com/Azure/azure-storage-net-data-movement), narzędzie wiersza polecenia azcopy dla [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) lub [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux), [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/), i [usługa Azure Batch Explorer](https://azure.github.io/BatchExplorer/).

Na przykład przy użyciu narzędzia azcopy, wszystkie zasoby w folderze można przenieść w następujący sposób:


`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /Y`

Aby skopiować tylko zmodyfikowane pliki, parametr /XO może służyć do:

`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /XO /Y`

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopiowanie plików wejściowych zasobu z magazynu obiektów blob do maszynami wirtualnymi pul usługi Batch

Istnieje kilka różnych podejść do kopiowania plików z najlepszym rozwiązaniem, określony przez rozmiar zasobów zadania.
Najprostszą metodą jest do skopiowania wszystkich plików zasobów do puli maszyn wirtualnych dla każdego zadania:

* Gdy pliki są unikatowe dla zadania, ale są wymagane dla wszystkich zadań, zadania, a następnie [zadania podrzędnego przygotowania zadania](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask) można określić do skopiowania wszystkich plików.  Zadanie przygotowania zadania jest uruchamiane jeden raz po pierwsze zadanie jest wykonywane na maszynie Wirtualnej, ale nie jest ponownie uruchamiany dla kolejnych zadań.
* A [zadanie podrzędne zwolnienia zadania](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask) należy określić umożliwiającego usunięcie plików dla zadania, po zakończeniu zadania; pozwoli to uniknąć wprowadzenie uzupełnione przez wszystkie pliki zasobów zadania dysku maszyny Wirtualnej.
* W przypadku wielu zadań przy użyciu tych samych zasobów, przy użyciu tylko zmiany przyrostowe zasobów dla każdego zadania, następnie wszystkie pliki zawartości są nadal kopiowane, nawet jeśli zostały zaktualizowane tylko ich podzestaw.  Powinien to być nieefektywne, gdy istnieje wiele zasobów dużych plików.

Gdy pliki zawartości są ponownie między zadaniami przy użyciu tylko przyrostowe zmiany między zadaniami, jest bardziej wydajne, ale wymaga nieco więcej wysiłku podejście jest do przechowywania zasobów w folderze udostępnionym na maszynie Wirtualnej i synchronizowanie zmienionych plików.

* Zadanie przygotowania zadania wykona kopiowania przy użyciu narzędzia azcopy z parametrem /XO do folderu udostępnionego maszyny Wirtualnej, określone przez zmienną środowiskową AZ_BATCH_NODE_SHARED_DIR.  To spowoduje tylko skopiowanie zmienionych plików do każdej maszyny Wirtualnej.
* Myślenia musi nadać rozmiarowi wszystkich trwałych upewnij się, że mieści się na tymczasowym dysku w puli maszyn wirtualnych.

Usługa Azure Batch ma wbudowaną funkcję kopiować pliki między maszynami wirtualnymi pul usługi Batch i konta magazynu.  Zadanie [pliki zasobów](https://docs.microsoft.com/rest/api/batchservice/job/add#resourcefile) kopiowania plików z magazynu do puli maszyn wirtualnych i może być określona dla zadania podrzędnego przygotowania zadania.  Niestety w przypadku setek plików jest możliwość osiągnięty limit i zadania nie powiedzie się.  W przypadku dużej liczby zasobów zaleca się w zadania podrzędnego przygotowania zadania, można używać symboli wieloznacznych, która nie ma żadnych ograniczeń za pomocą wiersza polecenia narzędzia azcopy.

### <a name="copying-output-files-to-blob-storage-from-batch-pool-vms"></a>Kopiowanie plików wyjściowych do usługi blob storage z maszynami wirtualnymi pul usługi Batch

[Pliki wyjściowe](https://docs.microsoft.com/rest/api/batchservice/task/add#outputfile) mogą być używane kopiowania plików z puli maszyn wirtualnych do magazynu.  Jeden lub więcej plików może zostać skopiowany na określone konto magazynu z maszyny Wirtualnej, po ukończeniu zadania.  Powinny zostać skopiowane wyniku renderowania, ale również może być pożądane do przechowywania plików dziennika.

## <a name="using-a-blobfuse-virtual-file-system-for-linux-vm-pools"></a>W przypadku pul maszyn wirtualnych systemu Linux przy użyciu blobfuse wirtualny system plików

Blobfuse jest sterownik systemu plików wirtualnych usługi Azure Blob Storage, która umożliwia dostęp do plików przechowywanych jako obiekty BLOB na koncie magazynu za pomocą systemu plików w systemie Linux.

Węzły puli można zainstalować system plików, podczas uruchamiania lub instalacja może się zdarzyć, jako część zadania przygotowania zadania — zadanie jest uruchomione tylko pierwsze zadanie w ramach zadania działa w węźle.  Blobfuse można skonfigurować na wykorzystanie dysku RAM i lokalny dysk SSD maszyn wirtualnych do buforowania plików, co spowoduje zwiększenie wydajności znacznie Jeśli wiele zadań w węźle dostęp do niektórych z tych samych plików.

[Dostępne są przykładowe szablony](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/vray/render-linux-with-blobfuse-mount) uruchamianie autonomicznego V-Ray renderowany przy użyciu systemu plików blobfuse i może służyć jako podstawa dla szablonów dla innych aplikacji.

### <a name="accessing-files"></a>Uzyskiwanie dostępu do plików

Zadania podrzędne, określ ścieżki dla plików wejściowych i pliki wyjściowe przy użyciu systemu zainstalowanego pliku.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopiowanie plików wejściowych zasobu z magazynu obiektów blob do maszynami wirtualnymi pul usługi Batch

Ponieważ pliki są po prostu obiektów blob w usłudze Azure Storage, następnie blob standardowych interfejsów API, narzędzi i interfejsów użytkownika może służyć do kopiowania plików między lokalnych plików systemu i blob storage; na przykład, narzędzia azcopy Eksploratora usługi Storage, narzędzie Batch Explorer itp.

## <a name="using-azure-files-with-windows-vms"></a>Za pomocą usługi Azure Files przy użyciu maszyn wirtualnych Windows

[Usługa Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) oferuje w pełni zarządzane udziały plików w chmurze, które są dostępne za pośrednictwem protokołu SMB.  Usługa pliki Azure opiera się na usłudze Azure blob storage; jest [efektywny kosztowo sposób](https://azure.microsoft.com/pricing/details/storage/files/) i może być konfigurowana przy użyciu replikacji danych do innego regionu, więc globalnie nadmiarowy.  [Skalowanie elementów docelowych](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-files-scale-targets) powinny być przejrzane w celu ustalenia, jeśli należy użyć usługi Azure Files podany rozmiar prognozy puli i liczby plików zasobów.

Brak [wpis w blogu](https://blogs.msdn.microsoft.com/windowsazurestorage/2014/05/26/persisting-connections-to-microsoft-azure-files/) i [dokumentacji](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) obejmujące sposób instalacji udziału plików platformy Azure.

### <a name="mounting-an-azure-files-share"></a>Instalowanie udziału plików platformy Azure

Operacja instalacji do użycia w usłudze Batch, należy wykonać każdorazowo, ponieważ nie jest możliwe utrwalanie połączeń między zadaniami można uruchomić zadania w.  W tym celu najłatwiej na potrzeby cmdkey utrwalanie poświadczeń przy użyciu zadania podrzędnego uruchamiania w konfiguracji puli, a następnie zainstalować ten udział przed każdym zadaniu.

Przykład użytkowania cmdkey w szablonie puli (poprzedzone znakiem zmiany znaczenia do użycia w pliku JSON) — należy pamiętać, że gdy oddzielając wywołanie cmdkey z wywołania polecenie net use, kontekstu użytkownika dla zadania podrzędnego uruchamiania musi być taka sama jak używane do uruchamiania zadań:

```
"startTask": {
  "commandLine": "cmdkey /add:storageaccountname.file.core.windows.net
    /user:AZURE\\markscuscusbatch /pass:storage_account_key",
  "userIdentity":{
    "autoUser": {
      "elevationLevel": "nonadmin",
      "scope": "pool"
    }
}
```

Przykładowy wiersz polecenia zadania zadania:
```
"commandLine":"net use S:
  \\\\storageaccountname.file.core.windows.net\\rendering &
3dsmaxcmdio.exe -v:5 -rfw:0 -10 -end:10
  -bitmapPath:\"s:\\3dsMax\\Dragon\\Assets\"
  -outputName:\"s:\\3dsMax\\Dragon\\RenderOutput\\dragon.jpg\"
  -w:1280 -h:720
  \"s:\\3dsMax\\Dragon\\Assets\\Dragon_Character_Rig.max\""
```

### <a name="accessing-files"></a>Uzyskiwanie dostępu do plików

Zadania podrzędne, określ ścieżki dla plików wejściowych i plików wyjściowych przy użyciu zainstalowany system plików, za pomocą zamapowany dysk lub ścieżkę UNC.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopiowanie plików wejściowych zasobu z magazynu obiektów blob do maszynami wirtualnymi pul usługi Batch

Usługa pliki systemu Azure są obsługiwane przez wszystkie główne interfejsów API i narzędzi usługi Azure Storage obsługuje; np. narzędzia azcopy, wiersza polecenia platformy Azure, Eksploratora usługi Storage, programu PowerShell usługi Azure Batch Explorer itp.

[Usługa Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) jest dostępny do automatycznej synchronizacji plików między lokalnym systemem plików i udział plików platformy Azure.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o opcjach magazynu Zobacz szczegółowa dokumentacja:

* [Usługa Azure blob storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)
* [Blobfuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux)
* [Usługa pliki Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)
