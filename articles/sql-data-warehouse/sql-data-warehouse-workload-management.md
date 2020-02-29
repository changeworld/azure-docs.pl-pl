---
title: Zarządzanie obciążeniem
description: Wskazówki dotyczące implementowania zarządzania obciążeniami w usłudze Azure Synapse Analytics.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 14ea742a40afff8105560f1003655004687c7c9e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197661"
---
# <a name="what-is-workload-management"></a>Co to jest zarządzanie obciążeniami?

Uruchamianie obciążeń mieszanych może stanowić wyzwanie dla zasobów w systemach zajętych.  Architektzy rozwiązań poszukują sposobów rozdzielania klasycznych działań związanych z magazynowaniem danych (np. ładowaniem, przekształcaniem i wykonywaniem zapytań dotyczących danych) w celu zapewnienia, że istnieją wystarczające zasoby do trafień umowy SLA.  

Izolacja serwera fizycznego może prowadzić do kieszeni na infrastrukturę, która jest niedostatecznie wykorzystywana, zastrzeżona lub w stanie, w którym bufory są stale uruchamiane z sprzętem, który jest uruchamiany i zatrzymywany.  Pomyślny schemat zarządzania obciążeniami skutecznie zarządza zasobami, zapewnia wysoce wydajne wykorzystanie zasobów i maksymalizuje zwrot z inwestycji.

Obciążenie magazynu danych odnosi się do wszystkich operacji, które wykonywanych w odniesieniu do hurtowni danych. Głębokość i szerokość tych składników zależą od poziomu dojrzałości hurtowni danych.  Obciążenia magazynu danych obejmują: 
- Cały proces ładowania danych do magazynu 
- Wykonywanie analizy i raportowania hurtowni danych
- Zarządzanie danymi w magazynie danych 
- Eksportowanie danych z magazynu danych

Wydajność hurtowni danych jest określana przez [jednostki magazynu danych](what-is-a-data-warehouse-unit-dwu-cdwu.md).
- Aby wyświetlić zasoby przydzieloną dla wszystkich profilów wydajności, zobacz [limity pamięci i współbieżności](memory-concurrency-limits.md).
- W celu dostosowania pojemności można [skalować w górę lub w dół](quickstart-scale-compute-portal.md).


## <a name="workload-management-concepts"></a>Pojęcia związane z zarządzaniem obciążeniem
W przeszłości w przypadku analiz SQL na platformie Azure Synapse wydajność zapytań za pomocą [klas zasobów](resource-classes-for-workload-management.md).  Klasy zasobów dozwolone do przypisywania pamięci do zapytania na podstawie przynależności do roli.  Podstawowym wyzwaniem z klasami zasobów jest to, że po skonfigurowaniu nie było żadnego ładu ani kontrolować obciążenia.  

Załóżmy na przykład, że przyznanie roli użytkownika ad hoc do smallrc zezwala temu użytkownikowi na korzystanie z 100% pamięci w systemie.  W przypadku klas zasobów nie ma możliwości zarezerwowania i zapewnienia dostępności zasobów dla obciążeń krytycznych.

Zarządzanie obciążeniem usługi SQL Analytics w usłudze Azure Synapse składa się z trzech koncepcji wysokiego poziomu: [klasyfikacji obciążeń](sql-data-warehouse-workload-classification.md), [ważności obciążeń](sql-data-warehouse-workload-importance.md) i [izolacji obciążeń](sql-data-warehouse-workload-isolation.md).  Te funkcje zapewniają większą kontrolę nad sposobem, w jaki obciążenie wykorzystuje zasoby systemowe.

Klasyfikacja obciążenia to koncepcja przypisywania żądania do grupy obciążeń i ustawiania poziomów ważności.  W przeszłości to przypisanie zostało wykonane za pośrednictwem przynależności do roli przy użyciu [sp_addrolemember](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#change-a-users-resource-class).  Można to zrobić za pośrednictwem [tworzenia obciążenia CLASSIFER](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql).  Funkcja klasyfikacji oferuje bogatszy zestaw opcji, takich jak etykieta, sesja i czas, aby sklasyfikować żądania.

Znaczenie obciążeń ma wpływ na kolejność, w jakiej żądanie uzyskuje dostęp do zasobów.  W przypadku zajętego systemu żądanie o wyższym znaczeniu ma pierwszy dostęp do zasobów.  Ważność może także zapewnić uporządkowany dostęp do blokad. 

Izolacja obciążenia rezerwuje zasoby dla grupy obciążenia.  Zasoby zarezerwowane w grupie obciążenia są przechowywane wyłącznie dla tej grupy obciążenia, aby zapewnić wykonanie.  Grupy obciążeń umożliwiają również zdefiniowanie ilości zasobów, które są przypisane na żądanie, podobnie jak klasy zasobów.  Grupy obciążeń zapewniają możliwość zarezerwowania lub zakończenia ilości zasobów, które mogą być używane przez zestaw żądań.  Na koniec grupy obciążeń to mechanizm do zastosowania reguł, takich jak przekroczenie limitu czasu zapytania, do żądań.  


## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat klasyfikacji obciążeń, zobacz [Klasyfikacja obciążeń](sql-data-warehouse-workload-classification.md).  
- Aby uzyskać więcej informacji o izolacji obciążenia, zobacz [izolacja obciążenia](sql-data-warehouse-workload-isolation.md).  
- Aby uzyskać więcej informacji o ważności obciążenia, zobacz [ważność obciążenia](sql-data-warehouse-workload-importance.md).  
- Aby uzyskać więcej informacji na temat monitorowania zarządzania obciążeniem, zobacz [monitorowanie Portal zarządzania obciążenia](sql-data-warehouse-workload-management-portal-monitor.md).  
