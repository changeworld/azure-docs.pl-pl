---
title: 'Samouczek: Konfigurowanie środowiska — uczenie maszynowe w usłudze Azure IoT Edge'
description: 'Samouczek: Przygotuj swoje środowisko do tworzenia i wdrażania modułów do uczenia maszynowego na urządzeniach brzegowych.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/12/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 42c776d4d6c3973e7c222c9c9adf3e5105f6c84f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79296810"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Samouczek: Konfigurowanie środowiska uczenia maszynowego w umyki IoT Edge

> [!NOTE]
> Ten artykuł jest częścią serii samouczka na temat korzystania z usługi Azure Machine Learning w usłudze IoT Edge. Jeśli dotarłeś do tego artykułu bezpośrednio, zachęcamy do rozpoczęcia [pierwszego artykułu](tutorial-machine-learning-edge-01-intro.md) z serii, aby uzyskać najlepsze wyniki.

Ten artykuł pomaga przygotować środowisko do programowania i wdrażania. Najpierw skonfiguruj maszynę programistykę ze wszystkimi narzędziami, których potrzebujesz. Następnie utwórz niezbędne zasoby w chmurze na platformie Azure.

## <a name="set-up-the-development-vm"></a>Konfigurowanie maszyny wirtualnej dewelopera

Ten krok jest zazwyczaj wykonywane przez dewelopera chmury. Niektóre z oprogramowania mogą być również pomocne dla analityka danych.

Utworzyliśmy skrypt programu PowerShell, który tworzy maszynę wirtualną platformy Azure z wieloma wymaganiami wstępnymi już skonfigurowanymi. Maszyna wirtualna, którą tworzymy, musi być w stanie obsługiwać [zagnieżdżoną wirtualizację](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization), dlatego wybraliśmy [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md) rozmiar maszyny.

Maszyna wirtualna rozwoju zostanie skonfigurowana z:

* Windows 10
* [Czekoladowy](https://chocolatey.org/)
* [Pulpit platformy Docker dla systemu Windows](https://www.docker.com/products/docker-desktop)
* [Git dla systemu Windows](https://gitforwindows.org/)
* [Menedżer poświadczeń Git dla systemu Windows](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [Zestaw SDK dla platformy .NET Core](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Kod programu Visual Studio](https://code.visualstudio.com/)
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.1.0)
* [Rozszerzenia kodu VS](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C #](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [Powershell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

Maszyna wirtualna dewelopera nie jest absolutnie konieczne — wszystkie narzędzia programistyczne mogą być uruchamiane na komputerze lokalnym. Jednak zdecydowanie zaleca się użycie maszyny Wirtualnej, aby zapewnić równe szanse.

Utworzenie i skonfigurowanie maszyny wirtualnej zajmuje około 30 minut.

1. Klonuj lub pobieraj przykładowe repozytorium [usługi Machine Learning i IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) na komputer lokalny.

1. Otwórz program PowerShell jako administrator i przejdź do katalogu **\IoTEdgeAndMlSample\DevVM** znajdującego się pod katalogiem głównym, w którym pobrano kod. Będziemy odnosić się do katalogu głównego `srcdir`dla źródła jako .

    ```powershell
    cd c:\srcdir\IoTEdgeAndMlSample\DevVM
    ```

   Katalog DevVM zawiera pliki potrzebne do utworzenia maszyny wirtualnej platformy Azure odpowiednie do ukończenia tego samouczka.

1. Uruchom następujące polecenie, aby umożliwić wykonywanie skryptów. Po wyświetleniu monitu wybierz pozycję **Tak na wszystkich.**

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Uruchom create-AzureDevVM.ps1.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    Po wyświetleniu monitu podaj następujące informacje:

    * **Identyfikator subskrypcji platformy Azure:** Twój identyfikator subskrypcji, który można znaleźć w [usłudze Subskrypcje platformy Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) w portalu.
    * **Nazwa grupy zasobów:** Nazwa nowej lub istniejącej grupy zasobów na platformie Azure.
    * **Lokalizacja:** Wybierz lokalizację platformy Azure, w której zostanie utworzona maszyna wirtualna. Na przykład "Zachodnie stany USA 2" lub "Europa Północna". Aby uzyskać więcej informacji, zobacz [lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/).
    * **Nazwa użytkownika**: Podaj zapadającą w pamięć nazwę konta administratora maszyny Wirtualnej.
    * **Hasło:** Ustaw hasło dla konta administratora maszyny Wirtualnej.

   Skrypt jest uruchamiany przez kilka minut, ponieważ wykonuje następujące kroki:

    1. Instaluje [moduł Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.1.0).
    1. Monituje o zalogowanie się na platformie Azure.
    1. Potwierdza informacje dotyczące tworzenia maszyny Wirtualnej. Naciśnij **klawisz y** lub **Enter,** aby kontynuować.
    1. Tworzy grupę zasobów, jeśli nie istnieje.
    1. Wdraża maszynę wirtualną.
    1. Włącza funkcji Hyper-V na maszynie wirtualnej.
    1. Instaluje oprogramowanie potrzebne do tworzenia i klonowania repozytorium próbek.
    1. Uruchamia ponownie maszynę wirtualną.
    1. Tworzy plik RDP na pulpicie w celu nawiązania połączenia z maszyną wirtualną.

   Jeśli zostanie wyświetlony monit o ponowne uruchomienie nazwy maszyny Wirtualnej, można skopiować jej nazwę z danych wyjściowych skryptu. Dane wyjściowe pokazuje również ścieżkę do pliku RDP do łączenia się z maszyną wirtualną.

### <a name="set-auto-shutdown-schedule"></a>Ustawianie harmonogramu automatycznego zamykania

Aby zmniejszyć koszty, maszyna wirtualna rozwoju została utworzona z harmonogramem automatycznego zamykania, który jest ustawiony na 1900 PST. Może być konieczne zaktualizowanie tego ustawienia w zależności od lokalizacji i harmonogramu. Aby zaktualizować harmonogram zamykania:

1. W witrynie Azure portal przejdź do maszyny Wirtualnej, która została utworzona przez skrypt.

1. Z menu lewego okienka w obszarze **Operacje**wybierz pozycję **Automatyczne zamykanie**.

1. Dostosuj **zaplanowane zamknięcie i** **strefę czasową** zgodnie z potrzebami i **wybierz**zapisz .

## <a name="connect-to-the-development-vm"></a>Łączenie się z deweloperem maszyny Wirtualnej

Teraz, gdy stworzyliśmy maszynę wirtualną, musimy zakończyć instalowanie oprogramowania potrzebnego do ukończenia samouczka.

1. Kliknij dwukrotnie plik RDP utworzony na pulpicie.

1. Zostanie wyświetlone okno dialogowe z informacją, że wydawca połączenia zdalnego jest nieznany. Jest to dopuszczalne, więc wybierz **Połącz**.

1. Podaj hasło administratora podane w celu utworzenia maszyny Wirtualnej i kliknij przycisk **OK**.

1. Zostanie wyświetlony monit o zaakceptowanie certyfikatu dla maszyny Wirtualnej. Wybierz **pozycję Tak**.

## <a name="install-visual-studio-code-extensions"></a>Instalowanie rozszerzeń kodu programu Visual Studio

Teraz, gdy masz połączenie z komputerem deweloperskim, dodaj kilka przydatnych rozszerzeń do programu Visual Studio Code, aby ułatwić środowisko programistyczne.

1. Połącz się z deweloperem, otwórz okno programu PowerShell i przejdź do katalogu **C:\source\IoTEdgeAndMlSample\DevVM.** Ten katalog został utworzony przez skrypt, który utworzył maszynę wirtualną.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\DevVM
    ```

1. Uruchom następujące polecenie, aby umożliwić wykonywanie skryptów. Po wyświetleniu monitu wybierz pozycję **Tak na wszystkich.**

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Uruchom skrypt rozszerzeń kodu programu Visual Studio.

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

1. Skrypt będzie działać przez kilka minut, instalując rozszerzenia kodu VS:

    * Narzędzia usługi Azure IoT
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>Konfigurowanie centrum IoT Hub i magazynu

Te kroki są zazwyczaj wykonywane przez dewelopera chmury.

Usługa Azure IoT Hub jest sercem dowolnej aplikacji IoT, ponieważ obsługuje bezpieczną komunikację między urządzeniami IoT a chmurą. Jest głównym punktem koordynacji działania rozwiązania do uczenia maszynowego IoT Edge.

* Usługa IoT Hub używa tras do kierowania danych przychodzących z urządzeń IoT do innych usług podrzędnych. Skorzystamy z tras Usługi IoT Hub, aby wysłać dane urządzenia do usługi Azure Storage. W usłudze Azure Storage dane urządzenia są używane przez usługę Azure Machine Learning w celu przeszkolenia klasyfikatora pozostałego okresu użytkowania (RUL).

* W dalszej części samouczka użyjemy usługi IoT Hub do konfigurowania i zarządzania naszym urządzeniem usługi Azure IoT Edge.

W tej sekcji używasz skryptu do utworzenia centrum Usługi Azure IoT hub i konta usługi Azure Storage. Następnie w witrynie Azure portal konfigurujesz trasę, która przekazuje dane odebrane przez centrum do kontenera usługi Azure Storage. Wykonanie tych czynności trwa około 10 minut.

1. Połącz się z deweloperską maszyną wirtualną, otwórz okno programu PowerShell i przejdź do katalogu **IoTHub.**

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Uruchom skrypt tworzenia. Użyj tych samych wartości dla identyfikatora subskrypcji, lokalizacji i grupy zasobów, jak podczas tworzenia maszyny Wirtualnej rozwoju.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location <location> -ResourceGroupName <resource group>
    ```

    * Zostanie wyświetlony monit o zalogowanie się do platformy Azure.
    * Skrypt potwierdza informacje dotyczące tworzenia konta Centrum i magazynu. Naciśnij **klawisz y** lub **Enter,** aby kontynuować.

Wykonywanie skryptu trwa około dwóch minut. Po zakończeniu skrypt wyprowadza nazwę centrum IoT hub i konto magazynu.

## <a name="review-route-to-storage-in-iot-hub"></a>Przejrzyj trasę do magazynu w Centrum IoT

W ramach tworzenia centrum IoT hub, skrypt, który uruchomiliśmy w poprzedniej sekcji również utworzony niestandardowy punkt końcowy i trasy. Trasy usługi IoT Hub składają się z wyrażenia zapytania i punktu końcowego. Jeśli wiadomość pasuje do wyrażenia, dane są wysyłane wzdłuż trasy do skojarzonego punktu końcowego. Punktami końcowymi mogą być centra zdarzeń, kolejki magistrali usług i tematy. W takim przypadku punkt końcowy jest kontenerem obiektów blob na koncie magazynu. Użyjmy witryny Azure portal, aby przejrzeć trasę utworzoną przez nasz skrypt.

1. Otwórz [witrynę Azure portal](https://portal.azure.com) i przejdź do grupy zasobów, której używasz w tym samouczku.

1. Na liście zasobów wybierz Centrum IoT, który został utworzony przez skrypt. Będzie miał nazwę kończącą się `IotEdgeAndMlHub-jrujej6de6i7w`losowymi znakami, takimi jak .

1. Z menu lewego okienka w obszarze **Wiadomości**wybierz pozycję **Routing wiadomości**.

1. Na stronie **Routing wiadomości** wybierz kartę **Niestandardowe punkty końcowe.**

1. Rozwiń sekcję **Magazyn:**

   ![Sprawdź, czy turbofanDeviceStorage znajduje się na liście niestandardowych punktów końcowych](media/tutorial-machine-learning-edge-02-prepare-environment/custom-endpoints.png)

   Widzimy **turbofanDeviceStorage** znajduje się na liście niestandardowych punktów końcowych. Należy zwrócić uwagę na następujące właściwości dotyczące tego punktu końcowego:

   * Wskazuje utworzony kontener magazynu obiektów blob o nazwie, `devicedata` zgodnie z nazwą **kontenera**.
   * Jego **format Filename** ma partycję jako ostatni element w nazwie. Uważamy, że ten format jest wygodniejszy dla operacji plików zrobimy z notesów platformy Azure w dalszej części samouczka.
   * Jego **stan** powinien być zdrowy.

1. Wybierz kartę **Trasa**.

1. Wybierz trasę o nazwie **turbofanDeviceDataToStorage**.

1. Na **trasy szczegóły** strony należy zauważyć, że punkt końcowy trasy jest **turbofanDeviceStorage** punktu końcowego.

   ![Przejrzyj szczegóły dotyczące trasy turbofanDeviceDataToStorage](media/tutorial-machine-learning-edge-02-prepare-environment/route-details.png)

1. Spójrz na **kwerendę routingu**, która jest ustawiona na **true**. To ustawienie oznacza, że wszystkie komunikaty telemetryczne urządzenia będą zgodne z tą trasą; i dlatego wszystkie wiadomości zostaną wysłane do punktu końcowego **turbofanDeviceStorage.**

1. Ponieważ nie wprowadzono żadnych zmian, po prostu zamknij tę stronę.

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzyliśmy Centrum IoT i skonfigurowaliśmy trasę do konta usługi Azure Storage. Następnie wyślemy dane z zestawu symulowanych urządzeń za pośrednictwem usługi IoT Hub do konta magazynu. W dalszej części samouczka po skonfigurowaniu naszego urządzenia i modułów usługi IoT Edge ponownie przejrzymy trasy i przyjrzymy się nieco więcej zapytaniu routingu.

Aby uzyskać więcej informacji na temat kroków omówionych w tej części uczenia maszynowego w uliczce IoT Edge, zobacz:

* [Azure IoT Edge — podstawy](https://docs.microsoft.com/azure/iot-fundamentals/)
* [Konfigurowanie routingu wiadomości przy użyciu usługi IoT Hub](../iot-hub/tutorial-routing.md)
* [Tworzenie centrum IoT przy użyciu portalu Azure](../iot-hub/iot-hub-create-through-portal.md)

Przejdź do następnego artykułu, aby utworzyć symulowane urządzenie do monitorowania.

> [!div class="nextstepaction"]
> [Generowanie danych urządzenia](tutorial-machine-learning-edge-03-generate-data.md)
