---
title: Zarządzanie przechwytywaniem pakietów — witryna Azure portal
titleSuffix: Azure Network Watcher
description: Dowiedz się, jak zarządzać funkcją przechwytywania pakietów funkcji Obserwatora sieciowego przy użyciu witryny Azure portal.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: damendo
ms.openlocfilehash: 6fc4a25e39fb8f27151b2e3bec1959d74a619233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840831"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Zarządzanie przechwytywaniem pakietów za pomocą usługi Azure Network Watcher przy użyciu portalu

Przechwytywanie pakietów Obserwatora sieciowego umożliwia tworzenie sesji przechwytywania w celu śledzenia ruchu do i z maszyny wirtualnej. Filtry są dostępne dla sesji przechwytywania, aby upewnić się, że przechwytujesz tylko ruch, który chcesz. Przechwytywanie pakietów pomaga diagnozować anomalie sieci, zarówno reaktywnie, jak i proaktywnie. Inne zastosowania obejmują zbieranie statystyk sieciowych, uzyskiwanie informacji o włamaniach do sieci, debugowanie komunikacji klient-serwer i wiele więcej. Możliwość zdalnego wyzwalania przechwytywania pakietów zmniejsza obciążenie związane z ręcznym uruchamianiem przechwytywania pakietów na żądanej maszynie wirtualnej, co pozwala zaoszczędzić cenny czas.

W tym artykule nauczysz się uruchamiać, zatrzymywać, pobierać i usuwać przechwytywanie pakietów. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przechwytywanie pakietów wymaga następującej łączności:
* Łączność wychodząca z kontem magazynu za przeładuj 443.
* Łączność przychodząca i wychodząca do 169.254.169.254
* Łączność przychodząca i wychodząca do 168.63.129.16

Jeśli sieciowa grupa zabezpieczeń jest skojarzona z interfejsem sieciowym lub podsiecią, w których znajduje się interfejs sieciowy, upewnij się, że istnieją reguły zezwalane na poprzednie porty. Podobnie dodanie tras ruchu zdefiniowanych przez użytkownika do sieci może uniemożliwić łączność z wyżej wymienionymi usługami IP i portami. Upewnij się, że są one dostępne. 

## <a name="start-a-packet-capture"></a>Rozpoczynanie przechwytywania pakietów

1. W przeglądarce przejdź do [witryny Azure portal](https://portal.azure.com) i wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Obserwator sieci** w **sekcji Sieć**.
2. Wybierz **pozycję Przechwytywanie pakietów** w obszarze **Narzędzia diagnostyczne sieci**. Wszystkie istniejące przechwytywanie pakietów są wyświetlane, niezależnie od ich stanu.
3. Wybierz **pozycję Dodaj,** aby utworzyć przechwytywanie pakietów. Można wybrać wartości dla następujących właściwości:
   - **Subskrypcja:** subskrypcja, dla której znajduje się maszyna wirtualna, dla której chcesz utworzyć przechwytywanie pakietów.
   - **Grupa zasobów:** Grupa zasobów maszyny wirtualnej.
   - **Docelowa maszyna wirtualna:** maszyna wirtualna, dla której chcesz utworzyć przechwytywanie pakietów.
   - **Nazwa przechwytywania pakietów**: Nazwa przechwytywania pakietów.
   - **Konto lub plik magazynu**: Wybierz **konto magazynu**, **Plik**lub oba. W przypadku **wybrania opcji Plik**przechwytywanie zostanie zapisane w ścieżce w obrębie maszyny wirtualnej.
   - **Lokalna ścieżka pliku:** Ścieżka lokalna na maszynie wirtualnej, na której zostanie zapisany przechwytywanie pakietów (jest prawidłowa tylko wtedy, gdy wybrano *opcję Plik).* Ścieżka musi być prawidłową ścieżką. Jeśli używasz maszyny wirtualnej systemu Linux, ścieżka musi zaczynać się od */var/captures*.
   - **Konta magazynu:** Wybierz istniejące konto magazynu, jeśli wybrano *konto magazynu*. Ta opcja jest dostępna tylko w przypadku **wybrania opcji Magazyn**.
   
     > [!NOTE]
     > Konta magazynu w wersji Premium nie są obecnie obsługiwane do przechowywania przechwytywania pakietów.

   - **Maksymalna liczba bajtów na pakiet**: Liczba bajtów z każdego przechwyconego pakietu. Jeśli pozostanie puste, wszystkie bajty zostaną przechwycone.
   - **Maksymalna liczba bajtów na sesję**: Całkowita liczba bajtów przechwyconych. Po osiągnięciu wartości przechwytywanie pakietów zostanie zatrzymane.
   - **Limit czasu (sekundy)**: Limit czasu przed zatrzymaniem przechwytywania pakietów. Wartość domyślna to 18 000 sekund.
   - Filtrowanie (opcjonalnie). Wybierz **+ Dodaj filtr**
     - **Protokół:** Protokół do filtrowania przechwytywania pakietów. Dostępne wartości to TCP, UDP i Any.
     - **Lokalny adres IP:** Filtruje przechwytywanie pakietów dla pakietów, w których lokalny adres IP odpowiada tej wartości.
     - **Port lokalny:** Filtruje przechwytywanie pakietów dla pakietów, w których port lokalny odpowiada tej wartości.
     - **Zdalny adres IP:** Filtruje przechwytywanie pakietów dla pakietów, w których zdalny adres IP odpowiada tej wartości.
     - **Port zdalny:** Filtruje przechwytywanie pakietów dla pakietów, w których port zdalny odpowiada tej wartości.
    
     > [!NOTE]
     > Wartości adresu portowego i IP mogą być pojedynczą wartością, zakresem wartości lub zakresem, takim jak 80-1024, dla portu. Można zdefiniować dowolną liczbę filtrów.

4. Kliknij przycisk **OK**.

Po upływie limitu czasu ustawionego na przechwytywanie pakietów przechwytywanie pakietów zostanie zatrzymane i można je przejrzeć. Można również ręcznie zatrzymać sesję przechwytywania pakietów.

> [!NOTE]
> Portal automatycznie:
>  * Tworzy obserwatora sieci w tym samym regionie co region, w jakim istnieje wybrana maszyna wirtualna, jeśli region nie ma jeszcze obserwatora sieci.
>  * Dodaje rozszerzenie *azurenetworkwatcherExtension* [Linux](../virtual-machines/linux/extensions-nwa.md) lub [Windows](../virtual-machines/windows/extensions-nwa.md) maszyny wirtualnej do maszyny wirtualnej, jeśli nie jest jeszcze zainstalowany.

## <a name="delete-a-packet-capture"></a>Usuwanie przechwytywania pakietów

1. W widoku przechwytywania pakietów wybierz **...** po prawej stronie przechwytywania pakietów lub kliknij prawym przyciskiem myszy istniejące przechwytywanie pakietów, a następnie wybierz polecenie **Usuń**.
2. Zostaniesz poproszony o potwierdzenie, że chcesz usunąć przechwytywanie pakietów. Wybierz **pozycję Tak**.

> [!NOTE]
> Usunięcie przechwytywania pakietów nie powoduje usunięcia pliku przechwytywania z konta magazynu ani na maszynie wirtualnej.

## <a name="stop-a-packet-capture"></a>Zatrzymywanie przechwytywania pakietów

W widoku przechwytywania pakietów wybierz **...** po prawej stronie przechwytywania pakietów lub kliknij prawym przyciskiem myszy istniejące przechwytywanie pakietów, a następnie wybierz pozycję **Zatrzymaj**.

## <a name="download-a-packet-capture"></a>Pobieranie przechwytywania pakietów

Po zakończeniu sesji przechwytywania pakietów plik przechwytywania jest przekazytywał do magazynu obiektów blob lub do pliku lokalnego na maszynie wirtualnej. Lokalizacja przechowywania przechwytywania pakietów jest definiowana podczas tworzenia przechwytywania pakietów. Wygodnym narzędziem dostępu do plików przechwytywania zapisanych na koncie magazynu jest Eksplorator Microsoft Azure Storage, który można [pobrać](https://storageexplorer.com/).

Jeśli określono konto magazynu, pliki przechwytywania pakietów są zapisywane na koncie magazynu w następującej lokalizacji:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

Jeśli podczas tworzenia przechwytywania wybrano **opcję Plik,** można go wyświetlić lub pobrać ze ścieżki skonfigurowaną na maszynie wirtualnej.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak zautomatyzować przechwytywanie pakietów za pomocą alertów maszyn wirtualnych, zobacz [Tworzenie wyzwalanego alertu przechwytywania pakietów](network-watcher-alert-triggered-packet-capture.md).
- Aby ustalić, czy określony ruch jest dozwolony na maszynie wirtualnej, czy poza maszyną wirtualną, zobacz Diagnozowanie problemu z [filtrem ruchu sieciowego maszyny wirtualnej](diagnose-vm-network-traffic-filtering-problem.md).
