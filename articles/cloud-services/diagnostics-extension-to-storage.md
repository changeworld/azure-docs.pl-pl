---
title: Przechowywanie i przeglądanie danych diagnostycznych w usłudze Azure Storage
description: Dowiedz się, jak zbierać dane diagnostyczne platformy Azure na koncie usługi Azure Storage, aby można było je wyświetlić za pomocą jednego z kilku dostępnych narzędzi.
services: azure-monitor
author: bwren
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/01/2016
ms.author: bwren
ms.subservice: diagnostic-extension
ms.openlocfilehash: 17430330d068fb55b45f073afecb8ba348286cb5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472676"
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Przechowywanie i przeglądanie danych diagnostycznych w usłudze Azure Storage
Dane diagnostyczne nie są przechowywane na stałe, chyba że zostaną przeniesione do emulatora magazynu platformy Microsoft Azure lub do magazynu platformy Azure. Po zakończeniu przechowywania można go wyświetlić za pomocą jednego z kilku dostępnych narzędzi.

## <a name="specify-a-storage-account"></a>Określanie konta magazynu
Konto magazynu, którego chcesz użyć, należy określić w pliku ServiceConfiguration.cscfg. Informacje o koncie są definiowane jako parametry połączenia w ustawieniu konfiguracji. W poniższym przykładzie przedstawiono domyślny ciąg połączenia utworzony dla nowego projektu usługi w chmurze w programie Visual Studio:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Można zmienić ten ciąg połączenia, aby zapewnić informacje o koncie dla konta magazynu platformy Azure.

W zależności od typu danych diagnostycznych, które są zbierane, usługa Azure Diagnostics używa usługi obiektów Blob lub usługi tabel. W poniższej tabeli przedstawiono źródła danych, które są utrwalone i ich format.

| Źródło danych | Format pamięci masowej |
| --- | --- |
| Dzienniki platformy Azure |Tabela |
| Dzienniki usługi IIS 7.0 |Obiekt blob |
| Dzienniki infrastruktury diagnostyki Azure |Tabela |
| Dzienniki śledzenia żądań nie powiodło się |Obiekt blob |
| Dzienniki zdarzeń systemu Windows |Tabela |
| Liczniki wydajności |Tabela |
| Zrzuty awaryjne |Obiekt blob |
| Dzienniki błędów niestandardowych |Obiekt blob |

## <a name="transfer-diagnostic-data"></a>Przesyłanie danych diagnostycznych
W przypadku zestawów SDK 2.5 i nowszych może wystąpić żądanie transferu danych diagnostycznych za pośrednictwem pliku konfiguracyjnego. Dane diagnostyczne można przesyłać w zaplanowanych odstępach czasu, jak określono w konfiguracji.

Dla SDK 2.4 i poprzedni można zażądać transferu danych diagnostycznych za pośrednictwem pliku konfiguracyjnego, jak również programowo. Podejście programowe pozwala również na transfery na żądanie.

> [!IMPORTANT]
> Podczas przesyłania danych diagnostycznych do konta magazynu platformy Azure, ponieść koszty zasobów magazynu, które używa danych diagnostycznych.
> 
> 

## <a name="store-diagnostic-data"></a>Przechowywanie danych diagnostycznych
Dane dziennika są przechowywane w magazynie obiektów Blob lub Table z następującymi nazwami:

**Tabele**

* **WadLogsTable** — dzienniki napisane w kodzie przy użyciu odbiornika śledzenia.
* **WADDiagnosticInfrastructureLogsTable** - Diagnostyka monitora i zmiany konfiguracji.
* **WADDirectoriesTable** — katalogi monitora diagnostycznego jest monitorowanie.  Obejmuje to dzienniki IIS, dzienniki żądań nie powiodło się usługi IIS i katalogi niestandardowe.  Lokalizacja pliku dziennika obiektu blob jest określona w polu Kontener, a nazwa obiektu blob znajduje się w polu Ścieżka względna.  Pole AbsolutePath wskazuje lokalizację i nazwę pliku w stanie nieuczonym na maszynie wirtualnej platformy Azure.
* **WADPerformanceCountersTable** – liczniki wydajności.
* **WADWindowsEventLogsTable** — dzienniki zdarzeń systemu Windows.

**Obiekty blob**

* **wad-control-container** — (tylko dla SDK 2.4 i poprzedni) Zawiera pliki konfiguracyjne XML, który kontroluje diagnostykę platformy Azure .
* **wad-iis-failedreqlogfiles** — zawiera informacje z dzienników żądań nieudanych iIS.
* **wad-iis-logfiles** — zawiera informacje o dziennikach IIS.
* **"custom"** — kontener niestandardowy oparty na konfigurowaniu katalogów, które są monitorowane przez monitor diagnostyczny.  Nazwa tego kontenera obiektu blob zostanie określona w waddirectoriestable.

## <a name="tools-to-view-diagnostic-data"></a>Narzędzia do wyświetlania danych diagnostycznych
Dostępnych jest kilka narzędzi do wyświetlania danych po ich przeniesieniu do magazynu. Przykład:

* Eksplorator serwera w programie Visual Studio — jeśli zainstalowano narzędzia platformy Azure dla programu Microsoft Visual Studio, możesz użyć węzła usługi Azure Storage w Eksploratorze serwera, aby wyświetlić dane obiektów blob i tabeli tylko do odczytu z kont magazynu platformy Azure. Można wyświetlać dane z konta emulatora magazynu lokalnego, a także z kont magazynu utworzonych dla platformy Azure. Aby uzyskać więcej informacji, zobacz [Przeglądanie zasobów magazynu i zarządzanie nimi za pomocą Eksploratora serwera](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) to autonomiczna aplikacja, która umożliwia łatwą pracę z danymi usługi Azure Storage w systemach Windows, OSX i Linux.
* [Usługa Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) zawiera usługę Azure Diagnostics Manager, która umożliwia wyświetlanie, pobieranie i zarządzanie danymi diagnostycznymi zebranymi przez aplikacje uruchomione na platformie Azure.

## <a name="next-steps"></a>Następne kroki
[Śledzenie przepływu w aplikacji usług w chmurze za pomocą diagnostyki platformy Azure](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)


