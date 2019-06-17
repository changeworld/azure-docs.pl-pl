---
title: Usługi Azure data transfer opcji w przypadku małych zestawów danych z małą ilością do Średnia przepustowość sieci | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wybrać rozwiązanie na platformie Azure do transferu danych, gdy trzeba niski Średnia przepustowość sieci w Twoim środowisku i planujesz przeniesienie małych zestawów danych.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: 3e6f4f3eb312f0d4d96a008c0944a9608d0bf4a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60397281"
---
# <a name="data-transfer-for-small-datasets-with-low-to-moderate-network-bandwidth"></a>Transfer danych w przypadku małych zestawów danych z małą ilością Średnia przepustowość sieci
 
Ten artykuł zawiera omówienie transferu danych rozwiązania gdy trzeba niski Średnia przepustowość sieci w Twoim środowisku i planujesz przeniesienie małych zestawów danych. Opisano również opcje transferu danych zalecany i macierzy odpowiedniego kluczowe możliwości, w tym scenariuszu.

Aby dowiedzieć się z omówieniem wszystkich opcji transferu danych o dostępności, przejdź do [wybierz rozwiązanie transferu danych systemu Azure](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Opis scenariusza

Małe zestawy danych dotyczą ilości danych według gigabajtów do kilka TB. Oznacza niskiej Średnia przepustowość sieci, 45 MB/s (T3 połączenie w centrum danych) do 1 GB/s.

- Jeśli przenosisz tylko niewielki podzbiór plików i nie trzeba zautomatyzować transfer danych, należy wziąć pod uwagę narzędzia z interfejsem graficznym.
- Jeśli potrafisz Administracja systemu, należy wziąć pod uwagę wiersza polecenia lub programowy/narzędzia obsługi skryptów.

## <a name="recommended-options"></a>Zalecane opcje

Dostępne są opcje zalecanych w tym scenariuszu:

- **Narzędzia interfejsu graficznego** takich jak Eksplorator usługi Azure Storage i Azure Storage w witrynie Azure portal. Te zapewniają prosty sposób, aby wyświetlić dane i szybko przesłać kilka plików.

    - **Eksplorator usługi Azure Storage** — to narzędzie dla wielu platform umożliwia Zarządzaj zawartością konta magazynu platformy Azure. Umożliwia ona przekazywanie, pobieranie i zarządzać obiektów blob, plików, kolejek, tabel i jednostek usługi Azure Cosmos DB. Umożliwia on z magazynem obiektów Blob Zarządzanie obiektów blob oraz folderów, a także przekazywanie i pobieranie obiektów blob między lokalnym systemie plików i magazynu obiektów Blob lub między kontami magazynu.
    - **Witryna Azure portal** — usługi Azure Storage w witrynie Azure portal udostępnia interfejs internetowy, aby eksplorować pliki i przekazywanie nowych plików pojedynczo. Jest to dobra opcja, jeśli nie chcesz zainstalować narzędzi lub wydawać polecenia, aby szybko zapoznać się z plikami lub po prostu Przekaż kilka nowych.

- **Narzędzia obsługi skryptów programowe** takich jak interfejs wiersza polecenia Azure-AzCopy/programu PowerShell i interfejsów API REST usługi Azure Storage.

    - **Narzędzie AzCopy** — łatwo skopiować danych do i z obiektów blob platformy Azure, plików, za pomocą tego narzędzia wiersza polecenia i Table storage z optymalną wydajnością. Narzędzie AzCopy obsługuje współbieżność i równoległości i możliwość wznowienia operacji kopiowania, w przypadku przerwania.
    - **Program Azure PowerShell** — w przypadku użytkowników doświadczenia z administrowania systemem użyć modułu programu Azure Storage w programie Azure PowerShell do transferu danych.
    - **Interfejs wiersza polecenia Azure** — narzędzie do zarządzania usługami platformy Azure i przekazywanie danych do usługi Azure Storage to dla wielu platform.
    - **Azure Storage REST API/zestawów SDK** — podczas tworzenia aplikacji, możesz opracowywać aplikacji względem magazynu REST API/zestawów SDK usługi Azure i użyć biblioteki klienckie systemu Azure, oferowane w wielu językach.


## <a name="comparison-of-key-capabilities"></a>Porównanie możliwości klucza

W poniższej tabeli podsumowano różnice w kluczowych funkcji.

| Cecha | Eksplorator usługi Azure Storage | Azure Portal | Narzędzie AzCopy<br>Azure PowerShell<br>Interfejs wiersza polecenia platformy Azure | Interfejsy API REST usługi Azure Storage lub zestawów SDK |
|---------|------------------------|--------------|-----------------------------------------|---------------------------------|
| Dostępność | Pobierz i zainstaluj <br>Autonomiczne narzędzie | Narzędzia do eksploracji opartej na sieci Web w witrynie Azure portal | Narzędzie wiersza polecenia |Programowalne interfejsy w .NET, Java, Python, JavaScript, C++, Go, Ruby i PHP |
| Interfejs graficzny | Yes | Yes | Nie | Nie |
| Obsługiwane platformy | Windows, Mac i Linux | Oparte na sieci Web |Windows, Mac i Linux |Wszystkie platformy |
| Dozwolone operacje magazynu obiektów Blob<br>dla obiektów blob oraz folderów | Przekazanie<br>Do pobrania<br>Zarządzanie | Przekazanie<br>Do pobrania<br>Zarządzanie |Przekazanie<br>Do pobrania<br>Zarządzanie | Tak, można dostosowywać |
| Dozwolone magazynu Data Lake Gen1<br>operacje dotyczące plików i folderów | Przekazanie<br>Do pobrania<br>Zarządzanie | Nie |Przekazanie<br>Do pobrania<br>Zarządzanie                   | Nie |
| Dozwolone operacje na plikach<br>dla plików i katalogów | Przekazanie<br>Do pobrania<br>Zarządzanie | Przekazanie<br>Do pobrania<br>Zarządzanie   |Przekazanie<br>Do pobrania<br>Zarządzanie | Tak, można dostosowywać |
| Dozwolone operacje magazynu tabeli<br>w przypadku tabel |Zarządzanie | Nie |Obsługa tabel w wersji 7 narzędzia AzCopy |Tak, można dostosowywać|
| Dozwolone usługi Queue storage | Zarządzanie | Nie  |Nie | Tak, jest możliwe do dostosowania|


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [transfer danych za pomocą Eksploratora usługi Azure Storage](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/move-data-to-azure-blob-using-azure-storage-explorer).
- [Transferowanie danych za pomocą narzędzia AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

