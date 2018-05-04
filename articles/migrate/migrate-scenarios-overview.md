---
title: Migrowanie lokalnego centrum danych na platformę Azure | Microsoft Docs
description: Przeczytaj oficjalny dokument z omówieniem migrowania lokalnych centrów danych na platformę Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/21/2018
ms.author: raynew
ms.openlocfilehash: 8ba490998ea5f20efca591327716a6e39e9c1ba8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="migrating-your-on-premises-workloads-to-azure"></a>Migrowanie lokalnych obciążeń na platformę Azure


Platforma Microsoft Azure zapewnia dostęp do rozbudowanego zestawu usług w chmurze, które umożliwiają deweloperom i informatykom tworzenie i wdrażanie aplikacji oraz zarządzanie nimi przy użyciu różnych narzędzi i platform za pośrednictwem globalnej sieci centrów danych. Jeśli Twoja firma ma problem z przejściem do środowiska cyfrowego, chmura platformy Azure może ułatwić określenie sposobu optymalizacji zasobów i operacji, komunikację z klientami i pracownikami oraz przekształcanie produktów.

Jednak w przypadku platformy Azure uwzględniono fakt, że mimo wszystkich zalet chmury w zakresie szybkości, elastyczności, wydajności, niezawodności i minimalizowania kosztów wiele organizacji będzie musiało jeszcze przez jakiś czas korzystać z lokalnych centrów danych. W odpowiedzi na problemy z dostosowaniem chmury platforma Azure udostępnia strategię chmury hybrydowej, która stanowi pomost między lokalnymi centrami danych i chmurą publiczną platformy Azure. Dotyczy to na przykład używania zasobów w chmurze platformy Azure, takich jak kopia zapasowa, do ochrony zasobów lokalnych lub używania usługi analiz platformy Azure w celu uzyskiwania wglądu w obciążenia lokalne. 

W ramach naszej strategii chmury hybrydowej platforma Azure udostępnia rozwijające się rozwiązania do migrowania aplikacji lokalnych i obciążeń do chmury. Wystarczy wykonać proste czynności, aby kompleksowo ocenić zasoby lokalne i określić, jak będą działać w chmurze platformy Azure. Następnie po przeprowadzeniu szczegółowej oceny można bez obaw zmigrować zasoby na platformę Azure. Gdy zasoby są uruchomione na platformie Azure, można je zoptymalizować w celu zachowania i poprawy dostępu, elastyczności, bezpieczeństwa i niezawodności.

W tej serii artykułów dotyczących migracji pokazano, jak zaplanować i opracować strategię migracji w firmie. W tych artykułach przedstawiono wiele scenariuszy w zakresie zwiększania złożoności, które będą dodawane z czasem. Podsumowanie tych scenariuszy znajduje się w poniższej tabeli. Dla każdego scenariusza udostępniono omówienie i architekturę migracji oraz pokazano etapy procesu migracji. 

**Scenariusz** | **Rozwiązanie** | **Usługi** | **Artykuł** 
--- | --- | --- | ---
[Scenariusz 1. Odnajdywanie i ocena](migrate-scenarios-assessment.md) | Odnajdywanie i oceny lokalnej aplikacji, danych i infrastruktury dla migracji na platformie Azure | Data Migration Assistant, usługa Azure Migrate  | Dostępny teraz
**[Scenariusz 2: Rehost aplikacji](migrate-scenarios-lift-and-shift.md)** | Podnieś i przesunięcia aplikacji na platformie Azure. | Azure Site Recovery, Azure Database Migration Service, wystąpienie zarządzane Azure SQL | Dostępny teraz
**Scenariusz 3: Zrefaktoryzuj aplikacji** | Refaktoryzuj aplikacji podczas migracji na platformie Azure. | W planowaniu | Planowany
**Scenariusz 4: Rearchitect aplikacji** | Rearchitect aplikacji podczas migracji na platformie Azure. | W planowaniu | Planowany
**Scenariusz 5: Ponowna kompilacja aplikacji** |Odbuduj aplikacji podczas migracji na platformie Azure | W planowaniu | Planowany




