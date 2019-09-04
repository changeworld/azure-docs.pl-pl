---
title: Używanie Azure Data Factory do migrowania danych z usługi Data Lake i magazynu danych na platformę Azure | Microsoft Docs
description: Użyj Azure Data Factory, aby zmigrować dane z usługi Data Lake i magazynu danych na platformę Azure.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 7/30/2019
ms.openlocfilehash: 937a076b3e0e3c5170779d3449776f0aa1cf5b49
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258998"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Używanie Azure Data Factory do migrowania danych z usługi Data Lake lub magazynu danych na platformę Azure 

Azure Data Factory może być narzędziem do migracji danych, gdy chcesz migrować usługę Data Lake lub magazyn danych przedsiębiorstwa (rozszerzenia) na platformę Azure. Migracja Data Lake — migracja i magazyn danych są związane z następującymi scenariuszami: 

- Migracja obciążenia danych Big Data z AWS S3, Premium systemu plików Hadoop do platformy Azure. 
- ROZSZERZENIA migrację z rozwiązań Oracle Exadata, Netezza, Teradata, AWS RedShift do platformy Azure. 

Azure Data Factory może przenosić usługę PBs z danych na potrzeby migracji usługi Data Lake i dziesiątki TB danych na potrzeby migracji magazynu danych. 

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Dlaczego Azure Data Factory można używać do migracji danych 

- Azure Data Factory można łatwo skalować w górę, aby przenieść dane w sposób bezserwerowy z wysoką wydajnością, odpornością i skalowalnością i płacisz tylko za to, czego używasz.  
  - Azure Data Factory nie ma ograniczeń dotyczących ilości danych i liczby plików.
  - Azure Data Factory 100 może korzystać z przepustowości sieci i magazynu, aby osiągnąć najwyższą przepływność przenoszenia danych w środowisku.   
  - Azure Data Factory jest zgodna z strategią płatność zgodnie z rzeczywistym użyciem, dzięki czemu płacisz tylko za czas, gdy używasz Azure Data Factory do przeprowadzenia migracji danych na platformę Azure.  
- Azure Data Factory ma możliwość wykonywania jednorazowego obciążenia historycznego oraz zaplanowanego obciążenia przyrostowego. 
- Azure Data Factory używa Azure IR do przeniesienia danych między publicznie dostępnymi punktami końcowymi usługi Data Lake/Warehouse lub alternatywnie do przeniesienia danych dla punktów końcowych usługi Data Lake/Warehouse w sieci wirtualnej lub za zaporą. 
- Azure Data Factory ma zabezpieczenia klasy korporacyjnej: należy użyć programu MSI lub tożsamości usługi do integracji zabezpieczonej usługi z usługą lub alternatywnie wykorzystać Azure Key Vault do zarządzania poświadczeniami. 
- Azure Data Factory zapewnia środowisko autorskie bez kodu i bogaty wbudowany pulpit nawigacyjny monitorowania.  

## <a name="online-vs-offline-data-migration"></a>Migracja danych w trybie online i offline

Azure Data Factory to typowe narzędzie do migracji danych w trybie online do transferowania danych za pośrednictwem sieci (Internet, ER lub VPN), gdzie migracja danych w trybie offline umożliwia fizycznym dostarczenie urządzeń do transferów danych z organizacji do centrum danych platformy Azure.  

Podczas wybierania trybu online i migracji w trybie offline istnieją trzy kluczowe zagadnienia:  

- Rozmiar danych do migracji. 
- Przepustowość sieci. 
- Okno migracji.   

Jeśli chcesz ukończyć migrację danych w ciągu dwóch tygodni (okno migracji), zobaczysz wycięty wiersz na poniższej ilustracji, aby pokazać, kiedy warto używać narzędzia do migracji w trybie online (Azure Data Factory) z innym rozmiarem danych i przepustowością sieci.   

![w trybie online a offline](media/data-migration-guidance-overview/online-offline.png)

> [!NOTE]
> Zalety migracji w trybie online polega na tym, że można osiągnąć zarówno dane historyczne ładowania, jak i przyrostowe źródła danych.  Dzięki temu dane można zachować synchronizację między istniejącym i nowym magazynem podczas całego okna migracji, aby można było ponownie skompilować logikę ETL w nowym sklepie przy użyciu odświeżonych danych. 


## <a name="next-steps"></a>Następne kroki

- [Migrowanie danych z AWS S3 na platformę Azure](data-migration-guidance-s3-azure-storage.md)
- [Migrowanie danych z lokalnego klastra Hadoop do platformy Azure](data-migration-guidance-hdfs-azure-storage.md)
- [Migrowanie danych z lokalnego serwera Netezza na platformę Azure](data-migration-guidance-netezza-azure-sqldw.md)
