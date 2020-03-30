---
title: Zalecane testy porównawcze wydajności — pliki NetApp usługi Azure
description: Dowiedz się więcej o zaleceniach dotyczących testowania danych porównawczych dotyczących wydajności woluminów i metryk przy użyciu plików NetApp usługi Azure.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 08/07/2019
ms.openlocfilehash: 8f354152c23dd7ad0413f27585d724f8070ca003
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551526"
---
# <a name="performance-benchmark-test-recommendations-for-azure-netapp-files"></a>Zalecenia dotyczące testu wydajności dla usługi Azure NetApp Files

Ten artykuł zawiera zalecenia dotyczące testowania porównawczego wydajności woluminu i metryki przy użyciu plików NetApp platformy Azure.

## <a name="overview"></a>Omówienie

Aby zrozumieć charakterystykę wydajności woluminu plików NetApp platformy Azure, można użyć narzędzia typu open source [FIO](https://github.com/axboe/fio) do uruchamiania serii testów porównawczych w celu symulowania różnych obciążeń. FIO można zainstalować zarówno w systemach operacyjnych Linux, jak i Windows.  Jest to doskonałe narzędzie, aby uzyskać szybką migawkę zarówno We/Wy, jak i przepływności woluminu.

### <a name="vm-instance-sizing"></a>Rozmiar wystąpienia maszyny Wirtualnej

Aby uzyskać najlepsze wyniki, upewnij się, że używasz wystąpienia maszyny wirtualnej (VM), które jest odpowiednio dopasowywać do wykonywania testów. W poniższych przykładach użyto wystąpienia Standard_D32s_v3. Aby uzyskać więcej informacji na temat rozmiarów wystąpień maszyn wirtualnych, zobacz [Rozmiary maszyn wirtualnych z systemem Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) na platformie Azure dla maszyn wirtualnych opartych na systemie Windows i [rozmiary maszyn wirtualnych z systemem Linux na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) dla maszyn wirtualnych opartych na systemie Linux.

### <a name="azure-netapp-files-volume-sizing"></a>Rozmiary woluminów usługi Azure NetApp

Upewnij się, że wybierzesz poprawny poziom usługi i rozmiar przydziału woluminu dla oczekiwanego poziomu wydajności. Aby uzyskać więcej informacji, zobacz [Poziomy usług dla plików NetApp platformy Azure.](azure-netapp-files-service-levels.md)

### <a name="virtual-network-vnet-recommendations"></a>Zalecenia dotyczące sieci wirtualnej

Należy wykonać testowanie porównawcze w tej samej sieci wirtualnej co usługi Azure NetApp Files. Poniższy przykład przedstawia zalecenie:

![Zalecenia sieci wirtualnej](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>Instalacja FIO

FIO jest dostępny w formacie binarnym zarówno dla Linuksa, jak i Windowsa. Postępuj zgodnie z sekcją Pakiety binarne w [FIO,](https://github.com/axboe/fio) aby zainstalować dla wybranej platformy.

## <a name="fio-examples-for-iops"></a>Przykłady FIO dla Operacji We/Wy 

Przykłady FIO w tej sekcji używają następującej konfiguracji:
* Rozmiar wystąpienia maszyny Wirtualnej: D32s_v3
* Poziom i rozmiar puli pojemności: Premium / 50 TiB
* Wielkość przydziału: 48 TiB

Poniższe przykłady pokazują FIO losowych odczytów i zapisów.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO: Rozmiar bloku 8k 100% losowych odczytów

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>Wyjście: 68k odczytu IOPS wyświetlane

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO: 8k rozmiar bloku 100% losowych zapisów

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>Wyjście: 73k zapisu IOPS wyświetlane

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>Przykłady FIO dotyczące przepustowości

Przykłady w tej sekcji pokazują FIO sekwencyjne odczyty i zapisy.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO: rozmiar bloku 64k 100% odczytów sekwencyjnych

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>Wyjście: wyświetlana przepustowość 11,8 Gbit/s

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO: rozmiar bloku 64k 100% zapisów sekwencyjnych

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>Wyjście: wyświetlana przepustowość 12,2 Gbit/s

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>Dane dotyczące głośności

Dane dotyczące wydajności usługi Azure NetApp Files są dostępne za pośrednictwem liczników usługi Azure Monitor. Liczniki są dostępne za pośrednictwem witryny Azure portal i żądania REST API GET. 

Można wyświetlić dane historyczne dla następujących informacji:
* Średnie opóźnienie odczytu 
* Średnie opóźnienie zapisu 
* Odczyt IOPS (średnia)
* Zapis We/Wy (średnia)
* Rozmiar logiczny woluminu (średnia)
* Rozmiar migawki woluminu (średnia)

### <a name="using-azure-monitor"></a>Korzystanie z usługi Azure Monitor 

Dostęp do liczników plików NetApp platformy Azure można uzyskać na podstawie woluminu na stronie Metryki, jak pokazano poniżej:

![Metryki usługi Azure Monitor](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

Można również utworzyć pulpit nawigacyjny w usłudze Azure Monitor dla plików NetApp platformy Azure, przechodząc do strony Metryki, filtrując dla netapp i określając liczniki woluminów zainteresowania: 

![Pulpit nawigacyjny usługi Azure Monitor](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Dostęp do interfejsu API monitora platformy Azure

Można uzyskać dostęp do liczników plików NetApp platformy Azure przy użyciu wywołań interfejsu API REST. Zobacz [Obsługiwane metryki za pomocą usługi Azure Monitor: Microsoft.NetApp/netAppAccounts/capacityPools/Volumes](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetappnetappaccountscapacitypoolsvolumes) for counters for capacity pools and volumes.

W poniższym przykładzie przedstawiono adres URL GET do wyświetlania rozmiaru woluminu logicznego:

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>Następne kroki

- [Poziomy usług dla usługi Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Testy porównawcze wydajności dla usługi Azure NetApp Files](azure-netapp-files-performance-benchmarks.md)