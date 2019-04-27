---
title: Zarządzanie przechwytywaniem pakietów przy użyciu usługi Azure Network Watcher — witryna Azure portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać funkcji przechwytywania pakietów usługi Network Watcher w witrynie Azure portal.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 59edd945-34ad-4008-809e-ea904781d918
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: kumud
ms.openlocfilehash: 22bdd50f129a48ade97db323f904f7e652a00d39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725860"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Zarządzanie przechwytywaniem pakietów przy użyciu usługi Azure Network Watcher przy użyciu portalu

Przechwytywanie pakietów obserwatora sieci umożliwia tworzenie sesji przechwytywania, aby śledzić ruch do i z maszyny wirtualnej. Filtry są dostarczane dla sesji przechwytywania, aby upewnić się, że Przechwytywanie ruchu, który ma. Przechwytywanie pakietów ułatwia diagnozowanie anomalie sieci zarówno sposób reaktywny, jak i aktywne. Inne zastosowania obejmują zbierania statystyk sieciowych, uzyskiwanie informacji na temat wtargnięcia sieci komunikacji klient serwer debugowania i wiele więcej. Możliwość zdalnie wyzwolić pakietów przechwytuje, minimalizuje nakład uruchomienia przechwytywania pakietów ręcznie na odpowiednią maszynę wirtualną, która zapisuje cenny czas.

W tym artykule dowiesz się uruchomić, zatrzymać, pobieranie i usuwanie przechwycenia pakietu. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przechwytywanie pakietów wymaga następujące połączenia:
* Łączność wychodząca z kontem magazynu za pośrednictwem portu 443.
* Łączność ruchu przychodzącego i wychodzącego 169.254.169.254
* Łączność ruchu przychodzącego i wychodzącego z adresu 168.63.129.16

Jeśli sieciowa grupa zabezpieczeń jest skojarzona z interfejsem sieciowym lub podsieci, który znajduje się interfejs sieciowy w, upewnij się, że reguły istnieje, dzięki czemu poprzednich portów. 

## <a name="start-a-packet-capture"></a>Rozpocząć przechwytywania pakietu

1. W przeglądarce przejdź do [witryny Azure portal](https://portal.azure.com) i wybierz **wszystkich usług**, a następnie wybierz pozycję **usługi Network Watcher** w **sekcji sieci**.
2. Wybierz **przechwytywania pakietów** w obszarze **narzędzia diagnostyczne sieci**. Wyświetlane są wszystkie istniejące przechwytywania pakietów niezależnie od ich stanu.
3. Wybierz **Dodaj** utworzyć przechwytywania pakietów. Można wybrać wartości następujących właściwości:
   - **Subskrypcja**: Jest maszyny wirtualnej, w której chcesz utworzyć pakiet przechwytywania dla subskrypcji.
   - **Grupa zasobów**: Grupa zasobów maszyny wirtualnej.
   - **Docelowa maszyna wirtualna**: Na maszynie wirtualnej, który chcesz utworzyć przechwytywania pakietów dla.
   - **Nazwa przechwycenia pakietu**: Nazwa przechwycenia pakietu.
   - **Konto magazynu lub plik**: Wybierz **konta magazynu**, **pliku**, lub obu. Jeśli wybierzesz **pliku**, przechwytywania są zapisywane do ścieżki na maszynie wirtualnej.
   - **Ścieżka do pliku lokalnego**: Ścieżka lokalna na maszynie wirtualnej, w którym zostanie zapisany przechwytywania pakietów (prawidłowe tylko wtedy, gdy *pliku* jest zaznaczona). Ścieżka musi być prawidłową ścieżką. Jeśli używasz maszyny wirtualnej systemu Linux ścieżka musi zaczynać się */var/przechwytuje*.
   - **Konta magazynu**: Wybierz istniejące konto magazynu, w przypadku wybrania *konta magazynu*. Ta opcja jest dostępna tylko jeśli wybrano **magazynu**.
   
     > [!NOTE]
     > Konta usługi Premium storage obecnie nie są obsługiwane dla przechowywania pakietów przechwytuje.

   - **Maksymalna liczba bajtów na pakiet**: Liczba bajtów z każdego pakietu, które są przechwytywane. Jeśli pole pozostanie puste, wszystkie wartości bajtowe są przechwytywane.
   - **Maksymalna liczba bajtów na sesję**: Całkowita liczba bajtów, które są przechwytywane. Po zatrzymaniu przechwytywania pakietów jest osiągnięta wartość.
   - **Limit czasu (w sekundach)**: Limit czasu, zanim przechwytywania pakietów została zatrzymana. Wartość domyślna to 18,000 sekund.
   - Filtrowanie (opcjonalne). Wybierz **+ Dodaj filtr**
     - **Protokół**: Protokół, aby filtrować pod kątem przechwytywania pakietów. Dostępne wartości to TCP, UDP i dowolny.
     - **Lokalny adres IP**: Filtruje przechwytywania pakietów dla pakietów, jeśli lokalny adres IP odpowiada tej wartości.
     - **Port lokalny**: Filtruje przechwytywania pakietów dla pakietów, gdzie port lokalny zgodna z tą wartością.
     - **Zdalny adres IP**: Filtruje przechwytywania pakietów dla pakietów, gdzie adres IP zdalnego zgodna z tą wartością.
     - **Port zdalny**: Filtruje przechwytywania pakietów dla pakietów, gdy port zdalny zgodna z tą wartością.
    
     > [!NOTE]
     > Wartości adresów port i adres IP może być pojedynczą wartość, zakres wartości lub zakresu, takie jak 80 – 1024 dla portu. Można zdefiniować dowolną liczbę filtrów według potrzeb.

4. Kliknij przycisk **OK**.

Po upływie limitu czasu, ustaw na przechwycenie pakietu przechwycenie pakietu zostanie zatrzymana, a następnie mogą być przeglądane. Można też ręcznie zatrzymać sesji przechwytywania pakietów.

> [!NOTE]
> Portal automatycznie:
>  * Tworzy usługi network watcher w tym samym regionie, co region, który maszynę wirtualną, którą wybrano istnieje, jeśli region nie ma jeszcze z usługi network watcher.
>  * Dodaje *AzureNetworkWatcherExtension* [Linux](../virtual-machines/linux/extensions-nwa.md) lub [Windows](../virtual-machines/windows/extensions-nwa.md) rozszerzenie maszyny wirtualnej do maszyny wirtualnej, jeśli jeszcze nie jest zainstalowany.

## <a name="delete-a-packet-capture"></a>Usuwanie przechwycenia pakietu

1. W widoku przechwytywania pakietów, zaznacz **...**  po prawej stronie pakiet przechwytywania, lub kliknij prawym przyciskiem myszy istniejącego przechwycenia pakietu i wybierz **Usuń**.
2. Zostanie wyświetlony monit o potwierdzenie, czy chcesz usunąć przechwycenie pakietu. Wybierz **tak**.

> [!NOTE]
> Usuwanie przechwycenia pakietu nie powoduje usunięcia pliku przechwytywania na koncie magazynu lub na maszynie wirtualnej.

## <a name="stop-a-packet-capture"></a>Zatrzymać przechwytywania pakietu

W widoku przechwytywania pakietów, zaznacz **...**  po prawej stronie pakiet przechwytywania, lub kliknij prawym przyciskiem myszy istniejącego przechwycenia pakietu i wybierz **zatrzymać**.

## <a name="download-a-packet-capture"></a>Pobierz przechwytywania pakietów

Po zakończeniu sesji przechwytywania pakietów pliku przechwytywania jest przekazywany do magazynu obiektów blob lub do pliku lokalnego na maszynie wirtualnej. Lokalizacja magazynu przechwytywania pakietów jest zdefiniowany podczas tworzenia przechwytywania pakietów. Wygodne narzędzie dostępu do przechwytywania plików na koncie magazynu jest Microsoft Azure Storage Explorer, które można [Pobierz](https://storageexplorer.com/).

Jeśli określono konto magazynu, pliki przechwytywania pakietów są zapisywane na koncie magazynu w następującej lokalizacji:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

W przypadku wybrania **pliku** podczas tworzenia przechwytywania możesz wyświetlić lub pobrać go ze ścieżki skonfigurowane na maszynie wirtualnej.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się, jak zautomatyzować przechwytywaniem pakietów przy użyciu alertów maszyny wirtualnej, zobacz [tworzenie przechwytywania pakietów wyzwolonych alertów](network-watcher-alert-triggered-packet-capture.md).
- Aby określić, czy określony ruch jest dozwolone w lub poza nią maszynę wirtualną, zobacz [diagnozowanie problemu z filtrowaniem ruchu maszyny wirtualnej sieci](diagnose-vm-network-traffic-filtering-problem.md).
