---
ms.assetid: ''
title: Usługa Azure Key Vault - sposobu usuwania nietrwałego za pomocą interfejsu wiersza polecenia
description: Wielkość przykłady usuwania nietrwałego za pomocą wycinków kodu interfejsu wiersza polecenia
author: lleonard-msft
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 08/04/2017
ms.author: alleonar
ms.openlocfilehash: a9b80cae69c4e5852341385b98fcccc86d7959e9
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2018
ms.locfileid: "27927978"
---
# <a name="how-to-use-key-vault-soft-delete-with-cli"></a>Jak używać usługi Key Vault soft usunięcie z interfejsu wiersza polecenia

Funkcja usuwania nietrwałego klucza magazynu Azure umożliwia odzyskiwanie usuniętych magazynów i magazynu obiektów. W szczególności soft usunięcie adresy następujących scenariuszy:

- Obsługa możliwych do odzyskania usuwanie magazynu kluczy
- Obsługa możliwych do odzyskania usuwanie magazynu kluczy obiektów; klucze i klucze tajne, i certyfikaty

## <a name="prerequisites"></a>Wymagania wstępne

- Azure CLI 2.0 — Jeśli nie masz tej instalacji dla danego środowiska, zobacz [Zarządzanie Key Vault za pomocą interfejsu wiersza polecenia 2.0](key-vault-manage-with-cli2.md).

Dla usługi Key Vault określone informacje dotyczące interfejsu wiersza polecenia, zobacz [odwołania Azure CLI 2.0 Key Vault](https://docs.microsoft.com/cli/azure/keyvault).

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

```azurecli
az resource update --id $(az keyvault show --name ContosoVault -o tsv | awk '{print $1}') --set properties.enableSoftDelete=true
```

### <a name="new-key-vault"></a>Nowy magazyn kluczy

Włączanie usuwania nietrwałego nowego magazynu kluczy odbywa w czasie tworzenia, dodając flagi Włącz usuwania nietrwałego Twojej Utwórz polecenie.

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --enable-soft-delete true --location westus
```

### <a name="verify-soft-delete-enablement"></a>Sprawdź usuwania nietrwałego aktywacji

Aby sprawdzić, czy magazyn kluczy usuwania nietrwałego, włączona, uruchom *Pokaż* poleceń i wyszukaj "nietrwałego usunąć włączony?" atrybut i jego ustawienie wartości true lub false.

```azurecli
az keyvault show --name ContosoVault
```

## <a name="deleting-a-key-vault-protected-by-soft-delete"></a>Usuwanie magazynu kluczy chronionych przez soft-delete

Polecenie, aby usunąć (lub usuń) magazynu kluczy jest taka sama, ale jego zachowanie zmienia się w zależności od tego, czy włączono usuwania nietrwałego lub nie.

```azurecli
az keyvault delete --name ContosoVault
```

> [!IMPORTANT]
>Po uruchomieniu poprzedniego polecenia dla magazynu kluczy, który nie ma włączone soft usunięcie spowoduje trwałe usunięcie tego magazynu kluczy i całą jego zawartość bez żadnych opcji odzyskiwania.

### <a name="how-soft-delete-protects-your-key-vaults"></a>Sposób usuwania nietrwałego chroni Twoje magazynów kluczy

Elastyczne delete jest włączona:

- Po usunięciu magazynu kluczy jest usuwane z jego grupa zasobów i umieszczony w zarezerwowaną przestrzenią nazw, które są tylko skojarzone z lokalizacji, w której został utworzony. 
- Obiekty Usunięto klucz magazynu, takie jak klucze kluczy tajnych i certyfikaty, są niedostępne i pozostać tak podczas ich zawierającego magazynu kluczy jest w stanie usunięty. 
- Nazwy DNS dla magazynu kluczy w stanie usunięty jest nadal zarezerwowany, więc nie można utworzyć nowego magazynu kluczy o tej samej nazwie.  

Można wyświetlić stanu usunięcia magazynów kluczy, skojarzonymi z Twoją subskrypcją za pomocą następującego polecenia:

```azurecli
az keyvault list-deleted
```

*Identyfikator zasobu* w danych wyjściowych odwołuje się do oryginalnego identyfikator zasobu w tym magazynie. Ponieważ w tym magazynie kluczy jest obecnie w stanie usunięty, żaden z zasobów istnieje z tym identyfikatorem zasobu. *Identyfikator* pole służy do identyfikacji zasobu podczas odzyskiwania lub czyszczenie. *Zaplanowane daty przeczyścić* pole wskazuje, kiedy magazynu zostaną trwale usunięte (przeczyścić) Jeśli nie podjęto żadnej akcji dla tego magazynu usunięte. Domyślny okres przechowywania, używane do obliczania *zaplanowane daty przeczyścić*, wynosi 90 dni.

## <a name="recovering-a-key-vault"></a>Odzyskiwanie magazyn kluczy

Aby odzyskać magazyn kluczy, należy określić nazwę magazynu kluczy, grupy zasobów i lokalizacji. Należy pamiętać, lokalizacji i grupy zasobów magazynu kluczy usunięte zgodnie z potrzebami dla procesu odzyskiwania magazynu kluczy.

```azurecli
az keyvault recover --location westus --name ContosoVault
```

Po odzyskaniu magazyn kluczy, wynikiem jest nowy zasób z oryginalnego identyfikatora zasobu magazynu kluczy Usunięcie grupy zasobów, której istniała magazynu kluczy można utworzyć nową grupę zasobów o tej samej nazwie, zanim magazyn kluczy, które mogą zostać odzyskane.

## <a name="key-vault-objects-and-soft-delete"></a>Obiekty usługi Key Vault i usuwania nietrwałego

Dla klucza "ContosoFirstKey" w magazynie kluczy o nazwie "ContosoVault" z usuwania nietrwałego włączone, tutaj w sposób można usuwać tego klucza.

```azurecli
az keyvault key delete --name ContosoFirstKey --vault-name ContosoVault
```

Z Twoim magazynem kluczy włączone dla usuwania nietrwałego Usunięto klucz jest nadal wyświetlana tak, jak jest usuwany z wyjątkiem, gdy jawnie listy lub pobrać usuniętych kluczy. Większość operacji na klucz w stanie usunięty zakończy się niepowodzeniem z wyjątkiem wyświetlania Usunięto klucz, odzyskaniu go lub usunięciu jej zawartości. 

Na przykład żądania, aby usunąć listę kluczy w magazynie kluczy, należy użyć następującego polecenia:

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="transition-state"></a>Stan przejścia 

Podczas usuwania klucza w magazynie kluczy usuwania nietrwałego, włączone, gdy może potrwać kilka sekund przejścia. W tym stanie przejścia może pojawić się, że klucz nie jest w stanie aktywnym lub w stanie usunięty. To polecenie spowoduje wyświetlenie listy wszystkich usuniętych kluczy w magazynie kluczy o nazwie "ContosoVault".

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="using-soft-delete-with-key-vault-objects"></a>Przy użyciu usuwania nietrwałego z obiektami magazyn kluczy

Po prostu jak magazynów kluczy, Usunięto klucz, hasło lub certyfikat pozostanie w stanie usunięty przez 90 dni chyba że odzyskanie go lub go przeczyścić. 

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

**Odzyskać** i **przeczyścić** akcji ma swoje własne w zasadach dostępu do magazynu kluczy są skojarzone uprawnienia. Dla użytkownika lub nazwy głównej usługi, aby można było wykonać **odzyskać** lub **przeczyścić** akcji w zasadach dostępu do magazynu kluczy muszą mieć odpowiednie uprawnienia dla tego obiektu (klucz lub klucz tajny). Domyślnie **przeczyścić** uprawnienia nie została dodana do magazynu kluczy, zasady dostępu w przypadku używania "all" skrót do wszystkie uprawnienia użytkownika. Należy jawnie udzielić **przeczyścić** uprawnienia. Na przykład następujące polecenie przyznaje user@contoso.com uprawnień, aby wykonać kilka operacji na klucze w *ContosoVault* tym **przeczyścić**.

#### <a name="set-a-key-vault-access-policy"></a>Ustawianie zasad dostępu do magazynu kluczy

```azurecli
az keyvault set-policy --name ContosoVault --key-permissions get create delete list update import backup restore recover purge
```

>[!NOTE] 
> Jeśli masz istniejący magazyn kluczy, który właśnie miał usuwania nietrwałego, włączona, użytkownik może nie mieć **odzyskać** i **przeczyścić** uprawnienia.

#### <a name="secrets"></a>Wpisy tajne

Takie jak klucze kluczy tajnych w magazynie kluczy wykonywane są w ich własnych poleceń. Po, są polecenia usuwania, wyświetlanie, odzyskiwania i przeczyszczanie kluczy tajnych.

- Usuń klucz tajny o nazwie SQLPassword: 
```azurecli
az keyvault secret delete --vault-name ContosoVault -name SQLPassword
```

- Wyświetl listę wszystkich usuniętych kluczy tajnych w magazynie kluczy: 
```azurecli
az keyvault secret list-deleted --vault-name ContosoVault
```

- Odzyskiwanie klucza tajnego w stanie usunięty: 
```azurecli
az keyvault secret recover --name SQLPassword --vault-name ContosoVault
```

- Przeczyść klucza tajnego w stanie usunięty: 
```azurecli
az keyvault secret purge --name SQLPAssword --vault-name ContosoVault
```

>[!NOTE]
>Przeczyszczanie klucz tajny spowoduje trwałe usunięcie go, co oznacza, że nie będzie możliwe do odzyskania.

## <a name="purging-and-key-vaults"></a>Magazyny przeczyszczania i klucza

### <a name="key-vault-objects"></a>Obiekty magazyn kluczy

Trwałe usuwanie klucza, hasło lub certyfikat spowoduje trwałe usunięcie go, co oznacza, że nie będzie możliwe do odzyskania. Magazyn kluczy, który zawiera usunięty obiekt jednak pozostaną nienaruszone, podobnie jak wszystkie inne obiekty w magazynie kluczy. 

### <a name="key-vaults-as-containers"></a>Klucz magazynów jako kontenery
Podczas przeczyszczania magazynu kluczy są całą jego zawartość, łącznie z kluczy, kluczy tajnych i certyfikaty, trwałe skasowanie. Aby przeczyścić magazyn kluczy, należy użyć `az keyvault purge` polecenia. Można znaleźć lokalizacji Twojej subskrypcji usunięto magazynów kluczy za pomocą polecenia `az keyvault list-deleted`.

```azurecli
az keyvault purge --location westus --name ContosoVault
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

