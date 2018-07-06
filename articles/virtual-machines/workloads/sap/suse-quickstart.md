---
title: Testowanie środowiska SAP NetWeaver na maszynach wirtualnych systemu Linux SUSE platformy Microsoft Azure | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: cc4438a770a8092275373ccf8da9cc9951a1f906
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2018
ms.locfileid: "37858616"
---
# <a name="running-sap-netweaver-on-microsoft-azure-suse-linux-vms"></a>Uruchamianie środowiska SAP NetWeaver na maszynach wirtualnych systemu SUSE Linux na platformie Microsoft Azure
W tym artykule opisano różne kwestii, które należy wziąć pod uwagę podczas pracy w oprogramowanie SAP NetWeaver na maszynach wirtualnych Microsoft Azure w systemie SUSE Linux (VM). Począwszy od 19 maja 2016 r. oprogramowanie SAP NetWeaver jest oficjalnie obsługiwany na maszynach wirtualnych systemu SUSE Linux na platformie Azure. Wszystkie szczegółowe informacje dotyczące wersji systemu Linux, wersje jądra SAP i inne wymagania wstępne znajdują się w 1928533 Uwaga SAP "aplikacji SAP na platformie Azure: obsługiwane produkty i typy maszyn wirtualnych platformy Azure".
Więcej dokumentacji dotyczącej rozwiązania SAP na maszynach wirtualnych systemu Linux można znaleźć tutaj: [używanie rozwiązań SAP na maszynach wirtualnych systemu Linux (VM)](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Poniższe informacje powinny pomóc w uniknięciu niektóre potencjalne pułapki związane.

## <a name="suse-images-on-azure-for-running-sap"></a>Obrazy SUSE na platformie Azure na uruchamianie oprogramowania SAP
Uruchamianie oprogramowania SAP NetWeaver na platformie Azure, użyj SUSE Linux Enterprise Server w systemie SLES 12 (SPx) lub SLES dla rozwiązania SAP — Zobacz też Uwaga SAP 1928533. Niestandardowy obraz SUSE jest w witrynie Azure Marketplace ("SLES 11 z dodatkiem SP3 dla SAP CAL"), ale obraz, który nie jest przeznaczona do użytku ogólnego. Nie należy używać tego obrazu, ponieważ jest on zarezerwowany do [SAP Cloud Appliance Library](https://cal.sap.com/) rozwiązania.  

Należy użyć framework wdrożenia usługi Azure Resource Manager dla wszystkich instalacji na platformie Azure. Aby wyszukać obrazów SUSE SLES i wersji przy użyciu programu Azure PowerShell lub interfejsu wiersza polecenia (CLI) platformy Azure, polecenia poniżej. Możesz następnie wykorzystywać dane wyjściowe, na przykład do definiowania obrazu systemu operacyjnego w szablonie JSON do wdrażania nowej maszyny Wirtualnej systemu Linux SUSE.
Te polecenia programu PowerShell są usługi Azure PowerShell w wersji 1.0.1 lub nowszej.

Choć można użyć standardowych obrazów SLES w przypadku instalacji SAP, zaleca się korzystania z tych nowych SLES obrazów SAP. Te obrazy są teraz dostępne w galerii obrazów systemu Azure. Więcej informacji na temat tych obrazów można znaleźć w odpowiedniej [stronie portalu Azure Marketplace]( https://azuremarketplace.microsoft.com/en-us/marketplace/apps/SUSE.SLES-SAP ) lub [strony sieci web SUSE — często zadawane pytania dotyczące systemu SLES dla rozwiązania SAP]( https://www.suse.com/products/sles-for-sap/frequently-asked-questions/ ).


* Zwróć uwagę na istniejących wydawców, w tym SUSE:
  
   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```
* Zwróć uwagę na istniejące oferty od firmy SUSE:
  
   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```
* Poszukaj ofert systemu SUSE SLES:
  
   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES-SAP"
   CLI : azure vm image list-skus westeurope SUSE SLES
   CLI : azure vm image list-skus westeurope SUSE SLES-SAP
   ```
* Zwróć uwagę na określoną wersję jednostki SKU z systemem SLES:
  
   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12-SP2"
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES-SAP" -skus "12-SP2"
   CLI : azure vm image list westeurope SUSE SLES 12-SP2
   CLI : azure vm image list westeurope SUSE SLES-SAP 12-SP2
   ```

## <a name="installing-walinuxagent-in-a-suse-vm"></a>Instalowanie WALinuxAgent maszyny Wirtualnej z systemem SUSE
Agent o nazwie WALinuxAgent jest częścią obrazów SLES w witrynie Azure Marketplace. Aby uzyskać informacje o instalowaniu go ręcznie (na przykład podczas przekazywania systemu operacyjnego w systemie SLES wirtualny dysk twardy (VHD) z lokalnych) zobacz:

* [OpenSUSE](http://software.opensuse.org/package/WALinuxAgent)
* [Azure](../../linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## <a name="sap-enhanced-monitoring"></a>SAP "enhanced monitoring"
SAP "enhanced monitoring" jest obowiązkowy wstępnie wymagana do uruchamiania SAP na platformie Azure. Sprawdź szczegóły w systemie SAP należy pamiętać, 2191498 "SAP w systemie Linux przy użyciu platformy Azure: Enhanced Monitoring".

## <a name="attaching-azure-data-disks-to-an-azure-linux-vm"></a>Dołączanie dysków danych na platformie Azure na maszynie Wirtualnej systemu Linux platformy Azure
Nigdy nie instalowanie usługi Azure dysków z danymi na maszynie Wirtualnej systemu Linux platformy Azure przy użyciu identyfikatora urządzenia. Zamiast tego należy użyć powszechnie Unikatowy identyfikator (UUID). Użyj narzędzi graficznych z dyskami danych Instalowanie usługi Azure, na przykład, należy zachować ostrożność. Dokładnie sprawdź wpisy w/etc/fstab.

Problem z Identyfikatorem urządzenia jest, że może go zmienić, a następnie maszyna wirtualna platformy Azure może Odłóż procesu rozruchu. Aby rozwiązać ten problem, można dodać parametr nofail w/etc/fstab. Jednak należy zachować ostrożność nofail ponieważ aplikacji może używać punktu instalacji jako przed i napisać do katalogu głównego systemu plików w przypadku, gdy dysk zewnętrznych danych platformy Azure nie został zainstalowany podczas rozruchu.

Jedyny wyjątek od za pomocą identyfikatora UUID jest dołączenie dysku systemu operacyjnego w celu rozwiązywania problemów, zgodnie z opisem w sekcji poniżej.

## <a name="troubleshooting-a-suse-vm-that-isnt-accessible-anymore"></a>Rozwiązywanie problemów z systemem SUSE maszyn wirtualnych, który nie jest już dostępny
Może to być sytuacje, w którym maszyny Wirtualnej z systemem SUSE na platformie Azure, zawiesza się w procesie rozruchu (na przykład z powodu błędu dotyczą instalowania dysków). Ten problem można sprawdzić za pomocą funkcji diagnostyki rozruchu dla maszyn wirtualnych platformy Azure w wersji 2 w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [diagnostykę rozruchu](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

Jest jednym ze sposobów rozwiązania tego problemu można dołączyć dysku systemu operacyjnego z uszkodzonych maszyny Wirtualnej do innego SUSE maszyn wirtualnych na platformie Azure. Następnie wprowadź odpowiednie zmiany, takie jak/etc/fstab edytowanie lub usuwanie reguł udev sieci, zgodnie z opisem w następnej sekcji.

Jest istotnym aspektem należy wziąć pod uwagę. Wdrażanie wielu SUSE maszyn wirtualnych na podstawie tego samego obrazu portalu Azure Marketplace (na przykład w systemie SLES 11 z dodatkiem SP4) powoduje, że dysk systemu operacyjnego, który zawsze należy zainstalować ten sam identyfikator UUID. W związku z tym za pomocą identyfikator UUID, można dołączyć dysku systemu operacyjnego z innej maszyny Wirtualnej, która została wdrożona przy użyciu tych samych wyników obrazu portalu Azure Marketplace w dwa identyfikatory UUID nie są identyczne. Dwie identyczne Przyczyna UUID maszyny Wirtualnej używanych do rozwiązywania problemów, rozruch z dysku systemu operacyjnego dołączona i uszkodzenia, zamiast oryginalny dysk systemu operacyjnego.

Istnieją dwa sposoby, aby uniknąć problemów:

* Użyj innego obrazu portalu Azure Marketplace do maszyny Wirtualnej rozwiązywania problemów (na przykład w systemie SLES 11 SPx zamiast systemu SLES 12).
* Nie dołączaj uszkodzonego dysku systemu operacyjnego z innej maszyny Wirtualnej przy użyciu identyfikatora UUID — Użyj coś innego.

## <a name="uploading-a-suse-vm-from-on-premises-to-azure"></a>Przekazywanie SUSE maszyny Wirtualnej z systemem ze środowiska lokalnego do platformy Azure
Opis czynności, które można przekazać SUSE maszyny Wirtualnej z systemem ze środowiska lokalnego do platformy Azure, zobacz [przygotowywanie maszyny wirtualnej systemu SLES lub openSUSE dla platformy Azure](../../linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Jeśli chcesz przekazać maszynę Wirtualną bez kroku anulowania aprowizacji na końcu (na przykład, aby zachować istniejącą instalację SAP, a także nazwę hosta), sprawdź następujące elementy:

* Upewnij się, że dysk systemu operacyjnego został zainstalowany przy użyciu identyfikatora UUID ani nie identyfikator urządzenia. Zmiana tylko w/etc/fstab UUID nie jest wystarczająca dla dysku systemu operacyjnego. Ponadto nie zapomnij dostosowania moduł ładujący rozruchu za pomocą YaST lub edytując /boot/grub/menu.lst.
* Jeśli używasz formatu VHDX dla dysku systemu operacyjnego SUSE i przekonwertować go do wirtualnego dysku twardego do przekazywania do usługi Azure, jest prawdopodobne, że urządzenie sieciowe zmienia się z eth0 eth1. Aby uniknąć problemów, gdy jest uruchamiany na platformie Azure później, należy zmienić do eth0 zgodnie z opisem w [ustalania eth0 w sklonowany SLES 11 VMware](https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/).

Oprócz opisane w artykule firma Microsoft zaleca, aby usunąć ten plik:

   /lib/udev/rules.d/75-persistent-net-generator.rules

Można także zainstalować agenta systemu Linux platformy Azure (waagent) co pozwala uniknąć potencjalnych problemów, tak długo, jak wiele kart sieciowych nie istnieją.

## <a name="deploying-a-suse-vm-on-azure"></a>Wdrażanie maszyny Wirtualnej z systemem SUSE na platformie Azure
Za pomocą plików szablonu JSON w nowym modelu usługi Azure Resource Manager, należy utworzyć nowe maszyny wirtualne z systemem SUSE. Po utworzeniu pliku szablonu JSON, można wdrożyć maszyny Wirtualnej przy użyciu następującego polecenia interfejsu wiersza polecenia jako alternatywa dla programu PowerShell:

   ```
   azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ```
Aby uzyskać więcej informacji na temat plików szablonów JSON, zobacz [tworzenia usługi Azure Resource Manager](../../../resource-group-authoring-templates.md) i [szablony szybkiego startu platformy](https://azure.microsoft.com/documentation/templates/).

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia i usługi Azure Resource Manager, zobacz [użyć wiersza polecenia platformy Azure dla systemów Mac, Linux i Windows za pomocą usługi Azure Resource Manager](../../../xplat-cli-azure-resource-manager.md).

## <a name="sap-license-and-hardware-key"></a>Klucz licencji i sprzętu SAP
Kwalifikację SAP do platformy Azure nowy mechanizm został wprowadzony do obliczania klucz sprzętu SAP, który jest używany do licencji na oprogramowanie SAP. Jądra SAP musiały zostać dostosowane, aby użyć nowego algorytmu. Wcześniejsze wersje jądra SAP dla systemu Linux nie zawiera tej zmiany kodu. W związku z tym, w niektórych sytuacjach (na przykład maszyny Wirtualnej platformy Azure zmiany rozmiaru), zmienić klucza sprzętowego SAP i SAP licencja została przestanie działać. To rozwiązanie jest dostarczana z nowszą jądra systemu Linux SAP.  Szczegółowe poprawki jądra SAP są udokumentowane w Uwaga SAP 1928533.

## <a name="suse-sapconf-package--tuned-adm"></a>Pakiet sapconf SUSE / dostosowana usługi adm
SUSE udostępnia pakiet o nazwie "sapconf" zarządzającą zbiór ustawień specyficznych dla SAP. Aby uzyskać więcej informacji na temat działania tego pakietu, a także jak zainstalować i używać go, zobacz: [przygotowanie SUSE Linux Enterprise Server do uruchamiania systemów SAP za pomocą sapconf](https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) i [co to jest sapconf lub jak przygotować SUSE Linux Enterprise Serwer do uruchamiania systemów SAP? ](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems).

W międzyczasie to nowe narzędzie zastępuje "sapconf - dostrojone adm". Jeden można znaleźć więcej informacji o tym narzędziu, następujące dwa łącza:

- Można znaleźć dokumentację w systemie SLES profilu "dostrojone adm" sap-hana [tutaj](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_saptune.html) 

- Można znaleźć systemy dostrajania dla obciążeń SAP za pomocą "dostrojone adm" - [tutaj](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf) w rozdziale 6.2

## <a name="nfs-share-in-distributed-sap-installations"></a>Udziału NFS w przypadku rozproszonej instalacji SAP
W przypadku instalacji rozproszonej — na przykład, którym chcesz zainstalować bazy danych i serwery aplikacji SAP na oddzielnych maszynach wirtualnych — możesz udostępniać /sapmnt katalogu za pomocą programu Network File System (NFS). Jeśli masz problemy z instrukcjami instalacji po utworzeniu udziału NFS dla /sapmnt, należy sprawdzić, jeśli dla danego udziału zostanie ustawiony "no_root_squash".

## <a name="logical-volumes"></a>Woluminy logiczne
W przeszłości w razie potrzeby jedną dużą logicznych na wielu dyskach danych platformy Azure (na przykład dla bazy danych SAP), został zalecane użycie narzędzia do zarządzania Raid MDADM ponieważ Linux Menedżer woluminów logicznych (LVM) nie została w pełni zweryfikowana jeszcze na platformie Azure. Aby dowiedzieć się, jak skonfigurować RAID systemu Linux na platformie Azure przy użyciu mdadm, zobacz [Konfigurowanie programowej macierzy RAID w systemie Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). W międzyczasie począwszy od początku maja 2016 r., Menedżer woluminów logicznych w systemie Linux jest w pełni obsługiwana na platformie Azure i mogą być używane jako alternatywa MDADM. Aby uzyskać więcej informacji na temat LVM na platformie Azure przeczytaj:  
[Konfigurowanie LVM na Maszynę wirtualną systemu Linux na platformie Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-suse-repository"></a>Repozytorium Azure SUSE
Jeśli masz problem z dostępem do standardowego repozytorium Azure SUSE służy polecenie je zresetować. Tych problemów może się zdarzyć, jeśli utworzysz prywatnego obrazu systemu operacyjnego w jednym regionie platformy Azure, a następnie skopiować go do innego regionu platformy Azure do wdrażania nowych maszyn wirtualnych, które są oparte na tego prywatnego obrazu systemu operacyjnego. Uruchom następujące polecenie z poziomu maszyny Wirtualnej:

   ```
   service guestregister restart
   ```

## <a name="gnome-desktop"></a>Pulpit gnome
Jeśli chcesz zainstalować całego systemu pokaz SAP w pojedynczej maszyny Wirtualnej — w tym SAP graficznego interfejsu użytkownika za pomocą pulpitu Gnome przeglądarki i konsoli zarządzania SAP — Użyj niej tę wskazówkę do zainstalowania go na obrazach platformy Azure w systemie SLES:

   Dla systemu SLES 11:

   ```
   zypper in -t pattern gnome
   ```

   Dla systemu SLES 12:

   ```
   zypper in -t pattern gnome-basic
   ```

## <a name="sap-support-for-oracle-on-linux-in-the-cloud"></a>SAP obsługę oprogramowania Oracle w systemie Linux w chmurze
Ma ograniczeń pomocy technicznej od firmy Oracle w systemie Linux w środowiskach zwirtualizowanych. Pomimo tego ograniczenia obsługi nie jest temat usługi specyficzne dla platformy Azure, ważne jest zrozumienie. SAP nie obsługuje Oracle w systemie SUSE lub Red Hat w chmurze publicznej, takich jak platforma Azure. W tym czasie jest uruchomiona, Oracle DB na platformie Azure jest w pełni obsługiwana przez firmę SAP w systemie Oracle Linux (zobacz 1928533 Uwaga SAP). Jeśli wymagane są inne kombinacje, skontaktuj się z oprogramowania Oracle bezpośrednio.

