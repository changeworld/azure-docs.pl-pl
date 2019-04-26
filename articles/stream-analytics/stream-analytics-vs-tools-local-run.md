---
title: Testowanie zapytań usługi Azure Stream Analytics lokalnie z programem Visual Studio
description: W tym artykule opisano sposób testowania zapytań lokalnie za pomocą usługi Azure Stream Analytics Tools for Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: 1b86085a76f5ff87147db9dbd0a584784f5e4a2e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60308490"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio"></a>Testowanie zapytań usługi Stream Analytics lokalnie z programem Visual Studio

Azure Stream Analytics tools for Visual Studio można użyć do testowania zadań usługi Stream Analytics lokalnie z przykładowymi danymi.

Użyj tego [Szybki Start](stream-analytics-quick-create-vs.md) Aby dowiedzieć się, jak utworzyć zadanie usługi Stream Analytics, za pomocą programu Visual Studio.

## <a name="test-your-query"></a>Testowanie zapytania

W projekcie usługi Azure Stream Analytics, kliknij dwukrotnie **Script.asaql** aby otworzyć skrypt w edytorze. Można skompilować zapytania, aby sprawdzić, czy są jakieś błędy składni. Edytor zapytań obsługuje funkcję IntelliSense, kolorowanie składni i znacznika błąd.

![Edytor zapytań](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="add-local-input"></a>Dodawanie lokalnych danych wejściowych

Aby sprawdzić poprawność zapytania względem lokalnych danych statycznych, kliknij prawym przyciskiem myszy dane wejściowe i wybierz pozycję **dodawanie lokalnych danych wejściowych**.
   
![Dodawanie lokalnych danych wejściowych](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-01.png)
   
W oknie podręcznym wybierz przykładowych danych z ścieżki lokalnej i **Zapisz**.
   
![Dodawanie lokalnych danych wejściowych](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-02.png)
   
Plik o nazwie **local_EntryStream.json** jest automatycznie dodawany do folderu danych wejściowych.
   
![Lista plików lokalnego folderu danych wejściowych](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-03.png)
   
Wybierz **uruchom lokalnie** w edytorze zapytań. Lub możesz nacisnąć klawisz F5.
   
![Uruchom lokalnie](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-01.png)
   
Dane wyjściowe mogą być wyświetlane w formacie tabeli bezpośrednio z programu Visual Studio.

![Dane wyjściowe w formacie tabeli](./media/stream-analytics-vs-tools-local-run/stream-analytics-for-vs-local-result.png)

Można znaleźć ścieżki wyjściowej z danych wyjściowych konsoli. Naciśnij dowolny klawisz, aby otworzyć folder wyników.
   
![Uruchamianie lokalne](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-02.png)
   
Sprawdź wyniki w folderze lokalnym.
   
![Lokalny folder wyników](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-03.png)
   

### <a name="sample-input"></a>Przykładowe dane wejściowe
Przykładowe dane wejściowe może również zbierać ze swoich źródeł wejściowych do pliku lokalnego. Kliknij prawym przyciskiem myszy plik konfiguracyjny danych wejściowych, a następnie wybierz pozycję **przykładowych danych**. 

![Dane przykładowe](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-01.png)

Możesz to zrobić tylko przykładowych danych przesyłanych strumieniowo z usługi Event Hubs lub centra IoT Hub. Inne źródła wejściowe nie są obsługiwane. W podręcznym oknie dialogowym, wprowadź ścieżkę lokalną, aby zapisać przykładowe dane, a następnie wybierz **przykładowe**.

![Przykładowa konfiguracja danych](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-02.png)
 
Można wyświetlić postęp w **dane wyjściowe** okna. 

![Przykładowe dane wyjściowe](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-03.png)

## <a name="next-steps"></a>Kolejne kroki

* [Use Visual Studio to view Azure Stream Analytics jobs (Wyświetlanie zadań usługi Azure Stream Analytics za pomocą programu Visual Studio)](stream-analytics-vs-tools.md)
* [Szybki start: Tworzenie zadania usługi Stream Analytics, za pomocą programu Visual Studio](stream-analytics-quick-create-vs.md)
* [Samouczek: Wdrażanie zadania usługi Azure Stream Analytics przy użyciu ciągłej integracji/ciągłego wdrażania za pomocą DevOps platformy Azure](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Ciągła integracja i ciągłe opracowywanie za pomocą narzędzi usługi Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)