---
ms.openlocfilehash: aec6282daadc61b4e1bcf6bbaf1266d9bc98cdac
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895802"
---
<!--save a copy of this file to your local repo. It's important that you follow the naming conventions by starting with the service name, and use lowercase only for the file name. See "file-names-and-locations.md" under the "contributor-guide" folder in your repo.

Info to help you use the template are enclosed in the Markdown comments using the caret, hyphen, dash syntax. Delete these from your file.

Text not wrapped in comment syntax is intended to be used as is, or with updates enclosed in [  ]. Add the info and delete the bracket. 

Pay attention to spacing and indents. They affect formatting. 

--> 

<!--replace this with Properties and Tags sections. These are required sections. See "article-metadata.md" in under the "contributor-guide" folder in your repo. Attributes in each section can be placed on separate lines to make them easier to read and check-->

# <a name="use-azure-powershell-to-task"></a>[Zadanie] przy użyciu programu Azure PowerShell
W tym artykule pokazano, jak [zadanie], przy użyciu poleceń zarówno z modułu platformy Azure i moduł usługi Azure Resource Manager. To ma na celu pomóc nauczyć się nowych poleceń, także, jak migrować istniejące skrypty do nowych poleceń.

## <a name="prerequisite-install-a-recent-version-of-azure-powershell"></a>Wymagania wstępne: Zainstaluj najnowszą wersję programu Azure PowerShell
Jeśli użytkownik jeszcze tego nie zrobiono, zainstaluj przynajmniej [numer wersji] wersję programu Azure PowerShell na komputerze lokalnym. Jeśli używasz starszej wersji, nie będzie mieć poleceń cmdlet usługi Azure Resource Manager, opisanych w tym artykule. Aby uzyskać szczegółowe informacje, zobacz:

* [Jak zainstalować i skonfigurować program Azure PowerShell](install-configure-powershell.md) Aby uzyskać instrukcje dotyczące konfigurowania programu Azure PowerShell.
* [Przy użyciu programu Windows PowerShell z usługą Resource Manager](powershell-azure-resource-manager.md) uzyskać podstawowe informacje na temat korzystania z usługi Resource Manager.

> [!NOTE]
> Większość zadań wymaga przy użyciu wiersza polecenia przy użyciu programu Azure PowerShell o poziomie administratora.
> 
> 

## <a name="command-comparison"></a>Polecenie porównania
To [Tabela | sekcji] przedstawiono składnię polecenia.

<!--[optional image - to use an image in this article, add a folder with the same name as the article file name without extension, inside the Media folder of the repo. Use only this folder to store the images. Don't attempt to use a common folder to share images you want to use in more than 1 file.]
Then, use the following syntax to add a reference to the image in your article:
![](./media/name-of-file-without-extension/image-name-no-spaces.png)
-->

<!--if a command string uses variables, define the variables first, using the  following construction. In some cases the variable is straightforward and doesn't need much explanation. But parameters such as location and size can benefit from brief explanation or listing all accepted values:--> 

Te przykłady poleceń, użyj poniższych zmiennych:

$FriendlyName"<Describe value>"

<!-- if it makes more sense to present this in a table, use this. Otherwise, delete. The table won't render until it's in GitHub or published to Sandbox.-->

| Zarządzanie usługami | Resource Manager |
| --- | --- |
| `syntax` |`syntax` |

<!--if it makes more sense to present this one command block after the other instead of a table, use this. Otherwise, delete-->

[Wprowadzenie krótkie zdanie o poleceniu. Pominąć, jeśli nie ma naprawdę nic powiedzieć. Ale korzysta z takich metod potoku, wyjaśniono, że]:

    [command string]

## <a name="script-examples"></a>Przykłady skryptów
Oto przykład pokazujący [nazwy poleceń cmdlet)] do [zadania]. Zawiera polecenia który:

* [krótki zlecenie, zastosowań, ma, jest itp]
* [dalej zlecenie krótki] 

<!--include this statement if it uses variables that weren't introduced earlier--> Zawiera ona następujące zmienne:

* [zmienna 1]
* [zmienna 2]

<!--This shows you how a recent example was presented as well as how it was formatted. Preceding each line with one tab or four spaces to format in a code block-->

    $family="Windows Server 2012 R2 Datacenter"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
    $vmname="AZDC1"
    $vmsize="Medium"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

    $vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

    $vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

    $disksize=20
    $disklabel="DCData"
    $lun=0
    $hcaching="None"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

    $svcname="Azure-TailspinToys"
    $vnetname="AZDatacenter"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname


## <a name="additional-resources"></a>Dodatkowe zasoby
<!--At a minimum, include a link back to the migration task list article. Use the formats shown below. See create-links-markdown.md for more info -->
<!--use this format for links to other articles, such as the migration task list. -->
[Zarządzanie dostępnością](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

<!--To link to an ACOM page outside the /documentation/ subdomain (such as a pricing page, SLA page or anything else that is not a documentation article), use an absolute URL, but omit the locale:

    [link text](https://azure.microsoft.com/pricing/details/virtual-machines/)-->

<!--use this for URLs outside of ACOM. Be sure to locale, and if you're linking to the Azure library on MSDN, include the '/azure/' part of the URL-->
[Dokumentacja dotycząca maszyn wirtualnych](/previous-versions/azure/jj156003(v=azure.100))

