---
title: Dostosowywanie rozwiązania do zdalnego monitorowania interfejsu użytkownika — Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje dotyczące sposobu dostępu do kodu źródłowego dla akceleratora rozwiązania monitorowania zdalnego interfejsu użytkownika i pewne dostosowania.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2018
ms.topic: conceptual
ms.openlocfilehash: 59f2860168782d96bf82d0a27f9bb9eeed0f1020
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167498"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>Dostosowywanie akceleratora rozwiązań zdalnego monitorowania

Ten artykuł zawiera informacje dotyczące sposobu dostępu do kodu źródłowego i dostosować akcelerator rozwiązań monitorowania zdalnego interfejsu użytkownika. W artykule opisano:

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Przygotowywanie lokalnego środowiska deweloperskiego dla interfejsu użytkownika

Zdalne monitorowanie akceleratora rozwiązań kodu interfejsu użytkownika jest implementowany przy użyciu React.js framework. Można znaleźć kodu źródłowego w [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) repozytorium GitHub.

Aby wprowadzić zmiany w interfejsie użytkownika, można uruchomić lokalnie jego kopię. Kopia lokalna łączy się wdrożonego wystąpienia rozwiązania, aby wykonać akcje, takie jak pobieranie danych telemetrycznych.

Poniższe kroki przedstawiają procedurę konfigurowania lokalnego środowiska do tworzenia interfejsu użytkownika:

1. Wdrażanie **podstawowe** wystąpienie za pomocą akceleratora rozwiązań **komputerów** interfejsu wiersza polecenia. Zanotuj nazwę wdrożenia i poświadczenia podane dla maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [wdrażanie przy użyciu interfejsu wiersza polecenia](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Za pomocą witryny Azure portal lub [interfejsu wiersza polecenia az](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) aby umożliwić dostęp SSH do maszyny wirtualnej, która obsługuje mikrousługi w rozwiązaniu. Na przykład:

    ```sh
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Dostęp SSH należy włączyć tylko podczas projektowania i testowania. Po włączeniu protokołu SSH, [należy wyłączyć je ponownie tak szybko jak to możliwe](../security/azure-security-network-security-best-practices.md#disable-rdpssh-access-to-virtual-machines).

1. Za pomocą witryny Azure portal lub [interfejsu wiersza polecenia az](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) można znaleźć nazwę i publiczny adres IP swojej maszyny wirtualnej. Na przykład:

    ```sh
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Za pomocą protokołu SSH Połącz się z maszyną wirtualną przy użyciu adresu IP z poprzedniego kroku i poświadczenia podane po uruchomieniu **komputerów** do wdrożenia rozwiązania.

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

1. W wierszu polecenia w lokalnej kopii `azure-iot-pcs-remote-monitoring-webui` folder, uruchom następujące polecenia, aby instalowanie wymaganych bibliotek i uruchom lokalnie interfejsu użytkownika:

    ```cmd/sh
    npm install
    npm start
    ```

1. Poprzednie polecenie uruchamia interfejsu użytkownika lokalnie na http://localhost:3000/dashboard. Można edytować kod lokacji jest uruchomiona i zobaczyć, że są aktualizowane dynamicznie.

## <a name="customize-the-layout"></a>Dostosowywanie układu

Każda strona w rozwiązaniu do zdalnego monitorowania składa się z zestawem formantów, nazywane *panele* w kodzie źródłowym. Na przykład **pulpit nawigacyjny** strony składa się z pięciu panele: Przegląd, mapy, alarmy, Telemetrii i kluczowe wskaźniki wydajności. Można znaleźć kod źródłowy, który definiuje każdej strony i jego paneli w [komputerów z systemem remote-monitoring-interfejsem sieci Web](https://github.com/Azure/pcs-remote-monitoring-webui) repozytorium GitHub. Na przykład, kod, który definiuje **pulpit nawigacyjny** strony, jej układ i panele na tej stronie znajduje się w [src/składniki/stron/pulpit nawigacyjny](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard) folderu.

Ponieważ zespoły zarządzać swoimi układ i zmiany rozmiaru, można łatwo modyfikować układ strony. Na przykład, poniższe zmiany **PageContent** element `src/components/pages/dashboard/dashboard.js` pliku wymiany pozycji paneli mapy i dane telemetryczne i zmienić względną szerokość kontrolki mapy i panele kluczowy wskaźnik wydajności:

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

![Zmień układ panelu](./media/iot-accelerators-remote-monitoring-customize/layout.png)

> [!NOTE]
> Mapa nie jest skonfigurowany w przypadku wdrożenia lokalnego.

Można również dodać wiele wystąpień tego samego panel lub wielu wersji Jeśli możesz [zduplikowane i Dostosowywanie panelu](#duplicate-and-customize-an-existing-control). Poniższy przykład pokazuje, jak dodać dwa wystąpienia w panelu dane telemetryczne, edytując `src/components/pages/dashboard/dashboard.js` pliku:

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

Można wyświetlić różne dane telemetryczne w każdym panelu:

![Wielu paneli telemetrii](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

> [!NOTE]
> Mapa nie jest skonfigurowany w przypadku wdrożenia lokalnego.

## <a name="duplicate-and-customize-an-existing-control"></a>Zduplikowany i dostosowywania istniejącej kontrolki

Następujące kroki przedstawiają sposób użycia **alarmy** panelu, na przykład jak Duplikowanie istniejącego panelu, zmodyfikuj go i użyć zmodyfikowanej wersji:

1. W lokalnej kopii repozytorium, Utwórz kopię **alarmy** folderze `src/components/pages/dashboard/panels` folderu. Nadaj nazwę nowej kopii **cust_alarms**.

1. W **alarmsPanel.js** w pliku **cust_alarms** folderu, Zmień nazwę klasy, która ma być **CustAlarmsPanel**:

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

Teraz zastąpiono oryginalny **alarmy** panelu kopią o nazwie **CustAlarms**. Ta kopia jest taka sama jak oryginalne. Można teraz modyfikować, kopiować. Na przykład, aby zmienić kolejność kolumn w **alarmy** panelu:

1. Otwórz plik `src/components/pages/dashboard/panels/cust_alarms/alarmsPanel.js`.

1. Zmień definicje kolumn, jak pokazano w poniższym fragmencie kodu:

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

Poniższy zrzut ekranu przedstawia nową wersję **alarmy** panelu:

![Panelu alarmów zaktualizowane](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Dostosowywanie wykresu telemetrii

Wykres danych telemetrycznych na **pulpit nawigacyjny** strony jest definiowany przez pliki w `src/components/pages/dashboard/panels/telemtry` folderu. Interfejs użytkownika pobiera dane telemetryczne z zaplecza rozwiązania w `src/services/telemetryService.js` pliku. Poniższe kroki pokazują, jak zmienić okres wyświetlany na wykresie dane telemetryczne od 15 minut na 5 minut:

1. W `src/services/telemetryService.js` plików, zlokalizować funkcji o nazwie **getTelemetryByDeviceIdP15M**. Utwórz kopię tej funkcji i modyfikować, kopiować w następujący sposób:

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

1. Aby użyć tej nowej funkcji do wypełnienia wykresu telemetrii, otwórz `src/components/pages/dashboard/dashboard.js` pliku. Znajdź wiersz, który inicjuje strumień danych telemetrycznych i zmodyfikować go w następujący sposób:

    ```node.js
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

Wykres danych telemetrycznych zawiera teraz pięć minut danych telemetrycznych:

![Dane telemetryczne wykres przedstawiający jeden dzień](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Dodaj nowy wskaźnik KPI

**Pulpit nawigacyjny** strony wyświetli wskaźniki KPI w **kluczowych wskaźników wydajności systemu** panelu. Te kluczowe wskaźniki wydajności są obliczane w `src/components/pages/dashboard/dashboard.js` pliku. Kluczowe wskaźniki wydajności są renderowane przy `src/components/pages/dashboard/panels/kpis/kpisPanel.js` pliku. W poniższych krokach opisano sposób obliczania i renderować nową wartość wskaźnika KPI na **pulpit nawigacyjny** strony. Przykład pokazany jest dodawanie nowych wartość procentową zmiany w alarmy ostrzeżenia kluczowy wskaźnik wydajności:

1. Otwórz plik `src/components/pages/dashboard/dashboard.js`. Modyfikowanie **stan początkowy** obiektu, aby uwzględnić **warningAlarmsChange** właściwości w następujący sposób:

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

1. Modyfikowanie **currentAlarmsStats** obiektu, aby uwzględnić **totalWarningCount** jako właściwość:

    ```nodejs
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alarmsPerDeviceId: updatedAlarmsPerDeviceId
    };
    ```

1. Oblicz nowy kluczowy wskaźnik wydajności. Znajdź obliczania liczby krytyczne alarmy. Zduplikowany kod i modyfikować, kopiować w następujący sposób:

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

1. Nowe **warningAlarmsChange** kluczowego wskaźnika wydajności w usłudze stream kluczowy wskaźnik wydajności:

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
    ```

1. Nowe **warningAlarmsChange** kluczowy wskaźnik wydajności w danych o stanie używany do renderowania interfejsu użytkownika:

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

1. Aktualizuj dane przekazywane do panelu kluczowe wskaźniki wydajności:

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

Zostało zakończone zmiany w `src/components/pages/dashboard/dashboard.js` pliku. W poniższych krokach opisano zmiany, aby w `src/components/pages/dashboard/panels/kpis/kpisPanel.js` plik, aby wyświetlić nowy wskaźnik KPI:

1. Zmodyfikuj następujący wiersz kodu w celu pobrania nowej wartości wskaźnika KPI w następujący sposób:

    ```nodejs
    const { t, isPending, criticalAlarmsChange, warningAlarmsChange, error } = this.props;
    ```

1. Zmodyfikuj kod znaczników, aby wyświetlić nową wartość wskaźnika KPI w następujący sposób:

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
