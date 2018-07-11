---
title: 'Usługi Azure Active Directory Domain Services: Konfiguracja Rozwiązywanie problemów z sieciową grupą zabezpieczeń | Dokumentacja firmy Microsoft'
description: Rozwiązywanie problemów z konfiguracją sieciowej grupy zabezpieczeń dla usług domenowych Azure AD
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
ms.openlocfilehash: 67f4f0850d0600fc7ca0f1323e7c7801187089f5
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37950738"
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>Rozwiązywanie problemów z nieprawidłowej konfiguracji sieci dla domeny zarządzanej
Ten artykuł pomoże Ci rozwiązać błędy konfiguracji odnoszące się do sieci, których wynikiem następujący komunikat alertu:

## <a name="alert-aadds104-network-error"></a>AADDS104 alertu: Błąd sieci
**Komunikat alertu:** *firmy Microsoft nie może nawiązać połączenia z kontrolerami domeny dla tej domeny zarządzanej. Może to nastąpić, jeśli sieciowa grupa zabezpieczeń (NSG) skonfigurowane w Twojej sieci wirtualnej blokuje dostęp do domeny zarządzanej. Inny możliwych przyczyn jest to, jeśli istnieje trasa zdefiniowana przez użytkownika, która blokuje ruch przychodzący z Internetu.*

Nieprawidłowe konfiguracje sieciowej grupy zabezpieczeń są najbardziej typowe przyczyny błędów sieci dla usługi Azure AD Domain Services. Grupy zabezpieczeń sieci (NSG) skonfigurowany na potrzeby sieci wirtualnej muszą zezwalać na dostęp do [określonych portów](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services). Jeśli te porty są zablokowane, firma Microsoft nie może monitorować lub zaktualizować Twojej domeny zarządzanej. Ponadto ma wpływ na synchronizacji katalogu usługi Azure AD Twojej domeny zarządzanej. Podczas tworzenia w sieciowej grupie zabezpieczeń, nie zamykaj tych portów w celu uniknięcia przerwy w działaniu usługi.

### <a name="checking-your-nsg-for-compliance"></a>Sprawdzanie sieciowa grupa zabezpieczeń pod kątem zgodności

1. Przejdź do [sieciowe grupy zabezpieczeń](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) strony w witrynie Azure portal
2. Z tabeli wybierz sieciową grupę zabezpieczeń skojarzoną z podsiecią, w którym Twoja domena zarządzana jest włączona.
3. W obszarze **ustawienia** w okienku po lewej stronie kliknij **reguły zabezpieczeń dla ruchu przychodzącego**
4. Zapoznaj się z zasadami w miejscu i zidentyfikować, które zasady blokują dostęp do [tych portów](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services)
5. Edytuj sieciowej grupy zabezpieczeń w celu zapewnienia zgodności przez usunięcie reguły, dodanie reguły albo całkowicie tworzenia nowej sieciowej grupy zabezpieczeń. Kroki umożliwiające [Dodaj regułę](#add-a-rule-to-a-network-security-group-using-the-azure-portal) lub [Utwórz nowe, zgodne sieciowa grupa zabezpieczeń](#create-a-nsg-for-azure-ad-domain-services-using-powershell) są poniżej

## <a name="sample-nsg"></a>Przykładowe sieciowej grupy zabezpieczeń
Poniższa tabela przedstawia przykładowe sieciowej grupy zabezpieczeń, który będzie chronić Twojej domeny zarządzanej zezwalając firmy Microsoft, aby monitorować, zarządzania i aktualizowania informacji.

![Przykładowe sieciowej grupy zabezpieczeń](.\media\active-directory-domain-services-alerts\default-nsg.png)

>[!NOTE]
> Azure AD Domain Services wymaga nieograniczony dostęp ruchu wychodzącego z sieci wirtualnej. Nie zaleca się tworzenie wszelkie dodatkowe reguły sieciowej grupy zabezpieczeń, która ogranicza dostęp ruchu wychodzącego dla sieci wirtualnej.

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Dodawanie reguły do sieciowej grupy zabezpieczeń w witrynie Azure portal
Jeśli nie chcesz przy użyciu programu PowerShell, można ręcznie dodawać jednej reguły do sieciowych grup zabezpieczeń przy użyciu witryny Azure portal. Tworzenie reguł w sieciowej grupy zabezpieczeń, wykonaj następujące czynności:

1. Przejdź do [sieciowe grupy zabezpieczeń](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) strony w witrynie Azure portal.
2. Z tabeli wybierz sieciową grupę zabezpieczeń skojarzoną z podsiecią, w którym Twoja domena zarządzana jest włączona.
3. W obszarze **ustawienia** w panelu po lewej stronie kliknij opcję **reguły zabezpieczeń dla ruchu przychodzącego** lub **reguły zabezpieczeń dla ruchu wychodzącego**.
4. Utworzyć regułę, klikając **Dodaj** i wypełniając informacje. Kliknij przycisk **OK**.
5. Sprawdź, czy reguła została utworzona, umieszczając go w tabeli reguł.


## <a name="create-a-nsg-for-azure-ad-domain-services-using-powershell"></a>Tworzenie sieciowej grupy zabezpieczeń dla usług domenowych Azure AD przy użyciu programu PowerShell
Ta sieciowa grupa zabezpieczeń jest skonfigurowana do Zezwalaj na ruch przychodzący do portów wymaganych przez usługi domenowe Azure AD, odrzuca wszelkie inne niechciane dla ruchu przychodzącego.

**Wymaganie Wstępne: Instalowanie i konfigurowanie programu Azure PowerShell** postępuj zgodnie z instrukcjami, aby [Instalowanie modułu Azure PowerShell i nawiązać połączenie z subskrypcją platformy Azure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

>[!TIP]
> Zalecamy używanie najnowszej wersji modułu Azure PowerShell. Jeśli masz już starszą wersję modułu Azure PowerShell, zainstalowane, zaktualizuj do najnowszej wersji.
>

Wykonaj następujące kroki, aby utworzyć nowej sieciowej grupy zabezpieczeń przy użyciu programu PowerShell.
1. Zaloguj się do subskrypcji platformy Azure.

  ```PowerShell
  # Log in to your Azure subscription.
  Connect-AzureRmAccount
  ```

2. Tworzenie sieciowej grupy zabezpieczeń przy użyciu trzech reguł. Poniższy skrypt definiuje trzy reguły sieciowej grupy zabezpieczeń, który umożliwia dostępu do portów wymaganych do uruchomienia usług domenowych Azure AD. Następnie skrypt tworzy nowej sieciowej grupy zabezpieczeń, która zawiera te reguły. Aby dodać dodatkowe reguły zezwalające na inne rodzaje ruchu przychodzącego, jeśli jest to wymagane przez obciążenia wdrożone w sieci wirtualnej, należy użyć tego samego formatu.

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

3. Na koniec skojarzyć sieciową grupę zabezpieczeń z sieci wirtualnej i podsieci wybór.

  ```PowerShell
  # Find vnet and subnet
  $Vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroup -Name $VnetName
  $Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $Vnet -Name $SubnetName

  # Set the nsg to the subnet and save the changes
  $Subnet.NetworkSecurityGroup = $Nsg
  Set-AzureRmVirtualNetwork -VirtualNetwork $Vnet
  ```

## <a name="full-script-to-create-and-apply-an-nsg-for-azure-ad-domain-services"></a>Pełny skrypt, aby utworzyć i zastosować sieciową grupę zabezpieczeń dla usług domenowych Azure AD
>[!TIP]
> Zalecamy używanie najnowszej wersji modułu Azure PowerShell. Jeśli masz już starszą wersję modułu Azure PowerShell, zainstalowane, zaktualizuj do najnowszej wersji.
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
Skontaktuj się z zespołem produktu usługi Azure Active Directory Domain Services, aby [Podziel się opinią lub pomocy technicznej](active-directory-ds-contact-us.md).
