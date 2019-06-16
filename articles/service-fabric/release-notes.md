---
title: Wersje usługi Azure Service Fabric
description: Informacje o wersji dla najnowszych funkcji i ulepszeń w usłudze Service Fabric.
author: athinanthny
manager: chackdan
ms.author: atsenthi
ms.date: 6/10/2019
ms.topic: conceptual
ms.service: service-fabric
hide_comments: true
hideEdit: true
ms.openlocfilehash: 5610c6d31732144086812bb02b65cfaffa067eae
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063000"
---
# <a name="service-fabric-releases"></a>Wersje usługi Service Fabric

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Wskazówki dotyczące rozwiązywania problemów</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">śledzenie problemów</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">opcje pomocy technicznej</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">obsługiwane wersje</a>  
|  <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Przykłady kodu</a>

Ten artykuł zawiera więcej informacji o zainstalowane najnowsze wersje i aktualizacje środowiska uruchomieniowego usługi Service Fabric i zestawów SDK.

## <a name="whats-new-in-service-fabric"></a>**Co nowego w usłudze Service Fabric**

### <a name="service-fabric-65"></a>Service Fabric 6.5

Najnowsza wersja usługi Service Fabric zapewnia wsparcie, niezawodności i ulepszenia wydajności, nowe funkcje, poprawki błędów i ulepszenia, aby ułatwić zarządzanie cyklem życia klastrami i aplikacjami.

> [!IMPORTANT]
> Usługa Service Fabric 6.5 jest ostateczna wersja z usługą Service Fabric narzędzia pomocy technicznej w programie Visual Studio 2015. Klienci doradza się przenieść [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) przyszłości.

Poniżej przedstawiono what's new in Service Fabric 6.5:

- Obejmuje narzędzia Service Fabric Explorer [Image Store Viewer](service-fabric-visualizing-your-cluster.md#image-store-viewer) do celów kontroli aplikacji zostało przesłane do magazynu obrazów.

- [Patch Orchestration Application (POA)](service-fabric-patch-orchestration-application.md) wersji [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) zawiera wiele ulepszeń diagnostyki automatycznej. Klienci POA są zalecane, aby przejść do tej wersji.

- [Usługa bazy danych EventStore jest domyślnie włączona,](service-fabric-visualizing-your-cluster.md#event-store) klastrów usługi Service Fabric 6.5, chyba że użytkownik zgodził.

- Dodano [zdarzenia cyklu życia repliki](service-fabric-diagnostics-event-generation-operational.md#replica-events) dla usług stanowych.

- [Lepszy wgląd stan węzła inicjatora](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), łącznie z ostrzeżeniami poziomu klastra, jeśli węzeł inicjujący jest w złej kondycji (*dół*, *usunięto* lub *nieznany*).

- [Narzędzia odzyskiwania po awarii aplikacji z usługą Service Fabric](https://github.com/Microsoft/Service-Fabric-AppDRTool) umożliwia stanowej usługi Service Fabric w celu przeprowadzenia szybkiego odzyskiwania, gdy klastra podstawowego napotka awarii. Dane z klastra podstawowego są stale synchronizowane dodatkowej wstrzymania aplikacji za pomocą okresowych kopii zapasowych i przywracania.

- Visual Studio dla systemu [publikowania aplikacji platformy .NET Core opartych na systemie Linux klastrów](service-fabric-how-to-publish-linux-app-vs.md).

- [Interfejsu wiersza polecenia platformy Azure Service Fabric Service (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) zostaną zainstalowane automatycznie dla usługi Service Fabric 6.5 (i nowszych wersjach) po uaktualnieniu lub Utwórz nowy klaster systemu Linux na platformie Azure.

- [Interfejs SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) jest instalowany domyślnie w klastrach OneBox systemu MacOS/Linux.

Aby uzyskać więcej informacji, zobacz [wersji do usługi Service Fabric 6.5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

## <a name="previous-versions"></a>Poprzednie wersje

### <a name="service-fabric-64-releases"></a>Wersje usługi Service Fabric 6.4

| Data wydania | Release | Więcej informacji |
|---|---|---|
| 30 listopada 2018 r. | [Usługa Azure Service Fabric 6.4 ](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 grudnia 2018 r. | [Usługa Azure klastry usługi Service Fabric 6.4 Odśwież wydania dla Windows](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 lutego 2019 r. | [Usługa Azure Service Fabric 6.4 odświeżania wersji](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 marca 2019 r. | [Usługa Azure Service Fabric 6.4 odświeżania wersji](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 kwietnia 2019 r. | [Usługa Azure Service Fabric 6.4 odświeżania wersji](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 maja 2019 r. | [Usługa Azure Service Fabric 6.4 odświeżania wersji](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 maja 2019 r. | [Usługa Azure Service Fabric 6.4 odświeżania wersji](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
