---
title: Najlepsze rozwiązania dotyczące tworzenia oceny wraz z oceną serwera migracji platformy Azure | Dokumentacja firmy Microsoft
description: Zawiera wskazówki dotyczące tworzenia oceny wraz z oceną serwera migracji platformy Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: d417efd4abf14247af171ea77b479f590e14fe76
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812976"
---
# <a name="best-practices-for-creating-assessments"></a>Najlepsze rozwiązania dotyczące tworzenia oceny

[Usługa Azure Migrate](migrate-overview.md) udostępnia Centrum narzędzi, które ułatwiają odnajdowanie, oceny i migracji aplikacji, infrastruktury i obciążeń w systemie Microsoft Azure. Centrum obejmuje usługę Azure Migrate narzędzi i ofert Niezależnym dostawcą oprogramowania niezależnie od innych firm. 

Ten artykuł zawiera podsumowanie najlepszych rozwiązań, podczas tworzenia oceny za pomocą narzędzia oceny Server migracji platformy Azure. 

## <a name="about-assessments"></a>Temat ocen

Oceny utworzonymi za pomocą usługi Azure migracji serwera oceny są migawkę punktu w czasie danych. Istnieją dwa typy ocen w usłudze Azure Migrate.

**Typ oceny** | **Szczegóły** | **Dane**
--- | --- | ---
**Na podstawie wydajności** | Oceny, które wydają zalecenia na podstawie danych wydajności zebranych | Zalecenie dotyczące rozmiaru maszyny Wirtualnej jest oparty na danych użycia procesora CPU i pamięci.<br/><br/> Zalecenie typ dysku (standardowa lub premium dysków zarządzanych) opiera się na operacje We/Wy i przepływność dysków w środowisku lokalnym.
**Jako — lokalnie** | Ocen, które nie używają danych wydajności zaleceń. | Zalecenie dotyczące rozmiaru maszyny Wirtualnej zależy od rozmiaru maszyny Wirtualnej w środowisku lokalnym<br/><br> Typ dysku zalecane jest oparty na wybrania w typ magazynu, ustawiając dla oceny.

### <a name="example"></a>Przykład
Na przykład jeśli masz lokalnej maszyny Wirtualnej za pomocą cztery rdzenie na poziomie 20% i 8 GB pamięci z wykorzystaniem 10% oceny będzie następująca:

- **Oceny na podstawie wydajności**:
    - Zaleca się rdzeni i pamięci na podstawie podstawowej (rdzenie 0,8) i wykorzystanie pamięci (0,8 GB).
    - Ocena stosuje się domyślny współczynnik komfortu, 30%.
    - Zalecenie dotyczące maszyny Wirtualnej: ~1.4 rdzeni (0,8 x1.3) i ~1.4 GB pamięci.
- **Jako — jest ocena (lokalnie)** :
    -  Zaleca się maszyna wirtualna o cztery rdzenie; 8 GB pamięci.

## <a name="best-practices-for-creating-assessments"></a>Najlepsze rozwiązania dotyczące tworzenia oceny

Urządzenie Azure Migrate stale profilują Twoje środowisko lokalne i wysyła dane metadanych i wydajności na platformie Azure. Wykonaj te najlepsze rozwiązania dotyczące tworzenia oceny:

- **Utwórz jako — jest ocen**: Możesz utworzyć jako — jest ocen natychmiast po odnalezieniu.
- **Utwórz ocenę na podstawie wydajności**: Po skonfigurowaniu odnajdowania, firma Microsoft zaleca, aby poczekać co najmniej dzień przed uruchomieniem oceny na podstawie wydajności:
    - Zbieranie danych o wydajności jest czasochłonne. Oczekiwanie na co najmniej jeden dzień zapewnia, że ma za mało punktów danych wydajności przed uruchomieniem oceny.
    - Dane dotyczące wydajności urządzenie zbiera punktów danych w czasie rzeczywistym, co 20 sekund dla każdego metryki wydajności i gromadzi informacje o ich do pojedynczego 5 minutową punktu danych. Urządzenie wysyła punktu danych 5 minut na platformie Azure co godzinę do obliczenia oceny.  
- **Pobierz najnowsze dane**: Oceny nie są automatycznie aktualizowane przy użyciu najnowszych danych. Aby zaktualizować oceny przy użyciu najnowszych danych, należy ponownie ją uruchomić. 
- **Upewnij się, czasu trwania zgodny**: Jeśli korzystasz z oceny na podstawie wydajności, upewnij się, Twój profil środowiska czas oceny. Na przykład jeśli tworzysz ocenę o czasie trwania wydajności ustawiany na jeden tydzień, należy poczekać co najmniej jednego tygodnia, po uruchomieniu odnajdywania dla wszystkich punktów danych mają być zbierane. Jeśli nie, oceny nie będą otrzymywać przyznanie pięciu gwiazdek. 
- **Należy unikać brakujących punktów danych**: Brak punktów danych w ramach oceny na podstawie wydajności może spowodować następujące problemy:
    - Maszyny wirtualne są wyłączone podczas przeprowadzania oceny i nie zostaną zebrane dane dotyczące wydajności. 
    - Jeśli tworzysz maszyny wirtualne w miesiącu, na którym opiera się historii wydajności. dane dla tych maszyn wirtualnych będą mniejsze niż miesiąc. 
    - Ocena zostanie utworzona natychmiast po odnalezieniu lub czas oceny nie jest zgodna czas zbierania danych wydajności.

## <a name="best-practices-for-confidence-ratings"></a>Najlepsze rozwiązania dotyczące oceny zaufania

Podczas wykonywania oceny na podstawie wydajności otrzymał ufności ocena (najniższe) od 1 gwiazdki do 5 gwiazdek (najwyższy) do oceny. Używać klasyfikacji ufności skutecznie:
- Azure oceny migracji serwera potrzebuje danych użycia Procesora/pamięci maszyny Wirtualnej i dysku na SEKUNDĘ lub przepływności danych.
- Dla poszczególnych kart sieciowych dołączonych do maszyny Wirtualnej z systemem Azure Migrate potrzebuje danych we/wy sieci.
- Jeśli wykorzystanie danych jest niedostępna w programie vCenter Server, zalecenie dotyczące rozmiaru określane przez usługę Azure Migrate może nie być wiarygodne. 

W zależności od wartości procentowej dostępnych punktów danych oceny zaufania dla oceny są podsumowane w poniższej tabeli.

   **Dostępność punktu danych.** | **Ocenę zaufania**
   --- | ---
   0%–20% | 1 gwiazdka
   21%–40% | 2 gwiazdki
   41%–60% | 3 gwiazdki
   61%–80% | 4 gwiazdki
   81%–100% | 5 gwiazdek

- Jeśli pojawi się oceną zaufania dla oceny, który znajduje się poniżej pięć gwiazdek, poczekać co najmniej jeden dzień i następnie ponownie obliczyć ocenę.
- Niską ocenę oznacza, że zalecenia dotyczące rozmiaru może nie być wiarygodne. W tym przypadku zaleca się zmodyfikowanie właściwości oceny do użycia jako — jest ocena w środowisku lokalnym.

## <a name="common-assessment-issues"></a>Typowe problemy z oceną

Poniżej przedstawiono sposób rozwiązać niektóre typowe problemy środowiska, które wpływają na ocen.

###  <a name="out-of-sync-assessments"></a>Poza synchronizacja ocen

Dodaj lub usuń maszyny z grupy, po utworzeniu oceny, zostaną oznaczone jako oceny utworzone **poza synchronizacja**. Uruchom ponownie ocenę (**ponownie Oblicz**) aby odzwierciedlały zmiany w grupie.

### <a name="outdated-assessments"></a>Nieaktualne ocen

W przypadku zmian lokalnych maszyn wirtualnych znajdujących się w grupie, która jest oceniana jest oznaczony oceny **nieaktualne**. Aby odzwierciedlić zmiany, uruchom ponownie ocenę.

### <a name="missing-data-points"></a>Brak punktów danych

Ocena może nie mieć wszystkich punktów danych z kilku powodów:

- Maszyny wirtualne, może być wyłączone podczas przeprowadzania oceny i nie zostaną zebrane dane dotyczące wydajności. 
- Maszyny wirtualne mogły być utworzone w miesiącu podstawą historii wydajności, które, dlatego ich dane wydajności jest krótszy niż miesiąc. 
- Ocena została utworzona natychmiast po odnalezieniu. W celu zbierania danych wydajności przez określony przedział czasu, należy poczekać na określoną ilość czasu przed uruchomieniem oceny. Na przykład jeśli chcesz ocenić dane dotyczące wydajności w każdym tygodniu, musisz odczekaj tydzień po odnalezieniu. W przeciwnym razie oceny nie będą otrzymywać przyznanie pięciu gwiazdek. 


## <a name="next-steps"></a>Następne kroki

- [Dowiedz się,](concepts-assessment-calculation.md) sposobu obliczania ocen.
- [Dowiedz się,](how-to-modify-assessment.md) sposobu dostosowywania ocenę.
