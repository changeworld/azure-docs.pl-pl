# <a name="use-infrastructure-automation-tools-with-virtual-machines-in-azure"></a>Użyj narzędzi automatyzacji infrastruktury z maszynami wirtualnymi na platformie Azure
Tworzenie i zarządzanie nimi Azure maszynach wirtualnych (VM) w spójny sposób na dużą skalę, wymagane jest zwykle jakiegoś automatyzacji. Istnieje wiele narzędzi i rozwiązań, dzięki którym można zautomatyzować wdrożenie całej infrastruktury platformy Azure i zarządzanie cyklem życia. W tym artykule przedstawiono niektóre narzędzia automatyzacji infrastruktury, które można użyć w systemie Azure. Te narzędzia zazwyczaj mieści się w na jedną z następujących metod:

- Zautomatyzować konfigurowanie maszyn wirtualnych
    - Narzędzia obejmują [Ansible](#ansible), [Chef](#chef), i [Puppet](#puppet).
    - Narzędzia specyficzne dla dostosowania maszyny Wirtualnej obejmują [init chmury](#cloud-init) dla maszyn wirtualnych systemu Linux, [konfiguracji żądanego stanu środowiska PowerShell (DSC)](#powershell-dsc)i [Azure niestandardowe rozszerzenie skryptu](#azure-custom-script-extension) dla wszystkich Maszyny wirtualne platformy Azure.
 
- Automatyczne zarządzanie infrastrukturą
    - Narzędzia obejmują [pakujący](#packer) do automatyzacji niestandardowej maszyny Wirtualnej tworzy obraz, i [Terraform](#terraform) można zautomatyzować proces kompilacji infrastruktury.
    - [Automatyzacja Azure](#azure-automation) może wykonywać akcje w infrastrukturze Azure i lokalnymi.

- Automatyzacja wdrażania aplikacji i dostarczania
    - Przykłady obejmują [Visual Studio Team Services](#visual-studio-team-services) i [Wpięć](#jenkins).


## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/) jest aparatem automatyzacji zarządzania konfiguracją, tworzenie maszyny Wirtualnej lub wdrożenia aplikacji. Ansible używają modelu bez agenta, zwykle z kluczy SSH do uwierzytelniania i zarządzania komputerami docelowymi. Zadania konfiguracji są definiowane w playbooks z liczbą modułów Ansible dostępne do wykonywania określonych zadań. Aby uzyskać więcej informacji, zobacz [działa jak Ansible](https://www.ansible.com/how-ansible-works).

Instrukcje:

- [Instalowanie i konfigurowanie Ansible w systemie Linux do użycia z usługą Azure](../articles/virtual-machines/linux/ansible-install-configure.md).
- [Tworzenie podstawowej maszyny Wirtualnej](../articles/virtual-machines/linux/ansible-create-vm.md).
- [Utwórz pełną środowisko maszyny Wirtualnej w tym obsługi zasobów](../articles/virtual-machines/linux/ansible-create-complete-vm.md).


## <a name="chef"></a>Chef
[Chef](https://www.chef.io/) to platforma automatyzacji, która umożliwia definiowanie konfiguracji infrastruktury, wdrażane i zarządzane. Dodatkowe składniki dołączony siedliska Chef automatyzacji cyklem życia aplikacji zamiast infrastruktury, a Chef InSpec ułatwiające automatyzację przestrzegania zasad wymagań dotyczących zabezpieczeń i. Klienci chef są zainstalowane na komputerach docelowych przy użyciu co najmniej jeden centralnej Chef serwerów magazynu i zarządzać związanymi z konfiguracji. Aby uzyskać więcej informacji, zobacz [omówienie Chef](https://docs.chef.io/chef_overview.html).

Instrukcje:

- [Wdrażanie Chef zautomatyzować z poziomu portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview).
- [Instalowanie Chef w systemie Windows i Tworzenie maszyn wirtualnych platformy Azure](../articles/virtual-machines/windows/chef-automation.md).


## <a name="puppet"></a>Puppet
[Puppet](https://www.puppet.com) to platforma automatyzacji gotowe enterprise, która obsługuje proces dostarczania i wdrażania aplikacji. Agenci są zainstalowani na komputerach docelowych umożliwiających wzorca Puppet, aby uruchomić manifestów, które definiują żądanej konfiguracji infrastruktury platformy Azure i maszyn wirtualnych. Puppet można zintegrować z innych rozwiązań, takich jak Wpięć i GitHub ulepszone devops przepływu pracy. Aby uzyskać więcej informacji, zobacz [działa jak Puppet](https://puppet.com/product/how-puppet-works).

Instrukcje:

- [Wdrażanie Puppet z poziomu portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/puppet.puppet-enterprise-2016-1?tab=Overview).


## <a name="cloud-init"></a>Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) to powszechnie używana metoda dostosowywania maszyny wirtualnej z systemem Linux podczas jej pierwszego rozruchu. Za pomocą pakietu cloud-init można instalować pakiety i zapisywać pliki lub konfigurować użytkowników i zabezpieczenia. Ponieważ init chmury jest wywoływana podczas początkowego procesu rozruchu, nie są żadne dodatkowe kroki lub agentów wymaganych do zastosowania konfiguracji.  Aby uzyskać więcej informacji na temat sposobu poprawnie sformatowana Twojej `#cloud-config` plików, zobacz [witryna dokumentacji usługi chmury init](http://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` pliki są plikami tekstowymi zakodowane w formacie base64.

Pakiet cloud-init działa również w różnych dystrybucjach. Przykładowo nie używa się poleceń **apt-get install** lub **yum install** do zainstalowania pakietu. Zamiast tego możesz zdefiniować listę pakietów do zainstalowania. Pakiet cloud-init automatycznie używa natywnego narzędzia do zarządzania pakietami dla wybranej dystrybucji.

 Obecnie pracujemy z partnerami potwierdzony distro systemu Linux w celu dostępnych obrazów włączone inicjowania chmury w portalu Azure marketplace. Te obrazy upewnij wdrożeń chmury init i konfiguracje współpracuje z maszynami wirtualnymi i zestawy skalowania maszyny wirtualnej. W poniższej tabeli przedstawiono bieżącej dostępności obrazów init chmury, włączone na platformie Azure:

| Wydawca | Oferta | SKU | Wersja | gotowe init chmury
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |16.04-LTS |najnowsza |tak | 
|Canonical |UbuntuServer |14.04.5-LTS |najnowsza |tak |
|CoreOS |CoreOS |Stable |najnowsza |tak |
|OpenLogic |CentOS |7-CI |najnowsza |wersja zapoznawcza |
|RedHat |RHEL |7-RAW-CI |najnowsza |wersja zapoznawcza |

Dowiedz się więcej informacji na temat inicjowania chmurze na platformie Azure:

- [Chmura inicjowania obsługi dla maszyn wirtualnych systemu Linux na platformie Azure](../articles/virtual-machines/linux/using-cloud-init.md)
- [Spróbuj samouczek dotyczący automatycznych konfiguracji maszyny Wirtualnej przy użyciu chmury init](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>DSC środowiska PowerShell
[Konfiguracji żądanego stanu środowiska PowerShell (DSC)](https://msdn.microsoft.com/powershell/dsc/overview) to platforma zarządzania, aby zdefiniować konfigurację komputerów docelowych. DSC można również w systemie Linux za pomocą [Open Management Infrastructure (OMI) serwera](https://collaboration.opengroup.org/omi/).

Konfiguracji DSC zdefiniować, co należy zainstalować na maszynie oraz sposób konfigurowania hosta. Aparat lokalnego Configuration Manager (LCM) działa na każdym węźle docelowym, który przetwarza żądane akcje wciśnięcia konfiguracji. Serwerem ściągania to usługa sieci web uruchomioną na hoście centralnej do przechowywania konfiguracji DSC i skojarzonych zasobów. Z serwerem ściągania komunikuje się z aparatem LCM na każdym hoście docelowym, aby zapewnić wymagane konfiguracje i raport dotyczący zgodności.

Instrukcje:

- [Utwórz podstawową konfigurację DSC](https://msdn.microsoft.com/powershell/dsc/quickstart).
- [Konfigurowanie serwera ściągania usługi Konfiguracja DSC](https://msdn.microsoft.com/powershell/dsc/pullserver).
- [Użyj konfiguracji DSC dla systemu Linux](https://msdn.microsoft.com/powershell/dsc/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Azure niestandardowe rozszerzenie skryptu
Niestandardowe rozszerzenie skryptu Azure dla [Linux](../articles/virtual-machines/linux/extensions-customscript.md) lub [Windows](../articles/virtual-machines/windows/extensions-customscript.md) pobiera i uruchamia skrypty na maszynach wirtualnych Azure. Podczas tworzenia maszyny Wirtualnej lub dowolnym momencie po maszyna wirtualna jest używana, można użyć rozszerzenia. 

Skrypty można pobrać z magazynu Azure lub z dowolnej lokalizacji publicznej, takich jak repozytorium GitHub. Z niestandardowe rozszerzenie skryptu można napisać skrypty w dowolnym języku, który jest uruchamiany na źródłowej maszyny Wirtualnej. Skrypty te można zainstalować aplikacje i konfigurowanie maszyny Wirtualnej zgodnie z potrzebami. Aby zabezpieczyć poświadczenia, poufne informacje, takie jak hasła mogą być przechowywane w chronionej konfiguracji. Te poświadczenia są odszyfrowane tylko wewnątrz maszyny Wirtualnej.

Instrukcje:

- [Utwórz Maszynę wirtualną systemu Linux z wiersza polecenia platformy Azure i użyć niestandardowe rozszerzenie skryptu](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json).
- [Utwórz maszynę Wirtualną z systemem Windows przy użyciu programu Azure PowerShell i użyć niestandardowe rozszerzenie skryptu](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json).


## <a name="packer"></a>Pakujący
[Pakujący](https://www.packer.io) automatyzuje proces kompilacji podczas tworzenia niestandardowego obrazu maszyny Wirtualnej na platformie Azure. Pakujący umożliwia definiowanie systemu operacyjnego i uruchamiać skrypty po konfiguracji, które dostosować maszyny Wirtualnej do określonych potrzeb. Po skonfigurowaniu maszyny Wirtualnej jest następnie przechwytywany jako obraz dysku zarządzanego. Pakujący automatyzuje proces, aby utworzyć źródło maszyny Wirtualnej, sieci i zasoby magazynu, uruchom skrypty do konfiguracji, a następnie utwórz obraz maszyny Wirtualnej.

Instrukcje:

- [Tworzenie obrazu maszyny Wirtualnej systemu Linux na platformie Azure za pomocą pakujący](../articles/virtual-machines/linux/build-image-with-packer.md).
- [Tworzenie obrazu maszyny Wirtualnej systemu Windows na platformie Azure za pomocą pakujący](../articles/virtual-machines/windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
[Terraform](https://www.terraform.io) to narzędzie automatyzacji, które pozwala zdefiniować i Utwórz całej infrastruktury platformy Azure z jednego szablonu język formatu — Language konfiguracji HashiCorp listy (zgodności sprzętu). Z Terraform należy zdefiniować szablony automatyzujących proces tworzenia sieci, magazynu i zasobów maszyny Wirtualnej dla rozwiązania danej aplikacji. Istniejących szablonów Terraform dla innych platform z platformy Azure, służy do zapewnienia spójności i uprościć wdrażanie infrastruktury bez konieczności konwertować na szablon usługi Azure Resource Manager.

Instrukcje:

- [Instalowanie i konfigurowanie Terraform z platformy Azure,](../articles/virtual-machines/linux/terraform-install-configure.md).
- [Tworzenie infrastruktury platformy Azure z Terraform](../articles/virtual-machines/linux/terraform-create-complete-vm.md).


## <a name="azure-automation"></a>Azure Automation
[Automatyzacja Azure](https://azure.microsoft.com/services/automation/) używa elementów runbook do przetwarzania zestawu zadań na tych maszynach docelowych. Automatyzacja Azure jest używana do zarządzania istniejących maszyn wirtualnych, a nie do utworzenia infrastruktury. Automatyzacja Azure można spotkać zarówno systemu Linux i maszyn wirtualnych systemu Windows, jak i lokalnych maszyn wirtualnych lub fizycznych z hybrydowy proces roboczy elementu runbook. Elementy Runbook mogą być przechowywane w repozytorium kontroli źródła, takich jak usługi GitHub. Te elementy runbook mogą następnie uruchomić ręcznie lub zgodnie z harmonogramem zdefiniowanym.

Usługi Automatyzacja Azure umożliwia również usługa konfiguracji żądanego stanu (DSC), która służy do tworzenia definicji dla konfiguracji podany zestaw maszyn wirtualnych. DSC następnie gwarantuje, że zastosowano wymaganej konfiguracji i maszyna wirtualna pozostaje spójna. Konfiguracja DSC automatyzacji Azure działa na komputerach z systemami Windows i Linux.

Instrukcje:

- [Tworzenie elementu runbook programu PowerShell](../articles/automation/automation-first-runbook-textual-powershell.md).
- [Używanie hybrydowy proces roboczy elementu Runbook do zarządzania zasobami lokalnymi](../articles/automation/automation-hybrid-runbook-worker.md).
- [Użyj usługi Azure Automation DSC](../articles/automation/automation-dsc-getting-started.md).


## <a name="visual-studio-team-services"></a>Visual Studio Team Services
[Zespół usługi](https://www.visualstudio.com/team-services/) to zestaw narzędzi, które ułatwiają udziału i ścieżki kodu, użyj kompilacjach zautomatyzowanych i utworzyć pełną ciągłej integracji i rozwoju (CI/CD) potoku. Usługi Team Services integruje się z programu Visual Studio i innych edytory uprościć użycia. Usługi Team Services może także utworzyć i skonfigurować maszyny wirtualne platformy Azure i wdrożyć kod do nich.

Instrukcje:

- [Tworzenie potoku ciągłej integracji z usługami zespołu](../articles/virtual-machines/windows/tutorial-vsts-iis-cicd.md).


## <a name="jenkins"></a>Jenkins
[Wpięć](https://www.jenkins.io) jest serwerem ciągłej integracji, który ułatwia wdrażanie i testowanie aplikacji i tworzenie zautomatyzowanych potoki dostarczenie kodu. Setki wtyczek rozszerzenie podstawowej platformy Wpięć, a także można zintegrować z wielu innych produktów i rozwiązań za pomocą elementów webhook. Można ręcznie zainstalować Wpięć na maszynie Wirtualnej platformy Azure, uruchom Wpięć z znajdujące się w kontenerze Docker lub użyć wbudowanych obrazu portalu Azure Marketplace.

Instrukcje:

- [Tworzenie infrastruktury programowanie na maszynie Wirtualnej systemu Linux na platformie Azure z Wpięć, GitHub i Docker](../articles/virtual-machines/linux/tutorial-jenkins-github-docker-cicd.md).


## <a name="next-steps"></a>Kolejne kroki
Istnieje wiele różnych opcji za pomocą narzędzi automatyzacji infrastruktury na platformie Azure. Masz swobody za pomocą rozwiązania, która najlepiej pasuje do środowiska i potrzeb użytkowników. Do rozpoczęcia pracy i spróbuj niektóre narzędzia, które są wbudowane w platformę Azure, zobacz temat Jak zautomatyzować dostosowywania [Linux](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) lub [Windows](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md) maszyny Wirtualnej.
