---
title: Co to jest łącze prywatne platformy Azure?
description: Dowiedz się, jak korzystać z prywatnego linku platformy Azure, aby uzyskiwać dostęp do usług Azure PaaS Services (na przykład Azure Storage i SQL Database) oraz hostowanych usług klienta i partnerskich platformy Azure za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: overview
ms.date: 11/04/2019
ms.author: allensu
ms.openlocfilehash: 03fdd67adf5df440cafe4e7411064437b8ef89ac
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671555"
---
# <a name="what-is-azure-private-link-preview"></a>Co to jest łącze prywatne platformy Azure? (Wersja zapoznawcza)
Link prywatny platformy Azure umożliwia dostęp do usług Azure PaaS Services (na przykład Azure Storage, Azure Cosmos DB i SQL Database) oraz hostowanych usług klienta i partnerskich platformy Azure za pośrednictwem [prywatnego punktu końcowego](private-endpoint-overview.md) w sieci wirtualnej. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Możesz również utworzyć własną [prywatną usługę linku](private-link-service-overview.md) w sieci wirtualnej (VNET) i dostarczyć ją prywatnie do klientów. Środowisko instalacji i użycia korzystające z prywatnego linku platformy Azure jest spójne w przypadku usługi Azure PaaS, należącej do klienta i udostępnionych usług partnerskich.

> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Aby uzyskać znane ograniczenia, zobacz [prywatny punkt końcowy](private-endpoint-overview.md#limitations) i [Usługa łącza prywatnego](private-link-service-overview.md#limitations).


![Omówienie prywatnego punktu końcowego](media/private-link-overview/private-endpoint.png)

## <a name="key-benefits"></a>Najważniejsze korzyści
Połączenie prywatne platformy Azure zapewnia następujące korzyści:  
- **Usługi dostępu prywatnego na platformie Azure**: łączenie sieci wirtualnej z usługami uruchomionymi na platformie Azure prywatnie bez konieczności publicznego adresu IP w lokalizacji źródłowej lub docelowej. Dostawcy usług mogą samodzielnie renderować swoje usługi w swojej sieci wirtualnej, a konsumenci mogą uzyskiwać dostęp do tych usług prywatnie w lokalnej sieci wirtualnej. Platforma linków prywatnych będzie obsługiwała łączność między klientem i usługami za pośrednictwem sieci szkieletowej platformy Azure. 
 
- **Sieci lokalne i równorzędne**: usługi dostępu uruchomione na platformie Azure pochodzą z zasobów lokalnych za pośrednictwem prywatnej komunikacji równorzędnej (ExpressRoute)/tuneli VPN (z lokalnego) i równorzędnych sieci wirtualnych za pomocą prywatnych punktów końcowych. Nie trzeba konfigurować publicznej komunikacji równorzędnej ani przechodzić przez Internet w celu uzyskania dostępu do usługi. Ta możliwość zapewnia bezpieczny sposób migracji obciążeń do platformy Azure.
 
- **Ochrona przed eksfiltracji danych**: przy użyciu prywatnego linku platformy Azure prywatny punkt końcowy w sieci wirtualnej jest mapowany do określonego wystąpienia zasobu PaaS klienta, a nie całej usługi. Korzystanie z prywatnych odbiorców punktu końcowego może łączyć się tylko z określonym zasobem, a nie z żadnym innym zasobem w usłudze. Mechanizm ten zapewnia ochronę przed zagrożeniami związanymi z eksfiltracji danych. 
 
- **Globalne zasięg**: Połącz się prywatnie z usługami działającymi w innych regionach. Oznacza to, że sieć wirtualna konsumenta może znajdować się w regionie A i może łączyć się z usługami za pośrednictwem prywatnego linku w regionie B.  
 
- Zapoznaj **się z własnymi usługami: Korzystaj**z tego samego środowiska i funkcjonalności, aby samodzielnie renderować swoją usługę klientom na platformie Azure. Przez umieszczenie usługi za usługa Load Balancer w warstwie Standardowa można ją włączyć dla linku prywatnego. Konsument może następnie połączyć się bezpośrednio z usługą przy użyciu prywatnego punktu końcowego we własnej sieci wirtualnej. Tymi żądaniami połączeń można zarządzać przy użyciu prostego przepływu wywołań zatwierdzania. Połączenie prywatne platformy Azure działa również dla klientów i usług należących do różnych Active Directory dzierżawców. 

## <a name="availability"></a>Dostępność 
 Poniższa tabela zawiera listę prywatnych usług linków oraz regionów, w których są one dostępne. 

|Scenariusz  |Obsługiwane usługi  |Dostępne regiony | Stan  |
|:---------|:-------------------|:-----------------|:--------|
|Prywatne łącze dla usług należących do klienta|Usługi linków prywatnych w tle usługa Load Balancer w warstwie Standardowa | Wszystkie regiony publiczne  | Wersja zapoznawcza  |
|Link prywatny dla usług Azure PaaS Services   | Azure Storage        |  Wszystkie regiony publiczne      | Wersja zapoznawcza <br/> [Dowiedz się więcej](/azure/storage/common/storage-private-endpoints).  |
|  | Usługa Azure Data Lake Storage 2. generacji        |  Wszystkie regiony publiczne      | Wersja zapoznawcza <br/> [Dowiedz się więcej](/azure/storage/common/storage-private-endpoints).  |
|  |  Azure SQL Database         | Wszystkie regiony publiczne      |   Wersja zapoznawcza      |
||Azure SQL Data Warehouse| Wszystkie regiony publiczne |Wersja zapoznawcza|
||Azure Cosmos DB| Zachodnio-środkowe stany USA, zachodnie stany USA |Wersja zapoznawcza|

Aby zapoznać się z najbardziej aktualnymi powiadomieniami, zapoznaj się ze [stroną usługi Azure Virtual Network Updates](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

Usługa Azure Private link jest zintegrowana z usługą Azure Monitor, która umożliwia archiwizowanie dzienników na koncie magazynu, przesyłanie strumieniowe zdarzeń do centrum zdarzeń lub wysyłanie ich do dzienników Azure Monitor. Na Azure Monitor można uzyskać dostęp do następujących informacji: 
- **Prywatny punkt końcowy**: dane przetworzone przez prywatny punkt końcowy (WE/out)
 
- **Usługa link prywatny**:
    - Dane przetwarzane przez usługę linku prywatnego (WE/wychodzącym)
    - Dostępność portów NAT  
 
## <a name="pricing"></a>Cennik   
Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik usługi Azure Private link](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>Często zadawane pytania  
Często zadawane pytania można znaleźć w sekcji [często zadawane pytania dotyczące linków prywatnych platformy Azure](private-link-faq.md).
 
## <a name="limits"></a>Limity  
Aby uzyskać limity, zobacz [limity linków prywatnych platformy Azure](../azure-subscription-service-limits.md#private-link-limits).

## <a name="next-steps"></a>Następne kroki
- [Tworzenie prywatnego punktu końcowego dla serwera SQL Database przy użyciu portalu](create-private-endpoint-portal.md)
- [Tworzenie prywatnego punktu końcowego dla serwera SQL Database przy użyciu programu PowerShell](create-private-endpoint-powershell.md)
- [Tworzenie prywatnego punktu końcowego dla serwera SQL Database przy użyciu interfejsu wiersza polecenia](create-private-endpoint-cli.md)
- [Tworzenie prywatnego punktu końcowego dla konta magazynu przy użyciu portalu](create-private-endpoint-storage-portal.md)
- [Tworzenie prywatnego punktu końcowego dla konta usługi Azure Cosmos za pomocą portalu](../cosmos-db/how-to-configure-private-endpoints.md)
- [Tworzenie własnej usługi linku prywatnego przy użyciu Azure PowerShell](create-private-link-service-powershell.md)


 
