---
title: Wdrażanie rozwiązania do zdalnego monitorowania przy użyciu interfejsu wiersza polecenia — Azure | Dokumentacja firmy Microsoft
description: Ten poradnik pokazuje, jak aprowizowanie akceleratora rozwiązania monitorowania zdalnego przy użyciu interfejsu wiersza polecenia.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: ea96b2b996ea79efacdcda50c6370f25e26e0aa2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58793779"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>Wdrażanie akceleratora rozwiązania monitorowania zdalnego przy użyciu interfejsu wiersza polecenia

Ten poradnik pokazuje, jak wdrożyć akcelerator rozwiązań monitorowania zdalnego. Możesz wdrożyć rozwiązanie za pomocą interfejsu wiersza polecenia. Można także wdrożyć rozwiązanie za pomocą interfejsu użytkownika opartego na sieci web w azureiotsolutions.com, aby dowiedzieć się więcej na temat tej opcji, zobacz [wdrażanie akceleratora rozwiązania monitorowania zdalnego](quickstart-remote-monitoring-deploy.md) Szybki Start.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wdrożyć akcelerator rozwiązań monitorowania zdalnego, konieczne jest aktywna subskrypcja platformy Azure.

Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

Aby uruchomić interfejs wiersza polecenia, musisz mieć [Node.js](https://nodejs.org/) zainstalowane na komputerze lokalnym.

## <a name="install-the-cli"></a>Instalowanie interfejsu wiersza polecenia

Aby zainstalować interfejs wiersza polecenia, uruchom następujące polecenie w środowisku wiersza polecenia:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Zaloguj się do interfejsu wiersza polecenia

Zanim będzie można wdrożyć akcelerator rozwiązań, musisz zarejestrować się do subskrypcji platformy Azure przy użyciu interfejsu wiersza polecenia:

```cmd/sh
pcs login
```

Postępuj zgodnie z wyświetlanymi instrukcjami, aby ukończyć procesu logowania.

## <a name="deployment-options"></a>Opcje wdrożenia

Podczas wdrażania akcelerator rozwiązań, dostępnych jest kilka opcji, w wyniku skonfigurowanych przez proces wdrażania:

| Opcja | Wartości | Opis |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | A _podstawowe_ wdrożenia jest przeznaczony dla testu i pokazów, jego wszystkie mikrousługi są wdrażane na pojedynczej maszyny wirtualnej. A _standardowa_ wdrożenia jest przeznaczony dla środowiska produkcyjnego, jego mikrousługi są wdrażane na wielu maszynach wirtualnych. A _lokalnego_ wdrożenia konfiguruje kontener platformy Docker, aby uruchomić mikrousług na komputerze lokalnym i korzysta z usług Azure cloud services, takich jak storage i Cosmos DB. |
| Środowisko uruchomieniowe | `dotnet`, `java` | Wybiera implementacji języka mikrousług. |

Aby dowiedzieć się, jak korzystać z opcji wdrażania lokalnego, zobacz [uruchamiane lokalnie rozwiązania do zdalnego monitorowania](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="basic-and-standard-deployments"></a>Podstawowa i standardowa wdrożeń

Ta sekcja zawiera podsumowanie najważniejszych różnic między wdrożenia podstawowa i standardowa.

### <a name="basic"></a>Podstawowa

Możecie podstawowego wdrożenia z [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) lub przy użyciu interfejsu wiersza polecenia.

Wdrożenie w warstwie Podstawowa to wdrożenie ukierunkowane na prezentowanie rozwiązania. Aby zmniejszyć koszty, wszystkie mikrousługi są wdrażane na jednej maszynie wirtualnej. To wdrożenie nie używa architektury gotowe do produkcji.

Podstawowe wdrożenie tworzy następujące usługi w subskrypcji platformy Azure:

| Licznik | Zasób                       | Typ         | Używane dla |
|-------|--------------------------------|--------------|----------|
| 1     | [Maszyny wirtualnej systemu Linux](https://azure.microsoft.com/services/virtual-machines/) | Standardowa D1, wersja 2  | Mikrousługi hostingu |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                  | S1 — warstwa standardowa | Zarządzanie urządzeniami i komunikacja |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | Standardowa (Standard)        | Przechowywanie danych konfiguracji, zasad, alertów i innych zimnego magazynu |  
| 1     | [Konto usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | Standardowa (Standard)        | Magazyn dla maszyny Wirtualnej i przesyłania strumieniowego punkty kontrolne |
| 1     | [Aplikacja sieci Web](https://azure.microsoft.com/services/app-service/web/)        |                 | Hosting aplikacji frontonu sieci web |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Zarządzanie tożsamościami użytkowników i zabezpieczeń |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standardowa (Standard)                | Wyświetlanie lokalizacji zawartości |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 jednostki              | Włączanie analizy w czasie rzeczywistym |
| 1     | [Usługa Azure Device Provisioning](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Inicjowanie obsługi administracyjnej urządzeń na dużą skalę |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 — 1 jednostka              | Magazyn dla analizy telemetrii rozszerzony dane i umożliwia wiadomości |

### <a name="standard"></a>Standardowa (Standard)

Możesz tworzyć standardowe wdrożenie tylko przy użyciu interfejsu wiersza polecenia.

Standardowe wdrożenie to wdrożenie gotowe do produkcji, który deweloper można dostosować i rozszerzyć. Użyj opcji standardowego wdrożenia, gdy wszystko będzie gotowe do dostosowywania architektury gotowe do produkcji, stworzona z myślą o skalowania i rozszerzalności. Mikrousługi aplikacji są kompilowane jako kontenery platformy Docker i wdrażane za pomocą usługi Azure Kubernetes Service. Koordynatora Kubernetes wdraża, którą można skalować, mikrousługi i zarządza.

Standardowe wdrożenie tworzy następujące usługi w subskrypcji platformy Azure:

| Licznik | Zasób                                     | Jednostka SKU / rozmiar      | Używane dla |
|-------|----------------------------------------------|-----------------|----------|
| 1     | [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service)| Użyj w pełni zarządzanej usługi organizowania kontenerów Kubernetes, wartość domyślna to 3 agentów|
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                     | S2 — warstwa standardowa | Zarządzanie urządzeniami, poleceń i kontroli |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Standardowa (Standard)        | Przechowywanie danych konfiguracji i danych telemetrycznych z urządzenia, takie jak reguły, alerty i komunikaty |
| 5     | [Konta usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Standardowa (Standard)        | 4 dla magazynu maszyn wirtualnych i 1 dla przesyłania strumieniowego punkty kontrolne |
| 1     | [App Service](https://azure.microsoft.com/services/app-service/web/)             | Standardowa S1     | Usługa Application gateway, za pośrednictwem protokołu SSL |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Zarządzanie tożsamościami użytkowników i zabezpieczeń |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standardowa (Standard)                | Wyświetlanie lokalizacji zawartości |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 jednostki              | Włączanie analizy w czasie rzeczywistym |
| 1     | [Usługa Azure Device Provisioning](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Inicjowanie obsługi administracyjnej urządzeń na dużą skalę |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 — 1 jednostka              | Magazyn dla analizy telemetrii rozszerzony dane i umożliwia wiadomości |

> [!NOTE]
> Można znaleźć informacje o tych usług w cenach [ https://azure.microsoft.com/pricing ](https://azure.microsoft.com/pricing). Można znaleźć szczegółowe informacje dla Twojej subskrypcji w rozliczeń i zużycia [witryny Azure Portal](https://portal.azure.com/).

## <a name="deploy-the-solution-accelerator"></a>Wdrażanie akceleratora rozwiązań

Przykłady wdrożeń:

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

Po uruchomieniu `pcs` polecenie, aby wdrożyć to rozwiązanie, pojawi się prośba dla:

- Nazwa rozwiązania. Ta nazwa musi być unikatowa.
- Subskrypcja platformy Azure, która ma być używana.
- Lokalizacja.
- Poświadczenia dla maszyn wirtualnych hostujących mikrousług. Dostęp do maszyn wirtualnych w celu rozwiązywania problemów, można użyć tych poświadczeń.

Gdy `pcs` polecenie zostanie zakończone, wyświetla adres URL Twój nowy akcelerator rozwiązań. `pcs` Polecenie tworzy również plik `{deployment-name}-output.json` zawierający informacje takie jak nazwa usługi IoT Hub, do którego utworzona.

Aby uzyskać więcej informacji na temat parametrów wiersza polecenia Uruchom polecenie:

```cmd/sh
pcs -h
```

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia, zobacz [sposób używania interfejsu wiersza polecenia](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku przedstawiono sposób:

> [!div class="checklist"]
> * Konfigurowanie akceleratora rozwiązań
> * Wdrażanie akceleratora rozwiązań
> * Zaloguj się do akceleratora rozwiązań

Teraz, że udało Ci się wdrożyć rozwiązania do zdalnego monitorowania, następnym krokiem jest [zapoznaj się z możliwościami pulpitu nawigacyjnego rozwiązania](./quickstart-remote-monitoring-deploy.md).

<!-- Next how-to guides in the sequence -->
