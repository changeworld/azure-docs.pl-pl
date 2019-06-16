---
title: Przechowywanie i przeglądanie danych diagnostycznych w usłudze Azure Storage
description: Pobierz dane diagnostyczne platformy Azure do usługi Azure Storage i wyświetlanie jej
services: azure-monitor
author: jpconnock
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/01/2016
ms.author: jeconnoc
ms.subservice: diagnostic-extension
ms.openlocfilehash: 23379e9d9bb29efb7fb026260e8245e8eb8a2d71
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60395071"
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Przechowywanie i przeglądanie danych diagnostycznych w usłudze Azure Storage
Dane diagnostyczne nie są trwale przechowywane, chyba że przeniesiesz emulatora usługi Microsoft Azure storage lub magazynu Azure. Jeden raz w magazynie, można je wyświetlić przy użyciu jednego z kilku dostępnych narzędzi.

## <a name="specify-a-storage-account"></a>Określ konto magazynu
Należy określić konto magazynu, które mają być używane w pliku ServiceConfiguration.cscfg. Informacje o koncie jest definiowany jako ciąg połączenia w ustawieniu konfiguracji. Domyślne parametry połączenia utworzone dla nowego projektu usługi w chmurze w programie Visual Studio można znaleźć w poniższym przykładzie:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Możesz zmienić te parametry połączenia, aby zapewnić informacje o koncie dla konta usługi Azure storage.

W zależności od typu zbieranych danych diagnostycznych usługi Azure Diagnostics korzysta z usługi obiektów Blob lub usłudze Table service. W poniższej tabeli przedstawiono źródła danych, które są trwałe i ich format.

| Źródło danych | Format magazynu |
| --- | --- |
| Dzienniki platformy Azure |Tabela |
| Dzienniki usług IIS 7.0 |Obiekt blob |
| Dzienniki infrastruktury diagnostyki Azure |Tabela |
| Nie powiodło się żądanie dzienników |Obiekt blob |
| Dzienniki zdarzeń Windows |Tabela |
| Liczniki wydajności |Tabela |
| Zrzuty awaryjne |Obiekt blob |
| Dzienniki błędów niestandardowych |Obiekt blob |

## <a name="transfer-diagnostic-data"></a>Przesyłanie danych diagnostycznych
Dla zestawu SDK 2.5 i nowszych żądanie na przesyłanie danych diagnostycznych może wystąpić, za pomocą pliku konfiguracji. Za przesyłanie danych diagnostycznych w zaplanowanych odstępach czasu określonych w konfiguracji.

Zestaw SDK 2.4 i poprzedniej możesz zażądać na przesyłanie danych diagnostycznych za pomocą pliku konfiguracji również, jak programowo. Programowe podejście umożliwia również to transferu na żądanie.

> [!IMPORTANT]
> Podczas przesyłania danych diagnostycznych na konto magazynu platformy Azure, możesz naliczane opłaty dla zasobów magazynu, które korzysta z danych diagnostycznych.
> 
> 

## <a name="store-diagnostic-data"></a>Dane diagnostyczne Store
Dane dziennika są przechowywane w magazynie obiektów Blob lub tabel o następujących nazwach:

**Tabele**

* **WadLogsTable** — Dzienniki zapisane w kodzie przy użyciu odbiornika śledzenia.
* **WADDiagnosticInfrastructureLogsTable** -diagnostycznych zmian konfiguracji i monitorowania.
* **WADDirectoriesTable** — katalogi, które są monitorowane przez program monitora diagnostycznego.  Obejmuje to dzienniki usług IIS, usługi IIS nie powiodło się, dzienniki żądania i niestandardowych katalogów.  W polu kontenera określono lokalizację pliku dziennika obiektów blob i pole RelativePath jest nazwa obiektu blob.  Pole ŚcieżkaBezwględna wskazuje lokalizację i nazwę pliku, jaka istniała na maszynie wirtualnej platformy Azure.
* **WADPerformanceCountersTable** — liczników wydajności.
* **WADWindowsEventLogsTable** — dzienniki zdarzeń Windows.

**Obiekty blob**

* **Narzędzie diagnostyczne kontroli kontenera** — (tylko w przypadku zestawu SDK 2.4 i poprzedniej) zawiera pliki konfiguracji XML, które kontroluje diagnostyki platformy Azure.
* **Narzędzie diagnostyczne iis-failedreqlogfiles** — zawiera informacje z dzienników usług IIS nie powiodło się żądanie.
* **Narzędzie diagnostyczne iis-logfiles** — zawiera informacje o dziennikach usług IIS.
* **"niestandardowy"** — kontener niestandardowy oparty na temat konfigurowania katalogi, które są monitorowane przy użyciu monitora diagnostycznego.  Nazwa ten kontener obiektów blob zostanie określony w WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Narzędzia do wyświetlania danych diagnostycznych
Niektóre narzędzia są dostępne do wyświetlania danych, gdy zostanie przeniesiona do magazynu. Na przykład:

* Eksploratora serwera w programie Visual Studio — po zainstalowaniu narzędzi platformy Azure dla programu Microsoft Visual Studio umożliwia węzła usługi Azure Storage w Eksploratorze serwera z Twojego konta usługi Azure storage widoku tylko do odczytu obiektów blob i danych tabeli. Można wyświetlić dane z konta magazynu lokalnego emulatora, a także z kont usługi storage utworzonego na platformie Azure. Aby uzyskać więcej informacji, zobacz [przeglądanie i zarządzanie zasobami magazynu za pomocą Eksploratora serwera](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) jest aplikacją autonomiczną, która umożliwia łatwą obsługę danych w usłudze Azure Storage w Windows, OS x i Linux.
* [Usługa Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) obejmuje Menedżera diagnostyki platformy Azure, dzięki czemu można wyświetlić i pobrać oraz zarządzać nimi dane diagnostyczne zbierane przez aplikacje działające na platformie Azure.

## <a name="next-steps"></a>Następne kroki
[Śledzenie przepływu w aplikacji usługi w chmurze przy użyciu diagnostyki Azure](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)


