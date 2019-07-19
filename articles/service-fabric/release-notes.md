---
title: Wersje Service Fabric platformy Azure
description: Informacje o wersji dotyczące najnowszych funkcji i ulepszeń w programie Service Fabric.
author: athinanthny
manager: chackdan
ms.author: atsenthi
ms.date: 6/10/2019
ms.topic: conceptual
ms.service: service-fabric
hide_comments: true
hideEdit: true
ms.openlocfilehash: 829963cf13eb1dc1b1113b6a296fe77dadb63bc4
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "68224936"
---
# <a name="service-fabric-releases"></a>Wersje Service Fabric

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Przewodniki</a> 
| dotyczące rozwiązywania problemów z<a href="https://github.com/Azure/service-fabric-issues" target="blank">śledzeniem</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">opcji</a> 
| pomocy technicznej<a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">obsługiwane wersje</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">przykłady kodu</a>

Ten artykuł zawiera więcej informacji o najnowszych wersjach i aktualizacjach środowiska uruchomieniowego Service Fabric i zestawów SDK.

## <a name="whats-new-in-service-fabric"></a>Co nowego w Service Fabric

### <a name="service-fabric-65"></a>Service Fabric 6,5

Najnowsza wersja Service Fabric obejmuje ulepszenia dotyczące możliwości obsługi, niezawodności i wydajności, nowe funkcje, poprawki błędów i ulepszenia ułatwiające zarządzanie klastrami i aplikacjami.

> [!IMPORTANT]
> Service Fabric 6,5 to ostateczna wersja z obsługą Service Fabric narzędzi w programie Visual Studio 2015. Klienci są zachęcani do przechodzenia do [programu Visual Studio 2019](https://visualstudio.microsoft.com/vs/) .

Oto nowości Nowość w Service Fabric 6,5:

- Service Fabric Explorer zawiera [podgląd magazyn obrazów](service-fabric-visualizing-your-cluster.md#image-store-viewer) do inspekcji aplikacji przekazanych do magazynu obrazów.

- Wersja [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) [aplikacji aranżacji patch (POA)](service-fabric-patch-orchestration-application.md) zawiera wiele ulepszeń samoobsługowych. Aby przejść do tej wersji, zalecamy użycie klientów POA.

- [Usługa EventStore jest domyślnie włączona](service-fabric-visualizing-your-cluster.md#event-store) dla klastrów Service Fabric 6,5, chyba że wybrałeś opcję.

- Dodano [zdarzenia cyklu życia repliki](service-fabric-diagnostics-event-generation-operational.md#replica-events) dla usług stanowych.

- [Lepsza widoczność stanu węzła inicjatora](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), w tym ostrzeżeń na poziomie klastra, jeśli węzeł inicjatora jest w złej kondycji (*w dół*, *usunięty* lub *nieznany*).

- [Service Fabric narzędzie do odzyskiwania po awarii aplikacji](https://github.com/Microsoft/Service-Fabric-AppDRTool) umożliwia Service Fabric usługi stanowe, aby szybko wykonać odzyskiwanie w przypadku wystąpienia awarii w klastrze podstawowym. Dane z klastra podstawowego są stale synchronizowane z pomocniczą aplikacją do rezerwowania przy użyciu okresowych kopii zapasowych i przywracania.

- Program Visual Studio obsługuje [publikowanie aplikacji platformy .NET Core w klastrach opartych na systemie Linux](service-fabric-how-to-publish-linux-app-vs.md).

- [Interfejs wiersza polecenia platformy Azure Service Fabric (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) zostanie automatycznie zainstalowany dla Service Fabric 6,5 (i nowszych) w przypadku uaktualniania lub tworzenia nowego klastra z systemem Linux na platformie Azure.

- [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) jest instalowany domyślnie w klastrach jednopunktowy MacOS/Linux.

Aby uzyskać więcej informacji, zobacz [Informacje o wersji Service Fabric 6,5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

### <a name="service-fabric-65-releases"></a>Wersje 6,5 Service Fabric

| Data wydania | Release | Więcej informacji |
|---|---|---|
| 11 czerwca 2019 | [Azure Service Fabric 6,5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Informacje o wersji](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 lipca 2019 | [Wersja odświeżenia systemu Azure Service Fabric 6,5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Informacje o wersji](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |

## <a name="previous-versions"></a>Poprzednie wersje

### <a name="service-fabric-64-releases"></a>Wersje 6,4 Service Fabric

| Data wydania | Release | Więcej informacji |
|---|---|---|
| 30 listopada 2018 | [Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 grudnia 2018 | [Usługa Azure Service Fabric 6,4 Refresh Release dla klastrów systemu Windows](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 lutego 2019 | [Wersja odświeżenia systemu Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 marca 2019 | [Wersja odświeżenia systemu Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 kwietnia 2019 | [Wersja odświeżenia systemu Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 maja 2019 | [Wersja odświeżenia systemu Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 maja 2019 | [Wersja odświeżenia systemu Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
