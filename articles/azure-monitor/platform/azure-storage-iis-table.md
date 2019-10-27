---
title: Korzystanie z usługi BLOB Storage na potrzeby usług IIS i magazynu tabel dla zdarzeń w Azure Monitor | Microsoft Docs
description: Azure Monitor może odczytać dzienniki usług platformy Azure, które zapisują dane diagnostyczne do magazynu tabel lub dzienników usług IIS zapisanych w usłudze BLOB Storage.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 04/12/2017
ms.openlocfilehash: 8f70ecc96269783c29c566fb89bd617f034316b1
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932671"
---
# <a name="collect-azure-diagnostic-logs-from-azure-storage"></a>Zbieranie dzienników diagnostycznych platformy Azure z usługi Azure Storage

Azure Monitor może odczytać dzienniki następujących usług, które zapisują dane diagnostyczne do magazynu tabel lub dzienników usług IIS zapisane w usłudze BLOB Storage:

* Klastry Service Fabric (wersja zapoznawcza)
* Virtual Machines
* Role sieci Web/procesu roboczego

Aby Azure Monitor mogły zbierać dane do Log Analytics obszaru roboczego dla tych zasobów, należy włączyć diagnostykę platformy Azure.

Po włączeniu diagnostyki można użyć Azure Portal lub programu PowerShell w celu skonfigurowania dzienników.

Diagnostyka Azure to rozszerzenie platformy Azure, które umożliwia zbieranie danych diagnostycznych z roli procesu roboczego, roli sieci Web lub maszyny wirtualnej działającej na platformie Azure. Dane są przechowywane na koncie usługi Azure Storage, a następnie mogą być zbierane przez Azure Monitor.

Aby Azure Monitor zebrać te dzienniki Diagnostyka Azure, dzienniki muszą się znajdować w następujących lokalizacjach:

| Typ dziennika | Typ zasobu | Lokalizacja |
| --- | --- | --- |
| Dzienniki usług IIS |Virtual Machines <br> Role sieci Web <br> Role procesu roboczego |funkcji wad-IIS-LogFiles (Blob Storage) |
| Dziennik systemu |Virtual Machines |LinuxsyslogVer2v0 (Table Storage) |
| Service Fabric zdarzenia operacyjne |Service Fabric węzły |WADServiceFabricSystemEventTable |
| Service Fabric niezawodne zdarzenia aktora |Service Fabric węzły |WADServiceFabricReliableActorEventTable |
| Service Fabric niezawodne zdarzenia usługi |Service Fabric węzły |WADServiceFabricReliableServiceEventTable |
| Dzienniki zdarzeń systemu Windows |Service Fabric węzły <br> Virtual Machines <br> Role sieci Web <br> Role procesu roboczego |WADWindowsEventLogsTable (Table Storage) |
| Dzienniki funkcji ETW systemu Windows |Service Fabric węzły <br> Virtual Machines <br> Role sieci Web <br> Role procesu roboczego |WADETWEventTable (Table Storage) |

> [!NOTE]
> Dzienniki usług IIS z usługi Azure Websites nie są obecnie obsługiwane.
>
>

W przypadku maszyn wirtualnych można zainstalować [agenta log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md) na maszynie wirtualnej, aby umożliwić dodatkowe szczegółowe informacje. Oprócz możliwości analizowania dzienników usług IIS i dzienników zdarzeń można przeprowadzić dodatkową analizę, w tym śledzenie zmian konfiguracji, ocenę SQL i ocenę aktualizacji.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Włączanie diagnostyki platformy Azure na maszynie wirtualnej w przypadku dzienników zdarzeń i zbierania dzienników usług IIS

Poniższa procedura umożliwia włączenie diagnostyki platformy Azure na maszynie wirtualnej na potrzeby zbierania dzienników zdarzeń i dzienników usług IIS przy użyciu Microsoft Azure Portal.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>Aby włączyć diagnostykę platformy Azure na maszynie wirtualnej z Azure Portal

1. Zainstaluj agenta maszyny wirtualnej podczas tworzenia maszyny wirtualnej. Jeśli maszyna wirtualna już istnieje, sprawdź, czy Agent maszyny wirtualnej jest już zainstalowany.

   * W Azure Portal przejdź do maszyny wirtualnej, wybierz opcję **Konfiguracja opcjonalna**, a następnie **Diagnostyka** i ustaw **stan** na **włączone**.

     Po zakończeniu maszyna wirtualna ma zainstalowane i uruchomione rozszerzenie Diagnostyka Azure. To rozszerzenie jest odpowiedzialne za gromadzenie danych diagnostycznych.
2. Włącz monitorowanie i Konfigurowanie rejestrowania zdarzeń na istniejącej maszynie wirtualnej. Można włączyć diagnostykę na poziomie maszyny wirtualnej. Aby włączyć diagnostykę, a następnie skonfigurować rejestrowanie zdarzeń, wykonaj następujące czynności:

   1. Wybierz maszynę wirtualną.
   2. Kliknij pozycję **monitorowanie**.
   3. Kliknij pozycję **Diagnostyka**.
   4. Ustaw **stan** na **włączone**.
   5. Wybierz każdy dziennik diagnostyczny, który chcesz zebrać.
   6. Kliknij przycisk **OK**.

## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Włącz diagnostykę platformy Azure w roli sieci Web dla dziennika i zbierania zdarzeń IIS

Zapoznaj się z [tematem Włączanie diagnostyki w usłudze w chmurze,](../../cloud-services/cloud-services-dotnet-diagnostics.md) aby zapoznać się z ogólnymi krokami dotyczącymi włączania diagnostyki Azure. Poniższe instrukcje wykorzystują te informacje i dostosowują je do użytku z Log Analytics.

Usługa Azure Diagnostics jest włączona:

* Dzienniki usług IIS są domyślnie przechowywane z danymi dziennika transferowanymi w interwale transferu scheduledTransferPeriod.
* Dzienniki zdarzeń systemu Windows nie są domyślnie transferowane.

### <a name="to-enable-diagnostics"></a>Aby włączyć diagnostykę

Aby włączyć dzienniki zdarzeń systemu Windows lub zmienić scheduledTransferPeriod, skonfiguruj Diagnostyka Azure przy użyciu pliku konfiguracyjnego XML (Diagnostics. wadcfg), jak pokazano w [kroku 4: Tworzenie pliku konfiguracji diagnostyki i Instalowanie rozszerzenia](../../cloud-services/cloud-services-dotnet-diagnostics.md)

Poniższy przykładowy plik konfiguracji zbiera dzienniki usług IIS i wszystkie zdarzenia z dzienników aplikacji i systemu:

```xml
    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>
```

Upewnij się, że ConfigurationSettings określa konto magazynu, tak jak w poniższym przykładzie:

```xml
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

Wartości **AccountName** i **AccountKey** są dostępne w Azure Portal na pulpicie nawigacyjnym konta magazynu w obszarze Zarządzaj kluczami dostępu. Protokół dla parametrów połączenia musi być typu **https**.

Po zastosowaniu zaktualizowanej konfiguracji diagnostyki do usługi w chmurze i zapisaniu jej w usłudze Azure Storage, możesz przystąpić do konfigurowania obszaru roboczego Log Analytics.

## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Używanie Azure Portal do zbierania dzienników z usługi Azure Storage

Za pomocą Azure Portal można skonfigurować Log Analytics obszar roboczy w Azure Monitor, aby zebrać dzienniki dla następujących usług platformy Azure:

* Klastry Service Fabric
* Virtual Machines
* Role sieci Web/procesu roboczego

W Azure Portal przejdź do obszaru roboczego Log Analytics i wykonaj następujące zadania:

1. Kliknij pozycję *dzienniki kont magazynu*
2. Kliknij przycisk *Dodaj* zadanie.
3. Wybierz konto magazynu zawierające dzienniki diagnostyki
   * To konto może być kontem klasycznego magazynu lub kontem magazynu Azure Resource Manager
4. Wybierz typ danych, dla których mają być zbierane dzienniki
   * Dostępne są dzienniki usług IIS; Wydarzeniach Dziennik systemu (Linux); Dzienniki ETW; Zdarzenia Service Fabric
5. Wartość dla źródła jest automatycznie wypełniana na podstawie typu danych i nie można jej zmienić.
6. Kliknij przycisk OK, aby zapisać konfigurację

Powtórz kroki 2-6 dla dodatkowych kont magazynu i typów danych, które mają być zbierane w obszarze roboczym.

W ciągu około 30 minut można zobaczyć dane z konta magazynu w obszarze roboczym Log Analytics. Po zastosowaniu konfiguracji będą widoczne tylko dane, które są zapisywane w magazynie. Obszar roboczy nie odczytuje wcześniej istniejących danych z konta magazynu.

> [!NOTE]
> Portal nie sprawdza, czy źródło istnieje na koncie magazynu lub czy nowe dane są zapisywane.
>
>

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Włączanie diagnostyki platformy Azure na maszynie wirtualnej w poszukiwaniu dzienników zdarzeń i zbierania dzienników usług IIS przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Wykonaj kroki opisane w artykule [konfigurowanie Azure monitor, aby zindeksować diagnostykę Azure](powershell-workspace-configuration.md#configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage) , aby użyć programu PowerShell do odczytu z usługi Azure Diagnostics, która jest zapisywana w usłudze Table Storage.

Za pomocą Azure PowerShell można dokładniej określić zdarzenia, które są zapisywane w usłudze Azure Storage.
Aby uzyskać więcej informacji, zobacz [Włączanie diagnostyki na platformie Azure Virtual Machines](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

Możesz włączyć i zaktualizować diagnostykę platformy Azure przy użyciu poniższego skryptu programu PowerShell.
Można również użyć tego skryptu z konfiguracją rejestrowania niestandardowego.
Zmodyfikuj skrypt, aby ustawić konto magazynu, nazwę usługi i nazwę maszyny wirtualnej.
Skrypt używa poleceń cmdlet dla klasycznych maszyn wirtualnych.

Zapoznaj się z poniższym przykładem skryptu, skopiuj go, zmodyfikuj zgodnie z wymaganiami, Zapisz przykład jako plik skryptu programu PowerShell, a następnie uruchom skrypt.

```powershell
    #Connect to Azure
    Add-AzureAccount

    # settings to change:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"

    #Construct Azure Diagnostics public config and convert to config format

    # Collect just system error events:
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

    #Construct Azure diagnostics private config

    $wad_storage_account_key = (Get-AzStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```


## <a name="next-steps"></a>Następne kroki

* [Zbieraj dzienniki i metryki dla usług platformy Azure](collect-azure-metrics-logs.md) dla obsługiwanych usług platformy Azure.
* [Umożliwiaj rozwiązanie](../../azure-monitor/insights/solutions.md) w celu zapewnienia wglądu w dane.
* Przeanalizuj dane przy [użyciu zapytań wyszukiwania](../../azure-monitor/log-query/log-query-overview.md) .