---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/11/2019
ms.author: cynthn
ms.openlocfilehash: 4259868ff3b3c9ca9f9818532acd7e865e0300d7
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77205754"
---
Aby utworzyć i zarządzać maszyn wirtualnych (VM) w sposób ciągły na dużą skalę, zwykle jest pożądane jakąś formę automatyzacji. Istnieje wiele narzędzi i rozwiązań, które umożliwiają automatyzację wdrażania całej infrastruktury platformy Azure i zarządzanie cyklem życia. W tym artykule przedstawiono niektóre z narzędzi automatyzacji infrastruktury, które można użyć na platformie Azure. Te narzędzia często mieści się w jednej z następujących metod:

- Automatyzuj konfigurację maszyn wirtualnych
    - Narzędzia obejmują [rozwiązania ansible](#ansible), [Chef](#chef)i [Puppet](#puppet).
    - Narzędzia specyficzne dla dostosowywania maszyn wirtualnych obejmują funkcję [Cloud-init](#cloud-init) dla maszyn wirtualnych z systemem Linux, [konfigurację żądanego stanu programu PowerShell (DSC)](#powershell-dsc)i [rozszerzenie niestandardowego skryptu platformy Azure](#azure-custom-script-extension) dla wszystkich maszyn wirtualnych platformy Azure.
 
- Automatyzowanie zarządzania infrastrukturą
    - Narzędzia obejmują [pakiet pakujący](#packer) do automatyzowania niestandardowych kompilacji obrazów maszyn wirtualnych i [Terraform](#terraform) w celu zautomatyzowania procesu tworzenia infrastruktury.
    - [Azure Automation](#azure-automation) mogą wykonywać działania na platformie Azure i w infrastrukturze lokalnej.

- Automatyzowanie wdrażania aplikacji i dostarczanie
    - Przykłady obejmują [Azure DevOps Services](#azure-devops-services) i [Jenkins](#jenkins).

## <a name="ansible"></a>Ansible
[Rozwiązania ansible](https://www.ansible.com/) to aparat automatyzacji służący do zarządzania konfiguracją, tworzenia maszyn wirtualnych lub wdrażania aplikacji. Rozwiązanie Ansible używają modelu bez agenta, zazwyczaj przy użyciu kluczy SSH do uwierzytelniania i zarządzania komputerami docelowymi. Zadania konfiguracji są definiowane w elementy playbook, wprowadzając szereg modułów Ansible dostępne do wykonywania określonych zadań. Aby uzyskać więcej informacji, zobacz [jak działa rozwiązania ansible](https://www.ansible.com/how-ansible-works).

Instrukcje:

- [Zainstaluj i skonfiguruj rozwiązania ansible w systemie Linux do użycia z platformą Azure](../articles/ansible/ansible-install-configure.md).
- [Utwórz maszynę wirtualną z systemem Linux](../articles/ansible/ansible-create-vm.md).
- [Zarządzanie maszyną wirtualną z systemem Linux](../articles/ansible/ansible-manage-linux-vm.md).


## <a name="chef"></a>Chef
[Chef](https://www.chef.io/) to platforma automatyzacji, która ułatwia definiowanie sposobu konfigurowania, wdrażania i zarządzania infrastrukturą. Dodatkowe składniki dołączana Chef, Habitat do automatyzacji cyklu życia aplikacji, a nie na infrastrukturze i Chef InSpec ułatwiające Automatyzowanie zgodności z zabezpieczeniami i wymagania dotyczące zasad. Program chef klienci zostali zainstalowani na komputerach docelowych, z co najmniej jeden centralny Chef serwerami, które przechowywać i zarządzać nimi konfiguracji. Aby uzyskać więcej informacji, zobacz [Omówienie Chef](https://docs.chef.io/chef_overview.html).

Instrukcje:

- [Wdróż automatyzację Chef z poziomu portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview).
- [Zainstaluj program Chef w systemie Windows i Utwórz maszyny wirtualne platformy Azure](../articles/virtual-machines/windows/chef-automation.md).


## <a name="puppet"></a>Puppet
[Puppet](https://www.puppet.com) to platforma automatyzacji gotowa dla przedsiębiorstw, która obsługuje proces dostarczania i wdrażania aplikacji. Agenci są zainstalowani na komputerach docelowych, aby zezwolić na główny Puppet, aby uruchomić manifestów, które definiują pożądaną konfigurację infrastruktury platformy Azure i maszyn wirtualnych. Puppet można zintegrować z innymi rozwiązaniami, takimi jak Jenkins i usługi GitHub dla przepływu pracy devops ulepszone. Aby uzyskać więcej informacji, zobacz [jak działa Puppet](https://puppet.com/products/how-puppet-works).

Instrukcje:

- [Wdróż Puppet z witryny Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/puppet.puppet-enterprise-2017-2?tab=Overview).


## <a name="cloud-init"></a>Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) to powszechnie używana metoda dostosowywania maszyny wirtualnej z systemem Linux podczas jej pierwszego rozruchu. Za pomocą pakietu cloud-init można instalować pakiety i zapisywać pliki lub konfigurować użytkowników i zabezpieczenia. Pakiet cloud-init jest wywoływana podczas początkowego rozruchu, dlatego są żadne dodatkowe kroki ani agenci wymagane do zastosowania konfiguracji.  Aby uzyskać więcej informacji na temat poprawnego formatowania plików `#cloud-config`, zobacz [witrynę dokumentacji Cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  Pliki `#cloud-config` są zakodowane w formacie base64.

Pakiet cloud-init działa również w różnych dystrybucjach. Przykładowo nie używa się poleceń **apt-get install** lub **yum install** do zainstalowania pakietu. Zamiast tego możesz zdefiniować listę pakietów do zainstalowania. Pakiet cloud-init automatycznie używa natywnego narzędzia do zarządzania pakietami dla wybranej dystrybucji.

Aktywnie współpracujemy z partnerami zalecanych dystrybucji systemu Linux, aby mogła mieć pakietu cloud-init, włączone obrazów dostępnych w witrynie Azure marketplace. Te obrazy upewnij wdrożenia pakietu cloud-init, a konfiguracje bezproblemowej współpracy z maszyn wirtualnych i zestawów skalowania maszyn wirtualnych. Dowiedz się więcej na temat pakietu cloud-init w systemie Azure:

- [Obsługa inicjowania w chmurze dla maszyn wirtualnych z systemem Linux na platformie Azure](../articles/virtual-machines/linux/using-cloud-init.md)
- [Wypróbuj samouczek dotyczący zautomatyzowanej konfiguracji maszyny wirtualnej przy użyciu funkcji Cloud-init](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>Rozszerzenie DSC programu PowerShell
[Konfiguracja żądanego stanu programu PowerShell (DSC)](/powershell/scripting/dsc/overview/overview) to platforma zarządzania służąca do definiowania konfiguracji maszyn docelowych. Konfiguracja DSC może być również używana w systemie Linux za pomocą [serwera infrastruktury Open Management Infrastructure (OMI)](https://collaboration.opengroup.org/omi/).

Konfiguracje DSC definiują, co należy zainstalować na komputerze oraz sposób konfigurowania hosta. Aparat lokalnego Configuration Manager (LCM) działa w każdym węźle docelowym, która przetwarza żądane akcje na podstawie konfiguracji wypychanie. Serwer ściągania jest usługą internetową, która działa na hoście centralnej do przechowywania konfiguracji DSC i skojarzonych zasobów. Serwerze ściągania komunikuje się z aparatem LCM na każdym hoście docelowym, podaj wymagane konfiguracje i sporządzić raport na temat zgodności.

Instrukcje:

- [Utwórz podstawową konfigurację DSC](/powershell/scripting/dsc/quickstarts/website-quickstart).
- [Skonfiguruj serwer ściągający DSC](/powershell/scripting/dsc/pull-server/pullserver).
- [Użyj DSC dla systemu Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Rozszerzenie niestandardowego skryptu platformy Azure
Rozszerzenie niestandardowego skryptu platformy Azure dla systemu [Linux](../articles/virtual-machines/linux/extensions-customscript.md) lub [Windows](../articles/virtual-machines/windows/extensions-customscript.md) pobiera i wykonuje skrypty na maszynach wirtualnych platformy Azure. Podczas tworzenia maszyny Wirtualnej lub dowolnym momencie po maszyny Wirtualnej jest używany, można użyć rozszerzenia. 

Skrypty można pobrać z usługi Azure storage lub w dowolnej lokalizacji publicznej, takich jak repozytorium GitHub. Za pomocą rozszerzenia niestandardowego skryptu można pisać skrypty w dowolnym języku, który jest uruchamiany na źródłowej maszynie Wirtualnej. Skrypty te można zainstalować aplikacje lub konfigurację maszyny Wirtualnej zgodnie z potrzebami. Aby zabezpieczyć poświadczenia, poufne informacje, takie jak hasła mogą być przechowywane w chronionym konfiguracji. Te poświadczenia są odszyfrowane tylko wewnątrz maszyny Wirtualnej.

Instrukcje:

- [Utwórz maszynę wirtualną z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure i Użyj niestandardowego rozszerzenia skryptu](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json).
- [Utwórz maszynę wirtualną z systemem Windows przy użyciu Azure PowerShell i Użyj niestandardowego rozszerzenia skryptu](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json).


## <a name="packer"></a>Usługi packer
[Pakujący](https://www.packer.io) automatyzuje proces kompilacji podczas tworzenia niestandardowego obrazu maszyny wirtualnej na platformie Azure. Narzędzia Packer umożliwia definiowanie systemu operacyjnego i uruchamiać skrypty po konfiguracji, które Dostosowywanie maszyny Wirtualnej do konkretnych potrzeb. Po skonfigurowaniu maszyny Wirtualnej są przechwytywane jako obrazu dysku zarządzanego. Narzędzia packer automatyzuje proces tworzenia maszyny Wirtualnej, sieci i zasoby magazynu źródła, uruchamiać skrypty konfiguracji, a następnie utworzyć obraz maszyny Wirtualnej.

Instrukcje:

- [Użyj programu Packer, aby utworzyć obraz maszyny wirtualnej z systemem Linux na platformie Azure](../articles/virtual-machines/linux/build-image-with-packer.md).
- [Użyj programu Packer, aby utworzyć obraz maszyny wirtualnej z systemem Windows na platformie Azure](../articles/virtual-machines/windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
[Terraform](https://www.terraform.io) to narzędzie do automatyzacji, które umożliwia definiowanie i tworzenie całej infrastruktury platformy Azure przy użyciu jednego szablonu języka formatowania — HashiCorp Configuration Language (HCL). Za pomocą programu Terraform należy zdefiniować szablonów, które automatyzują proces tworzenia sieci, magazynu i zasoby maszyny Wirtualnej dla rozwiązania danej aplikacji. Istniejące szablony programu Terraform dla innych platform za pomocą platformy Azure służy do zapewnienia spójności i uproszczenie wdrażania infrastruktury bez konieczności przekonwertować szablon usługi Azure Resource Manager.

Instrukcje:

- [Zainstaluj i skonfiguruj Terraform z platformą Azure](../articles/virtual-machines/linux/terraform-install-configure.md).
- [Utwórz infrastrukturę platformy Azure z usługą Terraform](../articles/virtual-machines/linux/terraform-create-complete-vm.md).


## <a name="azure-automation"></a>Azure Automation
[Azure Automation](https://azure.microsoft.com/services/automation/) używa elementów Runbook do przetwarzania zestawu zadań dla docelowych maszyn wirtualnych. Usługa Azure Automation służy do zarządzania istniejących maszyn wirtualnych, a nie do utworzenia infrastruktury. Usługa Azure Automation można uruchamiać zarówno systemu Linux i Windows maszyn wirtualnych, jak i lokalnych maszyn wirtualnych lub fizycznych przy użyciu hybrydowego procesu roboczego elementu runbook. Elementy Runbook mogą być przechowywane w repozytorium kontroli źródła, takich jak GitHub. Te elementy runbook, następnie można uruchomić ręcznie lub zgodnie ze zdefiniowanym harmonogramem.

Usługa Azure Automation zapewnia również usługi Desired State Configuration (DSC), który służy do tworzenia definicji dla konfiguracji określonego zestawu maszyn wirtualnych. DSC temu następnie wymagana konfiguracja zostanie zastosowana, a maszyna wirtualna pozostaje zgodny. Usługa Azure Automation DSC jest uruchamiany na komputerach z systemami Windows i Linux.

Instrukcje:

- [Utwórz element Runbook programu PowerShell](../articles/automation/automation-first-runbook-textual-powershell.md).
- [Użyj hybrydowego procesu roboczego elementu Runbook do zarządzania zasobami lokalnymi](../articles/automation/automation-hybrid-runbook-worker.md).
- [Użyj Azure Automation DSC](../articles/automation/automation-dsc-getting-started.md).


## <a name="azure-devops-services"></a>Usługa Azure DevOps Services
[Azure DevOps Services](https://www.visualstudio.com/team-services/) to pakiet narzędzi, które ułatwiają udostępnianie i śledzenie kodu, korzystanie z zautomatyzowanych kompilacji oraz tworzenie kompletnego potoku ciągłej integracji i tworzenia oprogramowania (Ci/CD). Usługi Azure DevOps integruje się z programu Visual Studio i innych edytorów, aby uprościć użycia. Usługom DevOps platformy Azure można również utworzyć i skonfigurować maszyny wirtualne platformy Azure i następnie wdrażania kodu do nich.

Dowiedz się więcej o usługach:

- [Azure DevOps Services](https://docs.microsoft.com/azure/devops/user-guide/index?view=vsts).


## <a name="jenkins"></a>Jenkins
[Jenkins](https://www.jenkins.io) to serwer ciągłej integracji, który pomaga wdrażać i testować aplikacje oraz tworzyć zautomatyzowane potoki do dostarczania kodu. Istnieją setki wtyczek rozszerzenie podstawowej platformy serwera Jenkins, a można również zintegrować z wielu innych produktów i rozwiązań za pośrednictwem elementów webhook. Możesz ręcznie zainstaluj usługę Jenkins na Maszynie wirtualnej platformy Azure, uruchamiać narzędzia Jenkins z poziomu usługi kontenera platformy Docker lub użyć wstępnie utworzonego obrazu portalu Azure Marketplace.

Instrukcje:

- [Tworzenie infrastruktury deweloperskiej na maszynie wirtualnej z systemem Linux na platformie Azure przy użyciu usług Jenkins, GitHub i Docker](../articles/virtual-machines/linux/tutorial-jenkins-github-docker-cicd.md).


## <a name="next-steps"></a>Następne kroki
Istnieje wiele różnych opcji na korzystanie z narzędzi automatyzacji infrastruktury na platformie Azure. Masz swobodę, aby skorzystać z rozwiązania, która najlepiej pasuje do potrzeb i środowiska. Aby rozpocząć pracę i wypróbować niektóre narzędzia wbudowane w platformę Azure, zobacz jak zautomatyzować Dostosowywanie maszyny wirtualnej z systemem [Linux](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) lub [Windows](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md) .
