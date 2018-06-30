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
ms.date: 06/28/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cff9be3b074dde4a0335675663133a8df81ae62d
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114596"
---
# <a name="operating-system-upgrade"></a>Uaktualnienie systemu operacyjnego
Ten dokument zawiera szczegółowe informacje na uaktualnienia systemu operacyjnego w wystąpieniach dużych HANA.

>[!NOTE]
>Uaktualnienie systemu operacyjnego jest odpowiedzialny za klientów, pomocy technicznej firmy Microsoft operations może doprowadzić do Najważniejsze kwestie, które Uważaj podczas uaktualniania. Należy skonsultować się z dostawcą systemu operacyjnego przed planowania uaktualnienia.

W czasie inicjowania obsługi administracyjnej jednostki HLI zespół operacyjny Microsoft zainstalowania systemu operacyjnego. Wraz z upływem czasu, są wymagane do obsługi systemu operacyjnego (przykład: stosowanie poprawek, dostosowywanie, uaktualnianie itp.) w jednostce HLI.

Przed główne zmiany systemu operacyjnego (na przykład uaktualnić SP1 do wersji z dodatkiem SP2), należy się z zespołem Microsoft Operations przez otwarcie biletu pomocy technicznej skontaktować się.


W macierzy pomocy technicznej w różnych wersjach SAP HANA z różnymi wersjami systemu Linux, zobacz [SAP Uwaga #2235581](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Znane problemy

Poniżej przedstawiono kilka typowych znane problemy występujące podczas uaktualniania:
- W klasie jednostki SKU typu II jednostka SKU oprogramowanie foundation (SFS) zostaną usunięte po uaktualnieniu systemu operacyjnego. Po uaktualnieniu systemu operacyjnego, należy ponownie zainstalować SFS zgodne.
- Sterowniki karty Ethernet (ENIC i FNIC) z powrotem obniżyć do starszej wersji. Po uaktualnieniu, należy ponownie zainstalować zgodnej wersji sterowników.

## <a name="next-steps"></a>Kolejne kroki
- Zobacz [kopii zapasowej i przywracania](hana-overview-high-availability-disaster-recovery.md) dla systemu operacyjnego należy utworzyć kopię zapasową klasy typu I wersji.
- Zobacz [kopii zapasowej systemu operacyjnego dla jednostki SKU II typu](os-backup-type-ii-skus.md) dla jednostki SKU II typu klasy.