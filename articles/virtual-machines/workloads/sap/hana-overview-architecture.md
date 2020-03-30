---
title: Omówienie sap HANA na platformie Azure (duże wystąpienia) | Dokumenty firmy Microsoft
description: Omówienie sposobu wdrażania sap HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 39fcf5d0fe2273c4debd3ae5ebe5fd1190ddc959
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616949"
---
#  <a name="what-is-sap-hana-on-azure-large-instances"></a>Co to jest oprogramowanie SAP HANA na platformie Azure — duże wystąpienia?

SAP HANA na platformie Azure (duże wystąpienia) to unikatowe rozwiązanie platformy Azure. Oprócz dostarczania maszyn wirtualnych do wdrażania i uruchamiania sap HANA, platforma Azure oferuje możliwość uruchamiania i wdrażania SAP HANA na serwerach bez systemu operacyjnego, które są dedykowane dla Ciebie. Rozwiązanie SAP HANA na platformie Azure (duże wystąpienia) opiera się na nieudzielonym sprzęcie hosta/serwera bez systemu operacyjnego, który jest przypisany do użytkownika. Sprzęt serwera jest osadzony w większych sygnaturach zawierających infrastrukturę obliczeniową/serwerową, sieciową i magazynową. W połączeniu certyfikat hana dostosowanej integracji centrum danych (TDI) jest certyfikowany. SAP HANA na platformie Azure (duże wystąpienia) oferuje różne jednostki SKU serwera lub rozmiary. Jednostki mogą mieć 36 rdzeni procesora Intel i 768 GB pamięci i przejść do jednostek, które mają do 480 rdzeni procesora Intel i do 24 TB pamięci.

Izolacja klienta w sygnatury infrastruktury jest wykonywana w dzierżawach, która wygląda następująco:

- **Networking**: Izolacja klientów w stosie infrastruktury za pośrednictwem sieci wirtualnych na klienta przypisany dzierżawy. Dzierżawca jest przypisany do jednego klienta. Klient może mieć wielu dzierżawców. Izolacja sieci dzierżawców zabrania komunikacji sieciowej między dzierżawcami na poziomie stempla infrastruktury, nawet jeśli dzierżawcy należą do tego samego klienta.
- **Składniki magazynu:** Izolacja za pośrednictwem maszyn wirtualnych magazynu, które mają przypisane woluminy magazynu. Woluminy magazynu można przypisać tylko do jednej maszyny wirtualnej magazynu. Maszyna wirtualna magazynu jest przypisana wyłącznie do jednej dzierżawy w stosie infrastruktury z certyfikatem SAP HANA TDI. W rezultacie woluminy magazynu przypisane do maszyny wirtualnej magazynu można uzyskać tylko w jednej określonej i powiązanej dzierżawie. Nie są one widoczne między różnymi wdrożonymi dzierżawami.
- **Serwer lub host:** Serwer lub jednostka hosta nie jest współużytkowana przez klientów lub dzierżawców. Serwer lub host wdrożony dla klienta to niepodzielna jednostka obliczeniowa bez systemu operacyjnego przypisana do jednej dzierżawy. *Nie* jest używana partycjonowanie sprzętowe ani partycjonowanie miękkie, co może spowodować udostępnienie hosta lub serwera innemu klientowi. Woluminy magazynu, które są przypisane do maszyny wirtualnej magazynu określonej dzierżawy są instalowane na takim serwerze. Dzierżawca może mieć jeden do wielu jednostek serwera różnych jednostek SKU wyłącznie przypisane.
- W ramach usługi SAP HANA na platformie Azure (duże wystąpienia) sygnatury infrastruktury, wiele różnych dzierżaw są wdrażane i izolowane względem siebie za pośrednictwem koncepcji dzierżawy na poziomie sieci, magazynu i obliczeń. 


Te nieoklementowe jednostki serwerowe są obsługiwane tylko do uruchamiania sap HANA. Warstwa aplikacji SAP lub warstwa środkowego obciążenia działa na maszynach wirtualnych. Sygnatury infrastruktury, które uruchamiają jednostki SAP HANA na platformie Azure (duże wystąpienia), są połączone z szkieletami usług sieciowych platformy Azure. W ten sposób zapewnia się łączność o małym opóźnieniu między sap HANA na platformie Azure (duże wystąpienia) jednostek i maszyn wirtualnych.

Od lipca 2019 r. rozróżniamy dwie różne wersje pieczęci dużych wystąpień HANA i lokalizację wdrożeń:

- "Wersja 3" (Rev 3): Czy znaczki, które zostały udostępnione klientowi do wdrożenia przed lipcem 2019 r.
- "Wersja 4" (Rev 4): Nowy projekt znaczka, który jest wdrażany w pobliżu hostów maszyn wirtualnych platformy Azure i który do tej pory są wydawane w regionach platformy Azure:
    -  Zachodnie stany USA 2 
    -  Wschodnie stany USA 
    -  Europa Zachodnia
    -  Europa Północna


Ten dokument jest jednym z kilku dokumentów, które obejmują SAP HANA na platformie Azure (duże wystąpienia). W tym dokumencie przedstawiono podstawową architekturę, obowiązki i usługi świadczone przez rozwiązanie. Omówiono również możliwości wysokiego poziomu rozwiązania. W większości innych obszarów, takich jak sieć i łączność, cztery inne dokumenty obejmują szczegóły i informacje szczegółowe. Dokumentacja sap HANA na platformie Azure (duże wystąpienia) nie obejmuje aspektów instalacji SAP NetWeaver lub wdrożeń SAP NetWeaver w maszynach wirtualnych. SAP NetWeaver na platformie Azure jest objęty oddzielnych dokumentów znalezionych w tym samym kontenerze dokumentacji platformy Azure. 


Różne dokumenty wytycznych HANA Large Instance obejmują następujące obszary:

- [Omówienie i architektura SAP HANA (duże wystąpienia) na platformie Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Infrastruktura i łączność SAP HANA (Duże wystąpienia) na platformie Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Instalowanie i konfigurowanie usługi SAP HANA (duże wystąpienia) na platformie Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Wysoka dostępność i odzyskiwanie po awarii w systemie SAP HANA (Duże wystąpienia) na platformie Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Rozwiązywanie problemów i monitorowanie sap hana (dużych wystąpień) na platformie Azure](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Wysoka dostępność skonfigurowana w SUSE przy użyciu stonith](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [Tworzenie kopii zapasowych i przywracanie systemu operacyjnego dla jednostek SKU typu II sygnatury wersji 3](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

**Następne kroki**
- Poleć [Poznaj terminy](hana-know-terms.md)