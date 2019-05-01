---
title: Wdrażanie maszyny Wirtualnej z sieci wirtualnych dysków twardych z rynku platformy Azure
description: Wyjaśnia, jak zarejestrować Maszynę wirtualną z wirtualnego dysku twardego wdrożonych przez usługę Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 11/30/2018
ms.author: pabutler
ms.openlocfilehash: a393620f28d45ec494c4e899f01e7e9a92b3ceba
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938290"
---
# <a name="deploy-a-vm-from-your-vhds"></a>Wdrażanie maszyny Wirtualnej z sieci wirtualnych dysków twardych

W tej sekcji wyjaśniono, jak wdrożyć maszynę wirtualną (VM) z wdrożonych przez usługę Azure wirtualnego dysku twardego (VHD).  Wyświetla listę wymagane narzędzia i jak ich używać do tworzenia obrazu maszyny Wirtualnej użytkownika, a następnie wdrożyć ją na platformie Azure za pomocą skryptów programu PowerShell.

Po przekazaniu wirtualnych dysków twardych (VHD) — uogólnionego wirtualnego dysku twardego systemu operacyjnego i zero lub więcej danych na dysku VHD — do swojego konta usługi Azure storage można zarejestrować je jako obraz maszyny Wirtualnej użytkownika. Następnie można przetestować tego obrazu. Ponieważ wirtualny dysk twardy systemu operacyjnego jest uogólniony, nie można bezpośrednio wdrożyć maszynę Wirtualną, podając adres URL wirtualnego dysku twardego.

Aby dowiedzieć się więcej na temat obrazów maszyn wirtualnych, zobacz następujące wpisy na blogu:

- [Obraz maszyny Wirtualnej](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [Obraz maszyny Wirtualnej w programie PowerShell "Jak"](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisite-install-the-necessary-tools"></a>Wymagania wstępne: Instalacja niezbędne narzędzia

Jeśli jeszcze tego nie zrobiono, należy zainstalować programu Azure PowerShell i wiersza polecenia platformy Azure, korzystając z następujących instrukcji:

- [Instalowanie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)
- [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="deployment-steps"></a>Kroki wdrażania

Poniższe kroki zostaną umożliwia tworzenie i wdrażanie obrazu użytkownika maszyny Wirtualnej:

1. Tworzenie obrazu maszyny Wirtualnej użytkownika, co pociąga za sobą przechwytywania i uogólnianie obrazu. 
2. Tworzenie certyfikatów i przechowywać je w nowej usłudze Azure Key Vault. Certyfikat jest wymagany do ustanawiania bezpiecznego połączenia usługi WinRM na maszynie wirtualnej.  Szablon usługi Azure Resource Manager i skrypt programu Azure PowerShell są dostarczane. 
3. Wdróż maszynę Wirtualną z obrazu maszyny Wirtualnej użytkownika, przy użyciu dostarczonego szablonu i skryptu.

Po wdrożeniu maszyny Wirtualnej można przystąpić do [certyfikować swój obraz maszyny Wirtualnej](./cpp-certify-vm.md).

1. Kliknij przycisk **New** i wyszukaj **wdrożenie szablonu**, a następnie wybierz **Utwórz własny szablon w edytorze**.  <br/>
   ![Tworzenie szablonu wdrożenia wirtualnego dysku twardego w witrynie Azure portal](./media/publishvm_021.png)

1. Skopiuj i Wklej to [szablon JSON](./cpp-deploy-json-template.md) w edytorze i kliknij przycisk **Zapisz**. <br/>
   ![Zapisz szablon wdrożenia wirtualnego dysku twardego w witrynie Azure portal](./media/publishvm_022.png)

1. Podaj wartości parametrów dla wyświetlonych **wdrożenie niestandardowe** strony właściwości.

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
            
1. Po podaniu tych wartości, kliknij przycisk **zakupu**. 

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


## <a name="next-steps"></a>Kolejne kroki

Następnie zostanie [utworzenie obrazu maszyny Wirtualnej użytkownika](cpp-create-user-image.md) dla Twojego rozwiązania.

