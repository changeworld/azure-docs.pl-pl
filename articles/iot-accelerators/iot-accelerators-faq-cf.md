---
title: Połączone fabryki rozwiązania często zadawane pytania — Azure | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące akcelerator rozwiązań połączone fabryki
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 4ed0cd413480e717e686f7e52123102e1a838f19
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="frequently-asked-questions-for-connected-factory-solution-accelerator"></a>Często zadawane pytania dotyczące akcelerator rozwiązań połączone fabryki

Zobacz też ogólne [— często zadawane pytania](iot-accelerators-faq.md) dla akceleratorów rozwiązania IoT.

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerator"></a>Gdzie można znaleźć kodu źródłowego dla akcelerator rozwiązań?

Kod źródłowy jest przechowywane w następujących repozytorium GitHub:

* [Połączone akcelerator rozwiązań fabryki](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>Co to jest OPC UA?

OPC Ujednolicona architektura (UA), wydane w ramach 2008, jest niezależne od platformy, zorientowane na usługę współdziałanie standardowa. OPC UA jest używany przez różnych systemów przemysłowych i urządzeń, takich jak czujniki branży komputerów i układy logiczne. OPC UA integruje funkcjonalność OPC klasycznego specyfikacji jeden rozszerzalne środowisko z wbudowanych zabezpieczeń. Jest to standardowy, który jest wymuszany przez OPC Foundation. [OPC Foundation](http://opcfoundation.org/) jest organizacją nie dla zysków z więcej niż 440 elementów członkowskich. Celem organizacji jest stosować specyfikacje OPC ułatwia współdziałanie wielu dostawców, obejmującego wiele platform, bezpieczny i niezawodny przy użyciu:

* Infrastruktura
* Specyfikacje
* Technologia
* Procesy

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-solution-accelerator"></a>Dlaczego Microsoft został wybrany OPC UA dla fabryki połączone akcelerator rozwiązań?

Microsoft postanowiono OPC UA, ponieważ jest to platforma otwarty, niezastrzeżonych, standard niezależne, branży i sprawdzone. Jest wymagane dla rozwiązań architektura odwołanie Industrie 4.0 (RAMI4.0) zapewniające współdziałanie szeroką gamę wytwarzania i urządzeń. Microsoft widzi żądanie z klientów do tworzenia rozwiązań Industrie 4.0. Obsługa OPC UA pomaga zmniejszyć bariery dla klientów do ich celach i udostępnia natychmiastowe wartość do nich.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Jak dodać publicznego adresu IP na symulacyjnych maszyny Wirtualnej?

Dostępne są dwie opcje, aby dodać adres IP:

* Użyj skryptu programu PowerShell `Simulation/Factory/Add-SimulationPublicIp.ps1` w [repozytorium](https://github.com/Azure/azure-iot-connected-factory). Podaj nazwę wdrożenia jako parametr. Dla wdrożenia lokalnego, należy użyć `<your username>ConnFactoryLocal`. Skrypt do drukowania adres IP maszyny wirtualnej.

* W portalu Azure Znajdź wdrożenia grupy zasobów. Z wyjątkiem wdrożenia lokalnego grupa zasobów ma nazwę określone jako rozwiązanie lub wdrożenia. W przypadku wdrożenia lokalnego przy użyciu skryptu kompilacji Nazwa grupy zasobów jest `<your username>ConnFactoryLocal`. Teraz Dodaj nową **publicznego adresu IP** zasobów do grupy zasobów.

> [!NOTE]
> W obu przypadkach upewnij się, zainstaluj najnowsze poprawki zgodnie z instrukcjami [Ubuntu witryny sieci Web](https://wiki.ubuntu.com/Security/Upgrades). Aktualizowanie instalacji dla tak długo, jak długo maszyny Wirtualnej jest dostępna za pośrednictwem publicznego adresu IP.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Jak usunąć publicznego adresu IP na symulacyjnych maszyny Wirtualnej?

Dostępne są dwie opcje, aby usunąć adres IP:

* Użyj programu PowerShell skryptu Simulation/Factory/Remove-SimulationPublicIp.ps1 z [repozytorium](https://github.com/Azure/azure-iot-connected-factory). Podaj nazwę wdrożenia jako parametr. Dla wdrożenia lokalnego, należy użyć `<your username>ConnFactoryLocal`. Skrypt do drukowania adres IP maszyny wirtualnej.

* W portalu Azure Znajdź wdrożenia grupy zasobów. Z wyjątkiem wdrożenia lokalnego grupa zasobów ma nazwę określone jako rozwiązanie lub wdrożenia. W przypadku wdrożenia lokalnego przy użyciu skryptu kompilacji Nazwa grupy zasobów jest `<your username>ConnFactoryLocal`. Teraz usunąć **publicznego adresu IP** zasobu z grupy zasobów.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Jak zalogować się na symulacyjnych maszyny Wirtualnej?

Logowanie do symulacji maszyny Wirtualnej jest obsługiwana tylko jeśli wdrożono rozwiązanie przy użyciu skryptu środowiska PowerShell `build.ps1` w [repozytorium](https://github.com/Azure/azure-iot-connected-factory).

Jeśli wdrożono rozwiązanie z www.azureiotsuite.com nie logowania się do maszyny Wirtualnej. Nie można zalogować się, ponieważ hasło jest generowany losowo i nie można zresetować go.

1. Dodaj publiczny adres IP do maszyny Wirtualnej. Zobacz [jak dodać publicznego adresu IP na symulacyjnych maszyny Wirtualnej?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Tworzenie sesji SSH maszyny Wirtualnej przy użyciu adresu IP maszyny wirtualnej.
1. Nazwa użytkownika do użycia: `docker`.
1. Hasło do użycia zależy od wersji, który został użyty do wdrożenia:
    * Wdrożenie za pomocą skryptu build.ps1 przed 1 czerwca 2017 rozwiązań, jest hasło: `Passw0rd`.
    * Wdrożenie za pomocą skryptu build.ps1 po 1 czerwca 2017 rozwiązania, można znaleźć hasło w `<name of your deployment>.config.user` pliku. Hasło jest zapisywane w **VmAdminPassword** ustawienie. Hasło jest generowane losowo w czasie wdrażania chyba że zostanie określony za pomocą `build.ps1` skryptu parametru `-VmAdminPassword`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Jak zatrzymać i uruchomić wszystkie procesy docker w symulacji maszyny Wirtualnej?

1. Zaloguj się na symulacyjnych maszyny Wirtualnej. Zobacz [jak zarejestrować się celu symulacji maszyny Wirtualnej?](#how-do-i-sign-in-to-the-simulation-vm)
1. Aby sprawdzić, kontenery, które są aktywne, uruchom: `docker ps`.
1. Aby zatrzymać wszystkie kontenery symulacji, uruchom: `./stopsimulation`.
1. Aby uruchomić wszystkie kontenery symulacji:
    * Eksportuj powłoki zmiennej o nazwie **IOTHUB_CONNECTIONSTRING**. Należy użyć wartości **IotHubOwnerConnectionString** w `<name of your deployment>.config.user` pliku. Na przykład:

        ```
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Uruchom polecenie `./startsimulation`.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Jak zaktualizować symulacji w maszynie Wirtualnej?

Jeśli wprowadzono zmiany do symulacji, możesz użyć skryptu programu PowerShell `build.ps1` w [repozytorium](https://github.com/Azure/azure-iot-connected-factory) przy użyciu `updatedimulation` polecenia. Ten skrypt tworzy wszystkie składniki symulacji, zatrzymuje symulacji w maszynie Wirtualnej przekazuje, instaluje i można je uruchomić.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Jak sprawdzić parametry połączenia używane przez Moje rozwiązanie Centrum IoT

Jeśli wdrożono rozwiązanie z `build.ps1` skryptu w [repozytorium](https://github.com/Azure/azure-iot-connected-factory), ciąg połączenia jest wartość **IotHubOwnerConnectionString** w `<name of your deployment>.config.user` pliku.

Można również znaleźć parametrów połączenia przy użyciu portalu Azure. W zasobie Centrum IoT w grupie zasobów wdrożenia zlokalizuj ustawień parametrów połączenia.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Centrum IoT urządzeń, które używa symulacji fabryka połączenia?

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

Przy użyciu [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) lub [rozszerzenie IoT Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension) narzędzia, można sprawdzić, które urządzenia są zarejestrowane w usłudze Centrum IoT używa rozwiązania. Za pomocą Eksploratora urządzenia, należy parametry połączenia dla Centrum IoT w danym wdrożeniu. Aby użyć rozszerzenia IoT Azure CLI 2.0, należy nazwę Centrum IoT.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Jak uzyskać dane dzienników ze składników symulacji

Wszystkie składniki w symulacji rejestrowania informacji plikach dziennika. Te pliki znajdują się w maszynie Wirtualnej w folderze `home/docker/Logs`. Aby pobrać dzienniki, można użyć skryptu programu PowerShell `Simulation/Factory/Get-SimulationLogs.ps1` w [repozytorium](https://github.com/Azure/azure-iot-connected-factory).

Ten skrypt należy zalogować się do maszyny Wirtualnej. Może być konieczne podanie poświadczeń przy logowaniu. Zobacz [jak zarejestrować się celu symulacji maszyny Wirtualnej?](#how-do-i-sign-in-to-the-simulation-vm) można znaleźć poświadczenia.

Skrypt dodaje i usuwa publiczny adres IP do maszyny Wirtualnej, jeśli jeszcze nie ma jeden i usuwa go. Skrypt umieszcza wszystkie pliki dziennika w archiwum i pobierane pliki archiwum na deweloperskiej stacji roboczej.

Możesz też zalogować się do maszyny Wirtualnej za pośrednictwem SSH i sprawdzić pliki dziennika w czasie wykonywania.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Jak można sprawdzić, czy symulacji wysyła dane w chmurze?

Z [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) lub [explorer Centrum iothub](https://github.com/azure/iothub-explorer) narzędzia, możesz sprawdzić dane wysyłane do Centrum IoT z określonymi urządzeniami. Aby korzystać z tych narzędzi, musisz wiedzieć, ciąg połączenia dla Centrum IoT w danym wdrożeniu. Zobacz [jak sprawdzić parametry połączenia Centrum IoT używane przez Moje rozwiązanie?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Sprawdź dane wysyłane przez jednego z urządzeń wydawcy:

* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Jeśli zostanie wyświetlone żadne dane wysyłane do Centrum IoT, oznacza to, że wystąpił problem z symulacji. Pierwszym krokiem analizy powinien analizowanie plików dziennika składników symulacji. Zobacz [jak można uzyskać danych dziennika z składniki symulacji?](#how-can-i-get-log-data-from-the-simulation-components) Spróbuj dalej zatrzymać i uruchomić symulacji i jeśli nie ma jeszcze żadnych danych wysyłane, zaktualizuj symulacji całkowicie. Zobacz [jak zaktualizować symulacji w maszynie Wirtualnej?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>Jak włączyć mapy interakcyjnej w Moje rozwiązanie fabryka połączenia?

Aby włączyć mapy interakcyjnej w rozwiązaniu fabryki połączone, musi mieć istniejącego interfejsu API map Bing planu przedsiębiorstwa.

W przypadku wdrażania z [www.azureiotsuite.com](http://www.azureiotsuite.com), proces wdrażania sprawdza ma włączone interfejsu API map Bing Enterprise planu subskrypcji i automatycznie wdraża mapy interakcyjnej do fabryki połączony. Jeśli nie jest to możliwe, można włączyć mapy interakcyjnej we wdrożeniu w następujący sposób:

Podczas wdrażania przy użyciu `build.ps1` skryptów w witrynie GitHub fabryki połączone repozytorium i ma interfejsu API map Bing planu przedsiębiorstwa, ustaw zmienną środowiskową `$env:MapApiQueryKey` w oknie kompilacji, aby klucz zapytania planu. Mapy interakcyjnej jest włączana automatycznie.

Jeśli nie masz interfejsu API map Bing Enterprise planu wdrażania rozwiązania fabryki połączone z [www.azureiotsuite.com](http://www.azureiotsuite.com) lub przy użyciu `build.ps1` skryptu. Następnie dodaj interfejsu API map Bing Enterprise planu subskrypcji, zgodnie z objaśnieniem w [sposób utworzenia interfejsu API map Bing dla konta organizacji?](#how-do-i-create-a-bing-maps-api-for-enterprise-account). Wyszukiwanie klucza zapytania tego konta, zgodnie z objaśnieniem w [Uzyskiwanie interfejsu API map Bing dla przedsiębiorstwa QueryKey](#how-to-obtain-your-bing-maps-api-for-enterprise-querykey) i Zapisz ten klucz. Przejdź do portalu Azure i uzyskać dostęp do zasobu usługi aplikacji w danym wdrożeniu połączone fabryki. Przejdź do **ustawienia aplikacji**, gdzie można znaleźć sekcji **ustawień aplikacji**. Ustaw **MapApiQueryKey** klucza zapytania został uzyskany. Zapisz ustawienia, a następnie przejdź do **omówienie** i uruchom ponownie usługę aplikacji.

### <a name="how-do-i-create-a-bing-maps-api-for-enterprise-account"></a>Jak utworzyć interfejsu API map Bing dla konta organizacji

Możesz uzyskać bezpłatne *wewnętrzne transakcje poziom 1 mapy Bing dla przedsiębiorstwa* planu. Jednak można tylko dodać dwa te plany z subskrypcją platformy Azure. Jeśli nie masz interfejsu API map Bing dla konta organizacji, utworzyć w portalu Azure, klikając **+ Utwórz zasób**. Następnie wyszukaj **interfejsu API map Bing dla przedsiębiorstwa** i postępuj zgodnie z monitami, aby go utworzyć.

![Klucz usługi Bing](./media/iot-accelerators-faq-cf/bing.png)

### <a name="how-to-obtain-your-bing-maps-api-for-enterprise-querykey"></a>Jak uzyskać interfejsu API map Bing dla QueryKey przedsiębiorstwa

Po utworzeniu interfejsu API map Bing planu przedsiębiorstwa, należy dodać mapy Bing dla zasobów przedsiębiorstwa do grupy zasobów rozwiązania fabryki połączone w portalu Azure.

1. W portalu Azure przejdź do grupy zasobów, zawierającą interfejsu API map Bing planu przedsiębiorstwa.

1. Kliknij przycisk **wszystkie ustawienia**, następnie **zarządzanie kluczami**.

1. Dostępne są dwa klucze: **umożliwia** i **QueryKey**. Kopiuj **QueryKey** wartości.

1. Do pobrania przez klucz `build.ps1` skryptów, ustaw zmienną środowiskową `$env:MapApiQueryKey` w środowisku PowerShell w celu **QueryKey** planu. Skrypt kompilacji następnie automatycznie dodaje wartość do ustawienia usługi App Service.

1. Uruchom lokalnie lub w chmurze przy użyciu wdrożenia `build.ps1` skryptu.

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>Jak włączyć mapy interakcyjnej przy debugowaniu lokalnym?

Aby włączyć interakcyjne mapy, podczas lokalnego debugowania, ustaw wartość ustawienia `MapApiQueryKey` w plikach `local.user.config` i `<yourdeploymentname>.user.config` w katalogu wdrożenia z wartością **QueryKey** skopiowane wcześniej.

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>Jak używać innego obrazu na stronie głównej Mój pulpit nawigacyjny?

Aby zmienić statyczny obraz wyświetlany we/wy strony głównej pulpitu nawigacyjnego, Zamień obraz `WebApp\Content\img\world.jpg`. Następnie ponowne skompilowanie i wdrożenie aplikacji sieci Web.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>Jak używać urządzeń z systemem innym niż UA OPC z fabryką połączenia?

Do wysyłania danych telemetrycznych z innych niż UA OPC urządzeń do fabryki połączenia:

1. [Konfigurowanie nowej stacji w topologii połączone fabryki](iot-accelerators-connected-factory-configure.md) w `ContosoTopologyDescription.json` pliku.

1. Pozyskiwania danych telemetrii w formacie JSON zgodne fabryka połączenia:

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

1. Uruchom ponownie usługę fabryki połączonych aplikacji.

### <a name="next-steps"></a>Kolejne kroki

Możesz także wypróbować niektóre inne funkcje i możliwości akceleratorów rozwiązań IoT:

* [Omówienie akceleratora rozwiązań do konserwacji zapobiegawczej](../iot-suite/iot-suite-predictive-overview.md)
* [Akceleratora rozwiązań połączonych fabryki — omówienie](iot-accelerators-connected-factory-overview.md)
* [Zabezpieczenia IoT od podstaw](../iot-suite/securing-iot-ground-up.md)
