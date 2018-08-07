---
title: Kiedy używać obiektów blob platformy Azure, Azure Files lub Azure Disks
description: Poznaj różne sposoby przechowywania i dostępu do danych na platformie Azure, aby ułatwić określenie technologii.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/28/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 3f2609ea57ea5a5a0cce2544a1031c55199d137b
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39531351"
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Kiedy używać obiektów blob platformy Azure, Azure Files lub Azure Disks

Microsoft Azure oferuje kilka funkcji w usłudze Azure Storage do przechowywania i uzyskiwania dostępu do danych w chmurze. Ten artykuł dotyczy usługi Azure Files, obiekty BLOB i dyski i zaprojektowano, aby ułatwić wybór między tymi funkcjami.

## <a name="scenarios"></a>Scenariusze

Poniższa tabela porównuje pliki, obiekty BLOB i dyski oraz przedstawiono przykładowe scenariusze odpowiednie dla każdego.

| Cecha | Opis | Kiedy stosować |
|--------------|-------------|-------------|
| **Usługa pliki Azure** | Udostępnia interfejs dla protokołu SMB, bibliotek klienckich i [interfejsu REST](/rest/api/storageservices/file-service-rest-api) umożliwiającą dostęp z dowolnego miejsca do przechowywanych plików. | Chcesz metodą "lift and shift" aplikacji w chmurze, która już korzysta z systemu plików natywnych interfejsów API do udostępniania danych pomiędzy tym programem i innych aplikacji działających na platformie Azure.<br/><br/>Chcesz przechowywać programowania i debugowania narzędzi, które muszą być dostępne z wielu maszyn wirtualnych. |
| **Obiekty BLOB platformy Azure** | Zawiera biblioteki klienckie i [interfejsu REST](/rest/api/storageservices/blob-service-rest-api) umożliwiająca pozbawionych struktury danych są przechowywane i używane w bardzo dużej skali w blokowych obiektach blob. | Chcesz aplikację do obsługi przesyłania strumieniowego i scenariuszy dostępu swobodnego.<br/><br/>Chcesz można było uzyskać dostęp do danych aplikacji z dowolnego miejsca. |
| **Dyski platformy Azure** | Zawiera biblioteki klienckie i [interfejsu REST](/rest/api/compute/manageddisks/disks/disks-rest-api) , umożliwia danych można trwale przechowywane i udostępniane z dołączonego wirtualnego dysku twardego. | Chcesz lift- and -shift aplikacje, które używają interfejsów API systemu plików natywnej do odczytu i zapisu danych na stałych dyskach.<br/><br/>Chcesz przechowywać dane, które nie są wymagane były dostępne z zewnątrz maszyny wirtualnej, do której jest dołączony dysk. |

## <a name="comparison-files-and-blobs"></a>Porównania: Pliki i obiekty BLOB

W poniższej tabeli porównano usługi Azure Files za pomocą obiektów blob platformy Azure.  
  
||||  
|-|-|-|  
|**Atrybut**|**Obiekty BLOB platformy Azure**|**Usługa pliki Azure**|  
|Opcje trwałości|LRS, ZRS, GRS, RA-GRS|LRS, ZRS, GRS|  
|Ułatwienia dostępu|Interfejsy API REST|Interfejsy API REST<br /><br /> Protokół SMB 2.1 i protokołu SMB 3.0 (system standardowych plikowych interfejsów API)|  
|Łączność|Interfejsy API REST — na całym świecie|Interfejsy API REST — na całym świecie<br /><br /> Protokół SMB 2.1--w obrębie regionu<br /><br /> Protokół SMB 3.0 — na całym świecie|  
|Punkty końcowe|`http://myaccount.blob.core.windows.net/mycontainer/myblob`|`\\myaccount.file.core.windows.net\myshare\myfile.txt`<br /><br /> `http://myaccount.file.core.windows.net/myshare/myfile.txt`|  
|Katalogi|Prosty obszar nazw|Obiekty katalogu true|  
|Wielkość liter w nazwach|Uwzględnianie wielkości liter|Przypadek jest rozróżniana wielkość liter, ale zachowanie wielkości liter|  
|Pojemność|Maks. 500 TiB kontenerów|Udziały plików w usłudze 5 TiB|  
|Przepływność|Maksymalnie 60 MiB/s dla blokowych obiektów blob|Maksymalnie 60 MiB/s na jedną akcję|  
|Rozmiar obiektu|Maksymalnie o 4.75 TiB dla blokowych obiektów blob|Do 1 TiB na plik|  
|Billed pojemności|Oparte na Bajty zapisane|Na podstawie rozmiaru plików|  
|Biblioteki klienta|Wiele języków|Wiele języków|  
  
## <a name="comparison-files-and-disks"></a>Porównania: Pliki i dyski

Usługa Azure Files uzupełniają dysków platformy Azure. Dysk może być dołączony tylko do jednej maszyny wirtualnej platformy Azure w danym momencie. Dyski są wirtualnymi dyskami twardymi stałym formacie przechowywane jako stronicowe obiekty BLOB w usłudze Azure Storage i są używane przez maszynę wirtualną do przechowywania danych trwałych. Udziały plików w usłudze Azure Files jest możliwy w taki sam sposób, zgodnie z dysku lokalnego odbywa się (przy użyciu systemu plików natywnych interfejsów API) i mogą być współużytkowane przez wiele maszyn wirtualnych.  
 
W poniższej tabeli porównano usługi Azure Files z usługi Azure Disks.  
 
||||  
|-|-|-|  
|**Atrybut**|**Dyski platformy Azure**|**Usługa pliki Azure**|  
|Zakres|Wyłącznie dla jednej maszyny wirtualnej|Dostępu współdzielonego między wieloma maszynami wirtualnymi|  
|Migawki i skopiuj|Yes|Yes|  
|Konfigurowanie|Połączony podczas uruchamiania maszyny wirtualnej|Połączone po przejściu maszyny wirtualnej została uruchomiona.|  
|Authentication|Wbudowane|Konfigurowanie przy użyciu netto|  
|Czyszczenie|Automatyczny|Ręczne|  
|Dostęp przy użyciu usługi REST|Nie można uzyskać dostępu do plików w ramach dysku VHD|Przechowywana w udziale plików jest możliwy|  
|Maksymalny rozmiar|4 TiB dysków|Udziału plików w usłudze 5 TiB i plików w udziale: 1 TiB|  
|Maks. IOps 8KB|500 operacji We/Wy|1000 operacji We/Wy|  
|Przepływność|Maksymalnie 60 MiB/s na dysk|Maksymalnie 60 MiB/s na jeden udział plików|  

## <a name="next-steps"></a>Kolejne kroki

Podczas podejmowania decyzji dotyczących jak Twoje dane są przechowywane i udostępniane, należy również rozważyć koszty związane. Aby uzyskać więcej informacji, zobacz [cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).
  
Niektóre funkcje protokołu SMB nie mają zastosowania do chmury. Aby uzyskać więcej informacji, zobacz [funkcji nie są obsługiwane przez usługę Azure File](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).
  
Aby uzyskać więcej informacji o dyskach, zobacz [Zarządzanie dyskami i obrazami](../../virtual-machines/windows/about-disks-and-vhds.md) i [jak dołączyć dysk danych do maszyny wirtualnej Windows](../../virtual-machines/windows/attach-managed-disk-portal.md).
