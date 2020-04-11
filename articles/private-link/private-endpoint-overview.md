---
title: Co to jest prywatny punkt końcowy platformy Azure?
description: Dowiedz się więcej o prywatnym punkcie końcowym platformy Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: d10b6c52310da3d799a7fe78c83284960318f82e
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115248"
---
# <a name="what-is-azure-private-endpoint"></a>Co to jest prywatny punkt końcowy platformy Azure?

Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który nawiązuje połączenie prywatnie i bezpiecznie z usługą obsługiwaną przez usługę Azure Private Link. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, skutecznie wprowadzając usługę do sieci wirtualnej. Usługa może być usługą platformy Azure, taką jak Usługa Azure Storage, Usługa Azure Cosmos DB, SQL [itp.](private-link-service-overview.md)
  
## <a name="private-endpoint-properties"></a>Właściwości prywatnego punktu końcowego 
 Prywatny punkt końcowy określa następujące właściwości: 


|Właściwość  |Opis |
|---------|---------|
|Nazwa    |    Unikatowa nazwa w grupie zasobów.      |
|Podsieć    |  Podsieć do wdrażania i przydzielania prywatnych adresów IP z sieci wirtualnej. Aby zapoznać się z wymaganiami dotyczącymi podsieci, zobacz sekcję Ograniczenia w tym artykule.         |
|Zasób łącza prywatnego    |   Zasób łącza prywatnego do łączenia się przy użyciu identyfikatora lub aliasu zasobu z listy dostępnych typów. Unikatowy identyfikator sieci zostanie wygenerowany dla całego ruchu wysyłanego do tego zasobu.       |
|Podźródło docelowe   |      Podźródło do nawiązania połączenia. Każdy typ zasobu łącza prywatnego ma różne opcje do wyboru na podstawie preferencji.    |
|Metoda zatwierdzania połączenia    |  Automatyczna lub ręczna. Na podstawie uprawnień kontroli dostępu opartej na rolach (RBAC) prywatny punkt końcowy może zostać zatwierdzony automatycznie. Jeśli spróbujesz połączyć się z zasobem łącza prywatnego bez funkcji RBAC, użyj metody ręcznej, aby umożliwić właścicielowi zasobu zatwierdzenie połączenia.        |
|Prośba o wiadomość     |  Można określić komunikat dla żądanych połączeń, które mają być zatwierdzane ręcznie. Ten komunikat może służyć do identyfikowania określonego żądania.        |
|Stan połączenia   |   Właściwość tylko do odczytu, która określa, czy prywatny punkt końcowy jest aktywny. Tylko prywatne punkty końcowe w zatwierdzonym stanie mogą służyć do wysyłania ruchu. Dostępne dodatkowe stany: <br>-**Zatwierdzone:** Połączenie zostało zatwierdzone automatycznie lub ręcznie i jest gotowe do użycia.</br><br>-**Oczekujące:** Połączenie zostało utworzone ręcznie i oczekuje na zatwierdzenie przez właściciela zasobu łącza prywatnego.</br><br>-**Odrzucone**: Połączenie zostało odrzucone przez właściciela zasobu łącza prywatnego.</br><br>-**Rozłączono:** Połączenie zostało usunięte przez właściciela zasobu łącza prywatnego. Prywatny punkt końcowy staje się pouczające i powinny zostać usunięte do czyszczenia. </br>|

Oto kilka kluczowych szczegółów dotyczących prywatnych punktów końcowych: 
- Prywatny punkt końcowy umożliwia łączność między konsumentami z tej samej sieci wirtualnej, regionalnie równorzędnych sieci wirtualnych, globalnie równorzędnych sieci wirtualnych i lokalnie przy użyciu [sieci VPN](https://azure.microsoft.com/services/vpn-gateway/) lub [express route](https://azure.microsoft.com/services/expressroute/) i usług obsługiwanych przez private link.
 
- Podczas tworzenia prywatnego punktu końcowego tworzony jest również interfejs sieciowy dla cyklu życia zasobu. Interfejs jest przypisywany prywatny adres IP z podsieci, który mapuje do usługi łącza prywatnego.
 
- Prywatny punkt końcowy musi zostać wdrożony w tym samym regionie co sieć wirtualna. 
 
- Zasób łącza prywatnego można wdrożyć w innym regionie niż sieć wirtualna i prywatny punkt końcowy.
 
- Wiele prywatnych punktów końcowych można utworzyć przy użyciu tego samego zasobu łącza prywatnego. W przypadku pojedynczej sieci przy użyciu wspólnej konfiguracji serwera DNS zalecaną praktyką jest użycie jednego prywatnego punktu końcowego dla danego zasobu łącza prywatnego, aby uniknąć zduplikowanych wpisów lub konfliktów w rozdzielczości DNS. 
 
- Wiele prywatnych punktów końcowych można utworzyć w tych samych lub różnych podsieci w tej samej sieci wirtualnej. Istnieją ograniczenia liczby prywatnych punktów końcowych, które można utworzyć w ramach subskrypcji. Aby uzyskać szczegółowe informacje, zobacz [limity platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


 
## <a name="private-link-resource"></a>Zasób łącza prywatnego 
Zasób łącza prywatnego jest miejscem docelowym danego prywatnego punktu końcowego. Poniżej znajduje się lista dostępnych typów zasobów łączy prywatnych: 
 
|Nazwa zasobu łącza prywatnego  |Typ zasobu   |Podźródła  |
|---------|---------|---------|
|**Private Link Service** (twoja własna usługa)   |  Microsoft.Network/privateLinkServices       | empty |
|**Azure SQL Database** | Microsoft.Sql/serwery    |  Sql Server (sqlServer)        |
|**Azure Synapse Analytics** | Microsoft.Sql/serwery    |  Sql Server (sqlServer)        | 
|**Azure Storage**  | Microsoft.Storage/storageKondyje    |  Obiekt blob (obiekt blob, blob_secondary)<BR> Tabela (tabela, table_secondary)<BR> Kolejka (kolejka, queue_secondary)<BR> Plik (plik, file_secondary)<BR> Web (www, web_secondary)        |
|**Usługa Azure Data Lake Storage 2. generacji**  | Microsoft.Storage/storageKondyje    |  Obiekt blob (obiekt blob, blob_secondary)<BR> Data Lake File System Gen2 (dfs, dfs_secondary)       |
|**Azure Cosmos DB** | Microsoft.AzureCosmosDB/databaseKonta kont | Sql, MongoDB, Cassandra, Gremlin, Tabela|
|**Usługa Azure Database for PostgreSQL -Single server** | Microsoft.DBforPostgreSQL/servers   | postgresqlServer |
|**Azure Database for MySQL** | Microsoft.DBforMySQL/servers    | serwer mysqlServer |
|**Azure Database for MariaDB** | Microsoft.DBforMariaDB/serwery    | mariadbServer (MariadbServer) |
|**Azure Key Vault** | Microsoft.KeyVault/przechowalnia    | magazyn |
|**Usługa Kubernetes platformy Azure — interfejs API usługi Kubernetes** | Microsoft.ContainerService/managedClusters | managedCluster |
|**Azure Search** | Microsoft.Search/searchService (Wyszukiwanie/wyszukiwanie)| wyszukiwanie Usługa|  
|**Rejestr kontenerów platformy Azure** | Microsoft.ContainerRegistry/rejestry  | registry |
|**Azure App Configuration** | Microsoft.Appconfiguration/configurationStores   | konfiguracjaSklep |
|**Azure Backup** | Microsoft.RecoverySługs/vaults   | magazyn |
|**Centrum zdarzeń Azure** | Microsoft.EventHub/przestrzenie nazw    | namespace |
|**Azure Service Bus** | Microsoft.ServiceBus/przestrzenie nazw | namespace |
|**Azure Relay** | Microsoft.Relay/przestrzenie nazw | namespace |
|**Azure Event Grid** | Microsoft.EventGrid/tematy  | temat |
|**Azure Event Grid** | Microsoft.EventGrid/domeny | domena |
|**Azure WebApps** | Microsoft.Web/witryny    | lokacja |
|**Azure Machine Learning** | Microsoft.MachineLearningUsługi/obszary robocze  | obszar roboczy |
  
 
## <a name="network-security-of-private-endpoints"></a>Bezpieczeństwo sieci prywatnych punktów końcowych 
Podczas korzystania z prywatnych punktów końcowych dla usług platformy Azure ruch jest zabezpieczony do określonego zasobu łącza prywatnego. Platforma wykonuje kontrolę dostępu, aby sprawdzić poprawność połączeń sieciowych docierających tylko do określonego zasobu łącza prywatnego. Aby uzyskać dostęp do dodatkowych zasobów w ramach tej samej usługi platformy Azure, wymagane są dodatkowe prywatne punkty końcowe. 
 
Możesz całkowicie zablokować swoje obciążenia przed dostępem do publicznych punktów końcowych, aby połączyć się z obsługiwaną usługą platformy Azure. Ten formant zapewnia dodatkową warstwę zabezpieczeń sieci do zasobów, zapewniając wbudowaną ochronę przed eksfiltracją, która uniemożliwia dostęp do innych zasobów hostowanych w tej samej usłudze platformy Azure. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Dostęp do zasobu łącza prywatnego przy użyciu przepływu pracy zatwierdzania 
Można połączyć się z zasobem łącza prywatnego przy użyciu następujących metod zatwierdzania połączenia:
- **Automatycznie** zatwierdzane, gdy jesteś właścicielem lub masz uprawnienia do określonego zasobu łącza prywatnego. Wymagane uprawnienie jest oparte na typie zasobu łącza prywatnego w następującym formacie: Microsoft. \<Dostawca>/<resource_type>/privateEndpointConnectionZatwierdzenie/działanie
- **Ręczne** żądanie, gdy nie masz wymagane uprawnienia i chcesz zażądać dostępu. Zostanie zainicjowany przepływ pracy zatwierdzania. Prywatny punkt końcowy i kolejne połączenie prywatnego punktu końcowego zostaną utworzone ze stanem „Oczekiwanie”. Właściciel zasobu linku prywatnego jest odpowiedzialny za zaakceptowanie połączenia. Po zatwierdzeniu prywatny punkt końcowy jest włączona do wysyłania ruchu normalnie, jak pokazano na poniższym diagramie przepływu pracy zatwierdzania.  

![zatwierdzanie przepływu pracy](media/private-endpoint-overview/private-link-paas-workflow.png)
 
Właściciel zasobu łącza prywatnego może wykonywać następujące akcje za sprawą połączenia prywatnego punktu końcowego: 
- Przejrzyj wszystkie szczegóły połączeń prywatnych punktów końcowych. 
- Zatwierdzanie połączenia prywatnego punktu końcowego. Odpowiedni prywatny punkt końcowy zostanie włączony do wysyłania ruchu do zasobu łącza prywatnego. 
- Odrzuć połączenie prywatnego punktu końcowego. Odpowiedni prywatny punkt końcowy zostanie zaktualizowany w celu odzwierciedlenia stanu.
- Usuń połączenie prywatnego punktu końcowego w dowolnym stanie. Odpowiedni prywatny punkt końcowy zostanie zaktualizowany o stan rozłączony, aby odzwierciedlić akcję, właściciel prywatnego punktu końcowego może usunąć tylko zasób w tym momencie. 
 
> [!NOTE]
> Tylko prywatny punkt końcowy w zatwierdzonym stanie może wysyłać ruch do danego zasobu łącza prywatnego. 

### <a name="connecting-using-alias"></a>Łączenie się przy użyciu aliasu
Alias to unikatowy moniker, który jest generowany, gdy właściciel usługi tworzy usługę łącza prywatnego za standardowym modułem równoważenia obciążenia. Właściciel usługi może udostępniać ten alias swoim konsumentom w trybie offline. Konsumenci mogą zażądać połączenia z usługą łączy prywatnych przy użyciu identyfikatora URI zasobu lub aliasu. Jeśli chcesz połączyć się przy użyciu aliasu, należy utworzyć prywatny punkt końcowy przy użyciu metody ręcznego zatwierdzania połączenia. W przypadku korzystania z metody ręcznego zatwierdzania połączeń należy ustawić parametr żądania ręcznego na true podczas przepływu tworzenia prywatnego punktu końcowego. Spójrz na [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0) i [az sieci prywatnego punktu końcowego tworzenia](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) szczegółów. 

## <a name="dns-configuration"></a>Konfiguracja usługi DNS 
Podczas łączenia się z zasobem łącza prywatnego przy użyciu w pełni kwalifikowanej nazwy domeny (FQDN) jako części ciągu połączenia, ważne jest, aby poprawnie skonfigurować ustawienia DNS, aby rozwiązać problem przydzielonego prywatnego adresu IP. Istniejące usługi platformy Azure mogą mieć już konfigurację DNS do użycia podczas łączenia się za pośrednictwem publicznego punktu końcowego. To musi zostać zastąpione, aby połączyć się przy użyciu prywatnego punktu końcowego. 
 
Interfejs sieciowy skojarzony z prywatnym punktem końcowym zawiera pełny zestaw informacji wymaganych do skonfigurowania systemu DNS, w tym nazwy FQDN i prywatne adresy IP przydzielone dla danego zasobu łącza prywatnego. 
 
Aby skonfigurować ustawienia DNS dla prywatnych punktów końcowych, można użyć następujących opcji: 
- **Użyj pliku hosta (zalecanego tylko do testowania)**. Za pomocą pliku hosta na maszynie wirtualnej można zastąpić system DNS.  
- **Użyj prywatnej strefy DNS**. Za pomocą prywatnych stref DNS można zastąpić rozdzielczość DNS dla danego prywatnego punktu końcowego. Prywatna strefa DNS może być połączona z siecią wirtualną w celu rozwiązania określonych domen.
- **Użyj niestandardowego serwera DNS**. Można użyć własnego serwera DNS, aby zastąpić rozpoznawanie DNS dla danego zasobu łącza prywatnego. Jeśli [serwer DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) jest obsługiwany w sieci wirtualnej, można utworzyć regułę przekazywania DNS, aby użyć prywatnej strefy DNS w celu uproszczenia konfiguracji wszystkich zasobów łączy prywatnych.
 
> [!IMPORTANT]
> Nie zaleca się zastępowania strefy, która jest aktywnie używana do rozpoznawania publicznych punktów końcowych. Połączenia z zasobami nie będą mogły poprawnie rozwiązać bez przekierowania DNS do publicznego systemu DNS. Aby uniknąć problemów, utwórz inną nazwę domeny lub postępuj zgodnie z sugerowaną nazwą dla każdej usługi poniżej. 
 
W przypadku usług platformy Azure należy używać nazw stref zgodnie z opisem w poniższej tabeli:

|Typ zasobu Łącza prywatnego   |Podźródło  |Nazwa strefy  |
|---------|---------|---------|
|Baza danych SQL (Microsoft.Sql/servers)    |  Sql Server (sqlServer)        |   privatelink.database.windows.net       |
|Usługa Azure Synapse Analytics (Microsoft.Sql/servers)    |  Sql Server (sqlServer)        | privatelink.database.windows.net |
|Konto magazynu (Microsoft.Storage/storageKondy)    |  Obiekt blob (obiekt blob, blob_secondary)        |    privatelink.blob.core.windows.net      |
|Konto magazynu (Microsoft.Storage/storageKondy)    |    Tabela (tabela, table_secondary)      |   privatelink.table.core.windows.net       |
|Konto magazynu (Microsoft.Storage/storageKondy)    |    Kolejka (kolejka, queue_secondary)     |   privatelink.queue.core.windows.net       |
|Konto magazynu (Microsoft.Storage/storageKondy)   |    Plik (plik, file_secondary)      |    privatelink.file.core.windows.net      |
|Konto magazynu (Microsoft.Storage/storageKondy)     |  Web (www, web_secondary)        |    privatelink.web.core.windows.net      |
|Data Lake File System Gen2 (Microsoft.Storage/storageKondykony)  |  Data Lake File System Gen2 (dfs, dfs_secondary)        |     privatelink.dfs.core.windows.net     |
|Usługa Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseKolejki)|SQL |privatelink.documents.azure.com|
|Usługa Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseKolejki)|MongoDB |privatelink.mongo.cosmos.azure.com|
|Usługa Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseKolejki)|Cassandra|privatelink.cassandra.cosmos.azure.com|
|Usługa Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseKolejki)|Gremlin |privatelink.gremlin.cosmos.azure.com|
|Usługa Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseKolejki)|Tabela|privatelink.table.cosmos.azure.com|
|Usługa Azure Database for PostgreSQL — pojedynczy serwer (Microsoft.DBforPostgreSQL/servers)|postgresqlServer|privatelink.postgres.database.azure.com|
|Usługa Azure Database for MySQL (Microsoft.DBforMySQL/servers)|serwer mysqlServer|privatelink.mysql.database.azure.com|
|Usługa Azure Database for MariaDB (Microsoft.DBforMariaDB/servers)|mariadbServer (MariadbServer)|privatelink.mariadb.database.azure.com|
|Usługa Azure Key Vault (Microsoft.KeyVault/vaults)|magazyn|privatelink.vaultcore.azure.net|
|Usługa Kubernetes platformy Azure — interfejs API usługi Kubernetes (Microsoft.ContainerService/managedClusters) | managedCluster | {guid}.privatelink. <region>azmk8s.io .azmk8s.io|
|Usługa Azure Search (Microsoft.Search/searchServices)|wyszukiwanie Usługa|privatelink.search.windows.net|   
|Usługa Azure Container Registry (Microsoft.ContainerRegistry/registryries) | registry | privatelink.azurecr.io |
|Konfiguracja aplikacji platformy Azure (Microsoft.Appconfiguration/configurationStores)| konfiguracjaSklep | privatelink.azconfig.io|
|Kopia zapasowa platformy Azure (Microsoft.RecoveryServices/vaults)| magazyn |privatelink. {region}.backup.windowsazure.com|
|Usługa Azure Event Hub (Microsoft.EventHub/przestrzenie nazw)| namespace |privatelink.servicebus.windows.net|
|Usługa Azure Service Bus (Microsoft.ServiceBus/namespaces) | namespace |privatelink.servicebus.windows.net|
|Azure Relay (Microsoft.Relay/namespaces) | namespace |privatelink.servicebus.windows.net|
|Usługa Azure Event Grid (Microsoft.EventGrid/topics)   | temat | Temat. {region}.privatelink.eventgrid.azure.net|
|Usługa Azure Event Grid (Microsoft.EventGrid/domains) | domena | Domeny. {region}.privatelink.eventgrid.azure.net |
|Usługi Azure WebApps (microsoft.web/sites)    | lokacja | privatelink.azurewebsites.net |
|Azure Machine Learning(Microsoft.MachineLearningServices/workspaces)   | obszar roboczy | privatelink.api.azureml.ms |
 
Platforma Azure utworzy rekord DNS nazwy kanonicznej (CNAME) w publicznym systemie DNS, aby przekierować rozdzielczość do sugerowanych nazw domen. Rozdzielczość można zastąpić prywatnym adresem IP prywatnych punktów końcowych. 
 
Aplikacje nie muszą zmieniać adresu URL połączenia. Podczas próby rozwiązania problemu przy użyciu publicznego systemu DNS serwer DNS będzie teraz rozpoznawany w prywatnych punktach końcowych. Proces nie ma wpływu na aplikacje. 
 
## <a name="limitations"></a>Ograniczenia
 
Poniższa tabela zawiera listę znanych ograniczeń podczas korzystania z prywatnych punktów końcowych: 


|Ograniczenia |Opis |Środki zaradcze  |
|---------|---------|---------|
|Reguły sieciowej grupy zabezpieczeń (NSG) i trasy zdefiniowane przez użytkownika nie mają zastosowania do prywatnego punktu końcowego    |Sieć sieciowa nie jest obsługiwana w prywatnych punktach końcowych. Podczas gdy podsieci zawierające prywatny punkt końcowy mogą mieć skojarzoną z nią sieć sieciowej sieciowej, reguły nie będą skuteczne w ruchu przetwarzanym przez prywatny punkt końcowy. Aby wdrożyć prywatne punkty końcowe w podsieci, musi [być wyłączone wymuszanie zasad sieciowych.](disable-private-endpoint-network-policy.md) NSG jest nadal wymuszane w innych obciążeń hostowanych w tej samej podsieci. Trasy w dowolnej podsieci klienta będą używać prefiksu /32, zmiana domyślnego zachowania routingu wymaga podobnego UDR  | Kontroluj ruch przy użyciu reguł sieciowej sieciowej dla ruchu wychodzącego na klientach źródłowych. Wdrażanie poszczególnych tras z prefiksem /32 w celu zastąpienia prywatnych tras punktów końcowych. Dzienniki przepływu sieciowych i informacje o monitorowaniu połączeń wychodzących są nadal obsługiwane i mogą być używane        |


## <a name="next-steps"></a>Następne kroki
- [Tworzenie prywatnego punktu końcowego dla programu SQL Database Server przy użyciu portalu](create-private-endpoint-portal.md)
- [Tworzenie prywatnego punktu końcowego dla programu SQL Database Server przy użyciu programu PowerShell](create-private-endpoint-powershell.md)
- [Tworzenie prywatnego punktu końcowego dla programu SQL Database Server przy użyciu interfejsu wiersza polecenia](create-private-endpoint-cli.md)
- [Tworzenie prywatnego punktu końcowego dla magazynu przy użyciu portalu](create-private-endpoint-storage-portal.md)
- [Tworzenie prywatnego punktu końcowego dla konta usługi Azure Cosmos przy użyciu portalu](../cosmos-db/how-to-configure-private-endpoints.md)
- [Tworzenie własnej usługi łącze prywatne przy użyciu programu Azure PowerShell](create-private-link-service-powershell.md)
- [Tworzenie własnego łącza prywatnego dla bazy danych usługi Azure dla postgreSQL — pojedynczy serwer przy użyciu portalu](../postgresql/howto-configure-privatelink-portal.md)
- [Tworzenie własnego łącza prywatnego dla bazy danych usługi Azure dla postgreSQL — pojedynczy serwer przy użyciu interfejsu wiersza polecenia](../postgresql/howto-configure-privatelink-cli.md)
- [Tworzenie własnego łącza prywatnego dla bazy danych platformy Azure dla mysql przy użyciu portalu](../mysql/howto-configure-privatelink-portal.md)
- [Tworzenie własnego łącza prywatnego dla bazy danych platformy Azure dla mysql przy użyciu interfejsu wiersza polecenia](../mysql/howto-configure-privatelink-cli.md)
- [Tworzenie własnego łącza prywatnego dla bazy danych usługi Azure dla bazy danych MariaDB przy użyciu portalu](../mariadb/howto-configure-privatelink-portal.md)
- [Tworzenie własnego łącza prywatnego dla bazy danych usługi Azure dla bazy danych MariaDB przy użyciu interfejsu wiersza polecenia](../mariadb/howto-configure-privatelink-cli.md)
