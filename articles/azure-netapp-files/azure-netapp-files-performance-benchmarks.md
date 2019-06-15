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
ms.openlocfilehash: aca0668fc364518fe45d9fe94d089ee366b25676
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64870885"
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

* 100% 8 KiB losowe odczytu
* 100% 8 KiB losowego zapisu.
* 100% 64 KiB sekwencyjnego odczytu
* 100% 64 KiB zapisu sekwencyjnego
* 50% 64 KiB sekwencyjnego odczytu, 50% 64 KiB zapisu sekwencyjnego
* 50% 8 KiB losowe odczytu, 50% 8 KiB losowego zapisu.

## <a name="bandwidth"></a>Przepustowość

Usługa Azure Files NetApp oferuje wiele [poziomów usług](azure-netapp-files-service-levels.md). Każdemu poziomowi usług oferuje inną ilość przepustowości na TiB zaprowizowaną pojemnością (limit przydziału woluminu). Limit przepustowości dla woluminu jest aprowizowany oparte na kombinacji odpowiedni poziom usług i limit przydziału woluminu. Należy pamiętać, że limit przepustowości jest tylko jeden czynnikiem wpływającym na rzeczywistą ilość przepustowości, który będzie zrealizowany.  

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

Odległość między testowych maszyn wirtualnych i wielkość plików NetApp Azure ma wpływ na wydajność operacji We/Wy.  Poniższej tabeli porównano operacje We/Wy i opóźnienia krzywych odpowiedzi dla dwóch różnych zestawów maszyn wirtualnych.  Usługi Azure Files NetApp zbliża się jeden zestaw maszyn wirtualnych i inny zestaw jest dalsze natychmiast.  Należy pamiętać, że zwiększenie opóźnienia dla dalszych zestawu maszyn wirtualnych ma wpływ na ilości operacji We/Wy w danym stopień równoległości.  Niezależnie od tego odczytów względem wolumin może przekroczyć 300 000 operacji We/Wy, jak przedstawiono poniżej: 

![Analiza opóźnienia](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>Podsumowanie

Wrażliwy na opóźnienia obciążeń (baz danych) może mieć jeden raz odpowiedzi milisekund. Wydajności transakcyjnej może być ponad 300 KB operacje We/Wy z jednego woluminu.

Aplikacje zależne od przepływności (do przesyłania strumieniowego i tworzenia obrazów) mogą mieć 4.5GiB / s przepływności.
