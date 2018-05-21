---
title: Zasobów szablonu usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: W tym artykule opisano sekcji Zasoby szablonów usługi Azure Resource Manager za pomocą składni deklaratywnej JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2017
ms.author: tomfitz
ms.openlocfilehash: 12dc5921cc1977b53f0457d89537193eadded188
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="resources-section-of-azure-resource-manager-templates"></a>Sekcja zasobów szablonów usługi Azure Resource Manager

W sekcji zasobów można zdefiniować zasoby, które są wdrożone lub aktualizowane. W tej sekcji można uzyskać skomplikowane, ponieważ należy zrozumieć typy, które jest wdrażany w celu zapewnienia właściwych wartości.

## <a name="available-properties"></a>Dostępne właściwości

Można zdefiniować zasoby o następującej strukturze:

```json
"resources": [
  {
      "condition": "<boolean-value-whether-to-deploy>",
      "apiVersion": "<api-version-of-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "name": "<name-of-the-resource>",
      "location": "<location-of-resource>",
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "comments": "<your-reference-notes>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Nazwa elementu | Wymagane | Opis |
|:--- |:--- |:--- |
| warunek | Nie | Wartość logiczna wskazująca, czy zasób jest wdrażany. |
| apiVersion |Yes |Wersja interfejsu API REST do użycia podczas tworzenia zasobu. |
| type |Yes |Typ zasobu. Ta wartość jest kombinacją przestrzeń nazw dostawcy zasobów i typu zasobu (takich jak **magazyn.Microsoft/kontamagazynu**). |
| name |Yes |Nazwa zasobu. Nazwa musi występować po zdefiniowane w RFC3986 ograniczenia składnika identyfikatora URI. Ponadto usług platformy Azure, które udostępniają poza strony zweryfikować nazwę, aby zapewnić, że nazwa zasobu nie jest próba podszywają się pod innego tożsamości. |
| location |Zmienia się |Obsługiwane lokalizacje geograficzne podane zasobu. Można wybrać dowolny z dostępnych lokalizacji, ale zazwyczaj warto wybrać, który znajduje się w pobliżu użytkowników. Zazwyczaj również dobrym rozwiązaniem jest umieszczenie zasoby, które współdziałają ze sobą w tym samym regionie. Większość typów zasobów wymaga lokalizacji, ale nie wymagają lokalizację niektórych typów (takich jak przypisanie roli). |
| tags |Nie |Tagi, które są skojarzone z zasobem. Zastosuj znaczniki, aby organizację zasobów w Twojej subskrypcji. |
| Komentarze |Nie |Notatki za dokumentację zasobów w szablonie |
| kopiuj |Nie |Jeśli wymagane jest więcej niż jedno wystąpienie, liczba zasobów do utworzenia. Domyślnym trybem jest równoległe. Określ tryb serial gdy nie ma wszystkich lub zasoby w celu wdrożenia w tym samym czasie. Aby uzyskać więcej informacji, zobacz [utworzyć wiele wystąpień zasobów usługi Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |Nie |Zasoby, które należy wdrożyć przed wdrożeniem tego zasobu. Menedżer zasobów ocenia zależności między zasobami i wdraża je w odpowiedniej kolejności. Zasoby nie są zależne od siebie, są wdrożone równolegle. Wartość może być rozdzielaną przecinkami listą zasobu nazwy lub unikatowych identyfikatorów zasobów. Tylko listy zasobów, które są wdrażane w tym szablonie. Zasoby, które nie są zdefiniowane w tym szablonie musi już istnieć. Unikaj Dodawanie zależności niepotrzebne, jak mogą spowalniać wdrożenia i utworzyć zależności cykliczne. Aby uzyskać wskazówki dotyczące zależności ustawienia, zobacz [Definiowanie zależności w szablonach usługi Azure Resource Manager](resource-group-define-dependencies.md). |
| properties |Nie |Ustawienia konfiguracji określonych zasobów. Wartości właściwości są takie same jak wartości podane w treści żądania dla operacji interfejsu API REST (metody PUT) do utworzenia zasobu. Można również określić tablicy kopiowania, aby utworzyć wiele wystąpień właściwości. |
| sku | Nie | Niektóre zasoby Zezwalaj wartości, które definiują SKU do wdrożenia. Na przykład można określić typ nadmiarowości dla konta magazynu. |
| rodzaj | Nie | Niektóre zasoby Zezwalaj wartość definiującą typ zasobu, które można wdrożyć. Na przykład można określić typ DB rozwiązania Cosmos do utworzenia. |
| plan | Nie | Zezwalaj na wartości, które definiują planujesz jej wdrożenie niektórych zasobów. Na przykład można określić obrazu witryny marketplace dla maszyny wirtualnej. | 
| zasoby |Nie |Zasoby podrzędne, które zależą od zasobu został określony. Podaj tylko typy zasobów, które są dozwolone w schemacie zasobu nadrzędnego. Pełny typ zasobu podrzędnych obejmuje nadrzędny typ zasobu, takich jak **Microsoft.Web/sites/extensions**. Zależność od zasobu nadrzędnego nie jest oznaczany. Jawnie zdefiniuj tej zależności. |

## <a name="resource-specific-values"></a>Wartości zasobów

**ApiVersion**, **typu**, i **właściwości** elementy są różne dla każdego typu zasobu. **Sku**, **rodzaj**, i **planu** elementy są dostępne dla niektórych typów zasobów, ale nie wszystkich. Aby określić wartości tych właściwości, zobacz [odwołania do szablonu](/azure/templates/).

## <a name="resource-names"></a>Nazwy zasobów
Ogólnie rzecz biorąc pracować z trzech rodzajów nazw zasobów w Menedżerze zasobów:

* Nazwy zasobów, które muszą być unikatowe.
* Nazwy zasobów, które nie muszą być unikatowe, ale zdecydować się na Podaj nazwę, która może pomóc w identyfikacji zasobu.
* Nazwy zasobów, które mogą być ogólne.

### <a name="unique-resource-names"></a>Nazwy zasobów unikatowy
Musisz podać nazwę zasobu unikatowy dla dowolnego typu zasobu, która zawiera punkt końcowy dostępu do danych. Niektóre typowe typy zasobów, które wymagają unikatową nazwę obejmują:

* Azure Storage<sup>1</sup> 
* Funkcje aplikacji internetowych w usłudze Azure App Service
* Oprogramowanie SQL Server
* W usłudze Azure Key Vault
* Azure Redis Cache
* Azure Batch
* Azure Traffic Manager
* Azure Search
* Azure HDInsight

<sup>1</sup> nazwy konta magazynu musi być także małe litery, 24 znaków lub mniej, a nie ma żadnych łączników.

Podczas ustawiania nazwy, można ręcznie utworzyć unikatowej nazwy lub użyj [uniqueString()](resource-group-template-functions-string.md#uniquestring) funkcji, aby wygenerować nazwy. Można również dodać prefiksu lub sufiksu domeny do **uniqueString** wynik. Modyfikowanie unikatową nazwę może pomóc więcej łatwo zidentyfikować z nazwy typu zasobu. Na przykład można wygenerować unikatowej nazwy dla konta magazynu przy użyciu następującą zmienną:

```json
"variables": {
    "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

### <a name="resource-names-for-identification"></a>Nazwy zasobów do identyfikacji
Niektóre typy zasobów, które mają nazwy, ale ich nazwy nie muszą być unikatowe. W przypadku tych typów zasobów można Podaj nazwę, która identyfikuje kontekst zasobów, jak i typ zasobu.

```json
"parameters": {
    "vmName": { 
        "type": "string",
        "defaultValue": "demoLinuxVM",
        "metadata": {
            "description": "The name of the VM to create."
        }
    }
}
```

### <a name="generic-resource-names"></a>Nazwy zasobów ogólnych
Dla typów zasobów, które przede wszystkim uzyskują dostęp za pomocą innego zasobu można użyć nazwy ogólnej, który jest ustalony w szablonie. Na przykład można ustawić nazwę standardowego, ogólne reguły zapory na serwerze SQL server:

```json
{
    "type": "firewallrules",
    "name": "AllowAllWindowsAzureIps",
    ...
}
```

## <a name="location"></a>Lokalizacja
W przypadku wdrażania szablonu, należy podać lokalizację każdego zasobu. Różne typy zasobów są obsługiwane w różnych lokalizacjach. Aby wyświetlić listę lokalizacje, które są dostępne dla subskrypcji dla określonego typu zasobu, użyj programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. 

W poniższym przykładzie użyto programu PowerShell, aby pobrać lokalizacji dla `Microsoft.Web\sites` typu zasobu:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

W poniższym przykładzie użyto 2.0 interfejsu wiersza polecenia platformy Azure można pobrać lokalizacji dla `Microsoft.Web\sites` typu zasobu:

```azurecli
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

Po ustaleniu obsługiwane lokalizacje dla zasobów, należy ustawić tę lokalizację w szablonie. Najprostszym sposobem ta wartość jest utworzenie grupy zasobów w lokalizacji, która obsługuje typy zasobów i ustawioną każdej lokalizacji `[resourceGroup().location]`. Można ponownie wdrożyć szablon do grup zasobów w różnych lokalizacjach i nie zmieniać wartości w szablonie lub parametrów. 

W poniższym przykładzie przedstawiono konta magazynu, który jest wdrożony w tej samej lokalizacji co grupa zasobów:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
      "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

Jeśli potrzebujesz kodowania lokalizację w szablonie, podaj nazwę jednego z obsługiwanych regionów. W poniższym przykładzie przedstawiono konta magazynu, który jest zawsze wdrożony w północno-środkowe stany:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storageloc', uniqueString(resourceGroup().id))]",
      "location": "North Central US",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

## <a name="tags"></a>Tagi
[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

### <a name="add-tags-to-your-template"></a>Dodawanie tagów do szablonu

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="child-resources"></a>Zasoby podrzędne

W niektórych typów zasobów można również zdefiniować tablicę zasoby podrzędne. Zasoby podrzędne są zasoby, które istnieją tylko w kontekście innego zasobu. Na przykład bazy danych SQL nie może istnieć bez programu SQL server, bazy danych jest elementem podrzędnym serwera. Można zdefiniować w ramach definicji dla serwera bazy danych.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  ...
  "resources": [
    {
      "name": "exampledatabase",
      "type": "databases",
      "apiVersion": "2014-04-01",
      ...
    }
  ]
}
```

Gdy zagnieżdżony, ma ustawioną wartość typu `databases` , ale jego typ zasobu pełne jest `Microsoft.Sql/servers/databases`. Nie podawaj `Microsoft.Sql/servers/` ponieważ zakłada się z nadrzędnego typu zasobu. Ustawiono nazwę zasobu podrzędnego `exampledatabase` , ale imię i nazwisko zawiera nazwę nadrzędnej. Nie podawaj `exampleserver` ponieważ zakłada się od zasobu nadrzędnego.

Format podrzędny typ zasobu jest: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`

Format podrzędny Nazwa zasobu jest: `{parent-resource-name}/{child-resource-name}`

Jednak nie trzeba definiować bazy danych na serwerze. Można zdefiniować zasobu podrzędnego na najwyższym poziomie. Tej metody może użyć, jeśli zasobu nadrzędnego nie została wdrożona w tym samym szablonie, lub jeśli chcesz użyć `copy` utworzyć wiele podrzędnych zasobów. Z tej metody należy udostępniają typ zasobu pełne i nazwy zasobu podrzędne zawierają nazwę zasobu nadrzędnego.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  "resources": [ 
  ],
  ...
},
{
  "name": "exampleserver/exampledatabase",
  "type": "Microsoft.Sql/servers/databases",
  "apiVersion": "2014-04-01",
  ...
}
```

Podczas konstruowania pełni kwalifikowane odwołanie do zasobu, aby połączyć segmenty z typem i nazwą nie jest po prostu składa się z dwóch.  Po obszarze nazw, użyj sekwencji *typ/nazwa* pary z najmniej specyficznych, które specyficzny:

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

Na przykład:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` jest poprawna `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` jest nieprawidłowy

## <a name="recommendations"></a>Zalecenia
Poniższe informacje mogą być przydatne podczas pracy z zasobami:

* Aby ułatwić innych uczestników zrozumienie przeznaczenia zasobu, należy określić **komentarze** dla każdego zasobu w szablonie:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* Jeśli używasz *publiczny punkt końcowy* w szablonie (np. Azure Blob magazynu publiczny punkt końcowy), *czy nie kodowane* przestrzeni nazw. Użyj **odwołania** funkcji można dynamicznie pobrać przestrzeni nazw. Tej metody można użyć do wdrożenia szablonu w środowiskach różnych publicznej przestrzeni nazw ręcznie zmieniając punktu końcowego w szablonie. Ustaw wersję interfejsu API do tej samej wersji, którego używasz konta magazynu w szablonie:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Jeśli konto magazynu jest wdrażana w tym samym szablonie, który tworzysz, nie należy określić przestrzeń nazw dostawcy podczas odwoływać się do zasobu. W poniższym przykładzie pokazano uproszczony składni:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Jeśli masz inne wartości w szablonie, które są skonfigurowane do korzystania z publicznej przestrzeni nazw, zmiany tych wartości, aby odzwierciedlić takie same **odwołania** funkcji. Na przykład można ustawić **storageUri** właściwości profilu diagnostyki maszyny wirtualnej:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   Możesz także odwoływać się do istniejącego konta magazynu, który znajduje się w innej grupie zasobów:

   ```json
   "osDisk": {
       "name": "osdisk", 
       "vhd": {
           "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
       }
   }
   ```

* Przypisz publiczne adresy IP do maszyny wirtualnej, tylko wtedy, gdy aplikacja wymaga on. Aby połączyć się z maszyną wirtualną (VM) do debugowania lub zarządzania lub celów administracyjnych, użyj reguły NAT ruchu przychodzącego, bramy sieci wirtualnej lub jumpbox.
   
     Aby uzyskać więcej informacji na temat podłączania do maszyn wirtualnych zobacz:
   
   * [Uruchamianie maszyn wirtualnych dla architektury N-warstwowa na platformie Azure](../guidance/guidance-compute-n-tier-vm.md)
   * [Konfigurowanie dostępu do usługi WinRM dla maszyn wirtualnych w usłudze Azure Resource Manager](../virtual-machines/windows/winrm.md)
   * [Dostęp do zewnętrznych do maszyny Wirtualnej za pomocą portalu Azure](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Dostęp do zewnętrznych do maszyny Wirtualnej za pomocą programu PowerShell](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Dostęp do zewnętrznych sieci maszyny Wirtualnej systemu Linux przy użyciu wiersza polecenia platformy Azure](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)
* **DomainNameLabel** właściwości dla publicznych adresów IP muszą być unikatowe. **DomainNameLabel** wartość musi mieć długość od 3 do 63 znaków i postępuj zgodnie z regułami określonymi przez tego wyrażenia regularnego: `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`. Ponieważ **uniqueString** funkcja generuje ciąg, który jest 13 znaków **dnsPrefixString** parametr jest ograniczone do 50 znaków:

   ```json
   "parameters": {
       "dnsPrefixString": {
           "type": "string",
           "maxLength": 50,
           "metadata": {
               "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
           }
       }
   },
   "variables": {
       "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
   }
   ```

* Gdy hasło jest dodawanie do rozszerzenia niestandardowego skryptu, użyj **commandToExecute** właściwości w **protectedSettings** właściwości:
   
   ```json
   "properties": {
       "publisher": "Microsoft.Azure.Extensions",
       "type": "CustomScript",
       "typeHandlerVersion": "2.0",
       "autoUpgradeMinorVersion": true,
       "settings": {
           "fileUris": [
               "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
           ]
       },
       "protectedSettings": {
           "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
       }
   }
   ```
   
   > [!NOTE]
   > Aby upewnić się, że hasła są szyfrowane, gdy są one przekazywane jako parametry do maszyn wirtualnych i rozszerzenia, należy użyć **protectedSettings** właściwości odpowiednich rozszerzeń.
   > 
   > 


## <a name="next-steps"></a>Kolejne kroki
* Aby wyświetlić pełną listę szablonów dla wielu różnych rozwiązań, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/).
* Aby uzyskać więcej informacji o funkcje, których można użyć z w ramach szablonu, zobacz [funkcje szablonów usługi Azure Resource Manager](resource-group-template-functions.md).
* Aby połączyć wiele szablonów podczas wdrażania, zobacz [za pomocą szablonów połączonych z usługą Azure Resource Manager](resource-group-linked-templates.md).
* Może być konieczne użycie zasobów, które istnieją w innej grupie zasobów. Ten scenariusz jest typowy podczas pracy z kontami magazynu lub sieci wirtualne, które są współdzielone przez wiele grup zasobów. Aby uzyskać więcej informacji, zobacz [funkcja resourceId](resource-group-template-functions-resource.md#resourceid).
* Aby uzyskać informacje o ograniczeniach nazw zasobów, zobacz [konwencje nazewnictwa dla zasobów platformy Azure zalecane](../guidance/guidance-naming-conventions.md).