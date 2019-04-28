---
title: Architektura środowiska SAP Hana na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Architektura sposób wdrażania oprogramowania SAP HANA na platformie Azure (duże wystąpienia).
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
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 80ef63cdd9de8cb2340fe15d761402bb9f00fae9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60795954"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>Architektura SAP HANA (duże wystąpienia) na platformie Azure

Na wysokim poziomie SAP HANA na platformie Azure (duże wystąpienia) rozwiązanie ma warstwy aplikacji SAP znajdującej się na maszynach wirtualnych. Warstwa bazy danych znajduje się na sprzęcie skonfigurowane SAP TDI, znajduje się w sygnaturze dużych wystąpień, w tym samym regionie platformy Azure, która jest połączona z infrastrukturą IaaS usługi Azure.

> [!NOTE]
> Wdróż warstwę aplikacji SAP, w tym samym regionie platformy Azure jako warstwa SAP DBMS. Ta reguła jest dobrze udokumentowana w opublikowanych informacji na temat obciążeń SAP na platformie Azure. 

Ogólna Architektura środowiska SAP Hana na platformie Azure (duże wystąpienia) udostępnia konfigurację sprzęt z certyfikatem TDI i SAP, czyli poziomie wirtualizacji, komputerach bez systemu operacyjnego, serwer o wysokiej wydajności dla bazy danych SAP HANA. Zapewnia również możliwość i elastyczność platformy Azure, skalować zasoby warstwy aplikacji SAP do własnych potrzeb.

![Omówienie architektury oprogramowania SAP Hana na platformie Azure (duże wystąpienia)](./media/hana-overview-architecture/image1-architecture.png)

Architektura, wyświetlane jest podzielony na trzy sekcje:

- **prawy**: Pokazuje infrastruktury lokalnej, uruchamianego z różnych aplikacji w danych centra tak, aby użytkownicy końcowi mogą uzyskiwać dostęp LOB aplikacji, takich jak SAP. W idealnym przypadku tej lokalnej infrastruktury następnie jest połączony z platformą Azure za pomocą [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Centrum**: Pokazuje IaaS platformy Azure i, w tym przypadku korzystanie z maszyn wirtualnych w celu hostowania SAP lub inne aplikacje korzystające z platformy SAP HANA jako DBMS system. Mniejsze wystąpień HANA, które funkcją pamięci, które maszyny wirtualne są wdrażane na maszynach wirtualnych wraz z ich warstwy aplikacji. Aby uzyskać więcej informacji na temat maszyn wirtualnych, zobacz [maszyn wirtualnych](https://azure.microsoft.com/services/virtual-machines/).

   Usługi sieci platformy Azure są używane do grupowania systemów SAP wraz z innych aplikacji w sieciach wirtualnych. Te sieci wirtualne nawiązać połączenie systemów lokalnych, jak również do platformy SAP HANA na platformie Azure (duże wystąpienia).

   Oprogramowanie SAP NetWeaver aplikacje i bazy danych, które są obsługiwane na platformie Azure, zobacz [SAP pomocy technicznej Uwaga #1928533 — aplikacje środowiska SAP na platformie Azure: Obsługiwane produkty i typy maszyn wirtualnych platformy Azure](https://launchpad.support.sap.com/#/notes/1928533). Aby uzyskać dokumentację na temat wdrażania rozwiązań SAP na platformie Azure zobacz:

  -  [Używanie SAP na maszynach wirtualnych Windows](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Korzystanie z rozwiązań SAP na maszynach wirtualnych platformy Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **po lewej stronie**: Zawiera sprzęt z certyfikatem TDI i programu SAP HANA w sygnaturze dużego wystąpienia platformy Azure. Jednostki dużych wystąpień HANA są połączone z sieciami wirtualnymi w subskrypcji przy użyciu tej samej technologii co łączność ze środowiska lokalnego na platformę Azure.

Sygnatura dużego wystąpienia platformy Azure, sama obejmuje następujące składniki:

- **Przetwarzanie**: Serwery, które są oparte na procesorach Intel Xeon E7-8890v3 lub Intel Xeon E7-8890v4, zapewniają wymagane możliwości obliczeniowych, które zostały SAP HANA z certyfikatem platformy.
- **Sieć**: Ujednolicone szybkich sieci szkieletowej, łączącym obliczeń, magazynu i składniki sieci LAN.
- **Magazyn**: Infrastruktura magazynu, która jest dostępna za pośrednictwem jednolitego sieci szkieletowej. Określonej pojemności, który znajduje się zależy od określonego oprogramowania SAP HANA na konfiguracji platformy Azure (duże wystąpienia), który jest wdrożony. Większa pojemność magazynu jest dostępna za dodatkową opłatą miesięcznych.

W ramach infrastruktury wielodostępnych sygnatury dużych wystąpień klienci są wdrażane jako dzierżaw izolowane. Podczas wdrażania dzierżawy nadaj nazwę subskrypcji platformy Azure w ramach rejestracji na platformie Azure. Tej subskrypcji platformy Azure jest rozliczany w dużych wystąpień HANA. Te dzierżawy mają relację 1:1 z subskrypcją platformy Azure. Dla sieci jest możliwe uzyskanie dostępu Jednostka dużych wystąpień HANA wdrożony w jednej dzierżawy w jednym regionie platformy Azure z różnych sieci wirtualnych, które należą do różnych subskrypcji platformy Azure. Te subskrypcje platformy Azure muszą należeć do tej samej rejestracji na platformie Azure. 

Podobnie jak w przypadku maszyn wirtualnych, SAP HANA na platformie Azure (duże wystąpienia) jest oferowana w wielu regionach platformy Azure. Aby zaoferować awarii funkcje odzyskiwania, można zgodzić się na. Różnych sygnaturach duże wystąpienie w ramach jednego regionu polityczne geograficznej są połączone ze sobą. Na przykład HANA duże wystąpienia sygnatury w zachodnich stanach USA i wschodnie stany USA są połączone za pośrednictwem dedykowanej sieci łącza replikacji odzyskiwania po awarii. 

Tak samo, jak można wybrać między różnymi typami maszyny Wirtualnej z usługą Azure Virtual Machines, możesz wybrać różne jednostki SKU z platformy HANA duże wystąpienie, które są dostosowane do typów różnych obciążeń SAP Hana. SAP dotyczy stosunek pamięci do procesorów gniazda dla różnych obciążeń, oparte na generacje procesor Intel. W poniższej tabeli przedstawiono oferowanych typów jednostek SKU.

Możesz znaleźć dostępne jednostki SKU [jednostki SKU dostępne dla HLI](hana-available-skus.md).

**Następne kroki**
- Zapoznaj się [architekturę sieci typu platformy SAP HANA (duże wystąpienia)](hana-network-architecture.md)