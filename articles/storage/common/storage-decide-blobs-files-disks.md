---
title: Kiedy należy użyć obiektów blob Azure, Azure plików lub dysków Azure
description: Dowiedz się o różnych sposobach do przechowywania i udostępniania danych na platformie Azure, aby pomóc zdecydować technologii.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/28/2018
ms.author: tamram
ms.openlocfilehash: ded0884ff83cc214d78f65fed8cefa646f11d952
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Kiedy należy użyć obiektów blob Azure, Azure plików lub dysków Azure

Microsoft Azure udostępnia kilka funkcji w usłudze Azure Storage do przechowywania i uzyskiwania dostępu do danych w chmurze. W tym artykule omówiono plików Azure, obiekty BLOB i dysków i jest przeznaczony do pomaga wybrać między tymi funkcjami.

## <a name="scenarios"></a>Scenariusze

W poniższej tabeli porównano pliki, obiekty BLOB i dyski i przedstawiono przykładowe scenariusze odpowiednie dla każdego.

| Cecha | Opis | Kiedy stosować |
|--------------|-------------|-------------|
| **Usługa pliki Azure** | Udostępnia interfejs SMB, bibliotek klienckich i [interfejsu REST](/rest/api/storageservices/file-service-rest-api) umożliwiającą dostęp z dowolnego miejsca do przechowywanych plików. | Aby "przyrostu i przesunięcia" aplikacji w chmurze, które używa już system plików natywnych interfejsów API do udostępniania danych między nim a inne aplikacje działające na platformie Azure.<br/><br/>Chcesz przechowywać rozwoju i narzędzia debugowania, które muszą być dostępne z wielu maszyn wirtualnych. |
| **Azure Blobs** | Udostępnia biblioteki klienta i [interfejsu REST](/rest/api/storageservices/blob-service-rest-api) umożliwiająca danych bez struktury przechowywanych i dostępne w bardzo dużej skali w blokowych obiektów blob. | Ma aplikacji do obsługi przesyłania strumieniowego i scenariusze dostępie.<br/><br/>Chcesz można było uzyskać dostęp do danych aplikacji z dowolnego miejsca. |
| **Dysku systemu Azure** | Udostępnia biblioteki klienta i [interfejsu REST](/rest/api/compute/manageddisks/disks/disks-rest-api) umożliwiająca danych trwale przechowywane i uzyskać dostęp z dołączonego wirtualnego dysku twardego. | Chcesz przyrostu lub klawisz shift, aplikacje używające systemu plików natywnych interfejsów API do odczytywania i zapisywania danych na dyski stałe.<br/><br/>Chcesz przechowywać dane, które nie jest wymagane do jako dostępne spoza maszyny wirtualnej, do której jest dołączona dysku. |

## <a name="comparison-files-and-blobs"></a>Porównania: Pliki i obiekty BLOB

W poniższej tabeli porównano plików Azure z obiektami blob Azure.  
  
||||  
|-|-|-|  
|**Atrybut**|**Azure Blobs**|**Usługa pliki Azure**|  
|Opcje trwałości|LRS, ZRS, GRS, RA-GRS|LRS, ZRS, GRS|  
|Ułatwienia dostępu|Interfejsy API REST|Interfejsy API REST<br /><br /> Protokół SMB 2.1 i 3.0 protokołu SMB (systemu plików standardowych interfejsów API)|  
|Łączność|Interfejsy API REST — na całym świecie|Interfejsy API REST - na całym świecie<br /><br /> Protokół SMB 2.1--w obrębie regionu<br /><br /> Protokół SMB 3.0 — na całym świecie|  
|Punkty końcowe|`http://myaccount.blob.core.windows.net/mycontainer/myblob`|`\\myaccount.file.core.windows.net\myshare\myfile.txt`<br /><br /> `http://myaccount.file.core.windows.net/myshare/myfile.txt`|  
|Katalogi|Prosty obszar nazw|Obiekty katalogu true|  
|Uwzględniana wielkość liter nazwy|Uwzględnianie wielkości liter|Bez uwzględniania wielkości liter, ale w przypadku zachowania|  
|Pojemność|Do 500 TiB kontenerów|Udziały plików TiB 5|  
|Przepływność|Do 60 MiB/s dla blokowych obiektów blob|Do 60 MiB/s na jedną akcję|  
|Rozmiar obiektu|Do około 4,75 TiB dla blokowych obiektów blob|Maksymalnie 1 TiB dla każdego pliku|  
|Pojemność rachunku|Oparte na zapisanych bajtów|Na podstawie rozmiaru plików|  
|Biblioteki klienckie|Wiele języków|Wiele języków|  
  
## <a name="comparison-files-and-disks"></a>Porównania: Pliki i dyski

Usługa pliki Azure uzupełniają dysków Azure. Dysk może zostać dołączona tyko do jednej maszyny wirtualnej Azure naraz. Dyski są ustalonym formacie wirtualne dyski twarde, przechowywane jako stronicowe obiekty BLOB w magazynie Azure i są używane przez maszynę wirtualną do przechowywania danych trwałych. Udziały plików w plikach Azure można uzyskać w taki sam sposób jak dysk lokalny jest dostępny (przy użyciu systemu plików natywnych interfejsów API) i może być współużytkowana przez wiele maszyn wirtualnych.  
 
W poniższej tabeli porównano plików Azure w przypadku dysków Azure.  
 
||||  
|-|-|-|  
|**Atrybut**|**Dysku systemu Azure**|**Usługa pliki Azure**|  
|Zakres|Wyłącznie dla jednej maszyny wirtualnej|Dostępu współdzielonego między wieloma maszynami wirtualnymi|  
|Migawki i kopii|Yes|Nie|  
|Konfigurowanie|Połączone podczas uruchamiania maszyny wirtualnej|Połączone po uruchomieniu maszyny wirtualnej|  
|Authentication|Wbudowane|Skonfiguruj za pomocą polecenie net use|  
|Czyszczenie|Automatyczny|Ręczne|  
|Dostęp za pomocą usługi REST|Nie można uzyskać dostępu do plików w ramach dysku VHD|Możliwy jest przechowywana w udziale plików|  
|Maksymalny rozmiar|4 TiB dysku|Udział plików TiB 5 i 1 TiB plik w udziale|  
|Maksymalna liczba IOps 8KB|500 IOps|1000 IOps|  
|Przepływność|Do 60 MiB/s na dysk|Do 60 MiB/s dla udziału plików|  

## <a name="next-steps"></a>Kolejne kroki

Podczas podejmowania decyzji o sposób przechowywania i uzyskać dostępu do danych, należy również rozważyć koszty związane. Aby uzyskać więcej informacji, zobacz [cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).
  
Niektóre funkcje protokołu SMB nie mają zastosowania do chmury. Aby uzyskać więcej informacji, zobacz [funkcji nie są obsługiwane przez usługę Azure pliku](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).
  
Aby uzyskać więcej informacji dotyczących dysków, zobacz [Zarządzanie dyskami i obrazy](../../virtual-machines/windows/about-disks-and-vhds.md) i [jak dołączyć dysku danych do maszyny wirtualnej systemu Windows](../../virtual-machines/windows/attach-managed-disk-portal.md).
