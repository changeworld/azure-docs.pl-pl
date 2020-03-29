---
title: Tworzenie oceny za pomocą oceny serwera migracji platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób tworzenia oceny za pomocą narzędzia Oceny programu Azure Migrate Server
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: cffde2a677650387dffd19733e082ff7002ccb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68229104"
---
# <a name="create-an-assessment"></a>Tworzenie oceny

W tym artykule opisano sposób tworzenia oceny lokalnych maszyn wirtualnych VMware lub maszyn wirtualnych funkcji Hyper-V za pomocą usługi Azure Migrate: Server Assessment.

[Usługa Azure Migrate](migrate-services-overview.md) ułatwia migrację na platformę Azure. Usługa Azure Migrate udostępnia scentralizowane centrum do śledzenia odnajdowania, oceny i migracji lokalnej infrastruktury, aplikacji i danych na platformę Azure. Centrum udostępnia narzędzia platformy Azure do oceny i migracji, a także oferty niezależnych dostawców oprogramowania innych firm (ISV). 

## <a name="before-you-start"></a>Przed rozpoczęciem

- Upewnij się, że [utworzono](how-to-add-tool-first-time.md) projekt migracji platformy Azure.
- Jeśli projekt został już utworzony, upewnij się, że [dodano](how-to-assess.md) narzędzie do oceny migracji: serwera platformy Azure.
- Aby utworzyć ocenę, należy skonfigurować urządzenie migracji platformy Azure dla [vmware](how-to-set-up-appliance-vmware.md) lub [funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md). Urządzenie odnajduje maszyny lokalne i wysyła metadane i dane wydajności do usługi Azure Migrate: Server Assessment. [Dowiedz się więcej](migrate-appliance.md).


## <a name="assessment-overview"></a>Przegląd oceny
Istnieją dwa typy ocen, które można utworzyć za pomocą usługi Azure Migrate: Server Assessment.

**Ocena** | **Szczegóły** | **Dane**
--- | --- | ---
**Oparte na wydajności** | Oceny na podstawie zebranych danych dotyczących wydajności | **Zalecany rozmiar maszyny Wirtualnej:** Na podstawie danych wykorzystania procesora i pamięci.<br/><br/> **Zalecany typ dysku (dysk zarządzany standardowy lub premium):** na podstawie usług We/Wy i przepływności dysków lokalnych.
**Jako lokalnie** | Oceny oparte na rozmiarze lokalnym. | **Zalecany rozmiar maszyny Wirtualnej:** Na podstawie lokalnego rozmiaru maszyny Wirtualnej<br/><br> **Zalecany typ dysku:** Na podstawie ustawienia typu magazynu wybranego dla oceny.

[Dowiedz się więcej](concepts-assessment-calculation.md) o ocenach.

## <a name="run-an-assessment"></a>Uruchamianie oceny

Przeprowadzić ocenę w następujący sposób:

1. Zapoznaj się z [najlepszymi rozwiązaniami w](best-practices-assessment.md) zakresie tworzenia ocen.
2. Na karcie **Serwery** w obszarze **Migracja platformy Azure:** kafelek Ocena serwera kliknij pozycję **Oceń**.

    ![Ocena](./media/how-to-create-assessment/assess.png)

2. W **obszarze Oceń serwery**określ nazwę oceny.
3. Kliknij pozycję **Wyświetl wszystko**, aby sprawdzić właściwości oceny.

    ![Właściwości oceny](./media/how-to-create-assessment//view-all.png)

3. W **obszarze Zaznacz lub utwórz grupę**wybierz pozycję **Utwórz nowy**i określ nazwę grupy. Grupa gromadzi jedną lub więcej maszyn wirtualnych razem do oceny.
4. W **obszarze Dodaj maszyny do grupy**wybierz maszyny wirtualne, które mają być dodane do grupy.
5. Kliknij **przycisk Utwórz ocenę,** aby utworzyć grupę, a następnie uruchom ocenę.

    ![Tworzenie oceny](./media/how-to-create-assessment//assessment-create.png)

6. Po utworzeniu oceny wyświetl ją w **obszarze Serwery** > **Migracji platformy Azure:** > **Oceny**oceny serwera .
7. Kliknij polecenie **Eksportuj ocenę**, aby pobrać ocenę jako plik programu Excel.



## <a name="review-an-assessment"></a>Przegląd oceny

W ocenie opisano:

- **Gotowość platformy Azure:** Czy maszyny wirtualne nadają się do migracji na platformę Azure.
- **Szacowanie kosztów miesięcznych:** szacowane miesięczne koszty obliczeń i magazynu związane z uruchamianiem maszyn wirtualnych na platformie Azure.
- **Miesięczne szacowanie kosztów magazynowania:** Szacowane koszty magazynu dysków po migracji.

### <a name="view-an-assessment"></a>Wyświetlanie oceny

1. W **obszarze** >  Cele migracji**Serwery**kliknij pozycję **Oceny** w obszarze **Migracja platformy Azure: Ocena serwera**.
2. W **ocenie**kliknij na ocenę, aby ją otworzyć.

    ![Podsumowanie oceny](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Przejrzyj gotowość platformy Azure

1. W **gotowości platformy Azure**sprawdź, czy maszyny wirtualne są gotowe do migracji na platformę Azure.
2. Przejrzyj stan maszyny Wirtualnej:
    - **Gotowe do platformy Azure:** Usługa Azure Migrate zaleca rozmiar maszyny Wirtualnej i szacowane koszty dla maszyn wirtualnych w ocenie.
    - **Gotowy z warunkami:** Pokazuje problemy i sugerowane środki zaradcze.
    - **Nie jest gotowy na platformę Azure:** pokazuje problemy i sugerowane rozwiązania.
    - **Gotowość nieznany:** używane, gdy usługa Azure Migrate nie może ocenić gotowości z powodu problemów z dostępnością danych.

2. Kliknij stan **gotowości platformy Azure.** Można wyświetlić szczegóły gotowości maszyny Wirtualnej i przejść do szczegółów, aby wyświetlić szczegóły maszyny Wirtualnej, w tym ustawienia obliczeniowe, magazynu i sieci.



### <a name="review-cost-details"></a>Przejrzyj szczegóły kosztów

Ten widok zawiera szacowany koszt obliczeń i magazynu uruchamiania maszyn wirtualnych na platformie Azure.

1. Przejrzyj miesięczne koszty obliczeń i magazynu. Koszty są agregowane dla wszystkich maszyn wirtualnych w ocenianej grupie.

    - Szacunki kosztów są oparte na zaleceniach dotyczących rozmiaru komputera oraz jego dyskach i właściwościach.
    - Wyświetlane są szacowane miesięczne koszty obliczeń i magazynu.
    - Szacowanie kosztów jest do uruchamiania lokalnych maszyn wirtualnych jako maszyny wirtualne IaaS. Ocena programu Azure Migrate Server nie uwzględnia kosztów paas lub SaaS.

2. Możesz przejrzeć miesięczne szacunki kosztów magazynowania. W tym widoku są wyświetlane zagregowane koszty magazynowania dla ocenianej grupy, podzielone na różne typy dysków magazynu.
3. Można przejść do szczegółów, aby wyświetlić szczegółowe informacje dotyczące określonych maszyn wirtualnych.


### <a name="review-confidence-rating"></a>Przegląd oceny zaufania

Podczas uruchamiania ocen opartych na wydajności do oceny jest przypisywana ocena zaufania.

![Ocena zaufania](./media/how-to-create-assessment/confidence-rating.png)

- Przyznawana jest ocena od 1 gwiazdki (najniższa) do 5 gwiazdek (najwyższa).
- Ocena zaufania pomaga oszacować wiarygodność zaleceń dotyczących rozmiaru dostarczonych w ocenie.
- Ocena zaufania jest oparta na dostępności punktów danych potrzebnych do obliczenia oceny.

Oceny ufności dla oceny są następujące.

**Dostępność punktów danych** | **Ocena zaufania**
--- | ---
0%–20% | 1 gwiazdka
21%–40% | 2 gwiazdki
41%–60% | 3 gwiazdki
61%–80% | 4 gwiazdki
81%–100% | 5 gwiazdek




## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak używać [mapowania zależności](how-to-create-group-machine-dependencies.md) do tworzenia grup wysokiego zaufania.
- [Dowiedz się więcej](concepts-assessment-calculation.md) na temat sposobu obliczania ocen.
