---
title: Użyj magazynu obiektów blob dla usług IIS i tabeli magazynu dla zdarzeń w Azure Log Analytics | Dokumentacja firmy Microsoft
description: Analiza dzienników mogą odczytać w dziennikach usług platformy Azure, które zapisać diagnostyki magazynu tabel lub dzienniki programu IIS zapisywane do magazynu obiektów blob.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: bf444752-ecc1-4306-9489-c29cb37d6045
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 459ef90ca1d76bada6565bfefd7b4bd1086197d5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23855446"
---
# <a name="use-azure-blob-storage-for-iis-and-azure-table-storage-for-events-with-log-analytics"></a>Użyj magazynu obiektów blob platformy Azure dla usług IIS i Azure magazyn tabel zdarzeń o analizy dzienników

Analiza dzienników można przeczytać w dziennikach następujących usług, które zapisać diagnostyki magazynu tabel lub dzienniki programu IIS zapisywane do magazynu obiektów blob:

* Sieć szkieletowa usług klastrów (wersja zapoznawcza)
* Maszyny wirtualne
* Role sieć Web/proces roboczy.

Analiza dzienników można zbierać dane dla tych zasobów, należy włączyć diagnostyki Azure.

Po włączeniu diagnostyki, mogą korzystać z portalu Azure lub programu PowerShell skonfiguruj analizy dzienników do zbierania dzienników.

Diagnostyka Azure to rozszerzenie Azure, która umożliwia zbieranie danych diagnostycznych z rolą proces roboczy, roli sieci web lub maszynę wirtualną działającą na platformie Azure. Dane są przechowywane na koncie magazynu Azure i następnie mogą zostać zebrane przez analizy dzienników.

Dzienniki analizy dzienników do zbierania tych dzienników diagnostyki Azure, konieczne jest w następujących lokalizacjach:

| Typ dziennika | Typ zasobu | Lokalizacja |
| --- | --- | --- |
| Dzienniki usług IIS |Maszyny wirtualne <br> Role sieci Web <br> Role procesów roboczych |wad iis logfiles (magazynu obiektów Blob) |
| Dziennik systemu |Maszyny wirtualne |LinuxsyslogVer2v0 (Tabela magazynu) |
| Zdarzenia operacyjne sieci szkieletowej usług |Węzły sieci szkieletowej usług |WADServiceFabricSystemEventTable |
| Zdarzenia niezawodnego aktora sieci szkieletowej usług |Węzły sieci szkieletowej usług |WADServiceFabricReliableActorEventTable |
| Zdarzenia niezawodnej usługi sieci szkieletowej usług |Węzły sieci szkieletowej usług |WADServiceFabricReliableServiceEventTable |
| Dzienniki zdarzeń systemu Windows |Węzły sieci szkieletowej usług <br> Maszyny wirtualne <br> Role sieci Web <br> Role procesów roboczych |WADWindowsEventLogsTable (Table Storage) |
| Dzienniki zdarzeń systemu Windows ETW |Węzły sieci szkieletowej usług <br> Maszyny wirtualne <br> Role sieci Web <br> Role procesów roboczych |WADETWEventTable (Table Storage) |

> [!NOTE]
> Dzienniki programu IIS z witryn sieci Web Azure nie są obecnie obsługiwane.
>
>

Dla maszyn wirtualnych, istnieje możliwość zainstalowania [analizy dzienników agenta](log-analytics-azure-vm-extension.md) do maszyny wirtualnej, aby włączyć dodatkowe informacje szczegółowe. Oprócz możliwości analizować dzienniki zdarzeń i dzienniki programu IIS, można wykonywać dodatkowe analizy, w tym śledzenia zmian konfiguracji, SQL do oceny i oceny aktualizacji.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Włącz diagnostykę Azure na maszynie wirtualnej dla dziennika zdarzeń i IIS zbierania dzienników
Poniższa procedura umożliwia włączanie diagnostyki Azure na maszynie wirtualnej do dziennika zdarzeń i IIS zbierania dzienników przy użyciu portalu Microsoft Azure.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>Aby włączyć diagnostyki Azure na maszynie wirtualnej z portalu Azure
1. Zainstaluj agenta maszyny Wirtualnej, podczas tworzenia maszyny wirtualnej. Jeśli maszyna wirtualna już istnieje, sprawdź, czy Agent maszyny Wirtualnej jest już zainstalowany.

   * W portalu Azure, przejdź do maszyny wirtualnej, wybierz opcję **konfiguracji opcjonalnej**, następnie **diagnostyki** i ustaw **stan** do **na**.

     Po zakończeniu maszyna wirtualna ma rozszerzenie Azure Diagnostics zainstalowany i uruchomiony. To rozszerzenie jest odpowiedzialny za zbierania danych diagnostycznych.
2. Aby włączyć monitorowanie i skonfigurować rejestrowanie zdarzeń w istniejącej maszyny Wirtualnej. Można włączyć diagnostyki na poziomie maszyny Wirtualnej. Włącz diagnostykę, a następnie skonfiguruj rejestrowanie zdarzeń, wykonaj następujące czynności:

   1. Wybierz maszynę Wirtualną.
   2. Kliknij przycisk **monitorowania**.
   3. Kliknij przycisk **diagnostyki**.
   4. Ustaw **stan** do **ON**.
   5. Wybierz każdego dziennika diagnostyki, które mają być zbierane.
   6. Kliknij przycisk **OK**.

## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Włącz diagnostykę Azure w roli sieci Web dla usług IIS dziennika i zdarzenie kolekcji
Zapoznaj się [jak do włączenia diagnostyki w usłudze w chmurze](../cloud-services/cloud-services-dotnet-diagnostics.md) ogólne instrukcje na temat włączania diagnostyki Azure. Poniższe instrukcje te informacje i dostosować go do użytku z analizy dzienników.

Diagnostyka Azure włączone:

* Dzienniki programu IIS są przechowywane domyślnie przenoszone w odstępach czasu transferu scheduledTransferPeriod danych dziennika.
* Dzienniki zdarzeń systemu Windows nie są przesyłane domyślnie.

### <a name="to-enable-diagnostics"></a>Aby włączyć diagnostyki
Aby włączyć dzienniki zdarzeń systemu Windows lub zmienić scheduledTransferPeriod, skonfiguruj diagnostyki Azure za pomocą pliku konfiguracji XML (diagnostics.wadcfg), jak pokazano w [krok 4: Tworzenie pliku konfiguracji diagnostyki i zainstaluj rozszerzenie](../cloud-services/cloud-services-dotnet-diagnostics.md)

Przykładowy plik konfiguracji zbiera dzienniki programu IIS i wszystkich zdarzeń w dziennikach aplikacji i systemu:

```
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

Upewnij się, że Twoje appSettings określa konta magazynu, jak w poniższym przykładzie:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

**AccountName** i **AccountKey** wartości znajdują się w portalu Azure na pulpicie nawigacyjnym konta magazynu, w obszarze Zarządzanie kluczami dostępu. Protokół ciągu połączenia musi być **https**.

Po zaktualizowanej konfiguracji diagnostyczne jest stosowany do usługi w chmurze i zapisuje diagnostyki do magazynu Azure, następnie możesz przystąpić do konfigurowania analizy dzienników.

## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Zbieranie dzienników z usługi Azure Storage za pomocą portalu Azure
Azure portal umożliwiają skonfigurowanie analizy dzienników do zbierania dzienników dla następujących usług platformy Azure:

* Klastrów sieci szkieletowej usług
* Maszyny wirtualne
* Role sieć Web/proces roboczy.

W portalu Azure przejdź do obszaru roboczego analizy dzienników i wykonywać następujące zadania:

1. Kliknij przycisk *dzienników kont magazynu*
2. Kliknij przycisk *Dodaj* zadań
3. Wybierz konto magazynu, który zawiera dzienników diagnostycznych
   * To konto może być konto magazynu classic lub konta magazynu usługi Azure Resource Manager
4. Wybierz typ danych, które mają być zbierane w dziennikach
   * Dostępne są następujące dzienniki programu IIS; Zdarzenia; SYSLOG (Linux); Dzienniki zdarzeń systemu Windows; Zdarzenia sieci szkieletowej usług
5. Wartość źródła jest wypełniane automatycznie na podstawie typu danych i nie można jej zmienić
6. Kliknij przycisk OK, aby zapisać konfigurację

Powtórz kroki od 2 do 6 dla dodatkowych kont magazynu i typy danych, które mają analizy dzienników do zbierania.

W ciągu 30 minut będą mogli wyświetlić dane z konta magazynu w analizy dzienników. Wyświetlany tylko dane, które są zapisywane w pamięci masowej, po zastosowaniu konfiguracji. Analiza dzienników nie odczytywać dane istniejące konta magazynu.

> [!NOTE]
> Nie można zweryfikować portalu, czy źródło istnieje na koncie magazynu lub jeśli nowe dane zostają zapisane.
>
>

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Włącz diagnostykę Azure na maszynie wirtualnej dziennika zdarzeń i IIS dziennika kolekcję przy użyciu programu PowerShell
Wykonaj kroki w [Konfigurowanie analizy dzienników do indeksu usługi Azure diagnostics](log-analytics-powershell-workspace-configuration.md#configuring-log-analytics-to-index-azure-diagnostics) odczytywać diagnostycznych platformy Azure, które są zapisywane w magazynie tabel przy użyciu programu PowerShell.

Przy użyciu programu Azure PowerShell można bardziej precyzyjnie określić zdarzenia, które są zapisywane do magazynu Azure.
Aby uzyskać więcej informacji, zobacz [Włączanie diagnostyki w maszynach wirtualnych platformy Azure](../virtual-machines-dotnet-diagnostics.md).

Można włączyć i zaktualizować diagnostyki Azure za pomocą następujący skrypt programu PowerShell.
Ten skrypt można również używać z konfiguracji rejestrowania niestandardowego.
Zmodyfikuj skrypt można ustawić konta magazynu, nazwę usługi i nazwy maszyny wirtualnej.
Skrypt używa poleceń cmdlet klasycznej maszyn wirtualnych.

Przejrzyj następującym przykładowym skrypcie, skopiuj go, w razie potrzeby, Zapisz próbki jako plik skryptu programu PowerShell, a następnie uruchom skrypt.

```
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

    $wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```


## <a name="next-steps"></a>Następne kroki
* [Zbieranie dzienników i metryki dla usługi Azure](log-analytics-azure-storage.md) obsługiwanych usług platformy Azure.
* [Włącz rozwiązań](log-analytics-add-solutions.md) zapewnienie wglądu w dane.
* [Użyj zapytań wyszukiwania](log-analytics-log-searches.md) do analizowania danych.
