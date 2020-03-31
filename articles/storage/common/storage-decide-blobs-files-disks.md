---
title: Decydowanie o tym, kiedy mają być używane obiekty blob azure, pliki platformy Azure lub dyski platformy Azure
description: Dowiedz się więcej o różnych sposobach przechowywania danych na platformie Azure i uzyskiwania do nich dostępu, aby ułatwić ci podjęcie decyzji, której technologii użyć.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 4b1a42e25a6d8c7b4a3c24dffcb858ffe63dd10b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71671048"
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Decydowanie o tym, kiedy mają być używane obiekty blob azure, pliki platformy Azure lub dyski platformy Azure

Platforma Microsoft Azure udostępnia kilka funkcji usługi Azure Storage do przechowywania danych i uzyskiwania do nich dostępu w chmurze. W tym artykule omówiono usługi Azure Files, blobs, i dyski i został zaprojektowany, aby ułatwić wybór między tymi funkcjami.

## <a name="scenarios"></a>Scenariusze

W poniższej tabeli porównano pliki, obiekty blob i dyski i przedstawiono przykładowe scenariusze odpowiednie dla każdego z nich.

| Funkcja | Opis | Kiedy stosować |
|--------------|-------------|-------------|
| **Pliki platformy Azure** | Zapewnia interfejs SMB, biblioteki klienckie i [interfejs REST,](/rest/api/storageservices/file-service-rest-api) który umożliwia dostęp z dowolnego miejsca do przechowywanych plików. | Chcesz "podnieść i przesunąć" aplikację do chmury, która już używa natywnych interfejsów API systemu plików do udostępniania danych między nią a innymi aplikacjami działającymi na platformie Azure.<br/><br/>Chcesz przechowywać narzędzia deweloperskie i debugowania, które muszą być dostępne z wielu maszyn wirtualnych. |
| **Obiekty blob platformy Azure** | Udostępnia biblioteki klientów i [interfejs REST,](/rest/api/storageservices/blob-service-rest-api) który umożliwia przechowywanie danych bez struktury i uzyskiwanie do nich dostępu na masową skalę w blokowych obiektach blob.<br/><br/>Obsługuje również [usługi Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) dla rozwiązań do analizy dużych zbiorów danych dla przedsiębiorstw. | Chcesz, aby aplikacja obsługiwała scenariusze przesyłania strumieniowego i dostępu losowego.<br/><br/>Chcesz mieć dostęp do danych aplikacji z dowolnego miejsca.<br/><br/>Chcesz utworzyć enterprise data lake na platformie Azure i przeprowadzić analizę dużych zbiorów danych. |
| **Azure Disks** | Udostępnia biblioteki klienckie i [interfejs REST,](/rest/api/compute/manageddisks/disks/disks-rest-api) który umożliwia trwałe przechowywanie danych i uzyskiwanie do nich dostępu z dołączonego wirtualnego dysku twardego. | Chcesz podnieść i przesunąć aplikacje, które używają natywnych interfejsów API systemu plików do odczytu i zapisu danych na dyskach trwałych.<br/><br/>Chcesz przechowywać dane, które nie są wymagane do uzyskania dostępu spoza maszyny wirtualnej, do której jest dołączony dysk. |


## <a name="next-steps"></a>Następne kroki

Podejmując decyzje dotyczące sposobu przechowywania i uzyskiwania dostępu do danych, należy również wziąć pod uwagę związane z tym koszty. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).
  
Niektóre funkcje SMB nie mają zastosowania do chmury. Aby uzyskać więcej informacji, zobacz [Funkcje nieobsługiwały usługi Azure File](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).
 
Aby uzyskać więcej informacji na temat obiektów blob platformy Azure, zobacz nasz artykuł, [Co to jest magazyn obiektów Blob platformy Azure?](../blobs/storage-blobs-overview.md).

Aby uzyskać więcej informacji na temat magazynu dysków, zobacz nasze [Wprowadzenie do dysków zarządzanych](../../virtual-machines/windows/managed-disks-overview.md).

Aby uzyskać więcej informacji na temat usług Azure Files, zobacz nasz artykuł [Planowanie wdrożenia usługi Azure Files](../files/storage-files-planning.md).