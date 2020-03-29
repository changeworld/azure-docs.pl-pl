---
title: Przechowywanie i przenoszenie danych do renderowania — usługa Azure Batch
description: Dowiedz się więcej o różnych opcjach przechowywania i przenoszenia danych do renderowania obciążeń plików zasobów i plików wyjściowych.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 1076fc152ddf2c3a2d4f2346262ca90215d68ddf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75390382"
---
# <a name="storage-and-data-movement-options-for-rendering-asset-and-output-files"></a>Opcje przechowywania i przenoszenia danych do renderowania plików zasobów i plików wyjściowych

Istnieje wiele opcji udostępniania plików sceny i zasobów aplikacjom renderowania na maszynach wirtualnych puli:

* [Magazyn obiektów blob platformy Azure:](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)
  * Pliki scen i zasobów są przekazywane do magazynu obiektów blob z lokalnego systemu plików. Gdy aplikacja jest uruchamiana przez zadanie, wymagane pliki są kopiowane z magazynu obiektów blob na maszynę wirtualną, dzięki czemu mogą być dostępne dla aplikacji renderowania. Pliki wyjściowe są zapisywane przez aplikację renderowania na dysku maszyny Wirtualnej, a następnie kopiowane do magazynu obiektów blob.  W razie potrzeby pliki wyjściowe można pobrać z magazynu obiektów blob do lokalnego systemu plików.
  * Magazyn obiektów blob platformy Azure jest prostą i ekonomiczną opcją dla mniejszych projektów.  Ponieważ wszystkie pliki zasobów są wymagane na każdej puli maszyny Wirtualnej, a następnie raz liczba i rozmiar plików zasobów zwiększa należy dołożyć starań, aby upewnić się, że transfery plików są tak wydajne, jak to możliwe.  
* Magazyn platformy Azure jako system plików przy użyciu [bluzki:](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux)
  * W przypadku maszyn wirtualnych z systemem Linux konto magazynu może być udostępniane i używane jako system plików, gdy używany jest sterownik wirtualnego systemu plików blobfuse.
  * Ta opcja ma tę zaletę, że jest bardzo opłacalne, ponieważ nie maszyny wirtualne są wymagane dla systemu plików, a blobfuse buforowanie na maszynach wirtualnych pozwala uniknąć wielokrotnego pobierania tych samych plików dla wielu zadań i zadań.  Przenoszenie danych jest również proste, ponieważ pliki są po prostu obiekty blob i standardowe interfejsy API i narzędzia, takie jak azcopy, mogą służyć do kopiowania pliku między lokalnym systemem plików a magazynem platformy Azure.
* Udział w systemie plików lub plikach:
  * W zależności od systemu operacyjnego maszyny Wirtualnej i wymagań dotyczących wydajności/skali opcje obejmują [usługę Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction), przy użyciu maszyny Wirtualnej z dołączonymi dyskami dla systemu plików NFS, przy użyciu wielu maszyn wirtualnych z dołączonymi dyskami dla rozproszonego systemu plików, takiego jak GlusterFS, lub przy użyciu oferty innej firmy.
  * [Avere Systems](https://www.averesystems.com/) jest teraz częścią firmy Microsoft i w niedalekiej przyszłości będzie mieć rozwiązania, które idealnie nadają się do renderowania na dużą skalę i wysokiej wydajności.  Rozwiązanie Avere umożliwi utworzenie pamięci podręcznej NFS lub SMB opartej na platformie Azure, która działa w połączeniu z magazynem obiektów blob lub lokalnymi urządzeniami NAS.
  * Za pomocą systemu plików pliki mogą być odczytywane lub zapisywane bezpośrednio do systemu plików lub mogą być kopiowane między systemem plików a maszynami wirtualnymi puli.
  * Udostępniony system plików umożliwia wykorzystanie dużej liczby zasobów współdzielonych między projektami i zadaniami, przy czym zadania renderowania uzyskują dostęp tylko do wymaganych obrażeń.

## <a name="using-azure-blob-storage"></a>Korzystanie z magazynu obiektów blob platformy Azure

Należy użyć konta magazynu obiektów blob lub konta magazynu ogólnego przeznaczenia w wersji 2.  Te dwa typy kont magazynu można skonfigurować ze znacznie wyższymi limitami w porównaniu do ogólnego przeznaczenia konta magazynu w wersji 1, jak opisano w [tym wpisie](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)w blogu.  Po skonfigurowaniu wyższe limity umożliwi znacznie lepszą wydajność i skalowalność, zwłaszcza gdy istnieje wiele maszyn wirtualnych puli uzyskujących dostęp do konta magazynu.

### <a name="copying-files-between-client-and-blob-storage"></a>Kopiowanie plików między magazynem klienta i obiektu blob

Aby skopiować pliki do i z usługi Azure Storage, można użyć różnych mechanizmów, w tym interfejsu API obiektu blob magazynu, [biblioteki przenoszenia danych usługi Azure Storage,](https://github.com/Azure/azure-storage-net-data-movement)narzędzia wiersza polecenia azcopy dla [systemu Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) lub [Linux,](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/)i [Eksploratora azure batch.](https://azure.github.io/BatchExplorer/)

Na przykład za pomocą azcopy wszystkie zasoby w folderze mogą być przenoszone w następujący sposób:


`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /Y`

Do kopiowania tylko zmodyfikowanych plików można użyć parametru /XO:

`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /XO /Y`

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopiowanie wejściowych plików zasobów z magazynu obiektów blob do maszyn wirtualnych puli partii

Istnieje kilka różnych podejść do kopiowania plików z najlepszym podejściem określonym przez rozmiar zasobów zadań.
Najprostszym rozwiązaniem jest skopiowanie wszystkich plików zasobów do puli maszyn wirtualnych dla każdego zadania:

* Jeśli istnieją pliki unikatowe dla zadania, ale są wymagane dla wszystkich zadań zadania, można określić [zadanie przygotowania zadania,](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask) aby skopiować wszystkie pliki.  Zadanie przygotowania zadania jest uruchamiane raz, gdy pierwsze zadanie zadania jest wykonywane na maszynie Wirtualnej, ale nie jest uruchamiane ponownie dla kolejnych zadań zadania.
* Należy określić [zadanie zwalniania zadania,](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask) aby usunąć pliki na zadanie po zakończeniu zadania; pozwoli to uniknąć dysku maszyny Wirtualnej coraz zapełnione przez wszystkie pliki zasobów zadania.
* Jeśli istnieje wiele zadań przy użyciu tych samych zasobów, tylko przyrostowe zmiany do zasobów dla każdego zadania, a następnie wszystkie pliki zasobów są nadal kopiowane, nawet jeśli tylko podzbiór zostały zaktualizowane.  Byłoby to nieefektywne, gdy istnieje wiele dużych plików zasobów.

Gdy pliki zasobów są ponownie używane między zadaniami, z tylko przyrostowe zmiany między zadaniami, a następnie bardziej wydajne, ale nieco bardziej zaangażowane podejście jest do przechowywania zasobów w folderze udostępnionym na maszynie Wirtualnej i synchronizacji zmienionych plików.

* Zadanie przygotowania zadania będzie wykonywać kopię przy użyciu azcopy z parametrem /XO do folderu udostępnionego maszyny Wirtualnej określonego przez AZ_BATCH_NODE_SHARED_DIR zmiennej środowiskowej.  Spowoduje to skopiowanie tylko zmienionych plików do każdej maszyny Wirtualnej.
* Należy pomyśleć, że należy podać rozmiar wszystkich zasobów, aby upewnić się, że zmieszczą się one na tymczasowym dysku maszyn wirtualnych puli.

Usługa Azure Batch ma wbudowaną obsługę kopiowania plików między kontem magazynu a maszynami wirtualnymi puli usług Batch.  [Pliki zasobów zadań](https://docs.microsoft.com/rest/api/batchservice/job/add#resourcefile) kopiują pliki z magazynu do puli maszyn wirtualnych i mogą być określone dla zadania przygotowania zadania.  Niestety, gdy istnieją setki plików, możliwe jest trafienie limitu i niepowodzenie zadań.  W przypadku dużej liczby zasobów zaleca się użycie wiersza polecenia azcopy w zadaniu przygotowania zadania, który może używać symboli wieloznacznych i nie ma limitu.

### <a name="copying-output-files-to-blob-storage-from-batch-pool-vms"></a>Kopiowanie plików wyjściowych do magazynu obiektów blob z maszyn wirtualnych puli partii

[Pliki wyjściowe](https://docs.microsoft.com/rest/api/batchservice/task/add#outputfile) mogą być używane pliki kopiowania z puli maszyny Wirtualnej do magazynu.  Jeden lub więcej plików można skopiować z maszyny Wirtualnej na określone konto magazynu po zakończeniu zadania.  Renderowane dane wyjściowe powinny być kopiowane, ale może być również pożądane do przechowywania plików dziennika.

## <a name="using-a-blobfuse-virtual-file-system-for-linux-vm-pools"></a>Korzystanie z wirtualnego systemu plików bluzki dla pul maszyn wirtualnych systemu Linux

Blobfuse to wirtualny sterownik systemu plików dla usługi Azure Blob Storage, który umożliwia dostęp do plików przechowywanych jako obiekty blob na koncie Magazynu za pośrednictwem systemu plików Linux.

Węzły puli można zainstalować system plików po uruchomieniu lub instalacji może się zdarzyć w ramach zadania przygotowania zadania — zadanie, które jest uruchamiane tylko wtedy, gdy pierwsze zadanie w zadaniu jest uruchamiane w węźle.  Blobfuse można skonfigurować tak, aby wykorzystywać zarówno ramdisk, jak i lokalny dysk SSD maszyn wirtualnych do buforowania plików, co znacznie zwiększy wydajność, jeśli wiele zadań w węźle uzyskuje dostęp do niektórych z tych samych plików.

[Przykładowe szablony są dostępne](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/vray/render-linux-with-blobfuse-mount) do uruchamiania autonomicznych renderów V-Ray przy użyciu systemu plików blobfuse i mogą być używane jako podstawa dla szablonów dla innych aplikacji.

### <a name="accessing-files"></a>Uzyskiwanie dostępu do plików

Zadania zadań określają ścieżki dla plików wejściowych i plików wyjściowych przy użyciu zainstalowanego systemu plików.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopiowanie wejściowych plików zasobów z magazynu obiektów blob do maszyn wirtualnych puli partii

Ponieważ pliki są po prostu obiekty blobs w usłudze Azure Storage, a następnie standardowe interfejsy API obiektów blob, narzędzia i interfejsy użytkownika mogą służyć do kopiowania plików między lokalnym systemem plików i magazynu obiektów blob; na przykład azcopy, Eksplorator magazynu, Eksplorator wsadowy itp.

## <a name="using-azure-files-with-windows-vms"></a>Korzystanie z plików platformy Azure z maszynami wirtualnymi systemu Windows

[Usługa Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) oferuje w pełni zarządzane udziały plików w chmurze, które są dostępne za pośrednictwem protokołu SMB.  Usługa Azure Files jest oparta na magazynie obiektów blob platformy Azure; jest [to opłacalne](https://azure.microsoft.com/pricing/details/storage/files/) i można skonfigurować za pomocą replikacji danych do innego regionu, tak globalnie nadmiarowe.  [Obiekty docelowe skalowania](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-files-scale-targets) powinny zostać sprawdzone, aby ustalić, czy usługa Azure Files powinna być używana, biorąc pod uwagę rozmiar puli prognoz i liczbę plików zasobów.

Istnieje wpis w [blogu](https://blogs.msdn.microsoft.com/windowsazurestorage/2014/05/26/persisting-connections-to-microsoft-azure-files/) i [dokumentacja](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) dotycząca sposobu instalowania udziału pliku usługi Azure.

### <a name="mounting-an-azure-files-share"></a>Instalowanie udziału w plikach azure

Aby użyć w partii, operacja instalacji musi być wykonywana za każdym razem, gdy zadanie jest uruchamiane, ponieważ nie jest możliwe utrwalenie połączenia między zadaniami.  Najprostszym sposobem, aby to zrobić, jest użycie cmdkey do utrwalania poświadczeń przy użyciu zadania uruchamiania w konfiguracji puli, a następnie instalowania udziału przed każdym zadaniem.

Przykład użycia cmdkey w szablonie puli (wysunął do użytku w pliku JSON) — należy zauważyć, że podczas oddzielania wywołania cmdkey od wywołania użycia sieci kontekst użytkownika dla zadania początkowego musi być taki sam, jak kontekst używany do uruchamiania zadań:

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

Zadania zadań określają ścieżki dla plików wejściowych i plików wyjściowych przy użyciu zamontowanego systemu plików, przy użyciu mapowanego dysku lub ścieżki UNC.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopiowanie wejściowych plików zasobów z magazynu obiektów blob do maszyn wirtualnych puli partii

Usługi Azure Files są obsługiwane przez wszystkie główne interfejsy API i narzędzia, które mają obsługę usługi Azure Storage; na przykład azcopy, Azure CLI, Storage Explorer, Azure PowerShell, Batch Explorer itp.

[Usługa Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) jest dostępna do automatycznej synchronizacji plików między lokalnym systemem plików a udziałem plików platformy Azure.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat opcji magazynowania, zobacz szczegółową dokumentację:

* [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)
* [Bluzka](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux)
* [Pliki platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)
