---
title: Ocena dużej liczby maszyn wirtualnych funkcji Hyper-V do migracji na platformę Azure za pomocą usługi Azure Migrate | Dokumentacja firmy Microsoft
description: Opisuje sposób oceny dużej liczby maszyn wirtualnych funkcji Hyper-V pod kątem migracji do platformy Azure przy użyciu usługi Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: 95704f2694892b349d0967fca2160dabd990b472
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811546"
---
# <a name="assess-large-numbers-of-hyper-v-vms-for-migration-to-azure"></a>Ocena dużej liczby maszyn wirtualnych funkcji Hyper-V pod kątem migracji do platformy Azure

W tym artykule opisano, jak do oceny dużą (> 1000) lokalnych maszyn wirtualnych funkcji Hyper-V pod kątem migracji do platformy Azure, za pomocą narzędzia oceny Server migracji platformy Azure.

[Usługa Azure Migrate](migrate-services-overview.md) udostępnia Centrum narzędzi, które ułatwiają odnajdowanie, oceny i migracji aplikacji, infrastruktury i obciążeń w systemie Microsoft Azure. Centrum obejmuje usługę Azure Migrate narzędzi i ofert Niezależnym dostawcą oprogramowania niezależnie od innych firm. 


W tym artykule omówiono sposób wykonywania następujących zadań:
> [!div class="checklist"]
> * Plan oceny na dużą skalę.
> * Skonfiguruj uprawnienia platformy Azure, a przygotowywanie oceny funkcji Hyper-V.
> * Utwórz projekt usługi Azure Migrate, a następnie utwórz ocenę.
> * Przejrzyj oceny podczas planowania migracji.


> [!NOTE]
> Jeśli chcesz wypróbować od koncepcji do oceny kilku maszyn wirtualnych przed oceny na dużą skalę, postępuj zgodnie z naszym [serii samouczków](tutorial-prepare-hyper-v.md)

## <a name="plan-for-assessment"></a>Plan oceny

Planując oceny dużej liczby maszyn wirtualnych funkcji Hyper-V, istnieje kilka rzeczy, które należy przemyśleć:

- **Planowanie projektów usługi Azure Migrate**: Zorientować się, jak wdrożyć usługę Azure Migrate projektów. Na przykład jeśli swoje centra danych znajdują się w różnych lokalizacjach geograficznych lub chcesz przechować wykrywania, oceny lub metadane dotyczące migracji w innej lokalizacji geograficznej, możesz potrzebować wielu projektów.
- **Plan urządzenia**: Usługa Azure Migrate używa urządzenie Azure Migrate w środowisku lokalnym, wdrożone jako maszyny Wirtualne funkcji Hyper-V, aby stale odnalezienia maszyn wirtualnych do oceny i migracji. Urządzenie monitoruje zmiany środowiska, takie jak dodawanie maszyn wirtualnych, dysków lub kart sieciowych. Wysyła również metadanych i wydajności danych dotyczących ich na platformę Azure. Należy sprawdzić, ile urządzeń do wdrożenia.


## <a name="planning-limits"></a>Planowanie limity
 
Użyj ograniczeń podsumowane w poniższej tabeli do planowania.

**Planowanie** | **Limity**
--- | --- 
**Projekty migracji platformy Azure** | Ocenianie maksymalnie 10 000 maszyn wirtualnych w projekcie.
**Urządzenie Azure Migrate** | Urządzenie można odnajdywać maksymalnie 5000 maszyn wirtualnych.<br/> Urządzenia mogą łączyć się z maksymalnie 300 hosty funkcji Hyper-V.<br/> Urządzenie może być skojarzony tylko z pojedynczego projektu Azure Migrate.<br/><br/> 
**Usługa Azure Migrate oceny** | Można oceniać gotowość do 10 000 maszyn wirtualnych w ramach pojedynczej oceny.



## <a name="other-planning-considerations"></a>Inne zagadnienia dotyczące planowania

- Aby rozpocząć odnajdowanie przez urządzenie, musisz wybrać każdy host funkcji Hyper-V. 
- Jeśli pracujesz w środowisku z wieloma dzierżawami, nie można obecnie odnaleźć tylko te maszyny wirtualne, które należą do określonej dzierżawy. 

## <a name="prepare-for-assessment"></a>Przygotowywanie oceny

Przygotowywanie platformy Azure i funkcji Hyper-V server oceny. 

1. Sprawdź [wymaganiami dotyczącymi obsługi funkcji Hyper-V i ograniczenia](migrate-support-matrix-hyper-v.md).
2. Skonfiguruj uprawnienia dla konta platformy Azure korzystać z usługi Azure Migrate
3. Przygotowanie hostów funkcji Hyper-V i maszyn wirtualnych

Postępuj zgodnie z instrukcjami w [w tym samouczku](tutorial-prepare-hyper-v.md) skonfigurować te ustawienia.

## <a name="create-a-project"></a>Tworzenie projektu

Zgodnie z wymaganiami planowania wykonaj następujące czynności:

1. Twórz projekty usługi Azure Migrate.
2. Dodaj narzędzia do oceny migracji serwera w Azure, do projektów.

[Dowiedz się więcej](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Tworzenie i przeglądanie oceny

1. Utworzyć oceny dla maszyn wirtualnych funkcji Hyper-V.
1. Przegląd ocen w ramach przygotowania do planowania migracji.

[Dowiedz się więcej](tutorial-assess-hyper-v.md) o tworzeniu i przeglądając ocen.
    

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano następujące zagadnienia:
 
> [!div class="checklist"] 
> * Planowanej pod kątem skalowania usługi Azure Migrate ocen dla maszyn wirtualnych funkcji Hyper-V
> * Przygotowanie platformy Azure oraz funkcji Hyper-V dla oceny
> * Utworzony projekt usługi Azure Migrate i uruchomiono ocen
> * Przeglądu oceny w ramach przygotowania do migracji.

Teraz [Dowiedz się, jak](concepts-assessment-calculation.md) obliczania ocen i jak [modyfikowanie oceny](how-to-modify-assessment.md)
