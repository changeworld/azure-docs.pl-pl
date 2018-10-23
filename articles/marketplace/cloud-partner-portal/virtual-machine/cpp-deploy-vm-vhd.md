---
title: Wdrażanie maszyny Wirtualnej z sieci wirtualnych dysków twardych z rynku platformy Azure | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak zarejestrować Maszynę wirtualną z wirtualnego dysku twardego wdrożonych przez usługę Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 2771549af29b3e717d117afb42de6db03fbee226
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639944"
---
# <a name="deploy-a-vm-from-your-vhds"></a>Wdrażanie maszyny Wirtualnej z sieci wirtualnych dysków twardych

W tym artykule wyjaśniono, jak zarejestrować maszynę wirtualną (VM) z wdrożonych przez usługę Azure wirtualnego dysku twardego (VHD).  Wyświetla listę wymagane narzędzia i jak ich używać do tworzenia obrazu maszyny Wirtualnej użytkownika, a następnie wdrożyć ją na platformie Azure przy użyciu [portalu Microsoft Azure](https://ms.portal.azure.com/) lub skryptów programu PowerShell. 

Po przekazaniu wirtualnych dysków twardych (VHD) — uogólnionego wirtualnego dysku twardego systemu operacyjnego i zero lub więcej danych na dysku VHD — do swojego konta usługi Azure storage można zarejestrować je jako obraz maszyny Wirtualnej użytkownika. Następnie można przetestować tego obrazu. Ponieważ wirtualny dysk twardy systemu operacyjnego jest uogólniony, nie można bezpośrednio wdrożyć maszynę Wirtualną, podając adres URL wirtualnego dysku twardego.

Aby dowiedzieć się więcej na temat obrazów maszyn wirtualnych, zobacz następujące wpisy na blogu:

- [Obraz maszyny Wirtualnej](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [Obraz maszyny Wirtualnej w programie PowerShell "Jak"](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)


## <a name="set-up-the-necessary-tools"></a>Konfigurowanie niezbędne narzędzia

Jeśli jeszcze tego nie zrobiono, należy zainstalować programu Azure PowerShell i wiersza polecenia platformy Azure, korzystając z następujących instrukcji:

<!-- TD: Change the following URLs (in this entire topic) to relative paths.-->

- [Instalowanie programu Azure PowerShell na Windows przy użyciu funkcji PowerShellGet](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)
- [Zainstaluj interfejs wiersza polecenia platformy Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="create-a-user-vm-image"></a>Tworzenie obrazu maszyny Wirtualnej użytkownika

Następnie utworzysz obrazu niezarządzanego z uogólnionego wirtualnego dysku twardego.

#### <a name="capture-the-vm-image"></a>Przechwytywanie obrazu maszyny Wirtualnej

Postępuj zgodnie z instrukcjami w artykule na przechwytywanie maszyny Wirtualnej, która odnosi się do danej metody dostępu:

-  Program PowerShell: [sposób tworzenia obrazu maszyny Wirtualnej niezarządzanej maszyny wirtualnej platformy Azure](../../../virtual-machines/windows/capture-image-resource.md)
-  Interfejs wiersza polecenia platformy Azure: [sposób tworzenia obrazu maszyny wirtualnej lub wirtualnego dysku twardego](../../../virtual-machines/linux/capture-image.md)
-  Interfejs API: [Virtual Machines — przechwytywania](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

### <a name="generalize-the-vm-image"></a>Uogólnij obraz maszyny Wirtualnej

Ponieważ obraz użytkownika został wygenerowany z wcześniej uogólnionego wirtualnego dysku twardego, również powinien być uogólniony.  Ponownie wybierz następującego artykułu, który odnosi się do mechanizmu Twojego dostępu.  (Możesz może mieć już uogólniony dysku podczas została przechwycona.)

-  Program PowerShell: [uogólnianie maszyny Wirtualnej](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Interfejs wiersza polecenia platformy Azure: [krok 2: Utwórz maszynę Wirtualną obrazu](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  Interfejs API: [uogólnić maszyny wirtualne —](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="deploy-a-vm-from-a-user-vm-image"></a>Wdrażanie maszyny Wirtualnej z obrazu maszyny Wirtualnej użytkownika

Następnie wdrożysz Maszynę wirtualną z obrazu maszyny Wirtualnej użytkownika, za pomocą witryny Azure portal lub programu PowerShell.

<!-- TD: Recapture following hilited images and replace with red-box. -->

### <a name="deploy-a-vm-from-azure-portal"></a>Wdrażanie maszyny Wirtualnej w witrynie Azure portal

Użyj następującego procesu wdrażania użytkownika maszyny Wirtualnej w witrynie Azure portal.

1.  Zaloguj się do [Azure Portal](https://portal.azure.com).

2.  Kliknij przycisk **New** i wyszukaj **wdrożenie szablonu**, a następnie wybierz **Utwórz własny szablon w edytorze**.  <br/>
  ![Tworzenie szablonu wdrożenia wirtualnego dysku twardego w witrynie Azure portal](./media/publishvm_021.png)

3. Skopiuj i Wklej to [szablon JSON](./cpp-deploy-json-template.md) w edytorze i kliknij przycisk **Zapisz**. <br/>
  ![Zapisz szablon wdrożenia wirtualnego dysku twardego w witrynie Azure portal](./media/publishvm_022.png)

4. Podaj wartości parametrów dla wyświetlonych **wdrożenie niestandardowe** strony właściwości.

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **Parametr**              |   **Opis**                                                            |
   |  -------------              |   ---------------                                                            |
   | Nazwa konta magazynu użytkownika   | Nazwa konta magazynu, w którym znajduje się uogólniony wirtualny dysk twardy                    |
   | Nazwa kontenera magazynu użytkownika | Nazwa kontenera, w którym znajduje się uogólniony wirtualny dysk twardy                          |
   | Nazwa DNS dla publicznego adresu IP      | Publiczna nazwa DNS adresu IP                                                           |
   | Nazwa użytkownika administratora             | Nazwa użytkownika konta administratora, dla nowej maszyny Wirtualnej                                  |
   | Hasło administratora              | Hasło do konta administratora dla nowej maszyny Wirtualnej                                  |
   | Typ systemu operacyjnego                     | System operacyjny maszyny Wirtualnej: `Windows` \| `Linux`                                    |
   | Identyfikator subskrypcji             | Identyfikator wybranej subskrypcji                                      |
   | Lokalizacja                    | Geograficzna lokalizacja wdrożenia                                        |
   | Rozmiar maszyny wirtualnej                     | [Rozmiar maszyny Wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), na przykład `Standard_A2` |
   | Nazwę publicznego adresu IP      | Nazwa publicznego adresu IP                                               |
   | Nazwa maszyny wirtualnej                     | Nazwa nowej maszyny Wirtualnej                                                           |
   | Nazwa sieci wirtualnej        | Nazwa sieci wirtualnej używane przez maszynę Wirtualną                                   |
   | Nazwa karty interfejsu Sieciowego                    | Nazwa karty interfejsu sieciowego z usługi virtual network               |
   | ADRES URL WIRTUALNEGO DYSKU TWARDEGO                     | Wykonaj adres URL wirtualnego dysku twardego dysku systemu operacyjnego                                                     |
   |  |  |
            
5. Po podaniu tych wartości, kliknij przycisk **zakupu**. 

Azure rozpocznie się wdrażanie: tworzy nową maszynę Wirtualną przy użyciu określonego niezarządzanego dysku VHD, w Ścieżka konta magazynu określony.  Możesz śledzić postęp w witrynie Azure portal, klikając **maszyn wirtualnych** po lewej stronie portalu.  Podczas tworzenia maszyny Wirtualnej stan zmieni się z `Starting` do `Running`. 


### <a name="deploy-a-vm-from-powershell"></a>Wdrażanie maszyny Wirtualnej za pomocą programu PowerShell

Aby wdrożyć dużą maszynę Wirtualną z obrazu uogólnionej maszyny Wirtualnej właśnie utworzony, użyj następujących poleceń cmdlet.

``` powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```

<!-- TD: The following is a marketplace-publishing article and may be out-of-date.  TD: update and move topic.
For help with issues, see [Troubleshooting common issues encountered during VHD creation](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting) for additional assistance.
-->

## <a name="next-steps"></a>Kolejne kroki

Po wdrożeniu maszyny Wirtualnej można przystąpić do [konfigurację maszyny Wirtualnej](./cpp-configure-vm.md).
