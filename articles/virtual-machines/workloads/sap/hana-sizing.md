---
title: Ustalanie rozmiaru środowiska SAP Hana na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Ustalanie rozmiaru środowiska SAP Hana na platformie Azure (duże wystąpienia).
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
ms.openlocfilehash: 7d403c284eefc1351aabb632977832f2bf11757a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60477126"
---
# <a name="sizing"></a>Ustalanie rozmiaru

Ustalanie rozmiaru dla dużych wystąpień HANA nie różni się od ogólnie rzecz biorąc ustalania rozmiaru dla platformy HANA. Dla istniejących i wdrażane systemy, które chcesz przenieść z innych RDBMS do platformy HANA, SAP udostępnia szereg raportów, które działają w istniejących systemów SAP. Jeśli baza danych zostanie przeniesiony do platformy HANA, te raporty, sprawdź dane i obliczyć wymagania dotyczące pamięci dla wystąpienia platformy HANA. Aby uzyskać więcej informacji na temat sposobu uruchamiania tych raportów i uzyskać ich najnowsze poprawki lub wersji należy przeczytać następujące uwagi SAP:

- [Uwaga SAP #1793345 - rozmiaru pakietu SAP na platformie HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP Uwaga 1872170 # - Suite on HANA i S/4 HANA raportu zmiany rozmiaru](https://launchpad.support.sap.com/#/notes/1872170)
- [Uwaga SAP #2121330 — często zadawane pytania: System SAP BW on HANA, zmiany rozmiaru raportu](https://launchpad.support.sap.com/#/notes/2121330)
- [Uwaga #1736976 - raport ustalania rozmiaru dla programu BW on HANA, SAP](https://launchpad.support.sap.com/#/notes/1736976)
- [Uwaga 2296290 # - nowe zmiany rozmiaru raportu dla BW on HANA, SAP](https://launchpad.support.sap.com/#/notes/2296290)

Implementacje zielone pole SAP szybkiego Rozmiar symboli jest dostępna do obliczania wymagania dotyczące pamięci wdrażania oprogramowania SAP HANA — na.

Wymagania dotyczące pamięci dla platformy HANA zwiększyć wzrostem ilości danych. Należy pamiętać o bieżące zużycie pamięci, aby ułatwić przewidywanie, co będzie znajdował się w przyszłości. Oparte na wymagania dotyczące pamięci, następnie można mapować zapotrzebowanie na jeden z jednostki SKU HANA dużych wystąpień.

**Następne kroki**
- Zapoznaj się [wymagań przechodzenia](hana-onboarding-requirements.md)