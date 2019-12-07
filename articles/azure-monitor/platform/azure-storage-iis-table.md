---
title: Korzystanie z usługi BLOB Storage na potrzeby usług IIS i magazynu tabel dla zdarzeń w Azure Monitor | Microsoft Docs
description: Azure Monitor może odczytać dzienniki usług platformy Azure, które zapisują dane diagnostyczne do magazynu tabel lub dzienników usług IIS zapisanych w usłudze BLOB Storage.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 04/12/2017
ms.openlocfilehash: 2d2d8d43cb5ee91810d639c4ee8925d185987954
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893556"
---
# <a name="collect-azure-resource-logs-from-azure-storage"></a>Zbieranie dzienników zasobów platformy Azure z usługi Azure Storage

Azure Monitor może odczytać dzienniki następujących usług, które zapisują dane diagnostyczne do magazynu tabel lub dzienników usług IIS zapisane w usłudze BLOB Storage:

* Usługa Service Fabric clusters (wersja zapoznawcza)
* Virtual Machines
* Role sieć Web/proces roboczy

Aby Azure Monitor mogły zbierać dane do Log Analytics obszaru roboczego dla tych zasobów, należy włączyć diagnostykę platformy Azure.

Po włączeniu diagnostyki można użyć Azure Portal lub programu PowerShell w celu skonfigurowania dzienników.

Narzędzie diagnostyczne systemu Azure to rozszerzenie platformy Azure, która umożliwia zbieranie danych diagnostycznych z roli proces roboczy, roli sieci web lub maszyny wirtualnej działającej na platformie Azure. Dane są przechowywane na koncie usługi Azure Storage, a następnie mogą być zbierane przez Azure Monitor.

Aby Azure Monitor zebrać te dzienniki Diagnostyka Azure, dzienniki muszą się znajdować w następujących lokalizacjach:

| Typ dziennika | Typ zasobu | Lokalizacja |
| --- | --- | --- |
| Dzienniki usług IIS |Virtual Machines <br> Role sieci Web <br> Role procesów roboczych |Narzędzie diagnostyczne iis logfiles (magazyn obiektów Blob) |
| Dziennik systemu |Virtual Machines |LinuxsyslogVer2v0 (Table Storage) |
| Zdarzeń operacyjnych usługi Service Fabric |Węzły usługi Service Fabric |WADServiceFabricSystemEventTable |
| Usługa Service Fabric Reliable Actor zdarzenia |Węzły usługi Service Fabric |WADServiceFabricReliableActorEventTable |
| Zdarzenia usługi Reliable Service Fabric usługi |Węzły usługi Service Fabric |WADServiceFabricReliableServiceEventTable |
| Dzienniki zdarzeń Windows |Węzły usługi Service Fabric <br> Virtual Machines <br> Role sieci Web <br> Role procesów roboczych |WADWindowsEventLogsTable (Table Storage) |
| Dzienniki Windows ETW |Węzły usługi Service Fabric <br> Virtual Machines <br> Role sieci Web <br> Role procesów roboczych |WADETWEventTable (Table Storage) |

> [!NOTE]
> Dzienniki usług IIS z witryn sieci Web platformy Azure nie są obecnie obsługiwane.
>
>

W przypadku maszyn wirtualnych ma możliwości zainstalowania [agenta usługi Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md) połączenie z maszyną wirtualną, aby włączyć dodatkowe informacje szczegółowe. Oprócz możliwości analizowania dzienników usług IIS i dzienniki zdarzeń, możesz wykonać dodatkową analizę, takich jak śledzenie zmian konfiguracji, ocena SQL i oceny aktualizacji.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Włącz diagnostykę platformy Azure na maszynie wirtualnej do dziennika zdarzeń i IIS zbierania dzienników

Poniższa procedura umożliwia Włącz diagnostykę platformy Azure na maszynie wirtualnej, do dziennika zdarzeń i IIS zbieranie dzienników przy użyciu portalu Microsoft Azure.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>Aby włączyć diagnostyki platformy Azure na maszynie wirtualnej w witrynie Azure portal

1. Zainstaluj agenta maszyny Wirtualnej, podczas tworzenia maszyny wirtualnej. Jeśli maszyna wirtualna już istnieje, sprawdź, czy Agent maszyny Wirtualnej jest już zainstalowany.

   * W witrynie Azure portal przejdź do maszyny wirtualnej, wybierz opcję **opcjonalna konfiguracja**, następnie **diagnostyki** i ustaw **stan** do **na** .

     Po zakończeniu maszyna wirtualna ma rozszerzenie diagnostyki platformy Azure zainstalowany i uruchomiony. To rozszerzenie jest odpowiedzialny za zbieranie danych diagnostycznych.
2. Aby włączyć monitorowanie i skonfigurować rejestrowanie zdarzeń w istniejącej maszyny Wirtualnej. Można włączyć diagnostykę na poziomie maszyny Wirtualnej. Aby włączyć diagnostykę, a następnie skonfiguruj rejestrowanie zdarzeń, wykonaj następujące czynności:

   1. Wybierz maszynę wirtualną.
   2. Kliknij przycisk **monitorowania**.
   3. Kliknij przycisk **diagnostyki**.
   4. Ustaw **stan** do **ON**.
   5. Zaznacz każdy dziennik diagnostyczny, które mają być zbierane.
   6. Kliknij przycisk **OK**.

## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Włącz diagnostykę platformy Azure w rolę sieci Web dla usług IIS zbierania dzienników i zdarzeń

Zapoznaj się [jak do włączenia diagnostyki w usłudze w chmurze](../../cloud-services/cloud-services-dotnet-diagnostics.md) ogólne kroki na temat włączania diagnostyki platformy Azure. W poniższych instrukcjach dzięki tym informacjom i dostosować go do użycia z usługą Log Analytics.

Z włączoną diagnostykę platformy Azure:

* Dzienniki usług IIS są przechowywane domyślnie przesłanych interwałem transferu okres zaplanowanego transferu danych dziennika.
* Dzienniki zdarzeń Windows nie są przesyłane domyślnie.

### <a name="to-enable-diagnostics"></a>Aby włączyć diagnostykę

Aby włączyć dzienniki zdarzeń Windows lub zmienić okres zaplanowanego transferu, konfigurowanie diagnostyki Azure za pomocą pliku konfiguracji XML (diagnostics.wadcfg), jak pokazano na [krok 4: Tworzenie pliku konfiguracji diagnostyki i zainstalować rozszerzenie](../../cloud-services/cloud-services-dotnet-diagnostics.md)

Przykładowy plik konfiguracji zbiera dzienniki usług IIS i wszystkie zdarzenia z dzienników aplikacji i systemu:

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

Upewnij się, że Twoje appSettings Określa konto magazynu, jak w poniższym przykładzie:

```xml
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

**AccountName** i **AccountKey** wartości znajdują się w witrynie Azure portal na pulpicie nawigacyjnym konta magazynu w obszarze Zarządzaj kluczami dostępu. Protokół dla parametrów połączenia musi być **https**.

Po zastosowaniu zaktualizowanej konfiguracji diagnostyki do usługi w chmurze i zapisaniu jej w usłudze Azure Storage, możesz przystąpić do konfigurowania obszaru roboczego Log Analytics.

## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Zbieranie dzienników z usługi Azure Storage za pomocą witryny Azure portal

Za pomocą Azure Portal można skonfigurować Log Analytics obszar roboczy w Azure Monitor, aby zebrać dzienniki dla następujących usług platformy Azure:

* Klastry usługi Service Fabric
* Virtual Machines
* Role sieć Web/proces roboczy

W witrynie Azure portal przejdź do obszaru roboczego usługi Log Analytics i wykonywać następujące zadania:

1. Kliknij przycisk *dzienniki kont magazynu*
2. Kliknij przycisk *Dodaj* zadań
3. Wybierz konto magazynu, który zawiera dzienniki diagnostyczne
   * To konto może być klasycznego konta magazynu lub konto magazynu usługi Azure Resource Manager
4. Wybierz typ danych, które chcesz zebrać dzienniki
   * Dostępne są następujące dzienniki usług IIS; Zdarzenia; SYSLOG (Linux); Dzienniki zdarzeń systemu Windows; Usługa Service Fabric zdarzenia
5. Wartość źródła jest automatycznie wypełniane na podstawie typu danych i nie można jej zmienić
6. Kliknij przycisk OK, aby zapisać konfigurację

Powtórz kroki 2-6 dla dodatkowych kont magazynu i typów danych, które mają być zbierane w obszarze roboczym.

W ciągu około 30 minut można zobaczyć dane z konta magazynu w obszarze roboczym Log Analytics. Widoczne są tylko dane, które są zapisywane do magazynu, po zastosowaniu konfiguracji. Obszar roboczy nie odczytuje wcześniej istniejących danych z konta magazynu.

> [!NOTE]
> Nie można zweryfikować portalu, że źródło istnieje na koncie magazynu lub jeśli nowe dane zostaną zapisane.
>
>

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Włącz diagnostykę platformy Azure na maszynie wirtualnej w dzienniku zdarzeń i IIS dziennika kolekcji przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Wykonaj kroki opisane w artykule [konfigurowanie Azure monitor, aby zindeksować diagnostykę Azure](powershell-workspace-configuration.md#configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage) , aby użyć programu PowerShell do odczytu z usługi Azure Diagnostics, która jest zapisywana w usłudze Table Storage.

Przy użyciu programu Azure PowerShell można bardziej precyzyjnie określić zdarzenia, które są zapisywane do usługi Azure Storage.
Aby uzyskać więcej informacji, zobacz [Włączanie diagnostyki w usłudze Azure Virtual Machines](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

Można włączyć i aktualizować diagnostyki platformy Azure za pomocą następującego skryptu programu PowerShell.
Umożliwia także ten skrypt z konfiguracją niestandardowego rejestrowania.
Zmodyfikuj skrypt można ustawić konta magazynu, nazwę usługi i nazwę maszyny wirtualnej.
Skrypt używa poleceń cmdlet dla klasycznych maszyn wirtualnych.

Przejrzyj następujący przykładowy skrypt, skopiuj go, zmodyfikuj go zgodnie z potrzebami, Zapisz plik jako plik skryptu programu PowerShell, a następnie uruchom skrypt.

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

* [Zbieranie dzienników i metryk dla usług platformy Azure](collect-azure-metrics-logs.md) obsługiwanych usług platformy Azure.
* [Włączanie rozwiązań](../../azure-monitor/insights/solutions.md) zapewniające wgląd w dane.
* [Używają zapytań wyszukiwania](../../azure-monitor/log-query/log-query-overview.md) do analizowania danych.