---
title: Uaktualnienie systemu operacyjnego dla SAP HANA na platformie Azure (wystąpienia duże) | Dokumentacja firmy Microsoft
description: Uaktualnienie systemu operacyjnego dla SAP HANA na platformie Azure (wystąpienia duże)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f6af1a6612360c2433c05a7add79d2e7b3b9d754
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658264"
---
# <a name="operating-system-upgrade"></a>Uaktualnienie systemu operacyjnego
Ten dokument zawiera szczegółowe informacje na uaktualnienia systemu operacyjnego w wystąpieniach dużych HANA.

>[!NOTE]
>Uaktualnienie systemu operacyjnego jest odpowiedzialny za klientów, pomocy technicznej firmy Microsoft operations może doprowadzić do Najważniejsze kwestie, które Uważaj podczas uaktualniania. Należy skonsultować się z dostawcą systemu operacyjnego przed planowania uaktualnienia.

W czasie inicjowania obsługi administracyjnej jednostki HLI zespół operacyjny Microsoft zainstalowania systemu operacyjnego. Wraz z upływem czasu, są wymagane do obsługi systemu operacyjnego (przykład: stosowanie poprawek, dostosowywanie, uaktualnianie itp.) w jednostce HLI.

Przed główne zmiany systemu operacyjnego (na przykład uaktualnienie systemu operacyjnego), możesz **musi** należy wziąć pod uwagę następujące macierz zgodności. Możesz **musi** również skontaktuj się z zespołem Microsoft Operations przez otwarcie biletu pomocy technicznej zapoznać się przed rozpoczęciem działań główne systemu operacyjnego, takich jak uaktualnianie.

W macierzy pomocy technicznej w różnych wersjach SAP HANA z różnymi wersjami systemu Linux, zobacz [SAP Uwaga #2235581](https://launchpad.support.sap.com/#/notes/2235581).

Następujące zgodności ma został przetestowany pod kątem HLIs. Jeśli serwer HLI znajduje się poza macierz zgodności, skontaktuj się z obsługą operacji firmy Microsoft.

## <a name="for-type-i-class-sku-category"></a>Dla typu klasy I kategorii jednostki SKU

| Konfigurowanie | SUSE12 Z DODATKIEM SP1 | SUSE12 Z DODATKIEM SP2 | RHEL 7.2 | RHEL 7.3|
| --- | --- | --- | --- | --- |
| Oprogramowanie układowe serwera | 3.1(2b) | 3.1(2b) | 3.1(2b) | 3.1(2b) |
| Wersja ENIC | 2.3.0.44 | 2.3.0.44 | 2.3.0.30 | 2.3.0.44 |
| Wersja FNIC | 1.6.0.34 | 1.6.0.34 | 1.6.0.27 | 1.6.0.36 |
| EDAC | Disabled (Wyłączony) | Disabled (Wyłączony) | Disabled (Wyłączony) | Disabled (Wyłączony) |
| Wersja jądra | 4.4.21-69-default | 3.12.49-11-default | 3.10.0-327.el7.X86_64 | 3.10.0-693.17.1 |


## <a name="for-type-ii-class-sku-category"></a>Dla typu II klasy jednostki SKU kategorii

| Konfigurowanie | SUSE12 Z DODATKIEM SP1 | SUSE12 Z DODATKIEM SP2 | RHEL 7.2 | RHEL 7.3|
| --- | --- | --- | --- | --- |
| Wersja oprogramowania układowego RMC | 1.1.121  | 1.1.121  | 1.1.121  | 1.1.121 |
| Wersja oprogramowania układowego BMC | 1.0.43   | 1.0.43   | 1.0.43   | 1.0.43  |
| Wersja serwera (SFS) Foundation oprogramowania | 2,16    | 2,16    | 2.14/2.16   | 2,16   |
| BIOS | 5.2.6    | 5.2.6    | 5.2.6    | 5.2.6   |
| Wersja i40e    | 2.0.19     | 2.0.19     | 1.5.10-k    | 1.5.10-k   |
| Wersja jądra    | 3.12.49-11.1     | 4.4.21-69.1     | 3.10.0-327    | 3.10.0-693.17.1   |


## <a name="known-issues"></a>Znane problemy

Poniżej przedstawiono kilka typowych znane problemy występujące podczas uaktualniania:
- W klasie jednostki SKU typu II jednostka SKU oprogramowanie foundation (SFS) zostaną usunięte po uaktualnieniu systemu operacyjnego. Po uaktualnieniu systemu operacyjnego, należy ponownie zainstalować SFS zgodne.
- Sterowniki karty Ethernet (ENIC i FNIC) z powrotem obniżyć do starszej wersji. Po uaktualnieniu, należy ponownie zainstalować zgodnej wersji sterowników.

## <a name="next-steps"></a>Kolejne kroki
- Zobacz [kopii zapasowej i przywracania](hana-overview-high-availability-disaster-recovery.md) dla systemu operacyjnego należy utworzyć kopię zapasową klasy typu I wersji.
- Zobacz [kopii zapasowej systemu operacyjnego dla jednostki SKU II typu](os-backup-type-ii-skus.md) dla jednostki SKU II typu klasy.