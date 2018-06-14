---
title: Szacowanie zdolności replikacji na platformie Azure | Dokumentacja firmy Microsoft
description: Użyj w tym artykule, aby oszacować pojemności, podczas replikacji przy użyciu usługi Azure Site Recovery
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: jwhit
editor: ''
ms.assetid: 0a1cd8eb-a8f7-4228-ab84-9449e0b2887b
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/09/2018
ms.author: nisoneji
ms.openlocfilehash: d9c2645be73c4b6e34d194d6b2444a700e3900d2
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2018
ms.locfileid: "29875909"
---
# <a name="plan-capacity-for-protecting-hyper-v-vms-with-site-recovery"></a>Planowanie pojemności do ochrony maszyn wirtualnych funkcji Hyper-V z usługą Site Recovery

Nowa wersja rozszerzone [Azure lokacji odzyskiwania wdrożenia Planistę dla funkcji Hyper-V do wdrożenia usługi Azure](site-recovery-hyper-v-deployment-planner.md) jest teraz dostępna. Zastępuje narzędzie stary. Użyj nowego narzędzia dla Planowanie wdrożenia.
To narzędzie zawiera następujące wytyczne:

* Ocena kwalifikujących się maszyny Wirtualnej, na podstawie liczby dysków, rozmiar dysku IOPS, przenoszenie i kilka właściwości maszyny Wirtualnej
* Przepustowość sieci musi i oceny cel punktu odzyskiwania
* Wymagania dotyczące infrastruktury platformy Azure
* Wymagania dotyczące infrastruktury lokalnej
* Przetwarzanie wsadowe wskazówki dotyczące replikacji początkowej
* Szacowany koszt odzyskiwania po całkowitej awarii na platformie Azure


Azure Site Recovery Capacity Planner ułatwia określenie wymagań dotyczących pojemności, podczas replikacji maszyn wirtualnych funkcji Hyper-V z usługą Azure Site Recovery.

Użyj planowania pojemności odzyskiwania lokacji, aby przeanalizować środowiska źródłowego i obciążeń. Pomaga oszacowania wymaganej przepustowości, zasobów serwera, które są potrzebne do lokalizacji źródłowej i zasoby (na przykład maszyn wirtualnych i magazynu) należy w lokalizacji docelowej.

Narzędzie można uruchomić w dwóch trybach:

* **Planowanie szybkie**: zapewnia projekcje sieci i serwera, na podstawie średniej liczby maszyn wirtualnych, dysków, magazynu i szybkość zmian.
* **Szczegółowe planowanie**: zawiera szczegółowe informacje o poszczególnych obciążeń na poziomie maszyny Wirtualnej. Analizowanie zgodności maszyny Wirtualnej i uzyskać projekcje sieci i serwera.

## <a name="before-you-start"></a>Przed rozpoczęciem

* Zbierz informacje o środowisku, w tym o maszynach wirtualnych, dysków dla maszyny Wirtualnej, magazynu na dysku.
* Określ częstotliwość codziennych zmian (przenoszenia) dla replikowanych danych. Pobierz [narzędzia do planowania pojemności funkcji Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) uzyskać szybkość zmian. [Dowiedz się więcej](site-recovery-capacity-planning-for-hyper-v-replication.md) o tym narzędziu. Firma Microsoft zaleca, uruchom to narzędzie dłużej niż przez tydzień średnie przechwytywania.


## <a name="run-the-quick-planner"></a>Uruchom szybki planowania
1. Pobierz i Otwórz [planowania pojemności odzyskiwania lokacji](http://aka.ms/asr-capacity-planner-excel). Musisz uruchomić makra. Po wyświetleniu monitu wybierz odpowiednie opcje Włącz edytowanie i zawartości.

2. W **wybierz typ planner** pola listy, wybierz **szybkie Planner**.

   ![Rozpoczęcie pracy](./media/site-recovery-capacity-planner/getting-started.png)

3. Na **Capacity Planner** arkusza, wprowadź wymagane informacje. Wypełnij wszystkie pola zaznaczona kółkiem na czerwono na poniższym zrzucie ekranu:

   a. W **wybierz scenariusz**, wybierz **funkcji Hyper-V w systemie Azure** lub **VMware/fizycznych do platformy Azure**.

   b. W **średnie dzienne zmienić częstotliwość (%)**, wprowadź informacje Zbierz za pomocą [narzędzia do planowania pojemności funkcji Hyper-V](site-recovery-capacity-planning-for-hyper-v-replication.md) lub [lokacji odzyskiwania wdrożenia Planistę](./site-recovery-deployment-planner.md).

   c. **Kompresji** ustawienie nie jest używany podczas replikowania maszyn wirtualnych funkcji Hyper-V do platformy Azure. Kompresja Użyj urządzenia innych firm, takich jak Riverbed.

   d. W **przechowywania w dniach**, określ w dniach, jak długo opcję zachowania repliki.

   e. W **liczbę godzin, w których replikacji początkowej dla partii maszyn wirtualnych należy wykonać** i **liczbę maszyn wirtualnych na partię replikacji początkowej**, wprowadź ustawienia, które są używane do obliczania wymagania dotyczące replikacji początkowej. Podczas wdrażania usługi Site Recovery jest przekazywany początkowej cały zestaw danych.

   ![Dane wejściowe](./media/site-recovery-capacity-planner/inputs.png)

4. Po wprowadzeniu wartości dla środowiska źródłowego zawiera wyświetlanych wyników:

   * **Przepustowość wymagana dla replikacja różnicowa (w megabitach na sekundę)**: przepustowość sieci dla replikacja różnicowa jest obliczana na średni dzienny częstotliwości zmian danych.
   * **Przepustowość wymagana w przypadku replikacji początkowej (w megabitach na sekundę)**: przepustowość sieci dla replikacji początkowej jest obliczana na wprowadzeniu wartości początkowej replikacji.
   * **Magazynu (w GB) wymagany**: łączna magazynu platformy Azure wymagane.
   * **Łączna liczba IOPS na Standard Storage**: Ta liczba jest obliczana na podstawie rozmiaru jednostki IOPS 8 KB na kontach magazynu w warstwie standardowa całkowita. Dla Planner szybki numer jest obliczany na podstawie wszystkich dysków maszyny Wirtualnej źródłowego i codziennych danych zmienić częstotliwość. Dla Planner szczegółowe numer jest obliczany na podstawie całkowitą liczbę maszyn wirtualnych, które są mapowane na standardowe maszynach wirtualnych platformy Azure i dane zmienić częstotliwość na tych maszynach wirtualnych.
   * **Liczba kont magazynu w warstwie standardowa wymagane**: Całkowita liczba kont magazynu w warstwie standardowa niezbędne do ochrony maszyn wirtualnych. Konta standardowe magazynu może zawierać maksymalnie 20 000 IOPS na wszystkich maszynach wirtualnych magazynu w warstwie standardowa. Obsługiwane są maksymalnie 500 iops dla każdego dysku.
   * **Liczba obiektów Blob dysków wymaganych**: liczba dysków, które są tworzone w magazynie Azure.
   * **Liczba kont premium wymagane**: Całkowita liczba kont magazynu premium niezbędne do ochrony maszyn wirtualnych. Źródło maszyny Wirtualnej o wysokiej IOPS (większe niż 20 000) musi mieć konto magazynu w warstwie premium. Konta premium magazynu może zawierać maksymalnie 80 000 IOPS.
   * **Łączna liczba IOPS na magazyn w warstwie Premium**: Ta liczba jest obliczana na podstawie rozmiaru jednostki IOPS 256 KB na kontach magazynu premium całkowitej. Dla Planner szybki numer jest obliczany na podstawie wszystkich dysków maszyny Wirtualnej źródłowego i codziennych danych zmienić częstotliwość. Dla Planner szczegółowe ta liczba jest obliczana na podstawie całkowitej liczby maszyn wirtualnych, które są mapowane na maszynach wirtualnych platformy Azure — warstwa premium (seria DS i GS) i dane zmienić częstotliwość na tych maszynach wirtualnych.
   * **Liczba serwerów konfiguracji wymaganych**: Pokazuje, ile serwerów konfiguracji są wymagane do wdrożenia.
   * **Liczba wymaganych dodatkowych serwerów procesu**: Pokazuje, czy są wymagane, oprócz serwera przetwarzania, który jest uruchomiony na serwerze konfiguracji domyślnie serwery dodatkowych procesów.
   * **dodatkowy magazyn 100% w źródle**: Pokazuje, czy w lokalizacji źródłowej jest wymagany dodatkowy magazyn.

      ![Dane wyjściowe](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>Uruchom szczegółowy planowania

1. Pobierz i Otwórz [planowania pojemności odzyskiwania lokacji](http://aka.ms/asr-capacity-planner-excel). Musisz uruchomić makra. Po wyświetleniu monitu wybierz odpowiednie opcje Włącz edytowanie i zawartości.

2. W **wybierz typ planner**, wybierz pozycję **szczegółowe Planner** w polu listy.

   ![Przewodnik Wprowadzenie](./media/site-recovery-capacity-planner/getting-started-2.png)

3. Na **kwalifikacji obciążenia** arkusza, wprowadź wymagane informacje. Należy podać wszystkie pola oznaczone.

   a. W **rdzenie procesora**, określ liczbę rdzeni na serwerze źródłowym.

   b. W **Alokacja pamięci (w MB)**, określ rozmiar pamięci RAM z serwera źródłowego.

   c. W **liczbę kart sieciowych**, określ liczbę kart sieciowych na serwerze źródłowym.

   d. W **całkowita ilość miejsca (w GB)**, określ całkowity rozmiar pamięci maszyny Wirtualnej. Na przykład jeśli serwer źródłowy ma trzy dyski 500 GB, całkowitego rozmiaru magazynu jest 1500 GB.

   e. W **liczba dysków dołączonych**, określić całkowitej liczby dysków z serwera źródłowego.

   f. W **wykorzystanie pojemności (%) na dysku**, określ średnie wykorzystanie.

   g. W **codzienne dane zmienić częstotliwość (%)**, określ codziennych danych zmienić częstotliwość, z serwera źródłowego.

   h. W **rozmiar maszyny Wirtualnej Azure mapowania**, wprowadź rozmiar maszyny Wirtualnej platformy Azure, który ma być mapowany. Jeśli nie chcesz zrobić to ręcznie, zaznacz **obliczeniowe maszyn wirtualnych IaaS**. Jeśli wprowadź ustawienia ręcznie, a następnie wybierz **obliczeniowe maszyn wirtualnych IaaS**, ręczne ustawienie może ona zostać zastąpiona. Proces obliczeń automatycznie rozpoznaje najlepszego dopasowania rozmiaru maszyny Wirtualnej platformy Azure.

   ![Obciążenie kwalifikacji arkusza](./media/site-recovery-capacity-planner/workload-qualification.png)

4. W przypadku wybrania **obliczeniowe maszyn wirtualnych IaaS**, Oto, czego:

   * Sprawdza poprawność obowiązkowe danych wejściowych.
   * Oblicza wartość IOPS, a także sugeruje najlepszego dopasowania rozmiaru maszyny Wirtualnej platformy Azure dla każdej maszyny Wirtualnej, które kwalifikują się do replikacji na platformie Azure. Jeśli odpowiedni rozmiar, którego nie można wykryć maszyny Wirtualnej platformy Azure, zawiera błąd. Na przykład jeśli liczba dysków dołączonych jest 65, błąd wyświetla ponieważ najwyższy rozmiar maszyny Wirtualnej platformy Azure jest 64.
   * Sugeruje konta magazynu, który może służyć do maszyny Wirtualnej platformy Azure.
   * Oblicza sumę kont magazynu w warstwie standardowa i premium kont magazynu wymagana dla obciążeń. Przewiń w dół do wyświetlania typu magazynu Azure i konto magazynu, który może służyć do serwera źródłowego.
   * Kończy i sortuje reszty tabeli na podstawie typu wymagane magazynu (standardowy lub premium) przypisanych do maszyny Wirtualnej i liczba dołączonych dysków. Dla wszystkich maszyn wirtualnych, które spełniają wymagania dotyczące platformy Azure, kolumnie **jest wirtualna kwalifikowana?** pokazuje **tak**. Jeśli dla maszyny Wirtualnej nie można wykonać kopii zapasowej na platformie Azure, wyświetla wystąpił błąd.

Kolumny AA AE są dane wyjściowe i podaj informacje dotyczące każdej maszyny Wirtualnej.

![Kolumny wyjściowe AA do AE](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Przykład
Na przykład sześć maszyn wirtualnych przy użyciu wartości z tabelą narzędzie oblicza i przypisuje najlepsze dopasowanie maszyny Wirtualnej platformy Azure i wymagania dotyczące magazynu Azure.

![Przydziały kwalifikacji obciążenia](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* W przykładzie danych wyjściowych należy uwzględnić następujące informacje:

  * Pierwsza kolumna jest kolumną sprawdzania poprawności dla maszyn wirtualnych, dysków i ilość danych churn.
  * Dwa konta magazynu w warstwie standardowa i premium jednego konta magazynu są potrzebne do pięciu maszynach wirtualnych.
  * VM3 nie kwalifikuje się do ochrony, ponieważ co najmniej jeden dysk jest więcej niż 1 TB.
  * VM1 i maszyny VM2 mogą używać pierwsze konto magazynu w warstwie standardowa
  * VM4 można użyć drugiego konta magazynu w warstwie standardowa.
  * VM5 i VM6 muszą premium konta magazynu, a jednocześnie używać jednego konta.

    > [!NOTE]
    > IOPS na warstwy standardowa i premium magazynu są obliczane na poziomie maszyny Wirtualnej, a nie na poziomie dysku. Standard maszyny Wirtualnej może obsługiwać maksymalnie 500 IOPS dla każdego dysku. Wartość IOPS dla dysku jest większa niż 500, należy najpierw Magazyn w warstwie premium. Jeśli wartość IOPS dla dysku jest więcej niż 500, ale IOPS całkowita dysków maszyny Wirtualnej są w ramach limitów pomocy technicznej standard maszyny Wirtualnej platformy Azure, planistę wybiera standardowe maszyny Wirtualnej i nie serii DS lub GS. (Limity maszyny Wirtualnej platformy Azure są rozmiar maszyny Wirtualnej, liczbę dysków, liczba kart, procesora CPU i pamięci). Należy ręcznie zaktualizować mapowania komórki rozmiaru platformy Azure z odpowiednią serii DS lub GS maszyny Wirtualnej.


Po wprowadzeniu wszystkich informacji wybierz **przesyłania danych do narzędzia do planowania** otworzyć planowania pojemności. Obciążenia są wyróżnione do wyświetlenia, czy są one kwalifikuje się do ochrony.

### <a name="submit-data-in-capacity-planner"></a>Przesyłanie danych w planowania pojemności
1. Po otwarciu **Capacity Planner** arkusza, zostanie wypełnione, zgodnie z ustawieniami określona. Wyraz "Obciążenie" pojawia się w **źródła danych wejściowych Infra** komórki, aby wyświetlić dane wejściowe są **kwalifikacji obciążenia** arkusza.

2. Jeśli chcesz wprowadzić zmiany, należy zmodyfikować **kwalifikacji obciążenia** arkusza. Następnie wybierz **przesyłania danych do narzędzia do planowania** ponownie.

   ![Planista pojemności](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się, jak uruchomić](site-recovery-capacity-planning-for-hyper-v-replication.md) narzędzia do planowania pojemności.
