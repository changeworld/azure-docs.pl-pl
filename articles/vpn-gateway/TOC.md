# [Dokumentacja usługi VPN Gateway](index.md)

# Omówienie
## [VPN Gateway — informacje](vpn-gateway-about-vpngateways.md)
## [VPN Gateway — często zadawane pytania](vpn-gateway-vpn-faq.md)
## [Limity usług i subskrypcji](../azure-subscription-service-limits.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)

# Rozpoczęcie pracy
## [Planowanie i projektowanie dla usługi VPN Gateway](vpn-gateway-plan-design.md)
## [Informacje na temat ustawień usługi VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md)
## [Informacje o urządzeniach sieci VPN](vpn-gateway-about-vpn-devices.md)
## [Informacje o wymaganiach kryptograficznych](vpn-gateway-about-compliance-crypto.md)
## [Informacje o protokole BGP i usłudze VPN Gateway](vpn-gateway-bgp-overview.md)
## [Informacje o połączeniach z elementami o wysokiej dostępności](vpn-gateway-highlyavailable.md)
## [Informacje o połączeniach typu punkt-lokacja](point-to-site-about.md)

# Instrukcje
## Konfigurowanie połączeń typu lokacja-lokacja
### [Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
### [Interfejs wiersza polecenia platformy Azure](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
### [Portal Azure (klasyczny)](vpn-gateway-howto-site-to-site-classic-portal.md)

## [Pobieranie skryptów konfiguracji urządzenia sieci VPN](vpn-gateway-download-vpndevicescript.md)

## Konfigurowanie połączeń typu punkt-lokacja — natywne uwierzytelnianie certyfikatu platformy Azure
### Konfigurowanie sieci VPN typu P2S
#### [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
#### [Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
#### [Portal Azure (klasyczny)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
### Generowanie certyfikatów z podpisem własnym
#### [Azure PowerShell](vpn-gateway-certificates-point-to-site.md)
#### [Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
### [Tworzenie i instalowanie plików konfiguracji klienta sieci VPN](point-to-site-vpn-client-configuration-azure-cert.md)
### [Instalowanie certyfikatów klienta](point-to-site-how-to-vpn-client-install-azure-cert.md)

## Konfigurowanie połączeń typu punkt-lokacja — uwierzytelnianie usługi RADIUS
### Konfigurowanie sieci VPN typu P2S
#### [Azure PowerShell](point-to-site-how-to-radius-ps.md)
### [Tworzenie i instalowanie plików konfiguracji klienta sieci VPN](point-to-site-vpn-client-configuration-radius.md)
### [Integrowanie uwierzytelniania RADIUS w sieci VPN typu P2S za pomocą serwera NPS](vpn-gateway-radiuis-mfa-nsp.md)

## Konfigurowanie połączeń między sieciami wirtualnymi
### [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
### [Interfejs wiersza polecenia platformy Azure](vpn-gateway-howto-vnet-vnet-cli.md)
### [Portal Azure (klasyczny)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
## Konfigurowanie połączenia sieć wirtualna-sieć wirtualna między modelami wdrażania
### [Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
### [Azure PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
## Konfigurowanie połączeń między lokacjami i współistniejących połączeń usługi ExpressRoute
### [Azure PowerShell](../expressroute/expressroute-howto-coexist-resource-manager.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)
## Konfigurowanie wielu połączeń typu lokacja-lokacja
### [Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
### [Azure PowerShell (wersja klasyczna)](vpn-gateway-multi-site.md)
## Łączenie wielu urządzeń sieci VPN opartej na zasadach
### [Azure PowerShell](vpn-gateway-connect-multiple-policybased-rm-ps.md)
## Konfigurowanie zasad IPsec/IKE dla połączeń
### [Azure PowerShell](vpn-gateway-ipsecikepolicy-rm-powershell.md)
## Konfigurowanie połączeń typu aktywne-aktywne o wysokiej dostępności
### [Azure PowerShell](vpn-gateway-activeactive-rm-powershell.md)
## Konfigurowanie protokołu BGP dla bramy sieci VPN
### [Azure PowerShell](vpn-gateway-bgp-resource-manager-ps.md)
### [Interfejs wiersza polecenia platformy Azure](bgp-how-to-cli.md)
## Konfigurowanie wymuszonego tunelowania
### [Azure PowerShell](vpn-gateway-forced-tunneling-rm.md)
### [Azure PowerShell (wersja klasyczna)](vpn-gateway-about-forced-tunneling.md)
## Modyfikowanie ustawień lokalnej bramy sieci
### [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-modify-local-network-gateway.md)
### [Interfejs wiersza polecenia platformy Azure](vpn-gateway-modify-local-network-gateway-cli.md)
## [Weryfikowanie połączenia z bramą VPN Gateway](vpn-gateway-verify-connection-resource-manager.md)
## [Resetowanie bramy VPN Gateway](vpn-gateway-resetgw-classic.md)
## Usuwanie bramy VPN Gateway
### [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
### [Azure PowerShell (wersja klasyczna)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
## [Konfigurowanie bramy sieci VPN (wdrożenie klasyczne)](vpn-gateway-configure-vpn-gateway-mp.md)
## [Jednostki SKU bramy (starsze)](vpn-gateway-about-skus-legacy.md)
## Konfigurowanie urządzeń sieci VPN innych firm
### [Overview & Azure configuration (Omówienie i konfiguracja platformy Azure)](vpn-gateway-3rdparty-device-config-overview.md)
### [Sample: Cisco ASA device (IKEv2/no BGP) (Przykład: Urządzenie ASA Cisco [IKEv2/bez protokołu BGP])](vpn-gateway-3rdparty-device-config-cisco-asa.md)
## [Migracja z modelu klasycznego do usługi Resource Manager](vpn-gateway-classic-resource-manager-migration.md)
## [Rozwiązywanie problemów](vpn-gateway-troubleshoot.md)
### [Sprawdzanie przepustowości sieci VPN do sieci wirtualnej](vpn-gateway-validate-throughput-to-vnet.md)
### [Ustawienia sieci VPN lub urządzenia zapory sugerowane przez społeczność](vpn-gateway-third-party-settings.md)
### [Problemy z połączeniem punkt-lokacja](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
#### [Problemy z połączeniem punkt-lokacja — klient VPN systemu Mac OS X](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
### [Problemy z połączeniem lokacja-lokacja](vpn-gateway-troubleshoot-site-to-site-cannot-connect.md)
#### [Połączenie lokacja-lokacja jest sporadycznie rozłączane](vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently.md)
### [Konfigurowanie i weryfikowanie połączeń sieci wirtualnej lub sieci VPN](https://support.microsoft.com/help/4032151/configuring-and-validating-vnet-or-vpn-connections)

# Dokumentacja
## [Azure PowerShell](/powershell/module/azurerm.network/?view=azurermps-4.0.0#vpn)
## [Azure PowerShell (wersja klasyczna)](/powershell/module/azure/?view=azuresmps-3.7.0#networking)
## [REST](/rest/api/network/virtualnetworkgateways)
## [REST (klasyczny)](https://msdn.microsoft.com/library/jj154113)
## [Interfejs wiersza polecenia platformy Azure](/cli/azure/network/vnet-gateway)

# Powiązane
## [Virtual Network](/azure/virtual-network/)
## [Application Gateway](/azure/application-gateway/)
## [System DNS platformy Azure](/azure/dns/)
## [Traffic Manager](/azure/traffic-manager/)
## [Load Balancer](/azure/load-balancer/)
## [ExpressRoute](/azure/expressroute/)

# Zasoby
## [Harmonogram działania dla platformy Azure](https://azure.microsoft.com/roadmap/?category=networking)
## [Blog](https://azure.microsoft.com/blog/topics/networking)
## [Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [Cennik](https://azure.microsoft.com/pricing/details/vpn-gateway)
## [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/)
## [Umowa SLA](https://azure.microsoft.com/support/legal/sla)
## [Filmy wideo](https://azure.microsoft.com/documentation/videos/index/?services=vpn-gateway)
