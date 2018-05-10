---
title: Magazyn i widoku danych diagnostycznych w magazynie Azure | Dokumentacja firmy Microsoft
description: Pobierz dane diagnostyczne platformy Azure do usługi Azure Storage i wyświetlić
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: tysonn
ms.assetid: 18e0780d-43e7-41e4-b8e9-f1fb9a36eb03
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: adegeo
ms.openlocfilehash: 248e1c87ec244649a296233bf2aaa323d3475a79
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Magazyn i widoku danych diagnostycznych w usłudze Azure Storage
Danych diagnostycznych nie są trwale przechowywane, chyba że transfer emulatora magazynu Microsoft Azure lub do magazynu Azure. Raz w magazynie, można je wyświetlić jeden z kilku dostępnych narzędzi.

## <a name="specify-a-storage-account"></a>Określ konto magazynu
Należy określić konto magazynu, które mają być używane w pliku ServiceConfiguration.cscfg pliku. Informacje o koncie jest zdefiniowany jako parametry połączenia w ustawieniu konfiguracji. W poniższym przykładzie przedstawiono domyślnego ciągu połączenia utworzone dla nowego projektu usługi w chmurze w programie Visual Studio:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Możesz zmienić te parametry połączenia, aby podać informacje o koncie dla konta magazynu platformy Azure.

W zależności od typu zbieranych danych diagnostycznych Diagnostyka Azure korzysta z usługi obiektów Blob lub usługi tabel. W poniższej tabeli przedstawiono źródła danych, które są zachowywane i ich format.

| Źródło danych | Formatu magazynowania |
| --- | --- |
| Dzienniki platformy Azure |Tabela |
| Dzienniki usług IIS 7.0 |Obiekt blob |
| Dzienniki infrastruktury diagnostyki Azure |Tabela |
| Nie można zażądać dzienników |Obiekt blob |
| Dzienniki zdarzeń systemu Windows |Tabela |
| Liczniki wydajności |Tabela |
| Zrzuty awaryjne |Obiekt blob |
| Dzienniki błędów niestandardowych |Obiekt blob |

## <a name="transfer-diagnostic-data"></a>Transfer danych diagnostycznych
Dla zestawu SDK, 2.5 i nowszych żądanie na przesyłanie danych diagnostycznych może wystąpić przy użyciu pliku konfiguracji. Można przenieść dane diagnostyczne w zaplanowanych odstępach czasu określonych w konfiguracji.

2.4 zestawu SDK i poprzedniego może wysłać żądanie na przesyłanie danych diagnostycznych jak programowo przy użyciu pliku konfiguracji. To rozwiązanie programowe umożliwia również czy transferów na żądanie.

> [!IMPORTANT]
> Podczas transferu danych diagnostycznych na konto magazynu platformy Azure, możesz pociągnąć za sobą koszty dla zasobów magazynu, które używa danych diagnostycznych.
> 
> 

## <a name="store-diagnostic-data"></a>Przechowywanie danych diagnostycznych
Dane dziennika są przechowywane w magazynie obiektów Blob lub tabela, z następujących nazw:

**Tabele**

* **WadLogsTable** — dzienniki zapisywane w kodzie za pomocą obiektu nasłuchującego śledzenia.
* **WADDiagnosticInfrastructureLogsTable** -diagnostycznych zmiany monitora i konfiguracji.
* **WADDirectoriesTable** — katalogów monitorowanych monitora diagnostycznego.  Obejmuje to dzienniki programu IIS, usługi IIS nie powiodło się, dzienniki żądań i niestandardowych katalogów.  W polu kontenera określono lokalizację pliku dziennika obiektów blob i pole RelativePath jest nazwa obiektu blob.  W polu ŚcieżkaBezwględna wskazuje lokalizację i nazwę pliku znajdowały się na maszynie wirtualnej Azure.
* **WADPerformanceCountersTable** — liczniki wydajności.
* **WADWindowsEventLogsTable** — dzienniki zdarzeń systemu Windows.

**Obiekty blob**

* **wad formantu kontenera** — (tylko w przypadku 2.4 zestawu SDK i poprzedniego) zawiera pliki konfiguracji XML, które kontroluje diagnostycznych platformy Azure.
* **wad — usługi iis-failedreqlogfiles** — zawiera informacje z dzienników usług IIS nie powiodło się żądanie.
* **wad — usługi iis-logfiles** — zawiera informacje o dziennikach usług IIS.
* **"niestandardowe"** — niestandardowe kontenera oparte na Konfigurowanie katalogów, które są monitorowane przez monitor diagnostyczny.  Nazwa tego kontenera obiektów blob zostanie określona w WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Narzędzia do wyświetlania danych diagnostycznych
Kilka narzędzi są dostępne wyświetlić dane, gdy zostanie przeniesiona do magazynu. Na przykład:

* Eksploratora serwera w programie Visual Studio — po zainstalowaniu narzędzi Azure dla programu Microsoft Visual Studio, służy węzła usługi Azure Storage w Eksploratorze serwera do wyświetlenia tylko do odczytu obiektów blob i danych tabel z kontami magazynu Azure. Można wyświetlić dane z Twojego konta emulatora magazynu lokalnego, a także z kont magazynu utworzonym dla platformy Azure. Aby uzyskać więcej informacji, zobacz [przeglądanie i zarządzanie zasobami magazynu za pomocą Eksploratora serwera](../vs-azure-tools-storage-resources-server-explorer-browse-manage.md).
* [Eksplorator magazynu Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) jest aplikacją autonomiczną, która pozwala łatwo pracować z danymi usługi Azure Storage w systemach Windows, OS x i Linux.
* [Azure Management Studio](http://www.cerebrata.com/products/azure-management-studio/introduction) zawiera Menedżera diagnostyki Azure, dzięki czemu można wyświetlić, Pobierz i zarządzanie dane diagnostyczne zebrane przez aplikacji działających na platformie Azure.

## <a name="next-steps"></a>Następne kroki
[Śledzenie przepływu w aplikacji usługi w chmurze Diagnostyka Azure](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)

