---
title: Integruj usługę Time Series Insights za pomocą zdalnego monitorowania — Azure | Dokumenty firmy Microsoft
description: W tym how-to dowiesz się, jak skonfigurować time series insights dla istniejącego rozwiązania zdalnego monitorowania, który nie zawiera jeszcze usługi Time Series Insights.
author: Philmea
manager: timlt
ms.author: philmea
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 752529454a5b6293d9cbfdf8378b46947aed5a0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564648"
---
# <a name="integrate-azure-time-series-insights-with-remote-monitoring"></a>Integrowanie usługi Azure Time Series Insights za pomocą zdalnego monitorowania

Usługa Azure Time Series Insights to w pełni zarządzana usługa analizy, magazynu i wizualizacji do zarządzania danymi szeregów czasowych w skali IoT w chmurze. Za pomocą usługi Time Series Insights można przechowywać dane szeregów czasowych i zarządzać nimi, jednocześnie eksplorować i wizualizować zdarzenia, przeprowadzać analizy przyczyn źródłowych oraz porównywać wiele witryn i zasobów.

Akcelerator rozwiązań do zdalnego monitorowania zapewnia teraz automatyczne wdrażanie i integrację z usługą Time Series Insights. W tym instrukcje dowiesz się, jak skonfigurować usługi Time Series Insights dla istniejącego rozwiązania zdalnego monitorowania, które nie zawiera jeszcze usługi Time Series Insights.

> [!NOTE]
> Usługa Time Series Insights nie jest obecnie dostępna w chmurze platformy Azure w Chinach. Nowe wdrożenia akceleratora rozwiązań do zdalnego monitorowania w chmurze azure chiny używać usługi Cosmos DB dla wszystkich magazynu.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać ten sposób, musisz już wdrożyć rozwiązanie do zdalnego monitorowania:

* [Wdrażanie akceleratora rozwiązań do zdalnego monitorowania](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Tworzenie grupy odbiorców

Utwórz dedykowaną grupę odbiorców w centrum IoT Hub, która będzie używana do przesyłania strumieniowego danych do usługi Time Series Insights.

> [!NOTE]
> Grupy konsumentów są używane przez aplikacje do ściągania danych z usługi Azure IoT Hub. Każda grupa konsumentów pozwala maksymalnie pięciu odbiorców wyjściowych. Należy utworzyć nową grupę odbiorców dla każdego pięciu pochłaniacze danych wyjściowych i można utworzyć maksymalnie 32 grup odbiorców.

1. W witrynie Azure portal kliknij przycisk Powłoki chmury.

1. Wykonaj następujące polecenie, aby utworzyć nową grupę odbiorców. Użyj nazwy centrum IoT w umieszczeniu zdalnego monitorowania oraz nazwy wdrożenia monitorowania zdalnego jako nazwy grupy zasobów:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="deploy-time-series-insights"></a>Wdrażanie szczegółowych informacji o szeregach czasowych

Następnie należy wdrożyć usługi Time Series Insights jako dodatkowy zasób w rozwiązaniu do zdalnego monitorowania i połączyć go z centrum IoT hub.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. Wybierz **pozycję Utwórz zasób** > **Aplikacja Internet of Things** > **Time Series Insights**.

    ![Nowe informacje o szeregach czasowych](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights.png)

1. Aby utworzyć środowisko usługi Time Series Insights, użyj wartości w poniższej tabeli:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa środowiska | Poniższy zrzut ekranu używa nazwy **contorosrmtsi**. Po wykonaniu tego kroku wybierz własną unikatową nazwę. |
    | Subskrypcja | Z listy rozwijanej wybierz subskrypcję platformy Azure. |
    | Grupa zasobów | **Użyj istniejącego**pliku . Wybierz nazwę istniejącej grupy zasobów monitorowania zdalnego. |
    | Lokalizacja | Używamy **Wschodnich Stanów Zjednoczonych**. W miarę możliwości utwórz środowisko w tym samym regionie co rozwiązanie do zdalnego monitorowania. |
    | SKU |**S1** |
    | Pojemność | **1** |

    ![Tworzenie statystyk szeregów czasowych](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights-create.png)

1. Kliknij przycisk **Utwórz**. Stworzenie środowiska może chwilę potrwać.

## <a name="create-event-source"></a>Tworzenie źródła zdarzeń

Utwórz nowe źródło zdarzeń, aby połączyć się z centrum IoT Hub. Upewnij się, że używasz grupy odbiorców utworzonej w poprzednich krokach. Usługa Time Series Insights wymaga, aby każda usługa miała dedykowaną grupę odbiorców, która nie jest używana przez inną usługę.

1. Przejdź do nowego środowiska usługi Time Series Insights.

1. Po lewej stronie wybierz pozycję **Źródła zdarzeń**.

    ![Wyświetlanie źródeł zdarzeń](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources.png)

1. Kliknij przycisk **Dodaj**.

    ![Dodaj źródło zdarzeń](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources-add.png)

1. Aby skonfigurować centrum IoT hub jako nowe źródło zdarzeń, użyj wartości w poniższej tabeli:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa źródła zdarzeń | Poniższy zrzut ekranu używa nazwy **contosorm-iot-hub**. Użyj własnej unikatowej nazwy po wykonaniu tego kroku. |
    | Element źródłowy | **Iot** |
    | Opcja importu | **Korzystanie z Usługi IoT Hub z dostępnych subskrypcji** |
    | Identyfikator subskrypcji | Z listy rozwijanej wybierz subskrypcję platformy Azure. |
    | Nazwa centrum Iot | **contosorma57a6**. Użyj nazwy centrum IoT hub z rozwiązania zdalnego monitorowania. |
    | Nazwa zasady IoT Hub | **iothubowner** Upewnij się, że używane zasady są zasadami właściciela. |
    | Klucz zasad centrum Iot | To pole jest wypełniane automatycznie. |
    | Grupa użytkowników IoT Hub | **timeseriesinsights** |
    | Format serializacji zdarzeń | **Json**     | 
    | Nazwa właściwości znacznika czasu | Pozostaw puste |

    ![Utwórz źródło zdarzeń](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-source-create.png)

1. Kliknij przycisk **Utwórz**.

## <a name="configure-the-data-access-policy"></a>Konfigurowanie zasad dostępu do danych

Aby upewnić się, że wszyscy użytkownicy, którzy mają dostęp do rozwiązania do zdalnego monitorowania są w stanie eksplorować dane w Eksploratorze usługi Time Series Insights, dodaj aplikację i użytkowników w ramach zasad dostępu do danych w witrynie Azure portal. 

1. Na liście nawigacji wybierz pozycję **Grupy zasobów**.

1. Wybierz grupę zasobów **ContosoRM.**

1. Wybierz **contosormtsi** na liście zasobów platformy Azure.

1. Wybierz pozycję **Zasady dostępu do danych,** aby wyświetlić bieżącą listę przypisań ról.

1. Wybierz **pozycję Dodaj,** aby otworzyć okienko **Wybierz regułę użytkownika.**

   Jeśli nie masz uprawnień do przypisywania ról, nie widzisz opcji **Dodaj.**

1. Z listy rozwijanej **Rola** wybierz rolę, taką jak **Czytelnik** i **Współautor**.

1. Z listy **Wybierz** wybierz użytkownika, grupę lub aplikację. Jeśli nie widzisz podmiotu zabezpieczeń na liście, możesz wpisać tekst w polu **Wybierz**, aby wyszukać w katalogu nazwy wyświetlane, adresy e-mail i identyfikatory obiektów.

1. Wybierz pozycję **Zapisz**, aby utworzyć przypisanie roli. Po kilku chwilach podmiot zabezpieczeń jest przypisany rolę w zasadach dostępu do danych.

> [!NOTE]
> Jeśli chcesz udzielić dodatkowym użytkownikom dostępu do Eksploratora Usługi Time Series Insights, możesz użyć tych kroków, aby [udzielić dostępu do danych](../time-series-insights/time-series-insights-data-access.md#grant-data-access).

## <a name="configure-azure-stream-analytics"></a>Konfigurowanie usługi Azure Stream Analytics 

Następnym krokiem jest skonfigurowanie mikrousługi usługi Azure Stream Analytics Manager do zaprzestania wysyłania wiadomości do usługi Cosmos DB i przechowywania ich tylko w usłudze Time Series Insights. Pomiń ten krok, jeśli chcesz powielić wiadomości w usłudze Cosmos DB.

1. Na liście nawigacji wybierz pozycję **Grupy zasobów**.

1. Wybierz grupę zasobów **ContosoRM.**

1. Znajdź zadanie przesyłania strumieniowego usługi Azure Stream Analytics (ASA) na liście zasobów. Nazwa zasobu zaczyna się od **streamingjobs-**.

1. U góry kliknij przycisk, aby zatrzymać zadania przesyłania strumieniowego ASA.

1. Edytuj kwerendę ASA i usuń select **,** **INTO**i **FROM** klauzule, które wskazują na strumień wiadomości w usłudze Cosmos DB. Te klauzule powinny znajdować się w dolnej części kwerendy i wyglądać jak w poniższym przykładzie:

    ```sql
    SELECT
            CONCAT(T.IoTHub.ConnectionDeviceId, ';', CAST(DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) AS nvarchar(max))) as id,
            1 as [doc.schemaVersion],
            'd2cmessage' as [doc.schema],
            T.IoTHub.ConnectionDeviceId as [device.id],
            'device-sensors;v1' as [device.msg.schema],
            'StreamingJobs' as [data.schema],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', System.Timestamp) as [device.msg.created],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) as [device.msg.received],
            udf.removeUnusedProperties(T) as Data
    INTO
        Messages
    FROM
        DeviceTelemetry T PARTITION BY PartitionId TIMESTAMP BY T.EventEnqueuedUtcTime
    ```

6. Uruchom ponownie zadania przesyłania strumieniowego usługi Azure Stream Analytics.

7. Ciągnienie najnowsze zmiany do mikrousługi usługi Azure Stream Analytics managera, wpisując następujące polecenie w wierszu polecenia:

.NET: 

```cmd/sh
docker pull azureiotpcs/asa-manager-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/asa-manager-java:1.0.2
```

## <a name="configure-the-telemetry-microservice"></a>Konfigurowanie mikrousługi telemetrii

Ciągnienie najnowszej mikrousługi telemetrii, wpisując następujące polecenie w wierszu polecenia:

.NET:

```cmd/sh
docker pull azureiotpcs/telemetry-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/telemetry-java:1.0.2
```

## <a name="optional-configure-the-web-ui-to-link-to-the-time-series-insights-explorer"></a>*[Opcjonalnie]* Konfigurowanie interfejsu użytkownika sieci Web w celu połączenia z eksploratorem usługi Time Series Insights

Aby łatwo wyświetlić dane w Eksploratorze usługi Time Series Insights, zalecamy dostosowanie interfejsu użytkownika, aby łatwo połączyć się ze środowiskiem. Aby to zrobić, pociągnij najnowsze zmiany do interfejsu użytkownika sieci Web za pomocą następującego polecenia:

```cmd/sh
docker pull azureiotpcs/pcs-remote-monitoring-webui:1.0.2
```

## <a name="configure-the-environment-variables"></a>Konfigurowanie zmiennych środowiskowych

Aby zakończyć integrację usługi Time Series Insights, należy skonfigurować środowisko wdrożenia dla zaktualizowanych mikrousług.

### <a name="basic-deployments"></a>Podstawowe wdrożenia

Skonfiguruj `basic` środowisko wdrażania dla zaktualizowanych mikrousług.

1. W witrynie Azure portal kliknij kartę **Usługi Azure Active Directory** w panelu po lewej stronie.

1. Kliknij **rejestracje aplikacji**.

1. Wyszukaj i kliknij aplikację **ContosoRM.**

1. Przejdź do**pozycji Klawisze** **ustawień,** > a następnie utwórz nowy klucz dla aplikacji. Pamiętaj, aby skopiować wartość klucza do bezpiecznej lokalizacji.

1. Ciągnienie [najnowszy plik docker compose yaml](https://github.com/Azure/pcs-cli/tree/5a9b4e0dbe313172eff19236e54a4d461d4f3e51/solutions/remotemonitoring/single-vm) z repozytorium GitHub przy użyciu najnowszego tagu. 

1. SSH do maszyny Wirtualnej, wykonując kroki opisane w [jaki sposób tworzyć i używać kluczy SSH](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

1. Po podłączeniu `cd /app`wpisz .

1. Dodaj następujące zmienne środowiskowe do każdej mikrousługi w pliku `env-setup` yaml docker compose i skryptu na maszynie Wirtualnej:

    ```sh
    PCS_TELEMETRY_STORAGE_TYPE=tsi
    PCS_TSI_FQDN={TSI Data Access FQDN}
    PCS_AAD_TENANT={AAD Tenant Id}
    PCS_AAD_APPID={AAD application Id}
    PCS_AAD_APPSECRET={AAD application key}
    ```

1. Przejdź do **usługi telemetryczne,** a także edytować plik redagowania docker, dodając te same zmienne środowiskowe powyżej.

1. Przejdź do **usługi menedżera ASA** i edytuj plik redagowania docker, dodając `PCS_TELEMETRY_STORAGE_TYPE`plik .

1. Uruchom ponownie kontenery `sudo ./start.sh` platformy docker przy użyciu maszyny Wirtualnej.

> [!NOTE]
> Powyższa konfiguracja zmiennych środowiskowych jest ważna dla wersji zdalnego monitorowania przed 1.0.2

### <a name="standard-deployments"></a>Wdrożenia standardowe

Konfigurowanie środowiska `standard` wdrażania dla zaktualizowanych mikro usług powyżej

1. W wierszu polecenia `kubectl proxy`uruchom polecenie . Aby uzyskać więcej informacji, zobacz [uzyskiwanie dostępu do interfejsu API usługi Kubernetes](https://kubernetes.io/docs/tasks/access-kubernetes-api/http-proxy-access-api/#using-kubectl-to-start-a-proxy-server).

1. Otwórz konsolę zarządzania kubernetes.

1. Znajdź mapę konfiguracji, aby dodać następujące nowe zmienne środowiskowe dla TSI:

    ```yaml
    telemetry.storage.type: "tsi"
    telemetry.tsi.fqdn: "{TSI Data Access FQDN}"
    security.auth.serviceprincipal.secret: "{AAD application service principal secret}"
    ```

4. Edytuj plik yaml szablonu dla zasobnika usługi telemetrii:

    ```yaml
    - name: PCS_AAD_TENANT
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.tenant
    - name: PCS_AAD_APPID
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.audience
    - name: PCS_AAD_APPSECRET
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.serviceprincipal.secret
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    - name: PCS_TSI_FQDN
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.tsi.fqdn
    ```

5. Edytuj plik yaml szablonu dla zasobnika usługi menedżera ASA:

    ```yaml
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    ```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak eksplorować dane i diagnozować alert w Eksploratorze usługi Time Series Insights, zapoznaj się z naszym samouczkiem na temat [przeprowadzania analizy głównej przyczyny.](iot-accelerators-remote-monitoring-root-cause-analysis.md)

* Aby dowiedzieć się, jak eksplorować i badać dane w Eksploratorze usługi Time Series Insights, zobacz dokumentację w [eksploratorze usługi Azure Time Series Insights.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)
