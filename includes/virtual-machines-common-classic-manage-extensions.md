---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d14cfb82ae74f85425dbd3e8a365e8b99969641d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485188"
---
## <a name="using-vm-extensions"></a>Przy użyciu rozszerzeń maszyn wirtualnych
Implementowanie rozszerzeń maszyny Wirtualnej platformy Azure, zachowania lub funkcje, które ułatwiają albo inne programy, które działają na maszynach wirtualnych Azure (na przykład **WebDeployForVSDevTest** rozszerzenie umożliwia programowi Visual Studio do narzędzia Web Deploy rozwiązań na maszynie Wirtualnej platformy Azure) lub podaj możliwość interakcji z maszyny Wirtualnej obsługuje kilka innych zachowań (na przykład umożliwia rozszerzenia dostępu do maszyny Wirtualnej z klientów programu PowerShell, interfejsu wiersza polecenia platformy Azure i REST zresetować lub zmodyfikuj wartości dostępu zdalnego na maszynie Wirtualnej platformy Azure).

> [!IMPORTANT]
> Aby uzyskać pełną listę rozszerzeń przy użyciu funkcji, które obsługują, zobacz [rozszerzeń maszyn wirtualnych platformy Azure i funkcji](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ponieważ każde rozszerzenie maszyny Wirtualnej obsługuje określonych funkcji, dokładnie co można, a nie będzie możliwe rozszerzenie jest zależna od rozszerzenia. Dlatego przed zmodyfikowaniem maszyny Wirtualnej, sprawdź, czy mają przeczytaj dokumentację dotyczącą rozszerzenia maszyny Wirtualnej, w której chcesz użyć. Usunięcie niektórych rozszerzeń maszyny Wirtualnej nie jest obsługiwana; inne osoby mają właściwości, które można ustawić, które znacząco zmieniają zachowanie maszyny Wirtualnej.
> 
> 

Najczęściej wykonywane czynności są następujące:

1. Znajdowanie dostępnych rozszerzeń
2. Aktualizowanie załadować rozszerzeń
3. Dodawanie rozszerzeń
4. Usuwanie rozszerzenia

## <a name="find-available-extensions"></a>Znajdowanie dostępnych rozszerzeń
Możesz znaleźć rozszerzenia i używanie rozszerzonych informacji:

* PowerShell
* Interfejs wiersza polecenia dla wielu Platform Azure (Azure CLI)
* Interfejs API protokołu REST zarządzania usługami

### <a name="azure-powershell"></a>Azure PowerShell
Niektóre rozszerzenia zostały poleceń cmdlet programu PowerShell, które są specyficzne dla nich, które mogą ułatwić ich konfiguracji z poziomu programu PowerShell; ale następujących poleceń cmdlet dla wszystkich rozszerzeń maszyny Wirtualnej.

Aby uzyskać informacje o dostępnych rozszerzeń, można użyć następujących poleceń cmdlet:

* Dla wystąpień ról sieć web lub ról procesów roboczych, można użyć [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) polecenia cmdlet.
* Dla wystąpień maszyn wirtualnych, można użyć [Get AzureVMAvailableExtension](https://msdn.microsoft.com/library/azure/dn722480.aspx) polecenia cmdlet.
  
   Na przykład, poniższy kod ilustruje sposób wyświetlenia listy informacji dla **IaaSDiagnostics** rozszerzenia za pomocą programu PowerShell.
  
      PS C:\> Get-AzureVMAvailableExtension -ExtensionName IaaSDiagnostics
  
      Publisher                   : Microsoft.Azure.Diagnostics
      ExtensionName               : IaaSDiagnostics
      Version                     : 1.2
      Label                       : Microsoft Monitoring Agent Diagnostics
      Description                 : Microsoft Monitoring Agent Extension
      PublicConfigurationSchema   :
      PrivateConfigurationSchema  :
      IsInternalExtension         : False
      SampleConfig                :
      ReplicationCompleted        : True
      Eula                        :
      PrivacyUri                  :
      HomepageUri                 :
      IsJsonExtension             : True
      DisallowMajorVersionUpgrade : False
      SupportedOS                 :
      PublishedDate               :
      CompanyName                 :

### <a name="azure-command-line-interface-azure-cli"></a>Interfejs wiersza polecenia platformy Azure (Azure CLI)
Niektóre rozszerzenia zostały polecenia wiersza polecenia platformy Azure, które są specyficzne dla nich (rozszerzenie maszyny Wirtualnej Docker jest jednym z przykładów), które mogą ułatwić ich konfiguracji; Jednak poniższe polecenia działać dla wszystkich rozszerzeń maszyny Wirtualnej.

Możesz użyć **listy rozszerzeń maszyny wirtualnej platformy azure** polecenie, aby uzyskać informacje na temat dostępnych rozszerzeń, a następnie użyj **—-json** opcję, aby wyświetlić wszystkie dostępne informacje o jedno lub więcej rozszerzeń. Jeśli nie zostanie użyta nazwa rozszerzenia, to polecenie zwraca opisu JSON wszystkich dostępnych rozszerzeń.

Na przykład, poniższy kod ilustruje sposób wyświetlenia listy informacji dla **IaaSDiagnostics** rozszerzenia za pomocą wiersza polecenia platformy Azure **listy rozszerzeń maszyny wirtualnej platformy azure** polecenia i zastosowań **—-json**  opcję, aby zwracać pełne informacje.

    $ azure vm extension list -n IaaSDiagnostics --json
    [
      {
        "publisher": "Microsoft.Azure.Diagnostics",
        "name": "IaaSDiagnostics",
        "version": "1.2",
        "label": "Microsoft Monitoring Agent Diagnostics",
        "description": "Microsoft Monitoring Agent Extension",
        "replicationCompleted": true,
        "isJsonExtension": true
      }
    ]



### <a name="service-management-rest-apis"></a>Interfejsy API REST zarządzania usługami
Aby uzyskać informacje o dostępnych rozszerzeń, można użyć następujących interfejsów API REST:

* Dla wystąpień ról sieć web lub ról procesów roboczych, można użyć [listę dostępnych rozszerzeń](https://msdn.microsoft.com/library/dn169559.aspx) operacji. Aby wyświetlić listę wersji dostępnych rozszerzeń, można użyć [Utwórz listę wersji rozszerzenia](https://msdn.microsoft.com/library/dn495437.aspx).
* Dla wystąpień maszyn wirtualnych, można użyć [listy rozszerzeń zasobów](https://msdn.microsoft.com/library/dn495441.aspx) operacji. Aby wyświetlić listę wersji dostępnych rozszerzeń, można użyć [Utwórz listę wersji rozszerzenia zasobu](https://msdn.microsoft.com/library/dn495440.aspx).

## <a name="add-update-or-disable-extensions"></a>Dodawanie, aktualizowanie lub wyłączyć rozszerzenia
Rozszerzenia można dodać, gdy tworzone jest wystąpienie lub mogą być dodawane do uruchomionego wystąpienia. Rozszerzenia można zaktualizować, wyłączone lub usunięte. Te akcje można wykonać przy użyciu poleceń cmdlet programu Azure PowerShell lub przy użyciu operacji interfejsu API REST zarządzania usługami. Aby zainstalować i skonfigurować niektóre rozszerzenia są wymagane parametry. Parametry publiczne i prywatne są obsługiwane dla rozszerzeń.

### <a name="azure-powershell"></a>Azure PowerShell
Za pomocą poleceń cmdlet programu Azure PowerShell jest najprostszym sposobem na dodawanie i aktualizowanie rozszerzeń. Korzystając z poleceń cmdlet rozszerzenia, większość konfiguracji rozszerzenia jest wykonywane. Czasami może być konieczne programowo dodać rozszerzenie. Gdy trzeba to zrobić, należy podać konfiguracji rozszerzenia.

Aby dowiedzieć się, czy rozszerzenie wymaga konfiguracji parametrów publicznych i prywatnych, można użyć następujących poleceń cmdlet:

* Dla wystąpień ról sieć web lub ról procesów roboczych, można użyć **Get-AzureServiceAvailableExtension** polecenia cmdlet.
* Dla wystąpień maszyn wirtualnych, można użyć **Get AzureVMAvailableExtension** polecenia cmdlet.

### <a name="service-management-rest-apis"></a>Interfejsy API REST zarządzania usługami
Możesz pobrać listę dostępnych rozszerzeń przy użyciu interfejsów API REST, pojawi się uzyskać informacje na temat sposobu rozszerzenia do skonfigurowania. Zwrócone informacje może wyświetlać informacje o parametrach, reprezentowany przez schemat publicznej i prywatnej schematu. Wartości parametrów publicznego są zwracane w zapytaniach dotyczących wystąpień. Wartości parametrów prywatne nie są zwracane.

Aby dowiedzieć się, czy rozszerzenie wymaga konfiguracji parametrów publicznych i prywatnych, można użyć następujące interfejsy API REST:

* Dla wystąpień ról sieć web lub ról procesów roboczych **PublicConfigurationSchema** i **PrivateConfigurationSchema** elementy zawierają informacje w odpowiedzi z [dostępne listy Rozszerzenia](https://msdn.microsoft.com/library/dn169559.aspx) operacji.
* Dla wystąpień maszyn wirtualnych **PublicConfigurationSchema** i **PrivateConfigurationSchema** elementy zawierają informacje w odpowiedzi z [listy zasobów Rozszerzenia](https://msdn.microsoft.com/library/dn495441.aspx) operacji.

> [!NOTE]
> Rozszerzenia umożliwiają konfiguracje, które są zdefiniowane przy użyciu formatu JSON. Gdy używane są te typy rozszerzeń, tylko **SampleConfig** element jest używany.
> 
> 

