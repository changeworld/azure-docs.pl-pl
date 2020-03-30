---
title: Poznaj warunki sap HANA na platformie Azure (duże wystąpienia) | Dokumenty firmy Microsoft
description: Poznaj warunki SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6785ae821f701121185f0064c6317c69d50191ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617065"
---
# <a name="know-the-terms"></a>Poznawanie warunków

Kilka wspólnych definicji są szeroko stosowane w architekturze i przewodnik wdrażania technicznego. Zwróć uwagę na następujące terminy i ich znaczenie:

- **IaaS**: Infrastruktura jako usługa.
- **PaaS**: Platforma jako usługa.
- **SaaS**: Oprogramowanie jako usługa.
- **Składnik SAP**: Pojedyncza aplikacja SAP, taka jak ERP Central Component (ECC), Business Warehouse (BW), Solution Manager lub Enterprise Portal (EP). Składniki SAP mogą być oparte na tradycyjnych technologiach ABAP lub Java lub aplikacji nienapartej na NetWeaver, takiej jak Obiekty biznesowe.
- **Środowisko SAP:** Co najmniej jeden składnik SAP logicznie pogrupowany w celu wykonania funkcji biznesowej, takiej jak programowanie, zapewnienie jakości, szkolenie, odzyskiwanie po awarii lub produkcja.
- **Krajobraz SAP:** Odnosi się do całych zasobów SAP w krajobrazie IT. Krajobraz SAP obejmuje wszystkie środowiska produkcyjne i nieprodukcyjne.
- **System SAP**: Połączenie warstwy DBMS i warstwy aplikacji, na przykład systemu rozwoju SAP ERP, systemu testowego SAP BW i systemu produkcyjnego SAP CRM. Wdrożenia platformy Azure nie obsługują podziału tych dwóch warstw między lokalnie i platformy Azure. System SAP jest wdrażany lokalnie lub jest wdrażany na platformie Azure. Można wdrożyć różne systemy środowiska SAP na platformie Azure lub lokalnie. Na przykład można wdrożyć sap CRM rozwoju i testowania systemów na platformie Azure podczas wdrażania systemu produkcyjnego SAP CRM w środowisku lokalnym. W przypadku sap HANA na platformie Azure (duże wystąpienia) jest przeznaczony do hostowania warstwy aplikacji SAP systemów SAP w maszynach wirtualnych i powiązanego wystąpienia SAP HANA na jednostce w sap HANA na platformie Azure (duże wystąpienia) znaczek.
- **Sygnatura dużych wystąpień:** stos infrastruktury sprzętowej z certyfikatem SAP HANA TDI i przeznaczony do uruchamiania wystąpień SAP HANA na platformie Azure.
- **SAP HANA na platformie Azure (duże wystąpienia):** Oficjalna nazwa oferty na platformie Azure do uruchamiania wystąpień HANA w na SAP HANA TDI certyfikat sprzętu, który jest wdrażany w sygnaturach dużych wystąpień w różnych regionach platformy Azure. Powiązany termin *HANA Duże wystąpienie* jest skrótem od *SAP HANA na platformie Azure (duże wystąpienia)* i jest szeroko stosowany w tym przewodniku wdrażania technicznego.
- **Między środowiskiem:** w tym artykule opisano scenariusz, w którym maszyny wirtualne są wdrażane w ramach subskrypcji platformy Azure, która ma łączność między lokacjami, wieloma lokacjami lub usługami Azure ExpressRoute między lokalnymi centrami danych a platformą Azure. We wspólnej dokumentacji platformy Azure tego rodzaju wdrożenia są również opisane jako scenariusze międzylokacyjnymi. Powodem połączenia jest rozszerzenie domen lokalnych, lokalnej usługi Azure Active Directory/OpenLDAP i lokalnego systemu DNS na platformę Azure. Lokalny krajobraz jest rozszerzony na zasoby platformy Azure subskrypcji platformy Azure. Dzięki temu rozszerzeniu maszyny wirtualne mogą być częścią domeny lokalnej. 

   Użytkownicy domeny lokalnej mogą uzyskiwać dostęp do serwerów i uruchamiać usługi na tych maszynach wirtualnych (takich jak usługi DBMS). Komunikacja i rozpoznawanie nazw między maszynami wirtualnymi wdrożonymi lokalnie i maszynami wirtualnymi wdrożonymi w platformie Azure jest możliwe. Ten scenariusz jest typowy dla sposobu, w jaki większość zasobów SAP są wdrażane. Aby uzyskać więcej informacji, zobacz [Brama sieci VPN platformy Azure](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) i Tworzenie sieci [wirtualnej z połączeniem lokacja-lokacja przy użyciu portalu Azure.](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- **Dzierżawa:** Klient wdrożony w sieczmię dużych wystąpień HANA zostaje odizolowany do *dzierżawy.* Dzierżawca jest izolowany w warstwie sieci, magazynu i obliczeniowego od innych dzierżawców. Jednostki magazynu i obliczeniowe przypisane do różnych dzierżaw nie widzą się nawzajem ani nie komunikują się ze sobą na poziomie stempla dużego wystąpienia HANA. Klient może wybrać wdrożenia w różnych dzierżawach. Nawet wtedy nie ma komunikacji między dzierżawami na poziomie stempla dużego wystąpienia HANA.
- **Kategoria SKU**: Dla wystąpienia dużych HANA oferowane są następujące dwie kategorie jednostek SKU:
    - **Klasa typu I:** S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 i S224m
    - **Klasa typu II:** S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm i S960m
- **Sygnatura**: Definiuje rozmiar wewnętrznego wdrożenia firmy Microsoft wystąpienia dużych wystąpień HANA. Zanim jednostki dużych wystąpień HANA mogą zostać wdrożone, sieka dużych wystąpień HANA składająca się z serwerów obliczeniowych, sieciowych i magazynowych musi zostać wdrożona w lokalizacji centrum danych. Takie wdrożenie jest nazywane znaczekem HANA Duże wystąpienie lub z wersji 4 (patrz poniżej) na używamy alternatywnego terminu **Wiersz dużych wystąpień**
- **Wersja**: Istnieją dwie różne wersje stempla dla znaczków hana dużych wystąpień. Różnią się one architekturą i bliskością hostów maszyn wirtualnych platformy Azure
    - "Wersja 3" (Rev 3): to oryginalny projekt, który został wdrożony od połowy roku 2016
    - "Wersja 4" (Rev 4): to nowy projekt, który może zapewnić bliższą bliskość hostów maszyn wirtualnych platformy Azure i z mniejszym opóźnieniem sieci między maszynami wirtualnymi platformy Azure a jednostkami dużych wystąpień HANA 

Dostępnych jest wiele dodatkowych zasobów dotyczących wdrażania obciążenia SAP w chmurze. Jeśli planujesz wykonać wdrożenie SAP HANA na platformie Azure, musisz mieć doświadczenie i znać zasady usługi Azure IaaS i wdrażanie obciążeń SAP na platformie Azure IaaS. Aby kontynuować, zobacz [Korzystanie z rozwiązań SAP na maszynach wirtualnych platformy Azure, aby](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) uzyskać więcej informacji. 

**Następne kroki**
- Zapoznaj się z [certyfikatem HLI](hana-certification.md)