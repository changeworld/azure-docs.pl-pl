# [Dokumentacja usługi Virtual Network](index.md)

# Omówienie
## [Sieci wirtualne](virtual-networks-overview.md)
## [Routing](virtual-networks-udr-overview.md)
## [Wirtualne sieci równorzędne](virtual-network-peering-overview.md)
## [Punkty końcowe usługi sieci wirtualnej](virtual-network-service-endpoints-overview.md)
## [Sieć wirtualna dla usług platformy Azure](virtual-network-for-azure-services.md)
## [Bezpieczeństwo](security-overview.md)
## [Sieć kontenerów](container-networking.md)
## [Ciągłość działalności biznesowej:](virtual-network-disaster-recovery-guidance.md)
## [Adresowanie IP](virtual-network-ip-addresses-overview-arm.md)
## [DDoS Protection](ddos-protection-overview.md)
## [Często zadawane pytania](virtual-networks-faq.md)
## Wdrożenie klasyczne
### [Adresowanie IP](virtual-network-ip-addresses-overview-classic.md)
### [Listy kontroli dostępu](virtual-networks-acl.md)

# Rozpoczęcie pracy
## [Create virtual network - Portal (Tworzenie sieci wirtualnej przy użyciu witryny Portal)](quick-create-portal.md)
## [Create virtual network - PowerShell (Tworzenie sieci wirtualnej przy użyciu programu PowerShell)](quick-create-powershell.md)
## [Create virtual network - Azure CLI (Tworzenie sieci wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure)](quick-create-cli.md)

# Instrukcje
## Planowanie i projektowanie
### [Sieci wirtualne](virtual-network-vnet-plan-design-arm.md)
### [Sieciowe grupy zabezpieczeń](virtual-networks-nsg.md)

## Wdrażanie

### Grupy zabezpieczeń sieci
#### [Azure PowerShell](tutorial-filter-network-traffic.md)
#### [Interfejs wiersza polecenia platformy Azure](tutorial-filter-network-traffic-cli.md)
#### Bez grup zabezpieczeń aplikacji
##### [Azure Portal](virtual-networks-create-nsg-arm-pportal.md)
##### [Szablon](virtual-networks-create-nsg-arm-template.md)
#### Wdrożenie klasyczne
##### [Azure PowerShell](virtual-networks-create-nsg-classic-ps.md)
##### [Interfejs wiersza polecenia platformy Azure 1.0](virtual-networks-create-nsg-classic-cli.md)

### Tabele tras
#### [Azure Portal](tutorial-create-route-table-portal.md)
#### [Azure PowerShell](tutorial-create-route-table-powershell.md)
#### [Interfejs wiersza polecenia platformy Azure](tutorial-create-route-table-cli.md)
#### [Szablon](virtual-network-create-udr-arm-template.md)
#### Wdrożenie klasyczne
##### [Azure PowerShell](virtual-network-create-udr-classic-ps.md)
##### [Interfejs wiersza polecenia platformy Azure](virtual-network-create-udr-classic-cli.md)

### Wirtualne sieci równorzędne
#### Ten sam model wdrażania — ta sama subskrypcja
##### [Azure Portal](tutorial-connect-virtual-networks-portal.md)
##### [Azure PowerShell](tutorial-connect-virtual-networks-powershell.md)
##### [Interfejs wiersza polecenia platformy Azure](tutorial-connect-virtual-networks-cli.md)
#### [Same deployment model - different subscriptions (Ten sam model wdrażania — różne subskrypcje)](create-peering-different-subscriptions.md)
#### [Different deployment models - same subscription (Różne modele wdrażania — ta sama subskrypcja)](create-peering-different-deployment-models.md)
#### [Different deployment models - different subscriptions (Różne modele wdrażania — różne subskrypcje)](create-peering-different-deployment-models-subscriptions.md)

### Punkty końcowe usługi sieci wirtualnej
#### [Azure Portal](tutorial-restrict-network-access-to-resources.md)
#### [Azure PowerShell](tutorial-restrict-network-access-to-resources-powershell.md)
#### [Interfejs wiersza polecenia platformy Azure](tutorial-restrict-network-access-to-resources-cli.md)

### Maszyny wirtualne
#### [Przepustowość sieci maszyny wirtualnej](virtual-machine-network-throughput.md)
#### Tworzenie maszyny wirtualnej ze statycznym publicznym adresem IP
##### [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
##### [Azure PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
##### [Interfejs wiersza polecenia platformy Azure](virtual-network-deploy-static-pip-arm-cli.md)
##### [Szablon](virtual-network-deploy-static-pip-arm-template.md)
##### Wdrożenie klasyczne
###### [Azure PowerShell](virtual-networks-reserved-public-ip.md)

#### Tworzenie maszyny wirtualnej — statyczny prywatny adres IP
##### [Azure Portal](virtual-networks-static-private-ip-arm-pportal.md)
##### [Azure PowerShell](virtual-networks-static-private-ip-arm-ps.md)
##### [Interfejs wiersza polecenia platformy Azure](virtual-networks-static-private-ip-arm-cli.md)
##### Wdrożenie klasyczne
###### [Azure Portal](virtual-networks-static-private-ip-classic-pportal.md)
###### [Azure PowerShell](virtual-networks-static-private-ip-classic-ps.md)
###### [Interfejs wiersza polecenia platformy Azure](virtual-networks-static-private-ip-classic-cli.md)

#### Tworzenie maszyny wirtualnej — wiele interfejsów sieciowych
##### [Azure PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Interfejs wiersza polecenia platformy Azure](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Szablon](virtual-network-deploy-multinic-arm-template.md)

##### Wdrożenie klasyczne
###### [Azure PowerShell](virtual-network-deploy-multinic-classic-ps.md)
###### [Interfejs wiersza polecenia platformy Azure](virtual-network-deploy-multinic-classic-cli.md)

#### Tworzenie maszyny wirtualnej — wiele adresów IP
##### [Azure Portal](virtual-network-multiple-ip-addresses-portal.md)
##### [Azure PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
##### [Interfejs wiersza polecenia platformy Azure](virtual-network-multiple-ip-addresses-cli.md)
##### [Szablon](virtual-network-multiple-ip-addresses-template.md)

#### Tworzenie maszyny wirtualnej — przyspieszona sieć
##### [Azure PowerShell](create-vm-accelerated-networking-powershell.md)
##### [Interfejs wiersza polecenia platformy Azure](create-vm-accelerated-networking-cli.md)

### Scenariusze łączności
#### [Z sieci wirtualnej do sieci wirtualnej](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Z sieci wirtualnej (Resource Manager) do sieci wirtualnej (klasycznej)](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Z sieci wirtualnej do sieci lokalnej (VPN)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Z sieci wirtualnej do sieci lokalnej (ExpressRoute)](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Wysoko dostępna hybrydowa architektura sieci](../guidance/guidance-hybrid-network-expressroute-vpn-failover.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### Scenariusze zabezpieczeń
#### [Zabezpieczanie sieci przy użyciu urządzeń wirtualnych](virtual-network-scenario-udr-gw-nva.md)
#### [Strefa DMZ między platformą Azure i Internetem](../guidance/guidance-iaas-ra-secure-vnet-dmz.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Zabezpieczenia usługi w chmurze i sieci](../best-practices-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Tworzenie strefy DMZ z sieciowymi grupami zabezpieczeń](virtual-networks-dmz-nsg.md)
##### [Tworzenie strefy DMZ z sieciowymi grupami zabezpieczeń (wersja klasyczna)](virtual-networks-dmz-nsg-asm.md)
##### [Tworzenie strefy DMZ z zaporą i sieciowymi grupami zabezpieczeń (wersja klasyczna)](virtual-networks-dmz-nsg-fw-asm.md)
##### [Strefa DMZ z zaporą, routingiem zdefiniowanym przez użytkownika i sieciowymi grupami zabezpieczeń (wersja klasyczna)](virtual-networks-dmz-nsg-fw-udr-asm.md)

##### [Przykładowa aplikacja](virtual-networks-sample-app.md)

### Wdrożenie klasyczne
#### [Sieć wirtualna](create-virtual-network-classic.md)
##### [Azure Portal](virtual-networks-create-vnet-classic-pportal.md)
##### [Azure PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md)
##### [Interfejs wiersza polecenia platformy Azure](virtual-networks-create-vnet-classic-cli.md)
#### [Określanie ustawień usługi DNS w pliku konfiguracji sieci wirtualnej](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)
#### [Określanie ustawień usługi DNS w pliku konfiguracji usługi](virtual-networks-specifying-dns-settings-in-a-service-configuration-file.md)

## Konfigurowanie
### Maszyny wirtualne
#### [Dodawanie lub usuwanie interfejsów sieciowych](virtual-network-network-interface-vm.md)
#### [Rozpoznawanie nazw dla maszyn wirtualnych i usług w chmurze](virtual-networks-name-resolution-for-vms-and-role-instances.md)
#### [Używanie dynamicznej usługi DNS do rejestrowania nazw hostów na własnym serwerze DNS](virtual-networks-name-resolution-ddns.md)
#### [Optymalizowanie przepływności sieci](virtual-network-optimize-network-bandwidth.md)
#### [Wyświetlanie i modyfikowanie nazw hostów](virtual-networks-viewing-and-modifying-hostnames.md)
#### Wdrożenie klasyczne
##### Statyczne adresy IP
###### [Program PowerShell](virtual-networks-reserved-private-ip.md)
###### [Interfejs wiersza polecenia](virtual-networks-static-private-ip-classic-cli.md)
##### [Publiczny adres IP na poziomie wystąpienia](virtual-networks-instance-level-public-ip.md)

### Wdrożenie klasyczne
#### Listy kontroli dostępu
##### [Azure Portal](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Azure PowerShell](virtual-networks-acl-powershell.md)

## Zarządzanie
### [Sieci wirtualne](manage-virtual-network.md)
#### [Podsieci](virtual-network-manage-subnet.md)
#### [Komunikacja równorzędna](virtual-network-manage-peering.md)
#### Wdrożenie klasyczne
##### [Plik konfiguracji sieci](virtual-networks-using-network-configuration-file.md)
##### [Migrowanie z grupy koligacji do regionu](virtual-networks-migrate-to-regional-vnet.md)
### Grupy zabezpieczeń sieci
#### [Azure Portal](virtual-network-manage-nsg-arm-portal.md)
#### [Azure PowerShell](virtual-network-manage-nsg-arm-ps.md)
#### [Interfejs wiersza polecenia platformy Azure](virtual-network-manage-nsg-arm-cli.md)

#### [Dzienniki](virtual-network-nsg-manage-log.md)
### [Tabele tras](manage-route-table.md)
### Interfejsy sieciowe
#### [Tworzenie, zmienianie lub usuwanie interfejsów sieciowych](virtual-network-network-interface.md)
#### [Dodawanie, zmienianie lub usuwanie adresów IP](virtual-network-network-interface-addresses.md)
### Maszyny wirtualne
#### [Przenoszenie maszyny wirtualnej do innej podsieci](virtual-networks-move-vm-role-to-subnet.md)
### [Publiczne adresy IP](virtual-network-public-ip-address.md)
### DDoS Protection
#### [Azure Portal](ddos-protection-manage-portal.md)
#### [Azure PowerShell](ddos-protection-manage-ps.md)

## Rozwiązywanie problemów
### Grupy zabezpieczeń sieci
#### [Azure Portal](virtual-network-nsg-troubleshoot-portal.md)
#### [Azure PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
### Trasy
#### [Azure Portal](virtual-network-routes-troubleshoot-portal.md)
#### [Azure PowerShell](virtual-network-routes-troubleshoot-powershell.md)
### [Testowanie przepływności](virtual-network-bandwidth-testing.md)
### [Cannot delete virtual networks (Nie można usunąć sieci wirtualnych)](virtual-network-troubleshoot-cannot-delete-vnet.md)
### [Problemy z łącznością między maszynami wirtualnymi](virtual-network-troubleshoot-connectivity-problem-between-vms.md)
### [Konfigurowanie rekordu PTR na potrzeby kontroli baneru SMTP](create-ptr-for-smtp-service.md)

## Przykładowe skrypty
### [Interfejs wiersza polecenia platformy Azure](cli-samples.md)
### [Azure PowerShell](powershell-samples.md)

# Dokumentacja
## [Przykłady kodu](https://azure.microsoft.com/resources/samples/?service=virtual-network)
## [Azure PowerShell (Resource Manager)](/powershell/module/azurerm.network)
## [Azure PowerShell (wersja klasyczna)](/powershell/module/azure/)
## [Interfejs wiersza polecenia platformy Azure](/cli/azure/network)
## [Java](/java/api/)
## [REST (Resource Manager)](https://msdn.microsoft.com/library/mt163658.aspx)
## [REST (klasyczny)](https://msdn.microsoft.com/library/jj157182.aspx)


# Powiązane
## [Virtual Machines](/azure/virtual-machines/)
## [Application Gateway](/azure/application-gateway/)
## [System DNS platformy Azure](/azure/dns/)
## [Traffic Manager](/azure/traffic-manager/)
## [Load Balancer](/azure/load-balancer/)
## [VPN Gateway](/azure/vpn-gateway/)
## [ExpressRoute](/azure/expressroute/)

# Zasoby
## [Harmonogram działania dla platformy Azure](https://azure.microsoft.com/roadmap/?category=networking)
## [Blog na temat sieci](http://azure.microsoft.com/blog/topics/networking)
## [Forum sieci](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [Cennik](https://azure.microsoft.com/pricing/details/virtual-network)
## [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-network)
## [Dostawca zasobów sieciowych](resource-groups-networking.md)
