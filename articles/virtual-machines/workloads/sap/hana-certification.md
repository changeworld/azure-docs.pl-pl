---
title: Certyfikacja sap HANA na platformie Azure (duże wystąpienia) | Dokumenty firmy Microsoft
description: Certyfikacja SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2a02f0e1b05b9de8105126d1c9e4e3f79057285f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617226"
---
# <a name="certification"></a>Certyfikacja

Oprócz certyfikacji NetWeaver sap wymaga specjalnego certyfikatu dla sap hana do obsługi SAP HANA w niektórych infrastrukturach, takich jak Azure IaaS.

Podstawową notatką SAP w programie NetWeaver i w pewnym stopniu certyfikatem SAP HANA jest [SAP Note #1928533 — aplikacje SAP na platformie Azure: obsługiwane produkty i typy maszyn wirtualnych platformy Azure.](https://launchpad.support.sap.com/#/notes/1928533)

Rekordy certyfikacji dla jednostek SAP HANA na platformie Azure (duże wystąpienia) można znaleźć w witrynie [platformy IaaS z certyfikatem SAP HANA.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) 

Typy SAP HANA na platformie Azure (duże wystąpienia), o których mowa w witrynie platformy IaaS z certyfikatem SAP HANA, zapewniają klientom firmy Microsoft i SAP możliwość wdrażania dużych obciążeń SAP Business Suite, SAP BW, S/4 HANA, BW/4HANA lub innych obciążeń SAP HANA na platformie Azure. Rozwiązanie oparte jest na dedykowanym znaczku sprzętowym z certyfikatem SAP-HANA ([sap HANA tailored data center integration – TDI](https://scn.sap.com/docs/DOC-63140)). Jeśli uruchomisz rozwiązanie skonfigurowane przez SAP HANA TDI, wszystkie aplikacje oparte na SAP HANA (takie jak SAP Business Suite w SAP HANA, SAP BW w SAP HANA, S4/HANA i BW4/HANA) działają na infrastrukturze sprzętowej.

W porównaniu do uruchamiania SAP HANA w maszynach wirtualnych, to rozwiązanie ma korzyści. Zapewnia znacznie większe woluminy pamięci. Aby włączyć to rozwiązanie, należy zrozumieć następujące kluczowe aspekty:

- Warstwa aplikacji SAP i aplikacje inne niż SAP są uruchamiane na maszynach wirtualnych, które są hostowane w zwykłych sygnaturach sprzętowych platformy Azure.
- Infrastruktura lokalna klienta, centra danych i wdrożenia aplikacji są połączone z platformą w chmurze za pośrednictwem usługi ExpressRoute (zalecane) lub wirtualnej sieci prywatnej (VPN). Usługa Active Directory i DNS są również rozszerzone na platformę Azure.
- Wystąpienie bazy danych SAP HANA dla obciążenia HANA jest uruchamiane na platformie SAP HANA na platformie Azure (duże wystąpienia). Sygnatura Dużego wystąpienia jest podłączona do sieci platformy Azure, dzięki czemu oprogramowanie działające na maszynach wirtualnych może wchodzić w interakcje z wystąpieniem HANA uruchomionym w dużym wystąpieniu HANA.
- Sprzęt SAP HANA na platformie Azure (Duże wystąpienia) to dedykowany sprzęt dostarczany w systemie IaaS z preinstalowanym systemem SUSE Linux Enterprise Server lub Red Hat Enterprise Linux. Podobnie jak w przypadku maszyn wirtualnych, dalsze aktualizacje i konserwacja systemu operacyjnego jest twoim obowiązkiem.
- Instalacja HANA lub wszelkie dodatkowe składniki niezbędne do uruchomienia SAP HANA na jednostkach HANA Duże wystąpienie jest twoim obowiązkiem. Wszystkie bieżące operacje i administrowanie SAP HANA na platformie Azure są również twoim obowiązkiem.
- Oprócz rozwiązań opisanych w tym miejscu można zainstalować inne składniki w ramach subskrypcji platformy Azure, która łączy się z sap HANA na platformie Azure (duże wystąpienia). Przykładami są składniki, które umożliwiają komunikację z bazą danych SAP HANA lub bezpośrednio z nią, takie jak serwery szybkiego dostępu, serwery RDP, SAP HANA Studio, usługi SAP Data Services dla scenariuszy SAP BI lub rozwiązania do monitorowania sieci.
- Podobnie jak na platformie Azure, hana duże wystąpienie oferuje obsługę wysokiej dostępności i funkcji odzyskiwania po awarii.

**Następne kroki**
- Zapoznaj się [z dostępnymi jednostkami SKU dla HLI](hana-available-skus.md) 