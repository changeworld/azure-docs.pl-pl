---
title: Magazyn obiektów blob na użytek usług IIS i magazynu table storage dla zdarzeń w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Usługa Azure Monitor może odczytywać dzienniki dla usług platformy Azure, które zapisać diagnostyki w usłudze table storage lub dzienniki programu IIS zapisywane do magazynu obiektów blob.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: bf444752-ecc1-4306-9489-c29cb37d6045
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/12/2017
ms.author: magoedte
ms.openlocfilehash: 901544886e0a0c90c29e83fc71f7a7a25ffc6862
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244884"
---
# <a name="collect-azure-diagnostic-logs-from-azure-storage"></a>Zbieraj dzienniki diagnostyczne platformy Azure z usługi Azure Storage

Usługa Azure Monitor może odczytywać dzienniki Aby uzyskać następujące usługi, które zapisać diagnostyki w usłudze table storage lub dzienniki programu IIS zapisywane do magazynu obiektów blob:

* Usługa Service Fabric clusters (wersja zapoznawcza)
* Maszyny wirtualne
* Role sieć Web/proces roboczy

Usługa Azure Monitor umożliwia zbieranie danych do obszaru roboczego usługi Log Analytics dla tych zasobów, należy włączyć diagnostyki platformy Azure.

Po włączeniu diagnostyki, można użyć witryny Azure portal lub programu PowerShell Konfiguruj obszar roboczy, aby zebrać dzienniki.

Narzędzie diagnostyczne systemu Azure to rozszerzenie platformy Azure, która umożliwia zbieranie danych diagnostycznych z roli proces roboczy, roli sieci web lub maszyny wirtualnej działającej na platformie Azure. Dane są przechowywane na koncie usługi Azure storage, a następnie mogą zostać zebrane przez usługi Azure Monitor.

Dla usługi Azure Monitor do zbierania tych dzienników diagnostyki platformy Azure dzienniki musi być w następujących lokalizacjach:

| Typ dziennika | Typ zasobu | Lokalizacja |
| --- | --- | --- |
| Dzienniki usług IIS |Maszyny wirtualne <br> Role sieci Web <br> Role procesów roboczych |Narzędzie diagnostyczne iis logfiles (magazyn obiektów Blob) |
| Dziennik systemu |Maszyny wirtualne |LinuxsyslogVer2v0 (Table Storage) |
| Zdarzeń operacyjnych usługi Service Fabric |Węzły usługi Service Fabric |WADServiceFabricSystemEventTable |
| Usługa Service Fabric Reliable Actor zdarzenia |Węzły usługi Service Fabric |WADServiceFabricReliableActorEventTable |
| Zdarzenia usługi Reliable Service Fabric usługi |Węzły usługi Service Fabric |WADServiceFabricReliableServiceEventTable |
| Dzienniki zdarzeń Windows |Węzły usługi Service Fabric <br> Maszyny wirtualne <br> Role sieci Web <br> Role procesów roboczych |WADWindowsEventLogsTable (Table Storage) |
| Dzienniki Windows ETW |Węzły usługi Service Fabric <br> Maszyny wirtualne <br> Role sieci Web <br> Role procesów roboczych |WADETWEventTable (Table Storage) |

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

Po zastosowaniu zaktualizowanej konfiguracji diagnostycznych do usługi w chmurze i zapisuje diagnostyczne do usługi Azure Storage, następnie można przystąpić do konfigurowania obszaru roboczego usługi Log Analytics.

## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Zbieranie dzienników z usługi Azure Storage za pomocą witryny Azure portal

Aby skonfigurować obszar roboczy usługi Log Analytics w usłudze Azure Monitor do zbierania dzienników dla następujących usług platformy Azure, można użyć witryny Azure portal:

* Klastry usługi Service Fabric
* Maszyny wirtualne
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

Powtórz kroki od 2 do 6 dla dodatkowych kont magazynu i typy danych, które mają być zbierane do obszaru roboczego.

W ciągu 30 minut jesteś w stanie wyświetlić dane z konta magazynu, w obszarze roboczym usługi Log Analytics. Widoczne są tylko dane, które są zapisywane do magazynu, po zastosowaniu konfiguracji. Obszar roboczy nie odczytuje już istniejące dane z konta magazynu.

> [!NOTE]
> Nie można zweryfikować portalu, że źródło istnieje na koncie magazynu lub jeśli nowe dane zostaną zapisane.
>
>

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Włącz diagnostykę platformy Azure na maszynie wirtualnej w dzienniku zdarzeń i IIS dziennika kolekcji przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Użyj kroków w [konfigurowania usługi Azure Monitor do indeksowania usługi Diagnostyka Azure](powershell-workspace-configuration.md#configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage) odczytywać diagnostyki platformy Azure, które są zapisywane w usłudze table storage przy użyciu programu PowerShell.

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


## <a name="next-steps"></a>Kolejne kroki

* [Zbieranie dzienników i metryk dla usług platformy Azure](collect-azure-metrics-logs.md) obsługiwanych usług platformy Azure.
* [Włączanie rozwiązań](../../azure-monitor/insights/solutions.md) zapewniające wgląd w dane.
* [Używają zapytań wyszukiwania](../../azure-monitor/log-query/log-query-overview.md) do analizowania danych.