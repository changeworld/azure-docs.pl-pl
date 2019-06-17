---
title: Certyfikacja platformy SAP HANA na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Certyfikacja platformy SAP HANA na platformie Azure (duże wystąpienia).
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
ms.openlocfilehash: 15de566d756d6b0f7719eabf74ee9c7ac66659d6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60793995"
---
# <a name="certification"></a>Certyfikacja

Oprócz certyfikacji NetWeaver środowisko SAP wymaga specjalnych certyfikacji platformy SAP Hana do obsługi platformy SAP HANA na niektórych infrastruktury, takich jak Azure IaaS.

Core Uwaga SAP w oprogramowaniu NetWeaver oraz stopień certyfikacji platformy SAP HANA, to [SAP Uwaga #1928533 — aplikacje środowiska SAP na platformie Azure: Obsługiwane produkty i typy maszyn wirtualnych platformy Azure](https://launchpad.support.sap.com/#/notes/1928533).

Rekordy certyfikacji dla platformy SAP HANA na platformie Azure (duże wystąpienia) jednostki można znaleźć w [platform IaaS z certyfikatem SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) lokacji. 

Oprogramowanie SAP HANA na typy Azure (duże wystąpienia), zgodnie z platformy SAP HANA certyfikat witryny IaaS platformy zapewnia firmy Microsoft i SAP klientom możliwość wdrażania dużych SAP Business Suite, SAP BW, S/4 HANA, BW/4HANA lub innych obciążeń SAP HANA na platformie Azure. To rozwiązanie jest oparte na sygnatury certyfikowanym sprzęcie dedykowanym platformy SAP HANA ([platformy SAP HANA dostosowane integrację centrum danych — TDI](https://scn.sap.com/docs/DOC-63140)). Jeśli uruchamiasz rozwiązanie SAP HANA TDI skonfigurowane, wszystkie aplikacje oparte na oprogramowanie SAP HANA (takie jak oprogramowanie SAP Business Suite na platformie SAP HANA, SAP BW na BW4/HANA, SAP HANA i S4/HANA) działa na infrastruktury sprzętowej.

W porównaniu do uruchamiania oprogramowania SAP HANA na maszynach wirtualnych, to rozwiązanie ma jedną z korzyści. Oferuje ona dużo większe woluminy pamięci. Aby włączyć to rozwiązanie, należy zrozumieć następujące kluczowe aspekty:

- Uruchom aplikacje warstwy i innych niż SAP aplikacji SAP na maszynach wirtualnych, które są hostowane w sygnatury zwykle sprzętowych platformy Azure.
- Klient w obrębie lokalnej infrastruktury, centrów danych oraz wdrożenia aplikacji są podłączone do platforma usług w chmurze za pośrednictwem usługi ExpressRoute (zalecane) lub wirtualnej sieci prywatnej (VPN). Usługa Active Directory i DNS również zostały rozszerzone na platformę Azure.
- Uruchamia wystąpienie bazy danych SAP HANA w przypadku obciążeń oprogramowania HANA na platformie SAP HANA na platformie Azure (duże wystąpienia). Sygnatura duże wystąpienie jest podłączony do sieci platformy Azure, aby oprogramowanie uruchomione na maszynach wirtualnych mogą wchodzić w interakcje z wystąpieniem platformy HANA w dużych wystąpień HANA.
- Sprzęt platformy SAP Hana na platformie Azure (duże wystąpienia) jest dedykowany sprzęt w usłudze IaaS z systemem SUSE Linux Enterprise Server lub Red Hat Enterprise Linux, na których wstępnie zainstalowano. Podobnie jak w przypadku maszyn wirtualnych, dalsze aktualizacje i obsługa, aby system operacyjny jest odpowiedzialny za.
- Instalacja oprogramowania HANA lub wszelkie dodatkowe składniki niezbędne do uruchamiania oprogramowania SAP HANA na jednostkach po dużych wystąpień HANA jest odpowiedzialny za. Wszystkie odpowiednie trwających operacji i administracyjnej platformy SAP HANA na platformie Azure są również odpowiedzialny za.
- Oprócz rozwiązania opisane w tym miejscu można zainstalować inne składniki w Twojej subskrypcji platformy Azure, który nawiązuje połączenie z platformą SAP HANA na platformie Azure (duże wystąpienia). Przykłady to składniki umożliwiające komunikację z lub bezpośrednio do platformy SAP HANA serwery baz danych, takich jak serwery przeskoku protokołu RDP, SAP HANA Studio, usług danych SAP dla scenariuszy SAP BI rozwiązania do monitorowania sieci.
- Jak Azure duże wystąpienie oprogramowania HANA oferuje obsługę wysokiej dostępności i funkcje odzyskiwania po awarii.

**Następne kroki**
- Zapoznaj się [jednostki SKU dostępne dla HLI](hana-available-skus.md) 