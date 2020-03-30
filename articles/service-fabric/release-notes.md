---
title: Wersje sieci szkieletowej usług Azure Service
description: Informacje o wersji dla sieci szkieletowej usług Azure. Zawiera informacje na temat najnowszych funkcji i ulepszeń w sieci szkieletowej usług.
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: cb4fdd56e9cf67c71ac690d423499929167f8977
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064245"
---
# <a name="service-fabric-releases"></a>Wydania sieci szkieletowej usług

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Rozwiązywanie problemów Przewodników</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">Śledzenie problemów</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">Opcje</a> 
| pomocy technicznej<a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">Obsługiwane wersje</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Kod przykłady</a>

Ten artykuł zawiera więcej informacji na temat najnowszych wersji i aktualizacji środowiska wykonawczego sieci szkieletowej usług i SDK.

## <a name="whats-new-in-service-fabric"></a>Co nowego w sieci szkieletowej usług

### <a name="service-fabric-70"></a>Tkanina serwisowa 7.0

Usługa Azure Service Fabric 7.0 jest już dostępna! Będzie można zaktualizować do 7.0 za pośrednictwem witryny Azure portal lub za pośrednictwem wdrożenia usługi Azure Resource Manager. Ze względu na opinie klientów na temat wydań w okresie świątecznym nie rozpoczniemy automatycznej aktualizacji klastrów ustawionych na automatyczne uaktualnienia do stycznia.
W styczniu wznowimy standardową procedurę wdrażania, a klastry z włączonymi automatycznymi uaktualnieniami zaczną automatycznie odbierać aktualizację 7.0. Przed rozpoczęciem wdrożenia przedstawimy kolejne ogłoszenie.
Zaktualizujemy również planowane daty premiery, aby wskazać, że bierzemy pod uwagę tę politykę. Poszukaj tutaj aktualizacji naszych przyszłych [harmonogramów wersji.](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)
 
Jest to najnowsza wersja sieci szkieletowej usług i jest ładowany z kluczowych funkcji i ulepszeń.

### <a name="key-announcements"></a>Kluczowe anonse
 - [**Obsługa keyvaultreference dla wpisów tajnych aplikacji (Wersja zapoznawcza)**](https://docs.microsoft.com/azure/service-fabric/service-fabric-keyvault-references): Aplikacje sieci szkieletowej usług, które włączyły [tożsamości zarządzane,](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) mogą teraz bezpośrednio odwoływać się do tajnego adresu URL magazynu kluczy jako zmiennej środowiskowej, parametru aplikacji lub poświadczenia repozytorium kontenera. Sieci szkieletowej usług automatycznie rozwiąże klucz tajny przy użyciu tożsamości zarządzanej aplikacji. 
     
- **Ulepszone bezpieczeństwo uaktualnień dla usług bezstanowych:** Aby zagwarantować dostępność podczas uaktualniania aplikacji, wprowadziliśmy nowe konfiguracje, aby zdefiniować [minimalną liczbę wystąpień dla usług bezstanowych,](https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet) które mają być uważane za dostępne. Wcześniej wartość ta wynosiła 1 dla wszystkich usług i nie była zmienna. Dzięki tej nowej kontroli bezpieczeństwa dla usług można zapewnić, że usługi zachowują minimalną liczbę wystąpień podczas uaktualniania aplikacji, uaktualnień klastra i innych konserwacji, które opierają się na kontrolach kondycji i bezpieczeństwa sieci szkieletowej usług.
  
- [**Limity zasobów dla usług użytkowników:**](https://docs.microsoft.com/azure/service-fabric/service-fabric-resource-governance#enforcing-the-resource-limits-for-user-services)Użytkownicy mogą konfigurować limity zasobów dla usług użytkowników w węźle, aby zapobiec scenariuszom, takim jak wyczerpanie zasobów usług systemu sieci szkieletowej usług sieci szkieletowej usług. 
  
- [**Bardzo wysoki koszt przeniesienia usługi**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost) dla typu repliki. Repliki z bardzo wysokim kosztem przeniesienia zostaną przeniesione tylko wtedy, gdy w klastrze występuje naruszenie ograniczeń, którego nie można naprawić w żaden inny sposób. Zobacz dokumenty, aby uzyskać dodatkowe informacje na temat tego, kiedy użycie "bardzo wysoki" koszt przeniesienia jest uzasadnione i dodatkowe uwagi.
  
-  **Dodatkowe kontrole bezpieczeństwa klastra:** W tej wersji wprowadziliśmy konfigurowalną kontrolę bezpieczeństwa kworum węzła źródłowego. Dzięki temu można dostosować, ile węzłów źródłowych muszą być dostępne podczas klastra cyklu życia i scenariuszy zarządzania. Operacje, które miałyby klastra poniżej skonfigurowaną wartość są blokowane. Obecnie wartość domyślna jest zawsze kworum węzłów źródłowych, na przykład, jeśli masz 7 węzłów źródłowych, operacja, która zabierze Cię poniżej 5 węzłów źródłowych będzie domyślnie zablokowany. Dzięki tej zmianie można wprowadzić minimalną bezpieczną wartość 6, która pozwoliłaby na wysunięciu tylko jednego węzła źródłowego naraz.
   
- Dodano obsługę [**zarządzania usługą tworzenia kopii zapasowych i przywracania w Eksploratorze sieci szkieletowej usług**](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster). Umożliwia to następujące działania bezpośrednio z poziomu SFX: odnajdowanie usługi tworzenia kopii zapasowych i przywracania, tworzenie zasad tworzenia kopii zapasowych, włączanie automatycznych kopii zapasowych, wykonywanie kopii zapasowych adhoc, wyzwalanie operacji przywracania i przeglądanie istniejących kopii zapasowych.

- Ogłaszając dostępność [**ReliableCollectionsMissingTypesTool:**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool)To narzędzie pomaga sprawdzić, czy typy używane w niezawodnych kolekcjach są zgodne do przodu i do tyłu podczas uaktualniania aplikacji stopniowych. Pomaga to zapobiec awariom uaktualnienia lub utracie danych i uszkodzeniu danych z powodu brakujących lub niezgodnych typów.

- [**Włącz stabilne odczyty w replikach pomocniczych:Odczyty**](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-configuration#configuration-names-1)stabilne ograniczą repliki pomocnicze do zwracanych wartości, które zostały przypisane do kworum.

Ponadto ta wersja zawiera inne nowe funkcje, poprawki błędów i ulepszenia obsługi, niezawodności i wydajności. Pełna lista zmian znajduje się w [informacjach o wersji](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md).

### <a name="service-fabric-70-releases"></a>Wersje usługi Fabric 7.0

| Data wydania | Release | Więcej informacji |
|---|---|---|
| 18 listopada 2019 r. | [Usługa Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Informacje o wersji](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 30 stycznia 2020 r. | [Wersja odświeżania usługi Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Informacje o wersji](https://github.com/Azure/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 6 lutego 2020 r. | [Wersja odświeżania usługi Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [Informacje o wersji](https://github.com/Azure/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|

### <a name="service-fabric-65"></a>Tkanina serwisowa 6.5

Ta wersja zawiera ulepszenia obsługi, niezawodności i wydajności, nowe funkcje, poprawki błędów i ulepszenia ułatwiające zarządzanie klastrem i cyklem życia aplikacji.

> [!IMPORTANT]
> Usługa Fabric 6.5 to ostateczna wersja z obsługą narzędzi sieci szkieletowej usług w programie Visual Studio 2015. Klienci powinni przejść do [programu Visual Studio 2019](https://visualstudio.microsoft.com/vs/) w przyszłości.

Oto co nowego w sieci szkieletowej usług 6.5:

- Eksplorator sieci szkieletowej usług zawiera [przeglądarkę magazynu obrazów](service-fabric-visualizing-your-cluster.md#image-store-viewer) do sprawdzania aplikacji przekazanych do magazynu obrazów.

- [Patch Orchestration Application (POA)](service-fabric-patch-orchestration-application.md) w wersji [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) zawiera wiele ulepszeń autodiagnostyki. Klienci POA są zalecane, aby przejść do tej wersji.

- [Usługa EventStore jest domyślnie włączona](service-fabric-visualizing-your-cluster.md#event-store) dla klastrów sieci szkieletowej usługi 6.5, chyba że zrezygnowano.

- Dodano [zdarzenia cyklu życia repliki](service-fabric-diagnostics-event-generation-operational.md#replica-events) dla usług stanowych.

- [Lepsza widoczność stanu węzła źródłowego,](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status)w tym ostrzeżenia na poziomie klastra, jeśli węzeł źródłowy jest w złej kondycji *(W dół,* *Usunięte* lub *Nieznane).*

- Narzędzie do [odzyskiwania po awarii aplikacji sieci szkieletowej](https://github.com/Microsoft/Service-Fabric-AppDRTool) usług umożliwia usługi stanowe sieci szkieletowej usług, aby szybko odzyskać, gdy klaster podstawowy napotka katastrofę. Dane z klastra podstawowego są stale synchronizowane w pomocniczej aplikacji rezerwowej przy użyciu okresowej kopii zapasowej i przywracania.

- Obsługa programu Visual Studio do [publikowania aplikacji .NET Core w klastrach opartych na systemie Linux](service-fabric-how-to-publish-linux-app-vs.md).

- [Interfejs wiersza polecenia sieci szkieletowej usług Azure (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) zostanie zainstalowany automatycznie dla sieci szkieletowej usługi 6.5 (i nowszych wersji) podczas uaktualniania lub tworzenia nowego klastra systemu Linux na platformie Azure.

- [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) jest instalowany domyślnie w klastrach MacOS/Linux OneBox.

Aby uzyskać więcej informacji, zobacz [Informacje o wersji sieci szkieletowej usług 6.5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

### <a name="service-fabric-65-releases"></a>Wersje usługi Fabric 6.5

| Data wydania | Release | Więcej informacji |
|---|---|---|
| 11 czerwca 2019 r. | [Usługa Azure Service Fabric 6.5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Informacje o wersji](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 lipca 2019 r. | [Wersja odświeżania usługi Azure Service Fabric 6.5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Informacje o wersji](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 lipca 2019 r. | [Wersja odświeżania usługi Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Informacje o wersji](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| 23 sierpnia 2019 r. | [Wersja odświeżania usługi Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Informacje o wersji](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 14 października 2019 r. | [Wersja odświeżania usługi Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Informacje o wersji](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md)  |


## <a name="previous-versions"></a>Poprzednie wersje

### <a name="service-fabric-64-releases"></a>Wersje usługi Fabric 6.4

| Data wydania | Release | Więcej informacji |
|---|---|---|
| 30 listopada 2018 r. | [Usługa Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 grudnia 2018 r. | [Wersja odświeżania usługi Azure Service Fabric 6.4 dla klastrów systemu Windows](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 lutego 2019 r. | [Wersja odświeżania usługi Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 marca 2019 r. | [Wersja odświeżania usługi Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 kwietnia 2019 r. | [Wersja odświeżania usługi Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 maja 2019 r. | [Wersja odświeżania usługi Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 maja 2019 r. | [Wersja odświeżania usługi Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
