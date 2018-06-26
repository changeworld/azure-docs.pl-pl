---
title: Tworzenie wyzwalaczy oparty na zdarzeniach w fabryce danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć wyzwalacza w fabryce danych Azure, uruchamiany potoku w odpowiedzi na zdarzenie.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/24/2018
ms.author: douglasl
ms.openlocfilehash: 2bcb0d4e6af00b56d083690439be45379ce4d175
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36752813"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Utwórz wyzwalacz uruchamia potoku w odpowiedzi na zdarzenia

W tym artykule opisano wyzwalacze oparty na zdarzeniach utworzonych w potokach z fabryki danych.

Sterowane zdarzeniami architektura (EDA) jest wspólnego wzorca integracji danych, która obejmuje produkcji, wykrywania zużycia i reagowania na zdarzenia. Scenariusze integracji danych często wymagają fabryki danych klientów do wyzwolenia potoki na podstawie zdarzeń.

## <a name="data-factory-ui"></a>Interfejs użytkownika usługi Data Factory

### <a name="create-a-new-event-trigger"></a>Utwórz nowy wyzwalacz zdarzeń

Typowe zdarzenia jest przybyciu pliku lub usunięcie pliku na koncie magazynu Azure. Można utworzyć wyzwalaczy, które odpowiada to zdarzenie planowaną fabryki danych.

> [!NOTE]
> Integracja ta obsługuje tylko w wersji 2 kont magazynu (ogólnego przeznaczenia).

![Utwórz nowy wyzwalacz zdarzeń](media/how-to-create-event-trigger/event-based-trigger-image1.png)

### <a name="select-the-event-trigger-type"></a>Wybierz typ wyzwalacza zdarzenia

Natychmiast po odebraniu pliku w lokalizacji magazynu i odpowiedniego obiektu blob jest tworzona, to zdarzenie jest wyzwalane i uruchamia potoku fabryki danych w sieci. Można utworzyć wyzwalacza, który reaguje na zdarzenia tworzenia obiektów blob, zdarzenie usuwania obiektów blob lub obu zdarzeń w potokach z fabryki danych.

![Typ wyzwalacza wybierz zdarzenie](media/how-to-create-event-trigger/event-based-trigger-image2.png)

### <a name="configure-the-event-trigger"></a>Skonfiguruj wyzwalacza zdarzenia

Z **rozpoczyna się od ścieżki obiektu Blob** i **obiektu Blob ścieżka kończy się** właściwości, można określić kontenerów, folderów i nazwach obiektów blob, dla których chcesz otrzymywać zdarzeń. Można użyć różnych wzorców dla obu **rozpoczyna się od ścieżki obiektu Blob** i **obiektu Blob ścieżka kończy się** właściwości, jak przedstawiono w przykładach w dalszej części tego artykułu. Co najmniej jeden z tych właściwości jest wymagana.

![Skonfiguruj wyzwalacza zdarzenia](media/how-to-create-event-trigger/event-based-trigger-image3.png)

## <a name="json-schema"></a>Schematu JSON

Poniższa tabela zawiera omówienie elementów schematu, które są związane z wyzwalaczy oparty na zdarzeniach:

| **JSON Element** | **Opis** | **Typ** | **Dozwolone wartości** | **Wymagane** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **Zakres** | Identyfikator zasobu usługi Azure Resource Manager konta magazynu. | Ciąg | Identyfikator usługi Azure Resource Manager | Yes |
| **Zdarzenia** | Typ zdarzenia, które powodują uruchomienie tego wyzwalacza. | Tablica    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | Tak, dowolną kombinację. |
| **blobPathBeginsWith** | Ścieżka obiektu blob musi rozpoczynać się od wzorca przewidzianych Aby uruchomić wyzwalacz. Na przykład "/ rekordów/obiektów blob grudnia /" tylko będzie uruchomić wyzwalacz dla obiektów blob w folderze grudnia w kontenerze rekordów. | Ciąg   | | Należy podać co najmniej jeden z tych właściwości: blobPathBeginsWith, blobPathEndsWith. |
| **blobPathEndsWith** | Ścieżka obiektu blob musi być zakończona z wzorcem przewidzianych Aby uruchomić wyzwalacz. Na przykład "december/boxes.csv" tylko będzie uruchomić wyzwalacz o nazwie pola w folderze grudnia obiektów blob. | Ciąg   | | Należy podać co najmniej jeden z tych właściwości: blobPathBeginsWith, blobPathEndsWith. |

## <a name="examples-of-event-based-triggers"></a>Przykłady wyzwalaczy oparty na zdarzeniach

Ta sekcja zawiera przykłady ustawień wyzwalacza opartego na zdarzeniach.

-   **Ścieżka obiektu blob, który rozpoczyna się od**("/ containername /") — odbiera zdarzenia dla dowolnego obiektu blob w kontenerze.
-   **Ścieżka obiektu blob, który rozpoczyna się od**("/ containername/nazwa folderu") — odbiera zdarzenia żadnych obiektów blob w kontenerze containername i nazwa_folderu folderu.
-   **Ścieżka obiektu blob, który rozpoczyna się od**("/ containername/foldername/file.txt") — odbiera zdarzenia dla obiektu blob o nazwie plik.txt w folderze nazwa_folderu w kontenerze containername.
-   **Ścieżka obiektu blob kończy się**("plik.txt") — Receive zdarzenia dla obiektu blob o nazwie plik.txt w dowolnej ścieżce.
-   **Ścieżka obiektu blob kończy się**("/ containername/file.txt") — odbiera zdarzenia dla obiektu blob o nazwie plik.txt w kontenerze containername.
-   **Ścieżka obiektu blob kończy się**("foldername/file.txt") — plik.txt w folderze nazwa_folderu każdy kontener o nazwie zdarzenia działania Receive dla obiektu blob.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać szczegółowe informacje dotyczące wyzwalaczy, zobacz [potoku wykonywania i wyzwalaczy](concepts-pipeline-execution-triggers.md#triggers).
