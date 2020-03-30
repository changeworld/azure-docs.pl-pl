---
title: Maszyny wirtualne platformy Azure HA dla SAP NW w systemie Windows z usługą Azure NetApp Files (SMB)| Dokumenty firmy Microsoft
description: Wysoka dostępność sap NetWeaver na maszynach wirtualnych platformy Azure w systemie Windows z usługą Azure NetApp Files (SMB) dla aplikacji SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: radeltch
ms.openlocfilehash: b41db629c5308348f632b3dc51c75822ba361c60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77591357"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-netapp-filessmb-for-sap-applications"></a>Wysoka dostępność sap NetWeaver na maszynach wirtualnych platformy Azure w systemie Windows z plikami NetApp(SMB) platformy Azure dla aplikacji SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

W tym artykule opisano sposób wdrażania, konfigurowania maszyn wirtualnych, instalowania struktury klastra i instalowania wysoce dostępnego systemu SAP NetWeaver 7.50 na maszynach wirtualnych systemu Windows przy użyciu [pamięci SMB](https://docs.microsoft.com/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) w [plikach NetApp.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)  

Warstwa bazy danych nie jest szczegółowo omówiona w tym artykule. Zakładamy, że [sieć wirtualna](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) platformy Azure została już utworzona.  

Najpierw przeczytaj następujące uwagi i dokumenty SAP:

* [Dokumentacja plików netapp platformy Azure][anf-azure-doc] 
* Uwaga SAP [1928533][1928533], która zawiera:  
  * Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP
  * Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure
  * Obsługiwane oprogramowanie SAP oraz system operacyjny (OS) i kombinacje baz danych
  * Wymagana wersja jądra SAP dla systemu Windows na platformie Microsoft Azure
* Uwaga SAP [2015553][2015553] zawiera listę wymagań wstępnych dla wdrożeń oprogramowania SAP obsługiwanych przez SAP na platformie Azure.
* Uwaga SAP [2178632][2178632] zawiera szczegółowe informacje na temat wszystkich metryk monitorowania zgłoszonych dla sap na platformie Azure.
* Uwaga SAP [1999351][1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzeniem rozszerzonego monitorowania platformy Azure dla systemu SAP.
* Uwaga SAP [2287140](https://launchpad.support.sap.com/#/notes/2287140) zawiera listę wymagań wstępnych dla funkcji urzędu certyfikacji obsługiwanej przez SAP protokołu SMB 3.x.
* Uwaga SAP [2802770](https://launchpad.support.sap.com/#/notes/2802770) zawiera informacje dotyczące rozwiązywania problemów z wolno działającą transakcją SAP AL11 w systemach Windows 2012 i 2016.
* Uwaga SAP [1911507](https://launchpad.support.sap.com/#/notes/1911507) zawiera informacje o funkcji przezroczystego trybu failover dla udziału plików w systemie Windows Server za pomocą protokołu SMB 3.0.
* Sap Note [662452](https://launchpad.support.sap.com/#/notes/662452) ma zalecenie (dezaktywacja generowania nazw 8.3), aby rozwiązać problemu Niska wydajność systemu plików / błędy podczas dostępu do danych.
* [Instalowanie wysokiej dostępności SAP NetWeaver w klastrze trybu failover systemu Windows i udziale plików dla wystąpień SAP ASCS/SCS na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-file-share) 
* [Architektura i scenariusze wysokiej dostępności maszyn wirtualnych platformy Azure dla sap NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
* [Dodawanie portu sondy w konfiguracji klastra ASCS](sap-high-availability-installation-wsfc-file-share.md)
* [Instalacja wystąpienia SCS (A)w klastrze trybu failover](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)
* [Tworzenie woluminu SMB dla usługi Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#requirements-for-active-directory-connections)
* [Aplikacje SAP NetApp na platformie Microsoft Azure przy użyciu plików NetApp platformy Azure][anf-sap-applications-azure]

## <a name="overview"></a>Omówienie

Firma SAP opracowała nowe podejście i alternatywę dla dysków współdzielonych klastra do klastrowania wystąpienia SAP ASCS/SCS w klastrze trybu failover systemu Windows. Zamiast używać dysków udostępnionych klastra, można użyć udziału plików SMB do wdrażania globalnych plików hosta SAP. Usługa Azure NetApp Files obsługuje protokół SMBv3 (wraz z systemem plików NFS) z acl ntfs przy użyciu usługi Active Directory. Usługa Azure NetApp Files jest automatycznie wysoce dostępna (podobnie jak usługa PaaS). Te funkcje sprawiają, że usługi Azure NetApp Files są doskonałym rozwiązaniem do hostowania udziału plików SMB dla sap global.  
Obsługiwane są zarówno [Usługi domenowe usługi w usłudze Azure Active Directory (AD),](https://docs.microsoft.com/azure/active-directory-domain-services/overview) jak i [Usługi domenowe Active Directory (AD DS).](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) Kontrolerów domeny usługi Active Directory można używać za pomocą plików NetApp usługi Azure. Kontrolery domeny mogą znajdować się na platformie Azure jako maszyny wirtualne lub lokalnie za pośrednictwem usługi ExpressRoute lub S2S VPN. W tym artykule użyjemy kontrolera domeny na maszynie Wirtualnej platformy Azure.  
Wysoka dostępność(HA) dla usług centralnych SAP Netweaver wymaga współdzielonego magazynu. Aby to osiągnąć w systemie Windows, do tej pory konieczne było zbudowanie klastra SOFS lub użycie współdzielonego dysku klastra s/w, takiego jak SIOS. Teraz można osiągnąć SAP Netweaver HA przy użyciu udostępnionego magazynu, wdrożony na usługi Azure NetApp Files. Korzystanie z usługi Azure NetApp Files dla udostępnionego magazynu eliminuje potrzebę SOFS lub SIOS.  

> [!NOTE]
> Klastrowanie wystąpień SAP ASCS/SCS przy użyciu udziału plików jest obsługiwane dla SAP NetWeaver 7.40 (i nowsze), z SAP Kernel 7.49 (i nowsze).  

![Architektura SAP ASCS/SCS HA z udziałem SMB](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb.png)

Wymagania wstępne dotyczące udziału plików SMB to:
* protokołu SMB 3.0 (lub nowszego).
* Możliwość ustawiania list kontroli dostępu usługi Active Directory (ACL) dla grup użytkowników usługi Active Directory i obiektu komputera$ .
* Udział plików musi być włączony jako funkcja wysokiej klasy.

Udział usług SAP Central w tej architekturze referencyjnej jest oferowany przez usługi Azure NetApp Files:

![Architektura SAP ASCS/SCS HA z udziałem SMB](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-detail.png)

## <a name="create-and-mount-smb-volume-for-azure-netapp-files"></a>Tworzenie i instalowanie woluminu SMB dla plików NetApp usługi Azure

Wykonaj następujące kroki, aby przygotować się do korzystania z plików NetApp platformy Azure.  

1. Postępuj zgodnie z instrukcjami, [aby zarejestrować się w przypadku plików NetApp platformy Azure](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)  
2. Tworzenie konta netapp platformy Azure, wykonując kroki opisane w [obszarze Tworzenie konta NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)  
3. Konfigurowanie puli pojemności zgodnie z instrukcjami w aplikacji [Konfigurowanie puli pojemności](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)
4. Zasoby usługi Azure NetApp Files muszą znajdować się w podsieci delegowanej. Postępuj zgodnie z instrukcjami w [delegowania podsieci do usługi Azure NetApp Files,](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) aby utworzyć podsieć delegowane.  

> [!IMPORTANT]
> Przed utworzeniem woluminu SMB należy utworzyć połączenia usługi Active Directory. Przejrzyj [wymagania dotyczące połączeń usługi Active Directory](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#requirements-for-active-directory-connections).  

5. Tworzenie połączenia usługi Active Directory w sposób opisany w [opisie w aplikacji Tworzenie połączenia usługi Active Directory](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#create-an-active-directory-connection)  
6. Tworzenie woluminu SMB Azure NetApp Files SMB, postępując zgodnie z instrukcjami w obszarze [Dodawanie woluminu SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#add-an-smb-volume)  
7. Zamontuj wolumin SMB na maszynie wirtualnej systemu Windows.

> [!TIP]
> Instrukcje dotyczące instalowania woluminu plików NetApp usługi Azure można znaleźć, jeśli przejdziesz w [witrynie Azure Portal](https://portal.azure.com/#home) do obiektu Usługi Azure NetApp Files, kliknij blok **Woluminy,** a następnie **zainstaluj instrukcje**.  

## <a name="prepare-the-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster"></a>Przygotowanie infrastruktury dla środowiska SAP HA przy użyciu klastra trybu failover systemu Windows 

1. [Ustawianie wymaganych adresów IP DNS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#b22d7b3b-4343-40ff-a319-097e13f62f9e)  
2. [Ustaw statyczne adresy IP dla maszyn wirtualnych SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#84c019fe-8c58-4dac-9e54-173efd4b2c30).
3. [Ustaw statyczny adres IP dla wewnętrznego modułu równoważenia obciążenia platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#7a8f3e9b-0624-4051-9e41-b73fff816a9e).
4. [Ustaw domyślne reguły równoważenia obciążenia ASCS/SCS dla wewnętrznego modułu równoważenia obciążenia platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#f19bd997-154d-4583-a46e-7f5a69d0153c).
5. [Zmień domyślne reguły równoważenia obciążenia ASCS/SCS dla wewnętrznego modułu równoważenia obciążenia platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#fe0bd8b5-2b43-45e3-8295-80bee5415716).
6. [Dodaj maszyny wirtualne systemu Windows do domeny](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#e69e9a34-4601-47a3-a41c-d2e11c626c0c).
7. [Dodawanie wpisów rejestru w obu węzłach klastra wystąpienia SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#661035b2-4d0f-4d31-86f8-dc0a50d78158)
8. [Konfigurowanie klastra trybu failover systemu Windows Server dla wystąpienia SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#0d67f090-7928-43e0-8772-5ccbf8f59aab)
9. Jeśli używasz systemu Windows Server 2016, zaleca się skonfigurowanie [usługi Azure Cloud Witness](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).


## <a name="install-sap-ascs-instance-on-both-nodes"></a>Instalowanie wystąpienia SAP ASCS w obu węzłach

Potrzebne jest następujące oprogramowanie firmy SAP:
   * Oprogramowanie SAP Software Provisioning Manager (SWPM) wersja narzędzia instalacyjnego SPS25 lub nowsza.
   * Sap Kernel 7.49 lub nowsze
   * Utwórz nazwę hosta wirtualnego (nazwę sieci klastra) dla klastrowanego wystąpienia SAP ASCS/SCS, zgodnie z opisem w [temat Tworzenie nazwy hosta wirtualnego dla klastrowanego wystąpienia SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-shared-disk#a97ad604-9094-44fe-a364-f89cb39bf097).

> [!NOTE]
> Klastrowanie wystąpień SAP ASCS/SCS przy użyciu udziału plików jest obsługiwane dla SAP NetWeaver 7.40 (i nowsze), z SAP Kernel 7.49 (i nowsze).  

### <a name="install-an-ascsscs-instance-on-the-first-ascsscs-cluster-node"></a>Instalowanie wystąpienia ASCS/SCS w pierwszym węźle klastra ASCS/SCS

1. Zainstaluj wystąpienie SAP ASCS/SCS w pierwszym węźle klastra. Uruchom narzędzie instalacyjne SAP SWPM, a następnie przejdź do: Product**DBMS** > Installation > Application Server ABAP (lub Java) > System o wysokiej dostępności > **wystąpienie** > ASCS/SCS > pierwszym węzłem klastra.  

2. Wybierz **klaster udziału plików** jako konfigurację udziału klastra w programie SWPM.  
3. Po wyświetleniu monitu w kroku **SAP System Cluster Parameters**, wprowadź nazwę hosta dla udziału SMB plików NetApp, który został już utworzony jako nazwa **hosta udziału plików**.  W tym przykładzie nazwa hosta udziału SMB to **anfsmb-9562**. 

> [!IMPORTANT]
> Jeśli wymagane wstępne sprawdzanie Wyniki w programie SWPM pokazuje warunek funkcji ciągłej dostępności nie został spełniony, można go rozwiązać, postępując zgodnie z instrukcjami w [komunikacie o błędzie Opóźnione podczas próby uzyskania dostępu do folderu udostępnionego, który już nie istnieje w systemie Windows](https://support.microsoft.com/help/2820470/delayed-error-message-when-you-try-to-access-a-shared-folder-that-no-l).  

> [!TIP]
> Jeśli wymagane wstępne wyniki sprawdzania w pamięci SWPM pokazują, że warunek zamiany nie został spełniony, można dostosować rozmiar swapu, przechodząc do ustawień właściwości systemu>komputera>>> zaawansowane>> pamięci wirtualnej.  

4. Skonfiguruj zasób klastra SAP, port `SAP-SID-IP` sondy, przy użyciu programu PowerShell. Wykonaj tę konfigurację na jednym z węzłów klastra SAP ASCS/SCS, zgodnie z opisem w [Konfigurowani port sondy](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-shared-disk#10822f4f-32e7-4871-b63a-9b86c76ce761).

### <a name="install-an-ascsscs-instance-on-the-second-ascsscs-cluster-node"></a>Instalowanie wystąpienia ASCS/SCS w drugim węźle klastra ASCS/SCS

1. Zainstaluj wystąpienie SAP ASCS/SCS w drugim węźle klastra. Uruchom narzędzie instalacyjne SAP SWPM, a następnie przejdź do **>** > **DBMS** > serwera aplikacji ABAP (lub Java) > wystąpienia systemu ASCS/SCS > ascs/SCS >.  

### <a name="install-a-dbms-instance-and-sap-application-servers"></a>Instalowanie wystąpienia systemu DBMS i serwerów aplikacji SAP

Zakończ instalację SAP, instalując:

   * Wystąpienie usługi DBMS  
   * Podstawowy serwer aplikacji SAP  
   * Dodatkowy serwer aplikacji SAP  

## <a name="test-the-sap-ascsscs-instance-failover"></a>Testowanie trybu failover wystąpienia SAP ASCS/SCS 

### <a name="fail-over-from-cluster-node-a-to-cluster-node-b-and-back"></a>Przebłażenie awaryjne z węzła klastra A do węzła B i z powrotem klastra
W tym scenariuszu testowym będziemy odwoływać się do węzła klastra sapascs1 jako węzła A i do węzła klastra sapascs2 jako węzła B.

1. Sprawdź, czy zasoby klastra są ![uruchomione w węźle A. Rysunek 1: Zasoby klastra trybu failover systemu Windows Server uruchomione w węźle A przed testem trybu failover](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-1.png)  

2. Uruchom ponownie węzeł klastra A. Zasoby klastra SAP zostaną przeniesione ![do węzła klastra B. Rysunek 2: Zasoby klastra trybu failover systemu Windows Server uruchomione w węźle B po teście trybu failover](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-2.png)  


## <a name="lock-entry-test"></a>Test wejścia blokady

1.Sprawdź, czy serwer replikacji SAP Enqueue (ERS) jest aktywny  
2. Zaloguj się do systemu SAP, wykonaj transakcję SU01 i otwórz identyfikator użytkownika w trybie zmiany. Spowoduje to wygenerowanie wpisu blokady SAP.  
3. Po zalogowaniu się w systemie SAP wyświetl wpis blokady, przechodząc do transakcji ST12.  
4. Przejmowanie zasobów ASCS w stan failover z węzła klastra A do węzła klastra B.  
5. Sprawdź, czy wpis blokady, wygenerowany przed zachowaniem pracy awaryjnej zasobów klastra SAP ASCS/SCS jest zachowywany.  

![Rysunek 3: Wpis blokady jest zachowywany po teście pracy awaryjnej](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-3.png)  

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z trybu failover w kolejce w uchwale ASCS z ERS](https://wiki.scn.sap.com/wiki/display/SI/Troubleshooting+for+Enqueue+Failover+in+ASCS+with+ERS)
## <a name="next-steps"></a>Następne kroki

* [Planowanie i implementacja maszyn wirtualnych platformy Azure dla systemu SAP][planning-guide]
* [Wdrożenie maszyn wirtualnych platformy Azure dla systemu SAP][deployment-guide]
* [Wdrożenie usługi DBMS maszyn wirtualnych platformy Azure dla systemu SAP][dbms-guide]
* Aby dowiedzieć się, jak ustalić wysoką dostępność i zaplanować odzyskiwanie po awarii sap 
* HANA na platformie Azure (duże wystąpienia), zobacz [SAP HANA (duże wystąpienia) wysoka dostępność i odzyskiwanie po awarii na platformie Azure.](hana-overview-high-availability-disaster-recovery.md)
* Aby dowiedzieć się, jak ustalić wysoką dostępność i plan odzyskiwania po awarii sap HANA na maszynach wirtualnych platformy Azure, zobacz [Wysoka dostępność SAP HANA na maszynach wirtualnych platformy Azure (VM)][sap-hana-ha]
