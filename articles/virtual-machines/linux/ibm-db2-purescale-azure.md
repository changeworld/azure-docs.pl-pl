---
title: IBM DB2 pureScale na platformie Azure
description: W tym artykule przedstawiono architekturę do uruchamiania środowiska pureScale IBM DB2 na platformie Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/09/2018
ms.author: njray
ms.openlocfilehash: 1622de0cccdbc8fee0681e209e756b30da292d3c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60543025"
---
# <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale na platformie Azure

Środowisko pureScale IBM DB2 zapewnia klaster bazy danych dla platformy Azure o wysokiej dostępności i skalowalności w systemach operacyjnych Linux. W tym artykule przedstawiono architekturę do uruchamiania bazy danych DB2 pureScale na platformie Azure.

## <a name="overview"></a>Omówienie

Przedsiębiorstwa, że długo używasz platformy systemu (danych RDBMS) zarządzania relacyjnymi bazami danych ich transakcji online (OLTP) potrzeby przetwarzania. Te dni wiele migracji środowisk baza danych oparta na mainframe na platformie Azure jako sposób, aby zwiększyć pojemność, obniżenie kosztów i zachowanie struktury stały koszty operacyjne.

Migracja często jest pierwszym krokiem w modernizowaniu platform starsze. Na przykład jednemu z klientów korporacyjnych ostatnio rehosted swoje środowisko programu IBM DB2 uruchomionego na z/OS do pureScale IBM DB2 na platformie Azure. Chociaż nie są identyczne do oryginalnego środowiska, pureScale IBM DB2 w systemie Linux udostępnia podobne wysokiej dostępności i skalowalności jako IBM DB2 dla działającej w konfiguracji równoległej Sysplex na mainframe z/OS.

W tym artykule opisano architekturę, używany do migracji na platformę Azure. Klient Red Hat Linux 7.4 są używane do testowania konfiguracji. Ta wersja jest dostępna w witrynie Azure Marketplace. Przed wybraniem dystrybucji systemu Linux upewnij się, że obecnie obsługiwane. Aby uzyskać szczegółowe informacje, zobacz dokumentację dla [pureScale IBM DB2](https://www.ibm.com/support/knowledgecenter/SSEPGG) i [GlusterFS](https://docs.gluster.org/en/latest/).

Ten artykuł stanowi punkt wyjścia dla planu wdrożenia bazy danych DB2. Twoje wymagania biznesowe będą się różnić, ale ma zastosowanie tego samego wzorca podstawowego. Umożliwia także ten wzorzec architektury dla aplikacji przetwarzania analitycznego online (OLAP) na platformie Azure.

Ten artykuł nie obejmuje różnice i zadania migracji możliwe przeniesienie IBM DB2 dla bazy danych z/OS do pureScale IBM DB2 działającej w systemie Linux. I nie zapewnia szacunki rozmiaru i obciążenia analizy dotyczące przechodzenia z bazy danych DB2 z/OS na pureScale bazy danych DB2. 

Aby pomóc w podjęciu decyzji Najlepsza architektura pureScale bazy danych DB2 w danym środowisku, firma Microsoft zaleca pełni szacowanie rozmiaru i wprowadzić hipotezę. W systemie źródłowym, upewnij się, że należy wziąć pod uwagę bazy danych DB2 z/OS równoległych Sysplex z architektury udostępnianie danych, Konfiguracja funkcji sprzężenia i statystyki użycia funkcji (DDF) danych rozproszonych.

> [!NOTE]
> W tym artykule opisano jedno z podejść do migracji bazy danych DB2, ale istnieją inne osoby. Na przykład bazy danych DB2 pureScale można również uruchomić w środowiskach zwirtualizowanych w środowisku lokalnym. IBM obsługuje bazy danych DB2 na Microsoft Hyper-V w różnych konfiguracjach. Aby uzyskać więcej informacji, zobacz [architektura wirtualizacji pureScale bazy danych DB2](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html) w centrum wiedzy firmy IBM.

## <a name="architecture"></a>Architektura

Aby zapewnić obsługę wysokiej dostępności i skalowalności na platformie Azure, umożliwia architektury skalowalnego w poziomie, udostępnione dane pureScale bazy danych DB2. Migracja klientów używana następująca architektura przykład.

![PureScale bazy danych DB2 na maszynach wirtualnych Azure przedstawiający magazynu i sieci](media/db2-purescale-on-azure/pureScaleArchitecture.png "pureScale bazy danych DB2 na maszynach wirtualnych Azure przedstawiający magazynu i sieci")


Na diagramie przedstawiono warstwy logiczne wymagane dla klastra pureScale bazy danych DB2. Obejmują one maszyny wirtualne, dla klienta, zarządzania, do buforowania, aparatu bazy danych i stosowania magazynów udostępnionych. 

Oprócz węzłów aparatu bazy danych ma diagramu obejmuje dwa węzły klastra pamięci podręcznej urządzenia (CFs). Co najmniej dwa węzły są używane dla aparatu bazy danych, sam. Serwer bazy danych DB2, który należy do klastra pureScale nosi nazwę członka. 

Klaster jest połączona za pomocą interfejsu iSCSI klastra magazynu udostępnionego GlusterFS trzema węzłami, aby zapewnić magazynu skalowalnego w poziomie oraz wysoka dostępność. Bazy danych DB2 pureScale jest instalowany na maszynach wirtualnych platformy Azure z systemem Linux.

To podejście jest szablon, który można modyfikować dla rozmiaru i skali w Twojej organizacji. Jest on oparty na następujących czynności:

-   Co najmniej dwa elementy członkowskie bazy danych są połączone z co najmniej dwa węzły CF. Węzły Zarządzanie puli buforów globalne (GBP) dla pamięci współużytkowanej i globalne blokady usług Menedżera (GLM) do kontrolowania udostępnionego dostępu i blokadę przy użyciu rywalizacji o zasoby z aktywnych członków. Jeden węzeł CF działa jako podstawowy, a drugi jako węzła pomocniczego, CF trybu failover. Aby uniknąć pojedynczego punktu awarii w środowisku, klaster pureScale bazy danych DB2 wymaga co najmniej czterech węzłów.

-   Magazyn udostępniony o wysokiej wydajności (w rozmiar P30 w diagramie pokazano). Każdego z węzłów Gluster FS korzysta z tego magazynu.

-   Sieci o wysokiej wydajności dla składowych danych i magazynu udostępnionego.

### <a name="compute-considerations"></a>Zagadnienia dotyczące obliczeń

Ta architektura działa warstwy aplikacji, magazynu i danych na maszynach wirtualnych platformy Azure. [Skryptów Instalatora wdrożenia](https://aka.ms/db2onazure) Utwórz następującą:

-   Klaster pureScale bazy danych DB2. Rodzaj zasobów obliczeniowych, czego potrzebujesz na platformie Azure, zależy od konfiguracji. Ogólnie rzecz biorąc można użyć dwóch metod:

    -   Użyj z wieloma węzłami, wysokowydajnych obliczeń (HPC) — styl sieci, gdzie małych i średnich wystąpieniach będzie używanych uzyskują dostęp do magazynu udostępnionego. Dla tego typu HPC konfiguracji platformy Azure zoptymalizowane pod kątem pamięci serii E lub zoptymalizowanych pod kątem pamięci serii L [maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) zapewniają moc obliczeniową potrzebne.

    -   Użyj mniejszej liczby wystąpień dużą maszynę wirtualną dla aparatów danych. Dla dużych wystąpień największy zoptymalizowane pod kątem pamięci [serii M](https://azure.microsoft.com/pricing/details/virtual-machines/series/) maszyny wirtualne są idealne dla dużych obciążeń w pamięci. Może być konieczne dedykowanego wystąpienia, w zależności od rozmiaru partycji logicznej (LPAR), które jest używane do uruchamiania bazy danych DB2.

-   CF bazy danych DB2 używa zoptymalizowanej pod kątem pamięci maszyn wirtualnych, takich jak seria E lub serii L.

-   Magazyn GlusterFS używa standardu\_DS4\_v2 maszyny wirtualne z systemem Linux.

-   Rampa GlusterFS jest standardem\_DS2\_maszyny wirtualnej v2 z systemem Linux.

-   Klient jest standardem\_DS3\_maszyny wirtualnej v2 z systemem Windows (używane do testowania).

-   Serwera monitora jest standardem\_DS3\_maszyny wirtualnej v2 z systemem Linux (używane dla bazy danych DB2 pureScale).

> [!NOTE]
> Klaster pureScale bazy danych DB2 wymaga co najmniej dwa wystąpienia bazy danych DB2. Wymaga również wystąpienie pamięci podręcznej i uzamknout instanci menedżera.

### <a name="storage-considerations"></a>Zagadnienia dotyczące magazynu

Podobnie jak RAC Oracle DB2 pureScale jest blok o wysokiej wydajności we/wy, skalowalnego w poziomie bazy danych. Firma Microsoft zaleca korzystanie w największym [platformy Azure w warstwie premium SSD](disks-types.md) opcji, który odpowiada Twoim potrzebom. Mniejsze opcje magazynu może być odpowiedni dla środowisk deweloperskich i testowych, podczas gdy środowisk produkcyjnych często muszą większa pojemność magazynu. Architektura przykład używa [P30](https://azure.microsoft.com/pricing/details/managed-disks/) ze względu na pierwotny współczynnik operacji We/Wy, wielkości i ceny. Bez względu na rozmiar Użyj usługi Premium Storage, aby uzyskać najlepszą wydajność.

Bazy danych DB2 pureScale używa udostępnionego — wszystko, czego architektury, w którym wszystkie dane są dostępne ze wszystkich węzłów klastra. Usługa Premium storage muszą zostać udostępnione w wystąpieniach, czy na żądanie lub na dedykowanych wystąpieniach.

Duży klaster pureScale bazy danych DB2 może wymagać 200 terabajtów (TB) lub więcej premium udostępnione dla magazynu, operacje We/Wy wynoszącego 100 000. Bazy danych DB2 pureScale obsługuje interfejs bloku iSCSI, których używasz na platformie Azure. Interfejs iSCSI wymaga klastra magazynu udostępnionego, który można implementować przy użyciu GlusterFS, S2D lub innego narzędzia. Tego typu rozwiązań tworzy urządzenia sieciowego (vSAN) obszaru magazynów wirtualnych na platformie Azure. Bazy danych DB2 pureScale używa sieci vSAN, w celu zainstalowania systemu plików, która umożliwia udostępnianie danych między maszynami wirtualnymi.

Architektura przykład używa GlusterFS, bezpłatny, skalowalne, typu open-source rozproszonego systemu plików zoptymalizowana pod kątem magazynu w chmurze.

### <a name="networking-considerations"></a>Zagadnienia dotyczące pracy w sieci

IBM zaleca sieci InfiniBand dla wszystkich elementów członkowskich w klastrze pureScale bazy danych DB2. Bazy danych DB2 pureScale również używa zdalnego bezpośredniego dostępu do pamięci (RDMA), gdzie są dostępne, CFs.

Podczas instalacji, Utwórz Azure [grupy zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) ma zawierać wszystkie maszyny wirtualne. Ogólnie rzecz biorąc grupowanie zasobów, w oparciu o ich okres istnienia i który będzie zarządzać nimi. Maszyny wirtualne w ramach tej architektury wymagają [funkcji przyspieszonej łączności sieciowej](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Jest to nowa funkcja platformy Azure, który zapewnia spójne, bardzo niskimi opóźnienie za pośrednictwem wirtualizację we/wy pojedynczego elementu głównego (SR-IOV) do maszyny wirtualnej.

Co maszyna wirtualna platformy Azure jest wdrażana w sieci wirtualnej, która ma podsieci: main, Gluster FS frontonu (gfsfe), Gluster FS zaplecza (bfsbe), bazy danych DB2 pureScale (db2be) i bazy danych DB2 pureScale frontonu zakończenia (db2fe). Skrypt instalacji tworzy także podstawowego [kart sieciowych](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics) na maszynach wirtualnych w podsieci głównego.

Użyj [sieciowe grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) do ograniczania ruchu sieciowego w ramach sieci wirtualnej i w celu odizolowania podsieci.

Na platformie Azure bazy danych DB2 pureScale musi używać protokołu TCP/IP jako połączenie sieciowe dla magazynu.

## <a name="next-steps"></a>Kolejne kroki

-   [Wdrożyć tę architekturę na platformie Azure](deploy-ibm-db2-purescale-azure.md)
