---
title: Ocena dużej liczby maszyn wirtualnych programu VMware do migracji na platformę Azure za pomocą usługi Azure Migrate | Dokumentacja firmy Microsoft
description: Opisuje sposób oceny dużej liczby maszyn wirtualnych programu VMware do migracji na platformę Azure za pomocą usługi Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: 6102a1c59be3627b95dc1e0cb1d1d712d5832d2f
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811338"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>Ocena dużej liczby maszyn wirtualnych programu VMware do migracji na platformę Azure


W tym artykule opisano sposób oceny dużą (35 000 1000) lokalnych maszyn wirtualnych programu VMware do migracji na platformę Azure za pomocą narzędzia oceny Server migracji platformy Azure

[Usługa Azure Migrate](migrate-services-overview.md) udostępnia Centrum narzędzi, które ułatwiają odnajdowanie, oceny i migracji aplikacji, infrastruktury i obciążeń w systemie Microsoft Azure. Centrum obejmuje usługę Azure Migrate narzędzi i ofert Niezależnym dostawcą oprogramowania niezależnie od innych firm. 

W tym artykule omówiono sposób wykonywania następujących zadań:
> [!div class="checklist"]
> * Plan oceny na dużą skalę.
> * Skonfiguruj uprawnienia platformy Azure i przygotowywanie wdrożenia oprogramowania VMware do oceny.
> * Utwórz projekt usługi Azure Migrate, a następnie utwórz ocenę.
> * Przejrzyj oceny podczas planowania migracji.


> [!NOTE]
> Jeśli chcesz wypróbować od koncepcji do oceny kilku maszyn wirtualnych przed oceny na dużą skalę, postępuj zgodnie z naszym [serii samouczków](tutorial-prepare-vmware.md)

## <a name="plan-for-assessment"></a>Plan oceny

Planując oceny dużej liczby maszyn wirtualnych VMware, istnieje kilka rzeczy, które należy przemyśleć:

- **Planowanie projektów usługi Azure Migrate**: Zorientować się, jak wdrożyć usługę Azure Migrate projektów. Na przykład jeśli swoje centra danych znajdują się w różnych lokalizacjach geograficznych lub chcesz przechować wykrywania, oceny lub metadane dotyczące migracji w innej lokalizacji geograficznej, możesz potrzebować wielu projektów. 
- **Plan urządzenia**: Usługa Azure Migrate używa urządzenia usługi Azure Migrate w środowisku lokalnym, wdrożone jako maszyny Wirtualnej programu VMware, aby stale odnajdywanie maszyn wirtualnych. Urządzenie monitoruje zmiany środowiska, takie jak dodawanie maszyn wirtualnych, dysków lub kart sieciowych. Wysyła również metadanych i wydajności danych dotyczących ich na platformę Azure. Należy sprawdzić, ile urządzenia, zajdzie potrzeba wdrożenia.
- **Plan kont do odnajdywania**: Usługa Azure Migrate urządzenie korzysta z konta z dostępem do programu vCenter Server w celu odnalezienia maszyn wirtualnych do oceny i migracji. W przypadku odnajdywania ponad 10 000 maszyn wirtualnych, należy skonfigurować wiele kont.


## <a name="planning-limits"></a>Planowanie limity
 
Użyj ograniczeń podsumowane w poniższej tabeli do planowania.

**Planowanie** | **Limity**
--- | --- 
**Projekty migracji platformy Azure** | Ocenianie maksymalnie 35 000 maszyn wirtualnych w projekcie.
**Urządzenie Azure Migrate** | Urządzenie może łączyć się tylko z jednego serwera vCenter.<br/><br/> Urządzenie może być skojarzony tylko z pojedynczego projektu Azure Migrate.<br/> Urządzenie można odnajdywać maksymalnie 10 000 maszyn wirtualnych w programie vcenter Server.
**Usługa Azure Migrate oceny** | Można oceniać gotowość do 35 000 maszyn wirtualnych w ramach pojedynczej oceny.

Te limity, pamiętając Oto kilka przykładowych wdrożeń:


**Serwer vCenter** | **Maszyna wirtualna na serwerze** | **Zalecenie** | **Akcja**
---|---|---
Jeden | < 10,000 | Jeden projekt usługi Azure Migrate.<br/> Jednego urządzenia.<br/> Jedno konto vCenter dla odnajdywania. | Skonfiguruj urządzenie, Połącz z programem vCenter Server przy użyciu konta.
Jeden | > 10,000 | Jeden projekt usługi Azure Migrate.<br/> Wiele urządzeń.<br/> Wiele kont vCenter. | Skonfiguruj urządzenie, co 10 000 maszyn wirtualnych.<br/><br/> Konfigurowanie kont vCenter i dzielenia magazynu do ograniczania dostępu dla konta usługi do mniej niż 10 000 maszyn wirtualnych.<br/> Programem vCenter server przy użyciu konta, należy połączyć z każdego urządzenia.<br/> Możesz analizować zależności między maszynami, które zostały wykryte przy użyciu różnych urządzeń.
Wiele | < 10,000 |  Jeden projekt usługi Azure Migrate.<br/> Wiele urządzeń.<br/> Jedno konto vCenter dla odnajdywania. | Konfigurowanie urządzeń, Połącz z programem vCenter Server przy użyciu konta.<br/> Możesz analizować zależności między maszynami, które zostały wykryte przy użyciu różnych urządzeń.
Wiele | > 10,000 | Jeden projekt usługi Azure Migrate.<br/> Wiele urządzeń.<br/> Wiele kont vCenter. | Jeśli odnajdowania programu vCenter Server w przypadku maszyn wirtualnych < 10 000 operacji Skonfiguruj urządzenie, dla każdego serwera vCenter.<br/><br/> Jeśli odnajdowania programu vCenter Server > 10 000 maszyn wirtualnych, skonfigurować urządzenie dla każdej 10 000 maszyn wirtualnych.<br/> Konfigurowanie kont vCenter i dzielenia magazynu do ograniczania dostępu dla konta usługi do mniej niż 10 000 maszyn wirtualnych.<br/> Programem vCenter server przy użyciu konta, należy połączyć z każdego urządzenia.<br/> Możesz analizować zależności między maszynami, które zostały wykryte przy użyciu różnych urządzeń.


## <a name="plan-discovery-in-a-multi-tenant-environment"></a>Planowanie odnajdywania w środowisku z wieloma dzierżawami

Jeśli planowane jest w przypadku środowiska z wieloma dzierżawami, można określić zakres odnajdywania w programie vcenter Server.

- Możesz ustawić zakres odnajdywania urządzenia do centrum danych z serwera vCenter, klastra lub folderze folder hostów lub poszczególne maszyny wirtualne, host lub klastry.
- Jeśli nie chcesz odnaleźć oddzielnie każdą dzierżawę środowiska są dzielone między dzierżawami, możesz ograniczyć dostęp do konta vCenter, używanego przez urządzenie do odnajdywania. 
    - Jeśli dzierżawcy mają hostów, Utwórz poświadczenia z dostępem tylko do odczytu dla maszyn wirtualnych, które należą do określonej dzierżawy. 
    - Te poświadczenia służą do odnajdywania urządzenia usługi Azure Migrate.
    - Usługa Azure Migrate oceny nie można odnaleźć maszyn wirtualnych, jeśli konto vCenter ma dostęp udzielany na poziomie folderu vCenter maszyny Wirtualnej. Foldery hostów i klastrów są obsługiwane. 

## <a name="prepare-for-assessment"></a>Przygotowywanie oceny

Przygotowywanie platformy Azure i programu VMware server oceny. 

1. Sprawdź [VMware obsługują wymagania i ograniczenia](migrate-support-matrix-vmware.md).
2. Skonfiguruj uprawnienia dla konta platformy Azure korzystać z usługi Azure Migrate.
3. Przygotowywanie wdrożenia oprogramowania VMware do oceny.


Postępuj zgodnie z instrukcjami w [w tym samouczku](tutorial-prepare-vmware.md) skonfigurować te ustawienia.


## <a name="create-a-project"></a>Tworzenie projektu

Zgodnie z wymaganiami planowania wykonaj następujące czynności:

1. Twórz projekty usługi Azure Migrate.
2. Dodaj narzędzia do oceny migracji serwera w Azure, do projektów.

[Dowiedz się więcej](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Tworzenie i przeglądanie oceny

1. Utworzyć oceny dla maszyn wirtualnych VMware.
1. Przegląd ocen w ramach przygotowania do planowania migracji.


Postępuj zgodnie z instrukcjami w [w tym samouczku](tutorial-assess-vmware.md) skonfigurować te ustawienia.
    

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano następujące zagadnienia:
 
> [!div class="checklist"] 
> * Planowanej pod kątem skalowania usługi Azure Migrate ocen dla maszyn wirtualnych VMware
> * Przygotowany platformy Azure i programu VMware do oceny
> * Utworzony projekt usługi Azure Migrate i uruchomiono ocen
> * Przeglądu oceny w ramach przygotowania do migracji.

Teraz [Dowiedz się, jak](concepts-assessment-calculation.md) obliczania ocen i jak [modyfikowanie oceny](how-to-modify-assessment.md).
