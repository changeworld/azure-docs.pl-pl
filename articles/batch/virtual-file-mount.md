---
title: Instalowanie wirtualnego systemu plików w puli — usługa Azure Batch | Dokumenty firmy Microsoft
description: Dowiedz się, jak zainstalować wirtualny system plików w puli wsadowej.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: labrenne
ms.openlocfilehash: bdf0b3bfc955d8a2e2ce1b363c8699ca719b957c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919009"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Instalowanie wirtualnego systemu plików w puli wsadowej

Usługa Azure Batch obsługuje teraz montaż magazynu w chmurze lub zewnętrznego systemu plików w węzłach obliczeniowych systemu Windows lub Linux w pulach usług Batch. Gdy węzeł obliczeniowy łączy pulę, wirtualny system plików jest instalowany i traktowany jako dysk lokalny w tym węźle. Można zainstalować systemy plików, takie jak Pliki Azure, Magazyn obiektów Blob azure, System plików sieciowych (NFS), w tym [pamięć podręczną Avere vFXT](../avere-vfxt/avere-vfxt-overview.md)lub Wspólny internetowy system plików (CIFS).

W tym artykule dowiesz się, jak zainstalować wirtualny system plików na puli węzłów obliczeniowych przy użyciu [biblioteki zarządzania wsadem dla platformy .NET](https://docs.microsoft.com/dotnet/api/overview/azure/batch?view=azure-dotnet).

> [!NOTE]
> Montaż wirtualnego systemu plików jest obsługiwany w pulach partii utworzonych w dniu lub po 2019-08-19. Pule wsadowe utworzone przed 2019-08-19 nie obsługują tej funkcji.
> 
> Interfejsy API do montażu systemów plików w węźle obliczeniowym są częścią biblioteki [Batch .NET.](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch?view=azure-dotnet)

## <a name="benefits-of-mounting-on-a-pool"></a>Zalety montażu na basenie

Montaż systemu plików do puli, zamiast zezwalać zadaniam na pobieranie własnych danych z dużego zestawu danych, ułatwia i jest bardziej wydajny w przypadku zadań dostęp do niezbędnych danych.

Należy wziąć pod uwagę scenariusz z wielu zadań wymagających dostępu do wspólnego zestawu danych, takich jak renderowanie filmu. Każde zadanie renderuje jedną lub więcej klatek naraz z plików sceny. Dzięki zamontowaniu dysku zawierającego pliki sceny węzełom obliczeniowym łatwiej jest uzyskać dostęp do udostępnionych danych. Ponadto podstawowy system plików można wybrać i skalować niezależnie na podstawie wydajności i skali (przepływność i Usługi We/Wy) wymaganych przez liczbę węzłów obliczeniowych równocześnie uzyskujących dostęp do danych. Na przykład rozproszona pamięć podręczna W pamięci [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) może służyć do obsługi dużych renderów w skali kinemtrów z tysiącami równoczesnych węzłów renderowania, uzyskując dostęp do danych źródłowych, które znajdują się lokalnie. Alternatywnie w przypadku danych, które już znajdują się w magazynie obiektów Blob opartych na chmurze, [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) może służyć do instalowania tych danych jako lokalnego systemu plików. Blobfuse jest dostępny tylko w węzłach systemu Linux, jednak [usługa Azure Files](https://azure.microsoft.com/blog/a-new-era-for-azure-files-bigger-faster-better/) zapewnia podobny przepływ pracy i jest dostępna zarówno w systemach Windows, jak i Linux.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>Instalowanie wirtualnego systemu plików na basenie  

Montaż wirtualnego systemu plików w puli sprawia, że system plików jest dostępny dla każdego węzła obliczeniowego w puli. System plików jest konfigurowany, gdy węzeł obliczeniowy łączy pulę lub gdy węzeł zostanie ponownie uruchomiony lub ponownie zaimagedyzem.

Aby zainstalować system plików na basenie, utwórz `MountConfiguration` obiekt. Wybierz obiekt, który pasuje do `AzureBlobFileSystemConfiguration` `AzureFileShareConfiguration`wirtualnego systemu plików: , , `NfsMountConfiguration`lub `CifsMountConfiguration`.

Wszystkie obiekty konfiguracji instalacji wymagają następujących parametrów podstawowych. Niektóre konfiguracje instalacji mają parametry specyficzne dla używanego systemu plików, które zostały omówione bardziej szczegółowo w przykładach kodu.

- **Nazwa konta lub źródło**: Aby zainstalować wirtualny udział plików, potrzebna jest nazwa konta magazynu lub jego źródła.
- **Względna ścieżka instalacji lub źródło:** Lokalizacja systemu plików zamontowanego `fsmounts` w węźle obliczeniowym względem standardowego katalogu dostępnego w węźle za pośrednictwem . `AZ_BATCH_NODE_MOUNTS_DIR` Dokładna lokalizacja różni się w zależności od systemu operacyjnego używanego w węźle. Na przykład fizyczna lokalizacja w węźle Ubuntu jest mapowana na `mnt\batch\tasks\fsmounts`węzeł `mnt\resources\batch\tasks\fsmounts`CentOS, a na węzeł CentOS jest mapowana na .
- **Opcje montażu lub opcje bluzki**: Opcje te opisują określone parametry montażu systemu plików.

Po `MountConfiguration` utworzeniu obiektu przypisz obiekt `MountConfigurationList` do właściwości podczas tworzenia puli. System plików jest instalowany, gdy węzeł łączy pulę lub gdy węzeł zostanie ponownie uruchomiony lub ponownie zaimagedyzem.

Po zamontowaniu systemu plików `AZ_BATCH_NODE_MOUNTS_DIR` tworzona jest zmienna środowiskowa, która wskazuje lokalizację zainstalowanych systemów plików, a także pliki dziennika, które są przydatne do rozwiązywania problemów i debugowania. Pliki dziennika są wyjaśnione bardziej szczegółowo w sekcji [Diagnozowanie błędów instalacji.](#diagnose-mount-errors)  

> [!IMPORTANT]
> Maksymalna liczba zainstalowanych systemów plików na puli wynosi 10. Aby uzyskać szczegółowe informacje i inne [limity, zobacz Przydziały usług wsadowych i limity.](batch-quota-limit.md#other-limits)

## <a name="examples"></a>Przykłady

Poniższe przykłady kodu pokazują, że montowanie różnych udziałów plików do puli węzłów obliczeniowych.

### <a name="azure-files-share"></a>Udział w usłudze Azure Files

Usługa Azure Files to standardowa oferta systemu plików w chmurze platformy Azure. Aby dowiedzieć się więcej o tym, jak uzyskać dowolny z parametrów w przykładzie kodu konfiguracji instalacji, zobacz [Korzystanie z udziału w usłudze Azure Files](../storage/files/storage-how-to-use-files-windows.md).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureFileShareConfiguration = new AzureFileShareConfiguration
            {
                AccountName = "AccountName",
                AzureFileUrl = "AzureFileShareUrl",
                AccountKey = "StorageAccountKey",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-file-system"></a>System plików obiektów Blob platformy Azure

Inną opcją jest użycie usługi Azure Blob storage za pomocą [bluzki](../storage/blobs/storage-how-to-mount-container-linux.md). Montaż systemu plików obiektów `AccountKey` blob wymaga lub `SasKey` dla konta magazynu. Aby uzyskać informacje na temat uzyskiwania tych kluczy, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../storage/common/storage-account-keys-manage.md)lub Korzystanie z [sygnatur dostępu współdzielonego (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md). Aby uzyskać więcej informacji na temat używania bluzki, zobacz bluzkę [Rozwiązywanie problemów z często zadawanymi pytaniami](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ). Aby uzyskać domyślny dostęp do katalogu zainstalowanego w bluzce, uruchom zadanie jako **administrator**. Blobfuse montuje katalog w przestrzeni użytkownika, a podczas tworzenia puli jest on zamontowany jako katalog główny. W Systemie Linux wszystkie zadania **administratora** są główne. Wszystkie opcje modułu FUSE opisano na [stronie referencyjnej FUSE](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html).

Oprócz przewodnika po rozwiązywaniu problemów, problemy z gitHub w repozytorium blobfuse są pomocnym sposobem sprawdzania bieżących problemów i rozwiązań blobfuse. Aby uzyskać więcej informacji, zobacz [problemy z blobfuse](https://github.com/Azure/azure-storage-fuse/issues).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureBlobFileSystemConfiguration = new AzureBlobFileSystemConfiguration
            {
                AccountName = "StorageAccountName",
                ContainerName = "containerName",
                AccountKey = "StorageAccountKey",
                SasKey = "",
                RelativeMountPath = "RelativeMountPath",
                BlobfuseOptions = "-o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120 "
            },
        }
    }
}
```

### <a name="network-file-system"></a>Sieciowy system plików

Sieciowe systemy plików (NFS) można również zamontować w węzłach puli, dzięki czemu tradycyjne systemy plików mogą być łatwo dostępne przez węzły usługi Azure Batch. Może to być pojedynczy serwer systemu plików NFS wdrożony w chmurze lub lokalny serwer systemu plików NFS, do który uzyskuje się dostęp za pośrednictwem sieci wirtualnej. Alternatywnie skorzystać z [Avere vFXT rozproszonej](../avere-vfxt/avere-vfxt-overview.md) pamięci podręcznej rozwiązanie, które zapewnia bezproblemową łączność z magazynu lokalnego, odczytywanie danych na żądanie w pamięci podręcznej i zapewnia wysoką wydajność i skalowanie do węzłów obliczeniowych opartych na chmurze.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            NfsMountConfiguration = new NFSMountConfiguration
            {
                Source = "source",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "options ver=1.0"
            },
        }
    }
}
```

### <a name="common-internet-file-system"></a>Wspólny internetowy system plików

Typowe internetowe systemy plików (CIFS) można również zamontować w węzłach puli, dzięki czemu tradycyjne systemy plików mogą być łatwo dostępne przez węzły usługi Azure Batch. CIFS to protokół udostępniania plików, który zapewnia otwarty i wieloplatformowy mechanizm żądania plików i usług serwera sieciowego. System CIFS jest oparty na ulepszonej wersji protokołu Microsoft Server Message Block (SMB) do udostępniania plików internetowych i intranetowych i służy do instalowania zewnętrznych systemów plików w węzłach systemu Windows. Aby dowiedzieć się więcej o SMB, zobacz [Serwer plików i SMB](https://docs.microsoft.com/windows-server/storage/file-server/file-server-smb-overview).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            CifsMountConfiguration = new CIFSMountConfiguration
            {
                Username = "StorageAccountName",
                RelativeMountPath = "cifsmountpoint",
                Source = "source",
                Password = "StorageAccountKey",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,serverino"
            },
        }
    }
}
```

## <a name="diagnose-mount-errors"></a>Diagnozowanie błędów instalacji

Jeśli konfiguracja instalacji nie powiedzie się, węzeł obliczeniowy w puli zakończy się niepowodzeniem, a stan węzła stanie się bezużyteczny. Aby zdiagnozować błąd konfiguracji [`ComputeNodeError`](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) instalacji, sprawdź właściwość, aby uzyskać szczegółowe informacje na temat błędu.

Aby uzyskać pliki dziennika do debugowania, użyj `*.log` [OutputFiles](batch-task-output-files.md) do przekazania plików. Pliki `*.log` zawierają informacje o instalacji `AZ_BATCH_NODE_MOUNTS_DIR` systemu plików w lokalizacji. Pliki dziennika instalacji `<type>-<mountDirOrDrive>.log` mają format: dla każdego instalacji. Na przykład `cifs` instalacja w katalogu `test` instalacji o nazwie będzie `cifs-test.log`miała plik dziennika instalacji o nazwie: .

## <a name="supported-skus"></a>Obsługiwane jednostki SKU

| Wydawca | Oferta | SKU | Udział plików platformy Azure | Bluzka | Mocowanie NFS | Mocowanie CIFS |
|---|---|---|---|---|---|---|
| partia | renderowanie-centos73 | Renderowania | :heavy_check_mark: <br>Uwaga: Kompatybilny z CentOS 7.7</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canonical | UbuntuServer | 16.04-LTS, 18.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ ( Credativ ) | Debian | 8| :heavy_check_mark: | :x: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ ( Credativ ) | Debian | 9 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-reklamy | linux-data-science-vm | linuxdsvm | :heavy_check_mark: <br>Uwaga: Kompatybilny z CentOS 7.4. </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-partia | centos-kontener | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-partia | centos-kontener-rdma | 7.4 | :heavy_check_mark: <br>Uwaga: Obsługuje A_8 lub 9 pamięci masowej</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-partia | ubuntu-server-container | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-dsvm | linux-data-science-vm-ubuntu | linuxdsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS-HPC | 7.4, 7.3, 7.1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oracle-Linux | 7.6 | :x: | :x: | :x: | :x: |
| Windows | WindowsServer | 2012, 2016, 2019 | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o instalowaniu udziału usługi Azure Files w systemie [Windows](../storage/files/storage-how-to-use-files-windows.md) lub [Linux](../storage/files/storage-how-to-use-files-linux.md).
- Dowiedz się więcej o używaniu i montażu wirtualnych systemów plików [blobfuse.](https://github.com/Azure/azure-storage-fuse)
- Zobacz [Omówienie sieciowego systemu plików,](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview) aby dowiedzieć się więcej o systemie plików NFS i jego aplikacjach.
- Więcej informacji na temat protokołu CIFS można znaleźć [w omówienie protokołu SMB firmy Microsoft i protokołu CIFS.](https://docs.microsoft.com/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)
