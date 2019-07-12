---
title: Omówienie maszyn wirtualnych systemu Linux na platformie Azure | Microsoft Docs
description: W tym artykule opisano działanie usług Azure Compute, Storage i Networking z maszynami wirtualnymi systemu Linux.
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: rickstercdn
manager: gwallace
editor: ''
ms.assetid: 7965a80f-ea24-4cc2-bc43-60b574101902
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: overview
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/29/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 9d33b478cb848724d0b3747761a99a1269d58b6e
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671055"
---
# <a name="azure-and-linux"></a>Azure i Linux
Platforma Microsoft Azure to rosnący zbiór zintegrowanych usług publicznych w chmurze, obejmujący maszyny wirtualne, usługi analityczne, bazodanowe, mobilne, sieciowe, magazynowe oraz internetowe &mdash; — idealne do hostowania rozwiązań.  Microsoft Azure to skalowalna platforma obliczeniowa, która pozwala na płacenie tylko za używane usługi, z których można korzystać w dowolnej chwili — bez konieczności inwestowania w sprzęt lokalny.  Platforma Azure jest gotowa wraz z Tobą do skalowania rozwiązań tak, aby sprostać wymaganiom klientów.

Jeśli znasz różne funkcje usług Amazon Web Services, możesz porównać platformę Azure z [dokumentem mapowania definicji](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) usług AWS.

## <a name="regions"></a>Regiony
Zasoby na platformie Microsoft Azure są dystrybuowane w obrębie wielu regionów geograficznych na całym świecie.  „Region” oznacza wiele centrów danych w jednym obszarze geograficznym. Platforma Azure ma obecnie (stan na sierpień 2018) 42 ogólnie dostępne regiony. Ponadto ogłoszono plany dotyczące udostępnienia kolejnych 12 regionów — co będzie stanowić więcej globalnych regionów niż jakikolwiek inny dostawca chmury. Zaktualizowaną listę istniejących i nowo zapowiedzianych regionów można znaleźć na następującej stronie:

* [Regiony platformy Azure](https://azure.microsoft.com/regions/)

## <a name="availability"></a>Dostępność
Platforma Azure oferuje najlepszą w branży umowę dotyczącą poziomu usług gwarantującą dostępność pojedynczego wystąpienia maszyny wirtualnej na poziomie 99,9% pod warunkiem wdrożenia tej maszyny wirtualnej z magazynem Premium dla wszystkich dysków.  Aby wdrożenie kwalifikowało się do standardowej umowy dotyczącej poziomu usług na poziomie 99,95%, należy wdrożyć co najmniej dwie maszyny wirtualne obsługujące obciążenie w zestawie dostępności. Zestaw dostępności gwarantuje, że maszyny wirtualne są rozproszone w wielu domenach błędów w centrach danych platformy Azure oraz wdrożone na hostach z różnymi okresami konserwacji. Pełna treść [umowy SLA dotyczącej usługi Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) wyjaśnia w całości kwestię gwarantowanej dostępności platformy Azure.

## <a name="managed-disks"></a>Dyski zarządzane

W przypadku funkcji Dyski zarządzane tworzenie konta usługi Azure Storage i zarządzanie nią odbywa się automatycznie w tle i nie trzeba martwić się o limity skalowalności konta magazynu. Wystarczy określić rozmiar dysku i warstwę wydajności (Standardowa/Premium), a platforma Azure utworzy dysk i będzie nim zarządzać. O używanym magazynie nie trzeba myśleć przy dodawaniu dysków albo skalowaniu maszyny wirtualnej w górę i w dół. Jeśli tworzysz nowe maszyny wirtualne, [użyj interfejsu wiersza polecenia platformy Azure](quick-create-cli.md) lub witryny Azure Portal, aby utworzyć maszyny wirtualne z zarządzanymi dyskami systemu operacyjnego i dyskami danych. Jeśli na maszynie wirtualnej znajdują się niezarządzane dyski, możesz [przekonwertować maszyny wirtualne, tak aby korzystały z funkcji Dyski zarządzane](convert-unmanaged-to-managed-disks.md).

Można też zarządzać własnymi obrazami niestandardowymi na jednym koncie magazynu na region platformy Azure i używać ich do tworzenia setek maszyn wirtualnych w tej samej subskrypcji. Aby uzyskać więcej informacji o dyskach zarządzanych, zobacz [Omówienie usługi Azure Managed Disks](../linux/managed-disks-overview.md).

## <a name="azure-virtual-machines--instances"></a>Maszyny wirtualne i wystąpienia platformy Azure
Platforma Microsoft Azure umożliwia uruchamianie wielu popularnych dystrybucji systemu Linux dostarczanych i utrzymywanych przez kilku partnerów.  W portalu Azure Marketplace dostępne są takie dystrybucje jak Red Hat Enterprise, CentOS, SUSE Linux Enterprise, Debian, Ubuntu, CoreOS, RancherOS, FreeBSD i wiele więcej. Firma Microsoft aktywnie współpracuje z wieloma społecznościami systemu Linux, aby dodać jeszcze więcej pozycji do listy [dystrybucji systemu Linux obsługiwanych na platformie Azure](endorsed-distros.md).

Jeśli Twoja preferowana dystrybucja systemu Linux nie jest w tej chwili dostępna w galerii, masz możliwość skorzystania z własnej maszyny wirtualnej systemu Linux po [utworzeniu i przekazaniu wirtualnego dysku twardego systemu Linux na platformie Azure](create-upload-generic.md).

Maszyny wirtualne platformy Azure umożliwiają elastyczne wdrażanie szerokiego zakresu rozwiązań obliczeniowych. Możesz wdrożyć niemal dowolne obciążenie i język w niemal dowolnym systemie operacyjnym — Windows, Linux lub innym, utworzonym przez jednego z coraz większej liczby partnerów. Nadal nie widzisz tego, czego szukasz?  Nie martw się — możesz również wprowadzić własne obrazy ze środowiska lokalnego.

## <a name="vm-sizes"></a>Rozmiary maszyn wirtualnych
[Rozmiar](sizes.md) maszyny wirtualnej zależy od obciążenia, które ma zostać uruchomione. Wybrany rozmiar ma więc wpływ na takie czynniki jak moc procesora, pamięć i przestrzeń dyskowa. W ramach platformy Azure dostępna jest szeroka gama rozmiarów umożliwiających wykorzystanie produktu do wielu różnych zastosowań.

Na platformie Azure obowiązuje [cena za godzinę](https://azure.microsoft.com/pricing/details/virtual-machines/linux/), która jest określana na podstawie rozmiaru maszyny wirtualnej i jej systemu operacyjnego. W przypadku rozpoczętych godzin opłaty są pobierane tylko za faktycznie wykorzystane minuty. Magazyn jest wyceniany oddzielnie; związane z nim opłaty są także pobierane osobno.

## <a name="automation"></a>Automatyzacja
Aby uzyskać właściwą kulturę DevOps, cała infrastruktura musi być zawarta w kodzie.  Gdy cała infrastruktura znajduje się w kodzie, można ją łatwo odtworzyć (serwery Phoenix).  Platforma Azure współpracuje ze wszystkimi najważniejszymi narzędziami automatyzacji, takimi jak Ansible, Chef, SaltStack i Puppet.  Platforma Azure oferuje również własne narzędzia automatyzacji:

* [Szablony platformy Azure](create-ssh-secured-vm-from-template.md)
* [Rozszerzenie Azure VMAccess](using-vmaccess-extension.md)

Platforma Azure wprowadza obsługę pakietu [cloud-init](https://cloud-init.io/) we wszystkich dystrybucjach systemu Linux, które go obsługują.  Obecnie maszyny wirtualne systemu Ubuntu firmy Canonical są wdrażane z domyślnie włączonym pakietem cloud-init.  Systemy RHEL, CentOS i Fedora firmy Red Hat obsługują pakiet cloud-init, ale obrazy platformy Azure obsługiwane przez firmę Red Hat nie mają obecnie zainstalowanego pakietu cloud-init.  Aby korzystać z pakietu cloud-init w systemie operacyjnym z rodziny Red Hat, należy utworzyć obraz niestandardowy z zainstalowanym pakietem cloud-init.

* [Korzystanie z pakietu cloud-init na maszynach wirtualnych platformy Azure z systemem Linux](using-cloud-init.md)

## <a name="quotas"></a>Przydziały
Każda subskrypcja platformy Azure ma domyślnie ustawiony limit przydziałów, który może mieć wpływ na wdrożenie wielu maszyn wirtualnych w projekcie. Aktualny limit dla każdej subskrypcji wynosi 20 maszyn wirtualnych na region.  Limity przydziałów można szybko i łatwo zwiększyć, przesyłając zgłoszenie pomocy technicznej.  Więcej informacji na temat limitów przydziałów znajduje się w artykułach:

* [Azure Subscription Service Limits](../../azure-subscription-service-limits.md) (Limity usług subskrypcji platformy Azure)

## <a name="partners"></a>Partnerzy
Firma Microsoft blisko współpracuje z partnerami, aby zagwarantować, że dostępne obrazy są aktualizowane i optymalizowane dla środowiska uruchomieniowego platformy Azure.  Aby uzyskać więcej informacji na temat partnerów platformy Azure, zobacz następujące linki:

* System Linux na platformie Azure — [zatwierdzone dystrybucje](endorsed-distros.md)
* SUSE — [Azure Marketplace — SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/marketplace/apps/SUSE.SLES?tab=Overview)
* Red Hat — [Azure Marketplace — Red Hat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)
* Canonical — [Azure Marketplace — Ubuntu Server 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)
* Debian — [Azure Marketplace — Debian 8 „Jessie”](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)
* FreeBSD — [Azure Marketplace — FreeBSD 10.4](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD104)
* CoreOS - [Azure Marketplace — CoreOS (wersja stabilna)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)
* RancherOS — [Azure Marketplace — RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)
* Bitnami — [Bitnami Library dla platformy Azure](https://azure.bitnami.com/)
* Mesosphere — [Azure Marketplace — Mesosphere DC/OS dla platformy Azure](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)
* Docker — [Azure Marketplace — usługa Azure Container Service z rozwiązaniem Docker Swarm](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)
* Jenkins — [Azure Marketplace — platforma CloudBees Jenkins](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cloudbees.cloudbees-core-contact)

## <a name="getting-started-with-linux-on-azure"></a>Wprowadzenie do korzystania z systemu Linux na platformie Azure
Zanim rozpoczniesz korzystanie z platformy Azure, musisz założyć konto na platformie Azure, zainstalować interfejs wiersza polecenia oraz mieć publiczny i prywatny klucz SSH.

### <a name="sign-up-for-an-account"></a>Tworzenie konta
Pierwszym krokiem korzystania z chmury Azure jest utworzenie konta platformy Azure.  Przejdź do strony [tworzenia konta Azure](https://azure.microsoft.com/pricing/free-trial/), aby rozpocząć pracę.

### <a name="install-the-cli"></a>Instalowanie interfejsu wiersza polecenia
Nowe konto platformy Azure umożliwia natychmiastowe rozpoczęcie korzystania z witryny Azure Portal, czyli internetowego panelu administracyjnego.  Aby zarządzać chmurą Azure z poziomu wiersza polecenia, musisz zainstalować narzędzie `azure-cli`.  Zainstaluj [interfejs wiersza polecenia Azure](/cli/azure/install-azure-cli) na stacji roboczej Mac lub Linux.

### <a name="create-an-ssh-key-pair"></a>Tworzenie pary kluczy SSH
Masz już teraz konto Azure, dostęp do witryny internetowej Azure Portal oraz interfejs wiersza polecenia Azure.  Kolejnym krokiem jest utworzenie pary kluczy SSH używanej do logowania SSH do systemu Linux bez konieczności używania hasła.  [Utwórz parę kluczy SSH w systemie Linux i Mac](mac-create-ssh-keys.md) w celu włączenia bezpieczniejszego logowania bez podawania hasła.

### <a name="create-a-vm-using-the-cli"></a>Tworzenie maszyny wirtualnej przy użyciu interfejsu wiersza polecenia
Tworzenie maszyny wirtualnej systemu Linux przy użyciu interfejsu wiersza polecenia to szybki sposób na wdrożenie maszyny wirtualnej bez opuszczania terminalu, w którym pracujesz.  Wszystko, co można określić w portalu internetowym, jest też dostępne za pośrednictwem flagi lub przełącznika wiersza polecenia.  

* [Tworzenie maszyny wirtualnej systemu Linux przy użyciu interfejsu wiersza polecenia](quick-create-cli.md)

### <a name="create-a-vm-in-the-portal"></a>Tworzenie maszyny wirtualnej w portalu
Tworzenie maszyny wirtualnej systemu Linux w witrynie internetowej Azure Portal umożliwia łatwe wskazywanie i klikanie różnych opcji wdrożenia.  Zamiast używać przełączników lub flag wiersza polecenia, możesz wyświetlić atrakcyjny wizualnie internetowy układ opcji i ustawień.  Wszystko, co jest dostępne poprzez interfejs wiersza polecenia, jest również dostępne w portalu.

* [Tworzenie maszyny wirtualnej Linux w portalu](quick-create-portal.md)

### <a name="log-in-using-ssh-without-a-password"></a>Logowanie przy użyciu protokołu SSH bez użycia hasła
Maszyna wirtualna działa teraz na platformie Azure, a Ty możesz już się zalogować.  Korzystanie z haseł do logowania przy użyciu protokołu SSH jest czasochłonne i mało bezpieczne.  Używanie kluczy SSH to najbezpieczniejszy i najszybszy sposób na zalogowanie się.  Gdy tworzysz maszynę wirtualną systemu Linux przy użyciu portalu lub interfejsu wiersza polecenia, masz do wyboru dwie opcje uwierzytelniania.  Jeśli wybierzesz hasło protokołu SSH, platforma Azure skonfiguruje maszynę wirtualną tak, aby umożliwiała logowanie przy użyciu haseł.  Jeśli zdecydujesz się na publiczny klucz SSH, platforma Azure skonfiguruje maszynę wirtualną tak, aby umożliwiała logowanie przy użyciu kluczy SSH, i wyłączy logowanie za pomocą haseł. Aby zabezpieczyć maszynę wirtualną systemu Linux poprzez zezwolenie wyłącznie na logowanie za pomocą klucza SSH, użyj opcji publicznego klucza SSH podczas tworzenia maszyny wirtualnej w portalu lub interfejsie wiersza polecenia.

## <a name="related-azure-components"></a>Powiązane składniki platformy Azure
## <a name="storage"></a>Magazyn
* [Wprowadzenie do usługi Microsoft Azure Storage](../../storage/common/storage-introduction.md)
* [Dodawanie dysku do maszyny wirtualnej systemu Linux przy użyciu interfejsu wiersza polecenia platformy Azure](add-disk.md)
* [Jak dołączyć dysk danych do maszyny wirtualnej w witrynie Azure Portal?](attach-disk-portal.md)

## <a name="networking"></a>Networking
* [Omówienie usługi Virtual Network](../../virtual-network/virtual-networks-overview.md)
* [Adresy IP na platformie Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Otwieranie portów dla maszyny wirtualnej systemu Linux na platformie Azure](nsg-quickstart.md)
* [Tworzenie w pełni kwalifikowanej nazwy domeny w witrynie Azure Portal](portal-create-fqdn.md)

## <a name="containers"></a>Containers
* [Maszyny wirtualne i kontenery na platformie Azure](containers.md)
* [Wprowadzenie do usługi Azure Container Service](../../container-service/container-service-intro.md)
* [Wdrażanie klastra usługi Azure Container Service](../../container-service/dcos-swarm/container-service-deployment.md)

## <a name="next-steps"></a>Następne kroki
Wiesz już teraz, jak działa system Linux na platformie Azure.  Następnym krokiem jest sprawdzenie tego w praktyce przez utworzenie kilku maszyn wirtualnych.

* [Przejrzyj naszą stale rozwijaną listę przykładowych skryptów do wykonywania codziennych zadań za pomocą interfejsu wiersza polecenia platformy Azure](cli-samples.md)
