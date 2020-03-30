---
title: Wdrażanie maszyny wirtualnej z wirtualnych sieci wirtualnych w portalu Azure Marketplace
description: W tym artykule wyjaśniono, jak zarejestrować maszynę wirtualną z dysku wirtualnego wdrożonego w usłudze Azure.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: dsindona
ms.openlocfilehash: 5263d24c411ef8de4187c2fd750013374d779f04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277943"
---
# <a name="deploy-a-vm-from-your-vhds"></a>Wdrażanie maszyny wirtualnej z wirtualnych sieci wirtualnych

W tej sekcji wyjaśniono, jak wdrożyć maszynę wirtualną (VM) z wirtualnego dysku twardego (VHD) wdrożonego pod platformą Azure.  Zawiera listę wymaganych narzędzi i jak ich używać do tworzenia obrazu maszyny Wirtualnej użytkownika, a następnie wdrożyć go na platformie Azure przy użyciu skryptów programu PowerShell.

Po przekazaniu wirtualnych dysków twardych (VHD) — uogólnionego systemu operacyjnego VHD i zero lub więcej dysków wirtualnych z dyskami danych — na konto magazynu platformy Azure można zarejestrować je jako obraz maszyny wirtualnej użytkownika. Następnie możesz przetestować ten obraz. Ponieważ dysk VHD systemu operacyjnego jest uogólniony, nie można bezpośrednio wdrożyć maszyny Wirtualnej, podając adres URL vhd.

Aby dowiedzieć się więcej o obrazach maszyn wirtualnych, zobacz następujące wpisy w blogu:

- [Obraz maszyny Wirtualnej](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [Obraz maszyny Wirtualnej PowerShell "Jak"](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisite-install-the-necessary-tools"></a>Warunek wstępny: zainstaluj niezbędne narzędzia

Jeśli jeszcze tego nie zrobiono, zainstaluj program Azure PowerShell i platformę Azure CLI, korzystając z następujących instrukcji:

- [Instalowanie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)
- [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="deployment-steps"></a>Kroki wdrażania

Aby utworzyć i wdrożyć obraz maszyny Wirtualnej użytkownika, należy wykonać następujące kroki:

1. Utwórz obraz maszyny Wirtualnej użytkownika, który pociąga za sobą przechwytywanie i uogólnianie obrazu. 
2. Tworzenie certyfikatów i przechowywanie ich w nowej usłudze Azure Key Vault. Certyfikat jest wymagany do ustanowienia bezpiecznego połączenia Usługi WinRM z maszyną wirtualną.  Szablon usługi Azure Resource Manager i skrypt programu Azure PowerShell są dostarczane. 
3. Wdrażanie maszyny Wirtualnej z obrazu maszyny Wirtualnej użytkownika przy użyciu dostarczonego szablonu i skryptu.

Po wdrożeniu maszyny Wirtualnej można przystąpić do [certyfikacji obrazu maszyny Wirtualnej.](./cpp-certify-vm.md)

1. Kliknij **pozycję Nowy** i wyszukaj opcję **Wdrażanie szablonów**, a następnie wybierz pozycję **Zbuduj własny szablon w Edytorze**.  <br/>
   ![Tworzenie szablonu wdrożenia VHD w witrynie Azure portal](./media/publishvm_021.png)

1. Skopiuj i wklej ten [szablon JSON](./cpp-deploy-json-template.md) do edytora i kliknij przycisk **Zapisz**. <br/>
   ![Zapisz szablon wdrożenia VHD w witrynie Azure portal](./media/publishvm_022.png)

1. Podaj wartości parametrów wyświetlanych stron właściwości **wdrożenia niestandardowego.**

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **Parametr**              |   **Opis**                                                            |
   |  -------------              |   ---------------                                                            |
   | Nazwa konta magazynu użytkownika   | Nazwa konta magazynu, w którym znajduje się uogólniony dysk VHD                    |
   | Nazwa kontenera magazynu użytkownika | Nazwa kontenera, w którym znajduje się uogólniony dysk VHD                          |
   | Nazwa DNS publicznego adresu IP      | Publiczna nazwa DNS IP. Nazwa DNS jest maszyny Wirtualnej, można zdefiniować to w witrynie Azure Portal, po wdrożeniu oferty.  |
   | Nazwa użytkownika administratora             | Nazwa użytkownika konta administratora dla nowej maszyny Wirtualnej                                  |
   | Hasło administratora              | Hasło konta administratora dla nowej maszyny Wirtualnej                                  |
   | Typ systemu operacyjnego                     | System operacyjny maszyny `Windows` \| Wirtualnej:`Linux`                                    |
   | Identyfikator subskrypcji             | Identyfikator wybranej subskrypcji                                      |
   | Lokalizacja                    | Lokalizacja geograficzna wdrożenia                                        |
   | Rozmiar maszyny wirtualnej                     | [Rozmiar maszyny Wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), na przykład`Standard_A2` |
   | Nazwa publicznego adresu IP      | Nazwa publicznego adresu IP                                               |
   | Nazwa maszyny wirtualnej                     | Nazwa nowej maszyny Wirtualnej                                                           |
   | Nazwa sieci wirtualnej        | Nazwa sieci wirtualnej używanej przez maszynę wirtualną                                   |
   | Nazwa karty sieciowej                    | Nazwa karty interfejsu sieciowego z siecią wirtualną               |
   | VHD URL                     | Pełny adres URL dysku systemu operacyjnego VHD                                                     |
   |  |  |
            
1. Po podasz te wartości kliknij przycisk **Kup**. 

Platforma Azure rozpocznie wdrażanie: tworzy nową maszynę wirtualną z określonym niezarządzanym dyskiem wirtualnym w określonej ścieżce konta magazynu.  Postęp w portalu Azure można śledzić, klikając na **maszyny wirtualne** po lewej stronie portalu.  Po utworzeniu maszyny Wirtualnej stan zmieni `Starting` `Running`się z na . 


### <a name="deploy-a-vm-from-powershell"></a>Wdrażanie maszyny wirtualnej z programu PowerShell

Aby wdrożyć dużą maszynę wirtualną z uogólnionego obrazu maszyny Wirtualnej, który właśnie został utworzony, należy użyć następujących poleceń cmdlet.

``` powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```


## <a name="next-steps"></a>Następne kroki

Następnie [utworzysz obraz maszyny Wirtualnej użytkownika](cpp-create-user-image.md) dla rozwiązania.

