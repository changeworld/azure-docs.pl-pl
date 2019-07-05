---
title: Testy porównawcze wydajności dla usługi Azure Files NetApp | Dokumentacja firmy Microsoft
description: Opisuje wyniki testów porównawczych wydajności dla usługi Azure Files NetApp na poziomie woluminu.
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
ms.date: 04/22/2019
ms.author: b-juche
ms.openlocfilehash: 14081daf1f45a84bc8ad19bf0239db1281d9e624
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449501"
---
# <a name="performance-benchmarks-for-azure-netapp-files"></a>Testy porównawcze wydajności dla usługi Azure NetApp Files

W tym artykule opisano wyniki testów porównawczych wydajności dla usługi Azure Files NetApp na poziomie woluminu. 

## <a name="sample-application-used-for-the-tests"></a>Przykładowa aplikacja używana dla testów

Testy wydajności został uruchomiony przy użyciu przykładowej aplikacji przy użyciu usługi Azure Files NetApp. Aplikacja ma następujące cechy: 

* Aplikacją opartą na systemie Linux, stworzona z myślą o chmurze
* Mogą być skalowane liniowo z dodano maszyn wirtualnych (VM) zwiększenie mocy obliczeniowej, zgodnie z potrzebami
* Wymaga szybkiej dostępność usługi data lake
* Ma wzorce operacji We/Wy, które są czasem losowych i czasami sekwencyjnych 
    * Losowy wzorzec wymaga małe opóźnienia dla większej liczby operacji We/Wy. 
    * Sekwencyjny wzorzec wymaga dużej przepustowości. 

## <a name="about-the-workload-generator"></a>Generator obciążenia — informacje

Wyniki pochodzą z plików podsumowań Vdbench. [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) to narzędzie wiersza polecenia, która generuje obciążeń We/Wy dysku na potrzeby sprawdzania poprawności dla wydajności przechowywania danych. Konfiguracja klient serwer, używany jest skalowalna.  Obejmuje ona pojedynczego mieszane master/klienta oraz 14 klienta dedykowanych maszyn wirtualnych.

## <a name="about-the-tests"></a>O testy

Testy zostały zaprojektowane do identyfikowania tej krzywych w granicach ograniczeń, które może być przykładowej aplikacji i czasu odpowiedzi.  

Następujące testy zostały uruchomione: 

* 100% 8-KiB losowe odczytu
* 100% 8 KiB losowego zapisu
* 100% 64-KiB sekwencyjnego odczytu
* Zapisu sekwencyjnego 64 KiB 100%
* 50%, 64-KiB sekwencyjnego odczytu i zapisu sekwencyjnego 64 KiB 50%
* 50% 8-KiB losowe odczytu 50% 8 KiB losowego zapisu

## <a name="bandwidth"></a>Przepustowość

Usługa Azure Files NetApp oferuje wiele [poziomów usług](azure-netapp-files-service-levels.md). Każdemu poziomowi usług oferuje inną ilość przepustowości na TiB zaprowizowaną pojemnością (limit przydziału woluminu). Limit przepustowości dla woluminu jest aprowizowany oparte na kombinacji odpowiedni poziom usług i limit przydziału woluminu. Limit przepustowości jest tylko jeden czynnikiem wpływającym na rzeczywistą ilość przepustowości, który będzie zrealizowany.  

Obecnie 4 500 MiB jest najwyższy przepływność, którą uzyskano obciążenia na jednym woluminie podczas testowania.  Z poziomu usługi — wersja Premium limit przydziału woluminu z 70.31 TiB przydzieli wystarczającą przepustowość do osiągnięcia tego przepływność na obliczenia poniżej: 

![Formuła przepustowości](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![Limit przydziału i usługi na poziomie](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>Przepływność dużych obciążeń

Test przepływności używał Vdbench wraz z kombinacją 12xD32s V3 magazynu maszyn wirtualnych. Wielkość próbki w teście osiągnąć następujące numery przepływność:

![Test przepływności](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>Czy mogę O dużych obciążeń wejścia /

Test operacji We/Wy używał Vdbench wraz z kombinacją 12xD32s V3 magazynu maszyn wirtualnych. Wielkość próbki w teście osiągnąć następujące liczby operacji We/Wy:

![Test operacji We/Wy](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Opóźnienie

Odległość między testowych maszyn wirtualnych i wielkość plików NetApp Azure ma wpływ na wydajność operacji We/Wy.  Poniższej tabeli porównano operacje We/Wy i opóźnienia krzywych odpowiedzi dla dwóch różnych zestawów maszyn wirtualnych.  Usługi Azure Files NetApp zbliża się jeden zestaw maszyn wirtualnych i inny zestaw jest dalsze natychmiast.  Większe opóźnienia dla dalszych zestawu maszyn wirtualnych ma wpływ na ilości operacji We/Wy w danym stopień równoległości.  Niezależnie od tego odczytów względem wolumin może przekroczyć 300 000 operacji We/Wy, jak przedstawiono poniżej: 

![Analiza opóźnienia](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>Podsumowanie

Wrażliwy na opóźnienia obciążeń (baz danych) może mieć czas reakcji jednej milisekundy. Wydajności transakcyjnej może być ponad 300 KB operacje We/Wy z jednego woluminu.

Aplikacje zależne od przepływności (do przesyłania strumieniowego i tworzenia obrazów) mogą mieć 4.5GiB / s przepływności.

## <a name="example-scripts"></a>Przykładowe skrypty

Następujące przykładowe skrypty są wyłącznie w celach demonstracyjnych.  Są one nie może być używane do celów produkcyjnych.  

    #
    #This script makes the following assumptions about the environment
    #VM Naming Convention:
    #   VM naming convention: vdbench-vnet1-1 .. vdbench-vnet1-x
    #
    #VM Count:
    #   The script is written for 24 VM's, 
    #   If you wish to test with a different number of vm's replace {1..24} with {1..Some Number}
    #
    #Volume mount point:
    #    The volumes in this script are mounted at the following mount points on all virtual machines
    #    /mnt/vdb1 ... /mnt/vdb6
    #   
    #Virtual machines must have
    #   make sure that vdbench is present on all vms in the environment and that java has been installed on each vm as well as nfs-utils
    #
    #The following tunables were configured on all virtual machines in the environment
    #    No special tunables were used to extract the results identified in this paper.
    #    Even rsize and wsize were left at the default (64K)
    #
    #Special Notes (thread counts) 
    #   You can hone in on the amount of I/O you hope to achieve in your testing using littles law,
    #   Run each test with a thread count of 1 to determnine the best possible latency.
    #   Thread count equals latency in seconds * desired I/O rate.  
    #   If you find a minimum latency of 1.14ms for example, divide that by 1000 to convert 1.3ms to .0013 seconds.
    #   
    #   If your goal from a single machine is for example 3,500 IOPS, a thread count of 4.0 is required.
    
    #   thread count 4.0 == .0013s * 5,000ops
    #
    #    
    #Special Notes: Increased window size:
    #   As round-trip time increases; corresponding increases are required in TCP Window Size – think Littles law as shown above. 
    #   Check the values of the following and adjust as needed: net.core.rmem_max, net.core.rmem_default, net.ipv4.tcp_rmem
    #
    #Run the utility like this:
    #    vdbench -f vnet1-SeqMix-workload
    #
    #When the utility is finished its run, grep 'avg' from the summary file to see overall run performance.
     
    
    
    #LUN CONFIG FILES
    for vnet in 1; do
        echo "sd=default,size=693g" > vnet${vnet}-luns-nfs
        for vm in {1..24}; do
            for vol in {1..6}; do
                echo "sd=sd-${vm}-${vol},host=vm${vm},lun=/mnt/vdb${vol}/file-${vm}-${vol},openflags=o_direct" >> vnet${vnet}-luns-nfs
            done
        done
    done
    
    
    #HOST CONFIG FILES
    for vnet in 1; do
        echo "hd=default,jvms=1,shell=ssh" > vnet${vnet}-hosts-nfs
        for vm in {1..24}; do
            echo "hd=vm${vm},system=vdbench-vnet${vnet}-${vm},user=root" >> vnet${vnet}-hosts-nfs
        done
    done
    
    #VDBENCH WORK SCRIPTS
    for vnet in 1; do
        for pattern in FillWrite SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "include=vnet${vnet}-hosts-nfs" > vnet${vnet}-${pattern}-workload
            echo "include=vnet${vnet}-luns-nfs" >> vnet${vnet}-${pattern}-workload
        done 
        #Fill Write File
        echo "wd=FillWrite,sd=sd*,rdpct=0,seekpct=eof,xfersize=64k" >> vnet${vnet}-FillWrite-workload
        echo "* Run Defaults" >> vnet${vnet}-FillWrite-workload
        echo 'rd=Initialize-LUNs-Full,wd=FillWrite,sd=("sd*"),iorate=max,interval=1,forthreads=1,elapsed=259200' >> vnet${vnet}-FillWrite-workload
    
        #The actual workload files - feel free to add more or use less.
        #   The thread counts shown below were used to generate the performance collateral, 
        #   feel free to change to suite your needs.
    
        echo "wd=SeqWrite,sd=sd*,rdpct=0,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqWrite-workload
        echo "wd=SeqRead,sd=sd*,rdpct=100,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqRead-workload
        echo "wd=RndRead,sd=sd*,rdpct=100,seekpct=100,xfersize=8k" >> vnet${vnet}-RndRead-workload
        echo "wd=RndWrite,sd=sd*,rdpct=0,seekpct=100,xfersize=8k" >> vnet${vnet}-RndWrite-workload
        echo "wd=SeqMix,sd=sd*,rdpct=50,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqMix-workload
        echo "wd=RndMix,sd=sd*,rdpct=50,seekpct=100,xfersize=8k" >> vnet${vnet}-RndMix-workload
        for pattern in SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "* Run Defaults" >> vnet${vnet}-${pattern}-workload
            #echo "rd=default,curve=(50,100),iorate=curve,warmup=60,elapsed=600,iorate=curve" >> vnet${vnet}-${pattern}-workload
            echo "rd=default,warmup=60,elapsed=600,iorate=max" >> vnet${vnet}-${pattern}-workload
            echo "* Run Definitions" >> vnet${vnet}-${pattern}-workload
        done
        echo 'rd=RndRead,wd=RndRead,sd=("sd*"),threads=25' >> vnet${vnet}-RndRead-workload
        echo 'rd=RndWrite,wd=RndWrite,sd=("sd*"),threads=20' >> vnet${vnet}-RndWrite-workload
        echo 'rd=SeqRead-4,wd=SeqRead,sd=("sd*"),threads=20' >> vnet${vnet}-SeqRead-workload
        echo 'rd=SeqWrite-26,wd=SeqWrite,sd=("sd*"),threads=26' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-32,wd=SeqWrite,sd=("sd*"),threads=32' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-40,wd=SeqWrite,sd=("sd*"),threads=40' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=RndMix,wd=RndMix,sd=("sd*"),threads=25' >> vnet${vnet}-RndMix-workload
        echo 'rd=SeqMix-6,wd=SeqMix,sd=("sd*"),threads=6' >> vnet${vnet}-SeqMix-workload
        echo 'rd=SeqMix-7,wd=SeqMix,sd=("sd*"),threads=7' >> vnet${vnet}-SeqMix-workload
    done
