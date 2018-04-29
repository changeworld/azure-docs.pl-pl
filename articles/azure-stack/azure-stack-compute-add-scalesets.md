---
title: Dostępne w stosie Azure zestawach skali maszyn wirtualnych upewnij | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać skalowania maszyny wirtualnej w portalu Azure Marketplace stosu operatorowi chmury
services: azure-stack
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.topic: article
ms.date: 04/06/2018
ms.author: brenduns
ms.reviewer: anajod
keywords: ''
ms.openlocfilehash: f844576edfb2869ed906415368eb809c7b4b6746
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>Udostępnia zestawy skalowania maszyny wirtualnej Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Zestawy skalowania maszyny wirtualnej są zasobów obliczeniowych Azure stosu. Można używać ich do wdrażania i zarządzania zestaw wiele identycznych maszyn wirtualnych. Z wszystkie maszyny wirtualne skonfigurowane tak samo, zestawy skalowania nie wymagają wstępnego inicjowania obsługi administracyjnej maszyn wirtualnych. Możliwe jest łatwiejsze tworzenie usług na dużą skalę, przeznaczonych dla dużych obliczeniowych, dane big data i konteneryzowanych obciążeń.

W tym artykule prowadzi użytkownika przez proces, aby udostępnić zestawy skalowania w portalu Azure Marketplace stosu. Po zakończeniu tej procedury użytkownicy mogą dodawać zestawy skalowania maszyny wirtualnej do subskrypcji.

Zestawy skalowania maszyny wirtualnej na stosie Azure są podobne zestawy skalowania maszyny wirtualnej na platformie Azure. Aby uzyskać więcej informacji zobacz następujące filmy wideo:
* [Mark Russinovich omawia zestawy skalowania na platformie Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Zestawy skalowania maszyn wirtualnych według Guya Bowermana](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

Na stosie Azure zestawy skalowania maszyny wirtualnej nie obsługuje automatycznego skalowania. Można dodać więcej wystąpień do skalowania, ustawić za pomocą portalu Azure stosu, szablony usługi Resource Manager lub programu PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne
* **Narzędzia i programu PowerShell**

   Instalowanie i PowerShell skonfigurowanych dla stosu Azure i narzędzi Azure stosu. Zobacz [rozpocząć pracę przy użyciu programu PowerShell w stosie Azure](azure-stack-powershell-configure-quickstart.md).

   Po zainstalowaniu narzędzi Azure stosu, upewnij się, należy zaimportować następujące moduł programu PowerShell (ścieżka względem. \ComputeAdmin folderu w folderze AzureStack Narzędzia główne):

        Import-Module .\AzureStack.ComputeAdmin.psm1

* **Obraz systemu operacyjnego**

   Jeśli nie dodano obrazu systemu operacyjnego do programu Azure Marketplace stosu, zobacz [Dodaj obraz maszyny Wirtualnej systemu Windows Server 2016 do stosu Azure marketplace](azure-stack-add-default-image.md).

   Obsługę systemu Linux, Pobierz Ubuntu Server 16.04 i dodać go za pomocą ```Add-AzsVMImage``` z następującymi parametrami: ```-publisher "Canonical" -offer "UbuntuServer" -sku "16.04-LTS"```.


## <a name="add-the-virtual-machine-scale-set"></a>Dodaj zestaw skali maszyny wirtualnej

Edytuj poniższy skrypt programu PowerShell dla danego środowiska, a następnie uruchom go, aby dodać zestaw do programu Azure Marketplace stosu skalowania maszyny wirtualnej. 

``$User`` to konto, które są używane do łączenia z portalu administratora. Na przykład serviceadmin@contoso.onmicrosoft.com.

```
$Arm = "https://adminmanagement.local.azurestack.external"
$Location = "local"

Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint $Arm

$Password = ConvertTo-SecureString -AsPlainText -Force "<your Azure Stack administrator password>"

$User = "<your Azure Stack service administrator user name>"

$Creds =  New-Object System.Management.Automation.PSCredential $User, $Password

$AzsEnv = Get-AzureRmEnvironment AzureStackAdmin
$AzsEnvContext = Connect-AzureRmAccount -Environment $AzsEnv -Credential $Creds

Select-AzureRmSubscription -SubscriptionName "Default Provider Subscription"

Add-AzsVMSSGalleryItem -Location $Location
```

## <a name="update-images-in-a-virtual-machine-scale-set"></a>Aktualizowanie obrazów w zestawie skalowania maszyn wirtualnych 
Po utworzeniu zestawu skalowania maszyn wirtualnych użytkowników można aktualizować obrazów w skali bez zestaw musi zostać ponownie utworzone skalowania. Proces aktualizacji obrazu jest zależna od następujących scenariuszy:

1. Szablon wdrożenia zestawu skalowania maszyn wirtualnych **określa najnowszych** dla *wersji*:  

   Gdy *wersji* jest ustawiony jako **najnowsze** w *elementu imageReference* sekcji szablonu dla skalowania ustawić, skalowanie w górę operacje przy użyciu zestawu skali najnowszej dostępnej wersji obrazu skali ustawić wystąpień. Po zakończeniu skalowania w górę, można usunąć starszej wystąpień zestawów skalowania maszyn wirtualnych.  (Wartości *wydawcy*, *oferują*, i *sku* pozostają niezmienione). 

   Poniżej przedstawiono przykład określenia *najnowsze*:  

          "imageReference": {
             "publisher": "[parameters('osImagePublisher')]",
             "offer": "[parameters('osImageOffer')]",
             "sku": "[parameters('osImageSku')]",
             "version": "latest"
             }

   Zanim skalowania w górę, można użyć nowego obrazu, należy pobrać nowe obrazu:  

   - Gdy obraz w witrynie Marketplace jest nowsza wersja niż obrazu w zestawie skalowania: Pobierz nowy obraz, który zastępuje starsze obrazu. Po obrazu zostanie zastąpiony, użytkownik może przejść do skalowanie w górę. 

   - Gdy wersja obrazu w witrynie Marketplace jest taka sama jak obrazu w zestawie skalowania: Usuń obraz, który jest używany w zestawie skalowania, a następnie Pobierz nowy obraz. W okresie między po usunięciu oryginalnego obrazu i pobieranie obrazu nie skalowanie w górę. 
      
     Ten proces jest wymagany do resyndicate obrazów, dzięki któremu używać formatu plików rozrzedzonych, wprowadzonym w wersji 1803. 
 

2. Szablon wdrożenia zestawu skalowania maszyn wirtualnych **nie określa najnowszych** dla *wersji* i zamiast tego Określa numer wersji:  

     W przypadku pobrania obrazu przy użyciu nowszej wersji (co spowoduje zmianę wersji dostępnych), zestaw skalowania nie skalowanie w górę. To jest celowe jako wersja obrazu określonego w szablonie zestaw skali musi być dostępna.  

Aby uzyskać więcej informacji, zobacz [dysków systemu operacyjnego i obrazy](.\user\azure-stack-compute-overview.md#operating-system-disks-and-images).  


## <a name="remove-a-virtual-machine-scale-set"></a>Usuń zestaw skali maszyny wirtualnej

Aby usunąć maszynę wirtualną skalować zestawu z elementem galerii, uruchom następujące polecenie programu PowerShell:

    Remove-AzsVMSSGalleryItem

> [!NOTE]
> Nie można bezpośrednio usunąć elementu galerii. Nocy należy odświeżyć portalu kilka razy, aby element będzie wyświetlany jako usunięte z portalu Marketplace.


## <a name="next-steps"></a>Kolejne kroki
[Często zadawane pytania dotyczące usługi Azure stosu](azure-stack-faq.md)

