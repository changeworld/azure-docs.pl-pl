---
title: Co to jest łącze prywatne platformy Azure?
description: Omówienie funkcji, architektury i implementacji łącza prywatnego platformy Azure. Dowiedz się, jak działają prywatne punkty końcowe platformy Azure i usługa Azure Private Link i jak z nich korzystać.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 02/27/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: f75f8392b01e69760171c13c0e6d3b2e22b20dea
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421191"
---
# <a name="what-is-azure-private-link"></a>Co to jest łącze prywatne platformy Azure? 
Usługa Azure Private Link umożliwia dostęp do usług PaaS platformy Azure (na przykład usługi Azure Storage i bazy danych SQL) oraz usługi azure hostowane przez klienta/partnera za pośrednictwem [prywatnego punktu końcowego](private-endpoint-overview.md) w sieci wirtualnej.

Ruch między siecią wirtualną a usługą odbywa się w sieci szkieletowej firmy Microsoft. Udostępnianie usługi w publicznym internecie nie jest już konieczne. Możesz utworzyć własną [usługę linków prywatnych](private-link-service-overview.md) w sieci wirtualnej i dostarczyć ją klientom. Konfiguracja i zużycie przy użyciu łącza prywatnego platformy Azure jest spójne w usługach Azure PaaS, należących do klienta i udostępnionych usługach partnerów.

> [!IMPORTANT]
> Usługa Azure Private Link jest teraz ogólnie dostępna. Zazwyczaj dostępne są usługi Private Endpoint i Private Link (usługa za standardowym modułem równoważenia obciążenia). Różne usługi Azure PaaS będzie onboard do usługi Azure Private Link w różnych harmonogramach. Sprawdź [sekcję dostępności](https://docs.microsoft.com/azure/private-link/private-link-overview#availability) poniżej, aby uzyskać dokładny stan usługi Azure PaaS w łączu prywatnym. Aby zapoznać się ze znanymi ograniczeniami, zobacz [Prywatny punkt końcowy](private-endpoint-overview.md#limitations) i usługa łącza [prywatnego](private-link-service-overview.md#limitations). 

![Omówienie prywatnego punktu końcowego](media/private-link-overview/private-endpoint.png)

## <a name="key-benefits"></a>Najważniejsze korzyści
Usługa Azure Private Link zapewnia następujące korzyści:  
- **Prywatny dostęp do usług na platformie Azure:** Połącz swoją sieć wirtualną z usługami na platformie Azure bez publicznego adresu IP u źródła lub miejsca docelowego. Dostawcy usług mogą świadczyć swoje usługi we własnej sieci wirtualnej, a konsumenci mogą uzyskiwać dostęp do tych usług w swojej lokalnej sieci wirtualnej. Platforma Private Link będzie obsługiwać łączność między konsumentem a usługami za pośrednictwem sieci szkieletowej platformy Azure. 
 
- **Sieci lokalne i sieci równorzędne:** dostęp do usług uruchomionych na platformie Azure z lokalnego za pośrednictwem prywatnej komunikacji równorzędnej usługi ExpressRoute, tuneli sieci VPN i sieci równorzędnych przy użyciu prywatnych punktów końcowych. Nie ma potrzeby konfigurowania publicznego komunikacji równorzędnej lub przechodzenia przez Internet, aby dotrzeć do usługi. Private Link zapewnia bezpieczny sposób migracji obciążeń na platformę Azure.
 
- **Ochrona przed wyciekiem danych:** prywatny punkt końcowy jest mapowany na wystąpienie zasobu PaaS zamiast całej usługi. Konsumenci mogą łączyć się tylko z określonym zasobem. Dostęp do dowolnego innego zasobu w usłudze jest zablokowany. Mechanizm ten zapewnia ochronę przed ryzykiem wycieku danych. 
 
- **Globalny zasięg:** Połącz się prywatnie z usługami działającymi w innych regionach. Sieć wirtualna konsumenta może znajdować się w regionie A i może łączyć się z usługami za łączem prywatnym w regionie B.  
 
- **Rozszerz na własne usługi:** Włącz te same środowisko i funkcje, aby prywatnie świadczyć usługę konsumentom na platformie Azure. Umieszczając usługę za standardowym modułem równoważenia obciążenia platformy Azure, można włączyć ją dla łącza prywatnego. Konsument może następnie połączyć się bezpośrednio z usługą przy użyciu prywatnego punktu końcowego we własnej sieci wirtualnej. Żądania połączenia można zarządzać za pomocą przepływu wywołania zatwierdzenia. Usługa Azure Private Link działa dla konsumentów i usług należących do różnych dzierżaw usługi Azure Active Directory. 

## <a name="availability"></a>Dostępność 
 W poniższej tabeli wymieniono usługi łącza prywatnego i regiony, w których są one dostępne. 

|Scenariusz  |Obsługiwane usługi  |Dostępne regiony | Stan  |
|:---------|:-------------------|:-----------------|:--------|
|Link prywatny dla usług należących do klienta |Usługi łącza prywatnego za standardowym modułem równoważenia obciążenia platformy Azure | Wszystkie regiony publiczne  | Ogólna dostępność <br/> [Dowiedz się więcej](https://docs.microsoft.com/azure/private-link/private-link-service-overview) |
|Prywatne łącze dla usług PaaS platformy Azure   | Azure Storage        |  Wszystkie regiony publiczne      | Ogólna dostępność <br/> [Dowiedz się więcej](/azure/storage/common/storage-private-endpoints)  |
|  | Usługa Azure Data Lake Storage 2. generacji        |  Wszystkie regiony publiczne      | Ogólna dostępność <br/> [Dowiedz się więcej](/azure/storage/common/storage-private-endpoints)  |
|  |  Azure SQL Database         | Wszystkie regiony publiczne      |   Ogólna dostępność <br/> [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)      |
|  |Usługa Azure Synapse Analytics (magazyn danych SQL)| Wszystkie regiony publiczne |Ogólna dostępność <br/> [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)|
|  |Azure Cosmos DB|  Wszystkie regiony publiczne |Ogólna dostępność <br/> [Dowiedz się więcej](https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints)|
|  |  Usługa Azure Database for PostgreSQL — pojedynczy serwer         | Wszystkie regiony publiczne      |   Ogólna dostępność <br/> [Dowiedz się więcej](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-private-link)      |
|  |  Azure Database for MySQL         | Wszystkie regiony publiczne      |   Ogólna dostępność <br/> [Dowiedz się więcej](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)     |
|  |  Azure Database for MariaDB         | Wszystkie regiony publiczne      |   Ogólna dostępność <br/> [Dowiedz się więcej](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link)      |
|  |  W usłudze Azure Key Vault         | Wszystkie regiony publiczne      |   Ogólna dostępność   <br/> [Dowiedz się więcej](https://docs.microsoft.com/azure/key-vault/private-link-service)   |
|  |Usługa Kubernetes platformy Azure — interfejs API usługi Kubernetes | Wszystkie regiony publiczne      |   Ogólna dostępność   <br/> [Dowiedz się więcej](https://docs.microsoft.com/azure/aks/private-clusters)   |
|  |Azure Search | WSCHODNIE STANY USA, ZACHODNIE STANY USA2, POŁUDNIOWO-ŚRODKOWE STANY ZJEDNOCZONE |   Wersja zapoznawcza    |
|  |Azure Container Registry | Wszystkie regiony publiczne      |   Wersja zapoznawcza   |
|  |Azure App Configuration | Wszystkie regiony publiczne      |   Wersja zapoznawcza   |
|  |Azure Backup | WSCHODNIE STANY USA, ZACHODNIE STANY USA2, POŁUDNIOWO-ŚRODKOWE STANY ZJEDNOCZONE     |   Wersja zapoznawcza   |
|  |Centrum zdarzeń Azure | Wszystkie regiony publiczne      |   Wersja zapoznawcza    |
|  |Azure Service Bus | Wszystkie regiony publiczne      |   Wersja zapoznawcza   |
|  |Azure Relay | Wszystkie regiony publiczne      |   Wersja zapoznawcza   |
|  |Azure Event Grid| Wszystkie regiony publiczne      |   Wersja zapoznawcza   <br/> [Dowiedz się więcej](https://docs.microsoft.com/azure/event-grid/network-security)   |
|  |Azure Web Apps | WSCHODNIE STANY USA, ZACHODNIE STANY USA2, POŁUDNIOWO-ŚRODKOWE STANY ZJEDNOCZONE      |   Wersja zapoznawcza   <br/> [Dowiedz się więcej](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)   |

Aby uzyskać najbardziej aktualne powiadomienia, sprawdź [stronę aktualizacji sieci wirtualnej platformy Azure](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

Usługa Azure Private Link ma integrację z usługą Azure Monitor. Ta kombinacja pozwala:

 - Archiwizacja dzienników na koncie magazynu.
 - Przesyłanie strumieniowe wydarzeń do Centrum zdarzeń.
 - Rejestrowanie usługi Azure Monitor.

Dostęp do następujących informacji można uzyskać w usłudze Azure Monitor: 
- **Prywatny punkt końcowy:** 
    - Dane przetwarzane przez prywatny punkt końcowy (IN/OUT)
 
- **Usługa Private Link**:
    - Dane przetwarzane przez usługę Private Link (IN/OUT)
    - Dostępność portów NAT  
 
## <a name="pricing"></a>Cennik   
Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik łącza prywatnego platformy Azure](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>Często zadawane pytania  
Aby zapoznać się z często zadawanymi pytaniami, zobacz Często zadawane pytania [dotyczące łącza prywatnego platformy Azure](private-link-faq.md).
 
## <a name="limits"></a>Limity  
Aby uzyskać limity, zobacz [Limity łączy prywatnych platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## <a name="service-level-agreement"></a>Umowa o poziomie usług
W przypadku umowy SLA zobacz [umowy SLA dla łącza prywatnego platformy Azure](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/).

## <a name="next-steps"></a>Następne kroki

- [Szybki start: tworzenie prywatnego punktu końcowego przy użyciu portalu Azure](create-private-endpoint-portal.md)
- [Szybki start: tworzenie usługi łącze prywatne przy użyciu portalu Azure](create-private-link-service-portal.md)


 
