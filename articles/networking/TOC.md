# Omówienie
## [Sieci platformy Azure — informacje](networking-overview.md)
## Architektura
### [Wirtualne centra danych](networking-virtual-datacenter.md)
### [Routing asymetryczny z wieloma ścieżkami sieciowymi](../expressroute/expressroute-asymmetric-routing.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Projekty bezpiecznych sieci](../best-practices-network-security.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Topologia piasty i szprych](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)
### [Najlepsze rozwiązania z zakresu zabezpieczeń sieci](../security/azure-security-network-security-best-practices.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Urządzenia wirtualne sieci o wysokiej dostępności](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha )
### [Łączenie metod równoważenia obciążenia](../traffic-manager/traffic-manager-load-balancing-azure.md?toc=%2fazure%2fnetworking%2ftoc.json)
## Planowanie i projektowanie
### [Sieci wirtualne](../virtual-network/virtual-network-vnet-plan-design-arm.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Połączenia obejmujące wiele lokalizacji — sieć VPN](../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Połączenia obejmujące wiele lokalizacji — dedykowane połączenie prywatne](../expressroute/expressroute-workflows.md?toc=%2fazure%2fnetworking%2ftoc.json)

##  Pojęcia
### [Sieci wirtualne](../virtual-network/virtual-networks-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Równoważenie obciążenia sieciowego](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Równoważenie obciążenia aplikacji](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [DNS](../dns/dns-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Dystrybucja ruchu oparta na systemie DNS](../traffic-manager/traffic-manager-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Połączenie systemów lokalnych — VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Połączenie systemów lokalnych — dedykowane](../expressroute/expressroute-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json)


# Rozpoczęcie pracy
## [Tworzenie pierwszej sieci wirtualnej](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)

# Instrukcje
## Łączność z Internetem
### [Serwery publiczne równoważenia obciążenia sieciowego](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Serwery publiczne równoważenia obciążenia aplikacji](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Ochrona aplikacji sieci Web](../application-gateway/application-gateway-web-application-firewall-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Rozłożenie ruchu na wiele lokalizacji](../traffic-manager/traffic-manager-configure-geographic-routing-method.md?toc=%2fazure%2fnetworking%2ftoc.json)
## Łączność wewnętrzna
### [Serwery prywatne równoważenia obciążenia sieciowego](../load-balancer/load-balancer-get-started-ilb-arm-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Serwery prywatne równoważenia obciążenia aplikacji](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Łączenie sieci wirtualnych (ta sama lokalizacja)](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Łączenie sieci wirtualnych (różne lokalizacje)](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
## Połączenia obejmujące wiele lokalizacji
### [Tworzenie połączenia sieci VPN lokacja-lokacja (IPsec/IKE)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Tworzenie połączenia sieci VPN punkt-lokacja (protokół SSTP z certyfikatami)](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Tworzenie dedykowanego połączenia prywatnego (ExpressRoute)](../expressroute/expressroute-howto-circuit-portal-resource-manager.md?toc=%2fazure%2fnetworking%2ftoc.json)

## Zarządzanie
### [Wyświetlanie topologii sieci](../network-watcher/network-watcher-topology-powershell.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Zarządzanie przechwytywaniem pakietów](../network-watcher/network-watcher-packet-capture-manage-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Ustalanie następnego przeskoku routingu](../network-watcher/network-watcher-check-next-hop-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Sprawdzanie przepływów adresów IP dla maszyny wirtualnej](../network-watcher/network-watcher-check-ip-flow-verify-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Omówienie monitorowania sieci](network-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)

## Przykładowe skrypty
### [Interfejs wiersza polecenia platformy Azure](cli-samples.md)
### [Azure PowerShell](powershell-samples.md)

## Samouczki
### [Równoważenie obciążenia maszyn wirtualnych](../virtual-machines/linux/tutorial-load-balance-nodejs.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Łączenie komputera z siecią wirtualną](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)

# Dokumentacja
## [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/network)
## [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/?view=azurermps-3.8.0)
## [.Net](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.network?view=azuremgmtnetwork-9.1.0-preview)
## [Node.js](https://azure.microsoft.com/develop/nodejs/#azure-sdk)
## [REST](https://msdn.microsoft.com/library/mt163658.aspx)

# Zasoby
## [Tworzenie szablonów](/azure/azure-resource-manager/resource-group-authoring-templates?toc=%2fazure%2fnetworking%2ftoc.json)
## [Harmonogram działania dla platformy Azure](https://azure.microsoft.com/roadmap/?category=networking)
## [Szablony społeczności](https://azure.microsoft.com/resources/templates/)
## [Blog na temat sieci](http://azure.microsoft.com/blog/topics/networking)
## [Cennik](https://azure.microsoft.com/pricing)
## [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/)
## [Dostępność regionalna](https://azure.microsoft.com/regions/services/)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-network)
## [Wideo](https://azure.microsoft.com/resources/videos/index/?services=virtual-network)

