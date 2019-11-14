---
title: Przegląd Windows Virtual Machines — Azure
description: Dowiedz się więcej na temat tworzenia maszyn wirtualnych z systemem Windows na platformie Azure oraz zarządzania nimi.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: fbae9c8e-2341-4ed0-bb20-fd4debb2f9ca
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: fe62f67071b77c464d5b3b8649d16db597d9ab21
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033035"
---
# <a name="overview-of-windows-virtual-machines-in-azure"></a>Omówienie maszyn wirtualnych z systemem Windows na platformie Azure

Usługa Azure Virtual Machines (VM) to jeden z wielu [skalowalnych zasobów obliczeniowych dostępnych na żądanie](/azure/architecture/guide/technology-choices/compute-decision-tree), które są oferowane na platformie Azure. W większości przypadków maszynę wirtualną należy wybrać wtedy, gdy potrzebna jest większa kontrola nad środowiskiem obliczeniowym niż ta, jaką oferują inne opcje. Ten artykuł zawiera informacje o kwestiach, jakie należy rozważyć przed przystąpieniem do tworzenia maszyny wirtualnej. Opisano w nim także tworzenie maszyny wirtualnej i zarządzanie nią.

Maszyna wirtualna Azure umożliwia swobodne korzystanie z wirtualizacji bez konieczności kupowania i utrzymywania fizycznego sprzętu potrzebnego do jej działania. Nadal niezbędna jest jednak konserwacja maszyny wirtualnej — konfigurowanie i instalowanie jej oprogramowania, a także instalowanie poprawek tego oprogramowania.

Z maszyn wirtualnych na platformie Azure można korzystać na różne sposoby. Przykłady to:

* **Tworzenie i testowanie oprogramowania** — maszyny wirtualne umożliwiają szybkie i łatwe utworzenie komputera o określonej konfiguracji wymaganej do tworzenia kodu oraz testowania aplikacji.
* **Aplikacje w chmurze** — jako że zapotrzebowanie na aplikację może zmieniać się w czasie, uzasadnione ekonomicznie może okazać się uruchomienie jej na maszynie wirtualnej na platformie Azure. Jeśli zajdzie potrzeba użycia dodatkowych maszyn wirtualnych, wystarczy je opłacić. Gdy dodatkowe maszyny staną się zbędne, można je wyłączyć.
* **Rozszerzone centrum danych** — maszyny wirtualne w sieci wirtualnej na platformie Azure można zostać z łatwością połączyć z siecią organizacji.

Liczbę maszyn wirtualnych używanych przez aplikację można dowolnie i bez ograniczeń zwiększać odpowiednio do potrzeb.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Co należy wziąć pod uwagę przed utworzeniem maszyny wirtualnej?
Podczas tworzenia infrastruktury aplikacji na platformie Azure należy zawsze wziąć pod uwagę wiele różnych [zagadnień projektowych](/azure/architecture/reference-architectures/virtual-machines-windows?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Kwestie dotyczące maszyn wirtualnych, które należy rozważyć, to między innymi:

* nazwy zasobów aplikacji;
* lokalizacja, w której są przechowywane zasoby;
* rozmiar maszyny wirtualnej;
* maksymalna liczba maszyn wirtualnych, które można utworzyć;
* system operacyjny uruchomiony na maszynie wirtualnej;
* konfiguracja maszyny wirtualnej po jej uruchomieniu;
* powiązane zasoby niezbędne do działania maszyny wirtualnej.

### <a name="naming"></a>Nazewnictwo
Maszyna wirtualna ma przypisaną do niej [nazwę](/azure/architecture/best-practices/resource-naming), a także nazwę komputera skonfigurowaną jako element systemu operacyjnego. Nazwa maszyny wirtualnej może zawierać maksymalnie 15 znaków.

Jeśli dysk systemu operacyjnego zostanie utworzony na platformie Azure, nazwa komputera jest taka sama jak nazwa maszyny wirtualnej. Nazwy mogą się różnić w przypadku [przesłania i użycia własnego obrazu](upload-generalized-managed.md), który zawiera skonfigurowany wcześniej system operacyjny, oraz wykorzystania go do utworzenia maszyny wirtualnej. Jeśli zostanie przesłany własny plik obrazu, zalecamy ustawienie takiej samej nazwy dla komputera w systemie operacyjnym i dla maszyny wirtualnej.

### <a name="locations"></a>Lokalizacje
Wszystkie zasoby tworzone na platformie Azure są dystrybuowane w obrębie wielu [regionów geograficznych](https://azure.microsoft.com/regions/) na całym świecie. Zazwyczaj podczas tworzenia maszyny wirtualnej region określa się mianem **lokalizacji**. W przypadku maszyny wirtualnej lokalizacja określa miejsce, w którym są przechowywane wirtualne dyski twarde.

W poniższej tabeli przedstawiono wybrane metody uzyskania dostępu do listy dostępnych lokalizacji.

| Metoda | Opis |
| --- | --- |
| Azure Portal |Wybór lokalizacji z listy podczas tworzenia maszyny wirtualnej. |
| Azure PowerShell |Użyj polecenia [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation). |
| Interfejs API REST |Użycie operacji [wyświetlania listy lokalizacji](https://docs.microsoft.com/rest/api/resources/subscriptions). |
| Interfejs wiersza polecenia platformy Azure |Użyj operacji [az account list-locations](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest). |

### <a name="vm-size"></a>Rozmiar maszyny wirtualnej
[Rozmiar](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) maszyny wirtualnej zależy od obciążenia, które ma zostać uruchomione. Wybrany rozmiar ma więc wpływ na takie czynniki jak moc procesora, pamięć i przestrzeń dyskowa. W ramach platformy Azure dostępna jest szeroka gama rozmiarów umożliwiających wykorzystanie produktu do wielu różnych zastosowań.

Na platformie Azure obowiązuje [cena za godzinę](https://azure.microsoft.com/pricing/details/virtual-machines/windows/), która jest określana na podstawie rozmiaru maszyny wirtualnej i jej systemu operacyjnego. W przypadku rozpoczętych godzin opłaty są pobierane tylko za faktycznie wykorzystane minuty. Magazyn jest wyceniany oddzielnie; związane z nim opłaty są także pobierane osobno.

### <a name="vm-limits"></a>Limity maszyn wirtualnych
Subskrypcje mają domyślne [limity przydziałów](../../azure-subscription-service-limits.md), które mogą mieć wpływ na wdrożenie wielu maszyn wirtualnych w projekcie. Aktualny limit dla każdej subskrypcji wynosi 20 maszyn wirtualnych na region. Limity można zwiększyć, [wypełniając odpowiednio bilet pomocy technicznej](../../azure-supportability/resource-manager-core-quotas-request.md)

### <a name="operating-system-disks-and-images"></a>Dyski i obrazy z systemem operacyjnym
System operacyjny i dane maszyny wirtualnej są przechowywane na [wirtualnym dysku twardym (VHD)](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Wirtualne dyski twarde są również używane do obsługi obrazów, spośród których można wybierać, chcąc zainstalować system operacyjny. 

Na platformie Azure jest dostępnych wiele [obrazów z portalu Marketplace](https://azure.microsoft.com/marketplace/virtual-machines/), których można używać z różnymi wersjami i typami systemów operacyjnych Windows Server. Obrazy z Marketplace są oznaczone nazwą wydawcy i oferty, jednostką SKU i wersją (zwykle najnowszą). Obsługiwane są tylko 64-bitowe systemy operacyjne. Aby uzyskać więcej informacji dotyczących obsługiwanych systemów operacyjnych gościa, ról oraz funkcji, zobacz temat [Microsoft server software support for Microsoft Azure virtual machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) (Obsługa oprogramowania serwera Microsoft maszyn wirtualnych Microsoft Azure).

W poniższej tabeli pokazano, jak można znaleźć informacje o obrazie.

| Metoda | Opis |
| --- | --- |
| Azure Portal |Wartości są podawane automatycznie po wybraniu obrazu, który ma zostać użyty. |
| Azure PowerShell |[Get-AzVMImagePublisher](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagepublisher) -Location *lokalizacja*<BR>[Get-AzVMImageOffer](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimageoffer) -Location *lokalizacja* -Publisher *nazwaWydawcy*<BR>[Get-AzVMImageSku](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagesku) -Location *lokalizacja* -Publisher *nazwaWydawcy* -Offer *nazwaOferty* |
| Interfejsy API REST |[Wyświetl listę wydawców obrazów](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers)<BR>[Wyświetl listę ofert obrazów](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers)<BR>[Wyświetl listę jednostek SKU obrazów](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus) |
| Interfejs wiersza polecenia platformy Azure |[az vm image list-publishers](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest) --location *lokalizacja*<BR>[az vm image list-offers](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest) --location *lokalizacja* --publisher *nazwa_wydawcy*<BR>[az vm image list-skus](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest) --location *lokalizacja* --publisher *nazwa_wydawcy* --offer *nazwa_oferty*|

Istnieje możliwość [przesłania i użycia własnego obrazu](upload-generalized-managed.md#upload-the-vhd-to-your-storage-account) — nazwa wydawcy, oferta i jednostka SKU nie są wtedy używane.

### <a name="extensions"></a>Rozszerzenia
[Rozszerzenia](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) maszyn wirtualnych udostępniają dodatkowe funkcje, z których można korzystać po wdrożeniu i do automatyzacji zadań.

Typowe zadania można realizować przy użyciu różnych rozszerzeń:

* **Uruchamianie skryptów niestandardowych** — [rozszerzenie niestandardowego skryptu](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ułatwia konfigurowanie obciążeń na maszynie wirtualnej poprzez uruchomienie skryptu po aprowizacji maszyny wirtualnej.
* **Wdrażanie konfiguracji i zarządzanie nimi** — [rozszerzenie Konfiguracja żądanego stanu (Desired State Configuration, DSC) programu PowerShell](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ułatwia skonfigurowanie żądanego stanu na maszynie wirtualnej w celu zarządzania konfiguracjami i środowiskami.
* **Zbieranie danych diagnostycznych** — [rozszerzenie Diagnostyka Azure](extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ułatwia skonfigurowanie maszyny wirtualnej w celu zbierania danych diagnostycznych używanych do monitorowania kondycji aplikacji.

### <a name="related-resources"></a>Powiązane zasoby
Zasoby wymienione w tej tabeli są używane przez maszynę wirtualną i muszą istnieć lub zostać utworzone podczas jej tworzenia.

| Zasób | Wymagany | Opis |
| --- | --- | --- |
| [Grupa zasobów](../../azure-resource-manager/resource-group-overview.md) |Yes |Maszyna wirtualna musi być zawarta w grupie zasobów. |
| [Konto magazynu](../../storage/common/storage-create-storage-account.md) |Yes |Maszyna wirtualna wymaga konta magazynu do przechowywania wirtualnych dysków twardych. |
| [Sieć wirtualna](../../virtual-network/virtual-networks-overview.md) |Yes |Maszyna wirtualna musi należeć do sieci wirtualnej. |
| [Publiczny adres IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |Nie |Maszyna wirtualna może mieć przypisany publiczny adres IP umożliwiający uzyskiwanie do niej dostępu zdalnego. |
| [Interfejs sieciowy](../../virtual-network/virtual-network-network-interface.md) |Yes |Maszyna wirtualna wymaga interfejsu sieciowego do komunikacji w sieci. |
| [Dyski danych](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |Nie |Maszyna wirtualna może zawierać dyski danych zwiększające jej pojemność. |

## <a name="how-do-i-create-my-first-vm"></a>Jak utworzyć maszynę wirtualną?
Można skorzystać z jednej z kilku opcji utworzenia maszyny wirtualnej. Wybór zależy od używanego środowiska. 

Ta tabela zawiera informacje ułatwiające utworzenie maszyny wirtualnej.

| Metoda | Artykuł |
| --- | --- |
| Azure Portal |[Tworzenie maszyny wirtualnej z systemem Windows przy użyciu portalu](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Szablony |[Tworzenie maszyny wirtualnej z systemem Windows przy użyciu szablonu usługi Resource Manager](ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Azure PowerShell |[Tworzenie maszyny wirtualnej z systemem Windows przy użyciu programu PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Zestawy SDK klienta |[Wdrażanie zasobów Azure przy użyciu języka C#](csharp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Interfejsy API REST |[Tworzenie lub aktualizowanie maszyny wirtualnej](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-create-or-update) |
| Interfejs wiersza polecenia platformy Azure |[Tworzenie maszyny wirtualnej za pomocą interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-cli-sample-create-vm) |

Nikt tego nie lubi, ale czasem coś może pójść nie tak. Warto zapoznać się wtedy z artykułem [Troubleshoot Resource Manager deployment issues with creating a Windows virtual machine in Azure](../troubleshooting/troubleshoot-deployment-new-vm-windows.md) (Rozwiązywanie problemów z wdrażaniem programu Resource Manager dotyczących tworzenia maszyny wirtualnej z systemem Windows na platformie Azure).

## <a name="how-do-i-manage-the-vm-that-i-created"></a>Jak zarządzać maszyną wirtualną?
Maszynami wirtualnymi można zarządzać w portalu w przeglądarce internetowej, a także przy użyciu narzędzi wiersza polecenia z obsługą skryptów oraz bezpośrednio za pomocą interfejsów API. Najczęściej wykonywane zadania administracyjne to np. uzyskiwanie informacji na temat maszyny wirtualnej, logowanie się do niej, zarządzanie dostępnością oraz wykonywanie kopii zapasowych.

### <a name="get-information-about-a-vm"></a>Uzyskiwanie informacji o maszynie wirtualnej
W tej tabeli opisano, w jaki sposób można uzyskiwać informacje o maszynie wirtualnej.

| Metoda | Opis |
| --- | --- |
| Azure Portal |W menu Centrum kliknij opcję **Maszyny wirtualne**, a następnie wybierz odpowiednią maszynę wirtualną z listy. Z poziomu bloku maszyny wirtualnej można uzyskać dostęp do podstawowych informacji, wartości ustawień i metryk monitorowania. |
| Azure PowerShell |Aby uzyskać informacje na temat używania programu PowerShell zarządzania maszynami wirtualnymi, zobacz [Create and manage Windows VMs with the Azure PowerShell module (Tworzenie maszyn wirtualnych z systemem Windows i zarządzanie nimi za pomocą programu Azure PowerShell)](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Interfejs API REST |Użyj operacji [Get VM information](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-get) (Pobierz informacje o maszynie wirtualnej), aby uzyskać informacje na temat maszyny wirtualnej. |
| Zestawy SDK klienta |Aby dowiedzieć się więcej o zarządzaniu maszynami wirtualnymi za pomocą języka C#, zobacz artykuł [Manage Azure Virtual Machines using Azure Resource Manager and C#](csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Zarządzanie maszynami wirtualnymi na platformie Azure za pomocą usługi Azure Resource Manager i języka C#). |
| Interfejs wiersza polecenia platformy Azure |Aby uzyskać informacje o zarządzaniu maszynami wirtualnymi za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [Dokumentacja interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/vm). |

### <a name="log-on-to-the-vm"></a>Logowanie się do maszyny wirtualnej
Korzystając z przycisku Połącz w witrynie Azure, można [uruchomić sesję pulpitu zdalnego (RDP)](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Podczas próby użycia połączenia zdalnego mogą czasami występować problemy. W takiej sytuacji zapoznaj się z artykułem [Rozwiązywanie problemów z połączeniami Pulpitu zdalnego z maszynami wirtualnymi systemu Windows na platformie Azure](../troubleshooting/troubleshoot-rdp-connection.md).

### <a name="manage-availability"></a>Zarządzanie dostępnością
Ważne jest zrozumienie, jak można [zapewnić wysoką dostępność](manage-availability.md) aplikacji. Ta konfiguracja obejmuje utworzenie wielu maszyn wirtualnych w celu zagwarantowania, że przynajmniej jednak z nich na pewno działa.

Aby wdrożenie kwalifikowało się do naszej Umowy dotyczącej poziomu usług (SLA) uwzględniającej dostępność maszyn wirtualnych na poziomie 99,95%, wymagane jest wdrożenie co najmniej dwóch maszyn wirtualnych z uruchomionym obciążeniem w obrębie [zestawu dostępności](tutorial-availability-sets.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ta konfiguracja daje gwarancję, że maszyny wirtualne są rozproszone w wielu domenach błędów i wdrożone na hostach z różnymi okresami konserwacji. Pełna treść [umowy SLA dotyczącej usługi Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) wyjaśnia w całości kwestię gwarantowanej dostępności platformy Azure.

### <a name="back-up-the-vm"></a>Tworzenie kopii zapasowych maszyn wirtualnych
[Magazyn usługi Recovery Services](../../backup/backup-introduction-to-azure-backup.md) służy do ochrony danych i zasobów w usługach Azure Backup i Azure Site Recovery. Magazynu usługi Recovery Services można użyć do [wdrażania kopii zapasowych maszyn wirtualnych wdrożonych przy użyciu usługi Resource Manager oraz zarządzania nimi przy użyciu programu PowerShell](../../backup/backup-azure-vms-automation.md). 

## <a name="next-steps"></a>Następne kroki
* Użytkownicy korzystający z maszyn wirtualnych z systemem Linux powinni zapoznać się z tematem [Azure and Linux](../linux/overview.md) (Usługa Azure a system Linux).
* Więcej informacji na temat wytycznych dotyczących konfigurowania infrastruktury można znaleźć w artykule [Example Azure infrastructure walkthrough](infrastructure-example.md) (Przewodnik po konfigurowaniu przykładowej infrastruktury na platformie Azure).
