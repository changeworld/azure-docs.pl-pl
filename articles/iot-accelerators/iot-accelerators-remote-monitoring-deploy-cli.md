---
title: Wdrażanie rozwiązania monitorowania zdalnego Java — Azure | Dokumentacja firmy Microsoft
description: W tym samouczku dowiesz się, jak wykonać aprowizację rozwiązania monitorowania zdalnego akceleratora przy użyciu interfejsu wiersza polecenia.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: dd696330c9ee78ef84ac9fcf85946c837ad5b824
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188022"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>Wdrażanie akceleratora rozwiązania monitorowania zdalnego przy użyciu interfejsu wiersza polecenia

Ten samouczek pokazuje, jak aprowizowanie akceleratora rozwiązania monitorowania zdalnego. Możesz wdrożyć rozwiązanie za pomocą interfejsu wiersza polecenia. Można także wdrożyć rozwiązanie za pomocą interfejsu użytkownika opartego na sieci web w witrynie azureiotsuite.com, aby dowiedzieć się więcej na temat tej opcji, zobacz [wdrażanie akceleratora rozwiązania monitorowania zdalnego](quickstart-remote-monitoring-deploy.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wdrożyć akcelerator rozwiązań monitorowania zdalnego, konieczne jest aktywna subskrypcja platformy Azure.

Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](http://azure.microsoft.com/pricing/free-trial/).

Aby uruchomić interfejs wiersza polecenia, musisz mieć [Node.js](https://nodejs.org/) zainstalowane na komputerze lokalnym.

## <a name="install-the-cli"></a>Instalowanie interfejsu wiersza polecenia

Aby zainstalować interfejs wiersza polecenia, uruchom następujące polecenie w środowisku wiersza polecenia:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Zaloguj się do interfejsu wiersza polecenia

Zanim będzie można wdrożyć akcelerator rozwiązań, musisz zarejestrować się do subskrypcji platformy Azure przy użyciu interfejsu wiersza polecenia w następujący sposób:

```cmd/sh
pcs login
```

Postępuj zgodnie z wyświetlanymi instrukcjami, aby ukończyć procesu logowania.

## <a name="deployment-options"></a>Opcje wdrożenia

Podczas wdrażania akcelerator rozwiązań, dostępnych jest kilka opcji, w wyniku skonfigurowanych przez proces wdrażania:

| Opcja | Wartości | Opis |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | A _podstawowe_ wdrożenia jest przeznaczony dla testu i pokazów, jego wszystkie mikrousługi są wdrażane na pojedynczej maszyny wirtualnej. A _standardowa_ wdrożenia jest przeznaczony dla środowiska produkcyjnego, jego mikrousługi są wdrażane na wielu maszynach wirtualnych. A _lokalnego_ wdrożenia konfiguruje kontener platformy Docker, aby uruchomić mikrousług na komputerze lokalnym i korzysta z usług platformy Azure, takie jak storage i Cosmos DB w chmurze. |
| Środowisko uruchomieniowe | `dotnet`, `java` | Wybiera implementacji języka mikrousług. |

Aby dowiedzieć się więcej o tym, jak przy użyciu lokalnego wdrażania, zobacz [uruchamiane lokalnie rozwiązania do zdalnego monitorowania](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Running-the-Remote-Monitoring-Solution-Locally#deploy-azure-services-and-set-environment-variables).

## <a name="basic-vs-standard-deployments"></a>Podstawowe vs. Wdrożeń w warstwie standardowa

### <a name="basic"></a>Podstawowa
Podstawowe wdrożenie jest skierowana do zaprezentować rozwiązania. Aby zmniejszyć koszt tego pokazu, wszystkie mikrousługi są wdrażane na jednej maszynie wirtualnej; nie uznaje architektury gotowe do produkcji.

Opcja nasze standardowe wdrożenie powinna być używana w przypadku, gdy jesteś gotowy do dostosowywania architektury gotowe do produkcji, stworzona z myślą o skalowania i rozszerzalności.

Tworzenie podstawowego rozwiązania spowoduje następujących usług platformy Azure aprowizowane w Twojej subskrypcji platformy Azure, opłat: 

| Licznik | Zasób                       | Typ         | Używane dla |
|-------|--------------------------------|--------------|----------|
| 1     | [Maszyny wirtualnej systemu Linux](https://azure.microsoft.com/services/virtual-machines/) | Standardowa D1, wersja 2  | Mikrousługi hostingu |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                  | S1 — warstwa standardowa | Zarządzanie urządzeniami i komunikacja |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | Standardowa (Standard)        | Przechowywanie danych konfiguracji i telemetrii urządzenia, takie jak reguły, alarmy i wiadomości |  
| 1     | [Konto usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | Standardowa (Standard)        | Magazyn dla maszyny Wirtualnej i przesyłania strumieniowego punkty kontrolne |
| 1     | [Aplikacja sieci Web](https://azure.microsoft.com/services/app-service/web/)        |                 | Hosting aplikacji frontonu sieci web |

### <a name="standard"></a>Standardowa (Standard)
Standardowe wdrożenie to wdrożenie gotowe do produkcji Deweloper można dostosować i rozszerzyć do własnych potrzeb. Niezawodności i skali mikrousługi aplikacji są kompilowane jako kontenery platformy Docker i wdrażane za pomocą programu orchestrator ([Kubernetes](https://kubernetes.io/) domyślnie). Program orchestrator jest odpowiedzialny za wdrażania, skalowania i zarządzania aplikacji.

Tworzenie standardowego rozwiązania spowoduje następujących usług platformy Azure aprowizowane w Twojej subskrypcji platformy Azure, opłat:

| Licznik | Zasób                                     | Jednostka SKU / rozmiar      | Używane dla |
|-------|----------------------------------------------|-----------------|----------|
| 4     | [Maszyny wirtualne z systemem Linux](https://azure.microsoft.com/services/virtual-machines/)   | Standardowa D2, wersja 2  | wzorzec 1 i 3 agentów do hostowania mikrousług z nadmiarowością |
| 1     | [Usługa Azure Container Service](https://azure.microsoft.com/services/container-service/) |                 | [Kubernetes](https://kubernetes.io) programu orchestrator |
| 1     | [Usługa azure IoT Hub] [https://azure.microsoft.com/services/iot-hub/]                     | S2 — warstwa standardowa | Zarządzanie urządzeniami, poleceń i kontroli |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Standardowa (Standard)        | Przechowywanie danych konfiguracji i telemetrii urządzenia, takie jak reguły, alarmy i wiadomości |
| 5     | [Konta usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Standardowa (Standard)        | 4 dla magazynu maszyn wirtualnych i 1 dla przesyłania strumieniowego punkty kontrolne |
| 1     | [App Service](https://azure.microsoft.com/services/app-service/web/)             | Standardowa S1     | Usługa Application gateway, za pośrednictwem protokołu SSL |

> Informacje o cenach dla tych usług można znaleźć [tutaj](https://azure.microsoft.com/pricing). Ilości użycia i szczegółów rozliczeń dla subskrypcji można znaleźć w [witryny Azure Portal](https://portal.azure.com/).

## <a name="deploy-the-solution-accelerator"></a>Wdrażanie akceleratora rozwiązań

### <a name="example-deploy-net-version"></a>Przykład: wdrożenie wersji platformy .NET

Poniższy przykład pokazuje, jak wdrożyć podstawowa, wersja platformy .NET akceleratora rozwiązania monitorowania zdalnego:

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Przykład: wdrożenie wersji języka Java

Poniższy przykład pokazuje, jak wdrożyć w warstwie standardowa wersja w języku Java akceleratora rozwiązania monitorowania zdalnego:

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>Opcje polecenia komputerów

Po uruchomieniu `pcs` polecenie, aby wdrożyć to rozwiązanie, użytkownik zostanie zapytany o:

- Nazwa rozwiązania. Ta nazwa musi być unikatowa.
- Subskrypcja platformy Azure, która ma być używana.
- Lokalizacja.
- Poświadczenia dla maszyn wirtualnych hostujących mikrousług. Dostęp do maszyn wirtualnych w celu rozwiązywania problemów, można użyć tych poświadczeń.

Gdy `pcs` polecenie zostanie zakończone, wyświetla adres URL nowego wdrożenia akcelerator rozwiązań. `pcs` Polecenie tworzy również plik `{deployment-name}-output.json` o dodatkowe informacje, takie jak nazwa Centrum IoT, które zostało zaaprowizowane dla Ciebie.

Aby uzyskać więcej informacji na temat parametrów wiersza polecenia Uruchom polecenie:

```cmd/sh
pcs -h
```

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia, zobacz [sposób używania interfejsu wiersza polecenia](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie akceleratora rozwiązań
> * Wdrażanie akceleratora rozwiązań
> * Zaloguj się do akceleratora rozwiązań

Teraz, gdy wdrożono rozwiązanie monitorowania zdalnego, następnym krokiem jest [zapoznaj się z możliwościami pulpitu nawigacyjnego rozwiązania](./quickstart-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->
