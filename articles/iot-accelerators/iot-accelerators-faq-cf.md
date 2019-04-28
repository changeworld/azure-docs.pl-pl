---
title: Połączone rozwiązania fabryki często zadawane pytania — Azure | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące akcelerator rozwiązania połączonej fabryki
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: ed429d923cad2c715621990c146d4cf3a23e7bca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447934"
---
# <a name="frequently-asked-questions-for-connected-factory-solution-accelerator"></a>Często zadawane pytania dotyczące akcelerator rozwiązania połączonej fabryki

Zobacz też ogólne [— często zadawane pytania](iot-accelerators-faq.md) dla akceleratorów rozwiązań IoT.

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerator"></a>Gdzie znaleźć kod źródłowy dla akceleratora rozwiązań

Kod źródłowy jest przechowywane w repozytorium GitHub na następujący:

* [Akcelerator rozwiązań usługi połączonej fabryki](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>Co to jest OPC UA?

OPC Unified architektury (UA), wydanej w 2008 roku jest niezależny od platformy, zorientowane na usługę współdziałanie standardowych. OPC UA jest używany przez różne systemy przemysłowych i urządzeń, takich jak branży komputery, czujniki i układy logiczne. OPC UA integruje funkcjonalność specyfikacji OPC klasycznego jeden rozszerzalna struktura zawiera wbudowane zabezpieczenia. Jest to standardowy, który jest wymuszany przez organizację OPC Foundation. [Organizację OPC Foundation](https://opcfoundation.org/) jest organizacją not for zyski z ponad 440 elementów członkowskich. Celem organizacji jest na potrzeby specyfikacji OPC w ułatwienia współdziałanie wielu dostawców, dla wielu platform, bezpieczny i niezawodny przy użyciu:

* Infrastruktura
* Specyfikacje
* Technologia
* Procesy

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-solution-accelerator"></a>Dlaczego Microsoft został wybrany OPC UA dla akceleratora rozwiązania połączonej fabryki?

Microsoft wybrała OPC UA, ponieważ jest standardem otwartym, niezastrzeżonych, platformy niezależnie od branży i sprawdzone. To wymaganie Industrie 4.0 (RAMI4.0) odwołanie do architektury rozwiązań, zapewnienie współdziałania między szeroką gamę procesów produkcyjnych i urządzeń. Uruchomiono żądanie od swoich klientów do tworzenia rozwiązań Industrie 4.0. Obsługa OPC UA pomaga zmniejszyć barierę dla klientów osiągnąć swoje cele i zapewnia natychmiastowe wymierne efekty do nich.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Jak dodać publiczny adres IP na symulację maszyny Wirtualnej?

Masz dwie opcje, aby dodać adres IP:

* Użyj skryptu programu PowerShell `Simulation/Factory/Add-SimulationPublicIp.ps1` w [repozytorium](https://github.com/Azure/azure-iot-connected-factory). Przekaż nazwę wdrożenia, jako parametr. W przypadku wdrożenia lokalnego, użyj `<your username>ConnFactoryLocal`. Skrypt do drukowania adres IP maszyny Wirtualnej.

* W witrynie Azure portal Znajdź grupę zasobów wdrożenia. Z wyjątkiem lokalne wdrożenie grupy zasobów ma nazwę określoną jako rozwiązanie lub nazwa wdrożenia. Lokalne wdrażanie przy użyciu skryptu kompilacji, nazwę grupy zasobów jest `<your username>ConnFactoryLocal`. Teraz Dodaj nową **publiczny adres IP** zasobów w grupie zasobów.

> [!NOTE]
> W obu przypadkach upewnij się, należy zainstalować najnowsze poprawki zgodnie z instrukcjami [witryny sieci Web w systemie Ubuntu](https://wiki.ubuntu.com/Security/Upgrades). Aktualizowanie instalacji dla tak długo, jak Twoja maszyna wirtualna jest dostępna za pośrednictwem publicznego adresu IP.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Jak usunąć publiczny adres IP na symulację maszyny Wirtualnej?

Masz dwie opcje, aby usunąć adres IP:

* Użyj programu PowerShell script Simulation/Factory/Remove-SimulationPublicIp.ps1 z [repozytorium](https://github.com/Azure/azure-iot-connected-factory). Przekaż nazwę wdrożenia, jako parametr. W przypadku wdrożenia lokalnego, użyj `<your username>ConnFactoryLocal`. Skrypt do drukowania adres IP maszyny Wirtualnej.

* W witrynie Azure portal Znajdź grupę zasobów wdrożenia. Z wyjątkiem lokalne wdrożenie grupy zasobów ma nazwę określoną jako rozwiązanie lub nazwa wdrożenia. Lokalne wdrażanie przy użyciu skryptu kompilacji, nazwę grupy zasobów jest `<your username>ConnFactoryLocal`. Teraz usunąć **publiczny adres IP** zasobu z grupy zasobów.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Jak Zaloguj się do symulacji maszyny Wirtualnej?

Logowanie do symulacji maszyny Wirtualnej jest obsługiwana tylko jeśli wdrożono rozwiązania przy użyciu skryptu programu PowerShell `build.ps1` w [repozytorium](https://github.com/Azure/azure-iot-connected-factory).

Jeśli wdrożono rozwiązanie z www.azureiotsolutions.com, nie możesz się zarejestrować do maszyny Wirtualnej. Nie można zalogować się, ponieważ hasło jest generowany losowo i nie można zresetować je.

1. Dodaj publiczny adres IP do maszyny Wirtualnej. Zobacz [jak dodać publiczny adres IP do symulacji maszyny Wirtualnej?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Utwórz sesję SSH z maszyną wirtualną przy użyciu adresu IP maszyny wirtualnej.
1. Nazwa użytkownika do użycia: `docker`.
1. Hasło do użycia zależy od wersji, używane do wdrożenia:
    * W przypadku rozwiązań wdrożonych za pomocą skryptu build.ps1 przed 1 czerwca 2017 roku, hasło jest: `Passw0rd`.
    * W przypadku rozwiązań wdrożonych przy użyciu skryptu build.ps1 po 1 czerwca 2017, możesz znaleźć hasło w `<name of your deployment>.config.user` pliku. Hasło jest przechowywane w **VmAdminPassword** ustawienie. Hasło jest generowany losowo w czasie wdrażania, chyba że określisz go za pomocą `build.ps1` parametru skryptu `-VmAdminPassword`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Jak zatrzymać i uruchomić wszystkie procesy platformy docker w symulacji maszyny Wirtualnej?

1. Zaloguj się do symulacji maszyny Wirtualnej. Zobacz [jak zalogować się do symulacji maszyny Wirtualnej?](#how-do-i-sign-in-to-the-simulation-vm)
1. Aby sprawdzić, kontenery, które są aktywne, uruchom: `docker ps`.
1. Aby zatrzymać wszystkie kontenery symulacji, uruchom: `./stopsimulation`.
1. Aby uruchomić wszystkie kontenery symulacji:
    * Eksportowanie z nazwą zmiennej powłoki **IOTHUB_CONNECTIONSTRING**. Użyj wartości **IotHubOwnerConnectionString** ustawienie w `<name of your deployment>.config.user` pliku. Na przykład:

        ```sh
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Uruchom polecenie `./startsimulation`.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Jak zaktualizować symulacji na maszynie wirtualnej?

Jeśli wszystkie zmiany zostały wprowadzone na symulację, możesz użyć skryptu programu PowerShell `build.ps1` w [repozytorium](https://github.com/Azure/azure-iot-connected-factory) przy użyciu `updatedimulation` polecenia. Ten skrypt tworzy wszystkie składniki symulacji, zatrzymuje symulacji na maszynie wirtualnej, przekazuje, instaluje i uruchamia je.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Jak sprawdzić parametry połączenia Centrum IoT używanego przez rozwiązanie?

Jeśli wdrożono rozwiązanie za pomocą `build.ps1` skryptu w [repozytorium](https://github.com/Azure/azure-iot-connected-factory), ciąg połączenia jest wartością **IotHubOwnerConnectionString** w `<name of your deployment>.config.user` pliku.

Można również znaleźć parametry połączenia przy użyciu witryny Azure portal. W zasobie usługi IoT Hub w grupie zasobów w danym wdrożeniu Znajdź ustawienia parametrów połączenia.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Które urządzenia usługi IoT Hub używa symulacji połączonej fabryki?

Symulacja samodzielnie rejestruje następujące urządzenia:

* proxy.beijing.corp.contoso
* proxy.capetown.corp.contoso
* proxy.mumbai.corp.contoso
* proxy.munich0.corp.contoso
* proxy.rio.corp.contoso
* proxy.seattle.corp.contoso
* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Za pomocą [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) lub [rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension) narzędzie, można sprawdzić, które urządzenia są zarejestrowane w usłudze IoT hub, Twoje rozwiązanie używa. Aby użyć Eksplorator urządzeń, należy parametry połączenia dla Centrum IoT hub w danym wdrożeniu. Aby użyć rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure, konieczne jest nazwa Twojego Centrum IoT.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Jak uzyskać dane dzienników z składniki symulacji?

Wszystkie składniki symulacji rejestrowania informacji plikach dziennika. Te pliki znajdują się na maszynie wirtualnej w folderze `home/docker/Logs`. Aby pobrać dzienniki, można użyć skryptu programu PowerShell `Simulation/Factory/Get-SimulationLogs.ps1` w [repozytorium](https://github.com/Azure/azure-iot-connected-factory).

Ten skrypt musi zalogować się do maszyny Wirtualnej. Może być konieczne podanie poświadczeń logowania. Zobacz [jak zalogować się do symulacji maszyny Wirtualnej?](#how-do-i-sign-in-to-the-simulation-vm) można znaleźć poświadczenia.

Skrypt dodaje/usuwa publicznego adresu IP do maszyny Wirtualnej, jeśli nie ma jeszcze jeden i usuwa go. Skrypt umieszcza wszystkie pliki dziennika w archiwum i pobiera archiwum na deweloperskiej stacji roboczej.

Możesz też zalogować się do maszyny Wirtualnej za pośrednictwem protokołu SSH, a przeglądanie plików dziennika w czasie wykonywania.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Jak sprawdzić, jeśli Symulacja wysyła dane do chmury?

Za pomocą [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) lub [zdarzeń monitora rozszerzenie interfejsu wiersza polecenia usługi Azure IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-monitor-events) polecenia, można sprawdzić dane wysyłane do usługi IoT Hub z niektórych urządzeń. Aby użyć tych narzędzi, musisz znać parametry połączenia dla Centrum IoT hub w danym wdrożeniu. Zobacz [jak mogę znaleźć parametry połączenia Centrum IoT używanego przez rozwiązanie?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Sprawdzanie danych wysłanych przez jedno z urządzeń wydawcy:

* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Jeśli są wyświetlane żadne dane wysyłane do usługi IoT Hub, występuje problem z symulacji. Pierwszym krokiem analizy należy analizowanie plików dziennika składniki symulacji. Zobacz [jak mogę uzyskać dane dzienników z składniki symulacji?](#how-can-i-get-log-data-from-the-simulation-components) Następnie spróbuj zatrzymać i uruchomić symulację i jeśli nie ma jeszcze żadnych danych, wysyłane, należy zaktualizować symulacji całkowicie. Zobacz [jak zaktualizować symulacji na maszynie wirtualnej?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>Jak włączyć mapę interaktywną w mojej rozwiązania połączonej fabryki?

Aby włączyć mapę interaktywną w rozwiązaniu połączonej fabryki, musi mieć konto usługi Azure Maps.

W przypadku wdrażania z [www.azureiotsolutions.com](https://www.azureiotsolutions.com), proces wdrażania dodaje konto usługi Azure Maps grupę zasobów, która zawiera usługi akcelerator rozwiązań.

Podczas wdrażania za pomocą `build.ps1` skryptu w zestawie repozytorium GitHub połączona fabryka zmiennej środowiskowej `$env:MapApiQueryKey` w oknie kompilacji, aby [klucza konta usługi Azure Maps](../azure-maps/how-to-manage-account-keys.md). Interaktywnej mapie jest włączana automatycznie.

Klucz konta usługi Azure Maps można również dodać do Twój akcelerator rozwiązań, po wdrożeniu. Przejdź do witryny Azure portal i uzyskać dostęp do zasobu usługi App Service w danym wdrożeniu połączonej fabryki. Przejdź do **ustawienia aplikacji**, gdzie znaleźć sekcję **ustawienia aplikacji**. Ustaw **MapApiQueryKey** do [klucza konta usługi Azure Maps](../azure-maps/how-to-manage-account-keys.md). Zapisz ustawienia, a następnie przejdź do **Przegląd** i uruchom ponownie usługi App Service.

### <a name="how-do-i-create-an-azure-maps-account"></a>Jak utworzyć konto usługi Azure Maps?

Zobacz, [jak zarządzać swoim kontem usługi Azure Maps i klucze](../azure-maps/how-to-manage-account-keys.md).

### <a name="how-to-obtain-your-azure-maps-account-key"></a>Jak uzyskać klucz konta usługi Azure Maps

Zobacz, [jak zarządzać swoim kontem usługi Azure Maps i klucze](../azure-maps/how-to-manage-account-keys.md).

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>Jak włączyć interaktywnej mapie podczas lokalnego debugowania?

Aby włączyć interaktywnej mapie podczas lokalnego debugowania, ustaw wartość ustawienia `MapApiQueryKey` w plikach `local.user.config` i `<yourdeploymentname>.user.config` w katalogu głównym danego wdrożenia, wartość **QueryKey** skopiowane wcześniej.

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>Jak używać innego obrazu na stronie głównej Mój pulpit nawigacyjny?

Aby zmienić obraz statyczny wyświetlane we/wy strony głównej pulpitu nawigacyjnego i zamienić obraz `WebApp\Content\img\world.jpg`. Następnie należy ponownie skompilować i ponowne wdrażanie aplikacji internetowej.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>Jak używać urządzenia inne niż OPC UA z połączonej fabryki?

Do wysyłania danych telemetrycznych z innych OPC UA urządzeń do połączonej fabryki:

1. [Konfigurowanie nowej stacji w topologii połączonej fabryki](iot-accelerators-connected-factory-configure.md) w `ContosoTopologyDescription.json` pliku.

1. Odbieranie danych telemetrycznych w zgodnym formacie JSON połączonej fabryki:

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

1. Format `<timestamp>` jest: `2017-12-08T19:24:51.886753Z`

1. Uruchom ponownie usługę aplikacji połączonej fabryki.

### <a name="next-steps"></a>Kolejne kroki

Możesz także wypróbować niektóre inne funkcje i możliwości akceleratorów rozwiązań IoT:

* [Omówienie akceleratora rozwiązań do konserwacji zapobiegawczej](iot-accelerators-predictive-overview.md)
* [Wdrażanie akceleratora rozwiązania połączonej fabryki](quickstart-connected-factory-deploy.md)
* [Zabezpieczenia IoT od podstaw](/azure/iot-fundamentals/iot-security-ground-up)
