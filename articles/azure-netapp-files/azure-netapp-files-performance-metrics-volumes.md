---
title: Testowanie pod kątem wydajności woluminu i metryki za pomocą usługi Azure Files NetApp testów porównawczych | Dokumentacja firmy Microsoft
description: Udostępnia testów porównawczych testowania zalecenia dotyczące wydajności woluminu i metryki za pomocą usługi Azure Files NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: b-juche
ms.openlocfilehash: 12ae9e313655924f11799152b5e58b77776c135c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478812"
---
# <a name="benchmark-testing-for-volume-performance-and-metrics-using-azure-netapp-files"></a>Testy porównawcze zbiorczej wydajności i metryk za pomocą usługi Azure NetApp Files

Ten artykuł zawiera testów porównawczych testowania zalecenia dotyczące wydajności woluminu i metryki za pomocą usługi Azure Files NetApp.

## <a name="overview"></a>Przegląd

Aby zrozumieć charakterystyki wydajności usługi Azure Files NetApp woluminu, można użyć narzędzia typu open source [FIO](https://github.com/axboe/fio) na uruchomieniu serii testów porównawczych, aby zasymulować różnych obciążeń. FIO można zainstalować na obu systemu Linux i Windows na podstawie systemów operacyjnych.  Jest doskonałym narzędziem do uzyskania szybkiej migawki operacje We/Wy i Przepływność dla woluminu.

### <a name="vm-instance-sizing"></a>Ustalanie rozmiaru wystąpienia maszyny Wirtualnej

Aby uzyskać najlepsze wyniki upewnij się, używają wystąpienia maszyny wirtualnej (VM), które jest nieodpowiedni rozmiar do wykonywania testów. W poniższych przykładach używane jest wystąpienie Standard_D32s_v3. Aby uzyskać więcej informacji na temat rozmiarów wystąpień maszyn wirtualnych, zobacz [rozmiary dla Windows maszyn wirtualnych na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) dla maszyn wirtualnych z systemem Windows, a [rozmiary maszyn wirtualnych systemu Linux na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) dla maszyn wirtualnych z systemem Linux.

### <a name="azure-netapp-files-volume-sizing"></a>Rozmiar woluminu w usłudze Azure plików NetApp

Upewnij się, wybierz opcję prawidłowego poziom i wolumin rozmiaru przydziału na poziomie oczekiwanym wydajności. Zobacz [poziomów usług dla usługi Azure Files NetApp](azure-netapp-files-service-levels.md) Aby uzyskać więcej informacji.

### <a name="virtual-network-vnet-recommendations"></a>Zalecenia dotyczące sieci wirtualnych (VNet)

Należy to wykonać testów porównawczych, testowanie w tej samej sieci wirtualnej jako usługi Azure Files NetApp. W poniższym przykładzie przedstawiono zalecenia:

![Zalecenia dotyczące sieci wirtualnej](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>Instalacja FIO

FIO jest dostępny w formacie binarnym, zarówno dla systemu Linux i Windows. Postępuj zgodnie z sekcji binarne pakietów w [FIO](https://github.com/axboe/fio) do zainstalowania dla wybranej platformy.

## <a name="fio-examples-for-iops"></a>Przykłady FIO operacje We/Wy 

Przykłady FIO w tej sekcji Użyj następujących ustawień:
* Rozmiar wystąpienia maszyny Wirtualnej: D32s_v3
* Poziom usług z pojemności puli i rozmiar: Premium / 50 TiB
* Rozmiar przydziału woluminu: 48 TiB

W poniższych przykładach pokazano FIO losowych operacji odczytu i zapisu.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO: 8 kilobajtów block losowych operacji odczytu 100% rozmiaru

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>Dane wyjściowe: 68k odczytu operacje We/Wy wyświetlane

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO: 8 kilobajtów block losowe operacje zapisu w 100% rozmiaru

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>Dane wyjściowe: 73k zapisu operacji We/Wy wyświetlane

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>Przykłady FIO przepustowości

W przykładach w tej sekcji show FIO sekwencyjne odczytuje i zapisuje.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO: 64 KB block odczytów sekwencyjnych 100% rozmiaru

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>Dane wyjściowe: 11.8 GB/s przepływności wyświetlane

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO: 64 KB block sekwencyjnych zapisów 100% rozmiaru

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>Dane wyjściowe: 12.2 GB/s przepływności wyświetlane

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>Metryki woluminu

Dane dotyczące wydajności NetApp plików platformy Azure jest dostępna za pośrednictwem liczników monitora platformy Azure. Te liczniki są dostępne za pośrednictwem witryny Azure portal i żądania GET interfejsu API REST. 

Możesz wyświetlić dane historyczne dotyczące następujące informacje:
* Średnia, opóźnienie odczytu 
* Opóźnienie zapisu średni 
* Odczytaj operacje We/Wy (średnia)
* Zapis operacji We/Wy (średnia)
* Rozmiar logiczny woluminu (średnia)
* Rozmiar migawki woluminu (średnia)

### <a name="using-azure-monitor"></a>Korzystanie z usługi Azure Monitor 

Aby uzyskać dostęp liczników usługi Azure Files NetApp na poszczególnych woluminów ze strony metryki, jak pokazano poniżej:

![Metryki usługi Azure Monitor](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

Można również utworzyć pulpit nawigacyjny w usłudze Azure Monitor dla usługi Azure Files NetApp, przechodząc do strony metryki, filtrowanie NetApp i określając liczniki woluminu zainteresowania: 

![Pulpit nawigacyjny usługi Azure Monitor](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Usługa Azure Monitor, API dostęp

Liczniki usługi Azure Files NetApp dostęp przy użyciu wywołań interfejsu API REST. Zobacz [metryki obsługiwane z usługą Azure Monitor: Microsoft.NetApp/netAppAccounts/capacityPools/Volumes](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetappnetappaccountscapacitypoolsvolumes) liczników dla pojemności puli i woluminów.

Poniższy przykład przedstawia pobieranie adresu URL do wyświetlania rozmiar logiczny woluminu:

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>Kolejne kroki

- [Poziomy usług dla usługi Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Testy porównawcze wydajności dla usługi Azure Files NetApp](azure-netapp-files-performance-benchmarks.md)