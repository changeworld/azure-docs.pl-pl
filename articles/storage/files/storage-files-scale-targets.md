---
title: Usługa Azure cele skalowalności i wydajności plików
description: Dowiedz się więcej o cele skalowalności i wydajności dla usługi Azure Files, w tym pojemności, liczba żądań i limity przepustowości dla ruchu przychodzącego i wychodzącego.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2e05f0cb46e1e54ced5911c0a78dd026dbb7f4fa
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905583"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Usługa Azure cele skalowalności i wydajności plików

[Usługa Azure Files](storage-files-introduction.md) oferuje w pełni zarządzane udziały plików w chmurze, które są dostępne za pośrednictwem standardowego protokołu SMB. W tym artykule omówiono cele skalowalności i wydajności dla usługi Azure Files i usługi Azure File Sync.

Cele dotyczące skalowalności i wydajności, wymienione w tym miejscu są cele wysokiej klasy, ale mogą mieć wpływ na inne zmienne w danym wdrożeniu. Na przykład przepływność dla pliku może być ona ograniczona przez dostępną przepustowość sieci, nie tylko serwerów obsługujących usługę Azure Files. Zdecydowanie zaleca się testowanie usługi wzorca użycia, aby ustalić, czy skalowalności i wydajności usługi Azure Files własnych wymagań. Ponadto staramy się zwiększyć te limity, wraz z upływem czasu. Zachęcamy do przesłać nam swoją opinię, albo w komentarzach poniżej lub na [UserVoice plików Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files), o limitach, które chcesz zobaczyć nam zwiększyć.

## <a name="azure-storage-account-scale-targets"></a>Obiekty docelowe skalowanie konta usługi Azure storage

Zasób nadrzędny dla udziału plików platformy Azure jest konto magazynu platformy Azure. Konto magazynu reprezentuje pulę magazynu na platformie Azure, który może służyć przez wielu usług magazynu, w tym usługi Azure Files do przechowywania danych. Inne usługi, które przechowują dane w ramach kont magazynu są usługi Azure Blob storage, Azure Queue storage i Azure Table storage. Następujące elementy docelowe mają zastosowanie wszystkich usług magazynu przechowywania danych w ramach konta magazynu:

[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Użycie konta magazynu ogólnego zastosowania z innych usług magazynu ma wpływ na udziały plików platformy Azure na Twoim koncie magazynu. Na przykład w przypadku osiągnięcia maksymalnej pojemności konta za pomocą usługi Azure Blob storage nie będzie mogła tworzyć nowe pliki w udziale plików platformy Azure, nawet jeśli udział plików platformy Azure znajduje się poniżej rozmiaru maksymalnego udziału.

## <a name="azure-files-scale-targets"></a>Usługa Azure celów skalowania plików

Istnieją trzy kategorie ograniczeń, które należy wziąć pod uwagę w Azure Files: konta magazynu, udziały i pliki.

Na przykład: z udziałami plików w warstwie Premium pojedynczy udział może osiągać 100 000 operacji we/wy, a pojedynczy plik może skalować do 5 000 operacji we/wy na sekundę. Dlatego jeśli masz trzy pliki w jednym udziale, Maksymalna liczba IOPS, którą można uzyskać z tego udziału, to 15 000.

### <a name="standard-storage-account-limits"></a>Limity kont magazynu w warstwie Standardowa

Zapoznaj się z sekcją [cele skalowania konta usługi Azure Storage](#azure-storage-account-scale-targets) , aby uzyskać te limity.

### <a name="premium-filestorage-account-limits"></a>Limity kont FileStorage Premium

[!INCLUDE [azure-storage-limits-filestorage](../../../includes/azure-storage-limits-filestorage.md)]

> [!IMPORTANT]
> Limity konta magazynu dotyczą wszystkich udziałów. Skalowanie w górę do maksimum dla kont FileStorage jest osiągalne tylko wtedy, gdy istnieje tylko jeden udział na konto FileStorage.

### <a name="file-share-and-file-scale-targets"></a>Elementy docelowe udziału plików i skalowania plików

> [!NOTE]
> Standardowe udziały plików większe niż 5 TiB mają pewne ograniczenia.
> Listę ograniczeń, informacje regionalne i instrukcje dotyczące włączania większych rozmiarów udziałów plików można znaleźć w sekcji Dołączanie [do większych udziałów plików](storage-files-planning.md#onboard-to-larger-file-shares-standard-tier) w przewodniku planowania.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Cele skalowania usługi Azure File Sync

Azure File Sync został zaprojektowany z myślą o celu nieograniczonego użycia, ale nieograniczone użycie nie zawsze jest możliwe. Poniższa tabela wskazuje granice testowania firmy Microsoft, a także wskazuje, które cele są sztywne:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Metryki wydajności usługi Azure File Sync

Ponieważ agent usługi Azure File Sync jest uruchomiony na komputerze z serwerem systemu Windows, który nawiązuje połączenie z udziałami plików platformy Azure, wydajność synchronizacji obowiązujące zależy od wielu czynników, w ramach infrastruktury: Windows Server i podstawowych konfiguracji dysku, przepustowość sieci między serwerem i usługi Azure storage, rozmiar pliku, rozmiar całkowitej zestawu danych a działania w zestawie danych. Ponieważ usługi Azure File Sync działa na poziomie plików, charakterystyki wydajności, rozwiązania opartej na usłudze Azure File Sync lepiej jest mierzony w liczbę obiektów (pliki i katalogi) przetwarzanych na sekundę.

Dla usługi Azure File Sync wydajność jest szczególnie ważne w dwóch etapach:

1. **Początkowe jednorazowe Inicjowanie obsługi administracyjnej**: w celu zoptymalizowania wydajności początkowego udostępnienia, dotyczą [dołączania przy użyciu usługi Azure File Sync](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) szczegóły optymalizację wdrażania.
2. **Trwającą synchronizacji**: po danych jest początkowo zasilany w udziałach plików platformy Azure, usługi Azure File Sync zapewnia wiele punktów końcowych w synchronizacji.

Aby ułatwić planowanie wdrożenia dla każdego z etapów, poniżej wyniki przestrzegane są podczas testowania wewnętrznego w systemie przy użyciu konfiguracji

| Konfiguracja systemu |  |
|-|-|
| Procesor CPU | 64 rdzeni wirtualnych za pomocą 64 pamięci podręcznej MiB L3 |
| Pamięć | 128 GiB |
| Dysk | Dyski SAS z macierzy RAID 10 z baterii kopii pamięci podręcznej |
| Network (Sieć) | Sieć 1 GB/s |
| Obciążenie | Serwer plików ogólnego przeznaczenia|

| Początkowa jednorazowe Inicjowanie obsługi administracyjnej  |  |
|-|-|
| Liczba obiektów | 25 000 000 obiektów |
| Rozmiar zestawu danych| ~ 4,7 TiB |
| Średni rozmiar plików | ~ 200 KiB (największy plik: 100 GiB) |
| Przekaż przepływności | 20 obiektów na sekundę |
| Namespace pobierania przepływności * | 400 obiektów na sekundę |

\* Po utworzeniu nowego punktu końcowego serwera agenta usługi Azure File Sync nie pobiera żadnej zawartości pliku. Najpierw synchronizuje pełną przestrzeni nazw i następnie wyzwalaczy w tle odwołania do pobierania plików, albo w całości lub, jeśli obsługa warstw w chmurze jest włączony, aby zasady obsługi warstw w chmurze ustawiony w punkcie końcowym serwera.

| Trwającą synchronizacji  |   |
|-|--|
| Liczba obiektów synchronizowane| 125 000 obiektów (około 1% zmian) |
| Rozmiar zestawu danych| 50 giB |
| Średni rozmiar plików | ~ 500 KiB |
| Przekaż przepływności | 20 obiektów na sekundę |
| Pobierz pełną przepływność * | 60 obiektów na sekundę |

\* Jeśli chmura warstw jest włączona, najprawdopodobniej będzie obserwować lepszą wydajność, jako część pliku danych zostanie pobrana. Usługa Azure File Sync pobiera tylko dane plików w pamięci podręcznej po zmianie na żadnym z punktów końcowych. Dla wszystkich plików warstwowych lub być nowo utworzoną agenta nie pobiera dane z pliku, a zamiast tego synchronizuje tylko przestrzeń nazw w celu wszystkie punkty końcowe serwera. Agent obsługuje również częściowe pobieranie plików warstwowych, ponieważ są one używane przez użytkownika. 

> [!Note]  
> Powyższe liczby nie są dane dotyczące wydajności, który wystąpi. Rzeczywista wydajność będzie zależeć od wielu czynników, tak jak pokazano na początku tej sekcji.

Jako ogólnej wskazówki dla danego wdrożenia należy zachować kilka rzeczy, należy pamiętać:

- Przepływność obiektu około skaluje się proporcjonalnie do liczby grup synchronizacji na serwerze. Dzielenie danych na wiele grup synchronizacji na serwerze daje lepszą przepustowość, który również jest ograniczony przez serwer i sieć.
- Przepływność obiektu jest odwrotnie proporcjonalne do MiB na drugim przepływności. Mniejsze pliki będą napotykać większą przepływność pod względem liczby obiektów przetwarzanych na drugi, ale niższe MiB na drugim przepływności. Z drugiej strony w przypadku plików większych otrzymasz mniejszą liczbę obiektów przetwarzanych na drugi, ale wyższy MiB na drugim przepływności. MiB na drugim przepływność jest ograniczona przez obiekty docelowe skalowania usługi Azure Files.

## <a name="see-also"></a>Zobacz także

- [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md)
- [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md)
