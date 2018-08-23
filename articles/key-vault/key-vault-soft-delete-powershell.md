---
ms.assetid: ''
title: Usługa Azure Key Vault — jak używać usuwania nietrwałego za pomocą programu PowerShell
description: Wielkość przykłady usuwania nietrwałego za pomocą wycinki kodu programu PowerShell
services: key-vault
author: bryanla
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 08/21/2017
ms.author: bryanla
ms.openlocfilehash: 174a5b41e6a48ea74cd813746b7c070463a8185b
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2018
ms.locfileid: "42060224"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>Jak używać usuwania nietrwałego w usłudze Key Vault przy użyciu programu PowerShell

Funkcja usuwania nietrwałego w usłudze Azure Key Vault umożliwia odzyskiwanie usuniętych magazynów i obiektów magazynu. W szczególności opcji soft-delete adresów w następujących scenariuszach:

- Obsługa do usunięcia możliwych do odzyskania magazynu kluczy
- Obsługa możliwe do odzyskania usuwanie obiektów usługi key vault; kluczy i wpisów tajnych oraz ich, a certyfikaty

## <a name="prerequisites"></a>Wymagania wstępne

- Program Azure PowerShell 4.0.0 lub nowsze wersje — Jeśli nie skonfigurowano tym już, zainstaluj program Azure PowerShell i skojarzyć go z subskrypcją platformy Azure, zobacz [jak zainstalować i skonfigurować program Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). 

>[!NOTE]
> Brak pliku nieaktualną wersją naszych formatowanie danych wyjściowych programu PowerShell dla Key Vault, który **może** być załadowane do środowiska zamiast poprawnej wersji. Firma Microsoft są przewidywania zaktualizowanej wersji programu PowerShell, które zawierają potrzebne korekty do formatowania danych wyjściowych i aktualizacja nastąpi w tym temacie w tym czasie. Bieżące obejście problemu należy napotkasz ten problem formatowania, jest:
> - Użyj następującego zapytania, jeśli nie widzisz opcji soft-delete włączone właściwości opisanych w tym temacie: `$vault = Get-AzureRmKeyVault -VaultName myvault; $vault.EnableSoftDelete`.


Dla usługi Key Vault refernece określonych informacji dla programu PowerShell, zobacz [dokumentacja programu PowerShell dla usługi Azure Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-4.2.0).

## <a name="required-permissions"></a>Wymagane uprawnienia

Operacje usługi Key Vault oddzielnie odbywa się za pośrednictwem uprawnień kontroli dostępu opartej na rolach w następujący sposób:

| Operacja | Opis | Uprawnienia użytkownika |
|:--|:--|:--|
|List|Listy usunięte magazynów kluczy.|Microsoft.KeyVault/deletedVaults/read|
|Recover|Przywrócenie usuniętego magazynu kluczy.|Microsoft.KeyVault/vaults/write|
|Purge|Trwale usuwa usunięty magazyn kluczy i całą jego zawartość.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Aby uzyskać więcej informacji na temat uprawnień i kontroli dostępu, zobacz [zabezpieczanie własnego magazynu kluczy](key-vault-secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Włączenie opcji soft-delete

Aby można było odzyskać usunięty magazyn kluczy lub obiektów przechowywanych w magazynie kluczy, należy najpierw włączyć opcji soft-delete, dla tego magazynu kluczy.

### <a name="existing-key-vault"></a>Istniejącego magazynu kluczy

Dla istniejącego magazynu kluczy o nazwie ContosoVault Włącz opcji soft-delete w następujący sposób. 

>[!NOTE]
>Aktualnie należy użyć manipulowania zasobów usługi Azure Resource Manager do zapisu bezpośrednio *enableSoftDelete* właściwości zasobu usługi Key Vault.

```powershell
($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzureRmResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>Nowy magazyn kluczy

Włączenie opcji soft-delete dla nowego magazynu kluczy jest wykonywane w czasie jego tworzenia, dodając Flaga włączenia opcji soft-delete, aby Twoje polecenia create.

```powershell
New-AzureRmKeyVault -VaultName "ContosoVault" -ResourceGroupName "ContosoRG" -Location "westus" -EnableSoftDelete
```

### <a name="verify-soft-delete-enablement"></a>Sprawdź włączenie opcji soft-delete

Aby sprawdzić, czy magazyn kluczy ma włączone opcji soft-delete, uruchom *uzyskać* poleceń i poszukaj "nietrwałego usuwania włączony?" atrybut i jego ustawienie wartości true lub false.

```powershell
Get-AzureRmKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-key-vault-protected-by-soft-delete"></a>Usuwanie magazynu kluczy chronionych przez opcji soft-delete

Polecenie, aby usunąć (lub usuwanie) magazynu kluczy pozostają takie same, ale jej zachowanie zmienia się w zależności od tego, czy włączono opcji soft-delete lub nie.

```powershell
Remove-AzureRmKeyVault -VaultName 'ContosoVault'
```

> [!IMPORTANT]
>Jeśli uruchamiasz poprzednie polecenie dla usługi key vault, który nie ma opcji soft-delete, włączone, spowoduje trwałe usunięcie tego magazynu kluczy i całą jego zawartość, bez żadnych opcji odzyskiwania.

### <a name="how-soft-delete-protects-your-key-vaults"></a>Sposób usuwania nietrwałego ochrony Twoich magazynów kluczy

Przy użyciu opcji soft-delete włączone:

- Po usunięciu magazynu kluczy jest usuwane z grupy zasobów i umieszczone w zarezerwowaną przestrzenią nazw, które są tylko skojarzona z lokalizacją, w której został utworzony. 
- Obiekty usunięty klucz magazynu, takie jak klucze, wpisy tajne i certyfikaty, są niedostępne i pozostają tak, gdy ich zawierającą magazyn kluczy jest w stanie usunięty. 
- Nazwa DNS dla magazynu kluczy w stanie usunięty jest nadal zarezerwowany, więc nie można utworzyć nowego magazynu kluczy o takiej samej nazwie.  

Możesz wyświetlić stan usunięto magazynów kluczy, powiązaną z Twoją subskrypcją za pomocą następującego polecenia:

```powershell
PS C:\> Get-AzureRmKeyVault -InRemovedStateVault 

Name           : ContosoVault
Location             : westus
Id                   : /subscriptions/xxx/providers/Microsoft.KeyVault/locations/westus/deletedVaults/ContosoVault
Resource ID          : /subscriptions/xxx/resourceGroups/ContosoVault/providers/Microsoft.KeyVault/vaults/ContosoVault
Deletion Date        : 5/9/2017 12:14:14 AM
Scheduled Purge Date : 8/7/2017 12:14:14 AM
Tags                 :
```

*Identyfikator zasobu* w danych wyjściowych odwołuje się do oryginalnego Identyfikatora zasobu tego magazynu. Ponieważ ten magazyn kluczy jest obecnie w stanie usunięty, żaden zasób istnieje przy użyciu tego identyfikatora zasobu. *Identyfikator* pola może służyć do identyfikacji zasobu podczas odzyskiwania lub czyszczenia. *Zaplanowana data przeczyścić* pole wskazuje, kiedy magazynu zostaną trwale usunięte (przeczyścić) Jeśli nie podjęto żadnej akcji dla tego usuniętego magazynu. Domyślny okres przechowywania, używany do obliczania *zaplanowana data przeczyścić*, wynosi 90 dni.

## <a name="recovering-a-key-vault"></a>Odzyskiwanie magazynu kluczy

Aby odzyskać magazynu kluczy, należy określić nazwę usługi key vault, grupy zasobów i lokalizacji. Należy pamiętać lokalizacji i grupie zasobów usunięty magazyn kluczy, ponieważ będą potrzebne do procesu odzyskiwania magazynu kluczy.

```powershell
Undo-AzureRmKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

Po odzyskaniu magazynu kluczy, wynik jest nowy zasób o identyfikatorze usługi key vault oryginalny zasób. Usunięcie grupy zasobów, w której istniała usługi key vault można utworzyć nową grupę zasobów o takiej samej nazwie, przed usługi key vault można odzyskać.

## <a name="key-vault-objects-and-soft-delete"></a>Obiekty usługi Key Vault i opcji soft-delete

Dla klucza "ContosoFirstKey" w magazynie kluczy o nazwie "ContosoVault" przy użyciu opcji soft-delete włączone, tutaj firmy sposób można usuwać tego klucza.

```powershell
Remove-AzureKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

Za pomocą usługi key vault włączone dla opcji soft-delete usunięty klucz nadal pojawia się tak, jak zostanie usunięty z wyjątkiem sytuacji, w przypadku jawnie wyświetlić lub pobrać klucze usunięte. Większość operacji na kluczu w stanie usunięty zakończy się niepowodzeniem z wyjątkiem ofercie usunięty klucz, jej odzyskanie innemu lub usunięciu jej zawartości. 

Na przykład na żądanie, aby usunąć listę kluczy w magazynie kluczy, należy użyć następującego polecenia:

```powershell
Get-AzureKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>Stan przejścia 

Po usunięciu klucz w magazynie kluczy przy użyciu opcji soft-delete, włączone, może potrwać kilka sekund przejścia. W tym stanie przejścia może pojawić się, że klucz nie jest w stanie aktywnym lub w stanie usunięty. To polecenie spowoduje wyświetlenie listy wszystkich usuniętych kluczy w magazynie kluczy o nazwie "ContosoVault".

```powershell
  Get-AzureKeyVaultKey -VaultName ContosoVault -InRemovedState
  Vault Name           : ContosoVault
  Name                 : ContosoFirstKey
  Id                   : https://ContosoVault.vault.azure.net:443/keys/ContosoFirstKey
  Deleted Date         : 2/14/2017 8:20:52 PM
  Scheduled Purge Date : 5/15/2017 8:20:52 PM
  Enabled              : True
  Expires              :
  Not Before           :
  Created              : 2/14/2017 8:16:07 PM
  Updated              : 2/14/2017 8:16:07 PM
  Tags                 :
```

### <a name="using-soft-delete-with-key-vault-objects"></a>Używanie opcji soft-delete z obiektów magazynu kluczy

Podobnie jak magazynów kluczy, usunięty klucz hasło lub certyfikat pozostanie w stanie usunięty przez 90 dni chyba że je odzyskać lub je Wyczyść. 

#### <a name="keys"></a>Klucze

Aby odzyskać usunięty klucz:

```powershell
Undo-AzureKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

Aby trwale usunąć klucza:

```powershell
Remove-AzureKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

>[!NOTE]
>Trwałe usuwanie klucza spowoduje trwałe usunięcie go, co oznacza, że nie będzie możliwe do odzyskania.

**Odzyskać** i **przeczyścić** akcji ma własne uprawnienia skojarzone zasady dostępu magazynu kluczy. Dla użytkownika lub nazwa główna usługi, aby można było wykonać **odzyskać** lub **przeczyścić** działania mają odpowiednie uprawnienia dla tego obiektu (klucza lub klucza tajnego) w zasadach dostępu magazynu kluczy. Domyślnie **przeczyścić** uprawnienie nie jest dodawany do zasad dostępu magazynu kluczy po skrótu "all" jest używane do udzielania wszystkie uprawnienia dla użytkownika. Należy jawnie udzielić **przeczyścić** uprawnień. Na przykład następujące polecenie przyznaje user@contoso.com uprawnień do wykonania kilka operacji dotyczących kluczy w *ContosoVault* tym **przeczyścić**.

#### <a name="set-a-key-vault-access-policy"></a>Ustawianie zasad dostępu magazynu kluczy

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> W przypadku istniejącego magazynu kluczy, który miał tylko opcji soft-delete, włączone, możesz nie mieć **odzyskać** i **przeczyścić** uprawnienia.

#### <a name="secrets"></a>Wpisy tajne

Takie jak klucze wpisy tajne w magazynie kluczy są przeznaczone na za pomocą ich własnych poleceń. Czynności, są polecenia usuwania, lista, odzyskiwanie i przeczyszczaniu wpisów tajnych.

- Usuń klucz tajny o nazwie SQLPassword: 
```powershell
Remove-AzureKeyVaultSecret -VaultName ContosoVault -name SQLPassword
```

- Wyświetl wszystkie usunięte wpisów tajnych w magazynie kluczy: 
```powershell
Get-AzureKeyVaultSecret -VaultName ContosoVault -InRemovedState
```

- Aby odzyskać wpisu tajnego w stanie usunięty: 
```powershell
Undo-AzureKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
```

- Wyczyść wpis tajny w stanie usunięty: 
```powershell
Remove-AzureKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
```

>[!NOTE]
>Trwałe usuwanie wpisu tajnego spowoduje trwałe usunięcie go, co oznacza, że nie będzie możliwe do odzyskania.

## <a name="purging-and-key-vaults"></a>Magazyny przeczyszczania i klucza

### <a name="key-vault-objects"></a>Obiekty usługi Key vault

Trwałe usuwanie klucza, hasło lub certyfikat spowoduje trwałe usunięcie go, co oznacza, że nie będzie możliwe do odzyskania. Magazyn kluczy, który zawierał usuniętego obiektu jednak pozostaną nienaruszone, podobnie jak wszystkie inne obiekty w magazynie kluczy. 

### <a name="key-vaults-as-containers"></a>Magazyny kluczy jako kontenery
Podczas przeczyszczania magazynu kluczy są wszystkie jego zawartość, w tym klucze, wpisy tajne i certyfikaty, trwale usunięte. Aby przeczyścić magazynu kluczy, należy użyć `Remove-AzureRmKeyVault` polecenia z opcją `-InRemovedState` i, określając lokalizację usunięto magazyn kluczy przy użyciu `-Location location` argumentu. Można znaleźć lokalizacji usunięty magazyn, za pomocą polecenia `Get-AzureRmKeyVault -InRemovedState`.

```powershell
Remove-AzureRmKeyVault -VaultName ContosoVault -InRemovedState -Location westus
```

>[!NOTE]
>Przeczyszczanie usługi key vault spowoduje trwałe usunięcie go, co oznacza, że nie będzie możliwe do odzyskania.

### <a name="purge-permissions-required"></a>Wyczyść wymagane uprawnienia
- Aby przeczyścić usunięty magazyn kluczy w taki sposób, że magazyn i całą jego zawartość są trwale usuwane, użytkownik potrzebuje uprawnień RBAC do wykonania *Microsoft.KeyVault/locations/deletedVaults/purge/action* operacji. 
- Aby wyświetlić listę usuniętych klucza, magazynie użytkownik potrzebuje uprawnień RBAC do wykonania *Microsoft.KeyVault/deletedVaults/read* uprawnień. 
- Domyślnie tylko administrator subskrypcji ma te uprawnienia. 

### <a name="scheduled-purge"></a>Przeczyszczanie zaplanowane

Wyświetlanie listy obiektów usuniętych usługi key vault pokazuje, gdy są one schedled zostaną usunięte przez usługę Key Vault. *Zaplanowana data przeczyścić* pole wskazuje, kiedy obiekt magazynu kluczy zostaną trwale usunięte, jeśli nie podjęto żadnej akcji. Domyślnie okres przechowywania dla obiektu usunięty magazyn kluczy jest 90 dni.

>[!NOTE]
>Obiekt magazynu przeczyszczone wyzwolone przez jego *zaplanowana data przeczyścić* polu, zostaną trwale usunięte. Nie jest możliwe do odzyskania.

## <a name="other-resources"></a>Inne zasoby

- Aby uzyskać omówienie funkcji usuwania nietrwałego w usłudze Key Vault, zobacz [omówienie usuwania nietrwałego w usłudze Azure Key Vault](key-vault-ovw-soft-delete.md).
- Aby uzyskać ogólne omówienie użycia usługi Azure Key Vault, zobacz [Rozpoczynanie pracy z usługą Azure Key Vault](key-vault-get-started.md).

