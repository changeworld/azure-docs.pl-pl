---
title: Szyfrowanie danych wdrożenia
description: Dowiedz się więcej o szyfrowaniu danych utrwalonych dla zasobów wystąpienia kontenera i o tym, jak szyfrować dane za pomocą klucza zarządzanego przez klienta
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: ad232c5d9df9f6bfae3a79dbd72e2c68143be949
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080364"
---
# <a name="encrypt-deployment-data"></a>Szyfrowanie danych wdrożenia

Podczas uruchamiania zasobów wystąpień kontenerów platformy Azure (ACI) w chmurze usługa ACI zbiera i utrwala dane związane z kontenerami. ACI automatycznie szyfruje te dane, gdy są utrwalone w chmurze. To szyfrowanie chroni dane, aby pomóc w spełnić zobowiązania organizacji dotyczące bezpieczeństwa i zgodności. ACI daje również możliwość szyfrowania tych danych za pomocą własnego klucza, co daje większą kontrolę nad danymi związanymi z wdrożeniami usługi ACI.

## <a name="about-aci-data-encryption"></a>Szyfrowanie danych ACI — informacje 

Dane w ACI są szyfrowane i odszyfrowywane przy użyciu 256-bitowego szyfrowania AES. Jest włączona dla wszystkich wdrożeń usługi ACI i nie trzeba modyfikować wdrożenia lub kontenerów, aby skorzystać z tego szyfrowania. Obejmuje to metadane dotyczące wdrażania, zmienne środowiskowe, klucze przekazywane do kontenerów i dzienniki utrwalone po zatrzymaniu kontenerów, dzięki czemu nadal można je zobaczyć. Szyfrowanie nie wpływa na wydajność grupy kontenerów i nie ma żadnych dodatkowych kosztów szyfrowania.

## <a name="encryption-key-management"></a>Zarządzanie kluczami szyfrowania

Szyfrowanie można polegać na kluczach zarządzanych przez firmę Microsoft do szyfrowania danych kontenera lub zarządzać szyfrowaniem za pomocą własnych kluczy. W poniższej tabeli porównano następujące opcje: 

|    |    Klucze zarządzane przez firmę Microsoft     |     Klucze zarządzane przez klienta     |
|----|----|----|
|    Operacje szyfrowania/odszyfrowywania    |    Azure    |    Azure    |
|    Przechowywanie kluczy    |    Magazyn kluczy firmy Microsoft    |    W usłudze Azure Key Vault    |
|    Kluczowa odpowiedzialność za rotację    |    Microsoft    |    Klient    |
|    Dostęp do klucza    |    Tylko firma Microsoft    |    Microsoft, Klient    |

Pozostała część dokumentu obejmuje kroki wymagane do zaszyfrowania danych wdrożenia usługi ACI za pomocą klucza (klucz zarządzany przez klienta). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>Szyfrowanie danych za pomocą klucza zarządzanego przez klienta

### <a name="create-service-principal-for-aci"></a>Tworzenie jednostki usługi dla usługi ACI

Pierwszym krokiem jest zapewnienie, że [dzierżawa platformy Azure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) ma jednostkę usługi przypisaną do udzielania uprawnień do usługi Azure Container Instances. 

> [!IMPORTANT]
> Aby uruchomić następujące polecenie i pomyślnie utworzyć jednostkę usługi, upewnij się, że masz uprawnienia do tworzenia podmiotów usługi w dzierżawie.
>

Następujące polecenie interfejsu wiersza polecenia skonfiguruje sp ACI w środowisku platformy Azure:

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

Dane wyjściowe z uruchomienia tego polecenia powinny pokazać jednostkę usługi, która została skonfigurowana z "displayName": "Usługa wystąpienia kontenera platformy Azure".

W przypadku, gdy nie można pomyślnie utworzyć jednostki usługi:
* upewnij się, że masz do tego uprawnienia w dzierżawie
* sprawdź, czy jednostki usługi już istnieje w dzierżawie do wdrażania w ACI. Można to zrobić, `az ad sp show --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9` uruchamiając i korzystając z tego podmiotu usługi zamiast

### <a name="create-a-key-vault-resource"></a>Tworzenie zasobu magazynu kluczy

Utwórz usługę Azure Key Vault przy użyciu [witryny Azure portal](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault), [interfejsu wiersza polecenia](https://docs.microsoft.com/azure/key-vault/quick-create-cli)lub programu [PowerShell](https://docs.microsoft.com/azure/key-vault/quick-create-powershell). 

Właściwości magazynu kluczy należy stosować następujące wskazówki: 
* Nazwa: wymagana jest unikatowa nazwa. 
* Subskrypcja: wybierz subskrypcję.
* W obszarze Grupa zasobów wybierz istniejącą grupę zasobów lub utwórz nową i wprowadź nazwę grupy zasobów.
* W menu rozwijanym Lokalizacja wybierz lokalizację.
* Możesz pozostawić inne opcje do ich ustawień domyślnych lub wybrać na podstawie dodatkowych wymagań.

> [!IMPORTANT]
> Podczas używania kluczy zarządzanych przez klienta do szyfrowania szablonu wdrażania usługi ACI zaleca się ustawienie następujących dwóch właściwości w magazynie kluczy, Usuwanie nietrwałe i Nie czyścić. Te właściwości nie są domyślnie włączone, ale można włączyć za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure w nowym lub istniejącym magazynie kluczy.

### <a name="generate-a-new-key"></a>Generowanie nowego klucza 

Po utworzeniu magazynu kluczy przejdź do zasobu w witrynie Azure Portal. W menu nawigacji po lewej stronie bloku zasobu w obszarze Ustawienia kliknij pozycję **Klawisze**. W widoku "Klucze" kliknij "Generuj/Zaimportuj", aby wygenerować nowy klucz. Użyj dowolnej unikatowej nazwy dla tego klucza i innych preferencji opartych na twoich wymaganiach. 

![Generowanie nowego klucza](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>Ustawianie zasad dostępu

Utwórz nowe zasady dostępu umożliwiające usłudze ACI dostęp do klucza.

* Po wygenerowaniu klucza wróć do bloku zasobów magazynu kluczy w obszarze Ustawienia kliknij pozycję **Zasady dostępu**.
* Na stronie "Zasady dostępu" magazynu kluczy kliknij pozycję **Dodaj zasady dostępu**.
* Ustawianie *uprawnień klucza* w celu uwzględnienia uprawnień klucza **Pobierz** i **Rozpakuj zestaw kluczy** ![](./media/container-instances-encrypt-data/set-key-permissions.png)
* W przypadku *wybierz jednostkę*wybierz usługę **Azure Container Instance Service**
* Kliknij **pozycję Dodaj** u dołu 

Zasady dostępu powinny teraz być wyświetlane w zasadach dostępu magazynu kluczy.

![Nowe zasady dostępu](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>Modyfikowanie szablonu wdrożenia JSON

> [!IMPORTANT]
> Szyfrowanie danych wdrożenia za pomocą klucza zarządzanego przez klienta jest dostępne w najnowszej wersji interfejsu API (2019-12-01), która jest obecnie wdrażana. Określ tę wersję interfejsu API w szablonie wdrożenia. Jeśli masz jakiekolwiek problemy z tym, skontaktuj się z pomocą techniczną platformy Azure.

Po skonfigurowaniu klucza magazynu kluczy i zasad dostępu dodaj następujące właściwości do szablonu wdrażania usługi ACI. Dowiedz się więcej o wdrażaniu zasobów usługi ACI za pomocą szablonu w [samouczku: Wdrażanie grupy wielu kontenerów przy użyciu szablonu Menedżera zasobów](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group). 
* W `resources`obszarze `apiVersion` `2019-12-01`, ustawiono na .
* W sekcji właściwości grupy kontenerów szablonu `encryptionProperties`wdrożenia dodaj program , który zawiera następujące wartości:
  * `vaultBaseUrl`: nazwę DNS magazynu kluczy można znaleźć na bloku przeglądu zasobu magazynu kluczy w portalu
  * `keyName`: nazwa klucza wygenerowanego wcześniej
  * `keyVersion`: bieżąca wersja klucza. Można to znaleźć, klikając sam klucz (w sekcji "Klucze" w sekcji Ustawienia zasobu magazynu kluczy)
* W obszarze właściwości grupy kontenerów `sku` dodaj `Standard`właściwość o wartości . Właściwość `sku` jest wymagana w wersji interfejsu API 2019-12-01.

Poniższy fragment kodu szablonu zawiera te dodatkowe właściwości do szyfrowania danych wdrożenia:

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "encryptionProperties": {
                "vaultBaseUrl": "https://example.vault.azure.net",
                "keyName": "acikey",
                "keyVersion": "xxxxxxxxxxxxxxxx"
            },
            "sku": "Standard",
            "containers": {
                [...]
            }
        }
    }
]
```

Poniżej znajduje się kompletny szablon, dostosowany do szablonu w [samouczku: Wdrażanie grupy wielu kontenerów przy użyciu szablonu Menedżera zasobów](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group). 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "encryptionProperties": {
            "vaultBaseUrl": "https://example.vault.azure.net",
            "keyName": "acikey",
            "keyVersion": "xxxxxxxxxxxxxxxx"
        },
        "sku": "Standard",  
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

### <a name="deploy-your-resources"></a>Wdrażanie zasobów

Jeśli plik szablonu został utworzony i edytowany na pulpicie, można go przekazać do katalogu Cloud Shell, przeciągając do niego plik. 

Utwórz grupę zasobów za pomocą polecenia [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Wdrażanie szablonu za pomocą polecenia [tworzenie wdrażania grupy AZ.][az-group-deployment-create]

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

W ciągu kilku sekund powinna pojawić się początkowa odpowiedź z platformy Azure. Po zakończeniu wdrażania wszystkie dane z nim związane utrwalone przez usługę ACI będą szyfrowane przy pomocą podanego klucza.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
