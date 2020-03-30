---
title: Wdrażanie rozwiązania do zdalnego monitorowania przy użyciu interfejsu wiersza polecenia — Azure | Dokumenty firmy Microsoft
description: W tym przewodniku pokazano, jak aprowizować akcelerator rozwiązań zdalnego monitorowania przy użyciu interfejsu wiersza polecenia.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: 501ca51a9542229a14e98a56679837950a82891e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258298"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>Wdrażanie akceleratora rozwiązań do zdalnego monitorowania przy użyciu interfejsu wiersza polecenia

W tym przewodniku przedstawiono sposób wdrażania akceleratora rozwiązań do zdalnego monitorowania. Wdrażanie rozwiązania przy użyciu interfejsu wiersza polecenia. Rozwiązanie można również wdrożyć przy użyciu interfejsu użytkownika opartego na sieci Web w azureiotsolutions.com, aby dowiedzieć się więcej o tej opcji, zobacz Szybki start [akceleratora rozwiązania monitorowania zdalnego.](quickstart-remote-monitoring-deploy.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby wdrożyć akcelerator rozwiązań do zdalnego monitorowania, potrzebujesz aktywnej subskrypcji platformy Azure.

Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

Aby uruchomić interfejsu wiersza polecenia, należy [node.js](https://nodejs.org/) zainstalowany na komputerze lokalnym.

## <a name="install-the-cli"></a>Instalowanie interfejsu wiersza polecenia

Aby zainstalować wiersz polecenia, uruchom następujące polecenie w środowisku wiersza polecenia:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Zaloguj się do interfejsu wiersza polecenia

Przed wdrożeniem akceleratora rozwiązań należy zalogować się do subskrypcji platformy Azure przy użyciu interfejsu wiersza polecenia:

```cmd/sh
pcs login
```

Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, aby zakończyć proces logowania.

## <a name="deployment-options"></a>Opcje wdrożenia

Podczas wdrażania akceleratora rozwiązań istnieje kilka opcji, które konfigurują proces wdrażania:

| Opcja | Wartości | Opis |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | _Podstawowe_ wdrożenie jest przeznaczone do testowania i demonstracji, wdraża wszystkie mikrousługi na jednej maszynie wirtualnej. _Wdrożenie standardowe_ jest przeznaczone do produkcji, wdraża mikrousługi na kilku maszynach wirtualnych. Wdrożenie _lokalne_ konfiguruje kontener platformy Docker do uruchamiania mikrousług na komputerze lokalnym i korzysta z usług w chmurze platformy Azure, takich jak magazyn i usługa Cosmos DB. |
| Środowisko uruchomieniowe | `dotnet`, `java` | Wybiera implementację języka mikrousług. |

Aby dowiedzieć się, jak korzystać z opcji wdrażania lokalnego, zobacz [Lokalne uruchamianie rozwiązania do zdalnego monitorowania](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="basic-and-standard-deployments"></a>Wdrożenia podstawowe i standardowe

W tej sekcji podsumowano kluczowe różnice między wdrożeniem podstawowym a standardowym.

### <a name="basic"></a>Podstawowa (Basic)

Można wykonać podstawowe wdrożenie z [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) lub przy użyciu interfejsu wiersza polecenia.

Podstawowe wdrożenie jest nastawione na pokazanie rozwiązania. Aby zmniejszyć koszty, wszystkie mikrousługi są wdrażane na jednej maszynie wirtualnej. To wdrożenie nie używa architektury gotowej do produkcji.

Podstawowe wdrożenie tworzy następujące usługi w ramach subskrypcji platformy Azure:

| Liczba | Zasób                       | Typ         | Używany do |
|-------|--------------------------------|--------------|----------|
| 1     | [Maszyna wirtualna z systemem Linux](https://azure.microsoft.com/services/virtual-machines/) | Standard D1 V2  | Hostowanie mikrousług |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                  | S1 – warstwa standardowa | Zarządzanie urządzeniami i komunikacja |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | Standardowa        | Przechowywanie danych konfiguracyjnych, reguł, alertów i innych chłodni |  
| 1     | [Konto usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | Standardowa        | Pamięć masowa dla maszyn wirtualnych i punktów kontrolnych przesyłania strumieniowego |
| 1     | [Aplikacja internetowa](https://azure.microsoft.com/services/app-service/web/)        |                 | Hosting aplikacji sieci web front-end |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Zarządzanie tożsamościami użytkowników i zabezpieczeniami |
| 1     | [Mapy platformy Azure](https://azure.microsoft.com/services/azure-maps/)        | Standardowa                | Wyświetlanie lokalizacji zasobów |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 szt.              | Włączanie analizy w czasie rzeczywistym |
| 1     | [Usługa inicjowania obsługi administracyjnej urządzeń platformy Azure](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Inicjowanie obsługi administracyjnej urządzeń na dużą skalę |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 jednostka              | Przechowywanie danych wiadomości i umożliwia dogłębną analizę telemetrii |

### <a name="standard"></a>Standardowa

Wdrożenie standardowe można wykonać tylko przy użyciu interfejsu wiersza polecenia.

Wdrożenie standardowe to wdrożenie gotowe do produkcji, które deweloper może dostosować i rozszerzyć. Użyj opcji wdrożenia standardowego, gdy będziesz gotowy do dostosowania architektury gotowej do produkcji, stworzonej z myślą o skali i rozszerzalności. Mikrousługi aplikacji są tworzone jako kontenery platformy Docker i wdrażane przy użyciu usługi Azure Kubernetes. Programor programu Kubernetes wdraża, skaluje i zarządza mikrousługami.

Wdrożenie standardowe tworzy następujące usługi w ramach subskrypcji platformy Azure:

| Liczba | Zasób                                     | Jednostka SKU / Rozmiar      | Używany do |
|-------|----------------------------------------------|-----------------|----------|
| 1     | [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service)| Użyj w pełni zarządzanej usługi aranżacji kontenera Kubernetes, domyślnie 3 agentów|
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                     | S2 – warstwa standardowa | Zarządzanie urządzeniami, sterowanie i sterowanie |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Standardowa        | Przechowywanie danych konfiguracji i danych telemetrycznych urządzenia, takich jak reguły, alerty i komunikaty |
| 5     | [Konta usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Standardowa        | 4 dla pamięci masowej maszyn wirtualnych i 1 dla punktów kontrolnych przesyłania strumieniowego |
| 1     | [App Service](https://azure.microsoft.com/services/app-service/web/)             | Standardowa S1     | Brama aplikacji za 10 |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Zarządzanie tożsamościami użytkowników i zabezpieczeniami |
| 1     | [Mapy platformy Azure](https://azure.microsoft.com/services/azure-maps/)        | Standardowa                | Wyświetlanie lokalizacji zasobów |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 szt.              | Włączanie analizy w czasie rzeczywistym |
| 1     | [Usługa inicjowania obsługi administracyjnej urządzeń platformy Azure](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Inicjowanie obsługi administracyjnej urządzeń na dużą skalę |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 jednostka              | Przechowywanie danych wiadomości i umożliwia dogłębną analizę telemetrii |

> [!NOTE]
> Informacje o cenach tych usług [https://azure.microsoft.com/pricing](https://azure.microsoft.com/pricing)można znaleźć na poziomie . Szczegóły użycia i rozliczeń dla subskrypcji można znaleźć w witrynie [Azure Portal.](https://portal.azure.com/)

## <a name="deploy-the-solution-accelerator"></a>Wdrażanie akceleratora rozwiązań

Przykłady wdrażania:

### <a name="example-deploy-net-version"></a>Przykład: wdrażanie wersji platformy .NET

W poniższym przykładzie pokazano, jak wdrożyć podstawową wersję .NET akceleratora rozwiązań do zdalnego monitorowania:

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Przykład: wdrażanie wersji Java

W poniższym przykładzie pokazano, jak wdrożyć standardową wersję java akceleratora rozwiązań do zdalnego monitorowania:

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>opcje poleceń szt.

Po uruchomieniu `pcs` polecenia, aby wdrożyć rozwiązanie, zostaniesz poproszony o:

- Nazwa rozwiązania. Ta nazwa musi być unikatowa.
- Subskrypcja platformy Azure, która ma być używana.
- Lokalizacja.
- Poświadczenia dla maszyn wirtualnych, które hostuje mikrousług. Poświadczeń tych można użyć, aby uzyskać dostęp do maszyn wirtualnych w celu rozwiązywania problemów.

Po `pcs` zakończeniu polecenia zostanie wyświetlony adres URL nowego akceleratora rozwiązań. Polecenie `pcs` tworzy również `{deployment-name}-output.json` plik, który zawiera informacje, takie jak nazwa centrum IoT, który został utworzony.

Aby uzyskać więcej informacji na temat parametrów wiersza polecenia, uruchom:

```cmd/sh
pcs -h
```

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia, zobacz [Jak używać interfejsu wiersza polecenia](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku instrukcja, dowiedziałeś się, jak:

> [!div class="checklist"]
> * Konfigurowanie akceleratora rozwiązań
> * Wdrażanie akceleratora rozwiązań
> * Zaloguj się do akceleratora rozwiązań

Teraz, gdy wdrożono rozwiązanie do zdalnego monitorowania, następnym krokiem jest [zbadanie możliwości pulpitu nawigacyjnego rozwiązania.](./quickstart-remote-monitoring-deploy.md)

<!-- Next how-to guides in the sequence -->
