---
title: Rozmiary sap HANA na platformie Azure (duże wystąpienia) | Dokumenty firmy Microsoft
description: Rozmiary sap HANA na platformie Azure (duże wystąpienia).
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
ms.openlocfilehash: 404f8318816edcc2cfd1c50ca42304ff6ec93039
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616911"
---
# <a name="sizing"></a>Ustalanie rozmiaru

Rozmiar dla hana duże wystąpienie nie różni się od rozmiaru dla HANA w ogóle. W przypadku istniejących i wdrożonych systemów, które chcesz przenieść z innych systemów RDBMS do HANA, sap udostępnia szereg raportów, które są uruchamiane w istniejących systemach SAP. Jeśli baza danych zostanie przeniesiona do hana, raporty te sprawdzić dane i obliczyć wymagania dotyczące pamięci dla wystąpienia HANA. Aby uzyskać więcej informacji na temat uruchamiania tych raportów i uzyskiwania ich najnowszych poprawek lub wersji, przeczytaj następujące uwagi SAP:

- [SAP Note #1793345 - Rozmiar dla pakietu SAP Suite w HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP Note #1872170 - Suite on HANA i S/4 HANA raport wielkości](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP Note #2121330 - CZĘSTO ZADAWANE PYTANIA: SAP BW w raporcie o wielkości HANA](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP Note #1736976 - Raport rozmiaru dla BW na HANA](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP Note #2296290 - Nowy raport rozmiaru dla BW na HANA](https://launchpad.support.sap.com/#/notes/2296290)

W przypadku implementacji zielonego pola sap Quick Sizer jest dostępny do obliczania wymagań dotyczących pamięci implementacji oprogramowania SAP na stronie HANA.

Wymagania dotyczące pamięci dla hana zwiększyć wraz ze wzrostem ilości danych. Należy pamiętać o bieżącym zużyciu pamięci, aby pomóc przewidzieć, co to będzie w przyszłości. Na podstawie wymagań dotyczących pamięci można następnie mapować zapotrzebowanie na jeden z jednostek SKU dużych wystąpień HANA.

**Następne kroki**
- Zapoznaj się [z wymaganiami dotyczącymi dołączania](hana-onboarding-requirements.md)