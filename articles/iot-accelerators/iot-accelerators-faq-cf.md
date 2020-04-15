---
title: Często zadawane pytania dotyczące rozwiązania połączonej fabryki — Platforma Azure | Dokumenty firmy Microsoft
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące akceleratora rozwiązań Połączonej fabryki. Zawiera łącza do repozytorium GitHub.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: f2f8c08176f80436a339924adb4b2a09338a548a
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313584"
---
# <a name="frequently-asked-questions-for-connected-factory-solution-accelerator"></a>Często zadawane pytania dotyczące akceleratora rozwiązań Connected Factory

Zobacz też ogólne często zadawane [pytania](iot-accelerators-faq.md) dotyczące akceleratorów rozwiązań IoT.

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerator"></a>Gdzie można znaleźć kod źródłowy akceleratora rozwiązań?

Kod źródłowy jest przechowywany w następującym repozytorium GitHub:

* [Akcelerator rozwiązań Connected Factory](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>Co to jest OPC UA?

OPC Unified Architecture (UA), wydany w 2008 roku, jest niezależnym od platformy, zorientowanym na usługi standardem interoperacyjności. OPC UA jest używany przez różne systemy i urządzenia przemysłowe, takie jak komputery przemysłowe, sterowniki PLC i czujniki. OPC UA integruje funkcjonalność specyfikacji OPC Classic w jedną rozszerzalną strukturę z wbudowanymi zabezpieczeniami. Jest to standard, który jest napędzany przez Fundację OPC. [Fundacja OPC](https://opcfoundation.org/) jest organizacją non-profit z ponad 440 członkami. Celem organizacji jest wykorzystanie specyfikacji OPC w celu ułatwienia wielodostawowej, wieloplatformowej, bezpiecznej i niezawodnej interoperacyjności poprzez:

* Infrastruktura
* Specyfikacje
* Technologia
* Procesy

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-solution-accelerator"></a>Dlaczego firma Microsoft wybrała OPC UA dla akceleratora rozwiązań Connected Factory?

Firma Microsoft wybrała OPC UA, ponieważ jest to otwarty, niezastrzeżony, niezależny od platformy, uznany w branży i sprawdzony standard. Jest to wymóg dla rozwiązań architektury referencyjnej Industrye 4.0 (RAMI4.0) zapewniających interoperacyjność między szerokim zestawem procesów produkcyjnych i urządzeń. Firma Microsoft widzi zapotrzebowanie klientów na tworzenie rozwiązań Industrye 4.0. Wsparcie dla OPC UA pomaga zmniejszyć barierę dla klientów, aby osiągnąć swoje cele i zapewnia im natychmiastową wartość biznesową.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Jak dodać publiczny adres IP do symulacji maszyny Wirtualnej?

Masz dwie opcje, aby dodać adres IP:

* Użyj skryptu `Simulation/Factory/Add-SimulationPublicIp.ps1` programu PowerShell w [repozytorium](https://github.com/Azure/azure-iot-connected-factory). Przekaż nazwę wdrożenia jako parametr. W przypadku wdrożenia `<your username>ConnFactoryLocal`lokalnego należy użyć pliku . Skrypt drukuje adres IP maszyny Wirtualnej.

* W witrynie Azure portal znajdź grupę zasobów wdrożenia. Z wyjątkiem wdrożenia lokalnego grupa zasobów ma nazwę określoną jako rozwiązanie lub nazwę wdrożenia. W przypadku wdrożenia lokalnego przy użyciu skryptu `<your username>ConnFactoryLocal`kompilacji nazwa grupy zasobów to . Teraz dodaj nowy zasób **publicznego adresu IP** do grupy zasobów.

> [!NOTE]
> W obu przypadkach upewnij się, że zainstalujesz najnowsze poprawki, postępując zgodnie z instrukcjami na [stronie internetowej Ubuntu](https://wiki.ubuntu.com/Security/Upgrades). Aktualizuj instalację tak długo, jak długo maszyna wirtualna jest dostępna za pośrednictwem publicznego adresu IP.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Jak usunąć publiczny adres IP do symulacji maszyny Wirtualnej?

Masz dwie opcje usunięcia adresu IP:

* Użyj skryptu Programu PowerShell Simulation/Factory/Remove-SimulationPublicIp.ps1 [repozytorium](https://github.com/Azure/azure-iot-connected-factory). Przekaż nazwę wdrożenia jako parametr. W przypadku wdrożenia `<your username>ConnFactoryLocal`lokalnego należy użyć pliku . Skrypt drukuje adres IP maszyny Wirtualnej.

* W witrynie Azure portal znajdź grupę zasobów wdrożenia. Z wyjątkiem wdrożenia lokalnego grupa zasobów ma nazwę określoną jako rozwiązanie lub nazwę wdrożenia. W przypadku wdrożenia lokalnego przy użyciu skryptu `<your username>ConnFactoryLocal`kompilacji nazwa grupy zasobów to . Teraz usuń zasób **publiczny adres IP** z grupy zasobów.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Jak zalogować się do symulacji maszyny Wirtualnej?

Logowanie się do maszyny Wirtualnej symulacji jest obsługiwane tylko wtedy, `build.ps1` gdy rozwiązanie zostało wdrożone przy użyciu skryptu programu PowerShell w [repozytorium](https://github.com/Azure/azure-iot-connected-factory).

Jeśli rozwiązanie wdrożono z www.azureiotsolutions.com, nie można zalogować się do maszyny Wirtualnej. Nie można się zalogować, ponieważ hasło jest generowane losowo i nie można go zresetować.

1. Dodaj publiczny adres IP do maszyny Wirtualnej. Zobacz [Jak dodać publiczny adres IP do maszyny wirtualnej symulacji?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Utwórz sesję SSH na maszynie wirtualnej przy użyciu adresu IP maszyny Wirtualnej.
1. Nazwa użytkownika do `docker`użycia to: .
1. Hasło do użycia zależy od wersji używanej do wdrożenia:
    * W przypadku rozwiązań wdrożonych przy użyciu skryptu build.ps1 przed 1 `Passw0rd`czerwca 2017 r. hasło brzmi: .
    * W przypadku rozwiązań wdrożonych przy użyciu skryptu build.ps1 po 1 czerwca `<name of your deployment>.config.user` 2017 r. hasło można znaleźć w pliku. Hasło jest przechowywane w ustawieniu **VmAdminPassword.** Hasło jest generowane losowo w czasie wdrażania, `build.ps1` chyba że określisz go przy użyciu parametru skryptu`-VmAdminPassword`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Jak zatrzymać i uruchomić wszystkie procesy platformy docker na maszynie wirtualnej symulacji?

1. Zaloguj się do symulacji maszyny Wirtualnej. Zobacz [Jak zalogować się do symulacji maszyny Wirtualnej?](#how-do-i-sign-in-to-the-simulation-vm)
1. Aby sprawdzić, które kontenery `docker ps`są aktywne, uruchom: .
1. Aby zatrzymać wszystkie kontenery `./stopsimulation`symulacyjne, uruchom: .
1. Aby uruchomić wszystkie kontenery symulacji:
    * Wyeksportuj zmienną powłoki o nazwie **IOTHUB_CONNECTIONSTRING**. Użyj wartości ustawienia **IotHubOwnerConnectionString** w `<name of your deployment>.config.user` pliku. Przykład:

        ```sh
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Uruchom polecenie `./startsimulation`.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Jak zaktualizować symulację na maszynie wirtualnej?

Jeśli wprowadzono jakiekolwiek zmiany w symulacji, można użyć `build.ps1` skryptu programu PowerShell `updatedimulation` w [repozytorium](https://github.com/Azure/azure-iot-connected-factory) za pomocą polecenia. Ten skrypt tworzy wszystkie składniki symulacji, zatrzymuje symulację na maszynie Wirtualnej, przekazuje, instaluje i uruchamia je.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Jak sprawdzić ciąg połączenia koncentratora IoT używanego przez moje rozwiązanie?

Jeśli rozwiązanie wdrożono ze `build.ps1` skryptem w [repozytorium,](https://github.com/Azure/azure-iot-connected-factory)parametry połączenia są wartością **IotHubOwnerConnectionString** w `<name of your deployment>.config.user` pliku.

Ciąg połączenia można również znaleźć za pomocą witryny Azure portal. W zasobie Usługi IoT Hub w grupie zasobów wdrożenia zlokalizuj ustawienia ciągu połączenia.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Jakich urządzeń IoT Hub używa symulacja Połączonej Fabryki?

Symulacja samodzielnie rejestruje następujące urządzenia:

* proxy.beijing.corp.contoso
* serwer proxy.capetown.corp.contoso
* proxy.mumbai.corp.contoso
* proxy.munich0.corp.contoso
* serwer proxy.rio.corp.contoso
* plik proxy.seattle.corp.contoso
* wydawca.beijing.corp.contoso
* wydawca.capetown.corp.contoso
* wydawca.mumbai.corp.contoso
* wydawca.munich0.corp.contoso
* wydawca.rio.corp.contoso
* publisher.seattle.corp.contoso

Za pomocą [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) lub [rozszerzenie IoT dla narzędzia Interfejsu wiersza polecenia platformy Azure,](https://github.com/Azure/azure-iot-cli-extension) można sprawdzić, które urządzenia są zarejestrowane w centrum IoT hub używa rozwiązania. Aby użyć Eksploratora urządzeń, potrzebujesz ciągu połączenia dla centrum IoT hub we wdrożeniu. Aby użyć rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure, potrzebujesz nazwy usługi IoT Hub.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Jak uzyskać dane dziennika ze składników symulacji?

Wszystkie składniki w informacje dziennika symulacji do rejestrowania plików. Pliki te można znaleźć na maszynie `home/docker/Logs`Wirtualnej w folderze . Aby pobrać dzienniki, można użyć skryptu `Simulation/Factory/Get-SimulationLogs.ps1` programu PowerShell w [repozytorium](https://github.com/Azure/azure-iot-connected-factory).

Ten skrypt musi zalogować się do maszyny Wirtualnej. Może być konieczne podanie poświadczeń dla logowania. Zobacz [Jak zalogować się do symulacji maszyny Wirtualnej?](#how-do-i-sign-in-to-the-simulation-vm)

Skrypt dodaje/usuwa publiczny adres IP do maszyny Wirtualnej, jeśli nie ma jeszcze jednego i usuwa go. Skrypt umieszcza wszystkie pliki dziennika w archiwum i pobiera archiwum do stacji roboczej rozwoju.

Alternatywnie zaloguj się do maszyny Wirtualnej za pośrednictwem protokołu SSH i sprawdź pliki dziennika w czasie wykonywania.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Jak sprawdzić, czy symulacja wysyła dane do chmury?

Za pomocą [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) lub [usługi Azure IoT CLI Extension monitor-events](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub?view=azure-cli-latest#ext-azure-iot-az-iot-hub-monitor-events) polecenia, można sprawdzić dane wysyłane do Usługi IoT Hub z niektórych urządzeń. Aby korzystać z tych narzędzi, należy znać parametry połączenia dla centrum IoT hub we wdrożeniu. Zobacz [Jak sprawdzić parametry połączenia koncentratora IoT używanego przez moje rozwiązanie?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Sprawdź dane wysyłane przez jedno z urządzeń wydawcy:

* wydawca.beijing.corp.contoso
* wydawca.capetown.corp.contoso
* wydawca.mumbai.corp.contoso
* wydawca.munich0.corp.contoso
* wydawca.rio.corp.contoso
* publisher.seattle.corp.contoso

Jeśli nie widzisz żadnych danych wysłanych do usługi IoT Hub, występuje problem z symulacją. W pierwszym kroku analizy należy przeanalizować pliki dziennika składników symulacji. Zobacz [Jak uzyskać dane dziennika ze składników symulacji?](#how-can-i-get-log-data-from-the-simulation-components) Następnie spróbuj zatrzymać i rozpocząć symulację, a jeśli nadal nie ma żadnych danych, należy całkowicie zaktualizować symulację. Zobacz [Jak zaktualizować symulację na maszynie wirtualnej?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>Jak włączyć interaktywną mapę w rozwiązaniu Connected Factory?

Aby włączyć interaktywną mapę w rozwiązaniu Połączonego systemu fabrycznego, musisz mieć konto usługi Azure Maps.

Podczas wdrażania z [www.azureiotsolutions.com](https://www.azureiotsolutions.com)proces wdrażania dodaje konto usługi Azure Maps do grupy zasobów, która zawiera usługi akceleratora rozwiązań.

Podczas wdrażania przy `build.ps1` użyciu skryptu w repozytorium Połączonej `$env:MapApiQueryKey` Fabryki GitHub ustaw zmienną środowiskową w oknie [kompilacji](../azure-maps/how-to-manage-account-keys.md)na klucz konta usługi Azure Maps . Interaktywna mapa jest następnie włączana automatycznie.

Można również dodać klucz konta usługi Azure Maps do akceleratora rozwiązań po wdrożeniu. Przejdź do witryny Azure portal i uzyskaj dostęp do zasobu usługi App Service we wdrożeniu połączonej fabryki. Przejdź do **sekcji Ustawienia aplikacji**, w której znajduje się sekcja Ustawienia **aplikacji**. Ustaw **mapapiQueryKey** na [klucz swojego konta usługi Azure Maps](../azure-maps/how-to-manage-account-keys.md). Zapisz ustawienia, a następnie przejdź do **sekcji Przegląd** i uruchom ponownie usługę App Service.

### <a name="how-do-i-create-an-azure-maps-account"></a>Jak utworzyć konto usługi Azure Maps?

Zobacz [Jak zarządzać kontem i kluczami usługi Azure Maps](../azure-maps/how-to-manage-account-keys.md).

### <a name="how-to-obtain-your-azure-maps-account-key"></a>Jak uzyskać klucz konta usługi Azure Maps

Zobacz [Jak zarządzać kontem i kluczami usługi Azure Maps](../azure-maps/how-to-manage-account-keys.md).

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>Jak włączyć interaktywną mapę podczas debugowania lokalnie?

Aby włączyć interaktywną mapę podczas debugowania lokalnie, `MapApiQueryKey` ustaw wartość `local.user.config` `<yourdeploymentname>.user.config` ustawienia w plikach i w katalogu głównym wdrożenia na wartość **querykey** skopiowany wcześniej.

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>Jak użyć innego obrazu na stronie głównej pulpitu nawigacyjnego?

Aby zmienić obraz statyczny wyświetlany io na stronie `WebApp\Content\img\world.jpg`głównej pulpitu nawigacyjnego, zastąp obraz . Następnie odbuduj i ponownie wdrożyć webapp.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>Jak używać urządzeń innych niż OPC UA z connected factory?

Aby wysłać dane telemetryczne z urządzeń innych niż OPC UA do connected factory:

1. [Skonfiguruj nową stację w topologii połączonej fabryki](iot-accelerators-connected-factory-configure.md) w `ContosoTopologyDescription.json` pliku.

1. Ponajmowanie danych telemetrycznych w formacie JSON zgodnym z connected factory:

    ```json
    [
      {
        "ApplicationUri": "<the_value_of_OpcUri_of_your_station",
        "DisplayName": "<name_of_the_datapoint>",
        "NodeId": "value_of_NodeId_of_your_datapoint_in_the_station",
        "Value": {
          "Value": <datapoint_value>,
          "SourceTimestamp": "<timestamp>"
        }
      }
    ]
    ```

1. Format `<timestamp>` jest:`2017-12-08T19:24:51.886753Z`

1. Uruchom ponownie usługę aplikacji połączonej fabryki.

### <a name="next-steps"></a>Następne kroki

Możesz także wypróbować niektóre inne funkcje i możliwości akceleratorów rozwiązań IoT:

* [Omówienie akceleratora rozwiązań do konserwacji zapobiegawczej](iot-accelerators-predictive-overview.md)
* [Wdrażanie akceleratora rozwiązań Connected Factory](quickstart-connected-factory-deploy.md)
* [Zabezpieczenia IoT od podstaw](/azure/iot-fundamentals/iot-security-ground-up)
