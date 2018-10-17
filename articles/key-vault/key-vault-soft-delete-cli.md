---
ms.assetid: ''
title: Usługa Azure Key Vault — jak używać usuwania nietrwałego za pomocą interfejsu wiersza polecenia
description: Wielkość przykłady usuwania nietrwałego za pomocą wycinki kodu interfejsu wiersza polecenia
author: bryanla
manager: mbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.workload: identity
ms.date: 10/15/2018
ms.author: bryanla
ms.openlocfilehash: af2d480e84ca69c0ecd795e38371375e6a71542b
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49363643"
---
# <a name="how-to-use-key-vault-soft-delete-with-cli"></a>Jak używać usuwania nietrwałego w usłudze Key Vault z interfejsem wiersza polecenia

Funkcja usuwania nietrwałego w usłudze Azure Key Vault umożliwia odzyskiwanie usuniętych magazynów i obiektów magazynu. W szczególności opcji soft-delete adresów w następujących scenariuszach:

- Obsługa do usunięcia możliwych do odzyskania magazynu kluczy
- Obsługa możliwe do odzyskania usuwanie obiektów usługi key vault; kluczy i wpisów tajnych oraz ich, a certyfikaty

## <a name="prerequisites"></a>Wymagania wstępne

- Wiersza polecenia platformy Azure — Jeśli nie masz, to skonfigurować w danym środowisku, zobacz [Zarządzanie Key Vault przy użyciu wiersza polecenia platformy Azure](key-vault-manage-with-cli2.md).

Dla usługi Key Vault szczegółowe informacje dotyczące interfejsu wiersza polecenia, zobacz [odwołanie do magazynu kluczy interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/keyvault).

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

> [!IMPORTANT]
> Trwa włączanie nietrwałe usuwanie na magazyn kluczy jest czynnością nieodwracalną. Gdy właściwość usuwania nietrwałego została ustawiona "true", nie można zmienić ani usunąć.  

### <a name="existing-key-vault"></a>Istniejącego magazynu kluczy

Dla istniejącego magazynu kluczy o nazwie ContosoVault Włącz opcji soft-delete w następujący sposób. 

```azurecli
az resource update --id $(az keyvault show --name ContosoVault -o tsv | awk '{print $1}') --set properties.enableSoftDelete=true
```

### <a name="new-key-vault"></a>Nowy magazyn kluczy

Włączenie opcji soft-delete dla nowego magazynu kluczy jest wykonywane w czasie jego tworzenia, dodając Flaga włączenia opcji soft-delete, aby Twoje polecenia create.

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --enable-soft-delete true --location westus
```

### <a name="verify-soft-delete-enablement"></a>Sprawdź włączenie opcji soft-delete

Aby sprawdzić, czy magazyn kluczy ma włączone opcji soft-delete, uruchom *Pokaż* poleceń i poszukaj "nietrwałego usuwania włączony?" Atrybut:

```azurecli
az keyvault show --name ContosoVault
```

## <a name="deleting-a-key-vault-protected-by-soft-delete"></a>Usuwanie magazynu kluczy chronionych przez opcji soft-delete

Polecenie Usuń zmiany usługi key vault, zachowanie w zależności od tego, czy usuwania nietrwałego jest włączona.

> [!IMPORTANT]
>Jeśli uruchomisz następujące polecenie dla magazynu kluczy, który nie ma opcji soft-delete, włączone, spowoduje trwałe usunięcie tego magazynu kluczy i całej jego zawartości, bez żadnych opcji odzyskiwania!

```azurecli
az keyvault delete --name ContosoVault
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Sposób usuwania nietrwałego ochrony Twoich magazynów kluczy

Przy użyciu opcji soft-delete włączone:

- Usunięty magazyn kluczy jest usuwane z grupy zasobów i umieszczone w przestrzeni nazw zastrzeżonych, skojarzona z lokalizacją, w której został utworzony. 
- Usunięte obiekty, takie jak klucze, wpisy tajne i certyfikaty, są niedostępne, tak długo, jak ich zawierającą magazyn kluczy jest w stanie usunięty. 
- Nazwy DNS, który ma być usunięty magazyn kluczy jest zarezerwowana uniemożliwia Trwa tworzenie nowego magazynu kluczy o takiej samej nazwie.  

Możesz wyświetlić stan usunięto magazynów kluczy, powiązaną z Twoją subskrypcją za pomocą następującego polecenia:

```azurecli
az keyvault list-deleted
```
- *Identyfikator* może służyć do identyfikacji zasobu podczas odzyskiwania lub czyszczenia. 
- *Identyfikator zasobu* jest oryginalny identyfikator zasobu tego magazynu. Ponieważ ten magazyn kluczy jest obecnie w stanie usunięty, żaden zasób istnieje przy użyciu tego identyfikatora zasobu. 
- *Planowana data przeczyścić* sytuacja, w magazynie zostaną trwale usunięte, jeśli nie podjęto żadnej akcji. Domyślny okres przechowywania, używany do obliczania *zaplanowana data przeczyścić*, wynosi 90 dni.

## <a name="recovering-a-key-vault"></a>Odzyskiwanie magazynu kluczy

Aby odzyskać magazynu kluczy, określ nazwę usługi key vault, grupy zasobów i lokalizacji. Należy pamiętać, lokalizacji i grupie zasobów usunięty magazyn kluczy, gdy ich potrzebujesz w procesie odzyskiwania.

```azurecli
az keyvault recover --location westus --resource-group ContosoRG --name ContosoVault
```

Po odzyskaniu magazynu kluczy przy użyciu oryginalnego identyfikatora zasobu usługi key vault jest tworzony nowy zasób Jeśli oryginalnej grupy zasobów zostanie usunięty, jeden musi zostać utworzona z tej samej nazwie przed podjęciem próby wykonania odzyskiwania.

## <a name="key-vault-objects-and-soft-delete"></a>Obiekty usługi Key Vault i opcji soft-delete

Dla klucza "ContosoFirstKey" w magazynie kluczy o nazwie "ContosoVault" przy użyciu opcji soft-delete włączone, tutaj firmy sposób można usuwać tego klucza.

```azurecli
az keyvault key delete --name ContosoFirstKey --vault-name ContosoVault
```

Za pomocą usługi key vault włączone dla opcji soft-delete usunięty klucz nadal pojawia się tak, jak zostanie usunięty z wyjątkiem sytuacji, w przypadku jawnie wyświetlić lub pobrać klucze usunięte. Większość operacji na kluczu w stanie usunięty zakończy się niepowodzeniem z wyjątkiem ofercie usunięty klucz, jej odzyskanie innemu lub usunięciu jej zawartości. 

Na przykład na żądanie, aby usunąć listę kluczy w magazynie kluczy, należy użyć następującego polecenia:

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="transition-state"></a>Stan przejścia 

Po usunięciu klucz w magazynie kluczy przy użyciu opcji soft-delete, włączone, może potrwać kilka sekund przejścia. Podczas tego przejścia może pojawić się, że klucz nie jest w stanie aktywnym lub w stanie usunięty. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Używanie opcji soft-delete z obiektów magazynu kluczy

Podobnie jak magazyny kluczy usunięty klucz, hasło lub certyfikat, pozostaje w stanie usunięty przez 90 dni, chyba że je odzyskać lub je Wyczyść.

#### <a name="keys"></a>Klucze

Aby odzyskać wszystkie usunięte nietrwale klucza:

```azurecli
az keyvault key recover --name ContosoFirstKey --vault-name ContosoVault
```

Aby trwale skasować (czyszczenie) wszystkie usunięte nietrwale klucza:

> [!IMPORTANT]
> Przeczyszczanie klucza spowoduje trwałe usunięcie go, a nie będzie możliwe do odzyskania! 

```azurecli
az keyvault key purge --name ContosoFirstKey --vault-name ContosoVault
```

**Odzyskać** i **przeczyścić** akcji ma własne uprawnienia skojarzone zasady dostępu magazynu kluczy. Dla użytkownika lub nazwa główna usługi, aby można było wykonać **odzyskać** lub **przeczyścić** akcji, muszą mieć odpowiednie uprawnienia dla tego klucza lub klucza tajnego. Domyślnie **przeczyścić** nie została dodana do zasad dostępu magazynu kluczy, gdy "all" skrótu służy do udzielania wszystkie uprawnienia. W szczególności należy przyznać **przeczyścić** uprawnień. 

#### <a name="set-a-key-vault-access-policy"></a>Ustawianie zasad dostępu magazynu kluczy

Następujące polecenie przyznaje user@contoso.com uprawnienia do korzystania z kilku operacji dotyczących kluczy w *ContosoVault* tym **przeczyścić**:

```azurecli
az keyvault set-policy --name ContosoVault --key-permissions get create delete list update import backup restore recover purge
```

>[!NOTE] 
> W przypadku istniejącego magazynu kluczy, który miał tylko opcji soft-delete, włączone, możesz nie mieć **odzyskać** i **przeczyścić** uprawnienia.

#### <a name="secrets"></a>Wpisy tajne

Takie jak klucze wpisy tajne są zarządzane za pomocą ich własnych poleceń:

- Usuń klucz tajny o nazwie SQLPassword: 
```azurecli
az keyvault secret delete --vault-name ContosoVault -name SQLPassword
```

- Wyświetl wszystkie usunięte wpisów tajnych w magazynie kluczy: 
```azurecli
az keyvault secret list-deleted --vault-name ContosoVault
```

- Aby odzyskać wpisu tajnego w stanie usunięty: 
```azurecli
az keyvault secret recover --name SQLPassword --vault-name ContosoVault
```

- Wyczyść wpis tajny w stanie usunięty: 

  > [!IMPORTANT]
  > Trwałe usuwanie wpisu tajnego spowoduje trwałe usunięcie go, a nie będzie możliwe do odzyskania! 

  ```azurecli
  az keyvault secret purge --name SQLPAssword --vault-name ContosoVault
  ```

## <a name="purging-and-key-vaults"></a>Magazyny przeczyszczania i klucza

### <a name="key-vault-objects"></a>Obiekty usługi Key vault

Przeczyszczanie klucz, hasło lub certyfikat, powoduje trwałego usunięcia i nie będzie możliwe do odzyskania. Magazyn kluczy, który zawierał usuniętego obiektu jednak pozostaną nienaruszone, podobnie jak wszystkie inne obiekty w magazynie kluczy. 

### <a name="key-vaults-as-containers"></a>Magazyny kluczy jako kontenery
Podczas przeczyszczania magazynu kluczy jego całą zawartość są trwale usuwane, w tym kluczy, wpisów tajnych i certyfikatów. Aby przeczyścić magazynu kluczy, należy użyć `az keyvault purge` polecenia. Możesz znaleźć lokalizację w Twojej subskrypcji usunięto magazynów kluczy za pomocą polecenia `az keyvault list-deleted`.

>[!IMPORTANT]
>Przeczyszczanie usługi key vault spowoduje trwałe usunięcie go, co oznacza, że nie będzie możliwe do odzyskania!

```azurecli
az keyvault purge --location westus --name ContosoVault
```

### <a name="purge-permissions-required"></a>Wyczyść wymagane uprawnienia
- Aby przeczyścić usunięty magazyn kluczy, użytkownik potrzebuje uprawnień RBAC do *Microsoft.KeyVault/locations/deletedVaults/purge/action* operacji. 
- Aby wyświetlić listę usuniętych usługi key vault, użytkownik potrzebuje uprawnień RBAC do *Microsoft.KeyVault/deletedVaults/read* operacji. 
- Domyślnie tylko administrator subskrypcji ma te uprawnienia. 

### <a name="scheduled-purge"></a>Przeczyszczanie zaplanowane

Wyświetlanie listy obiektów usuniętych usługi key vault pokazuje również, gdy są zaplanowane zostaną usunięte przez usługę Key Vault. *Planowana data przeczyścić* wskazuje, kiedy obiekt magazynu kluczy zostaną trwale usunięte, jeśli nie podjęto żadnej akcji. Domyślnie okres przechowywania dla obiektu usunięty magazyn kluczy jest 90 dni.

>[!IMPORTANT]
>Obiekt magazynu przeczyszczone wyzwolone przez jego *zaplanowana data przeczyścić* polu, zostaną trwale usunięte. Nie jest możliwe do odzyskania!

## <a name="other-resources"></a>Inne zasoby

- Aby uzyskać omówienie funkcji usuwania nietrwałego w usłudze Key Vault, zobacz [omówienie usuwania nietrwałego w usłudze Azure Key Vault](key-vault-ovw-soft-delete.md).
- Aby uzyskać ogólne omówienie użycia usługi Azure Key Vault, zobacz [Rozpoczynanie pracy z usługą Azure Key Vault](key-vault-get-started.md).

