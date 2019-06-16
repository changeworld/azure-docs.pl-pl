---
title: Planowanie pojemności na potrzeby odzyskiwania po awarii w funkcji Hyper-V za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule umożliwiają oszacowanie zdolności produkcyjnych, podczas konfigurowania odzyskiwania po awarii przy użyciu usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: eeadfd6a57ff8a26f3f124e2a807fcd66e77b85f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61036747"
---
# <a name="plan-capacity-for-hyper-v-vm-disaster-recovery"></a>Planowanie pojemności na potrzeby odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V 

Nowa wersja rozszerzone [planista wdrażania usługi Azure Site Recovery dla funkcji Hyper-V w celu wdrażania na platformie Azure](site-recovery-hyper-v-deployment-planner.md) jest teraz dostępna. Zastępuje starego narzędzia. Użyj tego nowego narzędzia do planowania wdrożenia.
To narzędzie zawiera następujące wytyczne:

* Ocena uprawnień maszyny Wirtualnej, na podstawie liczby dysków, rozmiaru dysku, operacje We/Wy, współczynnika zmian i pewnych charakterystyk maszyny Wirtualnej
* Zapotrzebowanie na przepustowość sieci i oceną celu punktu odzyskiwania
* Wymagania dotyczące infrastruktury platformy Azure
* Wymagania dotyczące infrastruktury lokalnej
* Wskazówki dotyczące dzielenia na partie replikacji początkowej
* Szacowany koszt odzyskiwania po całkowitej awarii na platformie Azure


Planista pojemności usługi Azure Site Recovery zawarto informacje ułatwiające określenie wymagań dotyczących wydajności podczas replikowania maszyn wirtualnych funkcji Hyper-V, za pomocą usługi Azure Site Recovery.

Planista wydajności usługi Site Recovery umożliwia analizowanie Twoje środowisko źródłowe i obciążeń. Pomaga oszacować wymagania dotyczące przepustowości, zasobów serwera, czego potrzebujesz do lokalizacji źródłowej i zasoby (takie jak maszyny wirtualne i magazyn), którego potrzebujesz w lokalizacji docelowej.

Narzędzie można uruchomić w dwóch trybach:

* **Planowanie szybkie**: Udostępnia projekcje sieci i serwera, na podstawie średniej liczby maszyn wirtualnych, dysków, magazynu i szybkość zmian.
* **Szczegóły planowania**: Zawiera szczegółowe informacje o poszczególnych obciążeń na poziomie maszyny Wirtualnej. Analizuj zgodność maszyny Wirtualnej i Uzyskaj projekcje sieci i serwera.

## <a name="before-you-start"></a>Przed rozpoczęciem

* Zbierz informacje o środowisku, w tym maszyn wirtualnych, dysków na maszynę Wirtualną, pojemności magazynu na dysku.
* Zidentyfikuj dziennych zmian (przenoszenia) dla replikowanych danych. Pobierz [narzędzia do planowania pojemności na potrzeby funkcji Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) można pobrać szybkość zmian. [Dowiedz się więcej](site-recovery-capacity-planning-for-hyper-v-replication.md) na temat tego narzędzia. Firma Microsoft zaleca, uruchom to narzędzie w ciągu tygodnia, aby przechwycić średnie.


## <a name="run-the-quick-planner"></a>Uruchamianie szybkiego Planisty
1. Pobierz i Otwórz [planista wydajności usługi Site Recovery](https://aka.ms/asr-capacity-planner-excel). Musisz uruchomić makra. Po wyświetleniu monitu wybierz odpowiednie opcje, Włącz edytowanie i zawartości.

2. W **wybierz typ aplikacji planner** pola listy, wybierz **szybkie planista**.

   ![Rozpoczęcie pracy](./media/site-recovery-capacity-planner/getting-started.png)

3. Na **Capacity Planner** arkusza, wprowadź wymagane informacje. Wypełnij wszystkie pola zakreślony na czerwono na poniższym zrzucie ekranu:

   a. W **wybierz scenariusz**, wybierz **funkcji Hyper-V na platformę Azure** lub **serwer fizyczny/VMware do platformy Azure**.

   b. W **średni dzienny współczynnik zmian danych (%)** , wprowadź informacje zostały zebrane za pomocą [narzędzia do planowania pojemności na potrzeby funkcji Hyper-V](site-recovery-capacity-planning-for-hyper-v-replication.md) lub [planista wdrażania usługi Site Recovery](./site-recovery-deployment-planner.md).

   c. **Kompresji** ustawienie nie jest używany podczas replikowania maszyn wirtualnych funkcji Hyper-V do platformy Azure. Dla kompresji należy użyć urządzenia innych firm, takimi jak Riverbed.

   d. W **przechowywania w dniach**, określ w dniach, jak długo do przechowywania replik.

   e. W **liczbę godzin, w których replikacji początkowej dla partii maszyn wirtualnych należy wykonać** i **liczby maszyn wirtualnych na partię replikacji początkowej**, wprowadź ustawienia, które są używane do obliczania wymagania dotyczące replikacji początkowej. Po wdrożeniu Usługa Site Recovery cały początkowy zestaw danych jest przekazywany.

   ![Dane wejściowe](./media/site-recovery-capacity-planner/inputs.png)

4. Po wprowadzeniu wartości dla środowiska źródłowego, wyświetlone dane wyjściowe obejmują:

   * **Przepustowość wymagana na potrzeby replikacji różnicowej (w megabitach na sekundę)** : Przepustowość sieci na potrzeby replikacji przyrostowej jest obliczany średni dzienny współczynnik zmian danych.
   * **Przepustowość wymagana na potrzeby replikacji początkowej (w megabitach na sekundę)** : Przepustowość sieci na potrzeby replikacji początkowej jest obliczany na wartości początkowej replikacji, które należy wprowadzić.
   * **Magazyn wymagany (w GB)** : Całkowita ilość miejsca Azure wymagana.
   * **Łączna liczba operacji We/Wy w magazynie Standard Storage**: Liczba jest obliczany na podstawie rozmiar jednostki operacje We/Wy 8K na kontach magazynu w warstwie standardowa całkowitej. Dla szybkiego Planner liczba jest obliczany na podstawie wszystkich dysków maszyny Wirtualnej źródłowego i dzienny współczynnik zmian danych. Dla szczegółowe Planner liczba jest obliczany na podstawie łączna liczba maszyn wirtualnych, które są mapowane do standardowych maszyn wirtualnych platformy Azure i je zmienić częstotliwość na tych maszynach wirtualnych.
   * **Liczba kont magazynu w warstwie standardowa wymagane**: Całkowita liczba kont magazynu w warstwie standardowa niezbędne do ochrony maszyn wirtualnych. Standardowe konto magazynu może zawierać maksymalnie 20 000 operacji We/Wy na wszystkich maszynach wirtualnych w magazynie standard storage. Maksymalnie 500 operacji We/Wy jest obsługiwana na dysk.
   * **Liczba obiektów Blob dysków wymagana**: Liczba dysków, które zostały utworzone w usłudze Azure storage.
   * **Liczba kont usługi premium wymagane**: Całkowita liczba kont usługi premium storage jest niezbędne do ochrony maszyn wirtualnych. Źródłowej maszyny Wirtualnej o wysokiej operacje We/Wy (większe niż 20 000) wymaga konta magazynu premium storage. Konto magazynu premium storage może zawierać maksymalnie 80 000 operacji We/Wy.
   * **Łączna liczba operacji We/Wy w magazynie Premium Storage**: Liczba jest obliczany na podstawie rozmiar jednostki operacje We/Wy 256 KB dla premium łączna liczba kont magazynu. Dla szybkiego Planner liczba jest obliczany na podstawie wszystkich dysków maszyny Wirtualnej źródłowego i dzienny współczynnik zmian danych. Dla szczegółowe Planner ta liczba jest obliczana na podstawie całkowitej liczby maszyn wirtualnych, które są mapowane na maszynach wirtualnych platformy Azure — wersja premium (seria DS i GS) i danych zmiany stawki na tych maszynach wirtualnych.
   * **Liczba serwerów konfiguracji wymaganych**: Pokazuje, jak wiele serwerów konfiguracji są wymagane do wdrożenia.
   * **Liczba dodatkowych serwerów przetwarzania wymagane**: Pokazuje, czy dodatkowych serwerów przetwarzania są wymagane, oprócz serwera przetwarzania, który jest uruchomiony na serwerze konfiguracji domyślnie.
   * **100% dodatkowego magazynu w źródle**: Pokazuje, czy dodatkowy magazyn jest wymagany w lokalizacji źródłowej.

      ![Dane wyjściowe](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>Uruchamianie Planisty szczegółowe

1. Pobierz i Otwórz [planista wydajności usługi Site Recovery](https://aka.ms/asr-capacity-planner-excel). Musisz uruchomić makra. Po wyświetleniu monitu wybierz odpowiednie opcje, Włącz edytowanie i zawartości.

2. W **wybierz typ aplikacji planner**, wybierz opcję **szczegółowe planista** z listy rozwijanej.

   ![Przewodnik Wprowadzenie](./media/site-recovery-capacity-planner/getting-started-2.png)

3. Na **kwalifikacji obciążenia** arkusza, wprowadź wymagane informacje. Wypełnij wszystkie pola oznaczone.

   a. W **rdzeni procesora**, określ łączna liczba rdzeni na serwerze źródłowym.

   b. W **Alokacja pamięci (w MB)** , określ rozmiar pamięci RAM na serwerze źródłowym.

   c. W **liczba kart sieciowych**, określ liczbę kart sieciowych na serwerze źródłowym.

   d. W **całkowita ilość miejsca (w GB)** , określ łączny rozmiar magazynu maszyny Wirtualnej. Na przykład jeśli serwer źródłowy ma trzy dyski z 500 GB, łączny rozmiar magazynu jest 1500 GB.

   e. W **liczby dysków dołączonych**, określ łączna liczba dysków z serwera źródłowego.

   f. W **wykorzystanie pojemności (%) na dysku**, określ średnie wykorzystanie.

   g. W **dzienny (%) współczynnik zmian danych**, określić Dzienny współczynnik zmian danych z serwera źródłowego.

   h. W **rozmiar maszyny Wirtualnej Azure mapowanie**, wprowadź rozmiar maszyny Wirtualnej platformy Azure, który ma być mapowany. Jeśli nie chcesz to zrobić ręcznie, zaznacz **obliczeniowych maszyn wirtualnych IaaS**. Jeśli dane wejściowe ręcznego ustawienia, a następnie wybierz pozycję **obliczeniowych maszyn wirtualnych IaaS**, ręcznego ustawienia mogą zostać zastąpione. Procesów obliczeniowych automatycznie rozpoznaje najbardziej odpowiedni rozmiar maszyny Wirtualnej platformy Azure.

   ![Obciążenie kwalifikacji arkusza](./media/site-recovery-capacity-planner/workload-qualification.png)

4. Jeśli wybierzesz **obliczeniowych maszyn wirtualnych IaaS**, poniżej przedstawiono, jak działa:

   * Sprawdza poprawność wymagane dane wejściowe.
   * Oblicza operacje We/Wy i sugeruje najlepsze dopasowanie rozmiaru maszyny Wirtualnej platformy Azure dla każdej maszyny Wirtualnej, który kwalifikuje się do replikacji do platformy Azure. Jeśli odpowiedni rozmiar maszyny Wirtualnej platformy Azure nie można wykryć, wyświetlany jest błąd. Na przykład jeśli liczba dysków dołączonych wynosi 65, błąd wyświetla ponieważ najwyższy rozmiar maszyny wirtualnej platformy Azure wynosi 64.
   * Sugeruje konta magazynu, który może służyć do maszyny Wirtualnej platformy Azure.
   * Oblicza wartość całkowita liczba kont magazynu w warstwie standardowa i kont usługi premium storage wymagane dla obciążenia. Przewiń w dół do wyświetlania typu magazynu platformy Azure i konto magazynu, które mogą służyć do serwera źródłowego.
   * Kończy i sortuje pozostała część tabeli na podstawie typu wymaganego do magazynowania (standardowa lub premium) przypisany do maszyny Wirtualnej i liczby dysków dołączonych. Dla wszystkich maszyn wirtualnych, które spełniają wymagania dotyczące platformy Azure, w kolumnie **jest kwalifikowana maszyny Wirtualnej?** pokazuje **tak**. Jeśli maszyny Wirtualnej nie można wykonać kopii zapasowej na platformie Azure, jest wyświetlany błąd.

Kolumny AA AE są dane wyjściowe i podaj informacje dotyczące poszczególnych maszyn wirtualnych.

![Kolumny wyjściowe AA do AE](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Przykład
Na przykład dla sześciu maszyn wirtualnych przy użyciu wartości podanych w tabeli Narzędzie oblicza i przypisuje najlepsze dopasowanie maszyn wirtualnych platformy Azure i wymagania dotyczące usługi Azure storage.

![Przypisania kwalifikowania obciążenia](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* W przykładowych danych wyjściowych Pamiętaj o następujących kwestiach:

  * Pierwsza kolumna jest kolumną sprawdzania poprawności dla maszyn wirtualnych, dysków i współczynnika zmian.
  * Dwa konta magazynu w warstwie standardowa i premium jednego konta magazynu są potrzebne do pięciu maszyn wirtualnych.
  * Maszyna VM3 nie kwalifikuje się do ochrony, ponieważ co najmniej jeden dysk jest więcej niż 1 TB.
  * Maszyna VM1 i VM2 mogą używać pierwszego konta magazynu w warstwie standardowa
  * VM4 służy drugiego konta magazynu w warstwie standardowa.
  * VM5 i VM6 wymagają konta magazynu premium storage, a jednocześnie można używać jednego konta.

    > [!NOTE]
    > Operacje We/Wy w warstwie standardowa i premium storage są obliczane na poziomie maszyny Wirtualnej, a nie na poziomie dysku. Standardowa maszyna wirtualna może obsługiwać maksymalnie 500 operacji We/Wy na dysk. W przypadku większym niż 500 operacji We/Wy dysku należy usługi premium storage. Jeśli operacje We/Wy dysku jest więcej niż 500, ale operacje We/Wy i łączna liczba dysków maszyny Wirtualnej są w ramach limitów pomocy technicznej standard maszyny Wirtualnej platformy Azure, planisty wybiera maszyna wirtualna w warstwie standardowa i nie serii DS lub GS. (Limity maszyn wirtualnych platformy Azure są rozmiar maszyny Wirtualnej, liczby dysków, liczba kart sieciowych, Procesora i pamięci). Musisz ręcznie zaktualizować mapowania komórka rozmiaru platformy Azure przy użyciu odpowiednich serii DS lub GS maszyny Wirtualnej.


Po wprowadzeniu wszystkich informacji wybierz **przesyłania danych do narzędzia planisty** otworzyć planowania pojemności. Obciążenia są wyróżniony, aby pokazać, czy są one kwalifikuje się do ochrony.

### <a name="submit-data-in-capacity-planner"></a>Przesyłanie danych w planowania pojemności
1. Po otwarciu **Capacity Planner** arkusza, jest wypełnione, zgodnie z określonymi ustawieniami. Słowo "Obciążenie" pojawia się w **źródła danych wejściowych Infra** komórki, aby pokazać, że dane wejściowe są **kwalifikacji obciążenia** arkusza.

2. Jeśli chcesz wprowadzić zmiany, należy zmodyfikować **kwalifikacji obciążenia** arkusza. Następnie wybierz pozycję **przesyłania danych do narzędzia planisty** ponownie.

   ![Planista wydajności](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się, jak uruchomić](site-recovery-capacity-planning-for-hyper-v-replication.md) pojemności, narzędzia do planowania.
