---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/11/2019
ms.author: cynthn
ms.openlocfilehash: 15724adfc6eb875f3d2d5b6d3ecbbb54135a7265
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671428"
---
# <a name="use-infrastructure-automation-tools-with-virtual-machines-in-azure"></a>Korzystanie z narzędzi automatyzacji infrastruktury z maszynami wirtualnymi na platformie Azure
Aby utworzyć i zarządzać maszyn wirtualnych (VM) w sposób ciągły na dużą skalę, zwykle jest pożądane jakąś formę automatyzacji. Istnieje wiele narzędzi i rozwiązań, które umożliwiają automatyzację wdrażania całej infrastruktury platformy Azure i zarządzanie cyklem życia. W tym artykule przedstawiono niektóre z narzędzi automatyzacji infrastruktury, które można użyć na platformie Azure. Te narzędzia często mieści się w jednej z następujących metod:

- Automatyzuj konfigurację maszyn wirtualnych
    - Narzędzia obejmują [Ansible](#ansible), [Chef](#chef), i [Puppet](#puppet).
    - Narzędzia specyficzne dla dostosowywania maszyny Wirtualnej obejmują [pakietu cloud-init](#cloud-init) maszyn wirtualnych systemu Linux, [PowerShell Desired State Configuration (DSC)](#powershell-dsc)i [niestandardowe rozszerzenie skryptu platformy Azure](#azure-custom-script-extension) dla wszystkich Maszyny wirtualne platformy Azure.
 
- Automatyzowanie zarządzania infrastrukturą
    - Narzędzia obejmują [Packer](#packer) zautomatyzować niestandardową maszynę Wirtualną tworzy obraz, i [Terraform](#terraform) zautomatyzować proces kompilacji infrastruktury.
    - [Usługa Azure Automation](#azure-automation) może wykonywać akcje w infrastrukturze platformy Azure i lokalnie.

- Automatyzowanie wdrażania aplikacji i dostarczanie
    - Przykłady obejmują [usługom DevOps platformy Azure](#azure-devops-services) i [Jenkins](#jenkins).

## <a name="ansible"></a>Ansible
[Rozwiązanie Ansible](https://www.ansible.com/) to aparat automatyzacji zarządzania konfiguracją, tworzenia maszyny Wirtualnej lub wdrażania aplikacji. Rozwiązanie Ansible używają modelu bez agenta, zazwyczaj przy użyciu kluczy SSH do uwierzytelniania i zarządzania komputerami docelowymi. Zadania konfiguracji są definiowane w elementy playbook, wprowadzając szereg modułów Ansible dostępne do wykonywania określonych zadań. Aby uzyskać więcej informacji, zobacz [działa jak Ansible](https://www.ansible.com/how-ansible-works).

Instrukcje:

- [Instalowanie i konfigurowanie rozwiązania Ansible w systemie Linux do użycia z usługą Azure](../articles/virtual-machines/linux/ansible-install-configure.md).
- [Utwórz maszynę wirtualną systemu Linux](../articles/virtual-machines/linux/ansible-create-vm.md).
- [Zarządzanie maszyną wirtualną systemu Linux](../articles/virtual-machines/linux/ansible-manage-linux-vm.md).


## <a name="chef"></a>Chef
[Program chef](https://www.chef.io/) to platforma do automatyzacji, który ułatwia definiują sposób konfigurowania infrastruktury, wdrażane i zarządzane. Dodatkowe składniki dołączana Chef, Habitat do automatyzacji cyklu życia aplikacji, a nie na infrastrukturze i Chef InSpec ułatwiające Automatyzowanie zgodności z zabezpieczeniami i wymagania dotyczące zasad. Program chef klienci zostali zainstalowani na komputerach docelowych, z co najmniej jeden centralny Chef serwerami, które przechowywać i zarządzać nimi konfiguracji. Aby uzyskać więcej informacji, zobacz [Przegląd Chef](https://docs.chef.io/chef_overview.html).

Instrukcje:

- [Wdrażanie programu Chef automatyzacji w portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview).
- [Instalowanie programu Chef na Windows i Tworzenie maszyn wirtualnych platformy Azure](../articles/virtual-machines/windows/chef-automation.md).


## <a name="puppet"></a>Puppet
[Puppet](https://www.puppet.com) to platforma automatyzacji z obsługą przedsiębiorstw, która obsługuje procesu dostarczania i wdrażania aplikacji. Agenci są zainstalowani na komputerach docelowych, aby zezwolić na główny Puppet, aby uruchomić manifestów, które definiują pożądaną konfigurację infrastruktury platformy Azure i maszyn wirtualnych. Puppet można zintegrować z innymi rozwiązaniami, takimi jak Jenkins i usługi GitHub dla przepływu pracy devops ulepszone. Aby uzyskać więcej informacji, zobacz [działa jak Puppet](https://puppet.com/products/how-puppet-works).

Instrukcje:

- [Wdrażanie Puppet z witryny Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/puppet.puppet-enterprise-2017-2?tab=Overview).


## <a name="cloud-init"></a>Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) to powszechnie używana metoda dostosowywania maszyny wirtualnej z systemem Linux podczas jej pierwszego rozruchu. Za pomocą pakietu cloud-init można instalować pakiety i zapisywać pliki lub konfigurować użytkowników i zabezpieczenia. Pakiet cloud-init jest wywoływana podczas początkowego rozruchu, dlatego są żadne dodatkowe kroki ani agenci wymagane do zastosowania konfiguracji.  Aby uzyskać więcej informacji na temat poprawnie sformatować swoje `#cloud-config` plików, zobacz [witrynie dokumentacji pakietu cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` są to pliki tekstowe zakodowane w formacie base64.

Pakiet cloud-init działa również w różnych dystrybucjach. Przykładowo nie używa się poleceń **apt-get install** lub **yum install** do zainstalowania pakietu. Zamiast tego możesz zdefiniować listę pakietów do zainstalowania. Pakiet cloud-init automatycznie używa natywnego narzędzia do zarządzania pakietami dla wybranej dystrybucji.

Aktywnie współpracujemy z partnerami zalecanych dystrybucji systemu Linux, aby mogła mieć pakietu cloud-init, włączone obrazów dostępnych w witrynie Azure marketplace. Te obrazy upewnij wdrożenia pakietu cloud-init, a konfiguracje bezproblemowej współpracy z maszyn wirtualnych i zestawów skalowania maszyn wirtualnych. Dowiedz się więcej na temat pakietu cloud-init w systemie Azure:

- [Pakiet cloud-init pomocy technicznej dla maszyn wirtualnych systemu Linux na platformie Azure](../articles/virtual-machines/linux/using-cloud-init.md)
- [Wypróbuj samouczek dotyczący automatycznych konfiguracji maszyny Wirtualnej przy użyciu pakietu cloud-init](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>PowerShell DSC
[Program PowerShell Desired State Configuration (DSC)](https://msdn.microsoft.com/powershell/dsc/overview) to platforma zarządzania, aby zdefiniować konfigurację komputerów docelowych. Może również służyć DSC w systemie Linux za pomocą [Open Management Infrastructure (OMI) serwera](https://collaboration.opengroup.org/omi/).

Konfiguracje DSC definiują, co należy zainstalować na komputerze oraz sposób konfigurowania hosta. Aparat lokalnego Configuration Manager (LCM) działa w każdym węźle docelowym, która przetwarza żądane akcje na podstawie konfiguracji wypychanie. Serwer ściągania jest usługą internetową, która działa na hoście centralnej do przechowywania konfiguracji DSC i skojarzonych zasobów. Serwerze ściągania komunikuje się z aparatem LCM na każdym hoście docelowym, podaj wymagane konfiguracje i sporządzić raport na temat zgodności.

Instrukcje:

- [Tworzenie podstawowej konfiguracji DSC](https://msdn.microsoft.com/powershell/dsc/quickstarts/website-quickstart).
- [Konfigurowanie serwera ściągania DSC](https://msdn.microsoft.com/powershell/dsc/pullserver).
- [Użyj DSC dla systemu Linux](https://msdn.microsoft.com/powershell/dsc/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Rozszerzenie niestandardowego skryptu platformy Azure
Niestandardowe rozszerzenie skryptu platformy Azure dla [Linux](../articles/virtual-machines/linux/extensions-customscript.md) lub [Windows](../articles/virtual-machines/windows/extensions-customscript.md) pobiera i wykonuje skrypty na maszynach wirtualnych platformy Azure. Podczas tworzenia maszyny Wirtualnej lub dowolnym momencie po maszyny Wirtualnej jest używany, można użyć rozszerzenia. 

Skrypty można pobrać z usługi Azure storage lub w dowolnej lokalizacji publicznej, takich jak repozytorium GitHub. Za pomocą rozszerzenia niestandardowego skryptu można pisać skrypty w dowolnym języku, który jest uruchamiany na źródłowej maszynie Wirtualnej. Skrypty te można zainstalować aplikacje lub konfigurację maszyny Wirtualnej zgodnie z potrzebami. Aby zabezpieczyć poświadczenia, poufne informacje, takie jak hasła mogą być przechowywane w chronionym konfiguracji. Te poświadczenia są odszyfrowane tylko wewnątrz maszyny Wirtualnej.

Instrukcje:

- [Tworzenie maszyny Wirtualnej z systemem Linux przy użyciu wiersza polecenia platformy Azure i używanie rozszerzenia niestandardowego skryptu](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json).
- [Tworzenie maszyny Wirtualnej z systemem Windows przy użyciu programu Azure PowerShell i korzystanie z rozszerzenia niestandardowego skryptu](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json).


## <a name="packer"></a>Usługi packer
[Narzędzia packer](https://www.packer.io) automatyzuje proces kompilacji, podczas tworzenia niestandardowego obrazu maszyny Wirtualnej na platformie Azure. Narzędzia Packer umożliwia definiowanie systemu operacyjnego i uruchamiać skrypty po konfiguracji, które Dostosowywanie maszyny Wirtualnej do konkretnych potrzeb. Po skonfigurowaniu maszyny Wirtualnej są przechwytywane jako obrazu dysku zarządzanego. Narzędzia packer automatyzuje proces tworzenia maszyny Wirtualnej, sieci i zasoby magazynu źródła, uruchamiać skrypty konfiguracji, a następnie utworzyć obraz maszyny Wirtualnej.

Instrukcje:

- [Użyj narzędzia Packer, aby utworzyć obraz maszyny Wirtualnej systemu Linux na platformie Azure](../articles/virtual-machines/linux/build-image-with-packer.md).
- [Użyj narzędzia Packer, aby utworzyć obraz maszyny Wirtualnej Windows na platformie Azure](../articles/virtual-machines/windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
[Narzędzie Terraform](https://www.terraform.io) jest narzędzie automatyzacji, która pozwala definiować i tworzyć całej infrastruktury platformy Azure za pomocą jednego szablonu języka format - języka konfiguracji HashiCorp (HCL). Za pomocą programu Terraform należy zdefiniować szablonów, które automatyzują proces tworzenia sieci, magazynu i zasoby maszyny Wirtualnej dla rozwiązania danej aplikacji. Istniejące szablony programu Terraform dla innych platform za pomocą platformy Azure służy do zapewnienia spójności i uproszczenie wdrażania infrastruktury bez konieczności przekonwertować szablon usługi Azure Resource Manager.

Instrukcje:

- [Instalowanie i konfigurowanie programu Terraform z platformą Azure](../articles/virtual-machines/linux/terraform-install-configure.md).
- [Tworzenie infrastruktury platformy Azure przy użyciu programu Terraform](../articles/virtual-machines/linux/terraform-create-complete-vm.md).


## <a name="azure-automation"></a>Azure Automation
[Usługa Azure Automation](https://azure.microsoft.com/services/automation/) używa elementów runbook do przetwarzania zestawu zadań na docelowych maszynach wirtualnych. Usługa Azure Automation służy do zarządzania istniejących maszyn wirtualnych, a nie do utworzenia infrastruktury. Usługa Azure Automation można uruchamiać zarówno systemu Linux i Windows maszyn wirtualnych, jak i lokalnych maszyn wirtualnych lub fizycznych przy użyciu hybrydowego procesu roboczego elementu runbook. Elementy Runbook mogą być przechowywane w repozytorium kontroli źródła, takich jak GitHub. Te elementy runbook, następnie można uruchomić ręcznie lub zgodnie ze zdefiniowanym harmonogramem.

Usługa Azure Automation zapewnia również usługi Desired State Configuration (DSC), który służy do tworzenia definicji dla konfiguracji określonego zestawu maszyn wirtualnych. DSC temu następnie wymagana konfiguracja zostanie zastosowana, a maszyna wirtualna pozostaje zgodny. Usługa Azure Automation DSC jest uruchamiany na komputerach z systemami Windows i Linux.

Instrukcje:

- [Tworzenie elementu runbook programu PowerShell](../articles/automation/automation-first-runbook-textual-powershell.md).
- [Zarządzanie zasobami w środowisku lokalnym przy użyciu hybrydowego procesu roboczego Runbook](../articles/automation/automation-hybrid-runbook-worker.md).
- [Używanie usługi Azure Automation DSC](../articles/automation/automation-dsc-getting-started.md).


## <a name="azure-devops-services"></a>Usługa Azure DevOps Services
[Usługi Azure DevOps](https://www.visualstudio.com/team-services/) to zestaw narzędzi ułatwiających udziału i ścieżki kodu, użyj zautomatyzowane kompilacje i Utwórz pełną ciągłej integracji i potoku tworzenia aplikacji (CI/CD). Usługi Azure DevOps integruje się z programu Visual Studio i innych edytorów, aby uprościć użycia. Usługom DevOps platformy Azure można również utworzyć i skonfigurować maszyny wirtualne platformy Azure i następnie wdrażania kodu do nich.

Dowiedz się więcej o usługach:

- [Usługi Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/index?view=vsts).


## <a name="jenkins"></a>Jenkins
[Jenkins](https://www.jenkins.io) to serwer ciągłej integracji, która ułatwia wdrażanie i testowanie aplikacji oraz tworzyć zautomatyzowane potoki dostarczania kodu. Istnieją setki wtyczek rozszerzenie podstawowej platformy serwera Jenkins, a można również zintegrować z wielu innych produktów i rozwiązań za pośrednictwem elementów webhook. Możesz ręcznie zainstaluj usługę Jenkins na Maszynie wirtualnej platformy Azure, uruchamiać narzędzia Jenkins z poziomu usługi kontenera platformy Docker lub użyć wstępnie utworzonego obrazu portalu Azure Marketplace.

Instrukcje:

- [Tworzenie infrastruktury tworzenia aplikacji na Maszynę wirtualną systemu Linux na platformie Azure przy użyciu narzędzia Jenkins, GitHub i Docker](../articles/virtual-machines/linux/tutorial-jenkins-github-docker-cicd.md).


## <a name="next-steps"></a>Kolejne kroki
Istnieje wiele różnych opcji na korzystanie z narzędzi automatyzacji infrastruktury na platformie Azure. Masz swobodę, aby skorzystać z rozwiązania, która najlepiej pasuje do potrzeb i środowiska. Aby rozpocząć pracę i wypróbować niektóre z narzędzi, które są wbudowane w platformę Azure, zobacz, jak zautomatyzować dostosowywania [Linux](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) lub [Windows](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md) maszyny Wirtualnej.
