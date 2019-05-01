---
title: Tworzenie zadania usługi Azure Stream Analytics w chmurze w programie Visual Studio Code (wersja zapoznawcza)
description: Ten przewodnik Szybki Start dowiesz się, jak rozpocząć pracę przez tworzenie zadania usługi Stream Analytics, skonfigurowanie danych wejściowych i wyjściowych oraz zdefiniowanie zapytania przy użyciu programu Visual Studio Code.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/06/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: dd6d527020bbf5e2fb510fa9605af408673e89dd
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/25/2019
ms.locfileid: "64514236"
---
# <a name="quickstart-create-an-azure-stream-analytics-cloud-job-in-visual-studio-code-preview"></a>Szybki start: Tworzenie zadania usługi Azure Stream Analytics w chmurze w programie Visual Studio Code (wersja zapoznawcza)

Ten przewodnik Szybki Start dowiesz się, jak utworzyć i uruchomić zadanie usługi Stream Analytics przy użyciu rozszerzenia usługi Azure Stream Analytics dla programu Visual Studio Code. Przykładowe zadanie odczytuje dane przesyłane strumieniowo z urządzenia usługi IoT Hub. Zdefiniujesz zadanie obliczające średnią temperaturę, gdy wynosi ona ponad 27°, i zapisujące wynikowe zdarzenia wyjściowe w magazynie obiektów blob.

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

* Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

* Zainstaluj narzędzie [Visual Studio Code](https://code.visualstudio.com/).

* Pobierz [rozszerzenie usługi Azure Stream Analytics dla programu VS Code](https://usqldownload.blob.core.windows.net/ext/asa/vscode-asa-0.0.2.vsix).

## <a name="install-the-azure-stream-analytics-extension"></a>Instalowanie rozszerzenia usługi Azure Stream Analytics

Zainstaluj rozszerzenie usługi Azure Stream Analytics z prywatnego pakietu VSIX, które zostały pobrane.

1. Otwórz program Visual Studio Code.

2. Z **rozszerzenia** w okienku po lewej stronie, wybierz wielokropek **(...)**  w prawym górnym rogu. Następnie wybierz pozycję **zainstalować z VSIX**.

   ![Zainstaluj z VSIX w programie Visual Studio Code](./media/quick-create-vs-code/install-vsix.png)

3. Wybierz rozszerzenie, pobierane jako warunek wstępny, a następnie wybierz pozycję **zainstalować**.  Może to potrwać kilka sekund.

4. Gdy instalacja została zakończona pomyślnie, wybierz pozycję **Załaduj ponownie teraz** w wyświetlonym oknie, jeśli zostanie wyświetlony monit.

5. Upewnij się, że **Azure Stream Analytics Tools** jest widoczna w Twojej **włączone rozszerzenia**.

   ![Azure Stream Analytics Tools obszarze włączone rozszerzenia programu Visual Studio Code](./media/quick-create-vs-code/enabled-extensions.png)

## <a name="activate-the-azure-stream-analytics-extension"></a>Aktywuj rozszerzenia usługi Azure Stream Analytics

1. Wybierz **Azure** ikonę na pasku działań programu VS Code. **Stream Analytics** będzie widoczna na pasku bocznym. W obszarze **usługi Stream Analytics**, wybierz opcję **logowanie do platformy Azure**. 

   ![Logowanie do platformy Azure w programie Visual Studio Code](./media/quick-create-vs-code/azure-sign-in.png)

2. Gdy użytkownik jest zalogowany, nazwę swojego konta platformy Azure pojawi się na pasku stanu, w lewym dolnym rogu okna programu VS Code.

> [!NOTE]
> Narzędzia usługi Azure Stream Analytics spowoduje automatyczne zalogowanie w następnym razem, jeśli nie możesz się wylogować. Jeśli Twoje konto ma uwierzytelniania dwuskładnikowego, zalecane jest używana telefonu uwierzytelniania, a nie przy użyciu numeru PIN.
> Jeśli masz problemy z listą zasobów, wylogowywania i zaloguj się ponownie zwykle pomaga. Aby się wylogować, wprowadź polecenie `Azure: Sign Out`.

## <a name="prepare-the-input-data"></a>Przygotowywanie danych wejściowych

Przed zdefiniowaniem zadania usługi Stream Analytics przygotuj dane, które następnie zostaną skonfigurowane jako dane wejściowe zadania. Aby przygotować dane wejściowe wymagane przez zadanie, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Wybierz pozycję **Utwórz zasób** > **Internet rzeczy** > **IoT Hub**.

3. W okienku **IoT Hub** wprowadź następujące informacje:
   
   |**Ustawienie**  |**Sugerowana wartość**  |**Opis**  |
   |---------|---------|---------|
   |Subskrypcja  | \<Twoja subskrypcja\> |  Wybierz subskrypcję platformy Azure, której chcesz użyć. |
   |Grupa zasobów   |   asaquickstart-resourcegroup  |   Wybierz pozycję **Utwórz nową** i wprowadź nazwę nowej grupy zasobów dla swojego konta. |
   |Region  |  \<Wybierz region, który jest najbliżej Twoich użytkowników\> | Wybierz lokalizację geograficzną, w której możesz hostować swoje centrum IoT Hub. Użyj lokalizacji znajdującej się najbliżej użytkowników. |
   |Nazwa centrum IoT Hub  | MyASAIoTHub  |   Wybierz nazwę swojego centrum IoT Hub.   |

   ![Tworzenie centrum IoT Hub](./media/quick-create-vs-code/create-iot-hub.png)

4. Wybierz opcję **Dalej: Ustaw rozmiar i skalę**.

5. Wybierz wartość pozycji **Warstwa cenowa i warstwa skali**. Na potrzeby tego przewodnika Szybki start wybierz warstwę **F1 — Bezpłatna**, jeśli jest ona nadal dostępna w ramach subskrypcji. Jeśli warstwa bezpłatna jest niedostępna, użyj najniższej dostępnej warstwy. Aby uzyskać więcej informacji, zobacz [cennik usługi IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Rozmiar i skala centrum IoT Hub](./media/quick-create-vs-code/iot-hub-size-and-scale.png)

6. Wybierz pozycję **Przegląd + utwórz**. Przejrzyj informacje o centrum IoT Hub, a następnie kliknij pozycję **Utwórz**. Proces tworzenia centrum IoT Hub może potrwać kilka minut. Postępy możesz monitorować w okienku **Powiadomienia**.

7. W menu nawigacji centrum IoT Hub kliknij pozycję **Dodaj** w obszarze **Urządzenia IoT**. Dodaj **identyfikator urządzenia** i kliknij przycisk **Zapisz**.

   ![Dodawanie urządzenia do centrum IoT Hub](./media/quick-create-vs-code/add-device-iot-hub.png)

8. Po utworzeniu urządzenia otwórz je z poziomu listy **Urządzenia IoT**. Skopiuj wartość pola **Parametry połączenia — klucz podstawowy** i zapisz ją w notatniku do późniejszego użycia.

   ![Kopiowanie parametrów połączenia urządzenia usługi IoT Hub](./media/quick-create-vs-code/save-iot-device-connection-string.png)

## <a name="create-blob-storage"></a>Tworzenie magazynu obiektów blob

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz zasób** > **Magazyn** > **Konto magazynu**.

2. W okienku **Utwórz konto magazynu** wprowadź nazwę konta magazynu, lokalizację i grupę zasobów. Wybierz tę samą lokalizację i grupę zasobów, co w przypadku utworzonego centrum IoT Hub. Następnie kliknij pozycję **Przeglądanie + tworzenie**, aby utworzyć konto.

   ![Tworzenie konta magazynu](./media/quick-create-vs-code/create-storage-account.png)

3. Po utworzeniu konta magazynu wybierz kafalek **Obiekty blob** w okienku **Przegląd**.

   ![Omówienie kont magazynu](./media/quick-create-vs-code/blob-storage.png)

4. Na stronie **Blob Service** wybierz pozycję **Kontener** i podaj nazwę swojego kontenera, na przykład *container1*. W polu **Poziom dostępu publicznego** pozostaw wartość **Prywatny (bez dostępu anonimowego)** i wybierz przycisk **OK**.

   ![Tworzenie kontenera obiektów blob](./media/quick-create-vs-code/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Tworzenie projektu usługi Stream Analytics

1. W programie Visual Studio Code, naciśnij klawisz **Ctrl + Shift + P** aby otworzyć paletę poleceń. Następnie wpisz **ASA** i wybierz **ASA: Utwórz nowy projekt**.

   ![Tworzenie nowego projektu](./media/quick-create-vs-code/create-new-project.png)

2. Wprowadź nazwę projektu, takie jak **myASAproj** i wybierz folder dla projektu.

    ![Utwórz nazwę projektu](./media/quick-create-vs-code/create-project-name.png)

3. Nowy projekt zostanie dodany do obszaru roboczego. Projekt ASA składa się z skrypt zapytania **(*.asaql)**, **JobConfig.json** pliku, a **asaproj.json** pliku konfiguracji.

   ![Stream Analytics pliki do projektu w programie VS Code](./media/quick-create-vs-code/asa-project-files.png)

4. Plik konfiguracyjny asaproj.json zawiera dane wejściowe i dane wyjściowe zadania pliku informacje o konfiguracji potrzebne do przesyłania zadania ASA na platformie Azure.

   ![Plik konfiguracji zadania Stream Analytics w programie VS Code](./media/quick-create-vs-code/job-configuration.png)

> [!Note]
> Podczas dodawania danych wejściowych i wyjściowych z palety poleceń, odpowiednich ścieżkach zostaną dodane do **asaproj.json** automatycznie. Jeśli dodasz lub bezpośrednio usunąć dane wejściowe lub wyjściowe na dysku, musisz ręcznie dodać lub usunąć je z **asaproj.json**. Możesz umieścić dane wejściowe i dane wyjściowe w jednym miejscu, a następnie odwoływać się do nich w różne zadania przez określenie ścieżek w każdym **asaproj.json**.

## <a name="define-an-input"></a>Zdefiniuj wejściowe

1. Wybierz **Ctrl + Shift + P** aby otworzyć paletę poleceń, a następnie wprowadź **ASA: Dodaj dane wejściowe**.

   ![Dodaj dane wejściowe usługi Stream Analytics w programie VS Code](./media/quick-create-vs-code/add-input.png)

2. Wybierz **usługi IoT Hub** dla typu danych wejściowych.

   ![Wybierz Centrum IoT Hub jako wprowadzanie opcji](./media/quick-create-vs-code/iot-hub.png)

3. Wybierz skrypt zapytania ASA, który będzie używany w danych wejściowych. Automatycznie należy wypełnić przy użyciu ścieżki pliku do **myASAproj.asaql**.

   ![Wybierz skrypt ASA w programie Visual Studio Code](./media/quick-create-vs-code/asa-script.png)

4. Wprowadź nazwę pliku wejściowego jako **IotHub.json**.

5. Edytuj **IoTHub.json** z następującymi wartościami. Zachowaj wartości domyślne dla pól, które nie są wymienione poniżej. Użyj funkcji CodeLens ułatwiających wprowadza się ciąg lub wybierz z listy rozwijanej.

   |Ustawienie|Sugerowana wartość|Opis|
   |-------|---------------|-----------|
   |Name (Nazwa)|Dane wejściowe|Wprowadź nazwę identyfikującą dane wejściowe zadania.|
   |IotHubNamespace|MyASAIoTHub|Wybierz lub wprowadź nazwę centrum IoT Hub. Nazwy centrów IoT Hub są wykrywane automatycznie, jeśli są one tworzone w tej samej subskrypcji.|
   |Punkt końcowy|Obsługa komunikatów| |
   |SharedAccessPolicyName|iothubowner| |

## <a name="define-an-output"></a>Zdefiniuj dane wyjściowe

1. Wybierz **Ctrl + Shift + P** aby otworzyć paletę poleceń. Następnie wprowadź **ASA: Dodaj dane wyjściowe**.

   ![Dodaj dane wyjściowe usługi Stream Analytics w programie VS Code](./media/quick-create-vs-code/add-output.png)

2. Wybierz **magazynu obiektów Blob** dla typ ujścia.

3. Wybierz skrypt zapytania ASA, która będzie używać tych danych wejściowych.

4. Wprowadź nazwę pliku wyjściowego jako **BlobStorage.json**.

5. Edytuj **BlobStorage.json** z następującymi wartościami. Zachowaj wartości domyślne dla pól, które nie są wymienione poniżej. Użyj funkcji CodeLens ułatwiających wprowadza się ciąg lub wybierz z listy rozwijanej.

   |Ustawienie|Sugerowana wartość|Opis|
   |-------|---------------|-----------|
   |Name (Nazwa)|Dane wyjściowe| Wprowadź nazwę identyfikującą dane wyjściowe zadania.|
   |Konto magazynu|asaquickstartstorage|Wybierz lub wprowadź nazwę konta magazynu. Nazwy kont magazynu są wykrywane automatycznie, jeśli są one tworzone w tej samej subskrypcji.|
   |Kontener|container1|Wybierz istniejący kontener utworzony na koncie magazynu.|
   |Wzorzec ścieżki|output|Wprowadź nazwę ścieżki pliku, który ma zostać utworzony w kontenerze.|

## <a name="define-the-transformation-query"></a>Definiowanie zapytania przekształcenia

1. Otwórz **myASAproj.asaql** w folderze projektu.

2. Dodaj następujące zapytanie:

   ```sql
   SELECT * 
   INTO Output
   FROM Input
   HAVING Temperature > 27
   ```

## <a name="compile-script"></a>Wykonanie skryptu kompilacji

Skrypt kompilacji wykonuje dwie czynności: Sprawdź składnię i generowanie szablonów usługi Azure Resource Manager do automatycznego wdrożenia.

Istnieją dwa sposoby, aby wyzwolić skryptu kompilacji:

1. Wybierz odpowiedni skrypt z obszaru roboczego, a następnie wyzwoli kompilacji z palety poleceń. 

   ![Użyj paletę poleceń programu VS Code, aby kompilować skrypt](./media/quick-create-vs-code/compile-script1.png)

2. Kliknij prawym przyciskiem myszy skrypt i wybierz pozycję **ASA: wykonanie skryptu kompilacji**.

    ![Kliknij prawym przyciskiem myszy skrypt ASA, aby skompilować](./media/quick-create-vs-code/compile-script2.png)

3. Po kompilacji, można znaleźć dwa szablony usługi Azure Resource Manager wygenerowanego w **Wdróż** folderze projektu. Te dwa pliki są używane do automatycznego wdrożenia.

## <a name="submit-a-stream-analytics-job-to-azure"></a>Przesyłanie zadania usługi Stream Analytics na platformie Azure

1. W oknie edytora skryptów programu Visual Studio Code wybierz **wybierz z subskrypcji**.

   ![Wybierz z subskrypcji tekstu w Edytorze skryptów](./media/quick-create-vs-code/select-subscription.png)

2. Wybierz swoją subskrypcję z listy menu podręczne.

3. Wybierz **wybierz zadanie**. Następnie wybierz polecenie Utwórz nowe zadanie.

4. Wprowadź nazwę zadania **myASAjob** i postępuj zgodnie z instrukcjami, aby wybrać grupę zasobów i lokalizacji.

5. Wybierz **przesłać na platformę Azure**. Dzienniki znajdują się w oknie danych wyjściowych. 

6. Po utworzeniu zadania mogli je zobaczyć, w Eksploratorze Stream Analytics.

## <a name="run-the-iot-simulator"></a>Uruchamianie symulatora IoT

1. Otwórz symulator [Raspberry Pi Azure IoT Online Simulator](https://azure-samples.github.io/raspberry-pi-web-simulator/) w nowej karcie lub nowym oknie przeglądarki.

2. Zamień symbol zastępczy w wierszu 15 na parametry połączenia urządzenia usługi Azure IoT Hub, które zostały zapisane w poprzedniej sekcji.

3. Kliknij pozycję **Run** (Uruchom). Dane wyjściowe powinny pokazywać dane z czujników i komunikaty, które są wysyłane do usługi IoT Hub.

   ![Symulator Raspberry Pi Azure IoT Online Simulator](./media/quick-create-vs-code/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>Uruchamianie zadania usługi Stream Analytics i sprawdzanie danych wyjściowych

1. Otwórz Stream Analytics Eksplorator programu Visual Studio Code i Znajdź zadanie,**myASAJob**.

2. Kliknij prawym przyciskiem myszy nazwę zadania. Następnie wybierz **Start** z menu kontekstowego.

3. Wybierz **teraz** w oknie podręcznym, aby uruchomić zadanie.

4. Należy pamiętać, stan zadania został zmieniony na **systemem**. Kliknij prawym przyciskiem myszy nazwę zadania, a następnie wybierz **Otwórz widok zadania w portalu** aby zobaczyć dane wejściowe i wyjściowe metryki zdarzenia. Może to potrwać kilka minut.

5. Aby wyświetlić wyniki, należy otworzyć magazynu obiektów blob w rozszerzeniu Visual Studio Code lub w witrynie Azure portal.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, zadanie przesyłania strumieniowego i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Usunięcie zadania pozwala uniknąć opłat za jednostki przesyłania strumieniowego zużywane przez zadanie. Jeśli planujesz użyć zadania w przyszłości, możesz je zatrzymać i uruchomić ponownie później, gdy będzie potrzebne. Jeśli nie zamierzasz w przyszłości korzystać z tego zadania, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki start:

1. W menu znajdującym się po lewej stronie w witrynie Azure Portal wybierz pozycję **Grupy zasobów**, a następnie wybierz nazwę utworzonego zasobu.  

2. Na stronie grupy zasobów wybierz pozycję **Usuń**, wpisz w polu tekstowym nazwę zasobu do usunięcia, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start wdrożono proste zadanie usługi Stream Analytics, za pomocą programu Visual Studio Code. Można także wdrożyć zadania usługi Stream Analytics przy użyciu [witryny Azure portal](stream-analytics-quick-create-portal.md), [PowerShell](stream-analytics-quick-create-powershell.md)i Visual Studio (strumienia analytics szybki tworzenie — vs.md). 

Aby dowiedzieć się więcej na temat narzędzi Azure Stream Analytics Tools for Visual Studio, przejdź do następującego artykułu:

> [!div class="nextstepaction"]
> [Use Visual Studio to view Azure Stream Analytics jobs (Wyświetlanie zadań usługi Azure Stream Analytics za pomocą programu Visual Studio)](stream-analytics-vs-tools.md)