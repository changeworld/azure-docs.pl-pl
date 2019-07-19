---
title: Poznaj warunki SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Poznaj warunki SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2564e0d34383f7c1daff5b02a871778fb90546cc
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868992"
---
# <a name="know-the-terms"></a>Poznawanie warunków

Kilka typowych definicji jest szeroko używanych w przewodniku wdrażania architektury i techniczne. Należy zwrócić uwagę na następujące warunki i ich znaczenie:

- **IaaS**: Infrastruktura jako usługa.
- **PaaS**: Platforma jako usługa.
- **SaaS**: Oprogramowanie jako usługa.
- **Składnik SAP**: Pojedyncza aplikacja SAP, taka jak ERP Central Component (ECC), Business Warehouse (BW), Menedżer rozwiązań lub Enterprise Portal (EP). Składniki SAP mogą opierać się na tradycyjnych technologiach ABAP lub Java lub aplikacjach nieopartych na NetWeaver, takich jak obiekty biznesowe.
- **Środowisko SAP**: Co najmniej jeden składnik SAP logicznie zgrupowany do wykonywania funkcji biznesowej, takich jak programowanie, gwarancja jakości, szkolenia, odzyskiwanie po awarii lub produkcja.
- **SAP pozioma**: Odnosi się do całego zasobu SAP w krajobrazu IT. System SAP zawiera wszystkie środowiska produkcyjne i nieprodukcyjne.
- **System SAP**: Kombinacja warstwy systemu DBMS i warstwy aplikacji, na przykład system wdrażania SAP ERP, system testowy SAP BW i System produkcyjny SAP CRM. Wdrożenia platformy Azure nie obsługują dzielenia tych dwóch warstw między środowiskiem lokalnym i platformą Azure. System SAP został wdrożony lokalnie lub wdrożony na platformie Azure. Różne systemy oprogramowania SAP można wdrożyć na platformie Azure lub lokalnie. Można na przykład wdrożyć systemy deweloperskie i testowe oprogramowania SAP CRM na platformie Azure podczas wdrażania systemu produkcyjnego SAP CRM w środowisku lokalnym. W przypadku SAP HANA na platformie Azure (duże wystąpienia) zaplanowano hostowanie warstwy aplikacji SAP systemów SAP na maszynach wirtualnych i powiązanego wystąpienia SAP HANA w jednostce SAP HANA na platformie Azure (duże wystąpienia).
- **Sygnatura dużej instancji**: Stos infrastruktury sprzętowej, który jest SAP HANA certyfikat TDI i przeznaczony do uruchamiania SAP HANA wystąpień na platformie Azure.
- **SAP HANA na platformie Azure (duże wystąpienia):** Oficjalna nazwa oferty na platformie Azure służąca do uruchamiania wystąpień platformy HANA w systemie SAP HANA sprzętu z certyfikatem TDI, który jest wdrażany w sygnaturach dużych wystąpień w różnych regionach świadczenia usługi Azure. *Duże wystąpienie usługi Hana* jest krótkie dla *SAP HANA na platformie Azure (duże wystąpienia)* i jest szeroko używane w tym przewodniku wdrażania technicznego.
- **Wiele**lokalizacji: Zawiera opis scenariusza, w którym maszyny wirtualne są wdrażane w ramach subskrypcji platformy Azure, która ma łączność obejmującą lokację, wiele witryn lub platformę Azure ExpressRoute między lokalnymi centrami danych i platformą Azure. W typowej dokumentacji platformy Azure te rodzaje wdrożeń są również opisane jako scenariusze obejmujące wiele lokalizacji. Przyczyną połączenia jest rozszerzonie domen lokalnych, lokalnych Azure Active Directory/OpenLDAP i lokalnych nazw DNS na platformę Azure. Lokalne poziomy są rozszerzane do zasobów platformy Azure w ramach subskrypcji platformy Azure. Dzięki temu rozszerzeniu maszyny wirtualne mogą być częścią domeny lokalnej. 

   Użytkownicy domeny lokalnej mogą uzyskać dostęp do serwerów i uruchamiać usługi na tych maszynach wirtualnych (na przykład w usługach DBMS). Możliwa jest komunikacja i rozpoznawanie nazw między maszynami wirtualnymi wdrożonymi lokalnie i maszynami wirtualnymi wdrożonymi na platformie Azure. Ten scenariusz jest typowym sposobem, w jaki są wdrażane większość zasobów SAP. Aby uzyskać więcej informacji, zobacz temat [Azure VPN Gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) i [Tworzenie sieci wirtualnej z połączeniem lokacja-lokacja przy użyciu Azure Portal](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Dzierżawa**: Klient wdrożony w sygnaturze dużego wystąpienia HANA zostanie odizolowany do *dzierżawy.* Dzierżawa jest odizolowana od sieci, magazynu i warstwy obliczeniowej od innych dzierżawców. Jednostki magazynowe i obliczeniowe przypisane do różnych dzierżawców nie są ze sobą widoczne ani nie komunikują się ze sobą na poziomie sygnatury dużego wystąpienia HANA. Klient może wybrać wdrożenie w różnych dzierżawach. Nawet nie ma żadnej komunikacji między dzierżawcami na poziomie sygnatury dużego wystąpienia HANA.
- **Kategoria jednostki SKU**: W przypadku dużego wystąpienia HANA oferowane są następujące dwie kategorie jednostek SKU:
    - **Typ I Klasa**: S72, S72m, s96, S144, S144m, S192, S192m i S192xm
    - **Klasa typu II**: S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm i S960m
- **Sygnatura**: Definiuje wewnętrzny rozmiar wdrożenia platformy HANA dla firmy Microsoft. Przed wdrożeniem jednostek dużego wystąpienia HANA należy wdrożyć sygnaturę dużego wystąpienia HANA składającą się z stojaków obliczeniowych, sieci i magazynu w lokalizacji centrum danych. Takie wdrożenie nazywa się sygnaturą dużego wystąpienia HANA lub z poprawką 4 (patrz poniżej), w którym jest używany alternatywny okres **dużego wystąpienia wiersza**
- **Poprawka**: Istnieją dwie różne wersje sygnatury dla sygnatur dużych wystąpień usługi HANA. Różnią się one w architekturze i sąsiedztwie na hostach maszyn wirtualnych platformy Azure
    - "Poprawka 3" (Rev. 3): to oryginalny projekt, który został wdrożony w połowie roku 2016
    - "Wersja 4" (Rev. 4): to nowy projekt, który może zapewnić bliższą bliskość hostom maszyn wirtualnych platformy Azure, oraz to, co zmniejsza opóźnienie sieci między maszynami wirtualnymi platformy Azure i jednostkami dużej instancji HANA 

W przypadku wdrażania obciążeń SAP w chmurze dostępne są różne dodatkowe zasoby. Jeśli planujesz wykonać wdrożenie SAP HANA na platformie Azure, musisz mieć doświadczenie i wiedzieć o zasadach platformy Azure IaaS oraz o wdrażaniu obciążeń SAP na platformie Azure IaaS. Przed kontynuowaniem zobacz [Korzystanie z rozwiązań SAP na maszynach wirtualnych platformy Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , aby uzyskać więcej informacji. 

**Następne kroki**
- Odwołaj się do [certyfikacji](hana-certification.md) .