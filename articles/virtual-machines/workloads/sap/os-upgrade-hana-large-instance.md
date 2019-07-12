---
title: Uaktualnienie systemu operacyjnego dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Uaktualnienie systemu operacyjnego dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5583a633c64943185f874e1c0ff80f654010aa53
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710013"
---
# <a name="operating-system-upgrade"></a>Uaktualnienie systemu operacyjnego
W tym dokumencie opisano szczegóły dotyczące uaktualnień systemu operacyjnego w dużych wystąpieniach HANA.

>[!NOTE]
>Uaktualnienie systemu operacyjnego jest odpowiedzialny za klientów, pomocy technicznej firmy Microsoft operacje może doprowadzić do kluczowe obszary, które należy uważać podczas uaktualniania. Przed rozpoczęciem planowania dla uaktualnienie, powinni skontaktować się z dostawcą systemu operacyjnego.

W momencie aprowizacja jednostek HLI zespół operacyjny firmy Microsoft, zainstalować system operacyjny. Wraz z upływem czasu, są wymagane do obsługi systemu operacyjnego (przykład: Stosowanie poprawek, dostosowywania, uaktualnianie itp.) w jednostce HLI.

Przed głównych zmian do systemu operacyjnego (na przykład uaktualnienie dodatku SP1, SP2), należy się z zespołem Microsoft Operations, otwierając bilet pomocy technicznej zapoznaj się.

Uwzględnij biletu:

* HLI identyfikatora subskrypcji.
* Nazwa serwera.
* Poziom poprawki, które będą dotyczyć.
* Data jest planowana tę zmianę. 

Zalecamy spowoduje otwarcie tego biletu co najmniej tydzień przed datą uaktualnienia pożądane z powodu konieczności zespół operacyjny sprawdzania, czy uaktualnienie oprogramowania układowego będzie konieczne w bloku serwera.


Macierz obsługi różnych wersji platformy SAP HANA z użyciem różnych wersji systemu Linux, można zobaczyć [2235581 # Uwaga SAP](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Znane problemy

Poniżej przedstawiono kilka typowych znane problemy podczas uaktualniania:
- Jednostka SKU typu II klasy jednostek SKU software foundation (SFS) zostanie usunięty po uaktualnieniu systemu operacyjnego. Po uaktualnieniu systemu operacyjnego, należy ponownie zainstalować SFS zgodny.
- Sterowniki karty Ethernet (ENIC i FNIC) z powrotem obniżyć do starszej wersji. Po uaktualnieniu należy ponownie zainstalować zgodną wersję sterowników.

## <a name="next-steps"></a>Następne kroki
- Zapoznaj się [kopii zapasowej i przywracania](hana-overview-high-availability-disaster-recovery.md) dla systemu operacyjnego należy utworzyć kopię zapasową klasy typu I jednostki SKU.
- Zapoznaj się [kopia zapasowa systemu operacyjnego dla jednostek SKU typu II](os-backup-type-ii-skus.md) dla jednostki SKU II typu klasy.
