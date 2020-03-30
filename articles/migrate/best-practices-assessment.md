---
title: Najważniejsze wskazówki dotyczące oceny w ocenie serwera migracji platformy Azure
description: Porady dotyczące tworzenia ocen za pomocą oceny serwera migracji usługi Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: de6953b6648613595bc9975b17941b3a453a6d60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185979"
---
# <a name="best-practices-for-creating-assessments"></a>Najważniejsze wskazówki dotyczące tworzenia ocen

[Usługa Azure Migrate](migrate-overview.md) udostępnia centrum narzędzi ułatwiających odnajdywanie, ocenę i migrację aplikacji, infrastruktury i obciążeń na platformę Microsoft Azure. Centrum zawiera narzędzia migracji platformy Azure i oferty niezależnych dostawców oprogramowania innych firm (ISV).

W tym artykule podsumowano najlepsze rozwiązania podczas tworzenia ocen przy użyciu narzędzia oceny programu Azure Migrate Server.

## <a name="about-assessments"></a>Informacje o ocenach

Oceny utworzone za pomocą oceny serwera migracji platformy Azure są migawką danych w czasie. Istnieją dwa typy ocen w usłudze Azure Migrate.

**Typ oceny** | **Szczegóły** | **Dane**
--- | --- | ---
**Oparte na wydajności** | Oceny, które wytęzają zalecenia na podstawie zebranych danych dotyczących wydajności | Zalecenie rozmiaru maszyny Wirtualnej jest oparte na danych wykorzystania procesora CPU i pamięci.<br/><br/> Zalecenie typu dysku (standardowe dyski HDD/SSD lub dyski zarządzane w wersji premium) opiera się na we/wy i przepływności dysków lokalnych.
**Jako jest w środowisku lokalnym** | Oceny, które nie używają danych o wydajności do zaleceń. | Zalecenie dotyczące rozmiaru maszyny Wirtualnej jest oparte na lokalnym rozmiarze maszyny Wirtualnej<br/><br> Zalecany typ dysku jest oparty na tym, co można wybrać w ustawieniu typu magazynu dla oceny.

### <a name="example"></a>Przykład
Na przykład, jeśli masz lokalną maszynę wirtualną z czterema rdzeniami przy 20% wykorzystaniu i pamięcią 8 GB z wykorzystaniem 10%, oceny będą następujące:

- **Ocena oparta na wynikach:**
    - Identyfikuje efektywne rdzenie i pamięć na podstawie wykorzystania rdzeni (4 x 0,20 = 0,8) i pamięci (8 GB x 0,10 = 0,8).
    - Stosuje współczynnik komfortu określony we właściwościach oceny (powiedzmy 1,3x), aby uzyskać wartości, które mają być używane do zmiany rozmiaru. 
    - Zaleca najbliższy rozmiar maszyny Wirtualnej na platformie Azure, który może obsługiwać ~1,04 rdzeni (0,8 x 1,3) i ~1,04 GB (0,8 x 1,3) pamięci.

- **Ocena jako "jak jest w środowisku lokalnym):As-is (as-premises) assessment:**
    -  Zaleca maszynę wirtualną z czterema rdzeniami; 8 GB pamięci.

## <a name="best-practices-for-creating-assessments"></a>Najważniejsze wskazówki dotyczące tworzenia ocen

Urządzenie migracji platformy Azure stale profiluje środowisko lokalne i wysyła metadane i dane o wydajności do platformy Azure. Postępuj zgodnie z tymi najlepszymi praktykami oceny serwerów wykrytych przy użyciu urządzenia:

- **Tworzenie ocen w stanie as- is:** Oceny stanu państwa jest możliwe natychmiast po wyświetleniu maszyn w portalu migracji platformy Azure.
- **Tworzenie oceny opartej na wydajności:** Po skonfigurowaniu odnajdywania zaleca się odczekanie co najmniej jednego dnia przed uruchomieniem oceny opartej na wydajności:
    - Zbieranie danych o wydajności wymaga czasu. Oczekiwanie co najmniej jeden dzień zapewnia, że istnieje wystarczająca liczba punktów danych wydajności przed uruchomieniem oceny.
    - Podczas przeprowadzania ocen opartych na wydajności upewnij się, że profilujesz środowisko na czas trwania oceny. Na przykład jeśli tworzysz ocenę z czasem trwania wydajności ustawionym na jeden tydzień, musisz poczekać co najmniej tydzień po rozpoczęciu odnajdywania, aby wszystkie punkty danych zostały zebrane. Jeśli tego nie zrobisz, ocena nie otrzyma oceny pięciogwiazdkowej.
- **Ponowne obliczenie ocen:** ponieważ oceny są migawkami typu "punkt w czasie", nie są one automatycznie aktualizowane o najnowsze dane. Aby zaktualizować ocenę z najnowszymi danymi, należy ją ponownie obliczyć.

Postępuj zgodnie z tymi najlepszymi rozwiązaniami w zakresie oceny serwerów importowanych do usługi Azure Migrate za pośrednictwem . Plik CSV:

- **Tworzenie ocen w stanie as- is:** Oceny stanu państwa jest możliwe natychmiast po wyświetleniu maszyn w portalu migracji platformy Azure.
- **Tworzenie oceny opartej na wydajności:** pomaga to uzyskać lepszy kosztorys, zwłaszcza jeśli masz nadmiernie aprowizowana pojemność serwera lokalnie. Jednak dokładność oceny opartej na wydajności zależy od danych wydajności określonych przez ciebie dla serwerów. 
- **Ponowne obliczenie ocen:** ponieważ oceny są migawkami typu "punkt w czasie", nie są one automatycznie aktualizowane o najnowsze dane. Aby zaktualizować ocenę z najnowszymi importowanymi danymi, należy ją ponownie obliczyć.

## <a name="best-practices-for-confidence-ratings"></a>Najważniejsze wskazówki dotyczące oceny zaufania

Podczas przeprowadzania ocen opartych na wydajności ocena zaufania od 1 gwiazdki (najniższa) do 5-gwiazdkowej (najwyższa) jest przyznawana do oceny. Aby skutecznie używać ocen ufności:
- Ocena serwera migracji usługi Azure wymaga danych wykorzystania procesora/pamięci maszyny Wirtualnej.
- Dla każdego dysku podłączonego do lokalnej maszyny Wirtualnej wymaga odczytu/zapisu danych We/Wy/przepływności.
- Dla każdej karty sieciowej podłączonej do maszyny Wirtualnej potrzebne są dane wyjętej/wychodzącej sieci.

W zależności od procentu punktów danych dostępnych dla wybranego czasu trwania, ocena zaufania dla oceny jest podana w podsumowaniu w poniższej tabeli.

   **Dostępność punktów danych** | **Ocena zaufania**
   --- | ---
   0%–20% | 1 gwiazdka
   21%–40% | 2 gwiazdki
   41%–60% | 3 gwiazdki
   61%–80% | 4 gwiazdki
   81%–100% | 5 gwiazdek


## <a name="common-assessment-issues"></a>Wspólne problemy z oceną

Oto jak rozwiązać niektóre typowe problemy ze środowiskiem, które wpływają na oceny.

###  <a name="out-of-sync-assessments"></a>Oceny po braku synchronizacji

Jeśli po utworzeniu oceny utworzysz lub usuniesz maszyny z grupy, utworzona ocena zostanie oznaczona **jako niezsynchronizowana.** Uruchom ponownie ocenę **(Ponowne obliczenie),** aby odzwierciedlić zmiany w grupie.

### <a name="outdated-assessments"></a>Nieaktualne oceny

Jeśli istnieją lokalne zmiany maszyn wirtualnych, które znajdują się w grupie, która została poddana ocenie, ocena jest oznaczona jako **przestarzała**. Aby odzwierciedlić zmiany, uruchom ponownie ocenę.

### <a name="low-confidence-rating"></a>Niska ocena zaufania

Ocena może nie mieć wszystkich punktów danych z kilku powodów:

- Nie profilujesz swojego środowiska przez czas trwania, dla którego tworzysz ocenę. Na przykład jeśli tworzysz *ocenę opartą na wydajności* z czasem trwania wydajności ustawionym na jeden tydzień, musisz poczekać co najmniej tydzień po rozpoczęciu odnajdywania dla wszystkich punktów danych, aby uzyskać zebrane. Zawsze możesz kliknąć **ponownie oblicz,** aby zobaczyć najnowszą odpowiednią ocenę zaufania. Ocena ufności ma zastosowanie tylko podczas tworzenia oceny *opartej na wydajności.*

- Kilka maszyn wirtualnych zostało wyłączonych w czasie, dla którego jest obliczana ocena. Jeśli niektóre maszyny wirtualne zostały odłączona od zasilania na pewien czas, narzędzie Server Assessment nie będzie mogło zebrać danych o wydajności dla tego okresu.

- Kilka maszyn wirtualnych zostało utworzonych po uruchomieniu odnajdywania w narzędziu Server Assessment. Jeśli na przykład tworzysz ocenę dla historii wydajności za ostatni miesiąc, ale kilka maszyn wirtualnych zostało utworzonych w środowisku tylko tydzień temu. W tym przypadku dane wydajności dla nowych maszyn wirtualnych nie będą dostępne przez cały czas trwania i ocena ufności będzie niska.


## <a name="next-steps"></a>Następne kroki

- [Dowiedz się,](concepts-assessment-calculation.md) jak obliczane są oceny.
- [Dowiedz się,](how-to-modify-assessment.md) jak dostosować ocenę.
