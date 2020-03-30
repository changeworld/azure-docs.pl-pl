---
title: Testowanie zapytań usługi Azure Stream Analytics lokalnie względem danych wejściowych strumienia na żywo przy użyciu kodu programu Visual Studio
description: W tym artykule opisano sposób testowania zapytań lokalnie względem danych wejściowych strumienia na żywo przy użyciu narzędzi Azure Stream Analytics Tools for Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: 34ce91a1385f951847abeedd3a6b526d3a07af35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75660855"
---
# <a name="test-stream-analytics-queries-locally-against-live-stream-input-by-using-visual-studio-code"></a>Testowanie usługi Stream Analytics zapytania lokalnie względem danych wejściowych strumienia na żywo przy użyciu programu Visual Studio Code

Za pomocą narzędzia Azure Stream Analytics Tools for Visual Studio Code można lokalnie przetestować zadania usługi Stream Analytics na danych wejściowych strumienia na żywo. Dane wejściowe mogą pochodzić ze źródła, takiego jak usługi Azure Event Hubs lub Usługi Azure IoT Hub. Wyniki wyjściowe są wysyłane jako pliki JSON do folderu w projekcie o nazwie **LocalRunOutputs**.

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj podstawowy pakiet [SDK platformy .NET](https://dotnet.microsoft.com/download) i uruchom ponownie kod programu Visual Studio.

* Skorzystaj z [tego przewodnika Szybki start,](quick-create-vs-code.md) aby dowiedzieć się, jak utworzyć zadanie usługi Stream Analytics przy użyciu programu Visual Studio Code.

## <a name="define-a-live-stream-input"></a>Definiowanie danych wejściowych na żywo

1. Kliknij prawym przyciskiem myszy folder **Inputs** w projekcie usługi Stream Analytics. Następnie wybierz **ASA: Dodaj dane wejściowe** z menu kontekstowego.

   ![Dodawanie danych wejściowych z folderu Wejścia](./media/quick-create-vs-code/add-input-from-inputs-folder.png)

   Można również wybrać **klawisze Ctrl+Shift+P,** aby otworzyć paletę poleceń i wprowadzić **ASA: Dodaj dane wejściowe**.

   ![Dodawanie danych wejściowych usługi Stream Analytics w programie Visual Studio Code](./media/quick-create-vs-code/add-input.png)

2. Wybierz typ źródła wejściowego z listy rozwijanej.

   ![Wybierz koncentrator IoT jako opcję wprowadzania](./media/quick-create-vs-code/iot-hub.png)

3. Jeśli dodano dane wejściowe z palety poleceń, wybierz skrypt zapytania usługi Stream Analytics, który będzie używał danych wejściowych. Powinien być automatycznie wypełniany ścieżką pliku do **myASAproj.asaql**.

   ![Wybieranie skryptu usługi Stream Analytics w programie Visual Studio Code](./media/quick-create-vs-code/asa-script.png)

4. Z menu rozwijanego wybierz pozycję **Wybierz subskrypcje platformy Azure.**

    ![Wybierz z subskrypcji](./media/quick-create-vs-code/add-input-select-subscription.png)

5. Skonfiguruj nowo wygenerowany plik JSON. Funkcja CodeLens ułatwia wprowadzanie ciągu, wybieranie z listy rozwijanej lub zmianę tekstu bezpośrednio w pliku. Poniższy zrzut ekranu przedstawia **Wybierz z subskrypcji** jako przykład.

   ![Konfigurowanie danych wejściowych w programie Visual Studio Code](./media/quick-create-vs-code/configure-input.png)

## <a name="preview-input"></a>Podgląd danych wejściowych

Aby upewnić się, że dane wejściowe nadchodzą, wybierz **podgląd danych** w pliku konfiguracji danych wejściowych na żywo z górnej linii. Niektóre dane wejściowe pochodzą z centrum IoT i są wyświetlane w oknie podglądu. Wyświetlenie podglądu może potrwać kilka sekund.

 ![Podgląd danych wejściowych na żywo](./media/quick-create-vs-code/preview-live-input.png)

## <a name="run-queries-locally"></a>Uruchamianie zapytań lokalnie

Wróć do edytora zapytań i wybierz pozycję **Uruchom lokalnie**. Następnie wybierz **pozycję Użyj wejścia na żywo** z listy rozwijanej.

![Wybierz "Uruchom lokalnie" w edytorze zapytań](./media/vscode-local-run/run-locally.png)

![Wybierz "Użyj wejścia na żywo"](./media/vscode-local-run-live-input/run-locally-use-live-input.png)

Wynik jest wyświetlany w prawym oknie i odświeżany co 3 sekundy. Możesz wybrać **Uruchom,** aby przetestować ponownie. Można również wybrać **otwórz w folderze,** aby wyświetlić pliki wyników w Eksploratorze plików i otworzyć je za pomocą programu Visual Studio Code lub narzędzia takiego jak Excel. Należy zauważyć, że pliki wyników są dostępne tylko w formacie JSON.

Domyślny czas rozpoczęcia tworzenia danych wyjściowych zadania jest ustawiony na **Teraz**. Można dostosować czas, wybierając przycisk **Czas rozpoczęcia wyjścia** w oknie wyników.

![Wyświetlanie lokalnego wyniku biegu](./media/vscode-local-run-live-input/vscode-livetesting.gif)

## <a name="next-steps"></a>Następne kroki

* [Poznaj zadania usługi Azure Stream Analytics za pomocą kodu programu Visual Studio (wersja zapoznawcza)](visual-studio-code-explore-jobs.md)

* [Konfigurowanie potoków ciągłej integracji/ciągłego wdrażania przy użyciu pakietu npm](setup-cicd-vs-code.md)
