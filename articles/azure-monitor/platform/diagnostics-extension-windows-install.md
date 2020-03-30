---
title: Instalowanie i konfigurowanie rozszerzenia diagnostyki systemu Windows Azure (WAD)
description: Dowiedz się, jak zbierać dane diagnostyczne platformy Azure na koncie usługi Azure Storage, aby można było je wyświetlić za pomocą jednego z kilku dostępnych narzędzi.
services: azure-monitor
author: bwren
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.openlocfilehash: 929ab4109eb8d0e90b6c561a2135c0b7dd4205bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672263"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>Instalowanie i konfigurowanie rozszerzenia diagnostyki systemu Windows Azure (WAD)
Rozszerzenie diagnostyki platformy Azure jest agentem w usłudze Azure Monitor, który zbiera dane monitorowania z systemu operacyjnego gościa i obciążeń maszyn wirtualnych platformy Azure i innych zasobów obliczeniowych. Ten artykuł zawiera szczegółowe informacje na temat instalowania i konfigurowania rozszerzenia diagnostyki systemu Windows oraz opis sposobu przechowywania danych i konta usługi Azure Storage.

Rozszerzenie diagnostyki jest implementowane jako [rozszerzenie maszyny wirtualnej](../../virtual-machines/extensions/overview.md) na platformie Azure, więc obsługuje te same opcje instalacji przy użyciu szablonów Menedżera zasobów, programu PowerShell i interfejsu wiersza polecenia. Zobacz [rozszerzenia i funkcje maszyny wirtualnej dla systemu Windows,](../../virtual-machines/extensions/features-windows.md) aby uzyskać szczegółowe informacje na temat instalowania i obsługi rozszerzeń maszyn wirtualnych.

## <a name="install-with-azure-portal"></a>Instalowanie za pomocą portalu Azure
Rozszerzenie diagnostyki można zainstalować i skonfigurować na indywidualnej maszynie wirtualnej w witrynie Azure portal, która zapewnia interfejs, w przeciwieństwie do pracy bezpośrednio z konfiguracją. Po włączeniu rozszerzenia diagnostyki, automatycznie użyje domyślnej konfiguracji z najczęstszych liczników wydajności i zdarzeń. Tę domyślną konfigurację można zmodyfikować zgodnie z określonymi wymaganiami.

> [!NOTE]
> Istnieją ustawienia rozszerzenia diagnostyki, których nie można skonfigurować przy użyciu portalu Azure portal, w tym wysyłanie danych do usługi Azure Event Hubs. Dla tych ustawień należy użyć jednej z innych metod konfiguracji.

1. Otwórz menu maszyny wirtualnej w witrynie Azure portal.
2. Kliknij **ustawienia diagnostyczne** w sekcji **Monitorowanie** menu maszyny Wirtualnej.
3. Kliknij **pozycję Włącz monitorowanie na poziomie gościa,** jeśli rozszerzenie diagnostyki nie zostało jeszcze włączone.
4. Nowe konto usługi Azure Storage zostanie utworzone dla maszyny Wirtualnej o nazwie będzie oparta na nazwie grupy zasobów dla maszyny Wirtualnej. Maszynę wirtualną można dołączyć do innego konta magazynu, wybierając kartę **Agent.**

![Ustawienia diagnostyczne](media/diagnostics-extension-windows-install/diagnostic-settings.png)


Konfigurację domyślną można zmodyfikować po włączeniu rozszerzenia diagnostyki. W poniższej tabeli opisano opcje, które można modyfikować na różnych kartach. Niektóre opcje mają **niestandardowe** polecenie, które pozwala określić bardziej szczegółową konfigurację; Zobacz [schemat rozszerzenia diagnostyki systemu Windows, aby](diagnostics-extension-schema-windows.md) uzyskać szczegółowe informacje na temat różnych ustawień.

| Tab | Opis |
|:---|:---|
| Omówienie | Wyświetla bieżącą konfigurację z łączami do innych kart. |
| Liczniki wydajności | Wybierz liczniki wydajności do zebrania i częstotliwość próbkowania dla każdego z nich.  |
| Dzienniki | Wybierz dane dziennika do zbierania. Obejmuje to dzienniki zdarzeń systemu Windows, dzienniki usług IIS, dzienniki aplikacji .NET i zdarzenia ETW.  |
| Zrzuty awaryjne | Włącz zrzut awaryjny dla różnych procesów. |
| Zlewozmywaki | Włącz pochłaniacze danych, aby wysyłać dane do miejsc docelowych oprócz usługi Azure Storage.<br>Usługa Azure Monitor — wysyła dane o wydajności do metryk usługi Azure Monitor.<br>Usługa Application Insights — wysyłanie danych do aplikacji usługi Application Insights. |
| Agent | Zmodyfikuj następującą konfigurację agenta:<br>- Zmień konto magazynu.<br>- Określ maksymalny dysk lokalny używany dla agenta.<br>- Konfigurowanie dzienników dla kondycji samego agenta.|


> [!NOTE]
> Podczas gdy konfiguracja rozszerzenia diagnostyki może być sformatowana w języku JSON lub XML, każda konfiguracja wykonana w witrynie Azure portal będzie zawsze przechowywana jako JSON. Jeśli używasz XML z inną metodą konfiguracji, a następnie zmienić konfigurację z witryny Azure portal, ustawienia zostaną zmienione na JSON.

## <a name="resource-manager-template"></a>Szablon usługi Resource Manager
Zobacz [Używanie monitorowania i diagnostyki z szablonami maszyny Wirtualnej systemu Windows i usługi Azure Resource Manager](../../virtual-machines/extensions/diagnostics-template.md) przy wdrażaniu rozszerzenia diagnostyki za pomocą szablonów usługi Azure Resource Manager. 

## <a name="azure-cli-deployment"></a>Wdrożenie interfejsu wiersza polecenia platformy Azure
Interfejsu wiersza polecenia platformy Azure może służyć do wdrażania rozszerzenia diagnostyki platformy Azure do istniejącej maszyny wirtualnej przy użyciu [zestawu rozszerzeń az vm,](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set) jak w poniższym przykładzie. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

Chronione ustawienia są zdefiniowane w [PrivateConfig element](diagnostics-extension-schema-windows.md#privateconfig-element) schematu konfiguracji. Poniżej przedstawiono minimalny przykład pliku ustawień chronionych, który definiuje konto magazynu. Zobacz [przykładowa konfiguracja,](diagnostics-extension-schema-windows.md#privateconfig-element) aby uzyskać szczegółowe informacje na temat ustawień prywatnych.

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```
Ustawienia publiczne są zdefiniowane w [Public element](diagnostics-extension-schema-windows.md#publicconfig-element) schematu konfiguracji. Poniżej przedstawiono minimalny przykład pliku ustawień publicznych, który umożliwia zbieranie dzienników infrastruktury diagnostycznej, pojedynczy licznik wydajności i dziennik pojedynczych zdarzeń. Zobacz [przykład konfiguracji,](diagnostics-extension-schema-windows.md#publicconfig-element) aby uzyskać szczegółowe informacje na temat ustawień publicznych.

```JSON
{
  "StorageAccount": "mystorageaccount",
  "WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": 5120,
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": [
          {
            "counterSpecifier": "\\Processor Information(_Total)\\% Processor Time",
            "unit": "Percent",
            "sampleRate": "PT60S"
          }
        ]
      },
      "WindowsEventLog": {
        "scheduledTransferPeriod": "PT1M",
        "DataSource": [
          {
            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
          }
        ]
      }
    }
  }
}
```



## <a name="powershell-deployment"></a>Wdrożenie programu PowerShell
Program PowerShell może służyć do wdrażania rozszerzenia diagnostyki platformy Azure na istniejącej maszynie wirtualnej przy użyciu [Set-AzVMDiagnosticsExtension,](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) jak w poniższym przykładzie. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

Ustawienia prywatne są zdefiniowane w [PrivateConfig element](diagnostics-extension-schema-windows.md#privateconfig-element), podczas gdy ustawienia publiczne są zdefiniowane w [Public element](diagnostics-extension-schema-windows.md#publicconfig-element) schematu konfiguracji. Można również określić szczegóły konta magazynu jako parametry polecenia cmdlet Set-AzVMDiagnosticsExtension, a nie uwzględniać je w ustawieniach prywatnych.

Poniżej przedstawiono minimalny przykład pliku konfiguracji, który umożliwia zbieranie dzienników infrastruktury diagnostycznej, pojedynczy licznik wydajności i dziennik pojedynczych zdarzeń. Zobacz [przykładowa konfiguracja,](diagnostics-extension-schema-windows.md#publicconfig-element) aby uzyskać szczegółowe informacje na temat ustawień prywatnych i publicznych. 

```JSON
{
    "PublicConfig": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                "overallQuotaInMB": 10000
            },
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M",
                        "unit": "percent"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                    "DataSource": [
                    {
                        "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                    }
                ]
            }
        },
        "StorageAccount": "mystorageaccount",
        "StorageType": "TableAndBlob"
    },
    "PrivateConfig": {
        "storageAccountName": "mystorageaccount",
        "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
    }
}
```

Zobacz też [Używanie programu PowerShell do włączania diagnostyki platformy Azure na maszynie wirtualnej z systemem Windows](../../virtual-machines/extensions/diagnostics-windows.md).

## <a name="data-storage"></a>Magazyn danych
W poniższej tabeli wymieniono różne typy danych zebranych z rozszerzenia diagnostyki i czy są one przechowywane jako tabela lub obiekt blob. Dane przechowywane w tabelach mogą być również przechowywane w obiektach blob w zależności od [ustawienia StorageType](diagnostics-extension-schema-windows.md#publicconfig-element) w konfiguracji publicznej.


| Dane | Typ magazynu | Opis |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Tabela | Zmiany w monitorze diagnostycznym i konfiguracji. |
| WadDirectoriesTable | Tabela | Katalogi monitora diagnostycznego jest monitorowany.  Obejmuje to dzienniki IIS, dzienniki żądań nie powiodło się usługi IIS i katalogi niestandardowe.  Lokalizacja pliku dziennika obiektu blob jest określona w polu Kontener, a nazwa obiektu blob znajduje się w polu Ścieżka względna.  Pole AbsolutePath wskazuje lokalizację i nazwę pliku w stanie nieuczonym na maszynie wirtualnej platformy Azure. |
| Tabela WadLogsTable | Tabela | Dzienniki napisane w kodzie przy użyciu odbiornika śledzenia. |
| WADPerformanceCountersTable | Tabela | Liczniki wydajności. |
| WADWindowsEventLogsTable | Tabela | Dzienniki zdarzeń systemu Windows. |
| wad-iis-failedreqlogfiles | Obiekt blob | Zawiera informacje z dzienników żądań niepowodzenia w ucho. |
| pliki wad-iis-logfiles | Obiekt blob | Zawiera informacje o dziennikach usługi IIS. |
| "niestandardowe" | Obiekt blob | Kontener niestandardowy oparty na konfigurowaniu katalogów monitora diagnostycznego.  Nazwa tego kontenera obiektu blob zostanie określona w waddirectoriestable. |

## <a name="tools-to-view-diagnostic-data"></a>Narzędzia do wyświetlania danych diagnostycznych
Dostępnych jest kilka narzędzi do wyświetlania danych po ich przeniesieniu do magazynu. Przykład:

* Eksplorator serwera w programie Visual Studio — jeśli zainstalowano narzędzia platformy Azure dla programu Microsoft Visual Studio, możesz użyć węzła usługi Azure Storage w Eksploratorze serwera, aby wyświetlić dane obiektów blob i tabeli tylko do odczytu z kont magazynu platformy Azure. Można wyświetlać dane z konta emulatora magazynu lokalnego, a także z kont magazynu utworzonych dla platformy Azure. Aby uzyskać więcej informacji, zobacz [Przeglądanie zasobów magazynu i zarządzanie nimi za pomocą Eksploratora serwera](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) to autonomiczna aplikacja, która umożliwia łatwą pracę z danymi usługi Azure Storage w systemach Windows, OSX i Linux.
* [Usługa Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) zawiera usługę Azure Diagnostics Manager, która umożliwia wyświetlanie, pobieranie i zarządzanie danymi diagnostycznymi zebranymi przez aplikacje uruchomione na platformie Azure.

## <a name="next-steps"></a>Następne kroki
- Zobacz [Wysyłanie danych z rozszerzenia diagnostyki systemu Windows Azure do centrum zdarzeń, aby](diagnostics-extension-stream-event-hubs.md) uzyskać szczegółowe informacje na temat przekazywania danych monitorowania do usługi Azure Event Hubs.
