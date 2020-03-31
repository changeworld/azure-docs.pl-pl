---
title: Ocena dużej liczby serwerów fizycznych do migracji na platformę Azure za pomocą usługi Azure Migrate | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób oceny dużej liczby serwerów fizycznych do migracji na platformę Azure przy użyciu usługi Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 01/19/2020
ms.author: hamusa
ms.openlocfilehash: a19a1b6e7416667079ab07fc5440ee8828c26bf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76294372"
---
# <a name="assess-large-numbers-of-physical-servers-for-migration-to-azure"></a>Ocena dużej liczby serwerów fizycznych pod kątem migracji na platformę Azure

W tym artykule opisano sposób oceny dużej liczby lokalnych serwerów fizycznych do migracji na platformę Azure przy użyciu narzędzia oceny programu Azure Migrate Server.

[Usługa Azure Migrate](migrate-services-overview.md) udostępnia centrum narzędzi ułatwiających odnajdywanie, ocenę i migrację aplikacji, infrastruktury i obciążeń na platformę Microsoft Azure. Centrum zawiera narzędzia migracji platformy Azure i oferty niezależnych dostawców oprogramowania innych firm (ISV). 


W tym artykule omówiono sposób wykonywania następujących zadań:
> [!div class="checklist"]
> * Plan oceny na dużą skalę.
> * Konfigurowanie uprawnień platformy Azure i przygotowywanie serwerów fizycznych do oceny.
> * Utwórz projekt migracji platformy Azure i utwórz ocenę.
> * Przejrzyj ocenę zgodnie z planem migracji.


> [!NOTE]
> Jeśli chcesz wypróbować proof-of-concept, aby ocenić kilka serwerów przed oceną na dużą skalę, postępuj zgodnie z naszą [serią samouczków](tutorial-prepare-physical.md).

## <a name="plan-for-assessment"></a>Plan oceny

Planując ocenę dużej liczby serwerów fizycznych, jest kilka rzeczy do przemyślenia:

- **Planowanie projektów migracji platformy Azure:** dowiedz się, jak wdrożyć projekty migracji platformy Azure. Jeśli na przykład centra danych znajdują się w różnych lokalizacjach geograficznych lub musisz przechowywać metadane odnajdywania, oceny lub migracji w innej lokalizacji geograficznej, może być konieczne wiele projektów.
- **Planowanie urządzeń:** Usługa Azure Migrate używa lokalnego urządzenia migracji platformy Azure, wdrożonego na komputerze z systemem Windows, do ciągłego odnajdywania serwerów do oceny i migracji. Urządzenie monitoruje zmiany w środowisku, takie jak dodawanie maszyn wirtualnych, dysków lub kart sieciowych. Wysyła również metadane i dane dotyczące wydajności na ich temat na platformie Azure. Musisz dowiedzieć się, ile urządzeń do wdrożenia.


## <a name="planning-limits"></a>Limity planowania
 
Użyj limitów podsumowanych w tej tabeli do planowania.

**Planowania** | **Limity**
--- | --- 
**Projekty migracji platformy Azure** | Oceń do 35 000 serwerów w projekcie.
**Urządzenie usługi Azure Migrate** | Urządzenie może wykryć do 250 serwerów.<br/> Urządzenie można skojarzyć tylko z jednym projektem migracji platformy Azure.<br/> Dowolna liczba urządzeń może być skojarzona z pojedynczym projektem migracji platformy Azure. <br/><br/> 
**Grupa** | W jednej grupie można dodać maksymalnie 35 000 serwerów.
**Ocena migracji platformy Azure** | W jednej ocenie można ocenić do 35 000 serwerów.


## <a name="other-planning-considerations"></a>Inne kwestie związane z planowaniem

- Aby rozpocząć odnajdowanie z urządzenia, należy wybrać każdy serwer fizyczny. 

## <a name="prepare-for-assessment"></a>Przygotowanie do oceny

Przygotuj platformę Azure i serwery fizyczne do oceny serwera. 

1. Sprawdź [wymagania i ograniczenia dotyczące obsługi serwera fizycznego](migrate-support-matrix-physical.md).
2. Skonfiguruj uprawnienia do interakcji z kontem Azure w celu interakcji z programem Azure Migrate.
3. Przygotuj serwery fizyczne.

Postępuj zgodnie z instrukcjami w [tym samouczku,](tutorial-prepare-physical.md) aby skonfigurować te ustawienia.

## <a name="create-a-project"></a>Tworzenie projektu

Zgodnie z wymaganiami planowania wykonaj następujące czynności:

1. Tworzenie projektu w usłudze Azure Migrate.
2. Dodaj narzędzie oceny programu Azure Migrate Server do projektów.

[Dowiedz się więcej](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Tworzenie i przeglądanie oceny

1. Tworzenie ocen dla serwerów fizycznych.
1. Przegląd ocen w ramach przygotowań do planowania migracji.

[Dowiedz się więcej](tutorial-assess-physical.md) o tworzeniu i recenzowaniu ocen.
    

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano następujące zagadnienia:
 
> [!div class="checklist"] 
> * Planowane skalowanie ocen migracji platformy Azure dla serwerów fizycznych.
> * Przygotowano platformę Azure i serwery fizyczne do oceny.
> * Utworzono projekt migracji platformy Azure i uruchomiono oceny.
> * Dokonano przeglądu ocen w ramach przygotowań do migracji.

Teraz [dowiedz się, jak](concepts-assessment-calculation.md) obliczane są oceny i jak [modyfikować oceny](how-to-modify-assessment.md).
