---
title: Podejmowanie decyzji o tym, kiedy korzystać z obiektów blob platformy Azure, Azure Files lub dysków platformy Azure
description: Dowiedz się więcej na temat różnych sposobów przechowywania i uzyskiwania dostępu do danych na platformie Azure, aby ułatwić podjęcie decyzji, która technologia ma być używana.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 4b1a42e25a6d8c7b4a3c24dffcb858ffe63dd10b
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671048"
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Podejmowanie decyzji o tym, kiedy korzystać z obiektów blob platformy Azure, Azure Files lub dysków platformy Azure

Microsoft Azure udostępnia kilka funkcji usługi Azure Storage do przechowywania danych w chmurze i uzyskiwania do nich dostępu. W tym artykule omówiono Azure Files, obiekty blob i dyski, które ułatwiają wybór między tymi funkcjami.

## <a name="scenarios"></a>Scenariusze

Poniższa tabela zawiera porównanie plików, obiektów blob i dysków oraz przykładowe scenariusze odpowiednie dla każdego z nich.

| Cecha | Opis | Kiedy stosować |
|--------------|-------------|-------------|
| **Usługa pliki Azure** | Udostępnia interfejs SMB, biblioteki klienta i [Interfejs REST](/rest/api/storageservices/file-service-rest-api) , który umożliwia dostęp z dowolnego miejsca do przechowywanych plików. | Chcesz "podnieść i przenieść" aplikację do chmury, która korzysta już z natywnych interfejsów API systemu plików do udostępniania danych między nimi a innymi aplikacjami uruchomionymi na platformie Azure.<br/><br/>Chcesz przechowywać narzędzia do tworzenia i debugowania, które muszą być dostępne z wielu maszyn wirtualnych. |
| **Obiekty blob platformy Azure** | Zapewnia biblioteki klienckie i [Interfejs REST](/rest/api/storageservices/blob-service-rest-api) , który umożliwia przechowywanie i dostęp do danych bez struktury w dużej skali w blokowych obiektach Blob.<br/><br/>Program obsługuje również [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) w przypadku rozwiązań do analizy danych Big Data w przedsiębiorstwie. | Chcesz, aby aplikacja obsługiwała scenariusze przesyłania strumieniowego i dostępu losowego.<br/><br/>Chcesz mieć możliwość dostępu do danych aplikacji z dowolnego miejsca.<br/><br/>Chcesz utworzyć przedsiębiorstwo Data Lake na platformie Azure i przeprowadzić analizę danych Big Data. |
| **Dyski platformy Azure** | Zapewnia biblioteki klienckie i [Interfejs REST](/rest/api/compute/manageddisks/disks/disks-rest-api) , który umożliwia trwałe przechowywanie danych i uzyskiwanie do nich dostępu z dołączonego wirtualnego dysku twardego. | Chcesz podnieść i przenieść aplikacje, które używają natywnych interfejsów API systemu plików do odczytu i zapisu danych na dyskach trwałych.<br/><br/>Chcesz przechowywać dane, które nie są wymagane, aby można było uzyskać do nich dostęp spoza maszyny wirtualnej, do której dysk jest podłączony. |


## <a name="next-steps"></a>Następne kroki

Przy podejmowaniu decyzji o sposobie przechowywania danych i uzyskiwania do nich dostępu należy również wziąć pod uwagę koszty związane z nimi. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).
  
Niektóre funkcje protokołu SMB nie mają zastosowania do chmury. Aby uzyskać więcej informacji, zobacz [funkcje nieobsługiwane przez usługę plików platformy Azure](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).
 
Aby uzyskać więcej informacji na temat obiektów blob platformy Azure, zobacz artykuł [co to jest usługa Azure Blob Storage?](../blobs/storage-blobs-overview.md).

Aby uzyskać więcej informacji na temat Disk Storage, zobacz [wprowadzenie do usługi Managed disks](../../virtual-machines/windows/managed-disks-overview.md).

Aby uzyskać więcej informacji na temat Azure Files, zapoznaj się z artykułem [Planowanie wdrożenia Azure Files](../files/storage-files-planning.md).