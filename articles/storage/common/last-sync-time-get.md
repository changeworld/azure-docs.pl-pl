---
title: Sprawdź właściwość Czas ostatniej synchronizacji dla konta magazynu
titleSuffix: Azure Storage
description: Dowiedz się, jak sprawdzić właściwość **Czas ostatniej synchronizacji** dla konta magazynu replikowanego geograficznie. **Właściwość Czas ostatniej synchronizacji** wskazuje ostatni czas, przy którym wszystkie zapisy z regionu podstawowego zostały pomyślnie zapisane w regionie pomocniczym.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/16/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 3a406ce6db060b9ff5be7bcadecb6c7ff7e65a1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77165490"
---
# <a name="check-the-last-sync-time-property-for-a-storage-account"></a>Sprawdź właściwość Czas ostatniej synchronizacji dla konta magazynu

Podczas konfigurowania konta magazynu można określić, że dane są kopiowane do regionu pomocniczego, który znajduje się setki mil od regionu podstawowego. Replikacja geograficzna zapewnia trwałość danych w przypadku znacznej awarii w regionie podstawowym, takiej jak klęska żywiołowa. Jeśli dodatkowo włączysz dostęp do odczytu do regionu pomocniczego, dane pozostaną dostępne dla operacji odczytu, jeśli region podstawowy stanie się niedostępny. Można zaprojektować aplikację, aby płynnie przełączyć się do odczytu z regionu pomocniczego, jeśli region podstawowy nie odpowiada.

Magazyn geograficznie nadmiarowy (GRS) i magazyn geograficzno-strefowy (GZRS) (wersja zapoznawcza) replikują dane asynchronicznie do regionu pomocniczego. Aby uzyskać dostęp do odczytu do regionu pomocniczego, włącz magazyn geograficzny dostępu do odczytu (RA-GRS) lub magazyn geograficzny dostępu do odczytu (RA-GZRS). Aby uzyskać więcej informacji na temat różnych opcji nadmiarowości oferowanych przez usługę Azure Storage, zobacz [Nadmiarowość usługi Azure Storage.](storage-redundancy.md)

W tym artykule opisano sposób sprawdzania właściwości **Ostatni czas synchronizacji** dla konta magazynu, aby można było ocenić wszelkie rozbieżności między regionami podstawowymi i pomocniczymi.

## <a name="about-the-last-sync-time-property"></a>Informacje o właściwości Czas ostatniej synchronizacji

Ponieważ replikacja geograficzna jest asynchroniczna, możliwe jest, że dane zapisane w regionie podstawowym nie zostały jeszcze zapisane w regionie pomocniczym w momencie wystąpienia awarii. **Właściwość Czas ostatniej synchronizacji** wskazuje, że ostatni raz dane z regionu podstawowego zostały pomyślnie zapisane w regionie pomocniczym. Wszystkie zapisy dokonane w regionie podstawowym przed ostatnim czasem synchronizacji są dostępne do odczytu z lokalizacji pomocniczej. Zapisy wprowadzone do regionu podstawowego po ostatniej właściwości czasu synchronizacji może lub nie mogą być jeszcze dostępne dla odczytów.

**Właściwość Czas ostatniej synchronizacji** jest wartością daty/godziny GMT.

## <a name="get-the-last-sync-time-property"></a>Pobierz właściwość Czas ostatniej synchronizacji

Za pomocą programu PowerShell lub narzędzia Azure CLI można pobrać wartość właściwości **Czas ostatniej synchronizacji.**

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Aby uzyskać czas ostatniej synchronizacji konta magazynu za pomocą programu PowerShell, zainstaluj moduł w wersji zapoznawczej usługi Azure Storage, który obsługuje uzyskiwanie statystyk replikacji geograficznej. Na przykład:

```powershell
Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
```

Następnie sprawdź konto magazynu **GeoReplicationStats.LastSyncTime** właściwości. Pamiętaj, aby zastąpić wartości zastępcze własnymi wartościami:

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uzyskać czas ostatniej synchronizacji dla konta magazynu za pomocą interfejsu wiersza polecenia platformy Azure, sprawdź właściwość **geoReplicationStats.lastSyncTime** konta magazynu. Użyj `--expand` parametru, aby zwrócić wartości właściwości zagnieżdżonych w obszarze **geoReplicationStats**. Pamiętaj, aby zastąpić wartości zastępcze własnymi wartościami:

```azurecli-interactive
$lastSyncTime=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --expand geoReplicationStats \
    --query geoReplicationStats.lastSyncTime \
    --output tsv)
```

---

## <a name="see-also"></a>Zobacz też

- [Nadmiarowość usługi Azure Storage](storage-redundancy.md)
- [Zmienianie opcji nadmiarowości dla konta magazynu](redundancy-migration.md)
- [Projektowanie aplikacji o wysokiej dostępności przy użyciu pamięci masowej geograficznej z dostępem do odczytu](storage-designing-ha-apps-with-ragrs.md)