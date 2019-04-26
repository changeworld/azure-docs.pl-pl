---
title: Usługa Azure Key Vault — jak używać usuwania nietrwałego za pomocą programu PowerShell
description: Wielkość przykłady usuwania nietrwałego za pomocą wycinki kodu programu PowerShell
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: mbaldwin
ms.openlocfilehash: ecc87e03a80ce10bedbe26b3ebb452ec704eefcb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60461369"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>Jak używać usuwania nietrwałego w usłudze Key Vault przy użyciu programu PowerShell

Funkcja usuwania nietrwałego w usłudze Azure Key Vault umożliwia odzyskiwanie usuniętych magazynów i obiektów magazynu. W szczególności opcji soft-delete adresów w następujących scenariuszach:

- Obsługa do usunięcia możliwych do odzyskania magazynu kluczy
- Obsługa możliwe do odzyskania usuwanie obiektów usługi key vault; kluczy i wpisów tajnych oraz ich, a certyfikaty

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- Program Azure PowerShell 1.0.0 lub nowsze wersje — Jeśli nie skonfigurowano tym już, zainstaluj program Azure PowerShell i skojarzyć go z subskrypcją platformy Azure, zobacz [jak zainstalować i skonfigurować program Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). 

>[!NOTE]
> Brak pliku nieaktualną wersją naszych formatowanie danych wyjściowych programu PowerShell dla Key Vault, który **może** być załadowane do środowiska zamiast poprawnej wersji. Firma Microsoft są przewidywania zaktualizowanej wersji programu PowerShell, które zawierają potrzebne korekty do formatowania danych wyjściowych i aktualizacja nastąpi w tym temacie w tym czasie. Bieżące obejście problemu należy napotkasz ten problem formatowania, jest:
> - Użyj następującego zapytania, jeśli nie widzisz opcji soft-delete włączone właściwości opisanych w tym temacie: `$vault = Get-AzKeyVault -VaultName myvault; $vault.EnableSoftDelete`.


Dla usługi Key Vault szczegółowe informacje dotyczące programu PowerShell, zobacz [dokumentacja programu PowerShell dla usługi Azure Key Vault](/powershell/module/az.keyvault).

## <a name="required-permissions"></a>Wymagane uprawnienia

Operacje usługi Key Vault oddzielnie odbywa się za pośrednictwem uprawnień kontroli dostępu opartej na rolach w następujący sposób:

| Operacja | Opis | Uprawnienia użytkownika |
|:--|:--|:--|
|List|Listy usunięte magazynów kluczy.|Microsoft.KeyVault/deletedVaults/read|
|Recover|Przywrócenie usuniętego magazynu kluczy.|Microsoft.KeyVault/vaults/write|
|Purge|Trwale usuwa usunięty magazyn kluczy i całą jego zawartość.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Aby uzyskać więcej informacji na temat uprawnień i kontroli dostępu, zobacz [zabezpieczanie własnego magazynu kluczy](key-vault-secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Włączenie opcji soft-delete

Możesz włączyć "opcji soft-delete" umożliwić odzyskiwanie usuniętych usługi key vault lub obiektów przechowywanych w magazynie kluczy.

### <a name="existing-key-vault"></a>Istniejącego magazynu kluczy

Dla istniejącego magazynu kluczy o nazwie ContosoVault Włącz opcji soft-delete w następujący sposób. 

```powershell
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>Nowy magazyn kluczy

Włączenie opcji soft-delete dla nowego magazynu kluczy jest wykonywane w czasie jego tworzenia, dodając Flaga włączenia opcji soft-delete, aby Twoje polecenia create.

```powershell
New-AzKeyVault -Name "ContosoVault" -ResourceGroupName "ContosoRG" -Location "westus" -EnableSoftDelete
```

### <a name="verify-soft-delete-enablement"></a>Sprawdź włączenie opcji soft-delete

Aby sprawdzić, czy magazyn kluczy ma włączone opcji soft-delete, uruchom *Pokaż* poleceń i poszukaj "nietrwałego usuwania włączony?" Atrybut:

```powershell
Get-AzKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>Usuwanie opcji soft-delete chronionego magazynu kluczy

Polecenie Usuń zmiany usługi key vault, zachowanie w zależności od tego, czy usuwania nietrwałego jest włączona.

> [!IMPORTANT]
>Jeśli uruchomisz następujące polecenie dla magazynu kluczy, który nie ma opcji soft-delete, włączone, spowoduje trwałe usunięcie tego magazynu kluczy i całej jego zawartości, bez żadnych opcji odzyskiwania!

```powershell
Remove-AzKeyVault -VaultName 'ContosoVault'
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Sposób usuwania nietrwałego ochrony Twoich magazynów kluczy

Przy użyciu opcji soft-delete włączone:

- Usunięty magazyn kluczy jest usuwane z grupy zasobów i umieszczone w przestrzeni nazw zastrzeżonych, skojarzona z lokalizacją, w której został utworzony. 
- Usunięte obiekty, takie jak klucze, wpisy tajne i certyfikaty, są niedostępne, tak długo, jak ich zawierającą magazyn kluczy jest w stanie usunięty. 
- Nazwy DNS, który ma być usunięty magazyn kluczy jest zarezerwowana uniemożliwia Trwa tworzenie nowego magazynu kluczy o takiej samej nazwie.  

Możesz wyświetlić stan usunięto magazynów kluczy, powiązaną z Twoją subskrypcją za pomocą następującego polecenia:

```powershell
Get-AzKeyVault -InRemovedState 
```

- *Identyfikator* może służyć do identyfikacji zasobu podczas odzyskiwania lub czyszczenia. 
- *Identyfikator zasobu* jest oryginalny identyfikator zasobu tego magazynu. Ponieważ ten magazyn kluczy jest obecnie w stanie usunięty, żaden zasób istnieje przy użyciu tego identyfikatora zasobu. 
- *Planowana data przeczyścić* sytuacja, w magazynie zostaną trwale usunięte, jeśli nie podjęto żadnej akcji. Domyślny okres przechowywania, używany do obliczania *zaplanowana data przeczyścić*, wynosi 90 dni.

## <a name="recovering-a-key-vault"></a>Odzyskiwanie magazynu kluczy

Aby odzyskać magazynu kluczy, określ nazwę usługi key vault, grupy zasobów i lokalizacji. Należy pamiętać, lokalizacji i grupie zasobów usunięty magazyn kluczy, gdy ich potrzebujesz w procesie odzyskiwania.

```powershell
Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

Po odzyskaniu magazynu kluczy przy użyciu oryginalnego identyfikatora zasobu usługi key vault jest tworzony nowy zasób Jeśli oryginalnej grupy zasobów zostanie usunięty, jeden musi zostać utworzona z tej samej nazwie przed podjęciem próby wykonania odzyskiwania.

## <a name="deleting-and-purging-key-vault-objects"></a>Usuwanie i usuwanie obiektów magazynu kluczy

Następujące polecenie spowoduje usunięcie klucza "ContosoFirstKey", w usłudze key vault o nazwie "ContosoVault", która ma włączoną opcji soft-delete:

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

Za pomocą usługi key vault włączone dla opcji soft-delete usunięty klucz nadal pojawia się do usunięcia, chyba że jawnie listy usuniętych kluczy. Większość operacji na kluczu w stanie usunięty zakończy się niepowodzeniem z wyjątkiem wyświetlania listy, odzyskiwanie, czyszczenia usuniętych klucza. 

Na przykład następujące polecenie wyświetla listę usuniętych kluczy w magazynie kluczy "ContosoVault":

```powershell
Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>Stan przejścia 

Po usunięciu klucz w magazynie kluczy przy użyciu opcji soft-delete, włączone, może potrwać kilka sekund przejścia. Podczas tego przejścia może pojawić się, że klucz nie jest w stanie aktywnym lub w stanie usunięty. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Używanie opcji soft-delete z obiektów magazynu kluczy

Podobnie jak magazyny kluczy usunięty klucz, hasło lub certyfikat, pozostaje w stanie usunięty przez 90 dni, chyba że je odzyskać lub je Wyczyść. 

#### <a name="keys"></a>Klucze

Aby odzyskać wszystkie usunięte nietrwale klucza:

```powershell
Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

Aby trwale skasować (czyszczenie) wszystkie usunięte nietrwale klucza:

> [!IMPORTANT]
> Przeczyszczanie klucza spowoduje trwałe usunięcie go, a nie będzie możliwe do odzyskania! 

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

**Odzyskać** i **przeczyścić** akcji ma własne uprawnienia skojarzone zasady dostępu magazynu kluczy. Dla użytkownika lub nazwa główna usługi, aby można było wykonać **odzyskać** lub **przeczyścić** akcji, muszą mieć odpowiednie uprawnienia dla tego klucza lub klucza tajnego. Domyślnie **przeczyścić** nie została dodana do zasad dostępu magazynu kluczy, gdy "all" skrótu służy do udzielania wszystkie uprawnienia. W szczególności należy przyznać **przeczyścić** uprawnień. 

#### <a name="set-a-key-vault-access-policy"></a>Ustawianie zasad dostępu magazynu kluczy

Następujące polecenie przyznaje user@contoso.com uprawnienia do korzystania z kilku operacji dotyczących kluczy w *ContosoVault* tym **przeczyścić**:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> W przypadku istniejącego magazynu kluczy, który miał tylko opcji soft-delete, włączone, możesz nie mieć **odzyskać** i **przeczyścić** uprawnienia.

#### <a name="secrets"></a>Wpisy tajne

Takie jak klucze wpisy tajne są zarządzane za pomocą ich własnych poleceń:

- Usuń klucz tajny o nazwie SQLPassword: 
  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

- Wyświetl wszystkie usunięte wpisów tajnych w magazynie kluczy: 
  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

- Aby odzyskać wpisu tajnego w stanie usunięty: 
  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

- Wyczyść wpis tajny w stanie usunięty: 

  > [!IMPORTANT]
  > Trwałe usuwanie wpisu tajnego spowoduje trwałe usunięcie go, a nie będzie możliwe do odzyskania!

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```

## <a name="purging-a-soft-delete-protected-key-vault"></a>Trwałe usuwanie opcji soft-delete chronionego magazynu kluczy

> [!IMPORTANT]
> Trwałe usuwanie magazynu kluczy lub jeden zawartych w nim obiektów, spowoduje to trwałe usunięcie go, co oznacza, że nie będzie możliwe do odzyskania!

Funkcja przeczyszczania umożliwia trwałe usunięcie obiektu usługi key vault lub całego magazynu kluczy, który został wcześniej wszystkie usunięte nietrwale. Jak pokazano w poprzedniej sekcji, obiekty przechowywane w magazynie kluczy przy użyciu funkcji usuwania nietrwałego, włączone, można przejść przez różne stany:
- **Aktywne**: przed usunięciem.
- **Wszystkie usunięte nietrwale**: po jego usunięciu stanie się na liście i odzyskany do stanu aktywna.
- **Trwale usunięte**: po przeczyszczenie nie można go odzyskać.


To samo dotyczy magazynu key Vault. Aby trwale usunąć wszystkie usunięte nietrwale magazyn kluczy i jego zawartość, można przeczyścić sam magazyn kluczy.

### <a name="purging-a-key-vault"></a>Trwałe usuwanie magazynu kluczy

Podczas przeczyszczania magazynu kluczy jego całą zawartość są trwale usuwane, w tym kluczy, wpisów tajnych i certyfikatów. Aby Przeczyść usunięty nietrwale magazyn kluczy, należy użyć `Remove-AzKeyVault` polecenia z opcją `-InRemovedState` i, określając lokalizację usunięto magazyn kluczy przy użyciu `-Location location` argumentu. Można znaleźć lokalizacji usunięty magazyn, za pomocą polecenia `Get-AzKeyVault -InRemovedState`.

```powershell
Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
```

### <a name="purge-permissions-required"></a>Wyczyść wymagane uprawnienia
- Aby przeczyścić usunięty magazyn kluczy, użytkownik potrzebuje uprawnień RBAC do *Microsoft.KeyVault/locations/deletedVaults/purge/action* operacji. 
- Aby wyświetlić listę usuniętych usługi key vault, użytkownik potrzebuje uprawnień RBAC do *Microsoft.KeyVault/deletedVaults/read* operacji. 
- Domyślnie tylko administrator subskrypcji ma te uprawnienia. 

### <a name="scheduled-purge"></a>Przeczyszczanie zaplanowane

Wyświetlanie listy obiektów usuniętych usługi key vault pokazuje również, gdy są zaplanowane zostaną usunięte przez usługę Key Vault. *Planowana data przeczyścić* wskazuje, kiedy obiekt magazynu kluczy zostaną trwale usunięte, jeśli nie podjęto żadnej akcji. Domyślnie okres przechowywania dla obiektu usunięty magazyn kluczy jest 90 dni.

>[!IMPORTANT]
>Obiekt magazynu przeczyszczone wyzwolone przez jego *zaplanowana data przeczyścić* polu, zostaną trwale usunięte. Nie jest możliwe do odzyskania!

## <a name="enabling-purge-protection"></a>Włączenie ochrony przeczyszczania

Gdy ochrona przed czyszczeniem jest włączona, magazynu lub obiektu w usuniętej stanu nie można przeczyścić, dopóki minął okres przechowywania 90 dni. Nadal można odzyskać taki magazyn lub obiektu. Ta funkcja zapewnia dodano pewność, że magazyn lub obiektu nigdy nie można trwale usunąć, dopóki przechowywania okres został przekazany.

Ochrona przed czyszczeniem można włączyć tylko wtedy, gdy włączona jest również opcji soft-delete. 

Aby włączyć zarówno usuwanie nietrwałe i przeczyścić ochrony podczas tworzenia magazynu, należy użyć [New AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault?view=azps-1.5.0) polecenia cmdlet:

```powershell
New-AzKeyVault -Name ContosoVault -ResourceGroupName ContosoRG -Location westus -EnableSoftDelete -EnablePurgeProtection
```

Aby dodać ochrona przed czyszczeniem do istniejącego magazynu (który jest już włączone usuwanie nietrwałe), użyj [Get AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault?view=azps-1.5.0), [Get AzResource](/powershell/module/az.resources/get-azresource?view=azps-1.5.0), i [AzResource zestaw](/powershell/module/az.resources/set-azresource?view=azps-1.5.0) poleceń cmdlet:

```
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

## <a name="other-resources"></a>Inne zasoby

- Aby uzyskać omówienie funkcji usuwania nietrwałego w usłudze Key Vault, zobacz [omówienie usuwania nietrwałego w usłudze Azure Key Vault](key-vault-ovw-soft-delete.md).
- Aby uzyskać ogólne omówienie użycia usługi Azure Key Vault, zobacz [co to jest usługa Azure Key Vault?](key-vault-overview.md). Utwórz = Powodzenie}