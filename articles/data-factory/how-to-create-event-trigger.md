---
title: Tworzenie wyzwalaczy opartych na zdarzeniach w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć wyzwalacz w usłudze Azure Data Factory uruchamia potok w odpowiedzi na zdarzenie.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/18/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 94c9c3f997143d72262c1ba3d8dbfea90d6f920c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61347725"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Utwórz wyzwalacz, który uruchamia potok w odpowiedzi na zdarzenie

W tym artykule opisano wyzwalacze oparte na zdarzeniach, które można tworzyć w potoków usługi Data Factory.

Architektura sterowana zdarzeniami (EDA) jest to typowy wzorzec integracji danych, która obejmuje produkcji, wykrywanie, wykorzystania i reagowania na zdarzenia. Kompleksowe scenariusze integracji danych często wymagają klientów usługi Data Factory wyzwalanie potoków w oparciu o zdarzenia. Fabryka danych jest teraz zintegrowana z [usługi Azure Event Grid](https://azure.microsoft.com/services/event-grid/), pozwalającej możesz wyzwolić potoków na zdarzenie.

Wprowadzenie 10 minutowy i pokaz działania tej funkcji Obejrzyj poniższy film wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> Integracja opisanych w tym artykule jest zależna od [usługi Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Upewnij się, że Twoja subskrypcja jest zarejestrowana za pomocą dostawcy zasobów usługi Event Grid. Aby uzyskać więcej informacji, zobacz [dostawcy zasobów i ich typy](../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

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

Gdy zdarzenie wyzwalacza dla konkretnego obiektu blob, zdarzenie przechwytuje ścieżkę i nazwę folderu obiektu blob do właściwości `@triggerBody().folderPath` i `@triggerBody().fileName`. Aby użyć wartości tych właściwości w potoku, należy zamapować właściwości do parametrów potoku. Po mapowania właściwości do parametrów, można uzyskać dostęp do wartości przechwycone przez wyzwalacz za pomocą `@pipeline().parameters.parameterName` wyrażenia w całym potoku.

![Mapowanie właściwości do parametrów potoku](media/how-to-create-event-trigger/event-based-trigger-image4.png)

Na przykład w poprzednim zrzucie ekranu. wyzwalacz jest skonfigurowany do uruchomi się, jeśli ścieżka obiektu blob, kończy się rozszerzeniem `.csv` jest tworzony na koncie magazynu. W rezultacie, gdy obiekt blob z `.csv` rozszerzenia jest tworzony w dowolnym miejscu na koncie magazynu `folderPath` i `fileName` właściwości przechwytywania lokalizacji nowy obiekt blob. Na przykład `@triggerBody().folderPath` ma wartość, takich jak `/containername/foldername/nestedfoldername` i `@triggerBody().fileName` ma wartość, takich jak `filename.csv`. Te wartości są mapowane w przykładzie do parametrów potoku `sourceFolder` i `sourceFile`. Można ich używać w całym potoku jako `@pipeline().parameters.sourceFolder` i `@pipeline().parameters.sourceFile` odpowiednio.

## <a name="json-schema"></a>Schemat JSON

Poniższa tabela zawiera omówienie elementów schematu that are related to Wyzwalacze oparte na zdarzeniach:

| **JSON Element** | **Opis** | **Typ** | **Dozwolone wartości** | **Wymagane** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **Zakres** | Identyfikator zasobu usługi Azure Resource Manager na koncie magazynu. | String | Identyfikator usługi Azure Resource Manager | Yes |
| **Zdarzenia** | Typ zdarzenia, które powodują uruchomienie tego wyzwalacza. | Tablica    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | Tak, dowolnej kombinacji tych wartości. |
| **blobPathBeginsWith** | Ścieżka obiektu blob musi zaczynać się od wzorca parametru Aby uruchomić wyzwalacz. Na przykład `/records/blobs/december/` tylko uruchamia wyzwalacz dla obiektów blob w `december` folderze `records` kontenera. | String   | | Należy podać wartość dla co najmniej jednej z tych właściwości: `blobPathBeginsWith` lub `blobPathEndsWith`. |
| **blobPathEndsWith** | Ścieżka obiektu blob musi kończyć się przy użyciu wzorca parametru Aby uruchomić wyzwalacz. Na przykład `december/boxes.csv` tylko uruchamia wyzwalacz dla obiektów blob o nazwie `boxes` w `december` folderu. | String   | | Należy podać wartość dla co najmniej jednej z tych właściwości: `blobPathBeginsWith` lub `blobPathEndsWith`. |

## <a name="examples-of-event-based-triggers"></a>Przykłady Wyzwalacze oparte na zdarzeniach

Ta sekcja zawiera przykłady ustawień wyzwalacza opartego na zdarzeniach.

> [!IMPORTANT]
> Musi zawierać `/blobs/` segment ścieżki, jak pokazano w poniższych przykładach zawsze wtedy, gdy Określ kontener oraz folder, kontener i plik lub kontener, folder i plik.

| Właściwość | Przykład | Opis |
|---|---|---|
| **Ścieżka obiektu blob, który rozpoczyna się od** | `/containername/` | Odbiera zdarzenia dla wszystkich obiektów blob w kontenerze. |
| **Ścieżka obiektu blob, który rozpoczyna się od** | `/containername/blobs/foldername/` | Odbiera zdarzenia dla wszystkie obiekty BLOB w `containername` kontenera i `foldername` folderu. |
| **Ścieżka obiektu blob, który rozpoczyna się od** | `/containername/blobs/foldername/subfoldername/` | Możesz też przywołać podfolderu. |
| **Ścieżka obiektu blob, który rozpoczyna się od** | `/containername/blobs/foldername/file.txt` | Odbiera zdarzenia dla obiektu blob o nazwie `file.txt` w `foldername` folderze `containername` kontenera. |
| **Ścieżka obiektu blob, który kończy się ciągiem** | `file.txt` | Odbiera zdarzenia dla obiektu blob o nazwie `file.txt` w dowolnej ścieżce. |
| **Ścieżka obiektu blob, który kończy się ciągiem** | `/containername/blobs/file.txt` | Odbiera zdarzenia dla obiektu blob o nazwie `file.txt` w kontenerze `containername`. |
| **Ścieżka obiektu blob, który kończy się ciągiem** | `foldername/file.txt` | Odbiera zdarzenia dla obiektu blob o nazwie `file.txt` w `foldername` folder w dowolnym kontenerze. |

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać szczegółowe informacje na temat wyzwalaczy, zobacz [wyzwalacze i wykonywanie potoku](concepts-pipeline-execution-triggers.md#triggers).
