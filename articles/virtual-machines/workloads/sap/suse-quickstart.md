---
title: Testowanie SAP NetWeaver na maszynach wirtualnych systemu Linux SUSE platformy Microsoft Azure | Dokumentacja firmy Microsoft
description: Testowanie środowiska SAP NetWeaver na maszynach wirtualnych systemu SUSE Linux na platformie Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 645e358b-3ca1-4d3d-bf70-b0f287498d7a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/14/2017
ms.author: hermannd
ms.openlocfilehash: 9bb13cde07141c576a0ec78c3b48c5a20d6dbb67
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657659"
---
# <a name="running-sap-netweaver-on-microsoft-azure-suse-linux-vms"></a>Uruchamianie środowiska SAP NetWeaver na maszynach wirtualnych systemu SUSE Linux na platformie Microsoft Azure
W tym artykule opisano różne rzeczy, które należy wziąć pod uwagę podczas pracy SAP NetWeaver na maszynach wirtualnych Microsoft Azure SUSE Linux (VM). Począwszy od 19 maja 2016 SAP NetWeaver jest oficjalnie obsługiwana w SUSE maszyn wirtualnych systemu Linux na platformie Azure. Wszystkie szczegóły dotyczące wersji systemu Linux, SAP jądra wersji i innych wymagań wstępnych znajdują się w 1928533 Uwaga SAP "aplikacje SAP na platformie Azure: obsługiwanych produktach i typy maszyna wirtualna platformy Azure".
Dalsze dokumentację dotyczącą SAP na maszynach wirtualnych systemu Linux można znaleźć tutaj: [przy użyciu programu SAP na maszynach wirtualnych systemu Linux (VM)](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Poniższe informacje powinny pomóc w uniknięciu niektórych potencjalne pułapki związane.

## <a name="suse-images-on-azure-for-running-sap"></a>Obrazy SUSE na platformie Azure do uruchamiania programu SAP
SAP NetWeaver działających na platformie Azure, użyj SUSE Linux Enterprise Server SLES 12 (SPx) lub SLES dla SAP — Zobacz też Uwaga SAP 1928533. Specjalne obrazu SUSE znajduje się w portalu Azure Marketplace ("SLES 11 z dodatkiem SP3 na SAP CAL"), ale obraz nie jest przeznaczony do użytku ogólnego. Nie należy używać tego obrazu, ponieważ jest on zarezerwowany dla [biblioteki urządzenia chmury SAP](https://cal.sap.com/) rozwiązania.  

Należy użyć platformy wdrażania usługi Azure Resource Manager dla wszystkich instalacji na platformie Azure. Aby wyszukać SUSE SLES obrazów i wersji przy użyciu programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure (CLI), należy użyć poleceń pokazano poniżej. Następnie można dane wyjściowe, na przykład zdefiniować obrazu systemu operacyjnego w szablonie JSON do wdrażania nowej maszyny Wirtualnej systemu Linux SUSE.
Te polecenia programu PowerShell są prawidłowe dla programu Azure PowerShell w wersji 1.0.1 i nowszym.

Mimo że jest nadal można używać standardowych obrazów SLES dla instalacji programu SAP, zaleca się korzystać z nowych SLES SAP obrazów. Te obrazy są teraz dostępne w galerii Azure obrazu. Więcej informacji na temat tych obrazów znajduje się w odpowiedniej [strony portalu Azure Marketplace]( https://azuremarketplace.microsoft.com/en-us/marketplace/apps/SUSE.SLES-SAP ) lub [strony sieci web SUSE — często zadawane pytania dotyczące SLES dla SAP]( https://www.suse.com/products/sles-for-sap/frequently-asked-questions/ ).


* Wyszukaj istniejących wydawców w tym SUSE:
  
   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```
* Wyszukaj istniejących ofert z SUSE:
  
   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```
* Wyszukaj ofert SUSE SLES:
  
   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES-SAP"
   CLI : azure vm image list-skus westeurope SUSE SLES
   CLI : azure vm image list-skus westeurope SUSE SLES-SAP
   ```
* Wygląd dla określonej wersji SLES SKU:
  
   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12-SP2"
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES-SAP" -skus "12-SP2"
   CLI : azure vm image list westeurope SUSE SLES 12-SP2
   CLI : azure vm image list westeurope SUSE SLES-SAP 12-SP2
   ```

## <a name="installing-walinuxagent-in-a-suse-vm"></a>Instalowanie na maszynie wirtualnej SUSE WALinuxAgent
Agent o nazwie WALinuxAgent jest częścią obrazów SLES w portalu Azure Marketplace. Informacje o instalowaniu go ręcznie (na przykład podczas przekazywania SLES OS wirtualny dysk twardy (VHD) z lokalnymi) zobacz:

* [OpenSUSE](http://software.opensuse.org/package/WALinuxAgent)
* [Azure](../../linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## <a name="sap-enhanced-monitoring"></a>SAP "rozszerzonego monitorowania"
SAP "rozszerzonego monitorowania" jest obowiązkowy wymagane do uruchomienia programu SAP na platformie Azure. Sprawdź szczegóły w SAP Pamiętaj 2191498 "SAP na Linux z: rozszerzonego monitorowania Azure".

## <a name="attaching-azure-data-disks-to-an-azure-linux-vm"></a>Dołączanie danych Azure dysków do maszyny Wirtualnej systemu Linux na platformie Azure
Nigdy nie Azure instalacji dysków z danymi do maszyny Wirtualnej systemu Linux platformy Azure przy użyciu identyfikatora urządzenia. W zamian użyj uniwersalnego unikatowego identyfikatora (UUID). Należy zachować ostrożność, korzystając z narzędzi graficznych do dysków z danymi instalacji Azure, np. Sprawdź wpisy w /etc/fstab.

Problem z Identyfikatorem urządzenia jest, że może go zmienić, a następnie maszyny Wirtualnej Azure może Odłóż procesu rozruchu. Aby uniknąć problemu, możesz dodać parametr nofail w /etc/fstab. Jednak należy zachować ostrożność przy nofail ponieważ aplikacji może użyć punktu instalacji jak przed i może zapisać w głównym systemie plików, w przypadku, gdy dysk danych zewnętrznych Azure nie został zainstalowany podczas rozruchu.

Jedynym wyjątkiem za pomocą identyfikatora UUID jest dołączenie dysku systemu operacyjnego w celu rozwiązywania problemów, zgodnie z opisem w sekcji, które należy wykonać.

## <a name="troubleshooting-a-suse-vm-that-isnt-accessible-anymore"></a>Rozwiązywanie problemów z SUSE maszynę Wirtualną, która nie jest już dostępny
Mogą wystąpić sytuacje, w którym SUSE maszyny Wirtualnej na platformie Azure, zawiesza się procesu rozruchu (na przykład z powodu błędu związane z instalowania dysków). Ten problem można sprawdzić za pomocą funkcji diagnostyki rozruchu dla maszyn wirtualnych platformy Azure w wersji 2 w portalu Azure. Aby uzyskać więcej informacji, zobacz [diagnostyki rozruchu](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

Jednym ze sposobów rozwiązania tego problemu jest można dołączyć dysku systemu operacyjnego z uszkodzonego maszyny Wirtualnej do innego SUSE maszyny Wirtualnej na platformie Azure. Następnie wprowadź odpowiednie zmiany, takie jak /etc/fstab edytowanie lub usuwanie reguł udev sieci, zgodnie z opisem w następnej sekcji.

Brak istotnym aspektem należy wziąć pod uwagę. Wdrażanie wielu SUSE maszyn wirtualnych z tego samego portalu Azure Marketplace obrazu (na przykład SLES 11 z dodatkiem SP4) powoduje, że dysk systemu operacyjnego, który zawsze należy zainstalować ten sam identyfikator UUID. W związku z tym przy użyciu identyfikator UUID, można dołączyć dysku systemu operacyjnego z inną maszynę Wirtualną, która została wdrożona przy użyciu takie same wyniki obrazu portalu Azure Marketplace w dwóch UUID nie są identyczne. Dwie identyczne Przyczyna UUID maszyny Wirtualnej używanych do rozwiązywania problemów, rozruch z dysku systemu operacyjnego dołączona i uszkodzony, zamiast oryginalny dysk systemu operacyjnego.

Istnieją dwa sposoby, aby uniknąć problemów:

* Użyj innego obrazu portalu Azure Marketplace do rozwiązywania problemów z maszyny Wirtualnej (na przykład SLES 11 SPx zamiast SLES 12).
* Nie należy dołączyć uszkodzonego dysku systemu operacyjnego z inną maszynę Wirtualną przy użyciu identyfikatora UUID--Użyj coś innego.

## <a name="uploading-a-suse-vm-from-on-premises-to-azure"></a>Przekazywanie wirtualna SUSE z lokalnej na platformie Azure
Opis kroków, aby przekazać maszyny Wirtualnej SUSE z lokalnego do platformy Azure, zobacz [przygotowanie SLES lub openSUSE maszyny wirtualnej na platformie Azure](../../linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Jeśli chcesz przekazać Maszynę wirtualną bez kroku deprovision na końcu (na przykład, aby zachować istniejącą instalację programu SAP, a także nazwę hosta), sprawdź następujące elementy:

* Upewnij się, że dysk systemu operacyjnego został zainstalowany przy użyciu identyfikatora UUID ani nie identyfikator urządzenia. Zmiana tylko w /etc/fstab UUID nie jest wystarczająca dla dysku systemu operacyjnego. Ponadto nie zapomnij dostosowania moduł ładujący rozruchu, za pomocą YaST lub edytując /boot/grub/menu.lst.
* Jeśli używasz formatu VHDX dla dysku systemu operacyjnego SUSE i przekonwertować go na dysk VHD do przekazywania do platformy Azure, jest prawdopodobne, że urządzenie sieciowe zmienia się z eth0 eth1. Aby uniknąć problemów, gdy jest uruchamiany na platformie Azure później, zmień wstecz na eth0 zgodnie z opisem w [ustalania eth0 w sklonować SLES 11 VMware](https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/).

Poza tym, co opisano w artykule firma Microsoft zaleca, aby usunąć ten plik:

   /lib/udev/rules.d/75-persistent-net-generator.rules

Można także zainstalować agenta systemu Linux platformy Azure (agenta waagent) pozwala uniknąć potencjalnych problemów, dopóki nie ma wiele kart sieciowych.

## <a name="deploying-a-suse-vm-on-azure"></a>Wdrażanie SUSE maszyny Wirtualnej na platformie Azure
Należy utworzyć nowe SUSE maszyn wirtualnych przy użyciu plików szablonu JSON w nowy model usługi Azure Resource Manager. Po utworzeniu pliku JSON szablonu można wdrożyć maszyny Wirtualnej za pomocą następującego polecenia interfejsu wiersza polecenia alternatywą dla środowiska PowerShell:

   ```
   azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ```
Aby uzyskać więcej informacji o plikach szablonu JSON, zobacz [szablonów Authoring Azure Resource Manager](../../../resource-group-authoring-templates.md) i [szablonów Szybki Start Azure](https://azure.microsoft.com/documentation/templates/).

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia i usługi Azure Resource Manager, zobacz [użyć wiersza polecenia platformy Azure dla komputerów Mac, Linux i Windows za pomocą Menedżera zasobów Azure](../../../xplat-cli-azure-resource-manager.md).

## <a name="sap-license-and-hardware-key"></a>Klucz sprzętu i licencji SAP
Kwalifikację SAP Azure nowego mechanizmu wprowadzono w celu obliczenia klucz sprzętu SAP, który jest używany do licencji SAP. Jądra SAP musiała zostać dostosowane, aby użyć nowego algorytmu. Wcześniejsze wersje jądra SAP dla systemu Linux nie zawiera tej zmiany kodu. Dlatego w niektórych sytuacjach (na przykład maszyny Wirtualnej Azure zmiany rozmiaru), zmienić SAP klucza sprzętu i licencji SAP został nie będzie już prawidłowy. Rozwiązanie jest dostarczany z nowszą jądra systemu Linux SAP.  Szczegółowe poprawki jądra SAP są udokumentowane w Uwaga SAP 1928533.

## <a name="suse-sapconf-package--tuned-adm"></a>Pakiet sapconf SUSE / adm dopasowane
SUSE zawiera pakiet o nazwie "sapconf", która zarządza zestawem ustawienia specyficzne dla programu SAP. Aby uzyskać więcej informacji dotyczących tego pakietu jest i jak zainstalować i korzystać z niego, zobacz: [przy użyciu sapconf przygotować SUSE Linux Enterprise Server do uruchomienia systemów SAP](https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) i [co to jest sapconf lub sposób przygotowania SUSE Linux Enterprise Serwer z systemami SAP? ](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems).

Do tego czasu jest nowe narzędzie zastępuje "sapconf - dopasowane adm". Co można znaleźć więcej informacji o tym narzędziu następujące dwa łącza:

- SLES dokumentację dotyczącą profilu "dopasowane adm" sap-hana można znaleźć [tutaj](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html) 

- Systemy dostrajania dla obciążeń SAP za pomocą "dopasowane adm" — można znaleźć [tutaj](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf) w rozdziale 6.2

## <a name="nfs-share-in-distributed-sap-installations"></a>W instalacji programu SAP rozproszonych udziału NFS
Jeśli masz instalacją rozproszoną — na przykład, gdzie chcesz zainstalować bazy danych i serwerów aplikacji SAP w oddzielnych maszyn wirtualnych — można udostępnić katalogu /sapmnt za pośrednictwem sieciowego systemu plików (NFS). Jeśli masz problemy z instrukcjami instalacji po utworzeniu udziału NFS dla /sapmnt, sprawdź, czy "no_root_squash" jest ustawiona dla udziału.

## <a name="logical-volumes"></a>Woluminy logiczne
W przeszłości ile wymagane dużą logiczne na wielu dyskach danych Azure (na przykład dla bazy danych SAP), został zalecane użycie narzędzia do zarządzania Raid MDADM ponieważ Linux menedżera woluminu logicznego (LVM) nie została całkowicie zweryfikowana jeszcze na platformie Azure. Aby dowiedzieć się, jak skonfigurować RAID systemu Linux na platformie Azure przy użyciu mdadm, zobacz [należy skonfigurować oprogramowanie RAID w systemie Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). W międzyczasie począwszy od początku maja 2016, Menedżer woluminów logicznych systemu Linux jest w pełni obsługiwany na platformie Azure i mogą być używane jako alternatywę MDADM. Aby uzyskać więcej informacji na temat LVM na platformie Azure przeczytaj:  
[Skonfiguruj LVM na Maszynę wirtualną systemu Linux na platformie Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-suse-repository"></a>Azure SUSE repozytorium
Jeśli masz problem z dostępem do standardowych repozytorium Azure SUSE służy polecenie je zresetować. Takie problemy mogą nastąpić, jeśli należy utworzyć obraz systemu operacyjnego prywatnych w jednym regionie Azure, a następnie skopiować obraz w innym regionie Azure, aby wdrożyć nowe maszyny wirtualne, które są oparte na tym prywatnej obrazu systemu operacyjnego. Uruchom następujące polecenie w ramach maszyny Wirtualnej:

   ```
   service guestregister restart
   ```

## <a name="gnome-desktop"></a>Gnome pulpitu
Jeśli chcesz korzystać z pulpitu Gnome do zainstalowania całego systemu pokaz SAP w jednej maszyny Wirtualnej — SAP graficznego interfejsu użytkownika, w tym przeglądarki i konsoli zarządzania programu SAP — Użyj Wskazówka do zainstalowania go na obrazy Azure SLES:

   Dla SLES 11:

   ```
   zypper in -t pattern gnome
   ```

   Dla SLES 12:

   ```
   zypper in -t pattern gnome-basic
   ```

## <a name="sap-support-for-oracle-on-linux-in-the-cloud"></a>Obsługa SAP Oracle w systemie Linux w chmurze
Brak ograniczeń pomocy technicznej z programem Oracle w systemie Linux w zwirtualizowanych środowiskach. Pomimo tego ograniczenia obsługi nie jest tematu specyficzne dla platformy Azure, ważne jest zrozumienie. W systemie SUSE lub Red Hat w chmurze publicznej, takich jak Azure SAP nie obsługuje Oracle. W tym czasie uruchomiona, bazy danych programu Oracle na platformie Azure jest w pełni obsługiwane przez SAP na Oracle Linux (zobacz 1928533 Uwaga SAP). Jeśli inne kombinacje są wymagane, skontaktuj się z programem Oracle bezpośrednio.

