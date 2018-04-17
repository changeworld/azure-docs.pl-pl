---
title: Azure wystąpienia bazy danych SQL zarządzane połączenia aplikacji | Dokumentacja firmy Microsoft
description: W tym artykule omówiono sposób połączyć aplikację z wystąpienia zarządzane bazy danych SQL Azure.
ms.service: sql-database
author: srdjan-bozovic
manager: craigg
ms.custom: managed instance
ms.topic: article
ms.date: 04/10/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 1eecd28d5e7043acae5cfd52edf93e8d301bd31e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Połączyć aplikację z wystąpienia zarządzane bazy danych SQL Azure

Obecnie masz wiele wyborów podczas podejmowania decyzji o, jak i gdzie hostowania aplikacji. 
 
Możesz udostępniać aplikacji w chmurze przy użyciu usługi Azure App Service lub opcji sieć wirtualną (VNet) zintegrowane platformy Azure, takich jak środowisko usługi aplikacji Azure, maszyny wirtualnej zestawu skalowania maszyn wirtualnych. Można również zastosowaniu podejścia chmur hybrydowych i Zachowaj aplikacji lokalnej. 
 
Niezależnie od wyboru wprowadzono, można połączyć go do wystąpienia zarządzane (wersja zapoznawcza).  

![Wysoka dostępność](./media/sql-database-managed-instance/application-deployment-topologies.png)  

## <a name="connect-an-application-inside-the-same-vnet"></a>Łączenie aplikacji w tej samej sieci wirtualnej 

Ten scenariusz jest najprostsza. Maszyny wirtualne w sieci wirtualnej można połączyć ze sobą bezpośrednio, nawet jeśli znajdują się w różnych podsieciach. Oznacza to, że wszystkie informacje wymagane do połączenia aplikacji wewnątrz środowiska aplikacji Azure lub maszyny wirtualnej jest odpowiednio ustawione parametry połączenia.  
 
W przypadku, gdy nie można ustanowić połączenia, sprawdź, czy istnieje sieciowej grupy zabezpieczeń, ustaw w podsieci aplikacji. W takim przypadku należy otworzyć połączeń wychodzących na SQL portu 1433, a także 11000 12000 zakres portów dla przekierowania. 

## <a name="connect-an-application-inside-a-different-vnet"></a>Łączenie aplikacji w innej sieci wirtualnej 

Ten scenariusz jest nieco bardziej skomplikowane, ponieważ wystąpienie zarządzane ma prywatnego adresu IP w jego własnej sieci wirtualnej. Aby połączyć, aplikacji wymaga dostępu do sieci wirtualnej wdrożonym zarządzane wystąpienia. Tak należy najpierw utworzyć połączenie między aplikacją a sieciami wirtualnymi wystąpienia zarządzane. Sieci wirtualne nie muszą być w tej samej subskrypcji, aby zrealizować ten scenariusz. 
 
Dostępne są dwie opcje do łączenia sieci wirtualnych: 
- [Komunikacji równorzędnej sieci wirtualnej platformy Azure](../virtual-network/virtual-network-peering-overview.md) 
- Aby wirtualnymi bramy sieci VPN ([portalu Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [interfejsu wiersza polecenia Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md)) 
 
Ta opcja komunikacji równorzędnej jest to preferowane, ponieważ równorzędna używa sieci firmy Microsoft w sieci szkieletowej tak, z punktu widzenia łączności, nie ma żadnej różnicy zauważalnego opóźnienia między maszynami wirtualnymi w połączyć za pomocą sieci wirtualnej, a w tej samej sieci wirtualnej. Sieci wirtualnej komunikacji równorzędnej jest ograniczona do sieci, w tym samym regionie, mimo że komunikacji równorzędnej między region jest włączony w pewnych regionach jako podgląd.  
 
> [!IMPORTANT]
> Komunikacji równorzędnych sieci wirtualnej utworzone między region nie może mieć taki sam poziom dostępności i niezawodności jako komunikacji równorzędnych w wersji ogólnodostępnej. Komunikacji równorzędnych sieci wirtualnej mogą mieć ograniczone możliwości i może nie być dostępne we wszystkich regionach platformy Azure. Najbardziej aktualne powiadomień o dostępności i stan tej funkcji, sprawdź [sieci wirtualnej Azure](https://azure.microsoft.com/updates/?product=virtual-network) aktualizacji strony. 

## <a name="connect-an-on-premises-application"></a>Łączenie aplikacji lokalnie 

Wystąpienia zarządzane można uzyskać tylko za pośrednictwem prywatnego adresu IP. Aby uruchomić je lokalnie, należy utworzyć połączenie lokacja-lokacja między aplikacją a sieciami wirtualnymi wystąpienia zarządzane. 
 
Jak nawiązać lokalną sieć wirtualną Azure dostępne są dwie opcje: 
- Połączenia sieci VPN lokacja-lokacja ([portalu Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [interfejsu wiersza polecenia Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)) 
- [ExpressRoute](../expressroute/expressroute-introduction.md) połączenia  
 
Nie można ustanowić połączenia z wystąpieniem zarządzane lokalnego do platformy Azure połączenie zostało ustanowione pomyślnie, sprawdź zapory otwartego połączenia wychodzącego na SQL portu 1433, a także 11000 12000 zakres portów dla przekierowania. 

## <a name="connect-an-azure-app-service-hosted-application"></a>Łączenie aplikacji hostowanej usługi aplikacji Azure 

Wystąpienia zarządzane są dostępne tylko za pośrednictwem prywatnego adresu IP, tak aby można było do niego dostęp z usługi aplikacji Azure należy najpierw utworzyć połączenie między aplikacją a sieciami wirtualnymi wystąpienia zarządzane. Zobacz [integracji aplikacji z sieci wirtualnej platformy Azure](../app-service/web-sites-integrate-with-vnet.md).  
 
Do rozwiązywania problemów, zobacz [Rozwiązywanie problemów z sieci wirtualnych i aplikacji](../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Jeśli nie można ustanowić połączenia, spróbuj [synchronizowanie konfiguracji sieci](sql-database-managed-instance-sync-network-configuration.md). 
 
Szczególnych przypadkach nawiązywanie połączeń zarządzane wystąpienia usługi Azure App Service jest po zintegrowaniu możesz usłudze Azure App Service z siecią połączyć za pomocą do zarządzanego sieci wirtualnej wystąpienia. Ten przypadek wymaga następującej konfiguracji można skonfigurować: 

- Zarządzane wystąpienia sieci wirtualnej nie może mieć bramy  
- Zarządzane wystąpienia sieci wirtualnej musi mieć ustawioną opcję Użyj zdalnego bram 
- Połączyć za pomocą sieci wirtualnej musi być ustawiona opcja przesyłania bramy Zezwalaj 
 
W tym scenariuszu przedstawiono na poniższym diagramie:

![Komunikacja równorzędna zintegrowanych aplikacji](./media/sql-database-managed-instance/integrated-app-peering.png)
 
## <a name="connect-an-application-on-the-developers-box"></a>Łączenie aplikacji w polu deweloperów 

Zarządzane wystąpienia jest możliwy tylko za pośrednictwem prywatnego adresu IP tak aby uzyskać dostęp do Twojego polu developer, należy najpierw utworzyć połączenie między Twoje okno deweloperów i wystąpienia zarządzane sieci wirtualnej.  
 
Skonfiguruj połączenie punkt-lokacja sieci wirtualnej za pomocą natywnego certyfikatu Azure authentication artykułów ([portalu Azure](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md), [programu PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md), [interfejsu wiersza polecenia Azure](../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)) przedstawiającego w szczegółach jak może być wykonana.  

## <a name="next-steps"></a>Kolejne kroki

- Informacji o zarządzanych wystąpienie znajduje się w temacie [co to jest wystąpieniem zarządzane](sql-database-managed-instance.md).
- Samouczek pokazuje sposób tworzenia nowego wystąpienia zarządzane, zobacz [utworzyć wystąpienia zarządzanego](sql-database-managed-instance-create-tutorial-portal.md).
