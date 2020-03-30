---
title: Ocena dużej liczby maszyn wirtualnych VMware do migracji na platformę Azure za pomocą usługi Azure Migrate
description: W tym artykule opisano sposób oceny dużej liczby maszyn wirtualnych VMware do migracji na platformę Azure przy użyciu usługi Azure Migrate.e
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: d404583b1bad474a5e24e8c7cf060aeb80d610bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336856"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>Ocena dużej liczby maszyn wirtualnych VMware do migracji na platformę Azure


W tym artykule opisano sposób oceny dużych liczb (1000-35 000) lokalnych maszyn wirtualnych VMware do migracji na platformę Azure przy użyciu narzędzia oceny migracji serwera azure.

[Usługa Azure Migrate](migrate-services-overview.md) udostępnia centrum narzędzi ułatwiających odnajdywanie, ocenę i migrację aplikacji, infrastruktury i obciążeń na platformę Microsoft Azure. Centrum zawiera narzędzia migracji platformy Azure i oferty niezależnych dostawców oprogramowania innych firm (ISV). 

W tym artykule omówiono sposób wykonywania następujących zadań:
> [!div class="checklist"]
> * Plan oceny na dużą skalę.
> * Skonfiguruj uprawnienia platformy Azure i przygotuj VMware do oceny.
> * Utwórz projekt migracji platformy Azure i utwórz ocenę.
> * Przejrzyj ocenę zgodnie z planem migracji.


> [!NOTE]
> Jeśli chcesz wypróbować dowód koncepcji, aby ocenić kilka maszyn wirtualnych przed oceną na dużą skalę, postępuj zgodnie z naszą [serią samouczków](tutorial-prepare-vmware.md)

## <a name="plan-for-assessment"></a>Plan oceny

Planując ocenę dużej liczby maszyn wirtualnych VMware, jest kilka rzeczy, o których trzeba pomyśleć:

- **Planowanie projektów migracji platformy Azure:** dowiedz się, jak wdrożyć projekty migracji platformy Azure. Jeśli na przykład centra danych znajdują się w różnych lokalizacjach geograficznych lub musisz przechowywać metadane odnajdywania, oceny lub migracji w innej lokalizacji geograficznej, może być konieczne wiele projektów. 
- **Planowanie urządzeń:** Usługa Azure Migrate używa lokalnego urządzenia migracji platformy Azure, wdrożonego jako maszyna wirtualna VMware, do ciągłego odnajdywać maszyny wirtualne. Urządzenie monitoruje zmiany w środowisku, takie jak dodawanie maszyn wirtualnych, dysków lub kart sieciowych. Wysyła również metadane i dane dotyczące wydajności na ich temat na platformie Azure. Musisz dowiedzieć się, ile urządzeń należy wdrożyć.
- **Planowanie kont do odnajdowania:** Urządzenie migracji platformy Azure używa konta z dostępem do serwera vCenter w celu odnajdywania maszyn wirtualnych do oceny i migracji. Jeśli odnajdujesz więcej niż 10 000 maszyn wirtualnych, skonfiguruj wiele kont.


## <a name="planning-limits"></a>Limity planowania
 
Użyj limitów podsumowanych w tej tabeli do planowania.

**Planowania** | **Limity**
--- | --- 
**Projekty migracji platformy Azure** | Ocena do 35 000 maszyn wirtualnych w projekcie.
**Urządzenie usługi Azure Migrate** | Urządzenie może odnajdować do 10 000 maszyn wirtualnych na serwerze vCenter.<br/> Urządzenie może łączyć się tylko z jednym serwerem vCenter.<br/> Urządzenie można skojarzyć tylko z jednym projektem migracji platformy Azure.<br/>  Dowolna liczba urządzeń może być skojarzona z pojedynczym projektem migracji platformy Azure. <br/><br/> 
**Grupa** | W jednej grupie można dodać maksymalnie 35 000 maszyn wirtualnych.
**Ocena migracji platformy Azure** | W jednej ocenie można ocenić do 35 000 maszyn wirtualnych.

Mając na uwadze te ograniczenia, oto kilka przykładowych wdrożeń:


**Serwer vCenter** | **Maszyny wirtualne na serwerze** | **Zalecenie** | **Akcja**
---|---|---
Jeden | < 10 tys. | Jeden projekt migracji platformy Azure.<br/> Jedno urządzenie.<br/> Jedno konto vCenter do odnajdowania. | Skonfiguruj urządzenie, połącz się z serwerem vCenter Server za pomocą konta.
Jeden | > 10 tys. | Jeden projekt migracji platformy Azure.<br/> Wiele urządzeń.<br/> Wiele kont vCenter. | Skonfiguruj urządzenie na każde 10 000 maszyn wirtualnych.<br/><br/> Skonfiguruj konta vCenter i podziel zapasy, aby ograniczyć dostęp do konta do mniej niż 10 000 maszyn wirtualnych.<br/> Połącz każde urządzenie z serwerem vCenter z kontem.<br/> Można analizować zależności między komputerami, które są odnalezione za pomocą różnych urządzeń.
Wiele | < 10 tys. |  Jeden projekt migracji platformy Azure.<br/> Wiele urządzeń.<br/> Jedno konto vCenter do odnajdowania. | Skonfiguruj urządzenia, połącz się z serwerem vCenter Server za pomocą konta.<br/> Można analizować zależności między komputerami, które są odnalezione za pomocą różnych urządzeń.
Wiele | > 10 tys. | Jeden projekt migracji platformy Azure.<br/> Wiele urządzeń.<br/> Wiele kont vCenter. | Jeśli odnajdowanie serwera vCenter < 10 000 maszyn wirtualnych, skonfiguruj urządzenie dla każdego serwera vCenter.<br/><br/> Jeśli odnajdywanie serwera vCenter > 10 000 maszyn wirtualnych, skonfiguruj urządzenie dla każdego 10 000 maszyn wirtualnych.<br/> Skonfiguruj konta vCenter i podziel zapasy, aby ograniczyć dostęp do konta do mniej niż 10 000 maszyn wirtualnych.<br/> Połącz każde urządzenie z serwerem vCenter z kontem.<br/> Można analizować zależności między komputerami, które są odnalezione za pomocą różnych urządzeń.


## <a name="plan-discovery-in-a-multi-tenant-environment"></a>Planowanie odnajdowania w środowisku wielodostępnym

Jeśli planujesz środowisko wielodostępne, można zakres odnajdywania na serwerze vCenter.

- Zakres odnajdowania urządzeń można ustawić na centra danych vCenter Server, klastry lub foldery klastrów, hostów lub folderów hostów lub poszczególnych maszyn wirtualnych.
- Jeśli środowisko jest współużytkowane przez dzierżawców i chcesz odnajdywać każdą dzierżawę oddzielnie, można uzyskać dostęp do konta vCenter, którego urządzenie używa do odnajdowania. 
    - Można mieć do zakresu przez foldery maszyn wirtualnych, jeśli dzierżawcy współużytkować hosty. Usługa Azure Migrate nie może odnajdywać maszyn wirtualnych, jeśli konto vCenter ma dostęp udzielony na poziomie folderu vCenter VM. Jeśli szukasz zakresu odnajdywania przez foldery maszyn wirtualnych, można to zrobić, zapewniając, że konto vCenter ma dostęp tylko do odczytu przypisany na poziomie maszyny Wirtualnej. [Dowiedz się więcej](set-discovery-scope.md).

## <a name="prepare-for-assessment"></a>Przygotowanie do oceny

Przygotuj platformę Azure i VMware do oceny serwera. 

1. Sprawdź [wymagania i ograniczenia dotyczące obsługi VMware](migrate-support-matrix-vmware.md).
2. Skonfiguruj uprawnienia do interakcji z kontem Azure w celu interakcji z programem Azure Migrate.
3. Przygotuj VMware do oceny.

Postępuj zgodnie z instrukcjami w [tym samouczku,](tutorial-prepare-vmware.md) aby skonfigurować te ustawienia.


## <a name="create-a-project"></a>Tworzenie projektu

Zgodnie z wymaganiami planowania wykonaj następujące czynności:

1. Tworzenie projektów migracji platformy Azure.
2. Dodaj narzędzie oceny programu Azure Migrate Server do projektów.

[Dowiedz się więcej](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Tworzenie i przeglądanie oceny

1. Tworzenie ocen dla maszyn wirtualnych VMware.
1. Przegląd ocen w ramach przygotowań do planowania migracji.


Postępuj zgodnie z instrukcjami w [tym samouczku,](tutorial-assess-vmware.md) aby skonfigurować te ustawienia.
    

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano następujące zagadnienia:
 
> [!div class="checklist"] 
> * Planowane skalowanie ocen migracji platformy Azure dla maszyn wirtualnych VMware
> * Przygotowano platformę Azure i VMware do oceny
> * Utworzono projekt migracji platformy Azure i uruchomiono oceny
> * Dokonano przeglądu ocen w ramach przygotowań do migracji.

Teraz [dowiedz się, jak](concepts-assessment-calculation.md) obliczane są oceny i jak [modyfikować oceny](how-to-modify-assessment.md).
