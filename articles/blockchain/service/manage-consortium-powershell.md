---
title: Zarządzanie członkami konsorcjum usługi Azure Blockchain Service — PowerShell
description: Dowiedz się, jak zarządzać członkami konsorcjum usługi Azure Blockchain Service przy użyciu programu Azure PowerShell.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: zeyadr
ms.openlocfilehash: e819dd39481b58d446384a5e2253c548ce0c267c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505983"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>Zarządzanie członkami konsorcjum w usłudze Azure Blockchain za pomocą programu PowerShell

Za pomocą programu PowerShell można zarządzać członkami konsorcjum łańcucha bloków dla usługi Azure Blockchain. Członkowie, którzy mają uprawnienia administratora, mogą zapraszać, dodawać, usuwać i zmieniać role dla wszystkich uczestników konsorcjum łańcucha bloków. Członkowie, którzy mają uprawnienia użytkownika, mogą przeglądać wszystkich uczestników konsorcjum łańcucha bloków i zmieniać nazwę wyświetlaną swojego członka.

## <a name="prerequisites"></a>Wymagania wstępne

* Utwórz członka łańcucha bloków przy użyciu [portalu Azure](create-member.md).
* Aby uzyskać więcej informacji na temat konsorcjów, członków i węzłów, zobacz [konsorcjum usługi Azure Blockchain Service.](consortium.md)

## <a name="open-azure-cloud-shell"></a>Otwieranie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna powłoka interaktywna, której można użyć do uruchomienia kroków w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie.

Możesz również otworzyć usługę Cloud Shell w osobnej karcie przeglądarki, przechodząc do [shell.azure.com/powershell](https://shell.azure.com/powershell). Wybierz **pozycję Kopiuj,** aby skopiować bloki kodu, wkleić go do powłoki chmury, a następnie wybierz pozycję **Enter,** aby go uruchomić.

## <a name="install-the-powershell-module"></a>Instalowanie modułu programu PowerShell

Zainstaluj pakiet Microsoft.AzureBlockchainService.ConsortiumManagement.PS z Galerii programu PowerShell.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>Ustawianie preferencji informacji

Więcej informacji można uzyskać podczas wykonywania poleceń cmdlet, ustawiając zmienną preferencji informacji. Domyślnie *$InformationPreference* jest ustawiona na *SilentlyContinue*.

Aby uzyskać więcej pełnych informacji z poleceń cmdlet, ustaw preferencje w programie PowerShell w następujący sposób:

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Ustanawianie połączenia z sieci Web3

Aby zarządzać członkami konsorcjum, nawiążej połączenie Web3 z punktem końcowym członka usługi Blockchain. Za pomocą tego skryptu można ustawić zmienne globalne do wywoływania poleceń cmdlet zarządzania konsorcjum.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Zastąp * \<hasło\> konta członkowskiego* hasłem konta członkowskiego użytym podczas tworzenia członka.

Znajdź inne wartości w witrynie Azure portal:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Przejdź do domyślnej strony **Przegląd** użytkownika usługi Blockchain.

    ![Omówienie elementu członkowskiego](./media/manage-consortium-powershell/member-overview.png)

    Zastąp * \<konto\> członkowskie* i * \<adres\> RootContract* wartościami z portalu.

1. W przypadku adresu punktu końcowego wybierz pozycję **Węzły transakcji**, a następnie wybierz **domyślny węzeł transakcji**. Domyślny węzeł ma taką samą nazwę jak element członkowski łańcucha bloków.
1. Wybierz **pozycję Parametry połączenia**.

    ![Parametry połączeń](./media/manage-consortium-powershell/connection-strings.png)

    Zamień * \<\> adres punktu końcowego* na wartość z **protokołu HTTPS (klucz dostępu 1)** lub **HTTPS (klucz dostępu 2)**.

## <a name="manage-the-network-and-smart-contracts"></a>Zarządzanie siecią i inteligentnymi kontraktami

Użyj poleceń cmdlet sieci i inteligentnych umów, aby nawiązać połączenie z inteligentnymi umowami punktu końcowego łańcucha bloków odpowiedzialnymi za zarządzanie konsorcjum.

### <a name="import-consortiummanagementcontracts"></a>Import-KonsorcjumWykonaństwo umowy

Użyj tego polecenia cmdlet, aby połączyć się z inteligentnymi umowami kierownictwa konsorcjum. Kontrakty te są używane do zarządzania i egzekwowania członków w ramach konsorcjum.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Parametr | Opis | Wymagany |
|-----------|-------------|:--------:|
| RootContractAddress (RootContractAddress) | Główny adres umowy inteligentnych umów dotyczących zarządzania konsorcjum | Tak |
| Web3Klient | Web3Client obiekt uzyskany z New-Web3Connection | Tak |

#### <a name="example"></a>Przykład

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Konto Importuj w sieci Web3

To polecenie cmdlet służy do tworzenia obiektu do przechowywania informacji dla konta zarządzania węzła zdalnego.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Parametr | Opis | Wymagany |
|-----------|-------------|:--------:|
| Adres zarządzanych kont | Adres konta użytkownika łańcucha bloków | Tak |
| Hasło zarządzanego konta | Hasło adresu konta | Tak |

#### <a name="example"></a>Przykład

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>Nowe połączenie web3

To polecenie cmdlet służy do ustanawiania połączenia z punktem końcowym RPC węzła transakcji.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Parametr | Opis | Wymagany |
|-----------|-------------|:--------:|
| Punkt zdalnego dodatku REMOTEREND | Adres punktu końcowego członka łańcucha bloków | Tak |

#### <a name="example"></a>Przykład

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>Zarządzanie członkami konsorcjum

Polecenia cmdlet zarządzania członkami konsorcjum umożliwia zarządzanie członkami w ramach konsorcjum. Dostępne działania zależą od roli konsorcjum.

### <a name="get-blockchainmember"></a>Get-BlockchainCzłonek

Użyj tego polecenia cmdlet, aby uzyskać szczegóły dotyczące elementu członkowskiego lub członków listy konsorcjum.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Parametr | Opis | Wymagany |
|-----------|-------------|:--------:|
| Nazwa | Nazwa członka usługi Blockchain, o której chcesz pobrać szczegółowe informacje. Po wprowadzeniu nazwy zwraca szczegóły elementu członkowskiego. Po pominięciu nazwy zwraca listę wszystkich członków konsorcjum. | Nie |
| Elementy członkowskie | Obiekt członków uzyskany z importu-konsorcjumPodejmowanie Umowy | Tak |
| Web3Klient | Web3Client obiekt uzyskany z New-Web3Connection | Tak |

#### <a name="example"></a>Przykład

[Nawiązuj połączenie z sieci Web3,](#establish-a-web3-connection) aby ustawić zmienną $ContractConnection.

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

### <a name="remove-blockchainmember"></a>Usuń-BlockchainCzłonek

Użyj tego polecenia cmdlet, aby usunąć członka łańcucha bloków.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Parametr | Opis | Wymagany |
|-----------|-------------|:--------:|
| Nazwa | Nazwa członka do usunięcia | Tak |
| Elementy członkowskie | Obiekt członków uzyskany z importu-konsorcjumPodejmowanie Umowy | Tak |
| Konto Web3 | Obiekt Web3Kontakon uzyskiwany z konta Import-Web3Account | Tak |
| Web3Klient | Web3Client obiekt uzyskany z New-Web3Connection | Tak |

#### <a name="example"></a>Przykład

[Nawiązuj połączenie z sieci Web3,](#establish-a-web3-connection) aby ustawić zmienne $ContractConnection i $MemberAccount.

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainCzłonek

Użyj tego polecenia cmdlet, aby ustawić atrybuty elementu członkowskiego łańcucha bloków, w tym nazwę wyświetlaną i rolę konsorcjum.

Administratorzy konsorcjum mogą ustawić **DisplayName** i **Role** dla wszystkich członków. Członek konsorcjum z rolą użytkownika może zmienić tylko nazwę wyświetlaną własnego członka.

```
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Parametr | Opis | Wymagany |
|-----------|-------------|:--------:|
| Nazwa | Nazwa członka łańcucha bloków | Tak |
| DisplayName | Nowa nazwa wyświetlana | Nie |
| Adres konta | Adres konta | Nie |
| Elementy członkowskie | Obiekt członków uzyskany z importu-konsorcjumPodejmowanie Umowy | Tak |
| Konto Web3 | Obiekt Web3Kontakon uzyskiwany z konta Import-Web3Account | Tak |
| Web3Klient |  Web3Client obiekt uzyskany z New-Web3Connection| Tak |

#### <a name="example"></a>Przykład

[Nawiązuj połączenie z sieci Web3,](#establish-a-web3-connection) aby ustawić zmienne $ContractConnection i $MemberAccount.

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>Zarządzanie zaproszeniami członków konsorcjum

Polecenia cmdlet zarządzania zaproszeniami członków konsorcjum umożliwia zarządzanie zaproszeniami członków konsorcjum. Dostępne działania zależą od roli konsorcjum.

### <a name="new-blockchainmemberinvitation"></a>Nowe-BlockchainMemberInvitation

Użyj tego polecenia cmdlet, aby zaprosić nowych członków do konsorcjum.

```
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parametr | Opis | Wymagany |
|-----------|-------------|:--------:|
| SubscriptionId | Identyfikator subskrypcji platformy Azure członka do zaproszenia | Tak |
| Rola | Rola konsorcjum. Wartości mogą być ADMIN lub USER. ADMIN jest rolą administratora konsorcjum. UŻYTKOWNIK jest rolą członka konsorcjum. | Tak |
| Elementy członkowskie | Obiekt członków uzyskany z importu-konsorcjumPodejmowanie Umowy | Tak |
| Konto Web3 | Obiekt Web3Kontakon uzyskiwany z konta Import-Web3Account | Tak |
| Web3Klient | Web3Client obiekt uzyskany z New-Web3Connection | Tak |

#### <a name="example"></a>Przykład

[Nawiązuj połączenie z sieci Web3,](#establish-a-web3-connection) aby ustawić zmienne $ContractConnection i $MemberAccount.

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Inicjatywa Get-BlockchainMemberInvitation

Użyj tego polecenia cmdlet, aby pobrać lub wyświetlić listę stanu zaproszenia członka konsorcjum.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Parametr | Opis | Wymagany |
|-----------|-------------|:--------:|
| SubscriptionId | Identyfikator subskrypcji platformy Azure członka do zaproszenia. Jeśli podano identyfikator subskrypcji, zwraca szczegóły zaproszenia identyfikatora subskrypcji. Jeśli identyfikator subskrypcji zostanie pominięty, zwraca listę wszystkich zaproszeń członków. | Nie |
| Elementy członkowskie | Obiekt członków uzyskany z importu-konsorcjumPodejmowanie Umowy | Tak |
| Web3Klient | Web3Client obiekt uzyskany z New-Web3Connection | Tak |

#### <a name="example"></a>Przykład

[Nawiązuj połączenie z sieci Web3,](#establish-a-web3-connection) aby ustawić zmienną $ContractConnection.

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation –SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>Przykładowe dane wyjściowe

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Usuń-BlockchainMemberInvitation

Użyj tego polecenia cmdlet, aby odwołać zaproszenie członka konsorcjum.

```
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| Parametr | Opis | Wymagany |
|-----------|-------------|:--------:|
| SubscriptionId | Identyfikator subskrypcji platformy Azure członka do odwołania | Tak |
| Elementy członkowskie | Obiekt członków uzyskany z importu-konsorcjumPodejmowanie Umowy | Tak |
| Konto Web3 | Obiekt Web3Kontakon uzyskiwany z konta Import-Web3Account | Tak |
| Web3Klient | Web3Client obiekt uzyskany z New-Web3Connection | Tak |

#### <a name="example"></a>Przykład

[Nawiązuj połączenie z sieci Web3,](#establish-a-web3-connection) aby ustawić zmienne $ContractConnection i $MemberAccount.

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainZachęcie członkowskie

Użyj tego polecenia cmdlet, aby ustawić **rolę** dla istniejącego zaproszenia. Zaproszenia mogą zmieniać tylko administratorzy konsorcjum.

```
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parametr | Opis | Wymagany |
|-----------|-------------|:--------:|
| SubscriptionId | Identyfikator subskrypcji platformy Azure członka do zaproszenia | Tak |
| Rola | Nowa rola konsorcjum na zaproszenie. Wartościami mogą być **UŻYTKOWNIK** lub **ADMINISTRATOR**. | Tak |
| Elementy członkowskie |  Obiekt członków uzyskany z importu-konsorcjumPodejmowanie Umowy | Tak |
| Konto Web3 | Obiekt Web3Kontakon uzyskiwany z konta Import-Web3Account | Tak |
| Web3Klient | Web3Client obiekt uzyskany z New-Web3Connection | Tak |

#### <a name="example"></a>Przykład

[Nawiązuj połączenie z sieci Web3,](#establish-a-web3-connection) aby ustawić zmienne $ContractConnection i $MemberAccount.

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat konsorcjów, członków i węzłów, zobacz [konsorcjum usługi Azure Blockchain Service](consortium.md)
