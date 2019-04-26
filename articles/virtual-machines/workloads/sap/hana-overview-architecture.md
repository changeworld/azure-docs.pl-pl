---
title: Omówienie środowiska SAP Hana na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Omówienie sposobu wdrażania oprogramowania SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c177dbad1145dee6eda3202d8076997cc7673dfc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60477804"
---
#  <a name="what-is-sap-hana-on-azure-large-instances"></a>Co to jest oprogramowanie SAP HANA na platformie Azure — duże wystąpienia?

Oprogramowanie SAP HANA na platformie Azure (duże wystąpienia) to unikatowe rozwiązanie na platformie Azure. Oprócz zapewniania maszyn wirtualnych umożliwiające wdrażanie i uruchamianie oprogramowania SAP HANA, platforma Azure oferuje możliwość uruchamiania i wdrażania oprogramowania SAP HANA na serwerach bez systemu operacyjnego, które są przeznaczone dla Ciebie. SAP HANA na platformie Azure (duże wystąpienia) rozwiązanie opiera się na sprzętu bez systemu operacyjnego hosta nieudostępniane/serwer, który jest przypisany do Ciebie. Sprzęt serwera jest osadzony w większych sygnatur, zawierające obliczeń/serwera, sieci i infrastruktury magazynu. Jako połączenie jest HANA dostosowane Centrum integracji danych (TDI) certyfikat. Oprogramowanie SAP HANA na platformie Azure (duże wystąpienia) oferuje jednostki SKU z innego serwera lub rozmiarów. Jednostki może mieć 36 rdzeni procesora CPU Intel i 768 GB pamięci, a następnie przejść do jednostek, które mają maksymalnie 480 rdzeni procesora CPU Intel i maksymalnie 24 TB pamięci.

Izolacja klienta w ramach sygnatury infrastruktury odbywa się w dzierżawach, który wygląda następująco:

- **Sieć**: Izolacja klientów w ramach infrastruktury stack za pośrednictwem sieci wirtualnych na klienta przypisany dzierżawy. Dzierżawa jest przypisany do jednego klienta. Klient może mieć wielu dzierżaw. Izolacja sieci dzierżaw zabrania komunikacji sieciowej między dzierżawami, na poziomie sygnatury infrastruktury, nawet, jeśli dzierżawcy należą do tego samego klienta.
- **Składniki magazynu**: Izolacja za pośrednictwem magazynu maszyn wirtualnych, które mają woluminy magazynu przypisane do nich. Woluminy magazynu można przypisać do jednego magazynu tylko maszyny wirtualnej. Magazyn maszyny wirtualnej jest przypisane wyłącznie do jednego pojedynczej dzierżawy w stosie certyfikowanych infrastruktury SAP HANA TDI. W rezultacie woluminów magazynu przypisane do maszyny wirtualnej magazynu są dostępne w jednej określonej i pokrewnych dzierżawy tylko. Nie są one widoczne między różnych dzierżawach wdrożone.
- **Serwera lub hosta**: Serwer lub jednostki hosta nie jest udostępniana między klientami lub dzierżawcy. Serwer lub hoście z klientem jest jednostki atomic obliczeń bez systemu operacyjnego, przypisany do jednego pojedynczej dzierżawy. *Nie* partycjonowanie sprzętowe lub nietrwałego Partycjonowanie jest używany, może prowadzić do współużytkowania hosta lub serwer z innego klienta. Woluminy magazynu, które są przypisane do magazynu maszyny wirtualnej określonej dzierżawy są instalowane do takiego serwera. Dzierżawca może mieć jeden do wielu jednostek serwera różne jednostki SKU przypisana wyłącznie.
- W ramach oprogramowania SAP HANA w sygnaturze infrastruktury platformy Azure (duże wystąpienia) wielu różnych dzierżawach są wdrażane i odizolowane od siebie wzajemnie koncepcjami dzierżawy w sieci, magazynu i poziomu obliczeniowego. 


Te jednostki bez systemu operacyjnego serwera są obsługiwane tylko uruchamianie oprogramowania SAP HANA. Warstwy aplikacji SAP lub warstwy pośredniczącym obciążenie działa na maszynach wirtualnych. Sygnatury infrastruktury z systemem SAP HANA na platformie Azure (duże wystąpienia) jednostki są podłączone do szkieletowymi usług sieci platformy Azure. W ten sposób znajduje się połączenie o małych opóźnieniach między maszyny wirtualne i SAP HANA na platformie Azure (duże wystąpienia) jednostki.

W tym dokumencie jest jednym z kilku dokumentów, które obejmują oprogramowanie SAP HANA na platformie Azure (duże wystąpienia). Ten dokument wprowadza podstawowej architektury, obowiązkach i usług udostępnianych przez to rozwiązanie. Omówiono także ogólne możliwości rozwiązania. Dla większości innych obszarów, takich jak sieć i łączność cztery inne dokumenty, które obejmują szczegółowe informacje o Przechodzenie do szczegółów i. Dokumentacja środowiska SAP Hana na platformie Azure (duże wystąpienia) nie obejmuje aspektów instalacji oprogramowania SAP NetWeaver lub wdrożenia oprogramowania SAP NetWeaver na maszynach wirtualnych. Oprogramowanie SAP NetWeaver na platformie Azure są omówione w osobnych dokumentów, w tym samym kontenerze dokumentacji platformy Azure. 


Różne dokumenty wskazówek dotyczących dużych wystąpień HANA obejmują następujące obszary:

- [Omówienie platformy SAP HANA (duże wystąpienia) i architektury na platformie Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Infrastrukturę SAP HANA (duże wystąpienia) i łączności na platformie Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Instalowanie i konfigurowanie oprogramowania SAP HANA (duże wystąpienia) na platformie Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA (duże wystąpienia) o wysokiej dostępności i odzyskiwania po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Rozwiązywanie problemów z platformą SAP HANA (duże wystąpienia) i monitorowania na platformie Azure](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Wysoka dostępność, skonfigurować w systemie SUSE przy użyciu pomocą metody STONITH](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [Kopia zapasowa i przywracanie systemu operacyjnego dla jednostek SKU typu II](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

**Następne kroki**
- Zapoznaj się [znasz warunki](hana-know-terms.md)