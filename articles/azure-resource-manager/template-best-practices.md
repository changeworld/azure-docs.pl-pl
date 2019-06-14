---
title: Najlepsze rozwiązania dotyczące szablonów usługi Azure Resource Manager
description: W tym artykule opisano zalecane podejścia do tworzenia szablonów usługi Azure Resource Manager. Oferuje sugestie, aby uniknąć typowych problemów, korzystając z szablonów.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2019
ms.author: tomfitz
ms.openlocfilehash: bcc529b02505359e6e4e320d4991a082797c5261
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60389580"
---
# <a name="azure-resource-manager-template-best-practices"></a>Usługa Azure Resource Manager szablon najlepszych rozwiązań

Ten artykuł zawiera zalecenia dotyczące sposobu tworzenia szablonu usługi Resource Manager. Te zalecenia pozwalają pomóc w uniknięciu typowe problemy, gdy za pomocą szablonu, aby wdrożyć rozwiązanie.

Aby uzyskać zalecenia dotyczące sposobu zarządzania subskrypcjami platformy Azure, zobacz [szkieletu przedsiębiorstw na platformie Azure: Narzucony nadzór subskrypcji](/azure/architecture/cloud-adoption/appendix/azure-scaffold?toc=%2Fen-us%2Fazure%2Fazure-resource-manager%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json).

Aby uzyskać zalecenia dotyczące sposobu tworzenia szablonów, które działają we wszystkich środowiskach w chmurze platformy Azure, zobacz [szablony Tworzenie usługi Azure Resource Manager w celu zachowania spójności chmury](templates-cloud-consistency.md).

## <a name="template-limits"></a>Limity szablonu

Limit rozmiaru szablonu do 1 MB, a każdy plik parametrów do 64 KB. Aby stan końcowy szablonu obowiązuje limit 1 MB, po została rozszerzona o definicjach iteracyjne zasobów i wartości dla zmiennych i parametrów. 

Możesz również są ograniczone do:

* Parametry 256
* zmienne 256
* 800 zasoby (w tym liczba kopii)
* wartości wyjściowe 64
* 24 576 znaków w wyrażeniu szablonu

Pewne ograniczenia szablonu może przekroczyć przy użyciu zagnieżdżonych szablonów. Aby uzyskać więcej informacji, zobacz [podczas wdrażania zasobów platformy Azure, za pomocą połączonymi szablonami](resource-group-linked-templates.md). Aby zmniejszyć liczbę parametrów, zmienne ani danych wyjściowych, możesz połączyć kilka wartości do obiektu. Aby uzyskać więcej informacji, zobacz [obiektów jako parametrów](resource-manager-objects-as-parameters.md).

## <a name="resource-group"></a>Grupa zasobów

Podczas wdrażania zasobów w grupie zasobów, grupa zasobów przechowuje metadane dotyczące zasobów. Metadane są przechowywane w lokalizacji grupy zasobów.

Jeśli region grupy zasobów jest tymczasowo niedostępny, nie można zaktualizować zasobów w grupie zasobów, ponieważ metadane są niedostępne. Zasoby w innych regionach będą nadal działać zgodnie z oczekiwaniami, ale nie można zaktualizować. Aby zminimalizować ryzyko, Znajdź swoją grupę zasobów i zasobów, w tym samym regionie.

## <a name="parameters"></a>Parametry
Informacje przedstawione w tej sekcji mogą być przydatne podczas pracy z [parametry](resource-group-authoring-templates.md#parameters).

### <a name="general-recommendations-for-parameters"></a>Ogólne zalecenia dotyczące parametrów

* Minimalizuj korzystanie z parametrów. Zamiast tego należy użyć zmiennych lub wartości literału dla właściwości, których nie trzeba określać podczas wdrażania.

* Użyj formatu camelcase dla nazw parametrów.

* Użyj parametrów ustawień, które zależą od środowiska, takich jak jednostki SKU, rozmiar lub pojemności.

* Użyj parametrów dla nazwy zasobów, które chcesz określić ułatwiający identyfikację.

* Podaj opis każdego parametru w metadanych:

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* Definiowanie wartości domyślnych dla parametrów, które nie są poufne. Określając wartość domyślną, jest łatwiejsze do wdrożenia szablonu, a użytkownikom szablonu, zobacz przykład odpowiednią wartość. Żadnej wartości domyślnej dla parametru musi być prawidłowe dla wszystkich użytkowników w domyślnej konfiguracji wdrożenia. 
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* Aby określić opcjonalny parametr, nie używaj pustego ciągu jako wartość domyślna. Zamiast tego należy użyć do utworzenia wartości wartość literału lub wyrażenia języka.

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   },
   ```

* Nie używaj parametru dla wersji interfejsu API dla typu zasobu. Właściwości zasobów i wartości mogą się różnić numer wersji. IntelliSense w edytorze kodu nie można ustalić poprawnego schematu, gdy parametr ma wartość wersja interfejsu API. Zamiast tego należy trwale kodować interfejsu API w wersji w szablonie.

* Użyj `allowedValues` rzadko. Go użyć tylko wtedy, gdy należy się upewnić, że niektóre wartości nie są uwzględniane w opcji dozwolone. Jeśli używasz `allowedValues` zbyt szeroko prawidłowe wdrożenia może spowodować zablokowanie, nie aktualizowanie Twojej listy.

* Gdy nazwa parametru w szablonie jest zgodna z parametrem w poleceniu wdrożenia programu PowerShell, Menedżer zasobów jest rozpoznawany jako ten konflikt nazw, dodając przyrostkowa **FromTemplate** z parametrem szablonu. Na przykład, jeśli zawierają parametr o nazwie **ResourceGroupName** w szablonie, powoduje on konflikt z **ResourceGroupName** parametru w [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) polecenia cmdlet. Podczas wdrażania, zostanie wyświetlony monit podać wartości **ResourceGroupNameFromTemplate**.

### <a name="security-recommendations-for-parameters"></a>Zalecenia dotyczące zabezpieczeń dla parametrów

* Zawsze należy używać parametrów dla nazwy użytkownika i hasła (lub kluczy tajnych).

* Użyj `securestring` dla wszystkich haseł i kluczy tajnych. Jeśli przekażesz poufnych danych w obiekcie JSON, użyj `secureObject` typu. Nie można odczytać parametrów szablonu z bezpiecznym ciągiem lub bezpiecznego wybierane po wdrożeniu zasobów. 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* Nie udostępni wartości domyślne dla nazwy użytkownika, hasła lub dowolną wartość, która wymaga `secureString` typu.

* Nie można podać wartości domyślne dla właściwości, które zwiększają obszar powierzchni ataku w aplikacji.

### <a name="location-recommendations-for-parameters"></a>Lokalizacja zalecenia dotyczące parametrów

* Użyj parametru, aby określić lokalizację dla zasobów, a wartość domyślna równa `resourceGroup().location`. Podanie parametru lokalizacji umożliwia użytkownikom szablonu lokalizację, do których mają uprawnienia do wdrożenia.

   ```json
   "parameters": {
     "location": {
       "type": "string",
       "defaultValue": "[resourceGroup().location]",
       "metadata": {
         "description": "The location in which the resources should be deployed."
       }
     }
   },
   ```

* Nie określaj `allowedValues` parametru lokalizacji. Lokalizacje, które określisz może nie być dostępne we wszystkich chmur.

* Wartość parametru lokalizacji na użytek zasoby, które mogą być w tej samej lokalizacji. To podejście minimalizuje liczbę przypadków, gdy użytkownicy są proszeni o dostarczenie informacji o lokalizacji.

* Dla zasobów, które nie są dostępne we wszystkich lokalizacjach należy użyć oddzielnych parametru lub określić wartość literału lokalizacji.

## <a name="variables"></a>Zmienne

Poniższe informacje mogą być przydatne podczas pracy z [zmienne](resource-group-authoring-templates.md#variables):

* Używać zmiennych dla wartości, które trzeba użyć więcej niż raz w szablonie. Jeśli wartość jest używana tylko raz, ustaloną wartość sprawia, że szablon łatwiejsza do odczytania.

* Używać zmiennych dla wartości, które konstruowania złożonych rozmieszczenie funkcji szablonu. Szablon jest łatwiejsza do odczytania, kiedy wyrażenie złożone pojawia się tylko w zmiennych.

* Nie należy używać zmiennych dotyczących `apiVersion` w zasobie. Wersja interfejsu API Określa schemat zasobu. Często nie można zmienić wersji bez zmiany właściwości zasobu.

* Nie można użyć [odwołania](resource-group-template-functions-resource.md#reference) działa w programach **zmienne** części szablonu. **Odwołania** funkcja dziedziczy swoją wartość od zasobu stan czasu wykonywania. Jednakże zmienne są rozwiązywane podczas początkowego analizowania szablonu. Konstrukcja wartości wymagające **odwołania** bezpośrednio w funkcji **zasobów** lub **generuje** części szablonu.

* Zawierają zmienne dla nazwy zasobów, które muszą być unikatowe.

* Użyj [pętlę kopiowania w zmiennych](resource-group-create-multiple.md#variable-iteration) utworzyć powtarzanych wzorzec obiektów JSON.

* Usuń nieużywane zmienne.

## <a name="resource-dependencies"></a>Zależności zasobów

Podejmując decyzję co [zależności](resource-group-define-dependencies.md) można ustawić, skorzystaj z poniższych wskazówek:

* Użyj **odwołania** działać, a następnie przekaż nazwę zasobu, można ustawić niejawne zależności między zasobami, które muszą udostępniać właściwość. Nie dodawaj jawnego `dependsOn` elementu, gdy już zdefiniowano niejawne zależności. Takie podejście zmniejsza ryzyko wystąpienia niepotrzebne zależności.

* Ustawić zasobu podrzędnego jako zależny od jej zasobu nadrzędnego.

* Zasoby z [element warunek](resource-group-authoring-templates.md#condition) ma wartość false, są automatycznie usuwane z kolejności wg zależności. Zależności należy ustawić tak, jakby zawsze wdrożono zasób.

* Pozwalają zastosować kaskadowo bez konieczności ustawiania ich jawnie zależności. Na przykład maszyna wirtualna jest zależny od interfejsu sieci wirtualnej, a interfejs sieci wirtualnej zależy od sieci wirtualnej i publicznych adresów IP. W związku z tym maszyna wirtualna jest wdrożone zasoby po wszystkich trzech, ale nie jest jawnie ustawiana maszyny wirtualnej jako zależny od wszystkich trzech zasobów. To podejście wyjaśnia kolejność zależności oraz ułatwia później zmienić szablonu.

* Przed wdrożeniem, można określić wartość, spróbuj wykonać wdrażanie zasobu bez zależności. Na przykład jeśli wartość konfiguracji wymaga innego zasobu, może być konieczne nie zależności. Te wskazówki nie zawsze działa, ponieważ niektóre zasoby sprawdzenia istnienia innego zasobu. Jeśli otrzymasz komunikat o błędzie, należy dodać zależność.

## <a name="resources"></a>Zasoby

Poniższe informacje mogą być przydatne podczas pracy z [zasobów](resource-group-authoring-templates.md#resources):

* Aby ułatwić innymi współautorami zrozumienie przeznaczenia zasobu, określić **komentarze** dla każdego zasobu w szablonie:
   
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

* Jeśli używasz *publicznym punktem końcowym* w szablonie (np. usługi Azure Blob storage publicznego punktu końcowego), *nie trwale kodować* przestrzeni nazw. Użyj **odwołania** funkcję, aby dynamicznie pobrać przestrzeni nazw. Tej metody można użyć do wdrożenia szablonu w innej przestrzeni nazw publicznych środowiskach bez ręcznej zmiany punktu końcowego w szablonie. Ustaw wersję interfejsu API do tej samej wersji, którego używasz dla konta magazynu w szablonie:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Jeśli konto magazynu jest wdrażana w tym samym szablonie, który tworzysz, nie trzeba określić przestrzeń nazw dostawcy w przypadku, gdy odwoływać się do zasobu. Poniższy przykład przedstawia uproszczoną składnię:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Jeśli masz inne wartości w szablonie, które są skonfigurowane do korzystania z publicznej przestrzeni nazw, Zmień te wartości, aby odzwierciedlić takie same **odwołania** funkcji. Na przykład można ustawić **storageUri** właściwości profilu diagnostyki maszyny wirtualnej:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   Możesz też przywołać istniejące konto magazynu, który znajduje się w innej grupie zasobów:

   ```json
   "osDisk": {
       "name": "osdisk", 
       "vhd": {
           "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
       }
   }
   ```

* Publiczne adresy IP można przypisywać do maszyny wirtualnej, tylko wtedy, gdy aplikacja wymaga go. Aby połączyć się z maszyną wirtualną (VM) podczas debugowania lub zarządzania lub do celów administracyjnych, użyj reguły NAT dla ruchu przychodzącego, bramy sieci wirtualnej lub serwera przesiadkowego.
   
     Aby uzyskać więcej informacji na temat nawiązywania połączenia z maszynami wirtualnymi zobacz:
   
   * [Uruchamianie maszyn wirtualnych dla architektury wielowarstwowej na platformie Azure](../guidance/guidance-compute-n-tier-vm.md)
   * [Konfigurowanie dostępu WinRM dla maszyn wirtualnych w usłudze Azure Resource Manager](../virtual-machines/windows/winrm.md)
   * [Zezwalaj na dostęp zewnętrzny do maszyny Wirtualnej przy użyciu witryny Azure portal](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Zezwalaj na dostęp zewnętrzny do maszyny Wirtualnej przy użyciu programu PowerShell](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Zezwalaj na dostęp zewnętrzny do maszyny Wirtualnej systemu Linux przy użyciu wiersza polecenia platformy Azure](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)

* **DomainNameLabel** właściwości publiczne adresy IP muszą być unikatowe. **DomainNameLabel** wartość musi mieć długość od 3 do 63 znaków i postępuj zgodnie z regułami określonymi przez tego wyrażenia regularnego: `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`. Ponieważ **uniqueString** funkcja generuje ciąg, który jest 13 znaków **dnsPrefixString** parametru jest ograniczona do 50 znaków:

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

* Po dodaniu hasła do rozszerzenia niestandardowego skryptu, użyj **commandToExecute** właściwość **protectedSettings** właściwości:
   
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
   > Aby upewnić się, że klucze tajne są szyfrowane, gdy są przekazywane jako parametry do maszyn wirtualnych i rozszerzeń, należy użyć **protectedSettings** właściwość odpowiednich rozszerzeń.
   > 
   > 

## <a name="outputs"></a>Dane wyjściowe

Jeśli używasz szablonu, aby utworzyć publiczne adresy IP, Uwzględnij [generuje sekcji](resource-group-authoring-templates.md#outputs) zwracającego szczegółowe informacje o adresie IP i w pełni kwalifikowana nazwa domeny (FQDN). Można użyć wartości danych wyjściowych można łatwo pobrać szczegółowe informacje dotyczące publicznych adresów IP i nazw FQDN po wdrożeniu.

```json
"outputs": {
    "fqdn": {
        "value": "[reference(parameters('publicIPAddresses_name')).dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]",
        "type": "string"
    }
}
```

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać informacje o strukturze pliku szablonu usługi Resource Manager, zobacz [Omówienie struktury i składni szablonów usługi Azure Resource Manager](resource-group-authoring-templates.md).
* Aby uzyskać zalecenia dotyczące sposobu tworzenia szablonów, które działają we wszystkich środowiskach w chmurze platformy Azure, zobacz [szablony Tworzenie usługi Azure Resource Manager w celu zachowania spójności chmury](templates-cloud-consistency.md).
