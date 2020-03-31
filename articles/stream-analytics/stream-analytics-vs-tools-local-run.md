---
title: Testowanie zapytań usługi Azure Stream Analytics lokalnie w programie Visual Studio
description: W tym artykule opisano sposób testowania zapytań lokalnie za pomocą narzędzia Azure Stream Analytics Tools for Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: 34c8555356d5c0142d7b677c8119fe66806ab064
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76834913"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio"></a>Testowanie zapytań usługi Stream Analytics lokalnie za pomocą programu Visual Studio

Za pomocą narzędzi Usługi Azure Stream Analytics dla programu Visual Studio można lokalnie przetestować zadania usługi Stream Analytics przy użyciu przykładowych danych lub [danych na żywo.](stream-analytics-live-data-local-testing.md) 

Skorzystaj z tego [przewodnika Szybki start,](stream-analytics-quick-create-vs.md) aby dowiedzieć się, jak utworzyć zadanie usługi Stream Analytics za pomocą programu Visual Studio.

## <a name="test-your-query"></a>Testowanie zapytania

W projekcie usługi Azure Stream Analytics kliknij dwukrotnie **pozycję Script.asaql,** aby otworzyć skrypt w edytorze. Kwerendę można skompilować, aby sprawdzić, czy występują błędy składni. Edytor zapytań obsługuje intellisense, kolorowanie składni i znacznik błędu.

![Edytor zapytań](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="add-local-input"></a>Dodawanie danych wejściowych lokalnych

Aby sprawdzić poprawność kwerendy względem lokalnych danych statycznych, kliknij prawym przyciskiem myszy dane wejściowe i wybierz polecenie **Dodaj dane lokalne**.
   
![Dodawanie danych wejściowych lokalnych](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-01.png)
   
W wyskakującym oknie wybierz przykładowe dane ze ścieżki lokalnej i **zapisz**.
   
![Dodawanie danych wejściowych lokalnych](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-02.png)
   
Plik o nazwie **local_EntryStream.json** jest dodawany automatycznie do folderu danych wejściowych.
   
![Lista plików folderu danych wejściowych lokalnego](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-03.png)
   
Wybierz **pozycję Uruchom lokalnie** w edytorze zapytań. Możesz też nacisnąć klawisz F5.
   
![Uruchom lokalnie](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-01.png)
   
Dane wyjściowe można wyświetlić w formacie tabeli bezpośrednio z programu Visual Studio.

![Dane wyjściowe w formacie tabeli](./media/stream-analytics-vs-tools-local-run/stream-analytics-for-vs-local-result.png)

Ścieżkę wyjściową można znaleźć na wyjściu konsoli. Naciśnij dowolny klawisz, aby otworzyć folder wyników.
   
![Uruchamianie lokalne](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-02.png)
   
Sprawdź wyniki w folderze lokalnym.
   
![Wynik folderu lokalnego](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-03.png)
   

### <a name="sample-input"></a>Przykładowe dane wejściowe
Można również zbierać przykładowe dane wejściowe ze źródeł wejściowych do pliku lokalnego. Kliknij prawym przyciskiem myszy plik konfiguracji wejściowej i wybierz **polecenie Przykładowe dane**. 

![Dane przykładowe](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-01.png)

Możesz pobierać próbki przesyłania strumieniowego tylko z centrów zdarzeń lub centrów IoT. Inne źródła wejściowe nie są obsługiwane. W wyskakującym oknie dialogowym wypełnij ścieżkę lokalną, aby zapisać przykładowe dane, a następnie wybierz **pozycję Przykład .**

![Przykładowa konfiguracja danych](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-02.png)
 
Postęp można zobaczyć w oknie **Dane wyjściowe.** 

![Przykładowe dane wyjściowe](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-03.png)

## <a name="next-steps"></a>Następne kroki

* [Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu programu Visual Studio](stream-analytics-quick-create-vs.md)
* [Use Visual Studio to view Azure Stream Analytics jobs (Wyświetlanie zadań usługi Azure Stream Analytics za pomocą programu Visual Studio)](stream-analytics-vs-tools.md)
* [Testowanie danych na żywo lokalnie przy użyciu narzędzi usługi Azure Stream Analytics dla programu Visual Studio (Wersja zapoznawcza)](stream-analytics-live-data-local-testing.md)
* [Samouczek: Wdrażanie zadania usługi Azure Stream Analytics przy użyciu ciągłej integracji/ciągłego wdrażania przy użyciu usługi Azure DevOps](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Ciągła integracja i ciągłe opracowywanie za pomocą narzędzi usługi Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)
