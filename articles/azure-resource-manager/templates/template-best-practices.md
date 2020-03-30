---
title: Najlepsze rozwiązania dotyczące szablonów
description: W tym artykule opisano zalecane podejścia do tworzenia szablonów usługi Azure Resource Manager. Oferuje sugestie, aby uniknąć typowych problemów podczas korzystania z szablonów.
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 870636d6457d842c89f261c2537644c17a335294
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156416"
---
# <a name="arm-template-best-practices"></a>Najlepsze rozwiązania dotyczące szablonów ARM

W tym artykule przedstawiono zalecenia dotyczące sposobu konstruowania szablonu usługi Azure Resource Manager (ARM). Te zalecenia pomagają uniknąć typowych problemów podczas wdrażania rozwiązania przy użyciu szablonu ARM.

Aby uzyskać zalecenia dotyczące sposobu zarządzania subskrypcjami platformy Azure, zobacz [szkielet środowiska Azure enterprise: Nakazowe zarządzanie subskrypcjami.](/azure/architecture/cloud-adoption/appendix/azure-scaffold?toc=%2Fen-us%2Fazure%2Fazure-resource-manager%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

Aby uzyskać zalecenia dotyczące tworzenia szablonów, które działają we wszystkich środowiskach chmury platformy Azure, zobacz [Tworzenie szablonów usługi Azure Resource Manager dla spójności chmury.](templates-cloud-consistency.md)

## <a name="template-limits"></a>Limity szablonów

Ogranicz rozmiar szablonu do 4 MB, a każdy plik parametrów do 64 KB. Limit 4 MB ma zastosowanie do ostatecznego stanu szablonu po jego rozwinięciu o iteracyjnych definicjach zasobów oraz wartościach zmiennych i parametrów. 

Ograniczasz się również do:

* 256 parametrów
* 256 zmiennych
* 800 zasobów (w tym liczba kopii)
* 64 wartości wyjściowe
* 24 576 znaków w wyrażeniu szablonu

Niektóre limity szablonów można przekroczyć przy użyciu szablonu zagnieżdżonego. Aby uzyskać więcej informacji, zobacz [Używanie połączonych szablonów podczas wdrażania zasobów platformy Azure](linked-templates.md). Aby zmniejszyć liczbę parametrów, zmiennych lub wyjść, można połączyć kilka wartości w obiekt. Aby uzyskać więcej informacji, zobacz [Obiekty jako parametry](/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

## <a name="resource-group"></a>Grupa zasobów

Podczas wdrażania zasobów w grupie zasobów grupa zasobów przechowuje metadane dotyczące zasobów. Metadane są przechowywane w lokalizacji grupy zasobów.

Jeśli region grupy zasobów jest tymczasowo niedostępny, nie można zaktualizować zasobów w grupie zasobów, ponieważ metadane są niedostępne. Zasoby w innych regionach będą nadal działać zgodnie z oczekiwaniami, ale nie można ich zaktualizować. Aby zminimalizować ryzyko, zlokalizuj grupę zasobów i zasoby w tym samym regionie.

## <a name="parameters"></a>Parametry

Informacje zawarte w tej sekcji mogą być przydatne podczas pracy z [parametrami](template-parameters.md).

### <a name="general-recommendations-for-parameters"></a>Ogólne zalecenia dotyczące parametrów

* Zminimalizuj użycie parametrów. Zamiast tego należy użyć zmiennych lub wartości literału dla właściwości, które nie muszą być określone podczas wdrażania.

* Użyj przypadku wielbłąda dla nazw parametrów.

* Użyj parametrów dla ustawień, które różnią się w zależności od środowiska, takich jak jednostka SKU, rozmiar lub pojemność.

* Użyj parametrów dla nazw zasobów, które chcesz określić dla łatwej identyfikacji.

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

* Zdefiniuj wartości domyślne dla parametrów, które nie są poufne. Określając wartość domyślną, łatwiej jest wdrożyć szablon, a użytkownicy szablonu zobaczą przykład odpowiedniej wartości. Dowolna wartość domyślna parametru musi być prawidłowa dla wszystkich użytkowników w domyślnej konfiguracji wdrażania. 
   
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

* Aby określić parametr opcjonalny, nie należy używać pustego ciągu jako wartości domyślnej. Zamiast tego należy użyć wartości literału lub wyrażenia języka do konstruowania wartości.

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   },
   ```

* Nie należy używać parametru dla wersji interfejsu API dla typu zasobu. Właściwości i wartości zasobów mogą się różnić w zależności od numeru wersji. IntelliSense w edytorze kodu nie można określić prawidłowy schemat, gdy wersja interfejsu API jest ustawiona na parametr. Zamiast tego zakodować wersję interfejsu API w szablonie.

* Używaj `allowedValues` oszczędnie. Używaj go tylko wtedy, gdy musisz upewnić się, że niektóre wartości nie są uwzględnione w dozwolonych opcjach. Jeśli używasz `allowedValues` zbyt szeroko, możesz zablokować prawidłowe wdrożenia, nie aktualizując listy.

* Gdy nazwa parametru w szablonie odpowiada parametrowi w poleceniu wdrażania programu PowerShell, Menedżer zasobów rozwiązuje ten konflikt nazewnictwa, dodając postfix **FromTemplate** do parametru szablonu. Na przykład jeśli w szablonie zostanie uwzględniony parametr o nazwie **ResourceGroupName,** powoduje on konflikt z **parametrem ResourceGroupName** w poleceniu cmdlet [New-AzResourceGroupDeployment.](/powershell/module/az.resources/new-azresourcegroupdeployment) Podczas wdrażania zostanie wyświetlony monit o podanie wartości **resourcegroupnameFromTemplate**.

### <a name="security-recommendations-for-parameters"></a>Zalecenia dotyczące zabezpieczeń dla parametrów

* Zawsze używaj parametrów nazw użytkowników i haseł (lub wpisów tajnych).

* Użyj `securestring` dla wszystkich haseł i wpisów tajnych. Jeśli przekażesz poufne dane w obiekcie JSON, użyj tego `secureObject` typu. Parametry szablonu z bezpiecznym ciągiem lub typami bezpiecznych obiektów nie mogą być odczytywane po wdrożeniu zasobów. 
   
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

* Nie podawaj wartości domyślnych nazw użytkowników, haseł ani `secureString` żadnej wartości, która wymaga typu.

* Nie podawaj wartości domyślnych dla właściwości, które zwiększają obszar ataku aplikacji.

### <a name="location-recommendations-for-parameters"></a>Zalecenia dotyczące lokalizacji parametrów

* Użyj parametru, aby określić lokalizację zasobów i `resourceGroup().location`ustawić wartość domyślną na . Podanie parametru lokalizacji umożliwia użytkownikom szablonu określenie lokalizacji, do których mają uprawnienia do wdrażania.

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

* Nie określaj `allowedValues` parametru lokalizacji. Określone lokalizacje mogą być niedostępne we wszystkich chmurach.

* Użyj wartości parametru lokalizacji dla zasobów, które mogą znajdować się w tej samej lokalizacji. Takie podejście minimalizuje liczbę użytkowników, którzy są proszeni o podanie informacji o lokalizacji.

* W przypadku zasobów, które nie są dostępne we wszystkich lokalizacjach, należy użyć oddzielnego parametru lub określić wartość dosłownej lokalizacji.

## <a name="variables"></a>Zmienne

Następujące informacje mogą być pomocne podczas pracy ze [zmiennymi:](template-variables.md)

* Użyj przypadku wielbłąda dla nazw zmiennych.

* Użyj zmiennych dla wartości, których należy używać więcej niż raz w szablonie. Jeśli wartość jest używana tylko raz, wartość zakodowane sprawia, że szablon jest łatwiejszy do odczytania.

* Zmienne należy używać dla wartości, które są konstruowania ze złożonego układu funkcji szablonu. Szablon jest łatwiejszy do odczytania, gdy wyrażenie złożone pojawia się tylko w zmiennych.

* Nie używaj zmiennych `apiVersion` dla zasobu. Wersja interfejsu API określa schemat zasobu. Często nie można zmienić wersji bez zmiany właściwości zasobu.

* Nie można użyć funkcji [odwołania](template-functions-resource.md#reference) w sekcji **zmiennych** szablonu. Funkcja **odwołania** wyprowadza swoją wartość ze stanu środowiska wykonawczego zasobu. Jednak zmienne są rozpoznawane podczas wstępnej analizy szablonu. Konstruuj wartości, które wymagają funkcji **odwołania** bezpośrednio w sekcji **zasobów** lub **wyjść** szablonu.

* Dołącz zmienne dla nazw zasobów, które muszą być unikatowe.

* Użyj [pętli kopiowania w zmiennych,](copy-variables.md) aby utworzyć powtarzający się wzorzec obiektów JSON.

* Usuń nieużywane zmienne.

## <a name="resource-dependencies"></a>Zależności zasobów

Przy podejmowaniu decyzji, jakie [zależności](define-resource-dependency.md) ustawić, należy użyć następujących wskazówek:

* Użyj funkcji **odwołania** i przekazać w nazwie zasobu, aby ustawić niejawną zależność między zasobami, które muszą współużytkować właściwość. Nie dodawaj jawnego `dependsOn` elementu, gdy zdefiniowano już niejawną zależność. Takie podejście zmniejsza ryzyko wystąpienia niepotrzebnych zależności.

* Ustaw zasób podrzędny jako zależny od jego zasobu nadrzędnego.

* Zasoby z [elementem warunku](conditional-resource-deployment.md) ustawionym na false są automatycznie usuwane z kolejności zależności. Ustaw zależności tak, jakby zasób był zawsze wdrażany.

* Niech zależności kaskadowo bez ustawiania ich jawnie. Na przykład maszyna wirtualna zależy od interfejsu sieci wirtualnej, a interfejs sieci wirtualnej zależy od sieci wirtualnej i publicznych adresów IP. W związku z tym maszyna wirtualna jest wdrażana po wszystkich trzech zasobów, ale nie jawnie ustawić maszynę wirtualną jako zależną od wszystkich trzech zasobów. Takie podejście wyjaśnia kolejność zależności i ułatwia późniejszą zmianę szablonu.

* Jeśli wartość można określić przed wdrożeniem, spróbuj wdrożyć zasób bez zależności. Na przykład jeśli wartość konfiguracji wymaga nazwy innego zasobu, może nie być konieczne zależności. Te wskazówki nie zawsze działa, ponieważ niektóre zasoby weryfikują istnienie innego zasobu. Jeśli zostanie wyświetlony błąd, dodaj zależność.

## <a name="resources"></a>Resources

Następujące informacje mogą być pomocne podczas pracy z [zasobami:](template-syntax.md#resources)

* Aby pomóc innym współautorom zrozumieć cel zasobu, określ **komentarze** dla każdego zasobu w szablonie:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-06-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* Jeśli używasz *publicznego punktu końcowego* w szablonie (na przykład publiczny punkt końcowy magazynu obiektów Blob platformy Azure), *nie koduj na komputerze z twardym kodem* obszaru nazw. Funkcja **odwołania** umożliwia dynamiczne pobieranie obszaru nazw. Za pomocą tej metody można wdrożyć szablon do różnych środowisk publicznych obszarów nazw bez ręcznej zmiany punktu końcowego w szablonie. Ustaw wersję interfejsu API na tę samą wersję, której używasz dla konta magazynu w szablonie:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   Jeśli konto magazynu jest wdrażany w tym samym szablonie, który tworzysz i nazwa konta magazynu nie jest współużytkowany z innym zasobem w szablonie, nie trzeba określać obszaru nazw dostawcy lub apiVersion podczas odwoływania się do zasobu. W poniższym przykładzie przedstawiono uproszczoną składnię:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
       }
   }
   ```
     
   Można również odwoływać się do istniejącego konta magazynu, które znajduje się w innej grupie zasobów:

   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('existingStorageAccountName')), '2019-06-01').primaryEndpoints.blob]"
       }
   }
   ```

* Przypisz publiczne adresy IP do maszyny wirtualnej tylko wtedy, gdy aplikacja tego wymaga. Aby połączyć się z maszyną wirtualną (VM) do debugowania lub do celów zarządzania lub administracyjnych, użyj reguł przychodzącego translatora adresów sieciowych, bramy sieci wirtualnej lub skrzynki szybkiego dostępu.
   
     Aby uzyskać więcej informacji na temat łączenia się z maszynami wirtualnymi, zobacz:
   
   * [Uruchamianie maszyn wirtualnych dla architektury n-warstwowej na platformie Azure](../../guidance/guidance-compute-n-tier-vm.md)
   * [Konfigurowanie dostępu usługi WinRM dla maszyn wirtualnych w usłudze Azure Resource Manager](../../virtual-machines/windows/winrm.md)
   * [Zezwalaj na dostęp zewnętrzny do maszyny Wirtualnej przy użyciu witryny Azure portal](../../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Zezwalaj na dostęp zewnętrzny do maszyny Wirtualnej przy użyciu programu PowerShell](../../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Zezwalaj na dostęp zewnętrzny do maszyny Wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure](../../virtual-machines/virtual-machines-linux-nsg-quickstart.md)

* Właściwość **domainNameLabel** dla publicznych adresów IP musi być unikatowa. **DomainNameLabel** wartość musi zawierać od 3 do 63 znaków i przestrzegać `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`reguł określonych przez to wyrażenie regularne: . Ponieważ funkcja **uniqueString** generuje ciąg o długości 13 znaków, parametr **dnsPrefixString** jest ograniczony do 50 znaków:

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

* Po dodaniu hasła do niestandardowego rozszerzenia skryptu należy użyć właściwości **commandToExecute** we właściwości **protectedSettings:**
   
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
   > Aby upewnić się, że wpisy tajne są szyfrowane, gdy są przekazywane jako parametry do maszyn wirtualnych i rozszerzeń, należy użyć **protectedSettings** właściwości odpowiednich rozszerzeń.
   > 
   > 

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje na temat struktury pliku szablonu, zobacz [Opis struktury i składni szablonów ARM](template-syntax.md).
* Aby uzyskać zalecenia dotyczące tworzenia szablonów, które działają we wszystkich środowiskach chmury platformy Azure, zobacz [Tworzenie szablonów ARM dla spójności chmury.](templates-cloud-consistency.md)
