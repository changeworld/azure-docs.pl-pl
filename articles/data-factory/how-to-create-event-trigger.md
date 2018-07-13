---
title: Tworzenie wyzwalaczy opartych na zdarzeniach w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć wyzwalacz w usłudze Azure Data Factory uruchamia potok w odpowiedzi na zdarzenie.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: douglasl
ms.openlocfilehash: ecd5f242d2dcb5662376541ac0a9e75ce533b59f
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005836"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Utwórz wyzwalacz, który uruchamia potok w odpowiedzi na zdarzenie

W tym artykule opisano wyzwalacze oparte na zdarzeniach, które można tworzyć w potoków usługi Data Factory.

Architektura sterowana zdarzeniami (EDA) jest to typowy wzorzec integracji danych, która obejmuje produkcji, wykrywanie, wykorzystania i reagowania na zdarzenia. Kompleksowe scenariusze integracji danych często wymagają klientów usługi Data Factory wyzwalanie potoków w oparciu o zdarzenia. Fabryka danych jest teraz zintegrowana z [usługi Azure Event Grid](https://azure.microsoft.com/services/event-grid/), pozwalającej możesz wyzwolić potoków na zdarzenie.

> [!NOTE]
> Integracja opisanych w tym artykule jest zależna od [usługi Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Upewnij się, że Twoja subskrypcja jest zarejestrowana za pomocą dostawcy zasobów usługi Event Grid. Aby uzyskać więcej informacji, zobacz [dostawcy zasobów i ich typy](../azure-resource-manager/resource-manager-supported-services.md#portal).

## <a name="data-factory-ui"></a>Interfejs użytkownika usługi Data Factory

### <a name="create-a-new-event-trigger"></a>Utwórz nowy wyzwalacz zdarzenia

Typowe zdarzeń jest przybycia pliku lub usunięcie pliku, w ramach konta usługi Azure Storage. Można utworzyć wyzwalacz, który odpowiada na to zdarzenie w potoku usługi fabryka danych.

> [!NOTE]
> Integracja ta obsługuje tylko kont usługi Storage w wersji 2 (ogólnego przeznaczenia).

![Utwórz nowy wyzwalacz zdarzenia](media/how-to-create-event-trigger/event-based-trigger-image1.png)

### <a name="configure-the-event-trigger"></a>Skonfiguruj wyzwalacz zdarzenia

Za pomocą **ścieżka obiektu Blob, który rozpoczyna się od** i **ścieżka obiektu Blob kończy się** właściwości, można określić kontenerów, folderów i nazw obiektów blob, dla których chcesz odbierać zdarzenia. Można użyć różnych wzorców dla obu **ścieżka obiektu Blob, który rozpoczyna się od** i **ścieżka obiektu Blob kończy się** właściwości, jak pokazano w przykładach w dalszej części tego artykułu. Co najmniej jedną z tych właściwości jest wymagana.

![Skonfiguruj wyzwalacz zdarzenia](media/how-to-create-event-trigger/event-based-trigger-image2.png)

### <a name="select-the-event-trigger-type"></a>Wybierz typ wyzwalacza zdarzenia

Jak najszybciej pliku dociera do lokalizacji Twojego magazynu i zostanie utworzony odpowiedni obiekt blob, to zdarzenie wyzwala i uruchamia potok usługi Data Factory. Można utworzyć wyzwalacz, który odpowiada na zdarzenie tworzenia obiektów blob, zdarzenie usuwania obiektów blob lub obu zdarzeń w potoków usługi Data Factory.

![Typ wyzwalacza wybierz jako zdarzenie](media/how-to-create-event-trigger/event-based-trigger-image3.png)

### <a name="map-trigger-properties-to-pipeline-parameters"></a>Mapowanie właściwości wyzwalacza do parametrów potoku

Gdy zdarzenie wyzwalacza dla konkretnego obiektu blob, zdarzenie przechwytuje ścieżkę i nazwę folderu obiektu blob do właściwości `@triggerBody().folderPath` i `@triggerBody().fileName`. Aby użyć wartości tych właściwości w potoku, należy zamapować właściwości do parametrów potoku. Po mapowania właściwości do parametrów, można uzyskać dostęp do wartości przechwycone przez wyzwalacz za pomocą `@pipeline.parameters.parameterName` wyrażenia w całym potoku.

![Mapowanie właściwości do parametrów potoku](media/how-to-create-event-trigger/event-based-trigger-image4.png)

Na przykład w poprzednim zrzucie ekranu. wyzwalacz jest skonfigurowany do uruchomi się, jeśli ścieżka obiektu blob, kończy się rozszerzeniem `.csv` jest tworzony na koncie magazynu. W rezultacie, gdy obiekt blob z `.csv` rozszerzenia jest tworzony w dowolnym miejscu na koncie magazynu `folderPath` i `fileName` właściwości przechwytywania lokalizacji nowy obiekt blob. Na przykład `@triggerBody().folderPath` ma wartość, takich jak `/containername/foldername/nestedfoldername` i `@triggerBody().fileName` ma wartość, takich jak `filename.csv`. Te wartości są mapowane w przykładzie do parametrów potoku `sourceFolder` i `sourceFile`. Można ich używać w całym potoku jako `@pipeline.parameters.sourceFolder` i `@pipeline.parameters.sourceFile` odpowiednio.

## <a name="json-schema"></a>Schemat JSON

Poniższa tabela zawiera omówienie elementów schematu that are related to Wyzwalacze oparte na zdarzeniach:

| **JSON Element** | **Opis** | **Typ** | **Dozwolone wartości** | **Wymagane** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **Zakres** | Identyfikator zasobu usługi Azure Resource Manager na koncie magazynu. | Ciąg | Identyfikator usługi Azure Resource Manager | Yes |
| **Zdarzenia** | Typ zdarzenia, które powodują uruchomienie tego wyzwalacza. | Tablica    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | Tak, dowolnej kombinacji. |
| **blobPathBeginsWith** | Ścieżka obiektu blob musi zaczynać się od wzorca przewidziane Aby wyzwalacz był uruchamiany. Na przykład "/ rekordy/obiekty BLOB/grudnia /" zostanie tylko przed uruchomieniem wyzwalacza dla obiektów blob w folderze grudnia w kontenerze rekordów. | Ciąg   | | Należy podać co najmniej jedną z tych właściwości: blobPathBeginsWith, blobPathEndsWith. |
| **blobPathEndsWith** | Ścieżka obiektu blob musi kończyć się przy użyciu wzorca przewidziane Aby wyzwalacz był uruchamiany. Na przykład "december/boxes.csv" będzie tylko przed uruchomieniem wyzwalacza dla obiektów blob o nazwie pola w folderze grudnia. | Ciąg   | | Należy podać co najmniej jedną z tych właściwości: blobPathBeginsWith, blobPathEndsWith. |

## <a name="examples-of-event-based-triggers"></a>Przykłady Wyzwalacze oparte na zdarzeniach

Ta sekcja zawiera przykłady ustawień wyzwalacza opartego na zdarzeniach.

-   **Ścieżka obiektu blob, który rozpoczyna się od**("/ containername /") — odbiera zdarzenia dla dowolnego obiektu blob w kontenerze.
-   **Ścieżka obiektu blob, który rozpoczyna się od**("/ containername/obiekty BLOB/foldername") — odbiera zdarzenia dla żadnych obiektów blob w kontenerze containername i folderze nazwa_folderu.
-   **Ścieżka obiektu blob, który rozpoczyna się od**("/ containername/blobs/foldername/file.txt") — odbiera zdarzenia dla obiektu blob o nazwie plik.txt w folderze nazwa_folderu containername kontener.
-   **Ścieżka obiektu blob kończy się**("plik.txt") — odbiera zdarzenia dla obiektu blob o nazwie plik.txt wszystkie ścieżce.
-   **Ścieżka obiektu blob kończy się**("/ containername/blobs/file.txt") — odbiera zdarzenia dla obiektu blob o nazwie plik.txt w obszarze containername kontener.
-   **Ścieżka obiektu blob kończy się**("foldername/file.txt") — odbiera zdarzenia dla obiektu blob o nazwie plik.txt w folderze nazwa_folderu w dowolnym kontenerze.

> [!NOTE]
> Musi zawierać `/blobs/` segmentu ścieżki po każdym Określ kontener oraz folder, kontener i plik lub kontener, folder i plik.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać szczegółowe informacje na temat wyzwalaczy, zobacz [wyzwalacze i wykonywanie potoku](concepts-pipeline-execution-triggers.md#triggers).
