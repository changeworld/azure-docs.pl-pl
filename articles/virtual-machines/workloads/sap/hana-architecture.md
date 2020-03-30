---
title: Architektura sap HANA na platformie Azure (duże wystąpienia) | Dokumenty firmy Microsoft
description: Architektura sposobu wdrażania sap HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 041da4198b0bdd040a4916008a1135aa2e2a5f7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614524"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>Architektura SAP HANA (duże wystąpienia) na platformie Azure

Na wysokim poziomie rozwiązanie SAP HANA na platformie Azure (duże wystąpienia) ma warstwę aplikacji SAP zamieszkałą na maszynach wirtualnych. Warstwa bazy danych znajduje się na sprzęcie skonfigurowanym przez SAP TDI, znajdującym się w sygnatury dużego wystąpienia w tym samym regionie platformy Azure, który jest połączony z usługą Azure IaaS.

> [!NOTE]
> Wdrażanie warstwy aplikacji SAP w tym samym regionie platformy Azure co warstwa SAP DBMS. Ta reguła jest dobrze udokumentowana w opublikowanych informacjach o obciążeniach SAP na platformie Azure. 

Ogólna architektura sap HANA na platformie Azure (duże wystąpienia) zapewnia konfigurację sprzętową z certyfikatem SAP TDI, która jest niewirtualizowanym, nieuwirtualizowanym, nieczytym, wydajnym serwerem bazy danych SAP HANA. Zapewnia również możliwość i elastyczność platformy Azure do skalowania zasobów dla warstwy aplikacji SAP, aby spełnić Twoje potrzeby.

![Omówienie architektury sap HANA na platformie Azure (duże wystąpienia)](./media/hana-overview-architecture/image1-architecture.png)

Pokazana architektura jest podzielona na trzy sekcje:

- **Po prawej:** Pokazuje infrastrukturę lokalną, która uruchamia różne aplikacje w centrach danych, dzięki czemu użytkownicy końcowi mogą uzyskiwać dostęp do aplikacji LOB, takich jak SAP. W idealnym przypadku ta infrastruktura lokalna jest połączona z platformą Azure za pomocą usługi [ExpressRoute.](https://azure.microsoft.com/services/expressroute/)

- **Centrum:** Pokazuje usługę Azure IaaS i, w tym przypadku, użycie maszyn wirtualnych do obsługi SAP lub innych aplikacji, które używają SAP HANA jako systemu DBMS. Mniejsze wystąpienia HANA, które działają z pamięcią, które zapewniają maszyny wirtualne są wdrażane na maszynach wirtualnych wraz z ich warstwy aplikacji. Aby uzyskać więcej informacji na temat maszyn wirtualnych, zobacz [Maszyny wirtualne](https://azure.microsoft.com/services/virtual-machines/).

   Usługi sieciowe platformy Azure są używane do grupowania systemów SAP wraz z innymi aplikacjami w sieci wirtualne. Te sieci wirtualne łączą się z systemami lokalnymi, a także z sap HANA na platformie Azure (duże wystąpienia).

   W przypadku aplikacji i baz danych SAP NetWeaver, które są obsługiwane do uruchamiania na platformie Azure, zobacz [Uwaga dotycząca pomocy technicznej SAP #1928533 — aplikacje SAP na platformie Azure: obsługiwane produkty i typy maszyn wirtualnych platformy Azure.](https://launchpad.support.sap.com/#/notes/1928533) Aby uzyskać dokumentację dotyczącą wdrażania rozwiązań SAP na platformie Azure, zobacz:

  -  [Używanie sapu na maszynach wirtualnych z systemem Windows](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Używanie rozwiązań SAP na maszynach wirtualnych platformy Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **Po lewej:** Pokazuje sprzęt z certyfikatem SAP HANA TDI w scenach dużego wystąpienia platformy Azure. Jednostki dużych wystąpień HANA są połączone z sieciami wirtualnymi subskrypcji platformy Azure przy użyciu tej samej technologii, co łączność z lokalnego na platformie Azure. Od maja 2019 r. wprowadzono optymalizację, która umożliwia komunikację między jednostkami dużych wystąpień HANA a maszynami wirtualnymi platformy Azure bez udziału bramy usługi ExpressRoute. Ta optymalizacja o nazwie Szybka ścieżka usługi ExpressRoute jest wyświetlana w tej architekturze (czerwone linie). 

Sam sygnatura dużego wystąpienia platformy Azure łączy następujące składniki:

- **Przetwarzanie:** Serwery oparte na różnych generacjach procesorów Intel Xeon, które zapewniają niezbędne możliwości obliczeniowe i posiadają certyfikat SAP HANA.
- **Sieć**: Ujednolicona sieć szkieletowa o dużej szybkości, która łączy komponenty obliczeniowe, pamięci masowej i sieci LAN.
- **Magazyn:** infrastruktura magazynu, która jest dostępna za pośrednictwem ujednoliconej sieci szkieletowej. Określona pojemność magazynu, która jest dostarczana zależy od określonej konfiguracji SAP HANA na platformie Azure (duże wystąpienia), która jest wdrażana. Większa pojemność pamięci masowej dostępna jest za dodatkową opłatą miesięczną.

W ramach infrastruktury wielu dzierżawców sygnatury Dużych wystąpień klienci są wdrażane jako izolowanych dzierżawców. Podczas wdrażania dzierżawy nadajesz nazwę subskrypcji platformy Azure w ramach rejestracji platformy Azure. Ta subskrypcja platformy Azure jest tym, za który naliczane są opłaty za duże wystąpienie HANA. Dzierżawy te mają relację 1:1 z subskrypcją platformy Azure. W przypadku sieci można uzyskać dostęp do jednostki dużych wystąpień HANA wdrożonej w jednej dzierżawie w jednym regionie platformy Azure z różnych sieci wirtualnych należących do różnych subskrypcji platformy Azure. Te subskrypcje platformy Azure muszą należeć do tej samej rejestracji platformy Azure. 

Podobnie jak w przypadku maszyn wirtualnych, sap HANA na platformie Azure (duże wystąpienia) jest oferowana w wielu regionach platformy Azure. Aby zaoferować funkcje odzyskiwania po awarii, możesz wyrazić zgodę. Różne znaczki dużych instancji w obrębie jednego regionu geopolitycznego są ze sobą połączone. Na przykład sygnatury dużych wystąpień HANA w stanach Zachodnich i Wschodnich stanów Usa są połączone za pośrednictwem dedykowanego łącza sieciowego do replikacji odzyskiwania po awarii. 

Podobnie jak można wybrać między różnymi typami maszyn wirtualnych za pomocą maszyn wirtualnych platformy Azure, można wybrać z różnych jednostek SKU HANA duże wystąpienie, które są dostosowane do różnych typów obciążeń SAP HANA. Sap stosuje współczynniki pamięci do gniazda procesora dla różnych obciążeń w oparciu o generacje procesorów Intel. W poniższej tabeli przedstawiono oferowane typy jednostek SKU.

Dostępne [jednostki SKU dostępne dla HLI](hana-available-skus.md)można znaleźć.

**Następne kroki**
- Odwoływanie się do [architektury sieci SAP HANA (duże wystąpienia)](hana-network-architecture.md)