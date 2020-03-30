---
title: Dostosowywanie interfejsu użytkownika rozwiązania do zdalnego monitorowania — Azure | Dokumenty firmy Microsoft
description: Ten artykuł zawiera informacje o tym, jak można uzyskać dostęp do kodu źródłowego interfejsu użytkownika akceleratora rozwiązania zdalnego monitorowania i wprowadzić pewne dostosowania.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/09/2018
ms.topic: conceptual
ms.openlocfilehash: eb3d5fea68b5b1b6e648943cb3dbaab5857e9e07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68608010"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>Dostosowywanie akceleratora rozwiązań do zdalnego monitorowania

Ten artykuł zawiera informacje o tym, jak uzyskać dostęp do kodu źródłowego i dostosować interfejs użytkownika akceleratora rozwiązania zdalnego monitorowania.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Przygotowanie lokalnego środowiska programistycznego dla interfejsu użytkownika

Kod interfejsu użytkownika akceleratora rozwiązania zdalnego monitorowania jest implementowany przy użyciu struktury React.js. Kod źródłowy można znaleźć w repozytorium [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) GitHub.

Aby wprowadzić zmiany w interfejsie użytkownika, można uruchomić jego kopię lokalnie. Aby wykonać akcje, takie jak pobieranie danych telemetrycznych, kopia lokalna łączy się z wdrożonym wystąpieniem rozwiązania.

Następujące kroki przedstawiają proces konfigurowania środowiska lokalnego dla rozwoju interfejsu użytkownika:

1. Wdrażanie **podstawowego** wystąpienia akceleratora rozwiązań przy użyciu **interfejsu wiersza** polecenia komputera. Zanotuj nazwę wdrożenia i poświadczenia podane dla maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Wdrażanie przy użyciu interfejsu wiersza polecenia](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Aby włączyć dostęp SSH do maszyny wirtualnej, która obsługuje mikrousług w rozwiązaniu, użyj witryny Azure portal lub usługi Azure Cloud Shell. Przykład:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Włącz dostęp SSH tylko podczas testowania i rozwoju. Jeśli włączysz SSH, [należy go wyłączyć, gdy tylko skończysz go używać](../security/fundamentals/network-best-practices.md#disable-rdpssh-access-to-virtual-machines).

1. Użyj witryny Azure portal lub usługi Azure Cloud Shell, aby znaleźć nazwę i publiczny adres IP maszyny wirtualnej. Przykład:

    ```azurecli-interactive
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Użyj SSH, aby połączyć się z maszyną wirtualną. Użyj adresu IP z poprzedniego kroku i poświadczeń podanych podczas pracy **na komputerach w** celu wdrożenia rozwiązania. Polecenie `ssh` jest dostępne w usłudze Azure Cloud Shell.

1. Aby zezwolić lokalnemu środowisku użytkownika na łączenie się, uruchom następujące polecenia w powłoce bash na maszynie wirtualnej:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Po zakończeniu polecenia i uruchomieniu witryny sieci Web można odłączyć się od maszyny wirtualnej.

1. W lokalnej kopii repozytorium [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) edytuj plik **.env,** aby dodać adres URL wdrożonego rozwiązania:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. W wierszu polecenia przejdź do lokalnej `azure-iot-pcs-remote-monitoring-webui` kopii folderu.

1. Aby zainstalować wymagane biblioteki i uruchomić interfejs użytkownika lokalnie, uruchom następujące polecenia:

    ```cmd/sh
    npm install
    npm start
    ```

1. Poprzednie polecenie uruchamia interfejs użytkownika lokalnie\/pod adresem http: /localhost:3000/dashboard. Możesz edytować kod, gdy witryna jest uruchomiona i zobaczyć go dynamicznie.

## <a name="customize-the-layout"></a>Dostosowywanie układu

Każda strona w rozwiązaniu zdalnego monitorowania składa się z zestawu formantów, określanych jako *panele* w kodzie źródłowym. Strona **pulpitu nawigacyjnego** składa się z pięciu paneli: Przegląd, Mapa, Alerty, Telemetria i Analytics. Kod źródłowy definiujący każdą stronę i jej panele można znaleźć w repozytorium [GitHub pcs-remote-monitoring-webui.](https://github.com/Azure/pcs-remote-monitoring-webui) Na przykład kod definiujący stronę **pulpitu nawigacyjnego,** jego układ i panele na stronie znajduje się w folderze [src/components/pages/dashboard.](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard)

Ponieważ panele zarządzają własnym układem i rozmiarami, można łatwo zmodyfikować układ strony. W tym celu należy wprowadzić następujące `src/components/pages/dashboard/dashboard.js` zmiany w elemencie **PageContent** w pliku:

* Zamień pozycje map i paneli telemetrycznych.
* Zmienianie względnych szerokości paneli mapy i analiz.

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

![Zmienianie układu panelu](./media/iot-accelerators-remote-monitoring-customize/layout.png)

Można również dodać kilka wystąpień tego samego panelu lub kilka wersji, jeśli [zduplikujesz i dostosujesz panel](#duplicate-and-customize-an-existing-control). W poniższym przykładzie pokazano, jak dodać dwa wystąpienia panelu telemetrii. Aby wprowadzić te zmiany, edytuj `src/components/pages/dashboard/dashboard.js` plik:

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

Następnie można wyświetlić różne dane telemetryczne w każdym panelu:

![Wiele paneli telemetrycznych](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

## <a name="duplicate-and-customize-an-existing-control"></a>Duplikowanie i dostosowywanie istniejącego formantu

W poniższych krokach opisano sposób duplikowania istniejącego panelu, modyfikowania go, a następnie użycia zmodyfikowanej wersji. W krokach użyto panelu **alertów** jako przykładu:

1. W lokalnej kopii repozytorium należy wykonać kopię folderu **alertów** w folderze. `src/components/pages/dashboard/panels` Nazwij nową kopię **cust_alerts**.

1. W pliku **alertsPanel.js** w folderze **cust_alerts** edytuj nazwę klasy, która ma być **CustAlertsPanel**:

    ```javascript
    export class CustAlertsPanel extends Component {
    ```

1. Dodaj do `src/components/pages/dashboard/panels/index.js` pliku następujący wiersz:

    ```javascript
    export * from './cust_alerts';
    ```

1. Zastąp `alertsPanel` `CustAlertsPanel` w `src/components/pages/dashboard/dashboard.js` pliku:

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

Oryginalny panel **alertów** został zastąpiony kopią o nazwie **CustAlerts**. Ta kopia jest taka sama jak oryginał. Teraz można zmodyfikować kopię. Na przykład, aby zmienić kolejność kolumn w panelu **alerty:**

1. Otwórz plik `src/components/pages/dashboard/panels/cust_alerts/alertsPanel.js`.

1. Zmodyfikuj definicje kolumn, jak pokazano w poniższym urywek kodu:

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

Poniższy zrzut ekranu przedstawia nową wersję panelu **alertów:**

![Zaktualizowano panel alertów](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Dostosowywanie wykresu telemetrycznego

Pliki w `src/components/pages/dashboard/panels/telemtry` folderze definiują wykres telemetryczny na stronie **Pulpit nawigacyjny.** Interfejs użytkownika pobiera dane telemetryczne z zaplecza rozwiązania w `src/services/telemetryService.js` pliku. Poniższe kroki pokazują, jak zmienić okres wyświetlany na wykresie telemetrycznym z 15 do 5 minut:

1. W `src/services/telemetryService.js` pliku zlokalizuj funkcję o nazwie **getTelemetryByDeviceIdP15M**. Zrób kopię tej funkcji i zmodyfikuj kopię w następujący sposób:

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

1. Aby użyć tej nowej funkcji do wypełniania wykresu telemetryczego, otwórz `src/components/pages/dashboard/dashboard.js` plik. Zlokalizuj wiersz, który inicjuje strumień telemetrii i zmodyfikuj go w następujący sposób:

    ```javascript
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

Wykres telemetryczny pokazuje teraz pięć minut danych telemetrycznych:

![Wykres telemetryczny przedstawiający jeden dzień](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Dodawanie nowego kluczowego wskaźnika wydajności

Na stronie **pulpitu nawigacyjnego** są wyświetlane kluczowe wskaźniki wydajności w panelu **Analytics.** Te kluczowe wskaźniki wydajności `src/components/pages/dashboard/dashboard.js` są obliczane w pliku. Kluczowe wskaźniki wydajności są renderowane przez `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` plik. W poniższych krokach opisano sposób obliczania i renderowania nowej wartości kluczowego wskaźnika wydajności na stronie **pulpitu nawigacyjnego.** Pokazany przykład polega na dodaniu nowej zmiany procentowej w alertach ostrzegawczych WSKAŹNIK KPI:

1. Otwórz plik `src/components/pages/dashboard/dashboard.js`. Zmodyfikuj **obiekt initialState,** aby uwzględnić właściwość **warningAlertsChange** w następujący sposób:

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

1. Zmodyfikuj **bieżący ObiektAlertsStats,** aby uwzględnić **totalWarningCount** jako właściwość:

    ```javascript
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alertsPerDeviceId: updatedAlertsPerDeviceId
    };
    ```

1. Oblicz nowy kluczowy wskaźnik wydajności. Znajdź obliczenia dla liczby alertów krytycznych. Zduplikuj kod i zmodyfikuj kopię w następujący sposób:

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

1. Uwzględnij nowy wskaźnik KPI **ostrzeżeniaAlertsChange** w strumieniu kluczowych wskaźników wydajności:

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

1. Uwzględnij nowy wskaźnik KPI **ostrzeżeniaAlertsChange** w danych o stanie używanych do renderowania interfejsu użytkownika:

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

1. Zaktualizuj dane przekazane do panelu kluczowych wskaźników wydajności:

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

Zmiany w `src/components/pages/dashboard/dashboard.js` pliku zostały zakończone. W poniższych krokach opisano `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` zmiany wprowadzone w pliku w celu wyświetlenia nowego kluczowego wskaźnika wydajności:

1. Zmodyfikuj następujący wiersz kodu, aby pobrać nową wartość kluczowego wskaźnika wydajności w następujący sposób:

    ```javascript
    const { t, isPending, criticalAlertsChange, warningAlertsChange, alertsPerDeviceId, topAlerts, timeSeriesExplorerUrl, error } = this.props;
    ```

1. Zmodyfikuj znaczniki, aby wyświetlić nową wartość kluczowego wskaźnika wydajności w następujący sposób:

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

Na stronie **pulpitu nawigacyjnego** jest teraz wyświetlana nowa wartość kluczowego wskaźnika wydajności:

![Wskaźnik KPI ostrzeżenia](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>Dostosowywanie mapy

Zobacz [dostosowywanie](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) strony mapy w usłudze GitHub, aby uzyskać szczegółowe informacje na temat składników mapy w rozwiązaniu.

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Inne opcje dostosowywania

Aby jeszcze bardziej zmodyfikować warstwę prezentacji i wizualizacji w rozwiązaniu Zdalne monitorowanie, można edytować kod. Odpowiednie repozytoria GitHub to:

* [Mikrousługa konfiguracji dla rozwiązań IoT usługi Azure (.NET)](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config)
* [Mikrousługa konfiguracji dla rozwiązań IoT usługi Azure (Java)](https://github.com/Azure/remote-monitoring-services-java/tree/master/config)
* [Interfejs użytkownika sieci Web zdalnego monitorowania usług IoT PCS usługi Azure IoT](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się o dostępnych zasobach ułatwiające dostosowanie interfejsu użytkownika sieci Web w akceleratorze rozwiązań do zdalnego monitorowania. Aby dowiedzieć się więcej na temat dostosowywania interfejsu użytkownika, zobacz następujące artykuły:

* [Dodawanie strony niestandardowej do interfejsu użytkownika internetowego akceleratora rozwiązania zdalnego monitorowania](iot-accelerators-remote-monitoring-customize-page.md)
* [Dodawanie usługi niestandardowej do interfejsu użytkownika sieci Web akceleratora rozwiązań do zdalnego monitorowania](iot-accelerators-remote-monitoring-customize-service.md)
* [Dodawanie siatki niestandardowej do interfejsu użytkownika sieci Web akceleratora rozwiązania do zdalnego monitorowania](iot-accelerators-remote-monitoring-customize-grid.md)
* [Dodawanie niestandardowego wysuwu do interfejsu użytkownika sieci Web akceleratora rozwiązania zdalnego monitorowania](iot-accelerators-remote-monitoring-customize-flyout.md)
* [Dodawanie panelu niestandardowego do pulpitu nawigacyjnego w interfejsie użytkownika sieci Web akceleratora rozwiązania zdalnego monitorowania](iot-accelerators-remote-monitoring-customize-panel.md)

Aby uzyskać więcej informacji koncepcyjnych dotyczących akceleratora rozwiązań do zdalnego monitorowania, zobacz [Architektura zdalnego monitorowania](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Aby uzyskać więcej informacji na temat dostosowywania mikrousług rozwiązania zdalnego monitorowania, zobacz [Dostosowywanie i ponowne wdrożenie mikrousługi.](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->
