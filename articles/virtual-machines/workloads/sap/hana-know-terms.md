---
title: Znasz warunki środowiska SAP Hana na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Znasz warunki środowiska SAP Hana na platformie Azure (duże wystąpienia).
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
ms.openlocfilehash: 6d8197e3695da8869806e44775f601d95b17fcb2
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2018
ms.locfileid: "44028406"
---
# <a name="know-the-terms"></a>Znasz warunki

Kilka wspólnych definicji są powszechnie używane w zakresie architektury i technicznej Deployment Guide. Należy zwrócić uwagę, następujące warunki i ich znaczenie:

- **IaaS**: infrastruktura jako usługa.
- **PaaS**: platforma jako usługa.
- **SaaS**: oprogramowanie jako usługa.
- **Składnik SAP**: poszczególnych aplikacji SAP, takich jak ERP i przeniesieniu jej centralnej składnika (ECC), Business Warehouse (BW), Menedżer rozwiązania lub Enterprise Portal (EP). Składniki SAP mogą być oparte na tradycyjnych technologii ABAP i Java lub aplikacji innych niż NetWeaver na podstawie takich jak obiekty biznesowych.
- **Środowisko SAP**: co najmniej jednego składnika SAP są logicznie pogrupowane w celu wykonywania funkcji biznesowych, takich jak rozwój, zapewniania jakości, szkolenia, odzyskiwania po awarii lub produkcji.
- **Środowisko SAP**: odwołuje się do całego zasoby SAP w orientacji poziomej usługi IT. Środowisko SAP obejmuje wszystkie produkcji i środowisk nieprodukcyjnych.
- **SAP system**: kombinacja warstwy system DBMS i warstwy aplikacji, na przykład system SAP ERP i przeniesieniu jej rozwoju, system SAP BW testu i system produkcyjny SAP CRM. Wdrożeń platformy Azure nie obsługują dzielenia tych dwóch warstw między lokalną i platformą Azure. System SAP jest wdrożone w środowisku lokalnym lub w jego wdrożonych na platformie Azure. Możesz wdrożyć różnych systemów środowisko SAP na platformie Azure lub lokalnie. Na przykład można wdrożyć rozwoju SAP CRM i systemy testowe na platformie Azure, podczas wdrażania SAP CRM produkcji systemu lokalnego. Dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia) jest on przeznaczony hosta z warstwy aplikacji SAP systemów SAP na maszynach wirtualnych i powiązanych wystąpienie SAP HANA na platformie SAP HANA na platformie Azure (duże wystąpienia) sygnatury jednostce.
- **Duże wystąpienia sygnatury**: stos infrastruktury sprzętu, który jest certyfikatem TDI i programu SAP HANA i dedykowane do uruchamiania wystąpień oprogramowania SAP HANA w systemie Azure.
- **Oprogramowanie SAP HANA na platformie Azure (duże wystąpienia):** oficjalną nazwą języka dla oferty na platformie Azure do uruchamiania oprogramowania HANA wystąpienia w na sprzęcie z certyfikatem TDI i programu SAP HANA, które zostało wdrożone w dużych wystąpień sygnatury w różnych regionach platformy Azure. Powiązane termin *dużych wystąpień HANA* jest mała w przypadku *platformy SAP HANA na platformie Azure (duże wystąpienia)* i jest powszechnie używana w tym przewodniku wdrożenia technicznego.
- **Między środowiskami lokalnymi**: w tym artykule opisano scenariusz, w której maszyny wirtualne są wdrażane z subskrypcją platformy Azure, site to site, obejmujące wiele lokacji lub połączenie usługi Azure ExpressRoute między centrami danych w środowisku lokalnym i platformą Azure. Dokumentacja wspólnych platformy Azure, tego rodzaju wdrożenia są również opisać jako scenariuszy obejmujących wiele lokalizacji. Przyczyna połączenie ma rozszerzone na platformę Azure w domenach lokalnych, lokalnej usługi Azure Active Directory/OpenLDAP i DNS w środowisku lokalnym. Pozioma w środowisku lokalnym jest rozszerzony do zasobów platformy Azure w subskrypcji platformy Azure. Dla tego rozszerzenia maszyn wirtualnych może być częścią domeny w środowisku lokalnym. 

   Użytkownicy domeny z domeny lokalnej można uzyskiwać dostęp do serwerów i uruchamiania usług na tych maszynach wirtualnych (takiej jak DBMS usługi). Komunikacja i rozpoznawanie nazw między maszynami wirtualnymi wdrożony w środowisku lokalnym i maszyn wirtualnych wdrożonych przez Azure jest możliwe. Ten scenariusz jest typowy sposób, w którym większość zasobów SAP są wdrażane. Aby uzyskać więcej informacji, zobacz [planowania i projektowania dla bramy sieci VPN Azure](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) i [tworzenie sieci wirtualnej za pomocą połączenia typu lokacja lokacja przy użyciu witryny Azure portal](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Dzierżawy**: klient wdrożony w sygnaturze dużych wystąpień HANA pobiera izolowane do *dzierżawy.* Dzierżawa jest odizolowany w sieci, magazynu i warstwy obliczeniowej od pozostałych dzierżawców. Jednostki magazynu i mocy obliczeniowej, przypisane do różnych dzierżawach nie widzę, siebie nawzajem i komunikować się ze sobą na poziomie sygnatury dużych wystąpień HANA. Klient może wybrać wdrożenie w różnych dzierżawach. Nawet wówczas nie ma komunikacji między dzierżawami, na poziomie sygnatury dużych wystąpień HANA.
- **Jednostka SKU kategorii**: dla platformy HANA duże wystąpienie, są dostępne w następujących dwóch kategorii jednostek SKU:
    - **Typ I klasa**: S72, S72m, S144, S144m, platformie S192, platformie S192m i S192xm
    - **Typ klasy II**: S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm i S960m


Szereg dodatkowych zasobów są dostępne w sposób wdrażania obciążeń SAP w chmurze. Jeśli planujesz wykonać wdrożenie oprogramowania SAP Hana na platformie Azure, musisz być doświadczenie w pracy z i korzystają z zasadami modelu IaaS platformy Azure i wdrażanie obciążeń SAP na platformie IaaS Azure. Przed kontynuowaniem, zobacz [rozwiązania użycia adapterów SAP na maszynach wirtualnych Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Aby uzyskać więcej informacji. 

**Następne kroki**
- Zapoznaj się [HLI certyfikacji](hana-certification.md)