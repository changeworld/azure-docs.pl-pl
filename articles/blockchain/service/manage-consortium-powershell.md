---
title: Zarządzanie członkami w konsorcjum Azure Blockchain Service przy użyciu programu Azure PowerShell
description: Dowiedz się, jak zarządzać członkami konsorcjum Azure Blockchain Service przy użyciu programu Azure PowerShell.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/10/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 4bb72bc3fe8b85a8d2aed88e02f5f3150abb6899
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66493669"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-by-using-powershell"></a>Zarządzanie członkami konsorcjum w usłudze Azure Service łańcucha bloków przy użyciu programu PowerShell

Za pomocą programu PowerShell do zarządzania członkami konsorcjum łańcucha bloków dla usługi Azure Service łańcucha bloków. Użytkownicy, którzy mają uprawnienia administratora może zaprosić, dodawania, usuwania i zmienić role dla wszystkich uczestników konsorcjum łańcucha bloków. Członkowie, którzy mają uprawnienia użytkowników można wyświetlić wszystkich uczestników konsorcjum łańcucha bloków i zmień jego nazwę wyświetlaną elementu członkowskiego.

## <a name="prerequisites"></a>Wymagania wstępne

* Tworzenie członka łańcucha bloków przy użyciu [witryny Azure portal](create-member.md).
* Aby uzyskać więcej informacji na temat konsorcjów, członków i węzłów, zobacz [konsorcjum Azure Blockchain Service](consortium.md).

## <a name="open-azure-cloud-shell"></a>Otwieranie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, który służy do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie.

Możesz również otworzyć usługę Cloud Shell w osobnej karcie przeglądarki, przechodząc do [shell.azure.com/powershell](https://shell.azure.com/powershell). Wybierz **kopiowania** do skopiowania bloki kodu, wklej go w usłudze Cloud Shell, a następnie wybierz **Enter** do jej uruchomienia.

## <a name="install-the-powershell-module"></a>Instalowanie modułu programu PowerShell

Zainstaluj pakiet Microsoft.AzureBlockchainService.ConsortiumManagement.PS z galerii programu PowerShell.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>Ustawianie preferencji informacji

Więcej informacji można uzyskać, podczas wykonywania polecenia cmdlet, ustawiając zmienną preferencji informacji. Domyślnie *$InformationPreference* ustawiono *SilentlyContinue*.

Aby uzyskać bardziej szczegółowy informacji z poleceń cmdlet należy ustawić preferencje w programie PowerShell w następujący sposób:

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Nawiąż połączenie sieci Web 3

Aby zarządzać członkami konsorcjum, nawiąż połączenie sieci Web 3 do punktu końcowego usługi łańcucha bloków elementu członkowskiego. Ten skrypt służy do ustawiania globalnych zmiennych wywoływania poleceń cmdlet do zarządzania konsorcjum.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Zastąp *\<hasło do konta członkowskiego\>* przy użyciu hasła konta elementu członkowskiego, użytego podczas tworzenia elementu członkowskiego.

Znajdź inne wartości w witrynie Azure portal:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do domyślny element członkowski usługi łańcucha bloków **Przegląd** strony.

    ![Omówienie elementu członkowskiego](./media/manage-consortium-powershell/member-overview.png)

    Zastąp *\<konto członka\>* i *\<adres RootContract\>* wartościami z portalu.

1. Adres punktu końcowego można wybrać **węzłów transakcji**, a następnie wybierz pozycję **węzła transakcji domyślnego**. Węzeł domyślna ma taką samą nazwę jak element członkowski łańcucha bloków.
1. Wybierz **parametry połączenia**.

    ![Parametry połączeń](./media/manage-consortium-powershell/connection-strings.png)

    Zastąp *\<adres punktu końcowego\>* wartością z **HTTPS (klucz dostępu 1)** lub **HTTPS (klucz dostępu 2)** .

## <a name="manage-the-network-and-smart-contracts"></a>Zarządzanie siecią i kontraktów inteligentnych

Do nawiązania połączenia z punktem końcowym łańcucha bloków kontraktów inteligentnych odpowiedzialnego za zarządzanie konsorcjum, należy użyć sieci i poleceń cmdlet inteligentne kontraktu.

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

Za pomocą tego polecenia cmdlet, aby połączyć z zarządzania konsorcjum kontraktów inteligentnych. Umowy te są używane do zarządzania i wymuszania członków w ramach konsorcjum.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Parametr | Opis | Wymagane |
|-----------|-------------|:--------:|
| RootContractAddress | Główny adres kontraktu kontraktów inteligentnych zarządzania konsorcjum | Yes |
| Web3Client | Obiekt Web3Client uzyskany z New Web3Connection | Tak |

#### <a name="example"></a>Przykład

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

Użyj tego polecenia cmdlet, aby utworzyć obiekt, do przechowywania informacji dla konta zarządzania zdalnego węzła.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Parametr | Opis | Wymagane |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Adres konta członkowskiego łańcucha bloków | Tak |
| ManagedAccountPassword | Hasło do konta adresu | Tak |

#### <a name="example"></a>Przykład

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>New-Web3Connection

Użyj następującego polecenia cmdlet, aby nawiązać połączenie z punktem końcowym węzła transakcji.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Parametr | Opis | Wymagane |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Adres punktu końcowego elementu członkowskiego łańcucha bloków | Tak |

#### <a name="example"></a>Przykład

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>Zarządzanie członkami konsorcjum

Element członkowski konsorcjum poleceń cmdlet do zarządzania umożliwia zarządzanie członków w ramach konsorcjum. Dostępne akcje zależą od Twojej roli konsorcjum.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Użyj następującego polecenia cmdlet, aby uzyskać szczegółowe informacje o użytkowniku lub listy elementów członkowskich konsorcjum.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Parametr | Opis | Wymagane |
|-----------|-------------|:--------:|
| Name (Nazwa) | Nazwa elementu członkowskiego usługi łańcucha bloków, który chcesz pobrać szczegółowe informacje o. Po wprowadzeniu nazwy zwraca szczegółowe informacje o użytkowniku. Gdy nazwa zostanie pominięty, zwraca listę wszystkich członków konsorcjum. | Nie |
| Elementy członkowskie | Elementy członkowskie obiektu uzyskany z ConsortiumManagementContracts importu | Yes |
| Web3Client | Obiekt Web3Client uzyskany z New Web3Connection | Tak |

#### <a name="example"></a>Przykład

```powershell-interactive
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

#### <a name="example-output"></a>Przykładowe dane wyjściowe

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

Użyj następującego polecenia cmdlet, aby usunąć element członkowski łańcucha bloków.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Parametr | Opis | Wymagane |
|-----------|-------------|:--------:|
| Name (Nazwa) | Nazwa elementu członkowskiego do usunięcia | Tak |
| Elementy członkowskie | Elementy członkowskie obiektu uzyskany z ConsortiumManagementContracts importu | Tak |
| Web3Account | Obiekt Web3Account uzyskany z Web3Account importu | Tak |
| Web3Client | Obiekt Web3Client uzyskany z New Web3Connection | Tak |

#### <a name="example"></a>Przykład

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Użyj następującego polecenia cmdlet, aby ustawić atrybuty elementu członkowskiego, w tym nazwę wyświetlaną i rolę konsorcjum łańcucha bloków.

Konsorcjum Administratorzy mogą ustawić **DisplayName** i **roli** wszystkim jej członkom. Członek konsorcjum z rolą użytkownika można zmienić tylko nazwę wyświetlaną własne elementu członkowskiego.

`Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Parametr | Opis | Wymagane |
|-----------|-------------|:--------:|
| Name (Nazwa) | Nazwa elementu członkowskiego łańcucha bloków | Yes |
| Nazwa wyświetlana | Nowa nazwa wyświetlana | Nie |
| AccountAddress | Adres konta | Nie |
| Elementy członkowskie | Elementy członkowskie obiektu uzyskany z ConsortiumManagementContracts importu | Tak |
| Web3Account | Obiekt Web3Account uzyskany z Web3Account importu | Tak |
| Web3Client |  Obiekt Web3Client uzyskany z New Web3Connection| Tak |

#### <a name="example"></a>Przykład

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>Zarządzanie zaproszeniami członków konsorcjum

Umożliwia zarządzanie zaproszeniami członkowie konsorcjum konsorcjum elementu członkowskiego zaproszenie poleceń cmdlet do zarządzania. Dostępne akcje zależą od Twojej roli konsorcjum.

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

To polecenie cmdlet umożliwia zapraszać nowych użytkowników do konsorcjum.

`New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>`

| Parametr | Opis | Wymagane |
|-----------|-------------|:--------:|
| SubscriptionId | Identyfikator subskrypcji platformy Azure, elementu członkowskiego o zaproszenie | Tak |
| Rola | Rola konsorcjum. Wartości mogą być administratora lub użytkownika. Administrator jest rola administratora konsorcjum. UŻYTKOWNIK jest konsorcjum roli członka. | Tak |
| Elementy członkowskie | Elementy członkowskie obiektu uzyskany z ConsortiumManagementContracts importu | Tak |
| Web3Account | Obiekt Web3Account uzyskany z Web3Account importu | Tak |
| Web3Client | Obiekt Web3Client uzyskany z New Web3Connection | Tak |

#### <a name="example"></a>Przykład

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Użyj następującego polecenia cmdlet do pobierania lub listy elementu członkowskiego konsorcjum stan zaproszenia.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Parametr | Opis | Wymagane |
|-----------|-------------|:--------:|
| SubscriptionId | Identyfikator subskrypcji platformy Azure należy zaprosić. Jeśli subskrypcja identyfikator zostanie podana, funkcja zwraca subskrypcji identyfikatory Szczegóły zaproszenia. Jeśli identyfikator subskrypcji jest pominięty, zwraca listę wszystkich zaproszeń elementu członkowskiego. | Nie |
| Elementy członkowskie | Elementy członkowskie obiektu uzyskany z ConsortiumManagementContracts importu | Yes |
| Web3Client | Obiekt Web3Client uzyskany z New Web3Connection | Tak |

#### <a name="example"></a>Przykład

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation – SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>Przykładowe dane wyjściowe

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

Użyj następującego polecenia cmdlet, aby można było odwołać zaproszenie elementu członkowskiego konsorcjum.

`Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>`

| Parametr | Opis | Wymagane |
|-----------|-------------|:--------:|
| SubscriptionId | Identyfikator subskrypcji platformy Azure, elementu członkowskiego, aby można było odwołać | Yes |
| Elementy członkowskie | Elementy członkowskie obiektu uzyskany z ConsortiumManagementContracts importu | Yes |
| Web3Account | Obiekt Web3Account uzyskany z Web3Account importu | Tak |
| Web3Client | Obiekt Web3Client uzyskany z New Web3Connection | Tak |

#### <a name="example"></a>Przykład

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Użyj tego polecenia cmdlet, aby ustawić **roli** dla istniejących zaproszenia. Tylko administratorzy konsorcjum można zmienić zaproszeń.

`Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>`

| Parametr | Opis | Wymagane |
|-----------|-------------|:--------:|
| SubscriptionId | Identyfikator subskrypcji platformy Azure, elementu członkowskiego o zaproszenie | Yes |
| Rola | Nowa rola konsorcjum na zaproszenie. Możliwe wartości to **użytkownika** lub **administratora**. | Tak |
| Elementy członkowskie |  Elementy członkowskie obiektu uzyskany z ConsortiumManagementContracts importu | Tak |
| Web3Account | Obiekt Web3Account uzyskany z Web3Account importu | Tak |
| Web3Client | Obiekt Web3Client uzyskany z New Web3Connection | Tak |

#### <a name="example"></a>Przykład

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat konsorcjów, członków i węzłów zobacz:

> [!div class="nextstepaction"]
> [Usługa Azure konsorcjum usługi łańcucha bloków](consortium.md)
