---
title: Azure cele wydajności i skalowalności plików | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o obiekty docelowe skalowalności i wydajności dla plików Azure, w tym pojemności, współczynnik żądań i limity przepustowości dla ruchu przychodzącego i wychodzącego.
services: storage
documentationcenter: na
author: wmgries
manager: aungoo
editor: tamram
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: beb3e5caf8c8dce9b2ea06bbd0a2ea5a4e05a714
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738078"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Azure cele wydajności i skalowalności plików
[Usługa pliki Azure](storage-files-introduction.md) oferuje pełni zarządzanych udziałów plików w chmurze, które są dostępne przy użyciu standardowego protokołu SMB. W tym artykule omówiono cele skalowalności i wydajności dla plików Azure i synchronizacji plików Azure (wersja zapoznawcza).

Obiekty docelowe skalowalności i wydajności wymienione w tym miejscu wysokiej klasy elementów docelowych, ale może wpłynąć na inne zmienne w danym wdrożeniu. Na przykład przepływność dla pliku może również być ograniczone przez dostępną przepustowość sieci, nie tylko serwery obsługujące usługi pliki Azure. Zdecydowanie zaleca się testowanie z wzorca użycia w celu określenia, czy skalowalność i wydajność usługi pliki Azure zgodnie z wymaganiami. Możemy również dużą wagę do zwiększenia tych limitów wraz z upływem czasu. Należy natychmiast Prześlij nam swoją opinię, albo w komentarzach poniżej lub na [UserVoice plików Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files), dotyczących ograniczeń, które chcesz zobaczyć się z nami zwiększyć.

## <a name="azure-storage-account-scale-targets"></a>Obiekty docelowe skalowania konta magazynu platformy Azure
Zasobu nadrzędnego do udziału plików na platformę Azure jest konto magazynu platformy Azure. Konto magazynu reprezentuje puli magazynu na platformie Azure, który może służyć przez wiele usług magazynu, w tym pliki Azure do przechowywania danych. Inne usługi, które przechowują dane na kontach magazynu są magazynu obiektów Blob platformy Azure, magazyn kolejek Azure i magazynem tabel Azure. Następujące elementy docelowe mają zastosowanie wszystkie usługi Magazyn przechowywania danych na koncie magazynu:

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Użycie konta magazynu z innych usług magazynu ma wpływ na Twoje udziały plików platformy Azure na koncie magazynu. Na przykład przekroczenie maksymalnej pojemności konta z magazynu obiektów Blob platformy Azure, nie będzie mogła tworzyć nowe pliki w udziale plików na platformę Azure, nawet jeśli udział plików Azure będzie poniżej rozmiar maksymalny udział.

## <a name="azure-files-scale-targets"></a>Cele plików Azure w skali
[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Azure cele skali synchronizacji plików
Z opcją synchronizacji plików Azure ma próbowaliśmy możliwie często projektować pod kątem nieograniczonego użycia, jednak nie jest to zawsze możliwe. Poniżej tabeli wskazuje granice testów i cele, które są rzeczywiście stałych limitów:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Metryki wydajności synchronizacji plików platformy Azure
Ponieważ agent synchronizacji plików Azure działa na komputerze systemu Windows Server, który łączy się udziały plików platformy Azure, wydajności skuteczne synchronizacji zależy od wielu czynników, w infrastrukturze: Windows Server i podstawowej konfiguracji dysku, przepustowość sieci między serwerem i usługi Azure storage, rozmiar pliku, rozmiar całkowitą zestawu danych i działania w zestawie danych. Ponieważ synchronizacji plików Azure działa na poziomie plików, charakterystyki wydajności rozwiązania oparte na synchronizacji plików Azure lepiej jest mierzony w liczbę obiektów (pliki i katalogi) przetwarzanych na sekundę. 
 
Do celów synchronizacji plików Azure ma kluczowe znaczenie w dwóch etapach:
1. **Początkowa jednorazowe Inicjowanie obsługi administracyjnej**: Aby zoptymalizować wydajność początkowego inicjowania obsługi, zapoznaj się [dołączania z synchronizacji plików Azure](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) szczegółowe optymalizację wdrażania.
2. **Trwającej synchronizacji**: po danych jest początkowo rozpoczęta w udziałach plików na platformę Azure, synchronizacji plików Azure śledzi wiele punktów końcowych w synchronizacji.

Aby zaplanować wdrożenie na każdym z etapów, poniżej są wyniki zaobserwowaną testach wewnętrznych wykonanych na komputerze z programem config
| Konfiguracja systemu |  |
|-|-|
| Procesor CPU | 64 rdzenie wirtualnego z pamięci podręcznej MiB L3 64 |
| Memory (Pamięć) | 128 GiB |
| Dysk | Dyski SAS z RAID 10 z baterii kopii pamięci podręcznej |
| Sieć | Sieć 1 GB/s |
| Obciążenie | Serwer plików ogólnego przeznaczenia|

| Początkowa jednorazowe Inicjowanie obsługi administracyjnej  |  |
|-|-|
| Liczba obiektów | 10 milionów obiektów | 
| Rozmiar zestawu danych| TiB ~ 4 |
| Średni rozmiar pliku | KiB ~ 500 (największy plik: 100 GiB) |
| Przekaż przepływności | 15 obiektów na sekundę |
| Namespace pobierania przepływności * | 350 obiektów na sekundę |
 
* Gdy tworzony jest nowy punkt końcowy serwera, agent synchronizacji plików Azure nie pobiera żadnej zawartości pliku. Najpierw synchronizacji pełnej przestrzeni nazw i następnie Wyzwalacze w tle odwołania do pobierania plików, w całości lub Obsługa poziomów w chmurze, jeśli jest włączona do chmury zasadami obsługi poziomów ustawić punkt końcowy serwera.

| Trwającej synchronizacji  |   |
|-|--|
| Liczba obiektów zsynchronizowane| 125 000 obiektów (przenoszenia ~ 1%) | 
| Rozmiar zestawu danych| 50 giB |
| Średni rozmiar pliku | KiB ~ 500 (największy plik: 100 GiB) |
| Przekaż przepływności | 20 obiektów na sekundę |
| Pobieranie pełnej przepływności * | 30 obiektów na sekundę |
 
Jeśli chmury obsługi jest włączony, najprawdopodobniej będzie obserwować lepszą wydajność jako część pliku danych jest pobierana. Synchronizacja programu Azure pliku pobiera tylko danych plików w pamięci podręcznej po zmianie na żadnym z punktów końcowych. Wszystkie pliki warstwowych lub nowo utworzonych agenta nie pobiera dane pliku i zamiast tego tylko synchronizuje przestrzeni nazw do wszystkich punktów końcowych serwera. Agenta obsługuje również częściowe pliki do pobrania plików warstwowych, ponieważ są one używane przez użytkownika. 
 
> [!Note]  
> Powyższych liczb nie są dane dotyczące wydajności, które wystąpią. Rzeczywista wydajność będzie zależeć od wielu czynników, zgodnie z opisem na początku tej sekcji.

Ogólne wskazówki dla danego wdrożenia należy mieć kilka rzeczy pamiętać:
- Przepływność obiektu około skaluje proporcjonalnie do liczby grup synchronizacji na serwerze. Podział danych do wielu grup synchronizacji na serwerze zapewnia lepszą przepustowość, który również jest ograniczona przez serwera i sieci.
- Przepływność obiektu jest odwrotnie proporcjonalne do MiB na drugi przepływności. W przypadku mniejszych plików wystąpią wyższej przepustowości pod względem liczby obiektów przetworzonych na drugi, ale niższe MiB na drugi przepływności. Z drugiej strony w przypadku plików większych wystąpi mniejszą liczbę obiektów przetworzonych na drugi, ale wyższe MiB na drugi przepływności. MiB na drugi przepływności jest ograniczona przez docelowe skali usługi pliki Azure. 

## <a name="see-also"></a>Zobacz także
- [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md)
- [Planowanie wdrożenia synchronizacji plików Azure](storage-sync-files-planning.md)
- [Obiekty docelowe skalowalności i wydajności dla innych usług magazynu](../common/storage-scalability-targets.md)