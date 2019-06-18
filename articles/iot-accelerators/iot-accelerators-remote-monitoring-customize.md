---
title: Dostosowywanie rozwiązania do zdalnego monitorowania interfejsu użytkownika — Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje dotyczące sposobu dostępu do kodu źródłowego dla akceleratora rozwiązania monitorowania zdalnego interfejsu użytkownika i pewne dostosowania.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/09/2018
ms.topic: conceptual
ms.openlocfilehash: aed63e332375be4f8ed939cf162545c9f366f329
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66143499"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>Dostosowywanie akceleratora rozwiązań zdalnego monitorowania

Ten artykuł zawiera informacje dotyczące sposobu dostępu do kodu źródłowego i dostosować akcelerator rozwiązań monitorowania zdalnego interfejsu użytkownika.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Przygotowywanie lokalnego środowiska deweloperskiego dla interfejsu użytkownika

Zdalne monitorowanie akceleratora rozwiązań kodu interfejsu użytkownika jest implementowany przy użyciu React.js framework. Można znaleźć kodu źródłowego w [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) repozytorium GitHub.

Aby wprowadzić zmiany w interfejsie użytkownika, można uruchomić lokalnie jego kopię. Aby wykonać akcje, takie jak pobieranie danych telemetrycznych, kopia lokalna łączy się z wdrożonego wystąpienia rozwiązania.

Poniższe kroki przedstawiają procedurę konfigurowania lokalnego środowiska do tworzenia interfejsu użytkownika:

1. Wdrażanie **podstawowe** wystąpienie za pomocą akceleratora rozwiązań **komputerów** interfejsu wiersza polecenia. Zanotuj nazwę wdrożenia i poświadczenia podane dla maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [wdrażanie przy użyciu interfejsu wiersza polecenia](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Aby włączyć dostęp SSH do maszyny wirtualnej, która obsługuje mikrousługi w rozwiązaniu, użyj witryny Azure portal lub usługi Azure Cloud Shell. Na przykład:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Dostęp SSH należy włączyć tylko podczas projektowania i testowania. Po włączeniu protokołu SSH, [należy wyłączyć je jak najszybciej po zakończeniu korzystania z niego](../security/azure-security-network-security-best-practices.md#disable-rdpssh-access-to-virtual-machines).

1. Użyj witryny Azure portal lub usługi Azure Cloud Shell, aby znaleźć nazwę i publiczny adres IP swojej maszyny wirtualnej. Na przykład:

    ```azurecli-interactive
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Korzystanie z protokołu SSH, aby nawiązać połączenie z maszyną wirtualną. Użyj adresu IP z poprzedniego kroku i poświadczenia podane po uruchomieniu **komputerów** do wdrożenia rozwiązania. `ssh` Polecenie jest dostępne w usłudze Azure Cloud Shell.

1. Aby umożliwić lokalnego środowiska użytkownika do łączenia z, uruchom następujące polecenia w powłoce bash na maszynie wirtualnej:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Po zakończeniu działania polecenia i rozpoczyna się w witrynie sieci web, możesz odłączyć od maszyny wirtualnej.

1. W lokalnej kopii [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) repozytorium, Edytuj **ENV** plik, aby dodać adres URL wdrożonego rozwiązania:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. W wierszu polecenia przejdź do swojej lokalnej kopii `azure-iot-pcs-remote-monitoring-webui` folderu.

1. Instalowanie wymaganych bibliotek i uruchomić interfejs użytkownika lokalnie, uruchom następujące polecenia:

    ```cmd/sh
    npm install
    npm start
    ```

1. Poprzednie polecenie uruchomieniu interfejsu użytkownika lokalnie http:\//localhost:3000 / pulpitu nawigacyjnego. Można edytować kod lokacji jest uruchomiona i zobaczyć, że są aktualizowane dynamicznie.

## <a name="customize-the-layout"></a>Dostosowywanie układu

Każda strona w rozwiązaniu do zdalnego monitorowania składa się z zestawem formantów, nazywane *panele* w kodzie źródłowym. **Pulpit nawigacyjny** strony składa się z pięciu panele: Omówienie, mapy, alerty, Telemetrii i analizy. Można znaleźć kod źródłowy, który definiuje każdej strony i jego paneli w [komputerów z systemem remote-monitoring-interfejsem sieci Web](https://github.com/Azure/pcs-remote-monitoring-webui) repozytorium GitHub. Na przykład, kod, który definiuje **pulpit nawigacyjny** strony, jej układ i panele na tej stronie znajduje się w [src/składniki/stron/pulpit nawigacyjny](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard) folderu.

Ponieważ zespoły zarządzać swoimi układ i zmiany rozmiaru, można łatwo modyfikować układ strony. Wprowadź następujące zmiany do **PageContent** element `src/components/pages/dashboard/dashboard.js` pliku:

* Zamień pozycje paneli mapy i dane telemetryczne.
* Zmienić względną szerokość kontrolki mapy i analizy paneli.

```javascript
<PageContent className="dashboard-container">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        activeDeviceGroup={activeDeviceGroup}
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={analyticsIsPending || devicesIsPending}
        error={deviceGroupError || devicesError || analyticsError}
        t={t} />
    </Cell>
    <Cell className="col-6">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <AlertsPanel
        alerts={currentActiveAlertsWithName}
        isPending={analyticsIsPending || rulesIsPending}
        error={rulesError || analyticsError}
        t={t}
        deviceGroups={deviceGroups} />
    </Cell>
    <Cell className="col-4">
      <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          analyticsVersion={analyticsVersion}
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlert={devicesInAlert}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || analyticsIsPending}
          error={azureMapsKeyError || devicesError || analyticsError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <AnalyticsPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        topAlerts={topAlertsWithName}
        alertsPerDeviceId={alertsPerDeviceType}
        criticalAlertsChange={criticalAlertsChange}
        isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || analyticsError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    {
      Config.showWalkthroughExamples &&
      <Cell className="col-4">
        <ExamplePanel t={t} />
      </Cell>
    }
  </Grid>
</PageContent>
```

![Zmień układ panelu](./media/iot-accelerators-remote-monitoring-customize/layout.png)

Można również dodać kilka wystąpień tego samego panel lub różne wersje Jeśli możesz [zduplikowane i Dostosowywanie panelu](#duplicate-and-customize-an-existing-control). Poniższy przykład pokazuje, jak dodać dwa wystąpienia w panelu dane telemetryczne. Aby wprowadzić te zmiany, należy edytować `src/components/pages/dashboard/dashboard.js` pliku:

```javascript
<PageContent className="dashboard-container">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        activeDeviceGroup={activeDeviceGroup}
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={analyticsIsPending || devicesIsPending}
        error={deviceGroupError || devicesError || analyticsError}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <AlertsPanel
        alerts={currentActiveAlertsWithName}
        isPending={analyticsIsPending || rulesIsPending}
        error={rulesError || analyticsError}
        t={t}
        deviceGroups={deviceGroups} />
    </Cell>
    <Cell className="col-4">
      <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          analyticsVersion={analyticsVersion}
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlert={devicesInAlert}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || analyticsIsPending}
          error={azureMapsKeyError || devicesError || analyticsError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <AnalyticsPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        topAlerts={topAlertsWithName}
        alertsPerDeviceId={alertsPerDeviceType}
        criticalAlertsChange={criticalAlertsChange}
        isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || analyticsError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    {
      Config.showWalkthroughExamples &&
      <Cell className="col-4">
        <ExamplePanel t={t} />
      </Cell>
    }
  </Grid>
</PageContent>
```

Można wyświetlić różne dane telemetryczne w każdym panelu:

![Wielu paneli telemetrii](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

## <a name="duplicate-and-customize-an-existing-control"></a>Zduplikowany i dostosowywania istniejącej kontrolki

Następujące kroki przedstawiają sposób Duplikowanie istniejącego panelu, zmodyfikuj go, a następnie użyć zmodyfikowanej wersji. Użyj kroków **alerty** panelu, na przykład:

1. W lokalnej kopii repozytorium, Utwórz kopię **alerty** folderze `src/components/pages/dashboard/panels` folderu. Nadaj nazwę nowej kopii **cust_alerts**.

1. W **alertsPanel.js** w pliku **cust_alerts** folderu, Zmień nazwę klasy, która ma być **CustAlertsPanel**:

    ```javascript
    export class CustAlertsPanel extends Component {
    ```

1. Dodaj następujący wiersz do `src/components/pages/dashboard/panels/index.js` pliku:

    ```javascript
    export * from './cust_alerts';
    ```

1. Zastąp `alertsPanel` z `CustAlertsPanel` w `src/components/pages/dashboard/dashboard.js` pliku:

    ```javascript
    import {
      OverviewPanel,
      CustAlertsPanel,
      TelemetryPanel,
      KpisPanel,
      MapPanel,
      transformTelemetryResponse,
      chartColors
    } from './panels';

    ...

    <Cell className="col-3">
      <CustAlertsPanel
        alerts={currentActivealertsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    ```

Została obecnie zastąpiona oryginalna **alerty** panelu kopią o nazwie **CustAlerts**. Ta kopia jest taka sama jak oryginalne. Można teraz modyfikować, kopiować. Na przykład, aby zmienić kolejność kolumn w **alerty** panelu:

1. Otwórz plik `src/components/pages/dashboard/panels/cust_alerts/alertsPanel.js`.

1. Zmień definicje kolumn, jak pokazano w poniższym fragmencie kodu:

    ```javascript
    this.columnDefs = [
      rulesColumnDefs.severity,
      {
        headerName: 'rules.grid.count',
        field: 'count'
      },
      {
        ...rulesColumnDefs.ruleName,
        minWidth: 200
      },
      rulesColumnDefs.explore
    ];
    ```

Poniższy zrzut ekranu przedstawia nową wersję **alerty** panelu:

![panelu alertów zaktualizowane](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Dostosowywanie wykresu telemetrii

Pliki w `src/components/pages/dashboard/panels/telemtry` folderu zdefiniować wykresu telemetrii na **pulpit nawigacyjny** strony. Interfejs użytkownika pobiera dane telemetryczne z zaplecza rozwiązania w `src/services/telemetryService.js` pliku. Poniższe kroki pokazują, jak zmienić okres wyświetlany na wykresie dane telemetryczne z 15, 5 minut:

1. W `src/services/telemetryService.js` plików, zlokalizować funkcji o nazwie **getTelemetryByDeviceIdP15M**. Utwórz kopię tej funkcji i modyfikować, kopiować w następujący sposób:

    ```javascript
    static getTelemetryByDeviceIdP5M(devices = []) {
      return TelemetryService.getTelemetryByMessages({
        from: 'NOW-PT5M',
        to: 'NOW',
        order: 'desc',
        devices
      });
    }
    ```

1. Aby użyć tej nowej funkcji do wypełnienia wykresu telemetrii, otwórz `src/components/pages/dashboard/dashboard.js` pliku. Znajdź wiersz, który inicjuje strumień danych telemetrycznych i zmodyfikować go w następujący sposób:

    ```javascript
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

Wykres danych telemetrycznych zawiera teraz pięć minut danych telemetrycznych:

![Dane telemetryczne wykres przedstawiający jeden dzień](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Dodaj nowy wskaźnik KPI

**Pulpit nawigacyjny** strony wyświetli wskaźniki KPI w **Analytics** panelu. Te kluczowe wskaźniki wydajności są obliczane w `src/components/pages/dashboard/dashboard.js` pliku. Kluczowe wskaźniki wydajności są renderowane przy `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` pliku. W poniższych krokach opisano sposób obliczania i renderować nową wartość wskaźnika KPI na **pulpit nawigacyjny** strony. Przykład pokazany jest dodawanie nowych wartość procentową zmiany w alerty ostrzegawcze kluczowy wskaźnik wydajności:

1. Otwórz plik `src/components/pages/dashboard/dashboard.js`. Modyfikowanie **stan początkowy** obiektu, aby uwzględnić **warningAlertsChange** właściwości w następujący sposób:

    ```javascript
    const initialState = {
      ...

      // Analytics data
      analyticsVersion: 0,
      currentActiveAlerts: [],
      topAlerts: [],
      alertsPerDeviceId: {},
      criticalAlertsChange: 0,
      warningAlertsChange: 0,
      analyticsIsPending: true,
      analyticsError: null

      ...
    };
    ```

1. Modyfikowanie **currentAlertsStats** obiektu, aby uwzględnić **totalWarningCount** jako właściwość:

    ```javascript
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alertsPerDeviceId: updatedAlertsPerDeviceId
    };
    ```

1. Oblicz nowy kluczowy wskaźnik wydajności. Znajdź obliczania liczby alertów krytycznych. Zduplikowany kod i modyfikować, kopiować w następujący sposób:

    ```javascript
    // ================== Warning Alerts Count - START
    const currentWarningAlerts = currentAlertsStats.totalWarningCount;
    const previousWarningAlerts = previousAlerts.reduce(
      (cnt, { severity }) => severity === Config.ruleSeverity.warning ? cnt + 1 : cnt,
      0
    );
    const warningAlertsChange = ((currentWarningAlerts - previousWarningAlerts) / currentWarningAlerts * 100).toFixed(2);
    // ================== Warning Alerts Count - END
    ```

1. Nowe **warningAlertsChange** kluczowego wskaźnika wydajności w usłudze stream kluczowy wskaźnik wydajności:

    ```javascript
    return ({
      analyticsIsPending: false,
      analyticsVersion: this.state.analyticsVersion + 1,

      // Analytics data
      currentActiveAlerts,
      topAlerts,
      criticalAlertsChange,
      warningAlertsChange,
      alertsPerDeviceId: currentAlertsStats.alertsPerDeviceId,

      ...
    });
    ```

1. Nowe **warningAlertsChange** kluczowy wskaźnik wydajności w danych o stanie używany do renderowania interfejsu użytkownika:

    ```javascript
    const {
      ...

      analyticsVersion,
      currentActiveAlerts,
      topAlerts,
      alertsPerDeviceId,
      criticalAlertsChange,
      warningAlertsChange,
      analyticsIsPending,
      analyticsError,

      ...
    } = this.state;
    ```

1. Aktualizuj dane przekazywane do panelu kluczowe wskaźniki wydajności:

    ```javascript
    <AnalyticsPanel
      timeSeriesExplorerUrl={timeSeriesParamUrl}
      topAlerts={topAlertsWithName}
      alertsPerDeviceId={alertsPerDeviceType}
      criticalAlertsChange={criticalAlertsChange}
      warningAlertsChange={warningAlertsChange}
      isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
      error={devicesError || rulesError || analyticsError}
      theme={theme}
      colors={chartColorObjects}
      t={t} />
    ```

Zmiany w został zakończony `src/components/pages/dashboard/dashboard.js` pliku. W poniższych krokach opisano zmiany, aby w `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` plik, aby wyświetlić nowy wskaźnik KPI:

1. Zmodyfikuj następujący wiersz kodu w celu pobrania nowej wartości wskaźnika KPI w następujący sposób:

    ```javascript
    const { t, isPending, criticalAlertsChange, warningAlertsChange, alertsPerDeviceId, topAlerts, timeSeriesExplorerUrl, error } = this.props;
    ```

1. Zmodyfikuj kod znaczników, aby wyświetlić nową wartość wskaźnika KPI w następujący sposób:

    ```javascript
    <div className="analytics-cell">
      <div className="analytics-header">{t('dashboard.panels.analytics.criticalAlerts')}</div>
      <div className="critical-alerts">
        {
          !showOverlay &&
            <div className="analytics-percentage-container">
              <div className="analytics-value">{ !isNaN(criticalAlertsChange) ? criticalAlertsChange : 0 }</div>
              <div className="analytics-percentage-sign">%</div>
            </div>
        }
      </div>
      <div className="critical-alerts">
        {
          !showOverlay &&
            <div className="analytics-percentage-container">
              <div className="analytics-value">{ !isNaN(warningAlertsChange) ? warningAlertsChange : 0 }</div>
              <div className="analytics-percentage-sign">%</div>
            </div>
        }
      </div>
    </div>
    ```

**Pulpit nawigacyjny** strony wyświetli teraz nową wartość wskaźnika KPI:

![Ostrzeżenie kluczowy wskaźnik wydajności](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>Dostosowywanie mapy

Zobacz [mapy Dostosuj](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) strony w usłudze GitHub, aby uzyskać szczegółowe informacje o składniki mapy w rozwiązaniu.

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Inne opcje dostosowywania

Dodatkowo zmodyfikować Warstwa prezentacji i wizualizacje w rozwiązaniu monitorowania zdalnego, należy edytować kod. Odpowiednich repozytoriów GitHub są następujące:

* [Mikrousługi konfiguracji dla rozwiązań IoT platformy Azure (.NET)](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config)
* [Mikrousługi konfiguracji dla rozwiązań IoT platformy Azure (Java)](https://github.com/Azure/remote-monitoring-services-java/tree/master/config)
* [Zdalne usługi Azure IoT komputerów monitorowanie internetowego interfejsu użytkownika](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano zasobów dostępnych do dostosowywania interfejsu użytkownika sieci web w akceleratora rozwiązania monitorowania zdalnego. Aby dowiedzieć się więcej o dostosowywaniu interfejsu użytkownika, zobacz następujące artykuły:

* [Dodawanie niestandardowej strony do monitorowania zdalnego rozwiązania akceleratora internetowego interfejsu użytkownika](iot-accelerators-remote-monitoring-customize-page.md)
* [Dodaj niestandardowe usługi do monitorowania zdalnego rozwiązania akceleratora internetowego interfejsu użytkownika](iot-accelerators-remote-monitoring-customize-service.md)
* [Dodaj niestandardowe siatki do monitorowania zdalnego rozwiązania akceleratora internetowego interfejsu użytkownika](iot-accelerators-remote-monitoring-customize-grid.md)
* [Dodaj niestandardowe okno wysuwane do monitorowania zdalnego rozwiązania akceleratora internetowego interfejsu użytkownika](iot-accelerators-remote-monitoring-customize-flyout.md)
* [Dodawanie panelu niestandardowe do pulpitu nawigacyjnego w zdalne monitorowanie rozwiązań akceleratora internetowego interfejsu użytkownika](iot-accelerators-remote-monitoring-customize-panel.md)

Aby uzyskać obszerniejszych informacji koncepcyjnych związanych akceleratora rozwiązania monitorowania zdalnego, zobacz [Architektura zdalnego monitorowania](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Aby uzyskać więcej informacji na temat dostosowywania mikrousługi rozwiązania monitorowania zdalnego, zobacz [dostosowywanie i ponowne wdrażanie mikrousługi](iot-accelerators-microservices-example.md).
<!-- Next tutorials in the sequence -->
