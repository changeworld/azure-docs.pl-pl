---
ms.assetid: ''
title: Usługa Azure Key Vault - sposobu usuwania nietrwałego za pomocą programu PowerShell
description: Wielkość przykłady usuwania nietrwałego za pomocą programu PowerShell wycinki kodu
services: key-vault
author: lleonard-msft
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 08/21/2017
ms.author: alleonar
ms.openlocfilehash: 48569e31e6400e3ec8958e0bceda1fd3b72207ea
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2018
ms.locfileid: "27927995"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>Jak używać usługi Key Vault soft usunięcie przy użyciu programu PowerShell

Funkcja usuwania nietrwałego klucza magazynu Azure umożliwia odzyskiwanie usuniętych magazynów i magazynu obiektów. W szczególności soft usunięcie adresy następujących scenariuszy:

- Obsługa możliwych do odzyskania usuwanie magazynu kluczy
- Obsługa możliwych do odzyskania usuwanie magazynu kluczy obiektów; klucze i klucze tajne, i certyfikaty

## <a name="prerequisites"></a>Wymagania wstępne

- Program Azure PowerShell 4.0.0 lub później — Jeśli nie masz już tym Instalatora, zainstalować program Azure PowerShell i skojarzyć go z subskrypcją platformy Azure, zobacz [jak instalowanie i konfigurowanie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). 

>[!NOTE]
> Brak pliku nieaktualną wersją naszych formatowanie danych wyjściowych klucza magazynu w programie PowerShell **może** być załadowane do środowiska zamiast poprawnej wersji. Firma Microsoft są przewidywanie zaktualizowanej wersji programu PowerShell zawiera naprawianie potrzebne do formatowania danych wyjściowych i zaktualizuje wszystkie informacje w tym temacie w tym czasie. Bieżącego rozwiązania, powinien wystąpi ten problem formatowania, jest:
> - Użyj następującego zapytania, Jeśli zauważysz, że nie występują usuwania nietrwałego włączone właściwości opisanych w tym temacie: `$vault = Get-AzureRmKeyVault -VaultName myvault; $vault.EnableSoftDelete`.


Magazyn kluczy refernece określonych informacji dla programu PowerShell, zobacz [odwołania programu PowerShell magazynu kluczy Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-4.2.0).

## <a name="required-permissions"></a>Wymagane uprawnienia

Operacje usługi Key Vault oddzielnie są zarządzane za pośrednictwem uprawnień kontroli dostępu opartej na rolach w następujący sposób:

| Operacja | Opis | Uprawnienia użytkownika |
|:--|:--|:--|
|List|Listy usunięte magazynów kluczy.|Microsoft.KeyVault/deletedVaults/read|
|Recover|Przywrócenie usuniętych magazynu kluczy.|Microsoft.KeyVault/vaults/write|
|Purge|Trwale usuwa usunięto magazyn kluczy i całą jego zawartość.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Aby uzyskać więcej informacji dotyczących uprawnień i kontroli dostępu, zobacz [bezpiecznego magazynu kluczy](key-vault-secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Włączanie soft-delete

Aby można było odzyskać usuniętego magazynu kluczy lub obiekty przechowywane w magazynie kluczy, należy najpierw włączyć soft usunięcie tego klucza magazynu.

### <a name="existing-key-vault"></a>Istniejący magazyn kluczy

W przypadku istniejący magazyn kluczy o nazwie ContosoVault włączyć w następujący sposób usuwania nietrwałego. 

>[!NOTE]
>Obecnie należy użyć manipulowania zasobów usługi Azure Resource Manager bezpośrednio zapisać *enableSoftDelete* właściwości do zasobu magazynu kluczy.

```powershell
($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzureRmResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>Nowy magazyn kluczy

Włączanie usuwania nietrwałego nowego magazynu kluczy odbywa w czasie tworzenia, dodając flagi Włącz usuwania nietrwałego Twojej Utwórz polecenie.

```powershell
New-AzureRmKeyVault -VaultName "ContosoVault" -ResourceGroupName "ContosoRG" -Location "westus" -EnableSoftDelete
```

### <a name="verify-soft-delete-enablement"></a>Sprawdź usuwania nietrwałego aktywacji

Aby sprawdzić, czy magazyn kluczy usuwania nietrwałego, włączona, uruchom *uzyskać* poleceń i wyszukaj "nietrwałego usunąć włączony?" atrybut i jego ustawienie wartości true lub false.

```powershell
Get-AzureRmKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-key-vault-protected-by-soft-delete"></a>Usuwanie magazynu kluczy chronionych przez soft-delete

Polecenie, aby usunąć (lub usuń) magazynu kluczy jest taka sama, ale jego zachowanie zmienia się w zależności od tego, czy włączono usuwania nietrwałego lub nie.

```powershell
Remove-AzureRmKeyVault -VaultName 'ContosoVault'
```

> [!IMPORTANT]
>Po uruchomieniu poprzedniego polecenia dla magazynu kluczy, który nie ma włączone soft usunięcie spowoduje trwałe usunięcie tego magazynu kluczy i całą jego zawartość bez żadnych opcji odzyskiwania.

### <a name="how-soft-delete-protects-your-key-vaults"></a>Sposób usuwania nietrwałego chroni Twoje magazynów kluczy

Elastyczne delete jest włączona:

- Po usunięciu magazynu kluczy jest usuwane z jego grupa zasobów i umieszczony w zarezerwowaną przestrzenią nazw, które są tylko skojarzone z lokalizacji, w której został utworzony. 
- Obiekty Usunięto klucz magazynu, takie jak klucze kluczy tajnych i certyfikaty, są niedostępne i pozostać tak podczas ich zawierającego magazynu kluczy jest w stanie usunięty. 
- Nazwy DNS dla magazynu kluczy w stanie usunięty jest nadal zarezerwowany, więc nie można utworzyć nowego magazynu kluczy o tej samej nazwie.  

Można wyświetlić stanu usunięcia magazynów kluczy, skojarzonymi z Twoją subskrypcją za pomocą następującego polecenia:

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

*Identyfikator zasobu* w danych wyjściowych odwołuje się do oryginalnego identyfikator zasobu w tym magazynie. Ponieważ w tym magazynie kluczy jest obecnie w stanie usunięty, żaden z zasobów istnieje z tym identyfikatorem zasobu. *Identyfikator* pole służy do identyfikacji zasobu podczas odzyskiwania lub czyszczenie. *Zaplanowane daty przeczyścić* pole wskazuje, kiedy magazynu zostaną trwale usunięte (przeczyścić) Jeśli nie podjęto żadnej akcji dla tego magazynu usunięte. Domyślny okres przechowywania, używane do obliczania *zaplanowane daty przeczyścić*, wynosi 90 dni.

## <a name="recovering-a-key-vault"></a>Odzyskiwanie magazyn kluczy

Aby odzyskać magazyn kluczy, należy określić nazwę magazynu kluczy, grupy zasobów i lokalizacji. Należy pamiętać, lokalizacji i grupy zasobów magazynu kluczy usunięte zgodnie z potrzebami dla procesu odzyskiwania magazynu kluczy.

```powershell
Undo-AzureRmKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

Po odzyskaniu magazyn kluczy, wynikiem jest nowy zasób z oryginalnego identyfikatora zasobu magazynu kluczy Usunięcie grupy zasobów, której istniała magazynu kluczy można utworzyć nową grupę zasobów o tej samej nazwie, zanim magazyn kluczy, które mogą zostać odzyskane.

## <a name="key-vault-objects-and-soft-delete"></a>Obiekty usługi Key Vault i usuwania nietrwałego

Dla klucza "ContosoFirstKey" w magazynie kluczy o nazwie "ContosoVault" z usuwania nietrwałego włączone, tutaj w sposób można usuwać tego klucza.

```powershell
Remove-AzureKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

Z Twoim magazynem kluczy włączone dla usuwania nietrwałego Usunięto klucz jest nadal wyświetlana tak, jak jest usuwany z wyjątkiem, gdy jawnie listy lub pobrać usuniętych kluczy. Większość operacji na klucz w stanie usunięty zakończy się niepowodzeniem z wyjątkiem wyświetlania Usunięto klucz, odzyskaniu go lub usunięciu jej zawartości. 

Na przykład żądania, aby usunąć listę kluczy w magazynie kluczy, należy użyć następującego polecenia:

```powershell
Get-AzureKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>Stan przejścia 

Podczas usuwania klucza w magazynie kluczy usuwania nietrwałego, włączone, gdy może potrwać kilka sekund przejścia. W tym stanie przejścia może pojawić się, że klucz nie jest w stanie aktywnym lub w stanie usunięty. To polecenie spowoduje wyświetlenie listy wszystkich usuniętych kluczy w magazynie kluczy o nazwie "ContosoVault".

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

### <a name="using-soft-delete-with-key-vault-objects"></a>Przy użyciu usuwania nietrwałego z obiektami magazyn kluczy

Po prostu jak magazynów kluczy, Usunięto klucz, hasło lub certyfikat pozostanie w stanie usunięty przez 90 dni chyba że odzyskanie go lub go przeczyścić. 

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

**Odzyskać** i **przeczyścić** akcji ma swoje własne w zasadach dostępu do magazynu kluczy są skojarzone uprawnienia. Dla użytkownika lub nazwy głównej usługi, aby można było wykonać **odzyskać** lub **przeczyścić** akcji w zasadach dostępu do magazynu kluczy muszą mieć odpowiednie uprawnienia dla tego obiektu (klucz lub klucz tajny). Domyślnie **przeczyścić** uprawnienia nie została dodana do magazynu kluczy, zasady dostępu w przypadku używania "all" skrót do wszystkie uprawnienia użytkownika. Należy jawnie udzielić **przeczyścić** uprawnienia. Na przykład następujące polecenie przyznaje user@contoso.com uprawnień, aby wykonać kilka operacji na klucze w *ContosoVault* tym **przeczyścić**.

#### <a name="set-a-key-vault-access-policy"></a>Ustawianie zasad dostępu do magazynu kluczy

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> Jeśli masz istniejący magazyn kluczy, który właśnie miał usuwania nietrwałego, włączona, użytkownik może nie mieć **odzyskać** i **przeczyścić** uprawnienia.

#### <a name="secrets"></a>Wpisy tajne

Takie jak klucze kluczy tajnych w magazynie kluczy wykonywane są w ich własnych poleceń. Po, są polecenia usuwania, wyświetlanie, odzyskiwania i przeczyszczanie kluczy tajnych.

- Usuń klucz tajny o nazwie SQLPassword: 
```powershell
Remove-AzureKeyVaultSecret -VaultName ContosoVault -name SQLPassword
```

- Wyświetl listę wszystkich usuniętych kluczy tajnych w magazynie kluczy: 
```powershell
Get-AzureKeyVaultSecret -VaultName ContosoVault -InRemovedState
```

- Odzyskiwanie klucza tajnego w stanie usunięty: 
```powershell
Undo-AzureKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
```

- Przeczyść klucza tajnego w stanie usunięty: 
```powershell
Remove-AzureKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
```

>[!NOTE]
>Przeczyszczanie klucz tajny spowoduje trwałe usunięcie go, co oznacza, że nie będzie możliwe do odzyskania.

## <a name="purging-and-key-vaults"></a>Magazyny przeczyszczania i klucza

### <a name="key-vault-objects"></a>Obiekty magazyn kluczy

Trwałe usuwanie klucza, hasło lub certyfikat spowoduje trwałe usunięcie go, co oznacza, że nie będzie możliwe do odzyskania. Magazyn kluczy, który zawiera usunięty obiekt jednak pozostaną nienaruszone, podobnie jak wszystkie inne obiekty w magazynie kluczy. 

### <a name="key-vaults-as-containers"></a>Klucz magazynów jako kontenery
Podczas przeczyszczania magazynu kluczy są całą jego zawartość, łącznie z kluczy, kluczy tajnych i certyfikaty, trwałe skasowanie. Aby przeczyścić magazyn kluczy, należy użyć `Remove-AzureRmKeyVault` polecenie z opcją `-InRemovedState` i określając lokalizację magazynu kluczy usuniętego z `-Location location` argumentu. Można znaleźć lokalizacji magazynu usunięte za pomocą polecenia `Get-AzureRmKeyVault -InRemovedState`.

```powershell
Remove-AzureRmKeyVault -VaultName ContosoVault -InRemovedState -Location westus
```

>[!NOTE]
>Usuwanie magazynu kluczy spowoduje trwałe usunięcie go, co oznacza, że nie będzie możliwe do odzyskania.

### <a name="purge-permissions-required"></a>Uprawnienia wymagane do przeczyszczenia
- Przeczyścić usunięto magazyn kluczy, tak, aby trwale usunąć magazyn i całą jego zawartość, użytkownik musi RBAC uprawnienia do wykonywania *Microsoft.KeyVault/locations/deletedVaults/purge/action* operacji. 
- Aby wyświetlić listę Usunięto klucz, magazynie użytkownik będzie potrzebował RBAC uprawnienia do wykonania *Microsoft.KeyVault/deletedVaults/read* uprawnienia. 
- Domyślnie tylko administrator subskrypcji ma te uprawnienia. 

### <a name="scheduled-purge"></a>Zaplanowane przeczyszczenia

Wyświetlanie obiektów usuniętych magazynu kluczy pokazuje, gdy są one schedled zostaną usunięte przez magazyn kluczy. *Zaplanowane daty przeczyścić* pole wskazuje, kiedy obiekt magazynu kluczy zostaną trwale usunięte, jeśli nie podjęto żadnej akcji. Domyślnie okres przechowywania dla obiekt usunięto magazynu kluczy jest 90 dni.

>[!NOTE]
>Obiekt magazynu przeczyszczone wyzwalane przez jego *zaplanowane daty przeczyścić* pola, są trwale usuwane. Nie jest możliwe do odzyskania.

## <a name="other-resources"></a>Inne zasoby

- Omówienie funkcji usuwania nietrwałego Key Vault, zobacz [omówienie usuwania nietrwałego usługi Azure Key Vault](key-vault-ovw-soft-delete.md).
- Ogólne omówienie użycia usługi Azure Key Vault, zobacz [wprowadzenie do usługi Azure Key Vault](key-vault-get-started.md).

