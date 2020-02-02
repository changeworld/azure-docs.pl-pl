---
title: Wersje Service Fabric platformy Azure
description: Informacje o wersji dla Service Fabric platformy Azure. Zawiera informacje na temat najnowszych funkcji i ulepszeń w programie Service Fabric.
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 73624df3cb7b04c3c442a6654f1388017482b2ba
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935441"
---
# <a name="service-fabric-releases"></a>Wersje Service Fabric

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">wskazówki dotyczące rozwiązywania problemów</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">śledzenia</a> problemów 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">Opcje pomocy technicznej</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">obsługiwane wersje</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">przykłady kodu</a>

Ten artykuł zawiera więcej informacji o najnowszych wersjach i aktualizacjach środowiska uruchomieniowego Service Fabric i zestawów SDK.

## <a name="whats-new-in-service-fabric"></a>Co nowego w Service Fabric

### <a name="service-fabric-70"></a>Service Fabric 7,0

Usługa Azure Service Fabric 7,0 jest teraz dostępna! Będzie można zaktualizować do 7,0 za pośrednictwem Azure Portal lub wdrożenia Azure Resource Manager. Ze względu na Opinie klientów dotyczące wydań w okresie świątecznym nie rozpocznie się automatyczne aktualizowanie klastrów ustawionych w taki sposób, aby otrzymywać automatyczne uaktualnienia do stycznia.
W styczniu wznowimy standardowe procedury i klastry z włączonymi uaktualnieniami automatycznymi umożliwią automatyczne otrzymywanie aktualizacji 7,0. Przed rozpoczęciem wycofywania zajmiemy się innym ogłoszeniem.
Będziemy również aktualizować nasze planowane daty wydania, aby wskazać, że te zasady są uwzględniane. Zobacz tutaj, aby uzyskać aktualizacje dotyczące naszych przyszłych [harmonogramów wydania](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule).
 
Jest to Najnowsza wersja Service Fabric i została załadowana przy użyciu najważniejszych funkcji i ulepszeń.

### <a name="key-announcements"></a>Anonse kluczy
 - [**Obsługa KeyVaultReference dla wpisów tajnych aplikacji (wersja zapoznawcza)** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-keyvault-references): Service Fabric aplikacje, które obsługują [tożsamości zarządzane](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) , mogą teraz bezpośrednio odwoływać się do Key Vault tajnego adresu URL jako zmiennej środowiskowej, parametru aplikacji lub poświadczenia repozytorium kontenerów. Service Fabric automatycznie rozwiąże wpis tajny przy użyciu tożsamości zarządzanej aplikacji. 
     
- **Ulepszone zabezpieczenia uaktualnienia dla usług bezstanowych**: w celu zagwarantowania dostępności podczas uaktualniania aplikacji wprowadzono nowe konfiguracje umożliwiające zdefiniowanie [minimalnej liczby wystąpień usług bezstanowych](https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet) , które mają być uznawane za dostępne. Wcześniej ta wartość była równa 1 dla wszystkich usług i nie mogła zostać zmieniona. Korzystając z tej nowej kontroli bezpieczeństwa dla poszczególnych usług, można zagwarantować, że usługi zachowują minimalną liczbę wystąpień podczas uaktualniania aplikacji, uaktualniania klastra i innych czynności konserwacyjnych, które opierają się na sprawdzaniu kondycji Service Fabric i bezpieczeństwa.
  
- [**Limity zasobów dla usług użytkowników**](https://docs.microsoft.com/azure/service-fabric/service-fabric-resource-governance#enforcing-the-resource-limits-for-user-services): użytkownicy mogą konfigurować limity zasobów dla usług użytkowników w węźle, aby zapobiec scenariuszom, takim jak wyczerpanie zasobów Service Fabric usług systemu. 
  
- [**Bardzo wysoki koszt przeniesienia usługi**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost) dla typu repliki. Repliki o bardzo wysokim koszcie są przenoszone tylko w przypadku naruszenia ograniczenia w klastrze, którego nie można naprawić w żaden inny sposób. Zapoznaj się z dokumentacją, aby uzyskać dodatkowe informacje o tym, kiedy użycie "bardzo wysokie" koszt przeniesienia jest rozsądne i dodatkowe zagadnienia.
  
-  **Dodatkowe sprawdzanie bezpieczeństwa klastra**: w tej wersji wprowadzono konfigurowalne sprawdzanie bezpieczeństwa kworum węzła inicjatora. Pozwala to na dostosowanie liczby węzłów inicjatora, które muszą być dostępne podczas scenariuszy związanych z cyklem życia klastra i zarządzaniem nimi. Operacje przyjmujące klaster poniżej skonfigurowanej wartości są blokowane. Dzisiaj wartość domyślna to zawsze kworum węzłów inicjatora, na przykład jeśli masz 7 węzłów inicjatora, operacja, która mogłaby potrwać poniżej 5 węzłów inicjatora, będzie domyślnie blokowana. W przypadku tej zmiany można wprowadzić minimalną bezpieczną wartość 6, która pozwoli na przekroczenie tylko jednego węzła inicjatora.
   
- Dodano obsługę [**zarządzania kopiami zapasowymi i przywracaniem w programie Service Fabric Explorer**](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster). Powoduje to, że następujące działania mogą być możliwe bezpośrednio z poziomu SFX: odnajdywanie usługi tworzenia kopii zapasowych i przywracania, tworzenie zasad tworzenia kopii zapasowych, Włączanie automatycznych kopii zapasowych, wykonywanie kopii zapasowych ad hoc, wyzwalanie operacji przywracania i przeglądanie istniejących kopii zapasowych.

- Zapowiedź dostępności [**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool): to narzędzie pomaga sprawdzić, czy typy używane w niezawodnych kolekcjach są zgodne z poprzednimi wersjami podczas stopniowego uaktualniania aplikacji. Pozwala to zapobiec błędom uaktualniania lub utracie danych i uszkodzeniu danych ze względu na brak lub niezgodne typy.

- [**Włącz stałe odczyty w replikach pomocniczych**](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-configuration#configuration-names-1): stabilne odczyty ograniczają repliki pomocnicze, które będą zwracać wartości, które były potwierdzone kworum.

Ponadto ta wersja zawiera inne nowe funkcje, poprawki błędów i usprawnienia, niezawodność i wydajność. Aby uzyskać pełną listę zmian, zapoznaj się z [informacjami o wersji](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md).

### <a name="service-fabric-70-releases"></a>Wersje 7,0 Service Fabric

| Data wydania | Wydawanie | Więcej informacji |
|---|---|---|
| 18 listopada, 2019 | [Azure Service Fabric 7,0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Informacje o wersji](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 30 stycznia 2020 | [Wersja odświeżenia systemu Azure Service Fabric 7,0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Informacje o wersji](https://github.com/Azure/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|


### <a name="service-fabric-65"></a>Service Fabric 6,5

Ta wersja zawiera ulepszenia dotyczące możliwości obsługi, niezawodności i wydajności, nowe funkcje, poprawki błędów i ulepszenia ułatwiające zarządzanie klastrami i aplikacjami.

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

| Data wydania | Wydawanie | Więcej informacji |
|---|---|---|
| 11 czerwca 2019 | [Azure Service Fabric 6,5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Informacje o wersji](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 lipca 2019 r. | [Wersja odświeżenia systemu Azure Service Fabric 6,5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Informacje o wersji](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 lipca 2019 | [Wersja odświeżenia systemu Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Informacje o wersji](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| 23 sierpnia 2019 | [Wersja odświeżenia systemu Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Informacje o wersji](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 14 października 2019 | [Wersja odświeżenia systemu Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Informacje o wersji](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md)  |


## <a name="previous-versions"></a>Poprzednie wersje

### <a name="service-fabric-64-releases"></a>Wersje 6,4 Service Fabric

| Data wydania | Wydawanie | Więcej informacji |
|---|---|---|
| 30 listopada 2018 | [Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 grudnia 2018 r. | [Usługa Azure Service Fabric 6,4 Refresh Release dla klastrów systemu Windows](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 lutego 2019 | [Wersja odświeżenia systemu Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 marca 2019 | [Wersja odświeżenia systemu Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 kwietnia 2019 | [Wersja odświeżenia systemu Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 maja 2019 | [Wersja odświeżenia systemu Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 maja 2019 r. | [Wersja odświeżenia systemu Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Informacje o wersji](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
