---
title: Usługa Microsoft Azure Data Box Gateway przypadki użycia | Dokumentacja firmy Microsoft
description: W tym artykule opisano przypadki użycia dla bramy pole danych platformy Azure, rozwiązanie magazynu urządzenie wirtualne, które pozwala na przesyłanie danych do platformy Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/02/2019
ms.author: alkohli
ms.openlocfilehash: cdc1b07b93386c0225c1e6c5f570ea1247e450b5
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924391"
---
# <a name="use-cases-for-azure-data-box-gateway"></a>Przypadki użycia dla bramy pola danych platformy Azure

Brama pola danych platformy Azure jest urządzenie magazynujące w chmurze bramy znajduje się w środowisku lokalnym, który wysyła obraz, multimediów i inne dane na platformie Azure. Ta brama magazynu w chmurze jest aprowizowane w Twojej funkcji hypervisor maszyny wirtualnej. Można zapisać danych do tego urządzenia wirtualnego za pomocą protokołów systemu plików NFS i protokołu SMB, które następnie wysyła do platformy Azure. Ten artykuł zawiera szczegółowy opis scenariuszy, w którym można wdrożyć to urządzenie.

Użyj bramy pola danych w następujących scenariuszach:

- Aby stale pozyskiwanie dużych ilości danych.
- Archiwizowanie danych w sposób bezpieczny i skuteczny chmury.
- Opłata za transfer danych przyrostowych za pośrednictwem sieci po początkowej zbiorczego transferu jest wykonywane przy użyciu pola danych.

Każdy z tych scenariuszy opisano szczegółowo w kolejnych sekcjach.


## <a name="continuous-data-ingestion"></a>Pozyskiwanie danych ciągłego

Jedną z podstawowych zalet bramy pola danych jest możliwość stale pozyskiwania danych w urządzeniu kopiowania do chmury, niezależnie od rozmiaru danych.

Ponieważ dane są zapisywane z urządzeniem bramy, urządzenie przekazywanie danych do usługi Azure Storage. Urządzenie automatycznie zarządza magazynem, usuwając pliki lokalnie po osiągnięciu określonego progu, zachowując metadanych. Utrzymywanie lokalną kopię metadanych umożliwia urządzenie bramy przekazać zmiany tylko, gdy plik zostanie zaktualizowany. Danych przekazanych do Twojego urządzenia bramy powinny być zgodnie z wytycznymi podanymi w [przekazywanie danych do zastrzeżenia](data-box-gateway-limits.md#data-upload-caveats).

Jako urządzenie wypełni się dane, uruchamia, ograniczania szybkości transferu danych przychodzących (zgodnie z potrzebami) aby dopasować częstotliwość, z jaką dane są przekazywane do chmury. Aby monitorować ciągłe wprowadzanie na urządzeniu, należy użyć alertów. Te alerty są wywoływane, gdy ograniczenie rozpoczyna się i są czyszczone po ograniczenie została zatrzymana.

## <a name="cloud-archival-of-data"></a>Archiwizowanie danych w chmurze

Jeśli chcesz przechowywać dane na potrzeby długoterminowego w chmurze za pomocą bramy pola danych. Możesz użyć **archiwum** warstw magazynowania do długoterminowego przechowywania danych.

Warstwę archiwum jest zoptymalizowany do przechowywania rzadko używanych danych, przez co najmniej 180 dni. **Archiwum** warstwa oferuje najniższe koszty magazynowania, ale ma najwyższe koszty dostępu. Aby uzyskać więcej informacji, przejdź do [warstwę dostępu archiwum](/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier).

### <a name="move-data-to-archive-tier"></a>Przenoszenie danych do warstwy archiwum

Przed rozpoczęciem upewnij się, że uruchomione urządzenie bramy pola danych. Wykonaj kroki opisane szczegółowo w [samouczka: Przygotowanie do wdrożenia usługi Azure Gateway pole danych](data-box-gateway-deploy-prep.md) i zachować do następnego samouczka, dopóki nie uzyskasz działającego urządzenia.

- Korzystanie z urządzenia bramy pola danych do przekazania danych na platformę Azure za pośrednictwem zwykłych transferu, zgodnie z opisem w [transferu danych za pośrednictwem bramy pola danych](data-box-gateway-deploy-add-shares.md).
- Po przekazaniu danych należy przenieść ją do warstwy archiwum. Możesz ustawić warstwę obiektu blob na dwa sposoby: Skrypt programu Azure PowerShell lub zasady zarządzania cyklem życia magazynu platformy Azure.  
    - Jeśli przy użyciu programu Azure PowerShell, postępuj zgodnie z tymi [kroki](/azure/databox/data-box-how-to-set-data-tier#use-azure-powershell-to-set-the-blob-tier) przenoszenia danych do warstwy archiwum.
    - Jeśli za pomocą zarządzania cyklem życia platformy Azure, wykonaj następujące kroki, aby przenieść dane do warstwy archiwum.
        - [Zarejestruj](/azure/storage/common/storage-lifecycle-management-concepts#register-for-preview) skorzystania z wersji zapoznawczej usługę zarządzania cyklem życia obiektów Blob pod kątem używania archiwum warstwy.
        - Użyj następujących zasad do [pozyskiwania danych archiwum na](/azure/storage/blobs/storage-lifecycle-management-concepts#archive-data-at-ingest).
- Po obiekty BLOB są oznaczone jako archiwum, ich można już nie można modyfikować przez bramę, chyba że są one przenoszone do warstwy gorąca lub zimnych. Jeśli plik znajduje się w magazynie lokalnym, wszelkie zmiany wprowadzone do lokalnej kopii (w tym usuwa) nie są przekazywane do warstwy archiwum.
- Do odczytywania danych w magazynie archiwalnym, należy wypełnienia przez zmianę warstwy obiektu blob na gorącą lub chłodną. [Odświeżanie udziału](data-box-gateway-manage-shares.md#refresh-shares) w bramie nie przywrócenia z magazynu trwałego obiektu blob.

Aby uzyskać więcej informacji, Dowiedz się więcej na temat [zarządzania usługi Azure Blob cykl życia magazynu](/azure/storage/common/storage-lifecycle-management-concepts).

## <a name="initial-bulk-transfer-followed-by-incremental-transfer"></a>Transfer początkowy zbiorcze następuje transferu przyrostowego

Urządzenie Data Box i bramy pola danych ze sobą należy używać do przekazywania masowego w dużych ilości danych, a następnie Transfery przyrostowe, zwiększając. Na użytek urządzenia Data Box w celu wykonania transferu zbiorczego w tryb offline (inicjatora początkowej) i bramy pola danych Transfery przyrostowe, zwiększając (bieżące źródło danych) za pośrednictwem sieci.

### <a name="seed-the-data-with-data-box"></a>Umieszczenia danych za pomocą urządzenia Data Box

Wykonaj następujące kroki, aby skopiować dane do urządzenia Data Box i przekazać go do usługi Azure Storage.

1. [Zamówienie usługi Data Box](/azure/databox/data-box-deploy-ordered).
2. [Konfigurowanie usługi Data Box](/azure/databox/data-box-deploy-set-up).
3. [Kopiowanie danych do urządzenia Data Box za pomocą protokołu SMB](/azure/databox/data-box-deploy-copy-data).
4. [Sprawdź przekazywania danych na platformie Azure, zwracają urządzenia Data Box](/azure/databox/data-box-deploy-picked-up).
5. Po zakończeniu przekazywania danych na platformie Azure wszystkie dane należy w kontenerach usługi Azure storage. Na koncie magazynu urządzenia Data Box przejdź do kontenera obiektów Blob (i plik), aby upewnić się, że wszystkie dane są kopiowane. Zanotuj nazwę kontenera, ponieważ ta nazwa będzie używana później. Na przykład w poniższym zrzucie ekranu `databox` kontenera będzie używany do transferu przyrostowego.

    ![Kontener z danymi na urządzenie Data Box](media/data-box-gateway-use-cases/data-container1.png)

To przeniesienie zbiorcze kończy początkowej fazie rozmieszczania.

### <a name="ongoing-feed-with-data-box-gateway"></a>Trwającą źródła danych za pomocą bramy pola danych

Wykonaj następujące kroki dla ciągłego wprowadzania przez bramę pola danych.

1. Utwórz udział chmury w bramie pola danych. Ten udział automatyczne przekazywanie danych do konta usługi Azure Storage. Przejdź do **udziałów** w zasobie bramy pola danych i kliknij przycisk **+ Dodaj udział**.

    ![Kliknij pozycję + Dodaj udział](media/data-box-gateway-use-cases/add-share1.png)

2. Upewnij się, że ten udział mapuje do kontenera, który zawiera wprowadzonych danych. Aby uzyskać **kontenera obiektów blob wybierz**, wybierz **Użyj istniejącej** i przejdź do kontenera, w której została przeniesiona danych z urządzenia Data Box.

    ![Ustawienia udziału](media/data-box-gateway-use-cases/share-settings-select-existing-container1.png)

3. Po utworzeniu udziału Odśwież udział. Ta operacja zostanie odświeżona w środowisku lokalnym udostępniasz zawartość z platformy Azure.

    ![Odśwież udział](media/data-box-gateway-use-cases/refresh-share1.png)

    Gdy udział jest zsynchronizowana, jeśli pliki zostały zmodyfikowane na kliencie bramy pola danych przekaże zmiany przyrostowe.

## <a name="next-steps"></a>Kolejne kroki

- Przejrzyj [wymagania systemowe usługi Data Box Gateway](data-box-gateway-system-requirements.md).
- Zapoznaj się z [limitami usługi Data Box Gateway](data-box-gateway-limits.md).
- Wdróż rozwiązanie [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) w witrynie Azure Portal.