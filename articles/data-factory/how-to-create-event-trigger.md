---
title: Tworzenie wyzwalaczy opartych na zdarzeniach w Azure Data Factory | Microsoft Docs
description: Dowiedz się, jak utworzyć wyzwalacz w Azure Data Factory, który uruchamia potok w odpowiedzi na zdarzenie.
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
ms.openlocfilehash: 32edacb7dd66274757359c4eb0e8c169995026ce
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019527"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Tworzenie wyzwalacza uruchamiającego potok w odpowiedzi na zdarzenie

W tym artykule opisano wyzwalacze oparte na zdarzeniach, które można utworzyć w potokach Data Factory.

Architektura sterowana zdarzeniami (EDA) to typowy wzorzec integracji danych, który obejmuje produkcję, wykrywanie, użycie i reagowanie na zdarzenia. Scenariusze integracji danych często wymagają Data Factory klientów do wyzwalania potoków w oparciu o zdarzenia, takie jak przyjęcie lub usunięcie pliku na koncie usługi Azure Storage. Data Factory jest teraz zintegrowana z [Azure Event Grid](https://azure.microsoft.com/services/event-grid/), co umożliwia wyzwalanie potoków na zdarzeniu.

W przypadku 10-minutowego wprowadzenia i pokazania tej funkcji Obejrzyj następujące wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> Integracja opisana w tym artykule zależy od [Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Upewnij się, że subskrypcja została zarejestrowana przy użyciu dostawcy zasobów Event Grid. Aby uzyskać więcej informacji, zobacz [dostawcy zasobów i ich typy](../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

## <a name="data-factory-ui"></a>Interfejs użytkownika usługi Data Factory

W tej sekcji pokazano, jak utworzyć wyzwalacz zdarzeń w Azure Data Factory interfejsie użytkownika.

1. Przejdź do **kanwy tworzenia**

2. W lewym dolnym rogu kliknij przycisk **wyzwalacze**

3. Kliknij pozycję **+ Nowy** , aby otworzyć stronę Utwórz wyzwalacz nawigacyjny

4. Wybierz **zdarzenie** typu wyzwalacza

![Utwórz nowy wyzwalacz zdarzeń](media/how-to-create-event-trigger/event-based-trigger-image1.png)

5. Wybierz konto magazynu z listy rozwijanej subskrypcja platformy Azure lub ręcznie przy użyciu identyfikatora zasobu konta magazynu. Wybierz kontener, w którym mają być wykonywane zdarzenia. Wybór kontenera jest opcjonalny, ale pamiętaj, że wybranie wszystkich kontenerów może prowadzić do dużej liczby zdarzeń.

   > [!NOTE]
   > Wyzwalacz zdarzenia obecnie obsługuje tylko konta magazynu Azure Data Lake Storage Gen2 i ogólnego przeznaczenia w wersji 2. Ze względu na ograniczenie Azure Event Grid Azure Data Factory obsługuje maksymalnie 500 wyzwalaczy zdarzeń na konto magazynu.

6. **Ścieżka obiektu BLOB rozpoczyna się od** , a **Ścieżka obiektu BLOB zostanie zakończona z** właściwościami, co pozwala na określenie kontenerów, folderów i nazw obiektów blob, dla których mają być odbierane zdarzenia. Wyzwalacz zdarzenia wymaga zdefiniowania co najmniej jednej z tych właściwości. Można użyć różnych wzorców dla **ścieżki obiektu BLOB zaczyna** się od, a **Ścieżka obiektu BLOB zostanie zakończona z** właściwościami, jak pokazano w przykładach w dalszej części tego artykułu.

    * **Ścieżka obiektu BLOB zaczyna się od:** Ścieżka obiektu BLOB musi rozpoczynać się od ścieżki folderu. Prawidłowe wartości to `2018/` include `2018/april/shoes.csv`i. Nie można wybrać tego pola, jeśli nie wybrano kontenera.
    * **Ścieżka obiektu BLOB zostanie zakończona z:** Ścieżka obiektu BLOB musi kończyć się nazwą pliku lub rozszerzeniem. Prawidłowe wartości to `shoes.csv` include `.csv`i. Nazwy kontenerów i folderów są opcjonalne, ale jeśli są określone, muszą być oddzielone `/blobs/` segmentami. Na przykład kontener o nazwie Orders może mieć wartość `/orders/blobs/2018/april/shoes.csv`. Aby określić folder w dowolnym kontenerze, Pomiń wiodący znak "/". Na przykład `april/shoes.csv` program wyzwoli zdarzenie na dowolnym pliku o nazwie `shoes.csv` w folderze a o nazwie "Kwiecień" w dowolnym kontenerze. 

7. Wybierz, czy wyzwalacz będzie reagować na zdarzenie **utworzone przez obiekt BLOB** , **usunięte zdarzenie obiektu BLOB** lub oba te elementy. W określonej lokalizacji przechowywania każde zdarzenie wywoła potoki Data Factory skojarzone z wyzwalaczem.

    ![Konfigurowanie wyzwalacza zdarzeń](media/how-to-create-event-trigger/event-based-trigger-image2.png)

8. Po skonfigurowaniu wyzwalacza kliknij przycisk **dalej: Podgląd**danych. Na tym ekranie są wyświetlane istniejące obiekty blob dopasowane przez konfigurację wyzwalacza zdarzeń. Upewnij się, że masz określone filtry. Skonfigurowanie zbyt szerokich filtrów może być zgodne z dużą liczbą utworzonych/usuniętych plików i może znacząco wpłynąć na koszt. Po zweryfikowaniu warunków filtrowania kliknij przycisk **Zakończ**.

    ![Podgląd danych wyzwalacza zdarzeń](media/how-to-create-event-trigger/event-based-trigger-image3.png)

9. Aby dołączyć potok do tego wyzwalacza, przejdź do kanwy potoku, a następnie kliknij pozycję **Dodaj wyzwalacz** i wybierz pozycję **Nowy/Edytuj**. Gdy zostanie wyświetlona strona nawigacji bocznej, kliknij pozycję **Wybierz wyzwalacz...** listy rozwijanej i wybierz utworzony wyzwalacz. Kliknij pozycję **Next: Podgląd** danych, aby potwierdzić, że konfiguracja jest poprawna, a następnie **obok pozycji** Sprawdź poprawność wersji zapoznawczej.

10. Jeśli potok zawiera parametry, można je określić dla wyzwalacza uruchamia po stronie parametru. Wyzwalacz zdarzenia przechwytuje ścieżkę folderu i nazwę pliku obiektu BLOB do właściwości `@triggerBody().folderPath` i. `@triggerBody().fileName` Aby użyć wartości tych właściwości w potoku, należy zmapować właściwości na parametry potoku. Po zamapowaniu właściwości na parametry można uzyskać dostęp do wartości przechwytywanych przez wyzwalacz za pomocą `@pipeline().parameters.parameterName` wyrażenia w ramach potoku. Po zakończeniu kliknij przycisk **Zakończ** .

    ![Mapowanie właściwości do parametrów potoku](media/how-to-create-event-trigger/event-based-trigger-image4.png)

W poprzednim przykładzie wyzwalacz jest skonfigurowany do uruchamiania, gdy ścieżka obiektu BLOB kończąca się w. csv zostanie utworzona w folderze Event-Tests w kontenerze przykład — dane. Właściwości **folderPath** i **filename** przechwytują lokalizację nowego obiektu BLOB. Na przykład po dodaniu MoviesDB. CSV do przykładowej ścieżki- `@triggerBody().folderPath` dane/testowanie zdarzeń ma `sample-data/event-testing` wartość `moviesDB.csv`i `@triggerBody().fileName` ma wartość. Te wartości są mapowane w `sourceFolder` przykładzie do parametrów potoku, `sourceFile` które mogą być używane w `@pipeline().parameters.sourceFile` całym potoku odpowiednio `@pipeline().parameters.sourceFolder` w zależności od siebie.

## <a name="json-schema"></a>Schemat JSON

Poniższa tabela zawiera omówienie elementów schematu, które są powiązane z wyzwalaczami opartymi na zdarzeniach:

| **Element JSON** | **Opis** | **Typ** | **Dozwolone wartości** | **Wymagane** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **Scope** | Identyfikator zasobu Azure Resource Manager konta magazynu. | String | Identyfikator Azure Resource Manager | Tak |
| **wydarzeniach** | Typ zdarzeń, które powodują uruchomienie tego wyzwalacza. | Array    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | Tak, dowolna kombinacja tych wartości. |
| **blobPathBeginsWith** | Ścieżka obiektu BLOB musi rozpoczynać się od wzorca dostarczonego dla wyzwalacza. Na przykład `/records/blobs/december/` wyzwala wyzwalacz dla obiektów BLOB `december` w folderze `records` w kontenerze. | String   | | Musisz podać wartość dla co najmniej jednej z następujących właściwości: `blobPathBeginsWith` lub. `blobPathEndsWith` |
| **blobPathEndsWith** | Ścieżka obiektu BLOB musi kończyć się wzorcem podanym dla wyzwalacza. Na przykład `december/boxes.csv` wyzwala wyzwalacz dla obiektów BLOB o nazwie `boxes` w `december` folderze. | String   | | Musisz podać wartość dla co najmniej jednej z następujących właściwości: `blobPathBeginsWith` lub. `blobPathEndsWith` |

## <a name="examples-of-event-based-triggers"></a>Przykłady wyzwalaczy opartych na zdarzeniach

Ta sekcja zawiera przykłady ustawień wyzwalaczy opartych na zdarzeniach.

> [!IMPORTANT]
> Musisz dołączyć `/blobs/` segment ścieżki, jak pokazano w poniższych przykładach, po określeniu kontenera i folderu, kontenera i pliku lub kontenera, folderu i pliku. W przypadku **blobPathBeginsWith**interfejs użytkownika Data Factory automatycznie dodaje `/blobs/` między folderem a nazwą kontenera w kodzie JSON wyzwalacza.

| Właściwość | Przykład | Opis |
|---|---|---|
| **Ścieżka obiektu BLOB zaczyna się od** | `/containername/` | Odbiera zdarzenia dla dowolnego obiektu BLOB w kontenerze. |
| **Ścieżka obiektu BLOB zaczyna się od** | `/containername/blobs/foldername/` | Odbiera zdarzenia dla wszystkich obiektów BLOB w `containername` kontenerze `foldername` i folderze. |
| **Ścieżka obiektu BLOB zaczyna się od** | `/containername/blobs/foldername/subfoldername/` | Można również odwoływać się do podfolderu. |
| **Ścieżka obiektu BLOB zaczyna się od** | `/containername/blobs/foldername/file.txt` | Odbiera zdarzenia dla obiektu BLOB o `file.txt` nazwie `foldername` w folderze `containername` znajdującym się w kontenerze. |
| **Ścieżka obiektu BLOB zostanie zakończona z** | `file.txt` | Odbiera zdarzenia dla obiektu BLOB o `file.txt` nazwie w dowolnej ścieżce. |
| **Ścieżka obiektu BLOB zostanie zakończona z** | `/containername/blobs/file.txt` | Odbiera zdarzenia dla obiektu BLOB o `file.txt` nazwie w `containername`kontenerze. |
| **Ścieżka obiektu BLOB zostanie zakończona z** | `foldername/file.txt` | Odbiera zdarzenia dla obiektu BLOB o `file.txt` nazwie `foldername` w folderze w dowolnym kontenerze. |

## <a name="next-steps"></a>Następne kroki
Aby uzyskać szczegółowe informacje na temat wyzwalaczy, zobacz [wykonywanie i wyzwalacze potoku](concepts-pipeline-execution-triggers.md#triggers).
