---
title: 'Usługi Azure Active Directory Domain Services: Konfiguracja Rozwiązywanie problemów z sieciowej grupy zabezpieczeń | Dokumentacja firmy Microsoft'
description: Rozwiązywania problemów z konfiguracją NSG dla usług domenowych Azure AD
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: ergreenl
ms.openlocfilehash: 807dd2bdcc1e2ad18b1a93c3337c8244e3f1366b
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36218983"
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>Rozwiązywanie problemów z nieprawidłową konfigurację sieci na potrzeby domeny zarządzanej
Ten artykuł pomaga w rozwiązywaniu problemów oraz usuwaniu błędów konfiguracji związanych z siecią, które powodują powstanie następujący komunikat alertu:

## <a name="alert-aadds104-network-error"></a>AADDS104 alertu: Błąd sieciowy
**Komunikat alertu:** *Microsoft nie będzie mógł łączność z kontrolerami domeny dla tej domeny zarządzanej. Może się tak zdarzyć, jeśli grupa zabezpieczeń sieci (NSG) skonfigurowany na Twojej sieci wirtualnej blokuje dostęp do domeny zarządzanej. Inny możliwych przyczyn jest to, czy istnieje trasa zdefiniowana przez użytkownika blokuje ruch przychodzący z Internetu.*

Nieprawidłowa grupa NSG konfiguracje są Najczęstszą przyczyną błędów sieciowych dla usług domenowych Azure AD. Grupy zabezpieczeń sieci (NSG) skonfigurowany na potrzeby sieci wirtualnej muszą zezwalać na dostęp do [określone porty](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services). Jeśli te porty są zablokowane, Microsoft nie można monitorować ani zaktualizować domeny zarządzanej. Ponadto synchronizacja katalogu usługi Azure AD i domeny zarządzanej jest w pełni funkcjonalne. Podczas tworzenia Twojej grupy NSG, należy zachować te porty otwarty, aby uniknąć przerwy w działaniu usługi.

### <a name="checking-your-nsg-for-compliance"></a>Sprawdzanie Twojej NSG zgodności

1. Przejdź do [sieciowej grupy zabezpieczeń](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) strony w portalu Azure
2. W tabeli wybierz NSG skojarzoną z podsiecią, w którym włączono domeny zarządzanej.
3. W obszarze **ustawienia** w lewym panelu kliknij **reguły zabezpieczeń dla ruchu przychodzącego**
4. Zapoznaj się z zasadami w miejscu i identyfikacji reguły są blokuje dostęp do [te porty](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services).
5. Edytuj NSG w celu zapewnienia zgodności usuwanie reguły, dodanie reguły lub całkowicie tworzenia nowej grupy NSG. Kroki umożliwiające [dodać regułę](#add-a-rule-to-a-network-security-group-using-the-azure-portal) lub [utworzyć nowe, zgodne grupy NSG](#create-a-nsg-for-azure-ad-domain-services-using-powershell) są poniżej.

## <a name="sample-nsg"></a>przykład grupy NSG
Poniższa tabela przedstawia przykład grupy NSG, który będzie chronić domeny zarządzanej zezwalając Microsoft w celu monitorowania, zarządzania i aktualizowania informacji.

![przykład grupy NSG](.\media\active-directory-domain-services-alerts\default-nsg.png)

>[!NOTE]
> Usługi domenowe Azure AD wymaga nieograniczony dostęp ruchu wychodzącego z sieci wirtualnej. Nie zaleca się tworzenia wszystkie dodatkowe reguły NSG, która ogranicza wychodzący dostęp do sieci wirtualnej.

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Dodaj regułę do grupy zabezpieczeń sieci przy użyciu portalu Azure
Jeśli nie chcesz użyć programu PowerShell, można ręcznie dodać jednej reguły do grupy NSG przy użyciu portalu Azure. Aby utworzyć reguły w swojej grupy zabezpieczeń sieci, wykonaj następujące kroki:

1. Przejdź do [sieciowej grupy zabezpieczeń](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) strony w portalu Azure
2. W tabeli wybierz NSG skojarzoną z podsiecią, w którym włączono domeny zarządzanej.
3. W obszarze **ustawienia** w lewym panelu kliknij **reguły zabezpieczeń dla ruchu przychodzącego** lub **reguł zabezpieczeń dla ruchu wychodzącego**.
4. Utworzyć regułę, klikając **Dodaj** i wypełnianie informacji. Kliknij przycisk **OK**.
5. Sprawdź, czy reguła została utworzona przez lokalizowanie jej w tabeli reguł.


## <a name="create-a-nsg-for-azure-ad-domain-services-using-powershell"></a>Tworzenie grupy NSG dla usług domenowych Azure AD przy użyciu programu PowerShell
Ta grupa NSG jest skonfigurowany do zezwalać na ruch przychodzący do portów wymaganych przez usługi domenowe Azure AD, odrzuca wszystkie inne niechcianego przychodzącego.

**Wymaganie Wstępne: Instalowanie i konfigurowanie programu Azure PowerShell** postępuj zgodnie z instrukcjami, aby [zainstalować moduł Azure PowerShell i nawiązać połączenia z subskrypcją platformy Azure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

>[!TIP]
> Zalecamy używanie najnowszej wersji modułu Azure PowerShell. Jeśli masz już starszą wersję zainstalowany moduł Azure PowerShell, zaktualizuj do najnowszej wersji.
>

Wykonaj następujące kroki, aby utworzyć nowa grupa NSG przy użyciu programu PowerShell.
1. Zaloguj się do subskrypcji platformy Azure.

  ```PowerShell
  # Log in to your Azure subscription.
  Connect-AzureRmAccount
  ```

2. Utwórz grupy NSG z trzech reguł. Poniższy skrypt definiuje trzy reguły NSG, który umożliwia dostępu do portów wymaganych do uruchomienia usług domenowych Azure AD. Następnie skrypt tworzy Nowa grupa NSG, który zawiera te reguły. Aby dodać dodatkowe reguły zezwalające na ruch przychodzący, jeśli jest to wymagane przez obciążeń wdrożonych w sieci wirtualnej, użyj tego samego formatu.

  ```PowerShell
  # Allow inbound HTTPS traffic to enable synchronization to your managed domain.
  $SyncRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowSyncWithAzureAD -Description "Allow synchronization with Azure AD" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 443

  # Allow management of your domain over port 5986 (PowerShell Remoting)
  $PSRemotingRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowPSRemoting -Description "Allow management of domain through port 5986" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
  -SourceAddressPrefix 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161 -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 5986

  #The following two rules are optional and needed only in certain situations.

  # Allow management of your domain over port 3389 (remote desktop).
  $RemoteDesktopRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowRD -Description "Allow management of domain through port 3389" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
  -SourceAddressPrefix 207.68.190.32/27, 13.106.78.32/27, 10.254.32.0/20, 10.97.136.0/22, 13.106.174.32/27, 13.106.4.96/27 -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389

  # Secure LDAP rule, it is recommended to change the source address prefix to include only the IP addresses
  $SecureLDAPRule = New-AzureRmNetworkSecurityRuleConfig -Name SecureLDAP -Description "Allow access through secure LDAP port" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 104 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 636

  # Create the NSG with the rules above (if you need the remote desktop rule and secure ldap rule, add it below)
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
  -Name "AADDomainServices-NSG" -SecurityRules $SyncRule, $PSRemotingRule
  ```

3. Ponadto skojarzyć grupy NSG z sieci wirtualnej i podsieci wyboru.

  ```PowerShell
  # Find vnet and subnet
  $Vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroup -Name $VnetName
  $Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $Vnet -Name $SubnetName

  # Set the nsg to the subnet and save the changes
  $Subnet.NetworkSecurityGroup = $Nsg
  Set-AzureRmVirtualNetwork -VirtualNetwork $Vnet
  ```

## <a name="full-script-to-create-and-apply-an-nsg-for-azure-ad-domain-services"></a>Pełne skryptu, aby utworzyć i zastosować grupy NSG dla usług domenowych Azure AD
>[!TIP]
> Zalecamy używanie najnowszej wersji modułu Azure PowerShell. Jeśli masz już starszą wersję zainstalowany moduł Azure PowerShell, zaktualizuj do najnowszej wersji.
>

```PowerShell
# Change the following values to match your deployment
$ResourceGroup = "ResourceGroupName"
$Location = "westus"
$VnetName = "exampleVnet"
$SubnetName = "exampleSubnet"

# Log in to your Azure subscription.
Connect-AzureRmAccount

# Allow inbound HTTPS traffic to enable synchronization to your managed domain.
$SyncRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowSyncWithAzureAD -Description "Allow synchronization with Azure AD" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 443

# Allow management of your domain over port 5986 (PowerShell Remoting)
$PSRemotingRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowPSRemoting -Description "Allow management of domain through port 5986" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
-SourceAddressPrefix 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161 -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 5986

# The following two rules are optional and needed only in certain situations.

# Allow management of your domain over port 3389 (remote desktop).
$RemoteDesktopRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowRD -Description "Allow management of domain through port 3389" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
-SourceAddressPrefix 207.68.190.32/27, 13.106.78.32/27, 10.254.32.0/20, 10.97.136.0/22, 13.106.174.32/27, 13.106.4.96/27 -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389

# Secure LDAP rule, it is recommended to change the source address prefix to include only the IP addresses
$SecureLDAPRule = New-AzureRmNetworkSecurityRuleConfig -Name SecureLDAP -Description "Allow access through secure LDAP port" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 104 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 636

# Create the NSG with the rules above (if you need the remote desktop rule and secure ldap rule, add it below)
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
-Name "AADDomainServices-NSG" -SecurityRules $SyncRule, $PSRemotingRule

# Find vnet and subnet
$Vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroup -Name $VnetName
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $Vnet -Name $SubnetName

# Set the nsg to the subnet and save the changes
$Subnet.NetworkSecurityGroup = $Nsg
Set-AzureRmVirtualNetwork -VirtualNetwork $Vnet
```


## <a name="need-help"></a>Potrzebujesz pomocy?
Skontaktuj się z zespołem produktu usługi Azure Active Directory Domain Services, aby [udostępnić opinię lub pomocy technicznej](active-directory-ds-contact-us.md).
