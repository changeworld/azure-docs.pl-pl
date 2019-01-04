---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/20/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: ab3d4cbe8a200e91d02177e49446065cd9c228df
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/19/2018
ms.locfileid: "53609076"
---
## <a name="create-a-consumer-group"></a>Utwórz grupę odbiorców

Musisz utworzyć dedykowanej grupy klientów w usłudze IoT hub do strumieni telemetrii do usługi Time Series Insights. Źródło zdarzenia w usłudze Time Series Insights powinny mieć wyłącznie do Twojego użytku grupy konsumentów usługi IoT Hub.

Następujące kroki umożliwiają wiersza polecenia platformy Azure w usłudze Azure Cloud Shell Utwórz grupę odbiorców:

1. Usługa IoT hub jest jednym z kilku zasobów utworzone podczas wdrażania akcelerator rozwiązań symulacji urządzenia. Wykonaj następujące polecenie Znajdź nazwę Centrum IoT hub — należy pamiętać użyć nazwy Twój akcelerator rozwiązań:

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    Usługa IoT hub jest zasób typu **Microsoft.Devices/IotHubs**.

1. Dodaj grupę konsumentów o nazwie **devicesimulationtsi** do koncentratora. W poniższym poleceniu należy użyć nazwy akceleratorze Centrum i rozwiązania:

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    Możesz teraz zamknąć usługę Azure Cloud Shell.

## <a name="create-a-new-time-series-insights-environment"></a>Utwórz nowe środowisko usługi Time Series Insights

[Usługa Azure Time Series Insights](../articles/time-series-insights/time-series-insights-overview.md) jest w pełni zarządzana usługa analizy, magazynowania i wizualizacji związanych z zarządzaniem danymi szeregów czasowych w skali IoT w chmurze. Aby utworzyć nowego środowiska usługi Time Series Insights:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Wybierz **Utwórz zasób** > **Internet of Things** > **usługi Time Series Insights**:

    ![Nowe usługi Time Series Insights](./media/iot-accelerators-create-tsi/new-time-series-insights.png)

1. Aby utworzyć środowisko usługi Time Series Insights w tej samej grupie zasobów, co Twój akcelerator rozwiązań, należy użyć wartości z poniższej tabeli:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa środowiska | Poniższy zrzut ekranu używa nazwy **Contoso TSI**. Wybierz swoją własną unikatową nazwę, po wykonaniu tego kroku. |
    | Subskrypcja | Z listy rozwijanej wybierz subskrypcję platformy Azure. |
    | Grupa zasobów | **Symulacja contoso**. Użyj nazwy Twój akcelerator rozwiązań. |
    | Lokalizacja | W tym przykładzie użyto **wschodnie stany USA**. Utwórz swoje środowisko, w tym samym regionie, co akceleratorze symulacji urządzenia. |
    | SKU |**S1** |
    | Pojemność | **1** |

    ![Tworzenie usługi Time Series Insights](./media/iot-accelerators-create-tsi/new-time-series-insights-create.png)

    > [!NOTE]
    > Dodawanie usługi Time Series Insights środowisko do tej samej grupie zasobów jako akcelerator rozwiązań oznacza, że jest on usuwany po usunięciu akcelerator rozwiązań.

1. Kliknij pozycję **Utwórz**. Może upłynąć kilka minut, aż środowisko, które ma zostać utworzony.

## <a name="create-event-source"></a>Tworzenie źródła zdarzeń

Utwórz nowe źródło zdarzeń, aby nawiązać połączenie z Centrum IoT hub. Użyj grupy odbiorców, który został utworzony w poprzednich krokach. Źródła zdarzeń usługi Time Series Insights wymaga dedykowanej grupy klientów nieużywany przez inną usługę.

1. W witrynie Azure portal przejdź do Twojego nowego środowiska usługi Time Series.

1. Po lewej stronie, kliknij przycisk **źródła zdarzeń**:

    ![Widok źródła zdarzeń](./media/iot-accelerators-create-tsi/time-series-insights-event-sources.png)

1. Kliknij przycisk **Dodaj**:

    ![Dodawanie źródła zdarzeń](./media/iot-accelerators-create-tsi/time-series-insights-event-sources-add.png)

1. Aby skonfigurować usługi IoT hub jako nowe źródło zdarzeń, należy użyć wartości z poniższej tabeli:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Źródło zdarzenia nazwy | Poniższy zrzut ekranu używa nazwy **contoso-iot-hub**. Po wykonaniu tego kroku, należy użyć swoją własną unikatową nazwę. |
    | Element źródłowy | **IoT Hub** |
    | Opcja importu | **Użyj usługi IoT Hub z dostępnych subskrypcji** |
    | Identyfikator subskrypcji | Z listy rozwijanej wybierz subskrypcję platformy Azure. |
    | Nazwa centrum iot | **Contoso simulation7d894**. Użyj nazwy Centrum IoT hub z Twój akcelerator rozwiązań symulacji urządzenia. |
    | Nazwa zasad Centrum iot | **iothubowner** |
    | Klucz zasad Centrum iot | To pole jest wypełniane automatycznie. |
    | Grupa konsumentów Centrum iot | **devicesimulationtsi** |
    | Format serializacji zdarzeń | **JSON** |
    | Nazwa właściwości sygnatury czasowej | Pozostaw puste |

    ![Tworzenie źródła zdarzeń](./media/iot-accelerators-create-tsi/time-series-insights-event-source-create.png)

1. Kliknij pozycję **Utwórz**.

> [!NOTE]
> Możesz [przyznawania dostępu użytkownikom](../articles/time-series-insights/time-series-insights-data-access.md#grant-data-access) do Eksploratora usługi Time Series Insights.