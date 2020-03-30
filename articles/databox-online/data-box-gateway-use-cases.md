---
title: Przypadki użycia bramy usługi Microsoft Azure Data Box | Dokumenty firmy Microsoft
description: W tym artykule opisano przypadki użycia usługi Azure Data Box Gateway, rozwiązania magazynunego urządzeń wirtualnych, które umożliwia przesyłanie danych na platformę Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/02/2019
ms.author: alkohli
ms.openlocfilehash: e72113313e27949819db567c550401b1f051473f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022685"
---
# <a name="use-cases-for-azure-data-box-gateway"></a>Przypadki użycia bramy usługi Azure Data Box

Usługa Azure Data Box Gateway to urządzenie bramy magazynu w chmurze, które znajduje się w twojej siedzibie i wysyła obraz, multimedia i inne dane na platformę Azure. Ta brama magazynu w chmurze jest maszyną wirtualną aprowizyjną w hipernadzorcy. Dane można zapisywać na tym urządzeniu wirtualnym przy użyciu protokołów NFS i SMB, które następnie wysyła do platformy Azure. Ten artykuł zawiera szczegółowy opis scenariuszy, w których można wdrożyć to urządzenie.

Użyj bramy pola danych dla następujących scenariuszy:

- Do ciągłego pozyskiwania ogromnych ilości danych.
- Do bezpiecznego i wydajnego archiwizacji danych w chmurze.
- W przypadku przyrostowego transferu danych przez sieć po wykonaniu początkowego transferu zbiorczego przy użyciu pola danych.

Każdy z tych scenariuszy jest szczegółowo opisany w kolejnych sekcjach.


## <a name="continuous-data-ingestion"></a>Ciągłe pojmowanie danych

Jedną z głównych zalet bramy pola danych jest możliwość ciągłego pozyskiwania danych do urządzenia w celu skopiowania do chmury, niezależnie od rozmiaru danych.

Jak dane są zapisywane na urządzeniu bramy, urządzenie przekazuje dane do usługi Azure Storage. Urządzenie automatycznie zarządza magazynem, usuwając pliki lokalnie, zachowując metadane po osiągnięciu określonego progu. Przechowywanie lokalnej kopii metadanych umożliwia urządzeniu bramy przekazywanie zmian tylko po zaktualizowaniu pliku. Dane przesyłane do urządzenia bramy powinny być zgodne z wytycznymi zawartymi w [zastrzeżeniach dotyczących przekazywania danych.](data-box-gateway-limits.md#data-upload-caveats)

Gdy urządzenie wypełnia dane, zaczyna ograniczać szybkość transferu danych przychodzących (w razie potrzeby), aby dopasować szybkość przekazywania danych do chmury. Aby monitorować ciągłe połkwanie na urządzeniu, należy użyć alertów. Te alerty są wywoływane po rozpoczęciu ograniczania przepustowości i są czyszczone po zatrzymaniu ograniczania przepustowości.

## <a name="cloud-archival-of-data"></a>Archiwizacja danych w chmurze

Użyj bramy pola danych, jeśli chcesz zachować dane przez długi czas w chmurze. Warstwy **Archiwum** magazynu można użyć do długoterminowego przechowywania.

Warstwa archiwum jest zoptymalizowana pod kątem przechowywania rzadko dostępnych danych przez co najmniej 180 dni. Warstwa **Archiwum** oferuje najniższe koszty magazynowania, ale ma najwyższe koszty dostępu. Aby uzyskać więcej informacji, przejdź do [warstwy dostępu Archiwum](/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier).

### <a name="move-data-to-archive-tier"></a>Przenoszenie danych do warstwy Archiwum

Przed rozpoczęciem upewnij się, że masz uruchomione urządzenie bramy pola danych. Wykonaj kroki opisane w [samouczku: Przygotuj się do wdrożenia bramy usługi Azure Data Box](data-box-gateway-deploy-prep.md) i kontynuuj przejście do następnego samouczka, dopóki nie będziesz mieć urządzenia operacyjnego.

- Użyj urządzenia Bramy pola danych, aby przekazywać dane na platformę Azure za pomocą zwykłej procedury transferu, jak opisano w [polu Transfer danych za pośrednictwem bramy pola danych.](data-box-gateway-deploy-add-shares.md)
- Po przekazaniu danych należy przenieść je do warstwy Archiwum. Warstwę obiektów blob można ustawić na dwa sposoby: skrypt programu Azure PowerShell lub zasady zarządzania cyklem życia usługi Azure Storage.  
    - Jeśli używasz programu Azure PowerShell, wykonaj następujące [kroki,](/azure/databox/data-box-how-to-set-data-tier#use-azure-powershell-to-set-the-blob-tier) aby przenieść dane do warstwy Archiwum.
    - Jeśli używasz usługi Azure Lifecycle Management, wykonaj następujące kroki, aby przenieść dane do warstwy Archiwum.
        - [Zarejestruj się](/azure/storage/common/storage-lifecycle-management-concepts) w wersji zapoznawczej usługi zarządzania cyklem życia obiektów blob, aby użyć warstwy Archiwum.
        - Poniższe zasady służy do [archiwizowania danych podczas pozyskiwania](/azure/storage/blobs/storage-lifecycle-management-concepts#archive-data-after-ingest).
- Gdy obiekty BLOB są oznaczone jako Archiwum, nie mogą być modyfikowane przez bramę, chyba że zostaną przeniesione do warstwy gorąca lub zimna. Jeśli plik znajduje się w magazynie lokalnym, wszelkie zmiany wprowadzone w kopii lokalnej (w tym usuwa) nie są przekazywane do warstwy archiwum.
- Aby odczytać dane w magazynie archiwum, musi być rehydrated przez zmianę warstwy obiektów blob na gorąco lub cool. [Odświeżanie udziału](data-box-gateway-manage-shares.md#refresh-shares) w bramie nie nawilża obiektu blob.

Aby uzyskać więcej informacji, dowiedz się więcej o [zarządzaniu cyklem życia usługi Azure Blob Storage](/azure/storage/common/storage-lifecycle-management-concepts).

## <a name="initial-bulk-transfer-followed-by-incremental-transfer"></a>Początkowe przeniesienie zbiorcze, po którym następuje przeniesienie przyrostowe

Użyj data box i data box gateway razem, jeśli chcesz zrobić zbiorcze przekazywanie dużej ilości danych, a następnie transfery przyrostowe. Pole danych służy do przesyłania zbiorczego w trybie offline (początkowy materiał siewny) i data box gateway dla przyrostowych transferów (bieżące przesyłanie danych) przez sieć.

### <a name="seed-the-data-with-data-box"></a>Seed danych z pola danych

Wykonaj następujące kroki, aby skopiować dane do pola danych i przekazać do usługi Azure Storage.

1. [Zamów swoje pole danych](/azure/databox/data-box-deploy-ordered).
2. [Skonfiguruj pole danych](/azure/databox/data-box-deploy-set-up).
3. [Skopiuj dane do data box za pośrednictwem protokołu SMB](/azure/databox/data-box-deploy-copy-data).
4. [Zwróć pole danych, sprawdź przekazywanie danych na platformę Azure](/azure/databox/data-box-deploy-picked-up).
5. Po zakończeniu przekazywania danych na platformę Azure wszystkie dane powinny znajdować się w kontenerach magazynu platformy Azure. Na koncie magazynu dla pola danych przejdź do kontenera obiektów Blob (i plik), aby upewnić się, że wszystkie dane są kopiowane. Zanotuj nazwę kontenera, ponieważ będzie używana ta nazwa później. Na przykład na poniższym `databox` zrzucie ekranu kontener będzie używany do transferu przyrostowego.

    ![Kontener z danymi na polu danych](media/data-box-gateway-use-cases/data-container1.png)

Ten transfer zbiorczy kończy początkową fazę wysiewu.

### <a name="ongoing-feed-with-data-box-gateway"></a>Bieżący kanał informacyjny z bramą data box

Wykonaj następujące kroki, aby bieżące gołągowanie przez bramę pola danych.

1. Utwórz udział w chmurze w bramie data box. Ten udział automatycznie przekazuje wszystkie dane do konta usługi Azure Storage. Przejdź do **pozycję Udziały** w zasobie bramy pola danych i kliknij przycisk **+ Dodaj udział**.

    ![Kliknij +Dodaj udział](media/data-box-gateway-use-cases/add-share1.png)

2. Upewnij się, że ten udział jest mapowany do kontenera, który zawiera dane rozstawione. W polu **Wybierz kontener obiektów blob**wybierz pozycję **Użyj istniejącego** i przejdź do kontenera, w którym zostały przeniesione dane z pola danych.

    ![Ustawienia udostępniania](media/data-box-gateway-use-cases/share-settings-select-existing-container1.png)

3. Po utworzeniu udziału odśwież udział. Ta operacja odświeża udział lokalny z zawartością z platformy Azure.

    ![Odświeżanie udziału](media/data-box-gateway-use-cases/refresh-share1.png)

    Po zsynchronizowanie udziału brama pola danych przekaże przyrostowe zmiany, jeśli pliki zostały zmodyfikowane na kliencie.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj [wymagania systemowe usługi Data Box Gateway](data-box-gateway-system-requirements.md).
- Zapoznaj się z [limitami usługi Data Box Gateway](data-box-gateway-limits.md).
- Wdróż rozwiązanie [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) w witrynie Azure Portal.