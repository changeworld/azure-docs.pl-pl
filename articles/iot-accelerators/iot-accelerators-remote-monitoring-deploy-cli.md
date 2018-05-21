---
title: Wdrażanie zdalne monitorowanie rozwiązania - Azure Java | Dokumentacja firmy Microsoft
description: W tym samouczku przedstawiono sposób udostępnienia zdalnego akcelerator rozwiązań monitorowania przy użyciu interfejsu wiersza polecenia.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/29/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 3178d51cd2c04f3be8d4a6284a4f1635845def8c
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>Wdrażanie zdalne akcelerator rozwiązań monitorowania przy użyciu interfejsu wiersza polecenia

W tym samouczku przedstawiono sposób udostępnienia zdalnego monitorowania akcelerator rozwiązań. Możesz wdrożyć rozwiązanie przy użyciu interfejsu wiersza polecenia. Można także wdrożyć rozwiązanie przy użyciu interfejsu użytkownika sieci web na azureiotsuite.com, aby dowiedzieć się więcej o tej opcji, zobacz [wdrożyć zdalnego monitorowania akcelerator rozwiązań](iot-accelerators-remote-monitoring-deploy.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wdrożyć zdalnego akcelerator rozwiązań monitorowania, należy aktywną subskrypcją platformy Azure.

Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](http://azure.microsoft.com/pricing/free-trial/).

Aby uruchomić interfejsu wiersza polecenia, należy [Node.js](https://nodejs.org/) zainstalowany na komputerze lokalnym.

## <a name="install-the-cli"></a>Instalowanie interfejsu wiersza polecenia

Aby zainstalować interfejsu wiersza polecenia, uruchom następujące polecenie w wierszu polecenia środowiska:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Zaloguj się do interfejsu wiersza polecenia

Przed wdrożeniem akcelerator rozwiązań, musisz się zalogować do subskrypcji platformy Azure przy użyciu interfejsu wiersza polecenia w następujący sposób:

```cmd/sh
pcs login
```

Postępuj zgodnie z wyświetlanymi instrukcjami, aby ukończyć procesu logowania.

## <a name="deployment-options"></a>Opcje wdrażania

Podczas wdrażania akcelerator rozwiązań, dostępnych jest kilka opcji, które konfigurują procesu wdrażania:

| Opcja | Wartości | Opis |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | A _podstawowe_ wdrożenia jest przeznaczony dla testu i pokazów, wszystkie mikrousług jest wdrażana na jednej maszynie wirtualnej. A _standardowe_ wdrożenia jest przeznaczony dla środowiska produkcyjnego, mikrousług wdrażania wielu maszyn wirtualnych. A _lokalnego_ wdrożenia konfiguruje kontener Docker do uruchomienia mikrousług na komputerze lokalnym i używa usług Azure, takich jak magazyn i DB rozwiązania Cosmos w chmurze. |
| Środowisko uruchomieniowe | `dotnet`, `java` | Wybiera implementacja języka mikrousług. |

Aby dowiedzieć się więcej o sposobie używania lokalnego wdrożenia, zobacz [uruchomiony lokalnie zdalnego rozwiązanie monitorowania](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Running-the-Remote-Monitoring-Solution-Locally#deploy-azure-services-and-set-environment-variables).

## <a name="basic-vs-standard-deployments"></a>Podstawowe programu vs. Standardowa wdrożenia

### <a name="basic"></a>Podstawowa
Podstawy wdrażania jest skierowaną do prezentacji rozwiązania. Aby zmniejszyć koszt tej prezentacji, wszystkie mikrousług są wdrażane w jednej maszynie wirtualnej; to nie omówiono architekturę gotowe do produkcji.

Nasze opcji standardowego wdrożenia należy używać, gdy wszystko będzie gotowe dostosować to architektura gotowe do produkcji, przeznaczony dla skali i rozszerzalność.

Tworzenie podstawowego rozwiązania spowoduje wyświetlenie następujących usług platformy Azure aprowizowany do Twojej subskrypcji platformy Azure, kosztem: 

| Licznik | Zasób                       | Typ         | Używany do |
|-------|--------------------------------|--------------|----------|
| 1     | [Maszyny wirtualnej systemu Linux](https://azure.microsoft.com/services/virtual-machines/) | Standardowa D1 V2  | Hosting mikrousług |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                  | S1 — warstwa podstawowa | Zarządzanie urządzeniami i komunikacji |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | Standardowa (Standard)        | Przechowywanie danych konfiguracji i telemetrii urządzenia, takie jak zasady, alarmy i komunikatów |  
| 1     | [Konto magazynu Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | Standardowa (Standard)        | Magazyn dla maszyny Wirtualnej i przesyłania strumieniowego punkty kontrolne |
| 1     | [Aplikacja sieci Web](https://azure.microsoft.com/services/app-service/web/)        |                 | Hosting aplikacji frontonu sieci web |

### <a name="standard"></a>Standardowa (Standard)
Standardowe wdrożenie to wdrożenie gotowe do produkcji, deweloper może dostosowywanie i rozszerzanie do własnych potrzeb. Niezawodności i skalowania aplikacji mikrousług są tworzone jako kontenery Docker i wdrażane za pomocą usługi orchestrator ([Kubernetes](https://kubernetes.io/) domyślnie). Orchestrator jest odpowiedzialny za wdrożenie skalowania i zarządzania aplikacji.

Tworzenie standardowego rozwiązania spowoduje wyświetlenie następujących usług platformy Azure aprowizowany do Twojej subskrypcji platformy Azure, kosztem:

| Licznik | Zasób                                     | Jednostka SKU / rozmiaru      | Używany do |
|-------|----------------------------------------------|-----------------|----------|
| 4     | [Maszyny wirtualne z systemem Linux](https://azure.microsoft.com/services/virtual-machines/)   | Standardowa D2 V2  | wzorzec 1 i 3 agentów do hostowania mikrousług z nadmiarowością |
| 1     | [Usługa kontenera platformy Azure](https://azure.microsoft.com/services/container-service/) |                 | [Kubernetes](https://kubernetes.io) orchestrator |
| 1     | [Centrum IoT azure] [https://azure.microsoft.com/services/iot-hub/]                     | S1 — warstwa podstawowa | Zarządzanie urządzeniami, polecenie i kontroli |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Standardowa (Standard)        | Przechowywanie danych konfiguracji i telemetrii urządzenia, takie jak zasady, alarmy i komunikatów |
| 5     | [Konta magazynu platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Standardowa (Standard)        | 4 dla magazynu maszyny Wirtualnej, a 1 w przypadku przesyłania strumieniowego punkty kontrolne |
| 1     | [App Service](https://azure.microsoft.com/services/app-service/web/)             | Standardowa S1     | Brama aplikacji za pośrednictwem protokołu SSL |

> Informacje o cenach dla tych usług można znaleźć [tutaj](https://azure.microsoft.com/pricing). Wielkość użycia i szczegóły płatności dotyczące Twojej subskrypcji można znaleźć w [Azure Portal](https://portal.azure.com/).

## <a name="deploy-the-solution-accelerator"></a>Wdrażanie akcelerator rozwiązań

### <a name="example-deploy-net-version"></a>Przykład: wdrożenie wersji platformy .NET

Poniższy przykład przedstawia sposób wdrażania basic, wersja platformy .NET zdalnego monitorowania akcelerator rozwiązań:

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Przykład: wdrożenie wersji języka Java

Poniższy przykład przedstawia sposób wdrażania standard, Java wersję zdalnego monitorowania akcelerator rozwiązań:

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>Opcje polecenia komputerów

Po uruchomieniu `pcs` polecenie, aby wdrożyć rozwiązanie, pojawi się monit dla:

- Nazwa rozwiązania. Ta nazwa musi być unikatowa.
- Subskrypcja platformy Azure, która ma być używana.
- Lokalizacja.
- Poświadczenia dla maszyn wirtualnych hostujących mikrousług. Można używać tych poświadczeń, uzyskiwać dostęp do maszyn wirtualnych do rozwiązywania problemów.

Gdy `pcs` polecenia zakończeniu on Wyświetla adres URL nowego wdrożenia akceleratora rozwiązań. `pcs` Polecenie powoduje utworzenie pliku `{deployment-name}-output.json` informacje dodatkowe, takie jak nazwa Centrum IoT udostępniony dla Ciebie.

Aby uzyskać więcej informacji na temat parametrów wiersza polecenia Uruchom polecenie:

```cmd/sh
pcs -h
```

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia, zobacz [sposób użycia interfejsu wiersza polecenia](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj akcelerator rozwiązań
> * Wdrażanie akcelerator rozwiązań
> * Zaloguj się do akcelerator rozwiązań

Teraz, gdy wdrożono rozwiązanie monitorowania zdalnego następnym krokiem jest [Poznaj możliwości pulpit nawigacyjny rozwiązania](./iot-accelerators-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->