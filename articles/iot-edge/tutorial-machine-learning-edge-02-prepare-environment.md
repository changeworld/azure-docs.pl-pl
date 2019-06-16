---
title: Konfigurowanie środowiska — Machine Learning na platformie Azure IoT Edge | Dokumentacja firmy Microsoft
description: Przygotowywanie środowiska do opracowywania i wdrażania modułów usługi machine learning na urządzeniach brzegowych.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f8652dab5db8bbd59982cb1dda727698868df23c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057743"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Samouczek: Konfigurowanie środowiska na potrzeby usługi machine learning w usłudze IoT Edge

> [!NOTE]
> Ten artykuł jest częścią serii, samouczek dotyczący przy użyciu usługi Azure Machine Learning w usłudze IoT Edge. Jeśli została wyświetlona bezpośrednio w tym artykule, firma Microsoft zachęca rozpoczynać się [najpierw artykuł](tutorial-machine-learning-edge-01-intro.md) z tej serii, aby uzyskać najlepsze wyniki.

W tym artykule z end-to-end usługi Azure Machine Learning na samouczek dotyczący usługi IoT Edge ułatwi Ci lepsze przygotowanie środowiska do opracowywania i wdrażania. Najpierw należy skonfigurować jest maszyna deweloperska z narzędziami, których potrzebujesz. Następnie utwórz zasoby niezbędne chmury na platformie Azure.

## <a name="set-up-a-development-machine"></a>Konfigurowanie komputera deweloperskiego z systemem

Ten krok jest najczęściej wykonywane przez dewelopera w chmurze. Część oprogramowania mogą być również przydatne do wydobywania danych.

W miarę upływu w tym artykule możemy wykonywać różne zadania dla deweloperów, w tym kodowania, kompilowania, konfigurowanie i wdrażanie moduły usługi IoT Edge i urządzeń IoT. Łatwość użycia utworzyliśmy skrypt programu PowerShell, który tworzy maszynę wirtualną platformy Azure z wieloma wymagania wstępne już skonfigurowane. Maszynę Wirtualną, która zostanie utworzona musi być w stanie obsłużyć [wirtualizacji zagnieżdżonej](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization), co jest dlaczego Wybraliśmy DS8V3 rozmiaru maszyny.

Tworzenie maszyny Wirtualnej będzie można skonfigurować za pomocą:

* Windows 10
* [Chocolatey](https://chocolatey.org/)
* [Desktop docker for Windows](https://www.docker.com/products/docker-desktop)
* [Git dla systemu Windows](https://gitforwindows.org/)
* [Program Git Credential Manager dla Windows](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [Zestaw .Net Core SDK](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.1.0)
* [Rozszerzenia kodu programu VS](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [Program PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

Deweloper maszyny Wirtualnej nie jest bezwzględnie konieczne — wszystkie narzędzia programistyczne mogą być uruchamiane na maszynie lokalnej. Jednak zdecydowanie zaleca się używania maszyny Wirtualnej zapewnienie równych.

Tworzenie i konfigurowanie maszyny wirtualnej trwa około 30 minut.

### <a name="get-the-script"></a>Pobierz skrypt

Klonuj lub Pobierz skrypt programu PowerShell z [uczenia maszynowego i usługi IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) przykładowego repozytorium.

### <a name="create-an-azure-virtual-machine"></a>Utwórz maszynę wirtualną platformy Azure

Katalog DevVM zawiera pliki potrzebne do utworzenia maszyny wirtualnej platformy Azure odpowiedni dla wykonanie kroków tego samouczka.

1. Otwórz program Powershell jako administrator i przejdź do katalogu, w której pobrano kod. Firma Microsoft będzie odnosił się do katalogu głównego źródła jako `<srcdir>`.

    ```powershell
    cd \<srcdir\>\IoTEdgeAndMlSample\DevVM
    ```

2. Uruchom następujące polecenie, aby umożliwić wykonywanie skryptów. Wybierz **tak na wszystko** po wyświetleniu monitu.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

3. Uruchom tworzenie AzureDevVM.ps1 z tego katalogu.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    * Po wyświetleniu monitu podaj następujące informacje:
      * **Identyfikator subskrypcji platformy Azure**: Identyfikator subskrypcji, który można znaleźć w witrynie Azure portal
      * **Nazwa grupy zasobów**: Nazwa nowej lub istniejącej grupy zasobów na platformie Azure
      * **Lokalizacja**: Wybierz lokalizację platformy Azure, której maszyna wirtualna zostanie utworzona. Na przykład westus2 lub northeurope. Aby uzyskać więcej informacji, zobacz [lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/).
      * **AdminUsername**: Podaj łatwych do zapamiętania nazwę konta administratora, który chcesz utworzyć i używanie na maszynie wirtualnej.
      * **AdminPassword**: Ustaw hasło dla konta administratora na maszynie wirtualnej.

    * Jeśli nie masz zainstalowany program Azure PowerShell, skrypt zainstaluje [modułu Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.1.0)

    * Zostanie wyświetlony monit o zalogowanie się do platformy Azure.

    * Skrypt potwierdza informacje dotyczące tworzenia maszyny Wirtualnej. Naciśnij klawisz `y` lub `Enter` aby kontynuować.

Skrypt jest uruchamiany przez kilka minut, ponieważ wykonuje następujące czynności:

* Utwórz grupę zasobów, jeśli nie istnieje
* Wdrażanie maszyny wirtualnej
* Włączanie funkcji Hyper-V na maszynie Wirtualnej
* Instalowanie oprogramowania na potrzeby programowania i sklonowania przykładowego repozytorium
* Uruchom ponownie maszynę Wirtualną
* Utwórz plik protokołu RDP na pulpicie systemu Windows do łączenia się z maszyną Wirtualną

### <a name="set-auto-shutdown-schedule"></a>Harmonogram automatycznego zamykania zestawu

Aby pomóc w zmniejszeniu kosztów, maszyna wirtualna została utworzona za pomocą harmonogramu automatycznego zamykania, który jest ustawiony na 1900 PST. Może być konieczne zaktualizowanie tego czasu w zależności od lokalizacji i harmonogram. Aby zaktualizować harmonogramem zamykania:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Przejdź do maszyny wirtualnej w grupie zasobów, podany w poprzedniej sekcji.

3. Wybierz **automatycznego zamykania** w Nawigatorze po stronie.

4. Wprowadź nową wartość czasu zamknięcia w **zaplanowano zamknięcie** lub zmienić **strefa czasowa** kliknięcie **Zapisz**.

### <a name="connect-and-configure-development-machine"></a>Połącz i skonfiguruj komputerze deweloperskim

Teraz, gdy utworzono Maszynę wirtualną należy zakończyć instalowanie oprogramowanie niezbędne do ukończenia tego samouczka.

#### <a name="start-a-remote-desktop-session"></a>Rozpocznij sesję usług pulpitu zdalnego

1. Skrypt tworzenia maszyny Wirtualnej utworzony plik RDP na pulpicie.

2. Kliknij dwukrotnie plik o nazwie  **\<nazwę maszyny Wirtualnej platformy Azure\>RDP**.

3. Użytkownik zobaczy okno dialogowe z informacją, że wydawcy połączenia zdalnego jest nieznany. Kliknij przycisk **nie pytaj mnie ponownie dla połączeń z tym komputerem** następnie zaznacz pole wyboru **Connect**.

4. Po wyświetleniu monitu podaj AdminPassword, używanym przez uruchomienie skryptu do skonfigurowania maszyny Wirtualnej i kliknij przycisk **OK**.

5. Zostanie wyświetlony monit zaakceptować certyfikat dla maszyny Wirtualnej. Wybierz **nie pytaj mnie ponownie dla połączeń z tym komputerem** i wybierz polecenie **tak**.

#### <a name="install-visual-studio-code-extensions"></a>Instalowanie rozszerzeń programu Visual Studio Code

Po podłączeniu do komputera deweloperskiego, dodać niektóre przydatne rozszerzenia programu Visual Studio Code dzięki niej programowanie łatwiejsze w obsłudze środowisko.

1. W oknie programu PowerShell, przejdź do **C:\\źródła\\IoTEdgeAndMlSample\\DevVM**.

2. Zezwalaj na skrypty, które mają być wykonane na maszynie wirtualnej, wpisując polecenie.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope CurrentUser -Force
    ```

3. Uruchom skrypt.

    ```powershell
    .\\Enable-CodeExtensions.ps1
    ```

4. Skrypt będzie uruchamiany przez kilka minut, instalowanie rozszerzeń programu VS code:

    * Narzędzia usługi Azure IoT
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>Skonfiguruj usługę IoT Hub i Magazyn

Kroki te są zazwyczaj wykonywane przez dewelopera chmury.

Usługa Azure IoT Hub jest niezwykle ważnymi zasobami każdej aplikacji IoT. Obsługuje on bezpiecznej komunikacji między chmurą a urządzeniami IoT. Jest punkt głównego koordynacji operacji rozwiązanie uczenia maszynowego usługi IoT Edge.

* Usługa IoT Hub używa trasy do kierowania danych przychodzących z urządzeń IoT z innymi usługami podrzędnymi. Firma Microsoft będzie korzystać z usługi IoT Hub tras wysyłanie danych urządzenia do usługi Azure Storage, gdzie mogą być używane przez usługi Azure Machine Learning to w opracowywaniu naszych pozostałych klasyfikatora okres eksploatacji (RUL).

* W dalszej części samouczka użyjemy usługi IoT Hub do konfigurowania i zarządzania naszych urządzenia Azure IoT Edge.

W tej sekcji użyjesz skryptu do tworzenia usługi Azure IoT hub i konto usługi Azure Storage. Następnie możesz skonfigurować trasę, która przekazuje dane odebrane przez Centrum do kontenera obiektów Blob magazynu platformy Azure przy użyciu witryny Azure portal. Te kroki zająć około 10 minut.

### <a name="create-cloud-resources"></a>Tworzenie zasobów w chmurze

1. Na komputerze deweloperskim Otwórz okno programu PowerShell.

1. Przejdź do katalogu IoTHub.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Uruchom skrypt tworzenia. Na użytek tych samych wartości subskrypcji Identyfikatora, lokalizacji i grupie zasobów tak jak w przypadku tworzenia deweloperskiej maszynie Wirtualnej.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location
    <location> -ResourceGroupName <resource group>
    ```

    * Zostanie wyświetlony monit o zalogowanie się do platformy Azure.
    * Skrypt potwierdza informacje dotyczące tworzenia konta Centrum i magazynu. Naciśnij klawisz `y` lub `Enter` aby kontynuować.

Skrypt trwa około dwóch minut do uruchomienia. Po wykonaniu tych czynności, skrypt generuje nazwę Centrum i konta magazynu.

### <a name="review-route-to-storage-in-iot-hub"></a>Przejrzyj kierować ruch do magazynu w usłudze IoT Hub

Podczas tworzenia Centrum IoT hub skrypt, który wystąpił w poprzedniej sekcji również utworzony niestandardowy punkt końcowy, a trasy. Usługi IoT Hub trasy składają się z wyrażenia zapytania i punktu końcowego. Jeśli komunikat jest zgodna z wyrażeniem, dane są przesyłane wzdłuż trasy do skojarzonego punktu końcowego. Punkty końcowe można usługi Event Hubs, kolejki usługi Service Bus i tematy. W tym przypadku punkt końcowy jest kontener obiektów Blob na koncie magazynu. Utwórzmy Przejrzyj trasy utworzone za pomocą naszego skryptu za pomocą witryny Azure portal.

1. Otwórz [portal Azure](http://portal.azure.com).

1. Wybierz wszystkie usługi z Nawigatora po lewej stronie, w polu wyszukiwania wpisz IoT, a następnie wybierz **usługi IoT Hub**.

1. Wybierz warstwę usługi IoT Hub utworzony w poprzednim kroku.

1. W Nawigatorze po stronie usługi IoT Hub, wybierz **routing komunikatów**.

1. Komunikat o routingu strona ma dwie karty **trasy** i **niestandardowe punkty końcowe**. Wybierz **niestandardowe punkty końcowe** kartę.

1. W obszarze **magazynu obiektów Blob**, wybierz opcję **turbofanDeviceStorage**.

1. Należy zauważyć, że ten punkt końcowy wskazuje kontener obiektów blob o nazwie **devicedata** w koncie magazynu utworzonym w poprzednim kroku, który nosi nazwę **iotedgeandml\<unikatowy sufiks\>** .

1. Należy również zauważyć **format nazwy pliku obiektu Blob** został zmieniony z domyślnym formatem, aby zamiast tego umieścić partycji jako ostatniego elementu w nazwie. Nam się znaleźć, że ten format jest bardziej wygodne do operacji plików, które firma Microsoft korzysta z notesami platformy Azure w dalszej części tego samouczka.

1. Zamknij blok szczegóły punktu końcowego, aby powrócić do **routing komunikatów** strony.

1. Wybierz **trasy** kartę.

1. Wybierz trasy o nazwie **turbofanDeviceDataToStorage**.

1. Należy zauważyć, że punkt końcowy trasy **turbofanDeviceStorage** niestandardowego punktu końcowego.

1. Przyjrzyj się routingu kwerendę, która jest równa **true**. Oznacza to, że wszystkie komunikaty telemetryczne urządzenia będą zgodne tę trasę i w związku z tym wszystkie komunikaty zostaną wysłane do **turbofanDeviceStorage** punktu końcowego.

1. Zamknij szczegóły trasy.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule będziemy utworzeniu Centrum IoT Hub i skonfigurowaniu trasę do konta usługi Azure Storage. W następnym artykule firma Microsoft będzie wysyłać dane z zestawu symulowanych urządzeń IoT Hub do konta magazynu. W dalszej części tego samouczka po skonfigurowany naszych urządzenie usługi IoT Edge i moduły, firma Microsoft ponownie tras i wyglądać nieco więcej w routingu zapytania.

Aby uzyskać więcej informacji na temat kroki omówione w tej części usługi Machine Learning na samouczek dotyczący usługi IoT Edge zobacz:

* [Azure IoT Edge — podstawy](https://docs.microsoft.com/azure/iot-fundamentals/)
* [Skonfiguruj routing komunikatów usługi IoT Hub](../iot-hub/tutorial-routing.md)
* [Tworzenie Centrum IoT przy użyciu witryny Azure portal](../iot-hub/iot-hub-create-through-portal.md)

Przejdź do następnego artykułu, aby utworzyć symulowane urządzenie do monitorowania.

> [!div class="nextstepaction"]
> [Generowanie danych urządzenia](tutorial-machine-learning-edge-03-generate-data.md)
