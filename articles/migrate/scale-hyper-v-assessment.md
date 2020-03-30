---
title: Ocenianie dużej liczby maszyn wirtualnych funkcji Hyper V w celu migracji na platformę Azure za pomocą usługi Azure Migrate | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób oceny dużej liczby maszyn wirtualnych funkcji Hyper-V do migracji na platformę Azure przy użyciu usługi Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: c1ae3a9ed8a775161aaf85ab2c91b1e43113d2e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70279437"
---
# <a name="assess-large-numbers-of-hyper-v-vms-for-migration-to-azure"></a>Ocena dużej liczby maszyn wirtualnych funkcji Hyper-V w celu migracji na platformę Azure

W tym artykule opisano sposób oceny dużej liczby lokalnych maszyn wirtualnych funkcji Hyper V do migracji na platformę Azure przy użyciu narzędzia oceny migracji serwera azure.

[Usługa Azure Migrate](migrate-services-overview.md) udostępnia centrum narzędzi ułatwiających odnajdywanie, ocenę i migrację aplikacji, infrastruktury i obciążeń na platformę Microsoft Azure. Centrum zawiera narzędzia migracji platformy Azure i oferty niezależnych dostawców oprogramowania innych firm (ISV). 


W tym artykule omówiono sposób wykonywania następujących zadań:
> [!div class="checklist"]
> * Plan oceny na dużą skalę.
> * Skonfiguruj uprawnienia platformy Azure i przygotuj funkcji Hyper-V do oceny.
> * Utwórz projekt migracji platformy Azure i utwórz ocenę.
> * Przejrzyj ocenę zgodnie z planem migracji.


> [!NOTE]
> Jeśli chcesz wypróbować dowód koncepcji, aby ocenić kilka maszyn wirtualnych przed oceną na dużą skalę, postępuj zgodnie z naszą [serią samouczków](tutorial-prepare-hyper-v.md)

## <a name="plan-for-assessment"></a>Plan oceny

Podczas planowania oceny dużej liczby maszyn wirtualnych funkcji Hyper-V, istnieje kilka rzeczy, o których trzeba pomyśleć:

- **Planowanie projektów migracji platformy Azure:** dowiedz się, jak wdrożyć projekty migracji platformy Azure. Jeśli na przykład centra danych znajdują się w różnych lokalizacjach geograficznych lub musisz przechowywać metadane odnajdywania, oceny lub migracji w innej lokalizacji geograficznej, może być konieczne wiele projektów.
- **Planowanie urządzeń:** Usługa Azure Migrate używa lokalnego urządzenia migracji platformy Azure, wdrożonego jako maszyna wirtualna funkcji Hyper-V, do ciągłego odnajdywania maszyn wirtualnych do oceny i migracji. Urządzenie monitoruje zmiany w środowisku, takie jak dodawanie maszyn wirtualnych, dysków lub kart sieciowych. Wysyła również metadane i dane dotyczące wydajności na ich temat na platformie Azure. Musisz dowiedzieć się, ile urządzeń do wdrożenia.


## <a name="planning-limits"></a>Limity planowania
 
Użyj limitów podsumowanych w tej tabeli do planowania.

**Planowania** | **Limity**
--- | --- 
**Projekty migracji platformy Azure** | Ocena do 35 000 maszyn wirtualnych w projekcie.
**Urządzenie usługi Azure Migrate** | Urządzenie może wykryć do 5000 maszyn wirtualnych.<br/> Urządzenie może łączyć się z maksymalnie 300 hostami funkcji Hyper-V.<br/> Urządzenie można skojarzyć tylko z jednym projektem migracji platformy Azure.<br/> Dowolna liczba urządzeń może być skojarzona z pojedynczym projektem migracji platformy Azure. <br/><br/> 
**Grupa** | W jednej grupie można dodać maksymalnie 35 000 maszyn wirtualnych.
**Ocena migracji platformy Azure** | W jednej ocenie można ocenić do 35 000 maszyn wirtualnych.



## <a name="other-planning-considerations"></a>Inne kwestie związane z planowaniem

- Aby rozpocząć odnajdowanie z urządzenia, należy wybrać każdy host funkcji Hyper-V. 
- Jeśli używasz środowiska wielodostępnego, obecnie nie można odnajdywać tylko maszyn wirtualnych należących do określonej dzierżawy. 

## <a name="prepare-for-assessment"></a>Przygotowanie do oceny

Przygotuj platformę Azure i funkcji Hyper-V do oceny serwera. 

1. Sprawdź [wymagania i ograniczenia dotyczące obsługi funkcji Hyper-V](migrate-support-matrix-hyper-v.md).
2. Konfigurowanie uprawnień do interakcji z kontem Azure w celu interakcji z programem Azure Migrate
3. Przygotowywanie hostów funkcji Hyper-V i maszyn wirtualnych

Postępuj zgodnie z instrukcjami w [tym samouczku,](tutorial-prepare-hyper-v.md) aby skonfigurować te ustawienia.

## <a name="create-a-project"></a>Tworzenie projektu

Zgodnie z wymaganiami planowania wykonaj następujące czynności:

1. Tworzenie projektów migracji platformy Azure.
2. Dodaj narzędzie oceny programu Azure Migrate Server do projektów.

[Dowiedz się więcej](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Tworzenie i przeglądanie oceny

1. Tworzenie ocen dla maszyn wirtualnych funkcji Hyper-V.
1. Przegląd ocen w ramach przygotowań do planowania migracji.

[Dowiedz się więcej](tutorial-assess-hyper-v.md) o tworzeniu i recenzowaniu ocen.
    

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano następujące zagadnienia:
 
> [!div class="checklist"] 
> * Planowane skalowanie ocen migracji platformy Azure dla maszyn wirtualnych z programem Hyper-V
> * Przygotowana platforma Azure i funkcja Hyper-V do oceny
> * Utworzono projekt migracji platformy Azure i uruchomiono oceny
> * Dokonano przeglądu ocen w ramach przygotowań do migracji.

Dowiedz [się, jak](concepts-assessment-calculation.md) obliczane są oceny i jak [modyfikować oceny](how-to-modify-assessment.md)
