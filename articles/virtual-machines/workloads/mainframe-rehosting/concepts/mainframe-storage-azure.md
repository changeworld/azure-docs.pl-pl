---
title: Przenieś magazyn komputera mainframe do usługi Azure Storage
description: Zasoby skalowalności usługi Azure storage może pomóc organizacjom mainframe migracji i zmodernizować IBM z14 aplikacji.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: storage
ms.openlocfilehash: dc78f87d9b47745119da91b8ed1f8f6c8572968c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65190440"
---
# <a name="move-mainframe-storage-to-azure"></a>Przenieś magazyn komputera mainframe na platformie Azure

Uruchamianie obciążeń mainframe na Microsoft Azure, musisz wiedzieć, jak porównanie możliwości usługi mainframe na platformie Azure. Wysoce skalowalny magazyn zasobów może pomóc organizacjom rozpocząć modernizowanie się bez porzucania aplikacji, które opierają się na.

System Azure oferuje funkcje podobne do komputera mainframe i pojemność magazynu, który jest porównywalna do systemów opartych na procesorze z14 IBM (najbardziej aktualną model na chwilę obecną). W tym artykule wyjaśniono, jak uzyskać porównywalnych wyników na platformie Azure.

## <a name="mainframe-storage-at-a-glance"></a>Magazyn komputera mainframe na pierwszy rzut oka

Mainframe firmy IBM charakteryzuje magazynu na dwa sposoby. Pierwsza to urządzenie magazynujące dostęp bezpośrednie (DASD). Druga to kolejny magazyn. Zarządzanie magazynem, mainframe udostępnia dane funkcji magazynu zarządzania podsystemu (DFSMS). Zarządza on dostęp do danych do różnych urządzeń magazynujących.

[DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device) odwołuje się do oddzielnych urządzeń zapasowy magazyn (nie. w pamięci), która umożliwia unikatowy adres służący do bezpośredniego dostępu do danych. Początkowo termin DASD stosowane do obrotowych dysków, bębny magnetyczne lub komórek danych. Jednak teraz termin można zastosować do urządzeń magazynujących półprzewodnikowych (SSD), obszar magazynu sieci SAN, dołączyć sieci, pamięci masowej (NAS) i stacje dysków optycznych. Do celów tego dokumentu DASD odnosi się do dysków, sieci SAN i dysków SSD obrotowych.

W przeciwieństwie do magazynu DASD sekwencyjne magazynu na mainframe odnosi się do urządzeń, takich jak stacje taśm, gdzie dane są dostępne z punktem początkowym, a następnie odczytywanych lub zapisywanych w wierszu.

Urządzenia magazynujące zazwyczaj są dołączone za pomocą połączenia światłowodowe (FICON) lub są dostępne bezpośrednio na mainframe we/wy Service bus przy użyciu [HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm), technologia firmy IBM o dużej szybkości komunikacji między partycjami na serwerze Funkcja hypervisor.

Większość systemów mainframe dzielenie dwa typy magazynu:

- *Magazyn online* (znany również jako magazynu gorącego) jest wymagany dla codziennych operacji. Magazyn DASD zwykle jest używany w tym celu. Jednak sekwencyjne magazynu, na przykład codziennych kopii zapasowych na taśmie (logiczny lub fizyczny), można również w tym celu.

- *Magazyn archiwum* (zimnego magazynu) nie musi być zainstalowany w danym momencie. Zamiast tego jest zainstalowany i dostępny zgodnie z potrzebami. Usługa Archive storage często jest implementowany przy użyciu kopii zapasowych na taśmie sekwencyjnego (logiczny lub fizyczny) dla magazynu.

## <a name="mainframe-versus-io-latency-and-iops"></a>Mainframe i czas oczekiwania operacji We/Wy oraz operacje We/Wy

Komputery mainframe są często używane w przypadku aplikacji wymagających wysokiej wydajności we/wy i małych opóźnień operacji We/Wy. Mogą one to robić za pomocą połączeń FICON z urządzeń We/Wy i HiperSockets. Gdy HiperSockets są używane do łączenia z aplikacjami i urządzeniami bezpośrednio do kanału We/Wy komputera mainframe, można osiągnąć opóźnienia w mikrosekundach.

## <a name="azure-storage-at-a-glance"></a>Usługa Azure storage w skrócie

Usługa Azure infrastruktury as-a-service ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)) opcje magazynowania zapewniają pojemność mainframe porównywalny.

Firma Microsoft oferuje petabajtów, przez które Magazyn dla aplikacji hostowanych na platformie Azure i masz kilka opcji magazynu. Te w zakresie od pamięci SSD o wysokiej wydajności do magazynu obiektów blob niskie koszty, pamięci masowej i archiwa. Ponadto platforma Azure udostępnia opcję nadmiarowości danych do przechowywania — coś, co zajmuje więcej nakładu pracy, aby skonfigurować w środowisku komputera mainframe.

Usługa Azure storage jest dostępna jako [Azure Disks](/azure/virtual-machines/windows/managed-disks-overview), [usługi Azure Files](/azure/storage/files/storage-files-introduction), i [obiektów blob platformy Azure](/azure/storage/blobs/storage-blobs-overview) jak Poniższa tabela zawiera podsumowanie. Dowiedz się więcej o [podczas korzystania z każdego](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Typ</th><th>Opis</th><th>Używaj w następujących sytuacjach:</th></tr>
</thead>
<tbody>
<tr><td>Azure Files
</td>
<td>
Udostępnia interfejs dla protokołu SMB, bibliotek klienckich i <a href="https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api">REST</a> interfejsu, który umożliwia dostęp z dowolnego miejsca do przechowywanych plików.
</td>
<td><ul>
<li>Lift- and -shift aplikacji w chmurze, gdy aplikacja używa interfejsów API systemu natywnych plików do udostępniania danych pomiędzy tym programem i innych aplikacji działających na platformie Azure.</li>
<li>Store programowania i debugowania narzędzi, które muszą być dostępne z wielu maszyn wirtualnych.</li>
</ul>
</td>
</tr>
<tr><td>Azure Blobs
</td>
<td>Zawiera biblioteki klienckie i <a href="https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api">REST</a> interfejs, który umożliwia pozbawionych struktury danych są przechowywane i używane w bardzo dużej skali w blokowych obiektach blob. Obsługuje również <a href="/azure/storage/blobs/data-lake-storage-introduction">usługi Azure Data Lake Storage Gen2</a> dla przedsiębiorstw rozwiązań do analizy danych big data.
</td>
<td><ul>
<li>Obsługuje scenariusze dotyczące przesyłania strumieniowego i dostępie losowym w aplikacji.</li>
<li>Dostęp do danych aplikacji z dowolnego miejsca.</li>
<li>Tworzenie usługi data lake enterprise na platformie Azure i wykonywać analizy danych big data.</li>
</ul></td>
</tr>
<tr><td>Azure Disks
</td>
<td>Zawiera biblioteki klienckie i <a href="https://docs.microsoft.com/rest/api/compute/disks">REST</a> interfejs, który umożliwia danych można trwale przechowywane i udostępniane z dołączonego wirtualnego dysku twardego.
</td>
<td><ul>
<li>Lift- and -shift aplikacje, które używają interfejsów API systemu plików natywnej do odczytu i zapisu danych na stałych dyskach.</li>
<li>Store danych, które nie są wymagane były dostępne z zewnątrz maszyny Wirtualnej, do której jest dołączony dysk.</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Azure (online) gorącego i zimnego (Archiwizacja) magazynu

Typ magazynu dla danego systemu zależy od wymagań systemu, w tym rozmiar magazynu, przepustowości i operacji We/Wy. W przypadku usługi storage typu DASD na mainframe aplikacji na platformie Azure zazwyczaj magazyn dysków dla usługi Azure Disks zamiast tego użyj. Dla komputera mainframe archiwalna magazynu obiektów blob jest używany na platformie Azure.

Dyski SSD zapewniają najwyższą wydajność magazynu na platformie Azure. Dostępne (pisania tego dokumentu) są następujące opcje:

| Typ         | Rozmiar           | Operacje wejścia/wyjścia                  |
|--------------|----------------|-----------------------|
| Ultra, SSD    | 4 GB do 64 TB  | 1200 na 160,000 operacje We/Wy |
| Premium, SSD  | 32 GB do 32 TB | 12 — 15 000 operacji We/Wy     |
| Standardowa, SSD | 32 GB do 32 TB | 12 do 2000 operacji We/Wy      |

Blob storage zapewnia największą ilość pamięci masowej na platformie Azure. Oprócz rozmiaru magazynu platforma Azure oferuje zarządzane i niezarządzane magazynu. Z uwzględnieniem magazynu zarządzanego platforma Azure zajmie się zarządzaniem podstawowe konta magazynu. Za pomocą magazynu niezarządzanego użytkownik przyjmuje odpowiedzialność za konfigurowanie konta usługi Azure storage odpowiedniego rozmiaru spełnić wymagania dotyczące magazynu.

## <a name="next-steps"></a>Kolejne kroki

- [Migracja komputera mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Mainframe rehosting na maszynach wirtualnych platformy Azure](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Przenoszenie komputera mainframe obliczeniowych na platformę Azure](mainframe-compute-Azure.md)
- [Kiedy używać obiektów blob platformy Azure, Azure Files lub Azure Disks](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)
- [Standardowy dysk SSD Managed Disks dla obciążeń maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)

### <a name="ibm-resources"></a>Zasoby firmy IBM

- [Równoległe Sysplex na Z firmy IBM](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS i funkcji sprzężenia: Beyond the Basics](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Tworzenie użytkowników wymagane dla bazy danych Db2 pureScale instalacji funkcji](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt — Utwórz wystąpienie, polecenie](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Bazy danych Db2 pureScale Clustered rozwiązanie bazy danych](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Chmura platformy Microsoft Azure Government dla aplikacji mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Firma Microsoft i FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Więcej zasobów migracji

- [Alliance modernizacji platformy: IBM Db2 na platformie Azure](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)
- [Wirtualne centrum danych usługi Azure Lift- and -Shift przewodnik](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
