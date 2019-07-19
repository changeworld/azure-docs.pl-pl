---
title: Uaktualnienie systemu operacyjnego dla SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Wykonaj uaktualnienie systemu operacyjnego SAP HANA na platformie Azure (duże wystąpienia)
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
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aa88e45f2523dd65c4f714bfeab1c0eda401d720
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869150"
---
# <a name="operating-system-upgrade"></a>Uaktualnienie systemu operacyjnego
W tym dokumencie opisano szczegółowe informacje dotyczące uaktualnień systemu operacyjnego w dużych wystąpieniach platformy HANA.

>[!NOTE]
>Uaktualnienie systemu operacyjnego jest odpowiedzialne za klientów, pomoc techniczna firmy Microsoft może poprowadzić Cię do kluczowych obszarów, które należy obejrzeć podczas uaktualniania. Przed zaplanowaniem uaktualnienia należy skonsultować się z dostawcą systemu operacyjnego.

W tym momencie Inicjowanie obsługi administracyjnej jednostki pakietu i programu Microsoft Operations Manager instaluje system operacyjny. W danym czasie wymagane jest zachowanie systemu operacyjnego (przykład: Stosowanie poprawek, dostrajanie, uaktualnianie itp.) w jednostce.

Przed wprowadzeniem istotnych zmian w systemie operacyjnym (na przykład uaktualnienia programu SP1 do wersji SP2) należy skontaktować się z zespołem ds. operacyjnych, otwierając bilet pomocy technicznej w celu konsultacji.

Dołącz do biletu:

* Identyfikator subskrypcji pakietu.
* Nazwa serwera.
* Poziom poprawki, który ma zostać zastosowany.
* Data, w której planowana jest ta zmiana. 

Zalecamy otworzenie tego biletu z co najmniej jednego tygodnia przed pożądanym terminem uaktualnienia, z powodu sprawdzania zespołu operacji, jeśli w bloku serwera będzie wymagane uaktualnienie oprogramowania układowego.


Aby uzyskać macierz pomocy technicznej dla różnych wersji SAP HANA z różnymi wersjami systemu Linux, zobacz [uwagi dotyczące oprogramowania SAP #2235581](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Znane problemy

Poniżej przedstawiono kilka typowych znanych problemów podczas uaktualniania:
- W jednostkach SKU klasy SKU typu II, oprogramowanie oprogramowania Software Foundation (SFS) zostanie usunięte po uaktualnieniu systemu operacyjnego. Po uaktualnieniu systemu operacyjnego należy ponownie zainstalować zgodną SFS.
- Sterowniki kart Ethernet (ENIC i FNIC) zostały przywrócone do starszej wersji. Po uaktualnieniu należy ponownie zainstalować zgodną wersję sterowników.

## <a name="next-steps"></a>Następne kroki
- Odwołaj się do [kopii zapasowych i przywracania](hana-overview-high-availability-disaster-recovery.md) dla klasy SKU typu kopia zapasowa systemu operacyjnego.
- Zapoznaj się [z kopią zapasową systemu operacyjnego dla jednostek SKU typu II poprawki 3 sygnatury](os-backup-type-ii-skus.md) dla klasy SKU typu II.
