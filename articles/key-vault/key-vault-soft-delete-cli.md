---
ms.assetid: ''
title: Usługa Azure Key Vault — jak używać usuwania nietrwałego za pomocą interfejsu wiersza polecenia
description: Wielkość przykłady usuwania nietrwałego za pomocą wycinki kodu interfejsu wiersza polecenia
author: bryanla
manager: mbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: bryanla
ms.openlocfilehash: 73ece43c26c3957a1b7dba02a673099f7d35e8d6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951784"
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

Aby można było odzyskać usunięty magazyn kluczy lub obiektów przechowywanych w magazynie kluczy, należy najpierw włączyć opcji soft-delete, dla tego magazynu kluczy.

### <a name="existing-key-vault"></a>Istniejącego magazynu kluczy

Dla istniejącego magazynu kluczy o nazwie ContosoVault Włącz opcji soft-delete w następujący sposób. 

>[!NOTE]
>Aktualnie należy użyć manipulowania zasobów usługi Azure Resource Manager do zapisu bezpośrednio *enableSoftDelete* właściwości zasobu usługi Key Vault.

```azurecli
az resource update --id $(az keyvault show --name ContosoVault -o tsv | awk '{print $1}') --set properties.enableSoftDelete=true
```

### <a name="new-key-vault"></a>Nowy magazyn kluczy

Włączenie opcji soft-delete dla nowego magazynu kluczy jest wykonywane w czasie jego tworzenia, dodając Flaga włączenia opcji soft-delete, aby Twoje polecenia create.

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --enable-soft-delete true --location westus
```

### <a name="verify-soft-delete-enablement"></a>Sprawdź włączenie opcji soft-delete

Aby sprawdzić, czy magazyn kluczy ma włączone opcji soft-delete, uruchom *Pokaż* poleceń i poszukaj "nietrwałego usuwania włączony?" atrybut i jego ustawienie wartości true lub false.

```azurecli
az keyvault show --name ContosoVault
```

## <a name="deleting-a-key-vault-protected-by-soft-delete"></a>Usuwanie magazynu kluczy chronionych przez opcji soft-delete

Polecenie, aby usunąć (lub usuwanie) magazynu kluczy pozostają takie same, ale jej zachowanie zmienia się w zależności od tego, czy włączono opcji soft-delete lub nie.

```azurecli
az keyvault delete --name ContosoVault
```

> [!IMPORTANT]
>Jeśli uruchamiasz poprzednie polecenie dla usługi key vault, który nie ma opcji soft-delete, włączone, spowoduje trwałe usunięcie tego magazynu kluczy i całą jego zawartość, bez żadnych opcji odzyskiwania.

### <a name="how-soft-delete-protects-your-key-vaults"></a>Sposób usuwania nietrwałego ochrony Twoich magazynów kluczy

Przy użyciu opcji soft-delete włączone:

- Po usunięciu magazynu kluczy jest usuwane z grupy zasobów i umieszczone w zarezerwowaną przestrzenią nazw, które są tylko skojarzona z lokalizacją, w której został utworzony. 
- Obiekty usunięty klucz magazynu, takie jak klucze, wpisy tajne i certyfikaty, są niedostępne i pozostają tak, gdy ich zawierającą magazyn kluczy jest w stanie usunięty. 
- Nazwa DNS dla magazynu kluczy w stanie usunięty jest nadal zarezerwowany, więc nie można utworzyć nowego magazynu kluczy o takiej samej nazwie.  

Możesz wyświetlić stan usunięto magazynów kluczy, powiązaną z Twoją subskrypcją za pomocą następującego polecenia:

```azurecli
az keyvault list-deleted
```

*Identyfikator zasobu* w danych wyjściowych odwołuje się do oryginalnego Identyfikatora zasobu tego magazynu. Ponieważ ten magazyn kluczy jest obecnie w stanie usunięty, żaden zasób istnieje przy użyciu tego identyfikatora zasobu. *Identyfikator* pola może służyć do identyfikacji zasobu podczas odzyskiwania lub czyszczenia. *Zaplanowana data przeczyścić* pole wskazuje, kiedy magazynu zostaną trwale usunięte (przeczyścić) Jeśli nie podjęto żadnej akcji dla tego usuniętego magazynu. Domyślny okres przechowywania, używany do obliczania *zaplanowana data przeczyścić*, wynosi 90 dni.

## <a name="recovering-a-key-vault"></a>Odzyskiwanie magazynu kluczy

Aby odzyskać magazynu kluczy, należy określić nazwę usługi key vault, grupy zasobów i lokalizacji. Należy pamiętać lokalizacji i grupie zasobów usunięty magazyn kluczy, ponieważ będą potrzebne do procesu odzyskiwania magazynu kluczy.

```azurecli
az keyvault recover --location westus --resource-group ContosoRG --name ContosoVault
```

Po odzyskaniu magazynu kluczy, wynik jest nowy zasób o identyfikatorze usługi key vault oryginalny zasób. Usunięcie grupy zasobów, w której istniała usługi key vault można utworzyć nową grupę zasobów o takiej samej nazwie, przed usługi key vault można odzyskać.

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

Po usunięciu klucz w magazynie kluczy przy użyciu opcji soft-delete, włączone, może potrwać kilka sekund przejścia. W tym stanie przejścia może pojawić się, że klucz nie jest w stanie aktywnym lub w stanie usunięty. To polecenie spowoduje wyświetlenie listy wszystkich usuniętych kluczy w magazynie kluczy o nazwie "ContosoVault".

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="using-soft-delete-with-key-vault-objects"></a>Używanie opcji soft-delete z obiektów magazynu kluczy

Podobnie jak magazynów kluczy, usunięty klucz hasło lub certyfikat pozostanie w stanie usunięty przez 90 dni chyba że je odzyskać lub je Wyczyść. 

#### <a name="keys"></a>Klucze

Aby odzyskać usunięty klucz:

```azurecli
az keyvault key recover --name ContosoFirstKey --vault-name ContosoVault
```

Aby trwale usunąć klucza:

```azurecli
az keyvault key purge --name ContosoFirstKey --vault-name ContosoVault
```

>[!NOTE]
>Trwałe usuwanie klucza spowoduje trwałe usunięcie go, co oznacza, że nie będzie możliwe do odzyskania.

**Odzyskać** i **przeczyścić** akcji ma własne uprawnienia skojarzone zasady dostępu magazynu kluczy. Dla użytkownika lub nazwa główna usługi, aby można było wykonać **odzyskać** lub **przeczyścić** działania mają odpowiednie uprawnienia dla tego obiektu (klucza lub klucza tajnego) w zasadach dostępu magazynu kluczy. Domyślnie **przeczyścić** uprawnienie nie jest dodawany do zasad dostępu magazynu kluczy po skrótu "all" jest używane do udzielania wszystkie uprawnienia dla użytkownika. Należy jawnie udzielić **przeczyścić** uprawnień. Na przykład następujące polecenie przyznaje user@contoso.com uprawnień do wykonania kilka operacji dotyczących kluczy w *ContosoVault* tym **przeczyścić**.

#### <a name="set-a-key-vault-access-policy"></a>Ustawianie zasad dostępu magazynu kluczy

```azurecli
az keyvault set-policy --name ContosoVault --key-permissions get create delete list update import backup restore recover purge
```

>[!NOTE] 
> W przypadku istniejącego magazynu kluczy, który miał tylko opcji soft-delete, włączone, możesz nie mieć **odzyskać** i **przeczyścić** uprawnienia.

#### <a name="secrets"></a>Wpisy tajne

Takie jak klucze wpisy tajne w magazynie kluczy są przeznaczone na za pomocą ich własnych poleceń. Czynności, są polecenia usuwania, lista, odzyskiwanie i przeczyszczaniu wpisów tajnych.

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
```azurecli
az keyvault secret purge --name SQLPAssword --vault-name ContosoVault
```

>[!NOTE]
>Trwałe usuwanie wpisu tajnego spowoduje trwałe usunięcie go, co oznacza, że nie będzie możliwe do odzyskania.

## <a name="purging-and-key-vaults"></a>Magazyny przeczyszczania i klucza

### <a name="key-vault-objects"></a>Obiekty usługi Key vault

Trwałe usuwanie klucza, hasło lub certyfikat spowoduje trwałe usunięcie go, co oznacza, że nie będzie możliwe do odzyskania. Magazyn kluczy, który zawierał usuniętego obiektu jednak pozostaną nienaruszone, podobnie jak wszystkie inne obiekty w magazynie kluczy. 

### <a name="key-vaults-as-containers"></a>Magazyny kluczy jako kontenery
Podczas przeczyszczania magazynu kluczy są wszystkie jego zawartość, w tym klucze, wpisy tajne i certyfikaty, trwale usunięte. Aby przeczyścić magazynu kluczy, należy użyć `az keyvault purge` polecenia. Możesz znaleźć lokalizację w Twojej subskrypcji usunięto magazynów kluczy za pomocą polecenia `az keyvault list-deleted`.

```azurecli
az keyvault purge --location westus --name ContosoVault
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

