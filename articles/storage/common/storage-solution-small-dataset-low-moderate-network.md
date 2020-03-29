---
title: Opcje transferu danych platformy Azure dla małych zestawów danych o niskiej lub umiarkowanej przepustowości sieci| Dokumenty firmy Microsoft
description: Dowiedz się, jak wybrać rozwiązanie platformy Azure do przesyłania danych, gdy masz niską lub umiarkowaną przepustowość sieci w danym środowisku i planujesz przenieść małe zestawy danych.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: 3e6f4f3eb312f0d4d96a008c0944a9608d0bf4a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60397281"
---
# <a name="data-transfer-for-small-datasets-with-low-to-moderate-network-bandwidth"></a>Transfer małych zestawów danych w przypadku niskiej lub umiarkowanej przepustowości sieci
 
Ten artykuł zawiera omówienie rozwiązań transferu danych, gdy masz niską lub umiarkowaną przepustowość sieci w danym środowisku i planujesz przenieść małe zestawy danych. W tym artykule opisano również zalecane opcje transferu danych i odpowiednią macierz możliwości klucza dla tego scenariusza.

Aby zapoznać się z omówieniem wszystkich dostępnych opcji transferu danych, przejdź do [witryny Wybierz rozwiązanie do transferu danych platformy Azure](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Opis scenariusza

Małe zestawy danych odnoszą się do rozmiarów danych w kolejności GBs do kilku TBs. Niska lub umiarkowana przepustowość sieci oznacza 45 Mb/s (połączenie T3 w centrum danych) do 1 Gb/s.

- Jeśli przesyłasz tylko kilka plików i nie musisz zautomatyzować transferu danych, rozważ narzędzia z interfejsem graficznym.
- Jeśli korzystasz z administrowania systemem, rozważ wiersz polecenia lub narzędzia programowe/skryptowe.

## <a name="recommended-options"></a>Zalecane opcje

Opcje zalecane w tym scenariuszu to:

- **Narzędzia interfejsu graficznego,** takie jak Eksplorator usługi Azure Storage i usługa Azure Storage w witrynie Azure portal. Zapewniają one łatwy sposób wyświetlania danych i szybkiego przesyłania kilku plików.

    - **Eksplorator usługi Azure Storage** — to narzędzie wieloplatformowe umożliwia zarządzanie zawartością kont magazynu platformy Azure. Umożliwia przekazywanie, pobieranie i zarządzanie obiektami BLOB, pliki, kolejki, tabele i jednostki usługi Azure Cosmos DB. Użyj go z magazynem obiektów Blob do zarządzania obiektami blob i folderów, a także przekazywania i pobierania obiektów blob między lokalnym systemem plików a magazynem obiektów Blob lub między kontami magazynu.
    - **Witryna Azure portal** — usługa Azure Storage w witrynie Azure portal udostępnia interfejs oparty na sieci Web do eksplorowania plików i przekazywania nowych plików po kolei. Jest to dobra opcja, jeśli nie chcesz instalować żadnych narzędzi lub wydawać poleceń, aby szybko eksplorować pliki lub po prostu przesłać kilka nowych.

- **Narzędzia do tworzenia skryptów/programów,** takie jak interfejsy API programu AzCopy/PowerShell/Azure i interfejsy API rest usługi Azure Storage.

    - **AzCopy** — użyj tego narzędzia wiersza polecenia, aby łatwo kopiować dane do i z usługi Azure Obiektów Blob, plików i magazynu tabel o optymalnej wydajności. AzCopy obsługuje współbieżność i równoległość oraz możliwość wznowienia operacji kopiowania po przerwaniu.
    - **Azure PowerShell** — dla użytkowników korzystających z administrowania systemem, użyj modułu usługi Azure Storage w programie Azure PowerShell do przesyłania danych.
    - **Narzędzie interfejsu wiersza polecenia platformy Azure** — użyj tego narzędzia między platformami do zarządzania usługami platformy Azure i przekazywania danych do usługi Azure Storage.
    - **Interfejsy API/zestawów SDK rest usługi Azure Storage** — podczas tworzenia aplikacji można opracować aplikację za pomocą interfejsów API/zestawów SDK rest usługi Azure Storage i używać bibliotek klienta platformy Azure oferowanych w wielu językach.


## <a name="comparison-of-key-capabilities"></a>Porównanie kluczowych możliwości

W poniższej tabeli podsumowano różnice w kluczowych możliwościach.

| Funkcja | Eksplorator usługi Azure Storage | Portal Azure | Narzędzie AzCopy<br>Azure PowerShell<br>Interfejs wiersza polecenia platformy Azure | Interfejsy API restulenia usługi Azure Storage lub zestawów SDK |
|---------|------------------------|--------------|-----------------------------------------|---------------------------------|
| Dostępność | Pobieranie i instalowanie <br>Narzędzie autonomiczne | Narzędzia do eksploracji oparte na sieci Web w witrynie Azure portal | Narzędzie wiersza polecenia |Programowalne interfejsy w programowacyjnych interfejsach w programach .NET, Java, Python, JavaScript, C++, Go, Ruby i PHP |
| Interfejs graficzny | Tak | Tak | Nie | Nie |
| Obsługiwane platformy | Windows, Mac, Linux | Oparte na sieci Web |Windows, Mac, Linux |Wszystkie platformy |
| Dozwolone operacje magazynu obiektów blob<br>dla obiektów blob i folderów | Upload<br>Pobierz<br>Zarządzaj | Upload<br>Pobierz<br>Zarządzaj |Upload<br>Pobierz<br>Zarządzaj | Tak, możliwość dostosowania |
| Dozwolone przechowywanie danych Lake Gen1<br>operacje dla plików i folderów | Upload<br>Pobierz<br>Zarządzaj | Nie |Upload<br>Pobierz<br>Zarządzaj                   | Nie |
| Dozwolone operacje przechowywania plików<br>dla plików i katalogów | Upload<br>Pobierz<br>Zarządzaj | Upload<br>Pobierz<br>Zarządzaj   |Upload<br>Pobierz<br>Zarządzaj | Tak, możliwość dostosowania |
| Dozwolone operacje przechowywania tabel<br>dla tabel |Zarządzaj | Nie |Obsługa tabeli w AzCopy v7 |Tak, możliwość dostosowania|
| Dozwolony magazyn kolejki | Zarządzaj | Nie  |Nie | Tak, można dostosowywać|


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [przesyłać dane za pomocą Eksploratora usługi Azure Storage.](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/move-data-to-azure-blob-using-azure-storage-explorer)
- [Transferowanie danych za pomocą narzędzia AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

