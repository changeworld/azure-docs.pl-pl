---
title: Przenoszenie magazynu mainframe do usługi Azure Storage
description: Masowo skalowalne zasoby magazynu platformy Azure mogą pomóc organizacjom opartym na komputerach typu mainframe w migracji i modernizacji aplikacji IBM z14.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: storage
ms.openlocfilehash: 86419811cdf2c11204caae0ca5bf6f65fba063d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76288918"
---
# <a name="move-mainframe-storage-to-azure"></a>Przenoszenie magazynu mainframe na platformę Azure

Aby uruchamiać obciążenia komputerów mainframe na platformie Microsoft Azure, musisz wiedzieć, jak możliwości komputera mainframe są porównywane z platformą Azure. Masowo skalowalne zasoby magazynu mogą pomóc organizacjom rozpocząć modernizację bez porzucania aplikacji, na których polegają.

Platforma Azure zapewnia funkcje podobne do modelu mainframe i pojemność pamięci masowej, która jest porównywalna z systemami ibm z14 (najbardziej aktualny model w tym piśmie). W tym artykule dowiesz się, jak uzyskać porównywalne wyniki na platformie Azure.

## <a name="mainframe-storage-at-a-glance"></a>Pamięć masowa mainframe w skrócie

Ibm mainframe charakteryzuje pamięć masową na dwa sposoby. Pierwszym z nich jest urządzenie pamięci masowej z dostępem bezpośrednim (DASD). Drugi to magazyn sekwencyjny. Do zarządzania magazynem komputer mainframe udostępnia podsystem zarządzania magazynowaniem obiektów danych (DFSMS). Zarządza dostępem do danych do różnych urządzeń pamięci masowej.

[DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device) odnosi się do oddzielnego urządzenia do dodatkowego (nie w pamięci) magazynu, który umożliwia unikatowy adres do bezpośredniego dostępu do danych. Pierwotnie termin DASD odnosił się do wirujących dysków, bębnów magnetycznych lub komórek danych. Jednak teraz termin ten może mieć również zastosowanie do półprzewodnikowych urządzeń pamięci masowej (SSD), sieci magazynowania (SAN), sieciowej pamięci masowej (NAS) i napędów optycznych. Na potrzeby tego dokumentu dasd odnosi się do dysków wirujących, sanów san i dysków SSD.

W przeciwieństwie do pamięci DASD sekwencyjne przechowywanie na mainframe odnosi się do urządzeń, takich jak napędy taśmowe, gdzie dane są dostępne od punktu wyjścia, a następnie odczytywane lub zapisywane w wierszu.

Urządzenia pamięci masowej są zazwyczaj podłączane za pomocą połączenia światłowodowego (FICON) lub są dostępne bezpośrednio na magistrali IO komputera mainframe za pomocą [hipersockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm), technologii IBM do szybkiej komunikacji między partycjami na serwerze z hipernadzorcą.

Większość systemów mainframe dzieli pamięć masową na dwa typy:

- Do codziennych operacji potrzebna jest *pamięć masowa online* (znana również jako pamięć gorąca). W tym celu zwykle używany jest magazyn DASD. Jednak sekwencyjne przechowywanie, takie jak codzienne kopie zapasowe taśmy (logiczne lub fizyczne), może być również używane w tym celu.

- *Przechowywanie archiwów* (znane również jako chłodnia) nie jest gwarantowane do zamontowania w danym momencie. Zamiast tego jest zamontowany i dostępny w razie potrzeby. Magazyn archiwum jest często implementowany przy użyciu sekwencyjnych kopii zapasowych na taśmach (logicznych lub fizycznych) do przechowywania.

## <a name="mainframe-versus-io-latency-and-iops"></a>Mainframe a opóźnienie we/wy i we/wy

Komputery mainframe są często używane w aplikacjach, które wymagają wysokiej wydajności we/wy i małych opóźnień we/wy. Mogą to zrobić za pomocą połączeń FICON z urządzeniami We/Wy i HiperSockets. Gdy HiperSockets są używane do łączenia aplikacji i urządzeń bezpośrednio do kanału IO komputera mainframe, można osiągnąć opóźnienie w mikrosekundach.

## <a name="azure-storage-at-a-glance"></a>Magazyn platformy Azure w skrócie

Opcje infrastruktury platformy Azure jako usługi[(IaaS)](https://azure.microsoft.com/overview/what-is-iaas/)dla magazynu zapewniają porównywalną pojemność komputera mainframe.

Firma Microsoft oferuje magazyn o wartości petabajtów dla aplikacji hostowanych na platformie Azure i masz kilka opcji magazynu. Obejmują one od magazynu SSD dla wysokiej wydajności do niedrogiego magazynu obiektów blob do masowego przechowywania i archiwów. Ponadto platforma Azure udostępnia opcję nadmiarowości danych dla magazynu — co wymaga więcej wysiłku, aby skonfigurować w środowisku mainframe.

Usługa Azure storage jest dostępna jako [dyski Azure Disks](/azure/virtual-machines/windows/managed-disks-overview), [Usługi Azure Files](/azure/storage/files/storage-files-introduction)i obiekty [blob platformy Azure,](/azure/storage/blobs/storage-blobs-overview) jak podsumowuje poniższa tabela. Dowiedz się więcej o [tym, kiedy używać każdego](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Typ</th><th>Opis</th><th>Użyj, gdy chcesz:</th></tr>
</thead>
<tbody>
<tr><td>Azure Files
</td>
<td>
Zapewnia interfejs SMB, biblioteki klienckie i interfejs <a href="https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api">REST,</a> który umożliwia dostęp z dowolnego miejsca do przechowywanych plików.
</td>
<td><ul>
<li>Podnieś i przenieś aplikację do chmury, gdy aplikacja używa natywnych interfejsów API systemu plików do udostępniania danych między nią a innymi aplikacjami uruchomionymi na platformie Azure.</li>
<li>Przechowywanie narzędzi deweloperskich i debugowania, które muszą być dostępne z wielu maszyn wirtualnych.</li>
</ul>
</td>
</tr>
<tr><td>Obiekty blob platformy Azure
</td>
<td>Udostępnia biblioteki klientów i interfejs <a href="https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api">REST,</a> który umożliwia przechowywanie danych bez struktury i uzyskiwanie do nich dostępu na masową skalę w blokowych obiektach blob. Obsługuje również <a href="/azure/storage/blobs/data-lake-storage-introduction">usługi Azure Data Lake Storage Gen2</a> dla rozwiązań do analizy dużych zbiorów danych dla przedsiębiorstw.
</td>
<td><ul>
<li>Obsługa scenariuszy przesyłania strumieniowego i dostępu losowego w aplikacji.</li>
<li>Mieć dostęp do danych aplikacji z dowolnego miejsca.</li>
<li>Tworzenie aplikacji enterprise data lake na platformie Azure i przeprowadzanie analizy dużych zbiorów danych.</li>
</ul></td>
</tr>
<tr><td>Azure Disks
</td>
<td>Udostępnia biblioteki klienckie i interfejs <a href="https://docs.microsoft.com/rest/api/compute/disks">REST,</a> który umożliwia trwałe przechowywanie danych i uzyskiwanie do nich dostępu z dołączonego wirtualnego dysku twardego.
</td>
<td><ul>
<li>Udźwignie i przesuń aplikacje korzystające z natywnych interfejsów API systemu plików do odczytu i zapisu danych na dyskach trwałych.</li>
<li>Przechowywanie danych, które nie są wymagane do uzyskania dostępu spoza maszyny Wirtualnej, do której jest dołączony dysk.</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Pamięć masowa azure hot (online) i cold (archiwum)

Typ magazynu dla danego systemu zależy od wymagań systemu, w tym rozmiaru magazynu, przepływności i usług We/Wy. W przypadku magazynu typu DASD na mainframe aplikacje na platformie Azure zazwyczaj używają magazynu dysku Azure Disks. W przypadku magazynu archiwum mainframe magazyn obiektów blob jest używany na platformie Azure.

Dyski SSD zapewniają najwyższą wydajność magazynu na platformie Azure. Dostępne są następujące opcje (od zapisu tego dokumentu):

| Typ         | Rozmiar           | Liczba operacji we/wy na sekundę                  |
|--------------|----------------|-----------------------|
| Dysk SSD w warstwie Ultra    | 4 GB do 64 TB  | Od 1200 do 160 000 we/wy |
| Dysk SSD w warstwie Premium  | 32 GB do 32 TB | Od 12 do 15 000 IOPS     |
| Dysk SSD w warstwie Standardowa | 32 GB do 32 TB | Od 12 do 2000 we/wy      |

Magazyn obiektów Blob zapewnia największą ilość magazynu na platformie Azure. Oprócz rozmiaru magazynu platforma Azure oferuje zarówno magazyn zarządzany, jak i niezarządzany. Dzięki zarządzanej przestrzeni dyskowej platforma Azure zajmuje się zarządzaniem podstawowymi kontami magazynu. W przypadku magazynu niezarządzanego użytkownik bierze odpowiedzialność za konfigurowanie kont magazynu platformy Azure o odpowiednim rozmiarze, aby spełnić wymagania dotyczące magazynu.

## <a name="next-steps"></a>Następne kroki

- [Migracja komputera mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Ponowne hostowanie pulpitu głównego na maszynach wirtualnych platformy Azure](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Przenoszenie obliczeń komputera mainframe na platformę Azure](mainframe-compute-Azure.md)
- [Decydowanie o tym, kiedy mają być używane obiekty blob azure, pliki platformy Azure lub dyski platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)
- [Standardowe dyski zarządzane SSD dla obciążeń maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)

### <a name="ibm-resources"></a>Zasoby IBM

- [Równoległy sysplex w ibm z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS i urządzenie sprzęgało: poza podstawami](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Tworzenie wymaganych użytkowników dla instalacji funkcji pureScale Db2](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt — polecenie Utwórz wystąpienie](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Rozwiązanie do klastrowej bazy danych Db2 pureScale](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Chmura microsoft azure dla instytucji rządowych dla aplikacji mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft i FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Więcej zasobów migracji

- [Przewodnik po wirtualnym centrum danych azure i przeniesieniu](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
