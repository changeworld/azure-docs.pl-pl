---
title: Konfigurowanie środowiska Machine Learning na Azure IoT Edge | Microsoft Docs
description: Przygotuj środowisko do opracowywania i wdrażania modułów na potrzeby uczenia maszynowego na brzegu.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1db94e683a0dfb3b60b12bc5ac205c766d405d0a
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299824"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Samouczek: Skonfiguruj środowisko do uczenia maszynowego na IoT Edge

> [!NOTE]
> Ten artykuł jest częścią serii samouczka dotyczącego używania Azure Machine Learning w IoT Edge. Jeśli ten artykuł został osiągnięty bezpośrednio, zachęcamy do rozpoczęcia od [pierwszego artykułu](tutorial-machine-learning-edge-01-intro.md) z serii w celu uzyskania najlepszych wyników.

Ten artykuł od kompleksowego Azure Machine Learning w samouczku IoT Edge ułatwia przygotowanie środowiska do projektowania i wdrażania. Najpierw skonfiguruj komputer deweloperski przy użyciu wszystkich potrzebnych narzędzi. Następnie utwórz niezbędne zasoby w chmurze na platformie Azure.

## <a name="set-up-a-development-machine"></a>Konfigurowanie komputera deweloperskiego

Ten krok jest zwykle wykonywany przez dewelopera chmury. Niektóre oprogramowanie może być również przydatne w przypadku Analityka danych.

W ramach tego artykułu wykonujemy różne zadania deweloperskie, takie jak kodowanie, kompilowanie, Konfigurowanie i wdrażanie modułów IoT Edge i urządzeń IoT. Aby ułatwić sobie korzystanie z programu, został utworzony skrypt programu PowerShell, który tworzy maszynę wirtualną platformy Azure z wieloma już skonfigurowanymi wymaganiami wstępnymi. Utworzona przez nas maszyna wirtualna musi być w stanie obsłużyć [wirtualizację zagnieżdżoną](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization), co oznacza, że wybrano rozmiar maszyny [Standard_D8s_v3](../virtual-machines/windows/sizes-general.md#dsv3-series-1) .

Maszyna wirtualna programu Development zostanie skonfigurowana przy użyciu:

* Windows 10
* [Narzędzia Chocolatey](https://chocolatey.org/)
* [Pulpit Docker dla systemu Windows](https://www.docker.com/products/docker-desktop)
* [Git dla systemu Windows](https://gitforwindows.org/)
* [Menedżer poświadczeń git dla systemu Windows](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [Zestaw .Net Core SDK](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.1.0)
* [Rozszerzenia VS Code](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

Maszyna wirtualna dla deweloperów nie jest ściśle wymagana — wszystkie narzędzia programistyczne można uruchomić na komputerze lokalnym. Jednak zdecydowanie zalecamy użycie maszyny wirtualnej w celu zagwarantowania pola poziomu odtwarzania.

Tworzenie i Konfigurowanie maszyny wirtualnej trwa około 30 minut.

### <a name="get-the-script"></a>Pobierz skrypt

Sklonuj lub Pobierz skrypt programu PowerShell z przykładowego repozytorium [Machine Learning i IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) .

### <a name="create-an-azure-virtual-machine"></a>Tworzenie maszyny wirtualnej platformy Azure

Katalog DevVM zawiera pliki potrzebne do utworzenia maszyny wirtualnej platformy Azure odpowiednie do wykonania tego samouczka.

1. Otwórz program PowerShell jako administrator i przejdź do katalogu, do którego pobrano kod. Odwołujemy się do katalogu głównego źródła jako `<srcdir>`.

    ```powershell
    cd <srcdir>\IoTEdgeAndMlSample\DevVM
    ```

2. Uruchom następujące polecenie, aby zezwolić na wykonywanie skryptów. Po wyświetleniu monitu wybierz pozycję **tak, aby wszystkie** .

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

3. Uruchom Create-AzureDevVM. ps1 z tego katalogu.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    * Po wyświetleniu monitu podaj następujące informacje:
      * **Identyfikator subskrypcji platformy Azure**: Identyfikator subskrypcji, który można znaleźć w Azure Portal
      * **Nazwa grupy zasobów**: Nazwa nowej lub istniejącej grupy zasobów na platformie Azure
      * **Lokalizacja**: Wybierz lokalizację platformy Azure, w której zostanie utworzona maszyna wirtualna. Na przykład westus2 lub northeurope. Aby uzyskać więcej informacji, zobacz [lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/).
      * **AdminUsername**: Podaj nazwę do zapamiętania dla konta administratora, które chcesz utworzyć i użyć na maszynie wirtualnej.
      * Klucz **AdminPassword**: Ustaw hasło dla konta administratora na maszynie wirtualnej.

    * Jeśli nie masz zainstalowanego Azure PowerShell, skrypt zainstaluje [Azure PowerShell AZ module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.1.0)

    * Zostanie wyświetlony monit o zalogowanie się do platformy Azure.

    * Skrypt potwierdza informacje dotyczące tworzenia maszyny wirtualnej. Naciśnij `y` klawisz `Enter` lub, aby kontynuować.

Skrypt jest uruchamiany przez kilka minut, ponieważ wykonuje następujące czynności:

* Utwórz grupę zasobów, jeśli nie istnieje
* Wdrażanie maszyny wirtualnej
* Włączanie funkcji Hyper-V na maszynie wirtualnej
* Zainstaluj oprogramowanie potrzebne do tworzenia i klonowania przykładowego repozytorium
* Uruchom ponownie maszynę wirtualną
* Tworzenie pliku RDP na pulpicie na potrzeby nawiązywania połączenia z maszyną wirtualną

### <a name="set-auto-shutdown-schedule"></a>Ustawianie harmonogramu automatycznego zamykania

Aby pomóc obniżyć koszty, maszyna wirtualna została utworzona z harmonogramem automatycznego zamykania, ustawionym na 1900 PST. W zależności od lokalizacji i harmonogramu może być konieczne zaktualizowanie tego chronometrażu. Aby zaktualizować harmonogram zamykania:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Przejdź do maszyny wirtualnej w grupie zasobów podanej w poprzedniej sekcji.

3. Wybierz pozycję **automatyczne zamykanie** w Nawigatorze bocznym.

4. Wprowadź nowy czas zamykania w **zaplanowanym zamknięciu** lub Zmień **strefę czasową** , a następnie kliknij przycisk **Zapisz**.

### <a name="connect-and-configure-development-machine"></a>Łączenie i Konfigurowanie maszyny deweloperskiej

Po utworzeniu maszyny wirtualnej musimy zainstalować oprogramowanie wymagane do ukończenia tego samouczka.

#### <a name="start-a-remote-desktop-session"></a>Uruchom sesję pulpitu zdalnego

1. Skrypt tworzenia maszyny wirtualnej utworzył plik RDP na pulpicie.

2. Kliknij dwukrotnie plik o nazwie  **\<Azure VM Name\>. rdp**.

3. Zostanie wyświetlone okno dialogowe z informacją, że Wydawca połączenia zdalnego jest nieznany. Kliknij pole wyboru **nie pytaj mnie ponownie dla połączeń z tym komputerem,** a następnie wybierz pozycję **Połącz**.

4. Po wyświetleniu monitu podaj klucz AdminPassword używany podczas uruchamiania skryptu w celu skonfigurowania maszyny wirtualnej i kliknij przycisk **OK**.

5. Zostanie wyświetlony monit o zaakceptowanie certyfikatu dla maszyny wirtualnej. Wybierz pozycję **nie pytaj mnie ponownie dla połączeń z tym komputerem** i wybierz opcję **tak**.

#### <a name="install-visual-studio-code-extensions"></a>Zainstaluj rozszerzenia Visual Studio Code

Teraz, gdy masz połączenie z maszyną deweloperskią, Dodaj przydatne rozszerzenia, aby Visual Studio Code ułatwić programowanie.

1. W oknie programu PowerShell przejdź do **C:\\\\Source IoTEdgeAndMlSample\\DevVM**.

2. Zezwalaj na wykonywanie skryptów na maszynie wirtualnej, wpisując polecenie.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope CurrentUser -Force
    ```

3. Uruchom skrypt.

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

4. Skrypt będzie uruchamiany przez kilka minut instalacji rozszerzeń programu VS Code:

    * Narzędzia usługi Azure IoT
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>Konfigurowanie IoT Hub i magazynu

Te kroki są zwykle wykonywane przez dewelopera chmury.

Platforma Azure IoT Hub to serce z dowolnej aplikacji IoT. Obsługuje ona bezpieczną komunikację między urządzeniami IoT i chmurą. Jest to główny punkt koordynacji dla operacji rozwiązania IoT Edge Machine Learning.

* IoT Hub używa tras do kierowania danych przychodzących z urządzeń IoT do innych usług podrzędnych. Będziemy używać tras IoT Hub do wysyłania danych urządzenia do usługi Azure Storage, które mogą być używane przez Azure Machine Learning do uczenia pozostałego klasyfikatora użytecznych okresów użytkowania (pozostałego czasu eksploatacji).

* W dalszej części tego samouczka będziemy używać IoT Hub do konfigurowania naszego urządzenia Azure IoT Edge i zarządzania nim.

W tej sekcji użyjesz skryptu do utworzenia usługi Azure IoT Hub i konta usługi Azure Storage. Następnie należy skonfigurować trasę, która przekazuje dane odebrane przez centrum do kontenera Azure Storage Blob przy użyciu Azure Portal. Wykonanie tych czynności zajmie około 10 minut.

### <a name="create-cloud-resources"></a>Tworzenie zasobów w chmurze

1. Na komputerze deweloperskim Otwórz okno programu PowerShell.

1. Przejdź do katalogu IoTHub.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Uruchom skrypt tworzenia. Użyj tych samych wartości dla identyfikatora subskrypcji, lokalizacji i grupy zasobów, jak podczas tworzenia maszyny wirtualnej deweloperskiej.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location
    <location> -ResourceGroupName <resource group>
    ```

    * Zostanie wyświetlony monit o zalogowanie się do platformy Azure.
    * Skrypt potwierdza informacje dotyczące tworzenia centrum i konta magazynu. Naciśnij `y` klawisz `Enter` lub, aby kontynuować.

Wykonanie skryptu trwa około dwie minuty. Po zakończeniu skrypt wyświetla nazwę centrum i konto magazynu.

### <a name="review-route-to-storage-in-iot-hub"></a>Przejrzyj trasy do magazynu w IoT Hub

W ramach tworzenia Centrum IoT, skrypt, który został uruchomiony w poprzedniej sekcji, również utworzył niestandardowy punkt końcowy i trasę. Trasy IoT Hub składają się z wyrażenia zapytania i punktu końcowego. Jeśli komunikat jest zgodny z wyrażeniem, dane są wysyłane wzdłuż trasy do skojarzonego punktu końcowego. Punkty końcowe mogą być Event Hubs, kolejki Service Bus i tematy. W takim przypadku punkt końcowy jest kontenerem obiektów BLOB na koncie magazynu. Użyjmy Azure Portal, aby przejrzeć trasę utworzoną przez nasz skrypt.

1. Otwórz [portal Azure](https://portal.azure.com).

1. Wybierz pozycję Wszystkie usługi w lewym Nawigatorze, wpisz IoT w polu wyszukiwania, a następnie wybierz pozycję **IoT Hub**.

1. Wybierz IoT Hub utworzony w poprzednim kroku.

1. W IoT Hub nawigatora po stronie wybierz pozycję **Routing komunikatów**.

1. Strona routing wiadomości zawiera dwie karty, **trasy** i **niestandardowe punkty końcowe**. Wybierz kartę **niestandardowe punkty końcowe** .

1. W obszarze **BLOB Storage**wybierz pozycję **turbofanDeviceStorage**.

1. Należy zauważyć, że ten punkt końcowy wskazuje kontener obiektów BLOB o nazwie **devicedata** w ramach konta magazynu utworzonego w ostatnim kroku o nazwie **iotedgeandml\<Unique sufiks\>** .

1. Należy również zauważyć, że **Format nazwy pliku obiektu BLOB** został zmieniony z formatu domyślnego, aby zamiast tego umieścić partycję jako ostatni element w nazwie. Ten format jest wygodniejszy dla operacji na plikach, które będziemy wykonywać, Azure Notebooks w dalszej części tego samouczka.

1. Zamknij blok szczegóły punktu końcowego, aby powrócić do strony **routingu wiadomości** .

1. Wybierz kartę **trasy** .

1. Wybierz trasę o nazwie **turbofanDeviceDataToStorage**.

1. Należy zauważyć, że punkt końcowy trasy jest niestandardowym punktem końcowym **turbofanDeviceStorage** .

1. Zapoznaj się z kwerendą routingu, która jest ustawiona na **wartość true**. Oznacza to, że wszystkie komunikaty telemetryczne urządzenia będą zgodne z tą trasą i w związku z tym wszystkie komunikaty będą wysyłane do punktu końcowego **turbofanDeviceStorage** .

1. Zamknij Szczegóły trasy.

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzyliśmy IoT Hub i skonfigurowano trasę do konta usługi Azure Storage. W następnym artykule wyślemy dane z zestawu symulowanych urządzeń za pośrednictwem IoT Hub na konto magazynu. W dalszej części tego samouczka po skonfigurowaniu naszych IoT Edge urządzeń i modułów, będziemy ponownie odwiedzić trasy i poszukać nieco więcej w zapytaniu routingu.

Aby uzyskać więcej informacji na temat kroków opisanych w tej części Machine Learning w samouczku IoT Edge, zobacz:

* [Azure IoT Edge — podstawy](https://docs.microsoft.com/azure/iot-fundamentals/)
* [Konfigurowanie routingu komunikatów przy użyciu IoT Hub](../iot-hub/tutorial-routing.md)
* [Tworzenie Centrum IoT Hub przy użyciu Azure Portal](../iot-hub/iot-hub-create-through-portal.md)

Przejdź do następnego artykułu, aby utworzyć symulowane urządzenie do monitorowania.

> [!div class="nextstepaction"]
> [Generowanie danych urządzenia](tutorial-machine-learning-edge-03-generate-data.md)
