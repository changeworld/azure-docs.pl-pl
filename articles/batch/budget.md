---
title: Analiza kosztów i budżet — usługa Azure Batch
description: Dowiedz się, jak uzyskać analizę kosztów i ustawić budżet dla podstawowych zasobów obliczeniowych i licencji oprogramowania używanych do uruchamiania obciążeń usługi Batch.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 07/19/2019
ms.author: labrenne
ms.openlocfilehash: 819b5e16f4730e9a1998234288e181772f7c1996
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022719"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Analiza kosztów i budżety usługi Azure Batch

Nie ma żadnych opłat za samą usługę Azure Batch, tylko podstawowe zasoby obliczeniowe i licencje oprogramowania używane do uruchamiania obciążeń usługi Batch. Na wysokim poziomie koszty są ponoszone z maszyn wirtualnych (VM) w puli, transferu danych z maszyny wirtualnej lub wszelkich danych wejściowych lub wyjściowych przechowywanych w chmurze. Przyjrzyjmy się niektórym kluczowym składnikom usługi Batch, aby dowiedzieć się, skąd pochodzą koszty, jak ustawić budżet dla puli lub konta oraz niektóre techniki tworzenia obciążeń usługi Batch bardziej oszczędnych.

## <a name="batch-resources"></a>Zasoby wsadowe

Maszyny wirtualne są najważniejszym zasobem używanym do przetwarzania wsadowego. Koszt używania maszyn wirtualnych dla partii jest obliczany na podstawie typu, ilości i czasu użytkowania. Opcje rozliczeń maszyn wirtualnych obejmują [płatność zgodnie z rzeczywistym i wyjazdem](https://azure.microsoft.com/offers/ms-azr-0003p/) lub [rezerwację](../cost-management-billing/reservations/save-compute-costs-reservations.md) (płatność z góry). Obie opcje płatności mają różne korzyści w zależności od obciążenia obliczeniowego, a oba modele płatności będą miały inny wpływ na rachunek.

Gdy aplikacje są wdrażane w węzłach partii (maszyny wirtualne) przy użyciu [pakietów aplikacji,](batch-application-packages.md)są naliczane za zasoby usługi Azure Storage, które korzystają z pakietów aplikacji. Są również rozliczane za przechowywanie wszelkich plików wejściowych lub wyjściowych, takich jak pliki zasobów i inne dane dziennika. Ogólnie rzecz biorąc koszt danych magazynu skojarzonych z partią batch jest znacznie niższa niż koszt zasobów obliczeniowych. Każda maszyna wirtualna w puli utworzonej za pomocą **VirtualMachineConfiguration** ma skojarzony dysk systemu operacyjnego, który używa dysków zarządzanych przez platformę Azure. Dyski zarządzane przez platformę Azure mają dodatkowe koszty, a inne warstwy wydajności dysku mają również różne koszty.

Pule wsadowe używają zasobów sieciowych. W szczególności dla **pul VirtualMachineConfiguration** używane są standardowe moduły równoważenia obciążenia, które wymagają statycznych adresów IP. Moduły równoważenia obciążenia używane przez usługę Batch są widoczne dla kont **subskrypcji użytkowników,** ale nie są widoczne dla kont **usługi wsadowej.** Standardowe moduły równoważenia obciążenia ponoszą opłaty za wszystkie dane przekazywane do i z maszyn wirtualnych puli wsadowej; wybierz wsadowe interfejsy API pobierające dane z węzłów puli (takich jak Pobierz plik zadania/węzła), pakiety aplikacji zadań, pliki zasobów/danych wyjściowych i obrazy kontenerów będą ponosić opłaty.

### <a name="additional-services"></a>Usługi dodatkowe

Usługi bez uwzględnienia maszyn wirtualnych i magazynu mogą uwzględniać koszt konta usługi Batch.

Inne usługi powszechnie używane z batch może zawierać:

- Application Insights
- Fabryka danych
- Azure Monitor
- Virtual Network
- Maszyny wirtualne z aplikacjami graficznymi

W zależności od tego, z jakich usług korzystasz z rozwiązania Batch, możesz ponieść dodatkowe opłaty. Zapoznaj się z [Kalkulatorem cen,](https://azure.microsoft.com/pricing/calculator/) aby określić koszt każdej dodatkowej usługi.

## <a name="cost-analysis-and-budget-for-a-pool"></a>Analiza kosztów i budżet puli

Za pośrednictwem witryny Azure portal można tworzyć budżety i alerty dotyczące wydatków dla puli usług wsadowych lub konta usługi Batch. Budżety i alerty są przydatne do powiadamiania zainteresowanych stron o ryzyku nadmiernych wydatków. Możliwe jest opóźnienie w alertach dotyczących wydatków i nieznaczne przekroczenie budżetu. W tym przykładzie wyświetlimy analizę kosztów poszczególnych partii puli.

1. W witrynie Azure portal wybierz pozycję **Zarządzanie kosztami + Rozliczenia** na lewym pasku nawigacyjnym.
1. Wybierz subskrypcję z sekcji **Moje subskrypcje**
1. Przejdź do **analizy kosztów** w sekcji **Zarządzanie kosztami** na lewym pasku nawigacyjnym, która wyświetli widok w ten sposób:
1. Wybierz **pozycję Dodaj filtr**. W pierwszym przedyscie wybierz **pozycję Zasób** ![Wybierz filtr zasobów](./media/batch-budget/resource-filter.png)
1. W drugiej listy rozwijanej wybierz pulę Partii. Po wybraniu puli analiza kosztów będzie wyglądać podobnie do poniższej analizy.
    ![Analiza kosztów puli](./media/batch-budget/pool-cost-analysis.png)

Wynikowa analiza kosztów pokazuje koszt puli, a także zasoby, które przyczyniają się do tego kosztu. W tym przykładzie maszyny wirtualne używane w puli są najbardziej kosztownym zasobem.

Aby utworzyć budżet dla puli wybierz **opcję Budżet: brak**, a następnie wybierz pozycję **Utwórz nowy budżet >**. Teraz użyj okna, aby skonfigurować budżet specjalnie dla puli.

Aby uzyskać więcej informacji na temat konfigurowania budżetu, zobacz [Tworzenie budżetów platformy Azure i zarządzanie nimi](../cost-management-billing/costs/tutorial-acm-create-budgets.md).

> [!NOTE]
> Usługa Azure Batch jest oparta na usługach w chmurze azure i technologii Azure Virtual Machines. Po wybraniu **opcji Konfiguracja usług w chmurze**naliczana jest opłata na podstawie struktury cenowej usług w chmurze. Po wybraniu **opcji Konfiguracja maszyny wirtualnej**naliczana jest opłata na podstawie struktury cenowej Maszyny wirtualne. W przykładzie na tej stronie używa **konfiguracji maszyny wirtualnej**.

## <a name="minimize-cost"></a>Minimalizowanie kosztów

Korzystanie z kilku maszyn wirtualnych i usług platformy Azure przez dłuższy czas może być kosztowne. Na szczęście dostępne są usługi, które pomagają zmniejszyć wydatki, a także strategie maksymalizacji wydajności obciążenia pracą.

### <a name="low-priority-virtual-machines"></a>Maszyny wirtualne o niskim priorytecie

Maszyny wirtualne o niskim priorytecie zmniejszają koszt obciążeń typu Batch, korzystając z nadwyżki mocy obliczeniowej na platformie Azure. Po określeniu maszyn wirtualnych o niskim priorytecie w pulach usługa Batch używa tej nadwyżki do uruchomienia obciążenia. Istnieje znaczne oszczędności kosztów przy użyciu maszyn wirtualnych o niskim priorytecie zamiast dedykowanych maszyn wirtualnych.

Dowiedz się więcej o konfigurowaniu maszyn wirtualnych o niskim priorytecie dla obciążenia w [aplikacji Użyj maszyn wirtualnych o niskim priorytecie z partią.](batch-low-pri-vms.md)

### <a name="virtual-machine-os-disk-type"></a>Typ dysku systemu operacyjnego maszyny wirtualnej

Istnieje wiele [typów dysków systemu operacyjnego maszyny Wirtualnej](../virtual-machines/windows/disks-types.md). Większość serii VM mają rozmiary, które obsługują zarówno magazynu premium i standardowego. Po wybraniu rozmiaru maszyny Wirtualnej dla puli usługa Batch konfiguruje dyski systemu operacyjnego SSD w wersji premium. Po wybraniu rozmiaru maszyny Wirtualnej "nie-s" używany jest tańszy, standardowy typ dysku twardego. Na przykład dyski SSD OS `Standard_D2s_v3` premium są używane dla i standardowe `Standard_D2_v3`dyski HDD OS są używane dla .

Dyski premium SSD OS są droższe, ale mają wyższą wydajność, a maszyny wirtualne z dyskami premium mogą uruchamiać się nieco szybciej niż maszyny wirtualne ze standardowymi dyskami z systemami operacyjnymi HDD. W przypadku usługi Batch dysk systemu operacyjnego często nie jest używany, ponieważ aplikacje i pliki zadań znajdują się na tymczasowym dysku SSD maszyn wirtualnych. W związku z tym w wielu przypadkach nie ma potrzeby płacenia zwiększonych kosztów dla ssd premium, który jest aprowizowany, gdy określono rozmiar maszyny Wirtualnej "s".

### <a name="reserved-virtual-machine-instances"></a>Zarezerwowane wystąpienia maszyn wirtualnych

Jeśli zamierzasz używać usługi Batch przez długi okres czasu, można zaoszczędzić na kosztach maszyn wirtualnych przy użyciu [rezerwacji platformy Azure](../cost-management-billing/reservations/save-compute-costs-reservations.md) dla obciążeń. Stawka rezerwacji jest znacznie niższa niż stawka płatności zgodnie z rzeczywistym wynagrodzeniem. Wystąpienia maszyny wirtualnej używane bez rezerwacji są naliczane według stawki płatności zgodnie z rzeczywistym użyciem. W przypadku zakupu rezerwacji zostanie zastosowana zniżka na rezerwację i nie zostaniesz już obciążony według stawek płatności zgodnie z rzeczywistym ratami.

### <a name="automatic-scaling"></a>Automatyczne skalowanie

[Automatyczne skalowanie](batch-automatic-scaling.md) dynamicznie skaluje liczbę maszyn wirtualnych w puli partii na podstawie wymagań bieżącego zadania. Skalując pulę na podstawie okresu istnienia zadania, automatyczne skalowanie zapewnia, że maszyny wirtualne skalowane w górę i używane tylko wtedy, gdy istnieje zadanie do wykonania. Po zakończeniu zadania lub nie ma żadnych zadań, maszyny wirtualne są automatycznie skalowane w dół, aby zapisać zasoby obliczeniowe. Skalowanie umożliwia obniżenie całkowitego kosztu rozwiązania batch przy użyciu tylko zasobów, których potrzebujesz.

Aby uzyskać więcej informacji na temat automatycznego skalowania, zobacz [Automatyczne skalowanie węzłów obliczeniowych w puli usługi Azure Batch](batch-automatic-scaling.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [interfejsach API partii i narzędziach](batch-apis-tools.md) dostępnych do tworzenia i monitorowania rozwiązań wsadowych.  

- Dowiedz się więcej o [maszynach wirtualnych o niskim priorytecie za pomocą usługi Batch](batch-low-pri-vms.md).
