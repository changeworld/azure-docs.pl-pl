---
title: Dostosowywanie monitorowania zdalnego rozwiązania UI - Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje dotyczące sposobu dostęp do kodu źródłowego akcelerator rozwiązań monitorowania zdalnego interfejsu użytkownika i dostosować.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/17/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 94bd864215ad80b299db09b6237f2cebe63feb88
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>Dostosowywanie akcelerator rozwiązań monitorowania zdalnego

Ten artykuł zawiera informacje dotyczące sposobu można uzyskać dostępu do kodu źródłowego i dostosować akcelerator rozwiązań monitorowania zdalnego interfejsu użytkownika. Artykuł opisuje:

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Przygotuj Środowisko deweloperskie lokalnego interfejsu użytkownika

Akcelerator rozwiązań monitorowania zdalnego interfejsu użytkownika kod jest implementowane za pomocą React.js framework. Można znaleźć kodu źródłowego w [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) repozytorium GitHub.

Aby wprowadzić zmiany w Interfejsie użytkownika, można uruchomić lokalnie jego kopię. Kopia lokalna łączy się wdrożone wystąpienie rozwiązania wykonuje działania takie jak pobieranie danych telemetrycznych.

Poniższe kroki wchodzą w skład procesu konfigurowania lokalnego środowiska do tworzenia interfejsu użytkownika:

1. Wdrażanie **podstawowe** wystąpienia przy użyciu akceleratora rozwiązań **komputerów** interfejsu wiersza polecenia. Zanotuj nazwę wdrożenia, a poświadczenia podane dla maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [wdrażanie przy użyciu interfejsu wiersza polecenia](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Użyj portalu Azure lub [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) umożliwiające SSH dostęp do maszyny wirtualnej, która obsługuje mikrousług w rozwiązaniu. Na przykład:

    ```sh
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

1. Użyj portalu Azure lub [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) można znaleźć nazwy i publicznego adresu IP maszyny wirtualnej. Na przykład:

    ```sh
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Używanie protokołu SSH, aby połączyć się z maszyną wirtualną przy użyciu adresu IP z poprzedniego kroku, a poświadczenia podane po uruchomieniu **komputerów** do wdrożenia rozwiązania.

1. Umożliwia lokalnego UX się połączyć, uruchom następujące polecenia w powłoki bash w maszynie wirtualnej:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Po zakończeniu wykonywania polecenia i uruchamiania witryny sieci web, możesz odłączyć od maszyny wirtualnej.

1. W lokalnej kopii [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) repozytorium, Edytuj **.env** plik, aby dodać adres URL wdrożonej rozwiązania:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. W wierszu polecenia w lokalnej kopii `azure-iot-pcs-remote-monitoring-webui` folderu, uruchom następujące polecenia do zainstalowania wymaganych bibliotek i uruchomienia interfejsu użytkownika lokalnie:

    ```cmd/sh
    npm install
    npm start
    ```

1. Poprzednie polecenie jest uruchamiane lokalnie w interfejsie użytkownika http://localhost:3000/dashboard. Można edytować kod lokacji jest uruchomiona i wyświetlenia go zaktualizować dynamicznie.

## <a name="customize-the-layout"></a>Dostosowywanie układu

Każdej strony w rozwiązaniu monitorowania zdalnego składa się z zestaw kontrolek, określany jako *panele* w kodzie źródłowym. Na przykład **pulpitu nawigacyjnego** strony składa się z pięciu panele: omówienie, mapy, alarmy, Telemetrii i wskaźników KPI. Możesz znaleźć kod źródłowy, który definiuje każdej strony i jego paneli w [komputerów remote monitorowania webui](https://github.com/Azure/pcs-remote-monitoring-webui) repozytorium GitHub. Na przykład kod, który definiuje **pulpitu nawigacyjnego** strony, jego układ i panele na tej stronie znajduje się w [src/składniki/stron/pulpit nawigacyjny](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard) folderu.

Ponieważ panele zarządzać swoimi układ i zmiany rozmiaru, można łatwo zmodyfikować układ strony. Na przykład poniższe zmiany **PageContent** element `src/components/pages/dashboard/dashboard.js` pliku wymiany pozycji paneli mapy i dane telemetryczne i zmienić względną szerokość mapy i panele wskaźnika KPI:

```nodejs
<PageContent className="dashboard-container" key="page-content">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={kpisIsPending || devicesIsPending}
        error={devicesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-5">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-4">
    <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlarm={devicesInAlarm}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || kpisIsPending}
          error={azureMapsKeyError || devicesError || kpisError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <KpisPanel
        topAlarms={topAlarmsWithName}
        alarmsPerDeviceId={alarmsPerDeviceType}
        criticalAlarmsChange={criticalAlarmsChange}
        warningAlarmsChange={warningAlarmsChange}
        isPending={kpisIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || kpisError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
  </Grid>
</PageContent>
```

![Układ panelu zmiany](./media/iot-accelerators-remote-monitoring-customize/layout.png)

> [!NOTE]
> Nie skonfigurowano mapy w ramach wdrożenia lokalnego.

Można również dodać wiele wystąpień tego samego panelu lub wielu wersji Jeśli możesz [zduplikowany i dostosować panelu](#duplicate-and-customize-an-existing-control). Poniższy przykład przedstawia sposób Dodaj dwa wystąpienia panelu dane telemetryczne, edytując `src/components/pages/dashboard/dashboard.js` pliku:

```nodejs
<PageContent className="dashboard-container" key="page-content">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={kpisIsPending || devicesIsPending}
        error={devicesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-2">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-4">
    <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlarm={devicesInAlarm}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || kpisIsPending}
          error={azureMapsKeyError || devicesError || kpisError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <KpisPanel
        topAlarms={topAlarmsWithName}
        alarmsPerDeviceId={alarmsPerDeviceType}
        criticalAlarmsChange={criticalAlarmsChange}
        warningAlarmsChange={warningAlarmsChange}
        isPending={kpisIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || kpisError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
  </Grid>
</PageContent>
```

Można wyświetlić dane telemetryczne różnych w każdym panelu:

![Wiele paneli telemetrii](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

> [!NOTE]
> Nie skonfigurowano mapy w ramach wdrożenia lokalnego.

## <a name="duplicate-and-customize-an-existing-control"></a>Zduplikowana i dostosować istniejącego formantu

Poniższe kroki przedstawiają sposób użycia **alarmy** panelu, na przykład jak Duplikowanie istniejącego panelu, zmodyfikuj go i używać zmodyfikowanej wersji:

1. W lokalnej kopii repozytorium, wykonaj kopię **alarmy** folderu w `src/components/pages/dashboard/panels` folderu. Nazwa nowej kopii **cust_alarms**.

1. W **alarmsPanel.js** w pliku **cust_alarms** folder, Zmień nazwę klasy, która ma być **CustAlarmsPanel**:

    ```nodejs
    export class CustAlarmsPanel extends Component {
    ```

1. Dodaj następujący wiersz do `src/components/pages/dashboard/panels/index.js` pliku:

    ```nodejs
    export * from './cust_alarms';
    ```

1. Zastąp `AlarmsPanel` z `CustAlarmsPanel` w `src/components/pages/dashboard/dashboard.js` pliku:

    ```nodejs
    import {
      OverviewPanel,
      CustAlarmsPanel,
      TelemetryPanel,
      KpisPanel,
      MapPanel,
      transformTelemetryResponse,
      chartColors
    } from './panels';

    ...

    <Cell className="col-3">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    ```

Teraz zastąpienia oryginalnej **alarmy** panelu z kopią o nazwie **CustAlarms**. Ta kopia jest takie same jak oryginalne. Można również zmodyfikować kopii. Na przykład, aby zmienić kolejność kolumn w **alarmy** panelu:

1. Otwórz plik `src/components/pages/dashboard/panels/cust_alarms/alarmsPanel.js`.

1. Zmień definicje kolumn, jak pokazano w poniższy fragment kodu:

    ```nodejs
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

Poniższy zrzut ekranu przedstawia nowej wersji **alarmy** panelu:

![Alarmy panelu aktualizacji](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Dostosuj wykres telemetrii

Wykres telemetrii na **pulpitu nawigacyjnego** strony jest definiowana za pomocą plików w `src/components/pages/dashboard/panels/telemtry` folderu. Interfejs użytkownika pobiera dane telemetryczne z zaplecza rozwiązania w `src/services/telemetryService.js` pliku. Poniższe kroki pokazują, jak zmienić okres wyświetlany na wykresie telemetrii od 15 minut na 5 minut:

1. W `src/services/telemetryService.js` plików, zlokalizować funkcji o nazwie **getTelemetryByDeviceIdP15M**. Utwórz kopię tej funkcji i zmodyfikuj kopię w następujący sposób:

    ```nodejs
    static getTelemetryByDeviceIdP5M(devices = []) {
      return TelemetryService.getTelemetryByMessages({
        from: 'NOW-PT5M',
        to: 'NOW',
        order: 'desc',
        devices
      });
    }
    ```

1. Aby użyć tej nowej funkcji do wypełnienia wykresu telemetrii, otwórz `src/components/pages/dashboard/dashboard.js` pliku. Odszukaj wiersz, który inicjuje strumienia danych telemetrii, a następnie zmodyfikować go w następujący sposób:

    ```node.js
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

Wykres telemetrii pokazuje teraz pięciu minut od danych telemetrii:

![Dane telemetryczne wykres przedstawiający jeden dzień](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Dodaj nowy kluczowy wskaźnik wydajności

**Pulpitu nawigacyjnego** kluczowych wskaźników wydajności zostanie wyświetlona strona **kluczowych wskaźników wydajności systemu** panelu. Te wskaźniki KPI są obliczane w `src/components/pages/dashboard/dashboard.js` pliku. Kluczowe wskaźniki wydajności są renderowane przez `src/components/pages/dashboard/panels/kpis/kpisPanel.js` pliku. W poniższych krokach opisano sposób obliczania i renderowania na nową wartość wskaźnika KPI **pulpitu nawigacyjnego** strony. Przykład pokazany jest dodać nową zmianę procent w alarmy ostrzeżenia wskaźnika KPI:

1. Otwórz plik `src/components/pages/dashboard/dashboard.js`. Modyfikowanie **stan początkowy** obiekt, aby uwzględnić **warningAlarmsChange** właściwości w następujący sposób:

    ```nodejs
    const initialState = {
      ...

      // Kpis data
      currentActiveAlarms: [],
      topAlarms: [],
      alarmsPerDeviceId: {},
      criticalAlarmsChange: 0,
      warningAlarmsChange: 0,
      kpisIsPending: true,
      kpisError: null,

      ...
    };
    ```

1. Modyfikowanie **currentAlarmsStats** obiekt, aby uwzględnić **totalWarningCount** jako właściwość:

    ```nodejs
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alarmsPerDeviceId: updatedAlarmsPerDeviceId
    };
    ```

1. Oblicz nowy kluczowy wskaźnik wydajności. Znajdź obliczania liczby alarmy krytyczne. Zduplikowany kod i zmodyfikuj kopię w następujący sposób:

    ```nodejs
    // ================== Warning Alarms Count - START
    const currentWarningAlarms = currentAlarmsStats.totalWarningCount;
    const previousWarningAlarms = previousAlarms.reduce(
      (cnt, { severity }) => severity === 'warning' ? cnt + 1 : cnt,
      0
    );
    const warningAlarmsChange = ((currentWarningAlarms - previousWarningAlarms) / currentWarningAlarms * 100).toFixed(2);
    // ================== Warning Alarms Count - END
    ```

1. Nowe **warningAlarmsChange** wskaźnika KPI w strumieniu wskaźnika KPI:

    ```nodejs
    return ({
      kpisIsPending: false,

      // Kpis data
      currentActiveAlarms,
      topAlarms,
      criticalAlarmsChange,
      warningAlarmsChange,
      alarmsPerDeviceId: currentAlarmsStats.alarmsPerDeviceId,

      ...
    });

1. Include the new **warningAlarmsChange** KPI in the state data used to render the UI:

    ```nodejs
    const {
      ...

      currentActiveAlarms,
      topAlarms,
      alarmsPerDeviceId,
      criticalAlarmsChange,
      warningAlarmsChange,
      kpisIsPending,
      kpisError,

      ...
    } = this.state;
    ```

1. Dane przekazywane do wskaźników KPI panelu aktualizacji:

    ```node.js
    <KpisPanel
      topAlarms={topAlarmsWithName}
      alarmsPerDeviceId={alarmsPerDeviceType}
      criticalAlarmsChange={criticalAlarmsChange}
      warningAlarmsChange={warningAlarmsChange}
      isPending={kpisIsPending || rulesIsPending || devicesIsPending}
      error={devicesError || rulesError || kpisError}
      colors={chartColorObjects}
      t={t} />
    ```

Zostało zakończone zmiany `src/components/pages/dashboard/dashboard.js` pliku. W poniższych krokach opisano zmiany, aby w `src/components/pages/dashboard/panels/kpis/kpisPanel.js` plik, aby wyświetlić nowy kluczowy wskaźnik wydajności:

1. Zmodyfikuj następujący wiersz kodu, aby pobrać nową wartość wskaźnika KPI w następujący sposób:

    ```nodejs
    const { t, isPending, criticalAlarmsChange, warningAlarmsChange, error } = this.props;
    ```

1. Zmień kod znaczników, aby wyświetlić nową wartość wskaźnika KPI w następujący sposób:

    ```nodejs
    <div className="kpi-cell">
      <div className="kpi-header">{t('dashboard.panels.kpis.criticalAlarms')}</div>
      <div className="critical-alarms">
        {
          criticalAlarmsChange !== 0 &&
            <div className="kpi-percentage-container">
              <div className="kpi-value">{ criticalAlarmsChange }</div>
              <div className="kpi-percentage-sign">%</div>
            </div>
        }
      </div>
      <div className="kpi-header">{t('Warning alarms')}</div>
      <div className="critical-alarms">
        {
          warningAlarmsChange !== 0 &&
            <div className="kpi-percentage-container">
              <div className="kpi-value">{ warningAlarmsChange }</div>
              <div className="kpi-percentage-sign">%</div>
            </div>
        }
      </div>
    </div>
    ```

**Pulpitu nawigacyjnego** teraz zostanie wyświetlona nowa wartość wskaźnika KPI:

![Ostrzeżenie wskaźnika KPI](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>Dostosowywanie mapy

Zobacz [mapy Dostosuj](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) strony w witrynie GitHub szczegółowe składników mapy w rozwiązaniu.

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Inne opcje dostosowywania

Aby modyfikować Warstwa prezentacji i wizualizacje w rozwiązaniu Monitorowanie zdalne, można edytować kodu. Odpowiednich repozytoriów GitHub są:

* [Mikrousługi konfiguracji dla rozwiązania IoT Azure (.NET)](https://github.com/Azure/pcs-ui-config-dotnet/)
* [Mikrousługi konfiguracji dla rozwiązania IoT Azure (Java)](https://github.com/Azure/pcs-ui-config-java/)
* [Azure IoT komputery zdalne monitorowanie interfejsu użytkownika sieci Web](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono o zasoby dostępne do dostosowywania interfejsu użytkownika sieci web w akcelerator rozwiązań monitorowania zdalnego.

Więcej informacje koncepcyjne o akcelerator rozwiązań monitorowania zdalnego dla [monitorowania zdalnego architektury](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Aby uzyskać więcej informacji dotyczących dostosowywania rozwiązanie monitorowania zdalnego, zobacz [dostosowywanie i wdróż go ponownie mikrousługi](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->