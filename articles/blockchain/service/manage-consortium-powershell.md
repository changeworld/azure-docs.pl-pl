---
title: Łańcuch bloków konsorcjum Zarządzanie usługi Azure Service przy użyciu programu PowerShell
description: Jak zarządzać członkami konsorcjum Azure Blockchain Service przy użyciu programu PowerShell
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: bef0c5d776e8ab6424b8604a49782088c45b0538
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028233"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>Zarządzanie członkami konsorcjum w usłudze Azure Service łańcucha bloków przy użyciu programu PowerShell

Za pomocą programu PowerShell do zarządzania członkami konsorcjum łańcucha bloków dla usługi Azure Service łańcucha bloków. Elementy członkowskie z uprawnieniami administratora może zaprosić, dodać, usunąć i zmienia role dla wszystkich uczestników konsorcjum łańcucha bloków. Członków z uprawnieniami użytkownika mogą wyświetlać wszyscy uczestnicy konsorcjum łańcucha bloków i zmieniać ich nazwy wyświetlanej członka.

## <a name="prerequisites"></a>Wymagania wstępne

* [Utwórz element członkowski łańcucha bloków w witrynie Azure portal](create-member.md)
* Aby uzyskać więcej informacji na temat konsorcjów, członków i węzłów, zobacz [konsorcjum Azure Blockchain Service](consortium.md)

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Możesz również uruchomić usługę Cloud Shell w oddzielnej karcie przeglądarki, przechodząc do strony [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

## <a name="install-powershell-module"></a>Zainstaluj moduł programu PowerShell

Zainstaluj pakiet Microsoft.AzureBlockchainService.ConsortiumManagement.PS z galerii programu PowerShell.

```powershell
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="establish-a-web3-connection"></a>Nawiąż połączenie sieci Web 3

Do zarządzania członkami konsorcjum, należy ustanowić połączenie sieci Web 3 do punktu końcowego usługi Azure Service łańcucha bloków elementu członkowskiego. Ten skrypt służy do ustawiania zmiennych globalnych, które mogą być używane podczas wywoływania polecenia cmdlet zarządzania konsorcjum.

```powershell
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Zastąp \<hasło do konta członkowskiego\> przy użyciu hasła konta członkowskiego używane podczas tworzenia elementu członkowskiego.

Znajdź inne wartości w witrynie Azure portal:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do usługi Azure Service łańcucha bloków domyślny element członkowski **Przegląd** strony.

    ![Omówienie elementu członkowskiego](./media/manage-consortium-powershell/member-overview.png)

    Zastąp \<konto członka\>, i \<adres RootContract\> wartościami z portalu.

1. Adres punktu końcowego można wybrać **węzłów transakcji** i wybierz węzeł transakcji.
1. Wybierz **parametry połączenia**.

    ![Parametry połączeń](./media/manage-consortium-powershell/connection-strings.png)

    Zastąp \<adres punktu końcowego\> wartością z **HTTPS (klucz dostępu 1)** lub **HTTPS (klucz dostępu 2)**.

## <a name="network-and-smart-contract-management"></a>Sieci i zarządzanie umowami inteligentne

Do nawiązania połączenia z łańcucha bloków punktu końcowego kontraktów inteligentnych odpowiedzialnego za zarządzanie konsorcjum, należy użyć sieci i poleceń cmdlet inteligentne kontraktu.

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

Nawiązanie połączenia konsorcjum zarządzania kontraktów inteligentnych, które są używane do zarządzania i wymuszania członków w ramach konsorcjum.

```powershell
Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>
```

| Parametr | Opis | Wymagane |
|-----------|-------------|:--------:|
| RootContractAddress | Główny adres kontraktu kontraktów inteligentnych zarządzania konsorcjum | Yes |
| Web3Client | Obiekt Web3Client uzyskany z New Web3Connection | Yes |

**Przykład**

```powershell
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

Użyj tego polecenia cmdlet, aby utworzyć obiekt, do przechowywania konta zarządzania zdalnego węzła informacji.

```powershell
Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>
```

| Parametr | Opis | Wymagane |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Adres konta członkowskiego łańcucha bloków | Yes |
| ManagedAccountPassword | Hasło do konta adresu | Yes |

**Przykład**

```powershell
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>New-Web3Connection

Nawiąże połączenie z punktem końcowym węzła transakcji.

```powershell
New-Web3Connection [-RemoteRPCEndpoint <String>]
```

| Parametr | Opis | Wymagane |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Adres punktu końcowego elementu członkowskiego łańcucha bloków | Yes |


**Przykład**

```powershell
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="consortium-member-management"></a>Zarządzanie elementu członkowskiego konsorcjum

Element członkowski konsorcjum poleceń cmdlet do zarządzania umożliwia zarządzanie członków w ramach konsorcjum. Dostępne akcje zależą od Twojej roli konsorcjum.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Pobiera szczegółowe informacje o użytkowniku lub Wyświetl listę członków konsorcjum.

```powershell
Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>
```

| Parametr | Opis | Wymagane |
|-----------|-------------|:--------:|
| Name (Nazwa) | Nazwa elementu członkowskiego Azure Blockchain Service, aby pobrać szczegółowe informacje na. Jeśli podasz nazwę elementu członkowskiego, zwracane są szczegóły elementu członkowskiego. Jeśli nazwa zostanie pominięty, zwracany jest listę członków konsorcjum. | Nie |
| Elementy członkowskie | Elementy członkowskie obiektu uzyskany z ConsortiumManagementContracts importu | Yes |
| Web3Client | Obiekt Web3Client uzyskany z New Web3Connection | Yes |

**Przykład**

```powershell
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

**Przykładowe dane wyjściowe**

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

Usuwa element członkowski łańcucha bloków.

```powershell
Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Parametr | Opis | Wymagane |
|-----------|-------------|:--------:|
| Name (Nazwa) | Nazwa elementu członkowskiego do usunięcia | Yes |
| Elementy członkowskie | Elementy członkowskie obiektu uzyskany z ConsortiumManagementContracts importu | Yes |
| Web3Account | Obiekt Web3Account uzyskany z Web3Account importu | Yes |
| Web3Client | Obiekt Web3Client uzyskany z New Web3Connection | Yes |

**Przykład**

```powershell
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Ustawia łańcucha bloków atrybutów elementu członkowskiego, takich jak wyświetlanie nazwy i konsorcjum roli.

Konsorcjum Administratorzy mogą ustawić **DisplayName** i **roli** wszystkim jej członkom. Konsorcjum członka roli użytkownika można zmienić tylko nazwę wyświetlaną własne elementu członkowskiego.

```powershell
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Parametr | Opis | Wymagane |
|-----------|-------------|:--------:|
| Name (Nazwa) | Nazwa elementu członkowskiego łańcucha bloków | Yes |
| Nazwa wyświetlana | Nowa nazwa wyświetlana | Nie |
| AccountAddress | Adres konta | Nie |
| Elementy członkowskie | Elementy członkowskie obiektu uzyskany z ConsortiumManagementContracts importu | Yes |
| Web3Account | Obiekt Web3Account uzyskany z Web3Account importu | Yes |
| Web3Client |  Obiekt Web3Client uzyskany z New Web3Connection| Yes |

**Przykład**

```powershell
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="consortium-member-invitation-management"></a>Zarządzanie zaproszenie elementu członkowskiego konsorcjum

Zaproszenie konsorcjum elementu członkowskiego poleceń cmdlet do zarządzania umożliwia zarządzanie zaproszeniami elementu członkowskiego konsorcjum. Dostępne akcje zależą od Twojej roli konsorcjum.

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

Zapraszanie nowych członków do konsorcjum.

```powershell
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parametr | Opis | Wymagane |
|-----------|-------------|:--------:|
| SubscriptionId | Identyfikator subskrypcji platformy Azure zaproszonego członka | Yes |
| Rola | Rola konsorcjum. Wartości mogą być administratora lub użytkownika. Administrator jest rola administratora konsorcjum. UŻYTKOWNIK jest konsorcjum roli członka. | Yes |
| Elementy członkowskie | Elementy członkowskie obiektu uzyskany z ConsortiumManagementContracts importu | Yes |
| Web3Account | Obiekt Web3Account uzyskany z Web3Account importu | Yes |
| Web3Client | Obiekt Web3Client uzyskany z New Web3Connection | Yes |

**Przykład**

```powershell
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Pobiera lub wyświetla ich listę stan zaproszenia elementu członkowskiego konsorcjum.

```powershell
Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>
```

| Parametr | Opis | Wymagane |
|-----------|-------------|:--------:|
| SubscriptionId | Identyfikator subskrypcji platformy Azure zaproszonego członka. Jeśli nie podano identyfikatora subskrypcji, zwracane są szczegóły zaproszenia identyfikatora subskrypcji. Jeśli SubscriptionID zostanie pominięty, zwracane są listy wszystkich zaproszeń elementu członkowskiego. | Nie |
| Elementy członkowskie | Elementy członkowskie obiektu uzyskany z ConsortiumManagementContracts importu | Yes |
| Web3Client | Obiekt Web3Client uzyskany z New Web3Connection | Yes |

**Przykład**

```powershell
$ContractConnection | Get-BlockchainMemberInvitation – SubscriptionId <Azure subscription ID>
```

**Przykładowe dane wyjściowe**

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

Odwołuje zaproszenia elementu członkowskiego konsorcjum.

```powershell
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| Parametr | Opis | Wymagane |
|-----------|-------------|:--------:|
| SubscriptionId | Identyfikator subskrypcji platformy Azure, elementu członkowskiego, aby można było odwołać | Yes |
| Elementy członkowskie | Elementy członkowskie obiektu uzyskany z ConsortiumManagementContracts importu | Yes |
| Web3Account | Obiekt Web3Account uzyskany z Web3Account importu | Yes |
| Web3Client | Obiekt Web3Client uzyskany z New Web3Connection | Yes |

**Przykład**

```powershell
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Zestawy **roli** dla istniejących zaproszenia. Tylko administratorzy konsorcjum można zmienić zaproszeń.

```powershell
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parametr | Opis | Wymagane |
|-----------|-------------|:--------:|
| SubscriptionId | Identyfikator subskrypcji platformy Azure zaproszonego członka | Yes |
| Rola | Nowa rola konsorcjum na zaproszenie. Możliwe wartości to **użytkownika** lub **administratora** | Yes |
| Elementy członkowskie |  Elementy członkowskie obiektu uzyskany z ConsortiumManagementContracts importu | Yes |
| Web3Account | Obiekt Web3Account uzyskany z Web3Account importu | Yes |
| Web3Client | Obiekt Web3Client uzyskany z New Web3Connection | Yes |

**Przykład**

```powershell
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat konsorcjów, członków i węzłów zobacz:

> [!div class="nextstepaction"]
> [Usługa Azure konsorcjum usługi łańcucha bloków](consortium.md)