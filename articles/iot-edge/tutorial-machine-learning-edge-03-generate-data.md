---
title: Generowanie danych symulowanego urządzenia — Machine Learning na platformie Azure IoT Edge | Dokumentacja firmy Microsoft
description: Utwórz urządzeń wirtualnych, które generują symulowanych danych telemetrycznych, które później mogą być używane do uczenia modelu uczenia maszynowego.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f3cf23f491aad1766f2d4087c440cc080dbe345e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057652"
---
# <a name="tutorial-generate-simulated-device-data"></a>Samouczek: Generowanie danych symulowanego urządzenia

> [!NOTE]
> Ten artykuł jest częścią serii, samouczek dotyczący przy użyciu usługi Azure Machine Learning w usłudze IoT Edge. Jeśli została wyświetlona bezpośrednio w tym artykule, firma Microsoft zachęca rozpoczynać się [najpierw artykuł](tutorial-machine-learning-edge-01-intro.md) z tej serii, aby uzyskać najlepsze wyniki.

W tym artykule używamy usługi machine learning szkolenia danych w celu symulowania urządzenia wysyłają dane telemetryczne do Centrum IoT Hub. Jak wspomniano we wprowadzeniu, korzysta z tego samouczka end-to-end [zestaw danych symulacji degradacji silnika Turbofan](https://c3.nasa.gov/dashlink/resources/139/) do symulacji dane z zestawu aparatów w trybie samolotowym celów szkoleniowych i testów.

Z towarzyszącym readme.txt wiemy, że:

* Dane składają się z wielu szeregach czasowych wielu zmiennych
* Każdy zestaw danych jest podzielony na szkolenie i testowanie podzestawy
* Każdy szereg czasowy jest z innego aparatu
* Każdy silnik zaczyna się od różne stopnie początkowej zużycia i produkcji odmianę

W tym samouczku używamy podzbiór danych szkoleniowych jednego zestawu danych (FD003).

W rzeczywistości każdy silnik będzie niezależnie od urządzenia IoT. Przy założeniu, że nie masz kolekcję aparatów turbofan podłączonej do Internetu, dostępne, utworzymy elementu zastępczego oprogramowania dla tych urządzeń.

Symulator C# program, który używa interfejsów API Centrum IoT można programowo zarejestrować urządzeń wirtualnych z usługą IoT Hub. Firma Microsoft następnie odczytywać dane dla każdego urządzenia z podzbioru dane dostarczane przez NASA i wysyłać je do Centrum IoT hub przy użyciu symulowanego urządzenia IoT. Cały kod dla tej części samouczka można znaleźć w katalogu DeviceHarness repozytorium.

Projekt DeviceHarness jest projektu .NET core napisane w C# składający się z czterech klasy:

* **Program:** Punkt wejścia dla wykonywania odpowiedzialny za obsługę danych wejściowych użytkownika i koordynacji ogólnej.
* **TrainingFileManager:** odpowiedzialna za odczytywanie i analizowanie pliku wybranych danych.
* **Elementu CycleData:** reprezentuje pojedynczy wiersz danych do pliku jest konwertowana na format komunikatu.
* **TurbofanDevice:** odpowiedzialne za tworzenie urządzeń IoT, która odnosi się do jednego urządzenia (szeregów czasowych) w danych i przesyłania danych do usługi IoT Hub za pośrednictwem urządzenia IoT.

Zadania opisane w tym artykule powinno zająć około 20 minut.

Odpowiednik rzeczywistych do pracy w tym kroku będzie prawdopodobnie wykonywane przez deweloperów urządzenia i deweloperów rozwiązań w chmurze.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Konfigurowanie programu Visual Studio Code i skompiluj projekt DeviceHarness

1. Otwórz sesję pulpitu zdalnego z maszyną wirtualną, jak pokazano w poprzednim artykule.

1. Otwórz program Visual Studio Code.

1. W programie Visual Studio Code wybierz **pliku** > **Otwórz Folder...** .

1. W **folderu** polu tekstowym wprowadź `C:\source\IoTEdgeAndMlSample\DeviceHarness` i kliknij przycisk **wybierz Folder** przycisku.

   Jeśli technologię OmniSharp błędy są wyświetlane w oknie danych wyjściowych, należy odinstalować C# zainstalować rozszerzenia, zamknij i otwórz ponownie program VS Code C# rozszerzenie, a następnie ponowne załadowanie okna.

1. Ponieważ używasz rozszerzenia na tym komputerze po raz pierwszy, niektóre rozszerzenia aktualizowane i zainstalowania ich zależności. Może zostać wyświetlony monit można zaktualizować rozszerzenia. Jeśli tak, zaznacz **Reload Window**.

1. Zostanie wyświetlony monit Dodaj wymagane zasoby dla DeviceHarness. Wybierz **tak** je dodać.

   * Powiadomienie może potrwać kilka sekund, są wyświetlane.
   * Jeśli zdarzyło Ci się przeoczyć tego powiadomienia, sprawdź ikonę "dzwonka" w prawym dolnym rogu.

   ![Okno podręczne rozszerzenia kodu programu VS](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. Wybierz **przywrócić** można przywrócić zależności pakietów.

   ![Wiersz przywracania kodu programu VS](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

1. Zweryfikuj, czy środowisko jest prawidłowo skonfigurowane, wyzwalając kompilacji `Ctrl + Shift + B` lub **terminalu** > **uruchomienia zadania kompilacji**.

1. Monit, aby wybrać uruchomienie zadania kompilacji. Wybierz **kompilacji**.

1. Kompilacja działa i generuje komunikat o powodzeniu.

   ![Kompilacja powiodła się komunikatu wyjściowego](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. Ułatwia to tworzenie domyślnego zadania kompilacji, wybierając **terminalu** > **skonfigurować domyślne zadania kompilacji...**  i wybierając pozycję **kompilacji** w wierszu polecenia.

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>Połącz z usługą IoT Hub i uruchom DeviceHarness

Skoro mamy już projektów, kompilowanie, łączenie do Twojego Centrum IoT, dostęp do parametrów połączenia i monitorować postęp generowania danych.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Logowanie do platformy Azure w programie Visual Studio Code

1. Zaloguj się do subskrypcji platformy Azure w programie Visual Studio Code, otwierając paletę poleceń `Ctrl + Shift + P` lub **widoku** > **paleta poleceń...** .

1. W monitu wyszukiwania dla i wybierz pozycję **platformy Azure: Zaloguj się**.

1. Okno przeglądarki otwiera i wyświetla monit o podanie poświadczeń. Gdy nastąpi przekierowanie do strony sukces, możesz zamknąć przeglądarkę.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>Łączenie do Centrum IoT i pobieranie parametrów połączenia Centrum

1. W dolnej części Eksploratora Visual Studio Code wybierz **urządzeń usługi Azure IoT Hub** ramki, aby ją rozwinąć.

1. W ramce rozwinięty, kliknij pozycję **wybierz Centrum IoT Hub**.

1. Po wyświetleniu monitu wybierz swoją subskrypcję platformy Azure, a następnie Centrum IoT hub.

1. Klikaj **urządzeń usługi Azure IoT Hub** ramki, a następnie kliknij przycisk **...**  Aby uzyskać więcej akcji. Wybierz **parametry połączenia Centrum IoT kopiowania**.

   ![Skopiuj parametry połączenia Centrum IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>Uruchom projekt DeviceHarness

1. Wybierz **widoku** > **terminalu** aby otworzyć terminal programu Visual Studio Code.

   Jeśli nie widzisz monit, naciśnij klawisz Enter.

1. Wprowadź `dotnet run` w terminalu.

1. Po wyświetleniu monitu, aby uzyskać parametry połączenia Centrum IoT, Wklej parametry połączenia skopiowane z poprzedniej sekcji.

1. W **urządzeń usługi Azure IoT Hub** ramki, kliknij przycisk Odśwież.

   ![Odśwież listę urządzeń usługi IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. Należy pamiętać, że urządzenia są dodawane do usługi IoT Hub i że urządzenia wyświetlane w kolorze zielonym aby wskazać, że dane są wysyłane za pośrednictwem tego urządzenia.

1. Można wyświetlić komunikaty są wysyłane do Centrum, klikając prawym przyciskiem myszy na dowolnym urządzeniu i wybierając **Rozpocznij monitorowania komunikatu D2C**. Komunikaty pojawią się w okienku danych wyjściowych programu Visual Studio Code.

1. Zatrzymaj monitorowanie, klikając w **Azure IoT Hub Toolkit** dane wyjściowe w okienku, a następnie wybierz **Zatrzymaj monitorowanie komunikatu D2C**.

1. Zezwalaj aplikacji na zostało ukończone, która zajmuje kilka minut.

## <a name="check-iot-hub-for-activity"></a>Sprawdź usługi IoT Hub dla działania

Dane wysyłane przez DeviceHarness zmieniał się do usługi IoT hub. To proste sprawdzić, czy dane osiągną Twojego Centrum przy użyciu witryny Azure portal.

1. Otwórz [witryny Azure portal](https://portal.azure.com/) i przejdź do Centrum IoT hub.

1. Na stronie Przegląd powinny pojawić się, że dane zostały wysłane do Centrum:  

   ![Wyświetlanie urządzenia komunikatów z chmury w usłudze IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Sprawdzanie poprawności danych w usłudze Azure Storage

Dane, które właśnie wysłaliśmy do Twojego Centrum IoT był kierowany do kontenera magazynu, które zostały utworzone w poprzednim artykule. Przyjrzyjmy się dane w naszej konta magazynu.

1. W witrynie Azure Portal przejdź do swojego konta magazynu.

1. W Nawigatorze konta magazynu, wybierz **Eksploratora usługi Storage (wersja zapoznawcza)** .

1. W programie storage explorer wybierz **kontenery obiektów Blob** następnie **devicedata**.

1. W okienku zawartości kliknij w folderze nazwę Centrum IoT hub, a następnie rok, miesiąc, dzień i godzinę. Pojawi się kilka folderów reprezentujący minut, jeśli dane zostały zapisane.

   ![Wyświetl foldery w magazynie obiektów blob](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. Kliknij na jeden z tych folderów, aby znaleźć pliki danych z oznaczeniem **00** i **01** odpowiadający partycji.

1. Pliki są zapisywane [Avro](http://avro.apache.org/) formatowanie, ale dwukrotne kliknięcie jednego z tych plików spowoduje to otwarcie innej karcie przeglądarki i częściowo przedstawienia tych danych. Jeśli zamiast tego zostanie wyświetlony monit, aby otworzyć go w programie, można wybrać program VS Code i będą prawidłowo renderowane.

1. Nie ma potrzeby próbował odczytać lub interpretowania danych w tej chwili; Zrobimy to w kolejnym artykule.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule użyliśmy projektu .NET Core, aby utworzyć zbiór urządzeń wirtualnych i przesyłania danych za pomocą tych urządzeń, za pośrednictwem naszej usługi IoT Hub oraz w kontenerze usługi Azure Storage. Ten projekt symuluje rzeczywistych scenariuszy, w którym urządzeń fizycznych wysyłają dane w tym odczyty czujników, ustawienia operacyjne, sygnałów awarii i tryby, i tak dalej do usługi IoT Hub i wartości do wyselekcjonowany składowania. Po zebraniu wystarczającej ilości danych używamy go do uczenia modeli, które przewidywania pozostałego okresu eksploatacji (RUL) dla urządzenia, które zostaną przedstawione w następnym artykule.

Przejdź do następnego artykułu, aby uczyć model z danymi uczenia maszynowego.

> [!div class="nextstepaction"]
> [Nauczanie i wdrożyć model uczenia maszynowego Azure](tutorial-machine-learning-edge-04-train-model.md)
