---
title: 'Samouczek: Generowanie symulowanych danych urządzenia — uczenie maszynowe w usłudze Azure IoT Edge'
description: Tworzenie urządzeń wirtualnych, które generują symulowane dane telemetryczne, które mogą być później używane do uczenia modelu uczenia maszynowego.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/20/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8f7a971315183e867ae06b58801d5855f90462a1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76722386"
---
# <a name="tutorial-generate-simulated-device-data"></a>Samouczek: Generowanie symulowanych danych urządzenia

> [!NOTE]
> Ten artykuł jest częścią serii samouczka na temat korzystania z usługi Azure Machine Learning w usłudze IoT Edge. Jeśli dotarłeś do tego artykułu bezpośrednio, zachęcamy do rozpoczęcia [pierwszego artykułu](tutorial-machine-learning-edge-01-intro.md) z serii, aby uzyskać najlepsze wyniki.

W tym artykule używamy danych szkoleniowych uczenia maszynowego do symulowania danych telemetrycznych wysyłania urządzenia do usługi Azure IoT Hub. Jak podano we wstępie, w tym samouczku użyto [zestawu danych symulacji degradacji silnika Turbofan](https://c3.nasa.gov/dashlink/resources/139/) do symulacji danych z zestawu silników lotniczych do szkolenia i testowania.

W naszym eksperymentalnym scenariuszu wiemy, że:

* Dane składają się z wielu wielozmiennych szeregów czasowych.
* Każdy zestaw danych jest podzielony na podzbiory szkoleniowe i testowe.
* Każda seria czasowa pochodzi z innego silnika.
* Każdy silnik zaczyna się od różnych stopni początkowego zużycia i zmienności produkcji.

W tym samouczku używamy podzbioru danych szkoleniowych pojedynczego zestawu danych (FD003).

W rzeczywistości każdy silnik będzie niezależnym urządzeniem IoT. Zakładając, że nie masz dostępnej kolekcji podłączonych do Internetu silników turbowentylatorowych, zbudujemy oprogramowanie stand-in dla tych urządzeń.

Symulator jest programem języka C#, który używa interfejsów API usługi IoT Hub do programowego rejestrowania urządzeń wirtualnych w centrum IoT Hub. Następnie odczytujemy dane dla każdego urządzenia z podzbioru danych dostarczonego przez NASA i wysyłamy je do centrum IoT hub przy użyciu symulowanego urządzenia IoT. Cały kod dla tej części samouczka można znaleźć w katalogu DeviceHarness repozytorium.

DeviceHarness projektu jest .NET core projektu napisanego w języku C# składający się z czterech klas:

* **Program:** Punkt wejścia do wykonania odpowiedzialny za obsługę danych wejściowych użytkownika i ogólnej koordynacji.
* **TrainingFileManager:** Odpowiedzialny za odczyt i analizowanie wybranego pliku danych.
* **CycleData:** Reprezentuje pojedynczy wiersz danych w pliku przekonwertowanym na format wiadomości.
* **TurbofanDeiwa:** Odpowiedzialny za tworzenie urządzenia IoT, który odpowiada pojedynczemu urządzeniu (szeregom czasowym) w danych i przesyłaniu danych do centrum IoT Hub.

Zadania opisane w tym artykule powinny potrwać około 20 minut.

Rzeczywistych odpowiednik pracy w tym kroku prawdopodobnie będą wykonywane przez deweloperów urządzeń i deweloperów chmury.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Konfigurowanie kodu programu Visual Studio i tworzenie projektu DeviceHarness

1. Otwórz sesję pulpitu zdalnego na dewelopera maszyny Wirtualnej.

1. W programie Visual Studio `C:\source\IoTEdgeAndMlSample\DeviceHarness` kod otwórz folder.

1. Ponieważ używasz rozszerzeń na tym komputerze po raz pierwszy, niektóre rozszerzenia zaktualizują i zainstalują swoje zależności. Może zostać wyświetlony monit o aktualizację rozszerzenia. Jeśli tak, wybierz opcję **Załaduj ponownie okno**.

   Jeśli błędy OmniSharp pojawiają się w oknie danych wyjściowych, należy odinstalować rozszerzenie języka C#.

1. Zostanie wyświetlony monit o dodanie wymaganych zasobów dla DeviceHarness. Wybierz **pozycję Tak,** aby je dodać.

   * Wyświetlenie powiadomienia może potrwać kilka sekund.
   * Jeśli nie odebrano tego powiadomienia, sprawdź ikonę dzwonka w prawym dolnym rogu.

   ![Wyskakujące okienko rozszerzenia kodu programu VS](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. Wybierz **przycisk Przywróć,** aby przywrócić zależności pakietu.

   ![Monit przywracania kodu VS](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

   Jeśli nie otrzymasz tych powiadomień, zamknij kod programu Visual Studio, `C:\source\IoTEdgeAndMlSample\DeviceHarness`usuń katalogi bin i obj w programie , otwórz program Visual Studio Code i ponownie otwórz folder DeviceHarness.

1. Sprawdź, czy środowisko jest poprawnie skonfigurowane, wyzwalając kompilację, **Ctrl** + **Shift** + **B**lub **Zadanie kompilacji uruchamiania terminalu** > **Run Build Task**.

1. Zostanie wyświetlony monit o wybranie zadania kompilacji do uruchomienia. Wybierz **pozycję Zbuduj**.

1. Kompilacja uruchamia i wyprowadza komunikat o sukcesie.

   ![Tworzenie komunikatu wyjściowego, który pomyślnie](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. Można uczynić tę kompilację domyślnym zadaniem kompilacji, wybierając **opcję Konfiguruj terminal** > **domyślne zadanie kompilacji...** i wybierając **polecenie Kompilacja** z monitu.

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>Połącz się z Centrum IoT i uruchom deviceharness

Teraz, gdy mamy tworzenie projektu, połącz się z centrum IoT, aby uzyskać dostęp do ciągu połączenia i monitorować postęp generowania danych.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Logowanie się do platformy Azure w programie Visual Studio Code

1. Zaloguj się do subskrypcji platformy Azure w programie `Ctrl + Shift + P` Visual Studio Code, otwierając paletę poleceń lub **Wyświetl** > **paletę poleceń.**

1. Wyszukaj polecenie **Azure: Zaloguj się.**

   Zostanie otwarte okno przeglądarki z monitem o podanie poświadczeń. Gdy zostaniesz przekierowany do strony sukcesu, możesz zamknąć przeglądarkę.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>Łączenie się z koncentratorem IoT i pobieranie ciągu połączenia koncentratora

1. W dolnej części Eksploratora kodu programu Visual Studio wybierz ramkę **usługi Azure IoT Hub,** aby ją rozwinąć.

1. W rozwiniętej ramce kliknij **pozycję Wybierz centrum IoT Hub**.

1. Po wyświetleniu monitu wybierz subskrypcję platformy Azure, a następnie centrum IoT.

1. Kliknij **...** po prawej stronie **usługi Azure IoT Hub, aby** uzyskać więcej akcji. Wybierz **polecenie Kopiuj ciąg połączenia centrum IoT**.

   ![Kopiowanie ciągu połączenia usługi IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>Uruchamianie projektu DeviceHarness

1. Wybierz **pozycję Wyświetl** > **terminal,** aby otworzyć terminal Kodu programu Visual Studio.

   Jeśli nie widzisz monitu, naciśnij klawisz Enter.

1. Wprowadź w terminalu polecenie `dotnet run`.

1. Po wyświetleniu monitu o ciąg połączenia usługi IoT Hub wklej ciąg połączenia skopiowany w poprzedniej sekcji.

1. W ramce **urządzeń usługi Azure IoT Hub** kliknij przycisk odświeżania.

   ![Odświeżanie listy urządzeń Usługi IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. Należy zauważyć, że urządzenia są dodawane do usługi IoT Hub i że urządzenia są wyświetlane na zielono, aby wskazać, że dane są wysyłane za pośrednictwem tego urządzenia. Po urządzenia wysyłają wiadomości do centrum IoT hub, rozłączają się i pojawiają się na niebiesko.

1. Wiadomości wysyłane do koncentratora można wyświetlać, klikając prawym przyciskiem myszy na dowolnym urządzeniu i wybierając **pozycję Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**. Komunikaty będą wyświetlane w okienku danych wyjściowych w programie Visual Studio Code.

1. Zatrzymaj monitorowanie, klikając okienko danych wyjściowych **usługi Azure IoT Hub** i wybierz pozycję **Zatrzymaj wbudowany punkt końcowy zdarzenia .**

1. Niech aplikacja zostanie ukończona, co zajmuje kilka minut.

## <a name="check-iot-hub-for-activity"></a>Sprawdź, czy W Centrum IoT nie ma aktywności

Dane wysyłane przez DeviceHarness udał się do centrum IoT Hub, gdzie można zweryfikować w witrynie Azure portal.

1. Otwórz [witrynę Azure portal](https://portal.azure.com/) i przejdź do centrum IoT utworzonego dla tego samouczka.

1. Z menu lewego okienka w obszarze **Monitorowanie**wybierz pozycję **Metryki**.

1. Na stronie definicji wykresu kliknij listę rozwijaną **Metryka,** przewiń listę w dół i wybierz pozycję **Routing: dane dostarczone do magazynu**. Wykres powinien przedstawiać skok, kiedy dane zostały przekierowane do magazynu.

   ![Wykres pokazuje skok, gdy dane dostarczane do magazynu](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Sprawdzanie poprawności danych w usłudze Azure Storage

Dane, które właśnie wysłaliśmy do centrum IoT hub został przekierowany do kontenera magazynu, który utworzyliśmy w poprzednim artykule. Przyjrzyjmy się danym na naszym koncie pamięci masowej.

1. W witrynie Azure Portal przejdź do swojego konta magazynu.

1. W nawigatorze konta magazynu wybierz **Eksplorator magazynu (wersja zapoznawcza)**.

1. W Eksploratorze magazynu wybierz `devicedata`pozycję **Kontenery obiektów Blob,** a następnie opcję .

1. W okienku zawartości kliknij folder dla nazwy centrum IoT hub, a następnie rok, miesiąc, dzień i godzinę. Zostanie wyświetlonych kilka folderów reprezentujących protokoły, gdy dane zostały zapisane.

   ![Wyświetlanie folderów w magazynie obiektów blob](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. Kliknij jeden z tych folderów, aby znaleźć pliki danych oznaczone **jako 00** i **01** odpowiadające partycji.

1. Pliki są zapisywane w formacie [Avro.](https://avro.apache.org/) Kliknij dwukrotnie jeden z tych plików, aby otworzyć inną kartę przeglądarki i częściowo renderować dane. Jeśli zostanie wyświetlony monit o otwarcie pliku w programie, możesz wybrać opcję VS Code, który zostanie poprawnie renderowany.

1. Nie ma potrzeby, aby spróbować odczytać lub zinterpretować dane w tej chwili; zrobimy to w następnym artykule.

## <a name="next-steps"></a>Następne kroki

W tym artykule użyliśmy projektu .NET Core do utworzenia zestawu wirtualnych urządzeń IoT i wysyłania danych za ich pośrednictwem do naszego centrum IoT hub i do kontenera usługi Azure Storage. Ten projekt symuluje rzeczywisty scenariusz, w którym fizyczne urządzenia IoT wysyłają dane do centrum IoT Hub i dalej do wyselekcjonowanego magazynu. Dane te obejmują odczyty czujników, ustawienia operacyjne, sygnały awarii i tryby itd. Po zebraniu wystarczającej ilości danych używamy ich do szkolenia modeli, które przewidują pozostały okres użytkowania (RUL) dla urządzenia. Zademonstrujemy to uczenie maszynowe w następnym artykule.

Przejdź do następnego artykułu, aby wyszkolić model uczenia maszynowego z danymi.

> [!div class="nextstepaction"]
> [Trenowanie i wdrażanie modelu usługi Azure Machine Learning](tutorial-machine-learning-edge-04-train-model.md)
