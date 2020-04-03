---
title: Zarządzanie obciążeniem
description: Wskazówki dotyczące wdrażania zarządzania obciążeniami w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 06fc9edd55aa51c985cbb981fc5a6892d0ca75e5
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583122"
---
# <a name="what-is-workload-management"></a>Co to jest zarządzanie obciążeniem pracą?

Uruchamianie mieszanych obciążeń może stanowić wyzwanie zasobów w systemach zajęty.  Solution Architects szukają sposobów na oddzielenie klasycznych działań magazynowania danych (takich jak ładowanie, przekształcanie i wykonywanie zapytań danych), aby upewnić się, że istnieje wystarczająca ilość zasobów, aby osiągnąć ula.  

Izolacja serwera fizycznego może prowadzić do kieszeni infrastruktury, które są niedostatecznie wyposażone, overbooked lub w stanie, w którym pamięci podręczne są stale zagruntowane ze sprzętem uruchamiania i zatrzymywania.  Skuteczny system zarządzania obciążeniem skutecznie zarządza zasobami, zapewnia wysoce efektywne wykorzystanie zasobów i maksymalizuje zwrot z inwestycji (ROI).

Obciążenie magazynu danych odnosi się do wszystkich operacji, które pojawiają się w odniesieniu do magazynu danych. Głębokość i szerokość tych składników zależy od poziomu dojrzałości magazynu danych.  Obciążenie magazynu danych obejmuje: 
- Cały proces załadunku danych do magazynu 
- Przeprowadzanie analizy i raportowania hurtowni danych
- Zarządzanie danymi w magazynie danych 
- Eksportowanie danych z magazynu danych

Wydajność magazynu danych jest określana przez [jednostki magazynu danych](what-is-a-data-warehouse-unit-dwu-cdwu.md).
- Aby wyświetlić zasoby przydzielone dla wszystkich profili wydajności, zobacz [Limity pamięci i współbieżności](memory-concurrency-limits.md).
- Aby dostosować pojemność, można [skalować w górę lub w dół](quickstart-scale-compute-portal.md).


## <a name="workload-management-concepts"></a>Koncepcje zarządzania obciążeniem pracą

W przeszłości dla puli SQL Synapse w usłudze Azure Synapse zarządzałeś wydajnością kwerendy za pośrednictwem [klas zasobów](resource-classes-for-workload-management.md).  Klasy zasobów mogą przypisywać pamięć do kwerendy na podstawie członkostwa roli.  Podstawowym wyzwaniem dla klas zasobów jest to, że po skonfigurowaniu nie było żadnego nadzoru ani możliwości kontrolowania obciążenia.  

Na przykład przyznanie członkostwa roli użytkownika ad hoc smallrc pozwoliło, aby użytkownik zużywał 100% pamięci w systemie.  W przypadku klas zasobów nie ma możliwości zarezerwowania i zapewnienia dostępności zasobów dla obciążeń krytycznych.

Zarządzanie obciążeniem puli SQL synapse w usłudze Azure Synapse składa się z trzech koncepcji wysokiego poziomu: [klasyfikacja obciążenia,](sql-data-warehouse-workload-classification.md) [ważność obciążenia](sql-data-warehouse-workload-importance.md) i [izolacja obciążenia.](sql-data-warehouse-workload-isolation.md)  Te możliwości zapewniają większą kontrolę nad tym, jak obciążenie wykorzystuje zasoby systemowe.

Klasyfikacja obciążenia to koncepcja przypisywania żądania do grupy obciążenia i ustawiania poziomów ważności.  Historycznie, to zadanie zostało wykonane poprzez członkostwo roli przy użyciu [sp_addrolemember](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#change-a-users-resource-class).  Można to teraz zrobić za pomocą [pliku CREATE WORKLOAD CLASSIFER](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql).  Możliwość klasyfikacji zapewnia bogatszy zestaw opcji, takich jak etykieta, sesja i czas klasyfikowania żądań.

Znaczenie obciążenia wpływa na kolejność, w której żądanie uzyskuje dostęp do zasobów.  W systemie zajęty żądanie o wyższym znaczeniu ma pierwszy dostęp do zasobów.  Znaczenie może również zapewnić uporządkowany dostęp do zamków. 

Izolacja obciążenia rezerwuje zasoby dla grupy obciążenia.  Zasoby zarezerwowane w grupie obciążenia są przechowywane wyłącznie dla tej grupy obciążenia, aby zapewnić wykonanie.  Grupy obciążenia umożliwiają również zdefiniowanie ilości zasobów, które są przypisane na żądanie, podobnie jak klasy zasobów.  Grupy obciążenia umożliwiają rezerwę lub ograniczenie ilości zasobów, z których może korzystać zestaw żądań.  Na koniec grupy obciążenia są mechanizmem do stosowania reguł, takich jak limit czasu kwerendy, do żądań.  


## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat klasyfikacji obciążeń, zobacz [Klasyfikacja obciążeń](sql-data-warehouse-workload-classification.md).  
- Aby uzyskać więcej informacji na temat izolacji obciążenia, zobacz [Izolacja obciążenia](sql-data-warehouse-workload-isolation.md).  
- Aby uzyskać więcej informacji na temat znaczania obciążenia, zobacz [Ważność obciążenia](sql-data-warehouse-workload-importance.md).  
- Aby uzyskać więcej informacji na temat monitorowania zarządzania [obciążeniem, zobacz Monitorowanie portalu zarządzania obciążeniem](sql-data-warehouse-workload-management-portal-monitor.md).  
