---
title: Integracja usługi Time Series Insights dzięki zdalnemu monitorowaniu — Azure | Dokumentacja firmy Microsoft
description: W tym instruktażu dowiesz się, jak skonfigurować usługi Time Series Insights do istniejącego rozwiązania monitorowania zdalnego, który nie zawiera jeszcze usługi Time Series Insights.
author: aditidugar
manager: timlt
ms.author: adugar
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 4cc9b0051eaa12eee07f067352126ad159107a83
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60007478"
---
# <a name="integrate-azure-time-series-insights-with-remote-monitoring"></a>Integrowanie usługi Azure Time Series Insights za pomocą zdalnego monitorowania

Azure Time Series Insights to w pełni zarządzana usługa związana z analizą, przechowywaniem i wizualizacją do zarządzania danymi szeregów czasowych w skali IoT w chmurze. Usługa Time Series Insights służy do przechowywania i zarządzania danymi szeregów czasowych, zapoznaj się z i wizualizowanie zdarzeń jednocześnie, przeprowadzać analizy głównej przyczyny oraz porównywać wiele lokacji i zasobów.

Akcelerator rozwiązań zdalnego monitorowania udostępnia teraz automatycznego wdrażania i integracji usługi Time Series insights. W tym instruktażu dowiesz się, jak skonfigurować usługi Time Series Insights do istniejącego rozwiązania monitorowania zdalnego, który nie zawiera jeszcze usługi Time Series Insights.

> [!NOTE]
> Usługa Time Series Insights nie jest obecnie dostępna w chmurze Azure (Chiny). Nowych wdrożeń akceleratora zdalne monitorowanie rozwiązań w chmurze Azure (Chiny) używać usługi Cosmos DB do przechowywania wszystkich.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć Instruktaż, musisz został już wdrożony rozwiązaniem monitorowania zdalnego:

* [Wdrażanie akceleratora rozwiązania monitorowania zdalnego](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Utwórz grupę odbiorców

Utwórz grupę odbiorców dedykowanej w usłudze IoT Hub ma być używany do przesyłania strumieniowego danych do usługi Time Series Insights.

> [!NOTE]
> Grupy konsumentów są używane przez aplikacje do pobierania danych z usługi Azure IoT Hub. Każdej grupy odbiorców zezwala na maksymalnie pięciu odbiorców danych wyjściowych. Należy utworzyć nową grupę odbiorców, co pięć ujścia w danych wyjściowych i można utworzyć maksymalnie 32 grup odbiorców.

1. W witrynie Azure portal kliknij przycisk Cloud Shell.

1. Wykonaj następujące polecenie, aby utworzyć nową grupę odbiorców. Użyj nazwy Centrum IoT w danym wdrożeniu zdalnego monitorowania i nazwa wdrożenia zdalnego monitorowania jako nazwę grupy zasobów:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="deploy-time-series-insights"></a>Deploy Time Series Insights

Następnie Wdróż usługi Time Series Insights jako dodatkowych zasobów w rozwiązaniu monitorowania zdalnego i podłącz go do usługi IoT hub.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Wybierz **Utwórz zasób** > **Internet of Things** > **usługi Time Series Insights**.

    ![Nowe usługi Time Series Insights](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights.png)

1. Aby utworzyć środowisko usługi Time Series Insights, należy użyć wartości z poniższej tabeli:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa środowiska | Poniższy zrzut ekranu używa nazwy **contorosrmtsi**. Wybierz swoją własną unikatową nazwę, po wykonaniu tego kroku. |
    | Subskrypcja | Z listy rozwijanej wybierz subskrypcję platformy Azure. |
    | Grupa zasobów | **Użyj istniejącej**. Wybierz nazwę istniejącej grupy zasobów monitorowania zdalnego. |
    | Lokalizacja | Używamy **wschodnie stany USA**. Utwórz swoje środowisko w tym samym regionie jako rozwiązania do monitorowania zdalnego, jeśli jest to możliwe. |
    | SKU |**S1** |
    | Pojemność | **1** |

    ![Create Time Series Insights](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights-create.png)

1. Kliknij pozycję **Utwórz**. Może potrwać chwilę dla środowiska, które ma zostać utworzony.

## <a name="create-event-source"></a>Tworzenie źródła zdarzeń

Utwórz nowe źródło zdarzeń, aby nawiązać połączenie z Centrum IoT hub. Należy upewnić się, że grupa odbiorców, utworzony w poprzednich krokach. Usługa Time Series Insights wymaga każdej usługi, aby mieć dedykowanej grupy klientów nieużywany przez inną usługę.

1. Przejdź do nowego środowiska usługi Time Series Insights.

1. Po lewej stronie, wybierz **źródła zdarzeń**.

    ![Widok źródła zdarzeń](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources.png)

1. Kliknij pozycję **Add** (Dodaj).

    ![Dodawanie źródła zdarzeń](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources-add.png)

1. Aby skonfigurować usługi IoT hub jako nowe źródło zdarzeń, należy użyć wartości z poniższej tabeli:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa źródła zdarzenia | Poniższy zrzut ekranu używa nazwy **contosorm-iot-hub**. Po wykonaniu tego kroku, należy użyć swoją własną unikatową nazwę. |
    | Element źródłowy | **IoT Hub** |
    | Opcja importu | **Użyj usługi IoT Hub z dostępnych subskrypcji** |
    | Identyfikator subskrypcji | Z listy rozwijanej wybierz subskrypcję platformy Azure. |
    | Nazwa centrum IoT | **contosorma57a6**. Użyj nazwy Centrum IoT hub z rozwiązania do monitorowania zdalnego. |
    | Nazwa zasad centrum IoT | **iothubowner** upewnij się, zasady, stosowane są zasady właściciela. |
    | Klucz zasad centrum IoT | To pole jest wypełniane automatycznie. |
    | Grupa odbiorców centrum IoT | **timeseriesinsights** |
    | Format serializacji zdarzeń | **JSON**     | 
    | Nazwa właściwości sygnatury czasowej | Pozostaw puste |

    ![Tworzenie źródła zdarzeń](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-source-create.png)

1. Kliknij pozycję **Utwórz**.

## <a name="configure-the-data-access-policy"></a>Konfigurowanie zasad dostępu do danych

Aby upewnić się, że wszyscy użytkownicy, którzy mają dostęp do rozwiązania do zdalnego monitorowania do eksplorowania danych w Eksploratorze usługi Time Series Insights, należy dodać aplikację i użytkowników, w obszarze zasady dostępu do danych w witrynie Azure portal. 

1. Na liście nawigacji wybierz pozycję **Grupy zasobów**.

1. Wybierz **ContosoRM** grupy zasobów.

1. Wybierz **contosormtsi** na liście zasobów platformy Azure.

1. Wybierz **zasady dostępu do danych** Aby wyświetlić bieżącą listę przypisań ról.

1. Wybierz **Dodaj** otworzyć **wybierz regułę użytkownika** okienka.

   Jeśli nie masz uprawnienia do przypisywania ról nie widzisz **Dodaj** opcji.

1. W **roli** listy rozwijanej, wybierz rolę, takie jak **czytnika** i **Współautor**.

1. Z listy **Wybierz** wybierz użytkownika, grupę lub aplikację. Jeśli nie widzisz podmiotu zabezpieczeń na liście, możesz wpisać tekst w polu **Wybierz**, aby wyszukać w katalogu nazwy wyświetlane, adresy e-mail i identyfikatory obiektów.

1. Wybierz pozycję **Zapisz**, aby utworzyć przypisanie roli. Po kilku chwilach podmiot zabezpieczeń jest przypisana rola w zasady dostępu do danych.

> [!NOTE]
> Jeśli zachodzi potrzeba przyznawania dostępu użytkownikom do Eksploratora usługi Time Series Insights, można następujące kroki, aby [przyznawanie dostępu do danych](../time-series-insights/time-series-insights-data-access.md#grant-data-access).

## <a name="configure-azure-stream-analytics"></a>Konfigurowanie usługi Azure Stream Analytics 

Następnym krokiem jest skonfigurować mikrousług usługi Azure Stream Analytics Manager przestać wysyłanie komunikatów do usługi Cosmos DB i przechowywać je tylko w przypadku usługi Time Series Insights. Pomiń ten krok, jeśli chcesz zduplikować wiadomości w usłudze Cosmos DB.

1. Na liście nawigacji wybierz pozycję **Grupy zasobów**.

1. Wybierz **ContosoRM** grupy zasobów.

1. Znajdowanie usługi Azure Stream Analytics (ASA) zadanie na liście zasobów przesyłania strumieniowego. Nazwa zasobu, który rozpoczyna się od **streamingjobs -**.

1. U góry strony kliknij przycisk, aby zatrzymać ASA zadań przesyłania strumieniowego.

1. Edytuj zapytanie ASA i Usuń **wybierz**, **INTO**, i **FROM** klauzul, które wskazują na komunikaty przesyłania strumieniowego w usłudze Cosmos DB. Klauzule te powinny być w dolnej części zapytania i wyglądać następująco:

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

6. Ponowne uruchomienie zadań przesyłania strumieniowego usługi Azure Stream Analytics.

7. Ściągnij najnowsze zmiany do mikrousług Menedżer usłudze Azure Stream Analytics, wpisując następujące polecenie w wierszu polecenia:

.NET: 

```cmd/sh
docker pull azureiotpcs/asa-manager-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/asa-manager-java:1.0.2
```

## <a name="configure-the-telemetry-microservice"></a>Konfigurowanie mikrousług Telemetrii

Ściągnij najnowsze mikrousług Telemetrii, wpisując następujące polecenie w wierszu polecenia:

.NET:

```cmd/sh
docker pull azureiotpcs/telemetry-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/telemetry-java:1.0.2
```

## <a name="optional-configure-the-web-ui-to-link-to-the-time-series-insights-explorer"></a>*[Opcjonalnie]*  Skonfigurowania internetowego interfejsu użytkownika, aby utworzyć łącze do Eksploratora usługi Time Series Insights

Aby łatwo wyświetlić dane w Eksploratorze usługi Time Series Insights, firma Microsoft zaleca, dostosowywaniu interfejsu użytkownika, aby łatwo utworzyć łącze do środowiska. W tym celu należy ściągnąć najnowsze zmiany w interfejsie użytkownika sieci Web, korzystając z następującego polecenia:

```cmd/sh
docker pull azureiotpcs/pcs-remote-monitoring-webui:1.0.2
```

## <a name="configure-the-environment-variables"></a>Konfigurowanie zmiennych środowiskowych

Aby ukończyć integracji usługi Time Series Insights, należy skonfigurować środowisko wdrożenia pod kątem zaktualizowanych mikrousług.

### <a name="basic-deployments"></a>Podstawowe wdrożeń

Konfigurowanie środowiska `basic` wdrożenie zaktualizowanego mikrousług.

1. W witrynie Azure portal kliknij pozycję **usługi Azure Active Directory** karty w panelu po lewej stronie.

1. Kliknij pozycję **rejestracje aplikacji**.

1. Wyszukaj i wybierz polecenie swoje **ContosoRM** aplikacji.

1. Przejdź do **ustawienia** > **klucze** , a następnie utwórz nowy klucz aplikacji. Upewnij się skopiować wartość klucz do bezpiecznej lokalizacji.

1. Ściągnij [najnowsze rozwiązania docker compose pliku yaml](https://github.com/Azure/pcs-cli/tree/5a9b4e0dbe313172eff19236e54a4d461d4f3e51/solutions/remotemonitoring/single-vm) z repozytorium GitHub przy użyciu najnowszych tagu. 

1. Protokół SSH z maszyną wirtualną, wykonując czynności opisane na [sposobu tworzenia i używania kluczy SSH](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

1. Po nawiązaniu połączenia wpisz `cd /app`.

1. Dodaj następujące zmienne środowiskowe do poszczególnych mikrousług na platformie docker compose pliku yaml i `env-setup` skryptu na maszynie wirtualnej:

    ```sh
    PCS_TELEMETRY_STORAGE_TYPE=tsi
    PCS_TSI_FQDN={TSI Data Access FQDN}
    PCS_AAD_TENANT={AAD Tenant Id}
    PCS_AAD_APPID={AAD application Id}
    PCS_AAD_APPSECRET={AAD application key}
    ```

1. Przejdź do **usługa telemetrii** i również edycji platformy docker compose pliku, dodając ten sam powyżej zmiennych środowiskowych.

1. Przejdź do **usługi Menedżera ASA** i Edytuj platformy docker compose pliku, dodając `PCS_TELEMETRY_STORAGE_TYPE`.

1. Ponownie uruchom kontenery platformy docker przy użyciu `sudo ./start.sh` z maszyny Wirtualnej.

> [!NOTE]
> Powyższa Konfiguracja zmienne środowiskowe nadaje się do monitorowania zdalnego wersje wcześniejsze niż 1.0.2

### <a name="standard-deployments"></a>Wdrożeń w warstwie standardowa

Konfigurowanie środowiska `standard` wdrożenie zaktualizowanego mikrousługami powyżej

1. W wierszu polecenia Uruchom `kubectl proxy`. Aby uzyskać więcej informacji, zobacz [uzyskiwania dostępu do interfejsu API rozwiązania Kubernetes](https://kubernetes.io/docs/tasks/access-kubernetes-api/http-proxy-access-api/#using-kubectl-to-start-a-proxy-server).

1. Otwórz konsolę zarządzania usługi Kubernetes.

1. Znajdź mapę konfiguracji, aby dodać następujące nowe zmienne środowiskowe dla TSI:

    ```yaml
    telemetry.storage.type: "tsi"
    telemetry.tsi.fqdn: "{TSI Data Access FQDN}"
    security.auth.serviceprincipal.secret: "{AAD application service principal secret}"
    ```

4. Przeprowadź edycję pliku yaml szablonu dla telemetrii usługi zasobnika:

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

5. Przeprowadź edycję pliku yaml szablonu dla ASA Menedżera usługi zasobnika:

    ```yaml
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    ```

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej o tym, jak eksplorować dane i diagnozować alertu w Eksploratorze usługi Time Series Insights, zobacz Nasz samouczek dotyczący na [przeprowadzenie głównego ustalić przyczynę problemu](iot-accelerators-remote-monitoring-root-cause-analysis.md).

* Aby dowiedzieć się, jak eksplorować i wykonywanie zapytań dotyczących danych w Eksploratorze usługi Time Series Insights, zobacz dokumentację [Eksploratora usługi Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).
