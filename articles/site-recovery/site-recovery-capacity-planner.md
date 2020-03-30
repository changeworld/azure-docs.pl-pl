---
title: Planowanie pojemności odzyskiwania po awarii funkcji Hyper-V za pomocą usługi Azure Site Recovery
description: Ten artykuł służy do szacowania pojemności podczas konfigurowania odzyskiwania po awarii za pomocą usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 843d5da26d6791cea880e5dfb654fe27b74f5d9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73936041"
---
# <a name="plan-capacity-for-hyper-v-vm-disaster-recovery"></a>Planowanie pojemności odzyskiwania po awarii maszyny Wirtualnej funkcji Hyper-V 

[Planer wdrażania usługi Azure Site Recovery Deployment Planner] (site-recovery-hyper-v-deployment-planner.md) dla wdrożenia funkcji Hyper-V na platformie Azure zawiera następujące informacje:

* Ocena kwalifikowalności maszyn wirtualnych na podstawie liczby dysków, rozmiaru dysku, usług We/Wy, zmian i kilku cech maszyny Wirtualnej
* Zapotrzebowanie na przepustowość sieci w porównaniu z oceną celu punktu odzyskiwania
* Wymagania dotyczące infrastruktury platformy Azure
* Wymagania dotyczące infrastruktury lokalnej
* Wskazówki dotyczące dzielenia na partie replikacji początkowej
* Szacowany całkowity koszt odzyskiwania po awarii na platformie Azure


Planista pojemności odzyskiwania usługi Azure site recovery pomaga określić wymagania dotyczące pojemności podczas replikowania maszyn wirtualnych funkcji Hyper-V za pomocą usługi Azure Site Recovery.

Użyj site recovery Capacity Planner do analizowania środowiska źródłowego i obciążeń. Pomaga oszacować zapotrzebowanie na przepustowość, zasoby serwera potrzebne dla lokalizacji źródłowej oraz zasoby (takie jak maszyny wirtualne i magazyn) potrzebne w lokalizacji docelowej.

Narzędzie można uruchomić w dwóch trybach:

* **Szybkie planowanie:** Zapewnia projekcje sieciowe i serwerowe na podstawie średniej liczby maszyn wirtualnych, dysków, magazynu i szybkości zmian.
* **Szczegółowe planowanie:** Zawiera szczegółowe informacje o każdym obciążeniu na poziomie maszyny Wirtualnej. Analizowanie zgodności maszyn wirtualnych i uzyskaj projekcje sieci i serwera.

## <a name="before-you-start"></a>Przed rozpoczęciem

* Zbieranie informacji o środowisku, w tym maszyn wirtualnych, dysków na maszynę wirtualną, magazynu na dysku.
* Zidentyfikuj dzienny współczynnik zmian (zmian) dla zreplikowanych danych. Pobierz narzędzie do [planowania pojemności funkcji Hyper-V,](https://www.microsoft.com/download/details.aspx?id=39057) aby uzyskać szybkość zmian. [Dowiedz się więcej](site-recovery-capacity-planning-for-hyper-v-replication.md) na temat tego narzędzia. Zalecamy uruchomienie tego narzędzia przez tydzień w celu rejestrowania średnich.


## <a name="run-the-quick-planner"></a>Uruchamianie szybkiego planowania
1. Pobierz i otwórz [Site Recovery Capacity Planner](https://aka.ms/asr-capacity-planner-excel). Musisz uruchomić makra. Po wyświetleniu monitu należy dokonać wyboru, aby włączyć edycję i zawartość.

2. W polu listy **Wybierz typ planisty** wybierz pozycję **Szybki planista**.

   ![Wprowadzenie](./media/site-recovery-capacity-planner/getting-started.png)

3. W arkuszu **Planerka zdolności produkcyjnych** wprowadź wymagane informacje. Wypełnij wszystkie pola zakreślone na czerwono na poniższym zrzucie ekranu:

   a. W **obszarze Wybierz swój scenariusz**wybierz pozycję **Hyper-V na platformie Azure** lub **VMware/Physical na platformie Azure**.

   b. W **aplikacji Średnia dzienna szybkość zmiany danych (%)** wprowadź informacje zebrane za pomocą [narzędzia do planowania pojemności funkcji Hyper-V](site-recovery-capacity-planning-for-hyper-v-replication.md) lub [programu Site Recovery Deployment Planner](./site-recovery-deployment-planner.md).

   d. Ustawienie **Kompresja** nie jest używane podczas replikowania maszyn wirtualnych z funkcją Hyper-V na platformie Azure. Do kompresji należy użyć urządzenia innej firmy, takiego jak Riverbed.

   d. W **obszarze Przechowywanie w dniach**określ w dniach, jak długo mają być przechowywane repliki.

   e. W **liczba godzin, w których replikacja początkowa dla partii maszyn wirtualnych powinna zostać ukończona** i **Liczba maszyn wirtualnych na początkową partię replikacji**, wprowadź ustawienia, które są używane do obliczania wymagań replikacji początkowej. Po wdrożeniu usługi Site Recovery cały początkowy zestaw danych jest przekazywanie.

   ![Dane wejściowe](./media/site-recovery-capacity-planner/inputs.png)

4. Po wprowadzeniu wartości dla środowiska źródłowego wyświetlane dane wyjściowe obejmują:

   * **Przepustowość wymagana dla replikacji różnicowej (w megabitach/s):** Przepustowość sieci dla replikacji różnicowej jest obliczana na podstawie średniej dziennej szybkości zmiany danych.
   * **Przepustowość wymagana dla replikacji początkowej (w megabitach/s):** Przepustowość sieci dla replikacji początkowej jest obliczana na podstawie wprowadzonych początkowych wartości replikacji.
   * **Wymagany magazyn (w gb)**: całkowita wymagana pamięć masowa platformy Azure.
   * **Całkowita liczba we/wy we współpracy w magazynie standardowym:** Liczba jest obliczana na podstawie rozmiaru jednostki We/Wy 8K na całkowitych standardowych kontach magazynu. W przypadku szybkiego planowania liczba jest obliczana na podstawie wszystkich źródłowych dysków maszyn wirtualnych i dziennej szybkości zmiany danych. Dla planisty szczegółowe liczba jest obliczana na podstawie całkowitej liczby maszyn wirtualnych, które są mapowane do standardowych maszyn wirtualnych platformy Azure i szybkość zmiany danych na tych maszynach wirtualnych.
   * **Wymagana liczba standardowych kont magazynu:** Całkowita liczba standardowych kont magazynu potrzebnych do ochrony maszyn wirtualnych. Standardowe konto magazynu może pomieścić do 20 000 kont We/Wy na wszystkich maszynach wirtualnych w magazynie standardowym. Maksymalnie 500 we/wy są obsługiwane na dysku.
   * **Wymagana liczba dysków obiektów Blob:** Liczba dysków utworzonych w magazynie platformy Azure.
   * **Wymagana liczba kont premium:** Całkowita liczba kont magazynu w wersji premium potrzebnych do ochrony maszyn wirtualnych. Źródłowa maszyna wirtualna z wysokimi we/wy (większa niż 20 000) wymaga konta magazynu w stanie Premium. Konto magazynu w wersji premium może pomieścić do 80 000 kont We/Wy.
   * **Całkowita liczba we/wy we/wy w magazynie w wersji Premium:** Liczba jest obliczana na podstawie rozmiaru jednostki We/Wy 256K na całkowitych kontach magazynu premium. W przypadku szybkiego planowania liczba jest obliczana na podstawie wszystkich źródłowych dysków maszyn wirtualnych i dziennej szybkości zmiany danych. Dla planisty szczegółowe liczba jest obliczana na podstawie całkowitej liczby maszyn wirtualnych, które są mapowane do wersji premium maszyn wirtualnych platformy Azure (serii DS i GS) i szybkość zmiany danych na tych maszyn wirtualnych.
   * **Liczba wymaganych serwerów konfiguracji**: Pokazuje, ile serwerów konfiguracyjnych jest wymaganych do wdrożenia.
   * **Liczba wymaganych dodatkowych serwerów przetwarzania**: Pokazuje, czy wymagane są dodatkowe serwery przetwarzania, oprócz serwera przetwarzania, który jest domyślnie uruchomiony na serwerze konfiguracji.
   * **100% dodatkowego miejsca na źródle**: Pokazuje, czy dodatkowa pamięć jest wymagana w lokalizacji źródłowej.

      ![Dane wyjściowe](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>Uruchamianie szczegółowego terminarza

1. Pobierz i otwórz [Site Recovery Capacity Planner](https://aka.ms/asr-capacity-planner-excel). Musisz uruchomić makra. Po wyświetleniu monitu należy dokonać wyboru, aby włączyć edycję i zawartość.

2. W **obszarze Wybierz typ planisty**wybierz pozycję **Szczegółowy planista** z pola listy.

   ![Przewodnik z wprowadzeniem](./media/site-recovery-capacity-planner/getting-started-2.png)

3. W arkuszu **Kwalifikacje obciążenia** wprowadź wymagane informacje. Należy wypełnić wszystkie zaznaczone pola.

   a. W **obszarze Rdzenie procesora**określ całkowitą liczbę rdzeni na serwerze źródłowym.

   b. W **obszarze Alokacja pamięci (w MB)** określ rozmiar pamięci RAM serwera źródłowego.

   d. W **obszarze Liczba kart sieciowych**określ liczbę kart sieciowych na serwerze źródłowym.

   d. W **obszarze Całkowita ilość miejsca do magazynowania (w GB)** określ całkowity rozmiar magazynu maszyn wirtualnych. Na przykład jeśli serwer źródłowy ma trzy dyski o pojemności 500 GB każdy, całkowity rozmiar magazynu wynosi 1500 GB.

   e. W **obszarze Liczba podłączonych dysków**określ całkowitą liczbę dysków serwera źródłowego.

   f. W **obszarze Wykorzystanie pojemności dysku (%)** określ średnie wykorzystanie.

   g. W **obszarze Dzienny współczynnik zmiany danych (%)** określ dzienny współczynnik zmiany danych serwera źródłowego.

   h. W **mapowaniu rozmiaru maszyny Wirtualnej platformy Azure**wprowadź rozmiar maszyny Wirtualnej platformy Azure, który chcesz zamapować. Jeśli nie chcesz tego robić ręcznie, wybierz opcję **Compute IaaS VMs**. Jeśli wprowadzona zostanie ręczna konfiguracja, a następnie **wybierzesz maszyny wirtualne IaaS obliczeniowe,** ustawienie ręczne może zostać zastąpione. Proces obliczeń automatycznie identyfikuje najlepsze dopasowanie do rozmiaru maszyny Wirtualnej platformy Azure.

   ![Arkusz kwalifikacji obciążenia](./media/site-recovery-capacity-planner/workload-qualification.png)

4. Jeśli **wybierzesz compute IaaS VMs**, oto co to robi:

   * Sprawdza poprawność obowiązkowych danych wejściowych.
   * Oblicza usługi We/Wy i sugeruje najlepsze dopasowanie rozmiaru maszyny Wirtualnej platformy Azure dla każdej maszyny Wirtualnej, która kwalifikuje się do replikacji na platformie Azure. Jeśli nie można wykryć odpowiedniego rozmiaru maszyny Wirtualnej platformy Azure, zostanie wyświetlony błąd. Na przykład jeśli liczba dołączonych dysków wynosi 65, zostanie wyświetlony błąd, ponieważ największy rozmiar maszyny Wirtualnej platformy Azure wynosi 64.
   * Sugeruje konto magazynu, które może służyć do maszyny Wirtualnej platformy Azure.
   * Oblicza całkowitą liczbę standardowych kont magazynu i kont magazynu w jakości wymaganych dla obciążenia. Przewiń w dół, aby wyświetlić typ magazynu platformy Azure i konto magazynu, które może być używane dla serwera źródłowego.
   * Kończy i sortuje resztę tabeli na podstawie wymaganego typu magazynu (standardowego lub premium) przypisanego do maszyny Wirtualnej i liczby dołączonych dysków. Dla wszystkich maszyn wirtualnych, które spełniają wymagania dotyczące platformy Azure, kolumna **Jest kwalifikowana maszyna wirtualna?** **Yes** Jeśli nie można nabrać kopii zapasowej maszyny Wirtualnej na platformie Azure, zostanie wyświetlony błąd.

Kolumny od AA do AE są dane wyjściowe i zawierają informacje dla każdej maszyny Wirtualnej.

![Kolumny wyjściowe Od AA do AE](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Przykład
Na przykład dla sześciu maszyn wirtualnych z wartościami wyświetlanymi w tabeli narzędzie oblicza i przypisuje najlepsze dopasowanie maszyny wirtualnej platformy Azure i wymagania dotyczące magazynu platformy Azure.

![Przydziały kwalifikacji obciążenia](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* W przykładowym wyjściu należy zwrócić uwagę na następujące kwestie:

  * Pierwsza kolumna jest kolumną sprawdzania poprawności maszyn wirtualnych, dysków i zmian.
  * Dwa standardowe konta magazynu i jedno konto magazynu w wersji premium są potrzebne dla pięciu maszyn wirtualnych.
  * Maszyna wirtualna VM3 nie kwalifikuje się do ochrony, ponieważ jeden lub więcej dysków jest więcej niż 1 TB.
  * Maszyny Wirtualne I wirtualne mogą korzystać z pierwszego standardowego konta magazynu
  * VM4 można użyć drugiego standardowego konta magazynu.
  * Maszyny Wirtualne5 i VM6 potrzebują konta magazynu w wersji premium i oba mogą używać jednego konta.

    > [!NOTE]
    > We/Wy w magazynie standardowym i premium są obliczane na poziomie maszyny Wirtualnej, a nie na poziomie dysku. Standardowa maszyna wirtualna może obsługiwać do 500 IOPS na dysk. Jeśli usługi We/Wy dla dysku są większe niż 500, potrzebujesz magazynu w wersji premium. Jeśli usługi We/Wy dla dysku są więcej niż 500, ale usługi We/Wy dla wszystkich dysków maszyn wirtualnych są w ramach standardowych limitów maszyny Wirtualnej platformy Azure, planista wybiera standardową maszynę wirtualną, a nie serii DS lub GS. (Limity maszyn wirtualnych platformy Azure to rozmiar maszyny Wirtualnej, liczba dysków, liczba kart, procesor i pamięć). Należy ręcznie zaktualizować mapowanie komórki rozmiaru platformy Azure z odpowiednią maszyną wirtualną z serii DS lub GS.


Po wprowadzeniu wszystkich informacji wybierz **pozycję Prześlij dane do narzędzia planisty,** aby otworzyć planer pojemności. Obciążenia są wyróżnione, aby pokazać, czy kwalifikują się do ochrony.

### <a name="submit-data-in-capacity-planner"></a>Przesyłanie danych w planowaniu zdolności produkcyjnych
1. Po otwarciu **arkusza Planer pojemności** jest on wypełniany na podstawie określonych ustawień. Słowo "Obciążenie" pojawia się w komórce **źródłowej infra inputs,** aby pokazać, że dane wejściowe są **arkuszem kwalifikacji obciążenia.**

2. Aby wprowadzić zmiany, należy zmodyfikować arkusz **kwalifikacji obciążenia.** Następnie ponownie wybierz pozycję **Prześlij dane do narzędzia planowania.**

   ![Planista zdolności produkcyjnych](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>Następne kroki
[Dowiedz się, jak uruchomić](site-recovery-capacity-planning-for-hyper-v-replication.md) narzędzie do planowania pojemności.
