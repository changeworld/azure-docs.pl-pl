---
title: Zarządzanie zasadami tworzenia kopii zapasowej serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono, jak można użyć usługi Menedżer StorSimple urządzenia do tworzenia i zarządzania ręcznego tworzenia kopii zapasowych, harmonogramy tworzenia kopii zapasowej i przechowywania kopii zapasowych na urządzeniu z serii StorSimple 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/05/2017
ms.author: alkohli
ms.openlocfilehash: 569dbfdeb7dcd526cb5a54b487ea1bfb59b13cc6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874871"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-manage-backup-policies"></a>Użyj usługi Menedżer StorSimple urządzenia w portalu Azure do zarządzania zasadami tworzenia kopii zapasowej


## <a name="overview"></a>Omówienie

W tym samouczku wyjaśniono, jak używać usługi Menedżer StorSimple urządzenia **kopii zapasowej zasad** bloku do kontroli procesów tworzenia kopii zapasowej i przechowywania kopii zapasowych dla woluminów StorSimple. Zawiera również opis jak zakończyć ręczne kopii zapasowej.

Podczas wykonywania kopii zapasowej woluminu, można utworzyć migawki lokalnej lub migawka w chmurze. W przypadku wykonywania kopii zapasowej woluminu przypiętego lokalnie, zaleca się określenie migawka w chmurze. Biorąc dużą liczbę lokalnych migawek woluminu przypiętego lokalnie, w połączeniu z zestawu danych, który ma wiele zmian spowoduje w sytuacji, w którym można szybko uruchomić poza lokalne miejsce. Jeśli zdecydujesz się na lokalnych migawek, zaleca się zająć mniej migawki codziennych kopii zapasowych ostatniego stanu, należy zachować je na dzień, a następnie usunąć je.

Podczas wykonywania migawki woluminu przypiętego lokalnie chmury kopiujesz tylko zmienione dane w chmurze, gdzie jest deduplikowany i skompresowane.

## <a name="the-backup-policy-blade"></a>Blok zasady tworzenia kopii zapasowej

**Kopii zapasowej zasad** bloku dla urządzenia StorSimple umożliwia zarządzanie zasadami tworzenia kopii zapasowej i zaplanować lokalne i migawki w chmurze. Zasady tworzenia kopii zapasowych umożliwiają skonfigurowanie harmonogramy tworzenia kopii zapasowej i przechowywania kopii zapasowych dla kolekcji woluminów. Zasady tworzenia kopii zapasowych umożliwiają utworzenie migawki wiele woluminów jednocześnie. Oznacza to, że kopie zapasowe utworzone przez zasady tworzenia kopii zapasowej będzie spójna w razie awarii kopie.

Tabelarycznej listę zasad tworzenia kopii zapasowych można też filtrować istniejących zasad tworzenia kopii zapasowych przez jedną lub więcej z następujących pól:

* **Nazwa zasad** — Nazwa skojarzona z zasadami. Różne typy zasad:

  * Zaplanowane zasady, które są jawnie utworzone przez użytkownika.
  * Zasady importowane, które zostały pierwotnie utworzone w programie StorSimple Snapshot Manager. Mają one znacznik hosta StorSimple Snapshot Manager, które zasady zostały zaimportowane z.

  > [!NOTE]
  > Automatyczne lub domyślne zasady tworzenia kopii zapasowej nie są już włączone w czasie tworzenia woluminu.

* **Ostatnia kopia zapasowa pomyślnie** — Data i godzina ostatniej pomyślnej kopii zapasowej, która została wykonana z tymi zasadami.

* **Następną kopią zapasową** — Data i godzina następnej zaplanowanej kopii zapasowej będą inicjowane przez te zasady.

* **Woluminy** — woluminy skojarzonych z zasadami. Wszystkie woluminy, które są skojarzone z zasadami tworzenia kopii zapasowej są grupowane razem, gdy kopie zapasowe są tworzone.

* **Harmonogramy** — liczba harmonogramów skojarzonych z zasadami tworzenia kopii zapasowej.

Są często używane operacje, które można wykonywać w odniesieniu do zasady tworzenia kopii zapasowej:

* Dodawanie zasad kopii zapasowych
* Dodaj lub zmodyfikuj harmonogram
* Dodawanie lub usuwanie woluminu
* Usuń zasady tworzenia kopii zapasowej
* Wykonaj kopię zapasową ręczne

## <a name="add-a-backup-policy"></a>Dodawanie zasad kopii zapasowych

Dodawanie zasad tworzenia kopii zapasowej, można zaplanować automatyczne kopie zapasowe. Najpierw należy utworzyć wolumin, nie ma żadnych zasad tworzenia kopii zapasowej domyślne skojarzone z woluminu. Należy dodać i przypisać zasady tworzenia kopii zapasowej do ochrony danych woluminu.

Wykonaj poniższe kroki w portalu Azure, aby dodać zasady tworzenia kopii zapasowej dla urządzenia StorSimple. Po dodaniu zasad można określić harmonogramu (zobacz [Dodaj lub zmodyfikuj harmonogram](#add-or-modify-a-schedule)).

[!INCLUDE [storsimple-8000-add-backup-policy-u2](../../includes/storsimple-8000-add-backup-policy-u2.md)]

## <a name="add-or-modify-a-schedule"></a>Dodaj lub zmodyfikuj harmonogram

Można dodać lub zmodyfikować harmonogram, który jest dołączony do istniejących zasad tworzenia kopii zapasowych w urządzeniu StorSimple. Wykonaj poniższe kroki w portalu Azure, aby dodać lub zmodyfikować harmonogram.

[!INCLUDE [storsimple-8000-add-modify-backup-schedule](../../includes/storsimple-8000-add-modify-backup-schedule-u2.md)]


## <a name="add-or-remove-a-volume"></a>Dodawanie lub usuwanie woluminu

Można dodawać i usuwać przypisane do zasad tworzenia kopii zapasowej na urządzeniu StorSimple woluminu. Wykonaj poniższe kroki w portalu Azure, aby dodać lub usunąć wolumin.

[!INCLUDE [storsimple-8000-add-volume-backup-policy-u2](../../includes/storsimple-8000-add-remove-volume-backup-policy-u2.md)]


## <a name="delete-a-backup-policy"></a>Usuń zasady tworzenia kopii zapasowej

Wykonaj poniższe kroki w portalu Azure, aby usunąć zasady tworzenia kopii zapasowych w urządzeniu StorSimple.

[!INCLUDE [storsimple-8000-delete-backup-policy](../../includes/storsimple-8000-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>Wykonaj kopię zapasową ręczne

Wykonaj poniższe kroki w portalu Azure, aby utworzyć kopię zapasową pojedynczego woluminu na żądanie (ręcznie).

[!INCLUDE [storsimple-8000-create-manual-backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [przy użyciu usługi Menedżer StorSimple urządzenia do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

