---
title: 'Samouczek: generowanie danych symulowanego urządzenia — Machine Learning na Azure IoT Edge'
description: W tym samouczku utworzysz urządzenia wirtualne generujące symulowane dane telemetryczne, które mogą być później używane do uczenia modelu uczenia maszynowego.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1c56dd23084feabeb72f084b03e055d4aa09a11d
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75912265"
---
# <a name="tutorial-generate-simulated-device-data"></a>Samouczek: generowanie danych symulowanego urządzenia

> [!NOTE]
> Ten artykuł jest częścią serii samouczka dotyczącego używania Azure Machine Learning w IoT Edge. Jeśli ten artykuł został osiągnięty bezpośrednio, zachęcamy do rozpoczęcia od [pierwszego artykułu](tutorial-machine-learning-edge-01-intro.md) z serii w celu uzyskania najlepszych wyników.

W tym artykule wykorzystamy dane szkoleniowe dotyczące uczenia maszynowego, aby symulować wysyłanie danych telemetrycznych do IoT Hub. Jak zostało to opisane we wprowadzeniu, ten kompleksowy samouczek używa [zestawu danych symulacji degradacji aparatu TurboFan](https://c3.nasa.gov/dashlink/resources/139/) w celu symulowania danych z zestawu aparatów samolotowych na potrzeby szkoleń i testowania.

Z towarzyszącego pliku Readme. txt wiemy, że:

* Dane składają się z wielu wieloczynnikowa szeregów czasowych
* Każdy zestaw danych jest podzielony na podzbiory szkoleń i testów
* Każda seria czasu pochodzi z innego aparatu
* Każdy silnik rozpoczyna się od różnych stopni początkowej zmiany zużycia i produkcji

W tym samouczku jest używany podzestaw danych szkolenia jednego zestawu danych (FD003).

W rzeczywistości każdy aparat będzie niezależnym urządzeniem IoT. Przy założeniu, że nie masz dostępnych kolekcji turbofanych podłączonych do Internetu aparatów dla tych urządzeń zostanie utworzona.

Symulator to C# program, który używa IoT Hub interfejsów API do programistycznego rejestrowania urządzeń wirtualnych z IoT Hub. Następnie odczytujemy dane dla każdego urządzenia z podzestawu danych NASA i wysyłamy je do usługi IoT Hub przy użyciu symulowanego urządzenia IoT. Cały kod tego fragmentu samouczka można znaleźć w katalogu DeviceHarness repozytorium.

Projekt DeviceHarness to projekt platformy .NET Core C# składający się z czterech klas:

* **Program:** Punkt wejścia do wykonania odpowiedzialny za obsługę danych wejściowych i ogólnej koordynacji użytkownika.
* **TrainingFileManager:** odpowiedzialny za odczytywanie i analizowanie wybranego pliku danych.
* **Danymi cycledata:** reprezentuje pojedynczy wiersz danych w pliku przekonwertowanym na format wiadomości.
* **TurbofanDevice:** odpowiedzialny za tworzenie urządzenia IoT, które odpowiada jednemu urządzeniu (szeregowi czasowym) w danych i przesyłaniu danych do IoT Hub za pośrednictwem urządzenia IoT.

Zadania opisane w tym artykule powinny zająć około 20 minut.

Rzeczywisty, podobny do pracy w tym kroku, będzie prawdopodobnie wykonywany przez deweloperów urządzeń i deweloperów rozwiązań w chmurze.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Konfigurowanie Visual Studio Code i kompilowanie projektu DeviceHarness

1. Otwórz sesję pulpitu zdalnego z maszyną wirtualną, jak pokazano w poprzednim artykule.

1. Otwórz program Visual Studio Code.

1. W Visual Studio Code wybierz pozycję **plik** > **Otwórz folder...** .

1. W polu tekstowym **folder** wprowadź `C:\source\IoTEdgeAndMlSample\DeviceHarness` i kliknij przycisk **Wybierz folder** .

   Jeśli błędy OmniSharp są wyświetlane w oknie dane wyjściowe, należy odinstalować C# rozszerzenie, zamknąć i ponownie otworzyć vs Code, zainstalować C# rozszerzenie, a następnie załadować ponownie okno.

1. Ponieważ używasz rozszerzeń na tym komputerze po raz pierwszy, Niektóre rozszerzenia będą aktualizować i instalować ich zależności. Może zostać wyświetlony monit o zaktualizowanie rozszerzenia. Jeśli tak, wybierz pozycję **Załaduj ponownie okno**.

1. Zostanie wyświetlony monit o dodanie wymaganych zasobów dla DeviceHarness. Wybierz pozycję **tak** , aby je dodać.

   * Wyświetlenie powiadomienia może potrwać kilka sekund.
   * Jeśli to powiadomienie zostało pominięte, sprawdź ikonę "dzwonka" w prawym dolnym rogu.

   ![Okno podręczne rozszerzenia VS Code](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. Wybierz pozycję **Przywróć** , aby przywrócić zależności pakietu.

   ![Monit VS Code przywrócenia](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

1. Sprawdź, czy środowisko jest prawidłowo skonfigurowane, wyzwalając zadanie kompilacji, `Ctrl + Shift + B` lub **terminalu** > **Uruchom kompilację**.

1. Zostanie wyświetlony monit o wybranie zadania kompilacji do uruchomienia. Wybierz pozycję **kompilacja**.

1. Kompilacja zostanie uruchomiona i wygeneruje komunikat o powodzeniu.

   ![Pomyślnie zakończono Tworzenie komunikatu wyjściowego](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. Możesz wprowadzić tę kompilację jako domyślne zadanie kompilacji, wybierając pozycję **Terminal** > **Skonfiguruj domyślne zadanie kompilacji...** i wybierając opcję **Kompiluj** z poziomu wiersza polecenia.

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>Nawiązywanie połączenia z usługą IoT Hub i uruchamianie DeviceHarness

Teraz, gdy mamy już kompilację projektu, Połącz się z Centrum IoT Hub, aby uzyskać dostęp do parametrów połączenia i monitorować postęp generowania danych.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Zaloguj się do platformy Azure w Visual Studio Code

1. Zaloguj się do subskrypcji platformy Azure w Visual Studio Code, otwierając paletę poleceń, `Ctrl + Shift + P` lub Wyświetl **paletę poleceń > ..** .

1. W wierszu polecenia Wyszukaj i wybierz pozycję **Azure: Zaloguj się**.

1. Zostanie otwarte okno przeglądarki i zostanie wyświetlony komunikat z prośbą o Twoje poświadczenia. Po przekierowaniu do strony sukcesu można zamknąć przeglądarkę.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>Nawiązywanie połączenia z Centrum IoT Hub i pobieranie parametrów połączenia centrum

1. W dolnej części Eksploratora Visual Studio Code Wybierz ramkę **urządzenia IoT Hub platformy Azure** , aby ją rozwinąć.

1. W rozwiniętej ramce kliknij pozycję **wybierz IoT Hub**.

1. Po wyświetleniu monitu wybierz subskrypcję platformy Azure, a następnie Centrum IoT Hub.

1. Kliknij ramkę **urządzeń IoT Hub platformy Azure** i kliknij pozycję **...** , aby uzyskać więcej akcji. Wybierz pozycję **kopiuj IoT Hub parametry połączenia**.

   ![Kopiuj parametry połączenia IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>Uruchamianie projektu DeviceHarness

1. Wybierz pozycję **wyświetl** > **Terminal** , aby otworzyć Visual Studio Code Terminal.

   Jeśli nie widzisz monitu, wybierz klawisz ENTER.

1. Wprowadź w terminalu polecenie `dotnet run`.

1. Po wyświetleniu monitu o parametry połączenia IoT Hub wklej parametry połączenia skopiowane w poprzedniej sekcji.

1. W ramce **usługi Azure IoT Hub Devices** kliknij przycisk Odśwież.

   ![Odświeżanie listy urządzeń IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. Należy zauważyć, że urządzenia są dodawane do IoT Hub i że urządzenia są wyświetlane w kolorze zielonym, aby wskazać, że dane są wysyłane za pośrednictwem tego urządzenia.

1. Komunikaty wysyłane do centrum można wyświetlić, klikając prawym przyciskiem myszy dowolne urządzenie i wybierając pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**. Komunikaty będą wyświetlane w okienku danych wyjściowych w Visual Studio Code.

1. Zatrzymaj monitorowanie, klikając w okienku danych wyjściowych **IoT Hub Azure** , a następnie wybierz pozycję **Zatrzymaj monitorowanie wbudowanego punktu końcowego zdarzenia**.

1. Pozwól, aby aplikacja działała, co potrwa kilka minut.

## <a name="check-iot-hub-for-activity"></a>Sprawdź działanie IoT Hub

Dane wysłane przez DeviceHarness zostały przekazane do centrum IoT Hub. Można łatwo sprawdzić, czy dane dotarły do centrum przy użyciu Azure Portal.

1. Otwórz [Azure Portal](https://portal.azure.com/) i przejdź do centrum IoT Hub.

1. Na stronie Przegląd należy zobaczyć, że dane zostały wysłane do centrum:  

   ![Wyświetlanie komunikatów z urządzenia w chmurze w IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Weryfikowanie danych w usłudze Azure Storage

Dane, które właśnie wysłaliśmy do centrum IoT Hub, zostały przekazane do kontenera magazynu utworzonego w poprzednim artykule. Przyjrzyjmy się danych na naszym koncie magazynu.

1. W witrynie Azure Portal przejdź do swojego konta magazynu.

1. W oknie Nawigator konta magazynu wybierz pozycję **Eksplorator usługi Storage (wersja zapoznawcza)** .

1. W Eksploratorze magazynu wybierz pozycję **kontenery obiektów BLOB** , a następnie **devicedata**.

1. W okienku Zawartość kliknij folder, aby uzyskać nazwę Centrum IoT, rok, miesiąc, dzień i godzinę. Po zapisaniu danych zobaczysz kilka folderów reprezentujących minuty.

   ![Wyświetlanie folderów w usłudze BLOB Storage](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. Kliknij jeden z tych folderów, aby znaleźć pliki danych z etykietami **00** i **01** odpowiadające partycji.

1. Pliki są zapisywane w formacie [Avro](https://avro.apache.org/) , ale dwukrotne kliknięcie jednego z tych plików spowoduje otwarcie innej karty przeglądarki i częściowe renderowanie danych. Jeśli zamiast tego zostanie wyświetlony monit o otwarcie pliku w programie, możesz wybrać VS Code i będzie on prawidłowo renderowany.

1. Nie ma potrzeby próby odczytu lub interpretacji danych teraz; zajmiemy się tym w następnym artykule.

## <a name="next-steps"></a>Następne kroki

W tym artykule użyto projektu .NET Core do utworzenia zestawu urządzeń wirtualnych i wysłania danych za pomocą tych urządzeń za pomocą naszego IoT Hub i kontenera usługi Azure Storage. Ten projekt symuluje rzeczywistą sytuację, w której urządzenia fizyczne wysyłają dane, w tym odczyty czujników, Ustawienia operacyjne, sygnały awaryjne i tryby, i tak dalej, do IoT Hub i z lewej do magazynu nadzorowanego. Po zebraniu wystarczającej ilości danych używamy go do uczenia modeli, które przewidywalną pozostały okres eksploatacji (pozostałego czasu eksploatacji) dla urządzenia, który zostanie zaprezentowany w następnym artykule.

Przejdź do następnego artykułu, aby przeszkolić model uczenia maszynowego z danymi.

> [!div class="nextstepaction"]
> [Uczenie i wdrażanie modelu Azure Machine Learning](tutorial-machine-learning-edge-04-train-model.md)
