---
title: Tworzenie Azure Stream Analytics zadania w chmurze w Visual Studio Code (wersja zapoznawcza)
description: W tym przewodniku szybki start pokazano, jak rozpocząć pracę przez utworzenie zadania Stream Analytics, skonfigurowanie danych wejściowych, wyjściowych i Definiowanie zapytania przy użyciu Visual Studio Code.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 09/16/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 3301be3a067982cb90e663fe3782319eb0b90ba0
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673130"
---
# <a name="quickstart-create-an-azure-stream-analytics-cloud-job-in-visual-studio-code-preview"></a>Szybki start: Tworzenie Azure Stream Analytics zadania w chmurze w Visual Studio Code (wersja zapoznawcza)

W tym przewodniku szybki start pokazano, jak utworzyć i uruchomić zadanie Stream Analytics przy użyciu rozszerzenia Azure Stream Analytics dla Visual Studio Code. Przykładowe zadanie odczytuje dane przesyłane strumieniowo z urządzenia usługi IoT Hub. Zdefiniujesz zadanie obliczające średnią temperaturę, gdy wynosi ona ponad 27°, i zapisujące wynikowe zdarzenia wyjściowe w magazynie obiektów blob.

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

* Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

* Zainstaluj narzędzie [Visual Studio Code](https://code.visualstudio.com/).

## <a name="install-the-azure-stream-analytics-extension"></a>Zainstaluj rozszerzenie Azure Stream Analytics

1. Otwórz program Visual Studio Code.

2. W obszarze **rozszerzenia** w lewym okienku wyszukaj pozycję **Stream Analytics** i wybierz pozycję **Zainstaluj** na rozszerzeniu **Azure Stream Analytics** .

3. Po zainstalowaniu rozszerzenia Sprawdź, czy w **włączonych rozszerzeniach**są widoczne **narzędzia Azure Stream Analytics** .

   ![Narzędzia Azure Stream Analytics w obszarze włączone rozszerzenia w programie Visual Studio Code](./media/quick-create-vs-code/enabled-extensions.png)

## <a name="activate-the-azure-stream-analytics-extension"></a>Aktywuj rozszerzenie Azure Stream Analytics

1. Wybierz ikonę **platformy Azure** na pasku działania vs Code. **Stream Analytics** będą widoczne na pasku bocznym. W obszarze **Stream Analytics**wybierz pozycję **Zaloguj się do platformy Azure**. 

   ![Zaloguj się do platformy Azure w Visual Studio Code](./media/quick-create-vs-code/azure-sign-in.png)

2. Po zalogowaniu nazwa konta platformy Azure zostanie wyświetlona na pasku stanu w lewym dolnym rogu okna VS Code.

> [!NOTE]
> Azure Stream Analytics narzędzia zostaną automatycznie zalogowane przy następnym wylogowaniu. Jeśli Twoje konto ma uwierzytelnianie dwuskładnikowe, zaleca się używanie uwierzytelniania przy użyciu telefonu zamiast numeru PIN.
> Jeśli masz problemy z wyświetlaniem zasobów, wylogowanie się i ponowne zalogowanie jest zwykle pomocne. Aby się wylogować, wprowadź polecenie `Azure: Sign Out`.

## <a name="prepare-the-input-data"></a>Przygotowywanie danych wejściowych

Przed zdefiniowaniem zadania usługi Stream Analytics przygotuj dane, które następnie zostaną skonfigurowane jako dane wejściowe zadania. Aby przygotować dane wejściowe wymagane przez zadanie, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Wybierz pozycję **Utwórz zasób** > **Internet rzeczy** > **IoT Hub**.

3. W okienku **IoT Hub** wprowadź następujące informacje:
   
   |**Ustawienie**  |**Sugerowana wartość**  |**Opis**  |
   |---------|---------|---------|
   |Subscription  | \<Twoja subskrypcja\> |  Wybierz subskrypcję platformy Azure, której chcesz użyć. |
   |Resource group   |   asaquickstart-resourcegroup  |   Wybierz pozycję **Utwórz nową** i wprowadź nazwę nowej grupy zasobów dla swojego konta. |
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

   ![Utwórz kontener obiektu blob](./media/quick-create-vs-code/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Tworzenie projektu usługi Stream Analytics

1. W Visual Studio Code naciśnij **kombinację klawiszy Ctrl + Shift + P** , aby otworzyć paletę poleceń. Następnie wpisz **ASA** i wybierz **ASA: Utwórz nowy projekt @ no__t-0.

   ![Utwórz nowy projekt](./media/quick-create-vs-code/create-new-project.png)

2. Wprowadź nazwę projektu, na przykład **myASAproj** , i wybierz folder dla projektu.

    ![Utwórz nazwę projektu](./media/quick-create-vs-code/create-project-name.png)

3. Nowy projekt zostanie dodany do obszaru roboczego. Projekt ASA składa się ze skryptu zapytania **(*. asaql)** , pliku **JobConfig. JSON** i pliku konfiguracji **asaproj. JSON** .

   ![Stream Analytics plików projektu w VS Code](./media/quick-create-vs-code/asa-project-files.png)

4. Plik konfiguracji **asaproj. JSON** zawiera informacje dotyczące danych wejściowych, wyjściowych i pliku konfiguracji zadania, które są konieczne do przesłania zadania Stream Analytics do platformy Azure.

   ![Plik konfiguracji zadania Stream Analytics w VS Code](./media/quick-create-vs-code/job-configuration.png)

> [!Note]
> Po dodaniu danych wejściowych i wyjściowych z palety poleceń odpowiednie ścieżki zostaną automatycznie dodane do pliku **asaproj. JSON** . Jeśli dodasz lub usuniesz dane wejściowe lub wyjściowe bezpośrednio na dysku, musisz ręcznie dodać je lub usunąć z **asaproj. JSON**. Możesz wybrać opcję umieszczania danych wejściowych i wyjściowych w jednym miejscu, a następnie odwoływać je w różnych zadaniach, określając ścieżki w każdym pliku **asaproj. JSON**.

## <a name="define-the-transformation-query"></a>Definiowanie zapytania przekształcenia

1. Otwórz **myASAproj. asaql** z folderu projektu.

2. Dodaj następujące zapytanie:

   ```sql
   SELECT * 
   INTO Output
   FROM Input
   HAVING Temperature > 27
   ```
## <a name="test-with-sample-data"></a>Testuj z użyciem danych przykładowych
Przed uruchomieniem zapytania w chmurze można przetestować zapytanie z lokalnymi danymi przykładowymi, aby zweryfikować logikę zapytania.

Postępuj zgodnie z instrukcjami w temacie [test z przykładowymi danymi](vscode-local-run.md) , aby uzyskać więcej szczegółów. 

 ![Testowanie przy użyciu przykładowych danych w VS Code](./media/quick-create-vs-code/vscode-localrun.gif)

## <a name="define-an-input"></a>Zdefiniuj wejściowe

1. Wybierz **kombinację klawiszy Ctrl + Shift + P** , aby otworzyć paletę poleceń i wprowadź **ASA: Dodaj parametr Input @ no__t-0.

   ![Dodawanie Stream Analytics danych wejściowych w VS Code](./media/quick-create-vs-code/add-input.png)

2. Wybierz **IoT Hub** dla typu danych wejściowych.

   ![Wybierz IoT Hub jako opcję wejściową](./media/quick-create-vs-code/iot-hub.png)

3. Wybierz skrypt zapytania ASA, który będzie używać danych wejściowych. Powinien być automatycznie wypełniany ścieżką pliku **myASAproj. asaql**.

   ![Wybierz skrypt ASA w Visual Studio Code](./media/quick-create-vs-code/asa-script.png)

4. Wprowadź nazwę pliku wejściowego jako **IotHub**.

5. Edytuj plik **IoTHub. JSON** przy użyciu następujących wartości. Zachowaj wartości domyślne dla pól, które nie są wymienione poniżej. Możesz użyć CodeLens, aby pomóc wprowadzić ciąg, wybrać opcję z listy rozwijanej lub zmienić tekst bezpośrednio w pliku.

   |Ustawienie|Sugerowana wartość|Opis|
   |-------|---------------|-----------|
   |Name|Dane wejściowe|Wprowadź nazwę identyfikującą dane wejściowe zadania.|
   |IotHubNamespace|MyASAIoTHub|Wybierz lub wprowadź nazwę centrum IoT Hub. Nazwy centrów IoT Hub są wykrywane automatycznie, jeśli są one tworzone w tej samej subskrypcji.|
   |SharedAccessPolicyName|iothubowner| |

   ![Konfigurowanie danych wejściowych w Visual Studio Code](./media/quick-create-vs-code/configure-input.png)



## <a name="define-an-output"></a>Zdefiniuj dane wyjściowe

1. Wybierz **kombinację klawiszy Ctrl + Shift + P** , aby otworzyć paletę poleceń. Następnie wprowadź **ASA: Dodaj dane wyjściowe @ no__t-0.

   ![Dodaj dane wyjściowe Stream Analytics w VS Code](./media/quick-create-vs-code/add-output.png)

2. Wybierz **BLOB Storage** dla typu ujścia.

3. Wybierz skrypt zapytania ASA, który będzie używać tych danych wejściowych.

4. Wprowadź nazwę pliku wyjściowego jako **BlobStorage**.

5. Edytuj **BlobStorage** z następującymi wartościami. Zachowaj wartości domyślne dla pól, które nie są wymienione poniżej. Użyj CodeLens, aby pomóc wybrać z listy rozwijanej lub wprowadzić ciąg. 

   |Ustawienie|Sugerowana wartość|Opis|
   |-------|---------------|-----------|
   |Name|Output| Wprowadź nazwę identyfikującą dane wyjściowe zadania.|
   |Konto magazynu|asaquickstartstorage|Wybierz lub wprowadź nazwę konta magazynu. Nazwy kont magazynu są wykrywane automatycznie, jeśli są one tworzone w tej samej subskrypcji.|
   |Kontener|container1|Wybierz istniejący kontener utworzony na koncie magazynu.|
   |Wzorzec ścieżki|wyjście|Wprowadź nazwę ścieżki pliku, który ma zostać utworzony w kontenerze.|

 ![Konfigurowanie danych wyjściowych w Visual Studio Code](./media/quick-create-vs-code/configure-output.png)

## <a name="compile-the-script"></a>Kompilowanie skryptu

Kompilacja skryptu wykonuje dwie czynności: Sprawdź składnię i Wygeneruj szablony Azure Resource Manager do wdrożenia autodeployment.

Istnieją dwa sposoby wyzwalania kompilacji skryptu:

1. Wybierz skrypt z obszaru roboczego, a następnie Wyzwól kompilację z palety poleceń. 

   ![Użyj palety poleceń VS Code, aby skompilować skrypt](./media/quick-create-vs-code/compile-script1.png)

2. Kliknij prawym przyciskiem myszy skrypt i wybierz polecenie **ASA: Kompiluj skrypt**.

    ![Kliknij prawym przyciskiem myszy skrypt ASA do skompilowania](./media/quick-create-vs-code/compile-script2.png)

3. Po skompilowaniu można znaleźć dwa wygenerowane szablony Azure Resource Manager w folderze **Deploy** projektu. Te dwa pliki są używane do wdrożenia autodeployment.

    ![Stream Analytics szablonów wdrażania w Eksploratorze plików](./media/quick-create-vs-code/deployment-templates.png)

## <a name="submit-a-stream-analytics-job-to-azure"></a>Prześlij zadanie Stream Analytics do platformy Azure

1. W oknie Edytor skryptów Visual Studio Code wybierz pozycję **Wybierz z subskrypcji**.

   ![Wybierz z tekstu subskrypcji w Edytorze skryptów](./media/quick-create-vs-code/select-subscription.png)

2. Wybierz swoją subskrypcję z listy podręcznej.

3. Wybierz zadanie * *. Następnie wybierz pozycję Utwórz nowe zadanie.

4. Wprowadź nazwę zadania, **myASAjob** , a następnie postępuj zgodnie z instrukcjami, aby wybrać grupę zasobów i lokalizację.

5. Wybierz pozycję **Prześlij do platformy Azure**. Dzienniki można znaleźć w oknie danych wyjściowych. 

6. Po utworzeniu zadania można je wyświetlić w **eksploratorze Stream Analytics**.

![Wyświetl listę zadań w Eksploratorze Stream Analytics](./media/quick-create-vs-code/list-job.png)


## <a name="run-the-iot-simulator"></a>Uruchamianie symulatora IoT

1. Otwórz symulator [Raspberry Pi Azure IoT Online Simulator](https://azure-samples.github.io/raspberry-pi-web-simulator/) w nowej karcie lub nowym oknie przeglądarki.

2. Zamień symbol zastępczy w wierszu 15 na parametry połączenia urządzenia usługi Azure IoT Hub, które zostały zapisane w poprzedniej sekcji.

3. Kliknij pozycję **Run** (Uruchom). Dane wyjściowe powinny pokazywać dane z czujników i komunikaty, które są wysyłane do usługi IoT Hub.

   ![Symulator Raspberry Pi Azure IoT Online Simulator](./media/quick-create-vs-code/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>Uruchamianie zadania usługi Stream Analytics i sprawdzanie danych wyjściowych

1. Otwórz **Stream Analytics Explorer** w Visual Studio Code i Znajdź swoje zadanie, **myASAJob**.

2. Kliknij prawym przyciskiem myszy nazwę zadania. Następnie wybierz pozycję **Rozpocznij** z menu kontekstowego.

   ![Uruchom zadanie Stream Analytics w VS Code](./media/quick-create-vs-code/start-asa-job-vs-code.png)

3. Wybierz **teraz** w oknie podręcznym, aby uruchomić zadanie.

4. Zwróć uwagę, że stan zadania został zmieniony na **uruchomiony**. Kliknij prawym przyciskiem myszy nazwę zadania i wybierz pozycję **Otwórz widok zadania w portalu** , aby wyświetlić metryki zdarzeń danych wejściowych i wyjściowych. Ta akcja może potrwać kilka minut.

5. Aby wyświetlić wyniki, otwórz magazyn obiektów BLOB w rozszerzeniu Visual Studio Code lub Azure Portal.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, zadanie przesyłania strumieniowego i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Usunięcie zadania pozwala uniknąć opłat za jednostki przesyłania strumieniowego zużywane przez zadanie. Jeśli planujesz użyć zadania w przyszłości, możesz je zatrzymać i uruchomić ponownie później, gdy będzie potrzebne. Jeśli nie zamierzasz w przyszłości korzystać z tego zadania, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki start:

1. W menu znajdującym się po lewej stronie w witrynie Azure Portal wybierz pozycję **Grupy zasobów**, a następnie wybierz nazwę utworzonego zasobu.  

2. Na stronie grupy zasobów wybierz pozycję **Usuń**, wpisz w polu tekstowym nazwę zasobu do usunięcia, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono proste zadanie Stream Analytics przy użyciu Visual Studio Code. Możesz również wdrożyć zadania Stream Analytics przy użyciu [Azure Portal](stream-analytics-quick-create-portal.md), [programu PowerShell](stream-analytics-quick-create-powershell.md)i programu Visual Studio (Stream-Analytics-Quick-Create-vs.MD). 

Aby dowiedzieć się więcej na temat narzędzi Azure Stream Analytics Tools for Visual Studio, przejdź do następującego artykułu:

> [!div class="nextstepaction"]
> [Use Visual Studio to view Azure Stream Analytics jobs (Wyświetlanie zadań usługi Azure Stream Analytics za pomocą programu Visual Studio)](stream-analytics-vs-tools.md)
