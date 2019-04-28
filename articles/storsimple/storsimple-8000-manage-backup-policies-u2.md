---
title: Zarządzanie zasadami tworzenia kopii zapasowych usługi StorSimple 8000 series | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak usługi Menedżer urządzeń StorSimple można użyć do tworzenia i zarządzania ręcznego tworzenia kopii zapasowych, harmonogramy tworzenia kopii zapasowych i przechowywania kopii zapasowych na urządzeniu z serii StorSimple 8000.
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
ms.openlocfilehash: 607379f8645226a031646376df9ca18f4d3164bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60818858"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-manage-backup-policies"></a>Usługa Menedżer urządzeń StorSimple w witrynie Azure portal umożliwia zarządzanie zasadami kopii zapasowych


## <a name="overview"></a>Omówienie

W tym samouczku wyjaśniono, jak korzystać z usługi Menedżer urządzeń StorSimple **zasady tworzenia kopii zapasowej** bloku do kontrolowania procesów tworzenia kopii zapasowej i przechowywanie kopii zapasowych woluminów StorSimple. Zawiera również opis jak ukończyć ręcznego tworzenia kopii zapasowej.

Gdy tworzysz kopię zapasową woluminu, można utworzyć migawki lokalnej lub migawkę w chmurze. W przypadku tworzenia kopii zapasowej woluminu przypiętego lokalnie, firma Microsoft zaleca, aby określić migawkę w chmurze. Biorąc dużą liczbę lokalnych migawek woluminu przypiętego lokalnie, w połączeniu z zestawu danych zawierającego wiele zmian spowoduje w sytuacji, w którym można szybko uruchomić mało miejsca na lokalnym. Jeśli użytkownik chce wykonać migawki lokalne, firma Microsoft zaleca migawek mniej codzienne tworzenie kopii zapasowej stan najnowszej zachować je przez jeden dzień i usuń je.

Jeśli przełączysz wolumin przypięty lokalnie migawkę w chmurze, kopiujesz tylko zmienionych danych do chmury, gdzie jest deduplikowany i skompresowany.

## <a name="the-backup-policy-blade"></a>Blok zasady tworzenia kopii zapasowej

**Zasady tworzenia kopii zapasowej** bloku dla urządzenia StorSimple umożliwia zarządzanie zasadami kopii zapasowych oraz planować lokalne i migawki w chmurze. Zasady tworzenia kopii zapasowych są używane do skonfigurować harmonogramy tworzenia kopii zapasowych i przechowywania kopii zapasowych dla kolekcji woluminów. Zasady tworzenia kopii zapasowych umożliwiają utworzenie migawki wiele woluminów jednocześnie. Oznacza to, że kopie zapasowe utworzone przy użyciu zasad tworzenia kopii zapasowej będzie spójna w razie awarii kopie.

Zasady tworzenia kopii zapasowych tabelarycznej liście umożliwia również filtrować istniejące zasady tworzenia kopii zapasowych co najmniej jeden z następujących pól:

* **Nazwa zasad** — nazwy skojarzonej z zasadami. Różne typy zasad:

  * Zaplanowane zasady, które są jawnie tworzone przez użytkownika.
  * Zasady importowane, które zostały oryginalnie utworzone Menedżera migawek StorSimple. Muszą one być znacznik, który opisuje hosta programu StorSimple Snapshot Manager, które zasady zostały zaimportowane z.

  > [!NOTE]
  > Automatyczne lub domyślne zasady tworzenia kopii zapasowych nie są już włączone w czasie tworzenia woluminu.

* **Ostatnia pomyślna kopia zapasowa** — Data i godzina ostatniej pomyślnej kopii zapasowej, która została wykonana z tymi zasadami.

* **Następną kopią zapasową** — Data i godzina następnej zaplanowanej kopii zapasowej, który zostanie zainicjowany przy użyciu tych zasad.

* **Woluminy** — woluminów skojarzonych z zasadami. Podczas tworzenia kopii zapasowych, wszystkie woluminy, które są skojarzone z zasadami kopii zapasowych są grupowane razem.

* **Harmonogramy** — liczba harmonogramów skojarzonych z zasadami tworzenia kopii zapasowej.

Są często używane operacje, które można wykonać dla zasad tworzenia kopii zapasowych:

* Dodawanie zasad kopii zapasowych
* Dodaj lub zmodyfikuj harmonogram
* Dodawanie lub usuwanie woluminu
* Usuwanie zasad kopii zapasowych
* Utwórz kopię zapasową ręczne

## <a name="add-a-backup-policy"></a>Dodawanie zasad kopii zapasowych

Dodawanie zasad kopii zapasowych, aby zaplanować automatyczne kopie zapasowe. Podczas tworzenia woluminu nie ma żadnych domyślnymi zasadami kopii zapasowych skojarzone z woluminu. Należy dodać i przypisać zasady kopii zapasowych, aby chronić dane w woluminie.

Wykonaj następujące czynności w witrynie Azure portal, aby dodać zasady kopii zapasowych dla urządzenia StorSimple. Po dodaniu zasad, można zdefiniować harmonogram (zobacz [Dodaj lub zmodyfikuj harmonogram](#add-or-modify-a-schedule)).

[!INCLUDE [storsimple-8000-add-backup-policy-u2](../../includes/storsimple-8000-add-backup-policy-u2.md)]

## <a name="add-or-modify-a-schedule"></a>Dodaj lub zmodyfikuj harmonogram

Można dodać lub zmodyfikować harmonogram, który jest dołączony do istniejących zasad tworzenia kopii zapasowej na urządzeniu StorSimple. Wykonaj następujące czynności w witrynie Azure portal, aby dodać lub zmodyfikować harmonogram.

[!INCLUDE [storsimple-8000-add-modify-backup-schedule](../../includes/storsimple-8000-add-modify-backup-schedule-u2.md)]


## <a name="add-or-remove-a-volume"></a>Dodawanie lub usuwanie woluminu

Można dodać lub usunąć wolumin przypisane do zasad kopii zapasowych na urządzeniu StorSimple. Wykonaj następujące czynności w witrynie Azure portal, aby dodać lub usunąć wolumin.

[!INCLUDE [storsimple-8000-add-volume-backup-policy-u2](../../includes/storsimple-8000-add-remove-volume-backup-policy-u2.md)]


## <a name="delete-a-backup-policy"></a>Usuwanie zasad kopii zapasowych

Wykonaj następujące czynności w witrynie Azure portal, aby usunąć zasady kopii zapasowych na urządzeniu StorSimple.

[!INCLUDE [storsimple-8000-delete-backup-policy](../../includes/storsimple-8000-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>Utwórz kopię zapasową ręczne

Wykonaj następujące czynności w witrynie Azure portal, aby utworzyć kopię zapasową pojedynczego woluminu na żądanie (ręcznie).

[!INCLUDE [storsimple-8000-create-manual-backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [przy użyciu usługi Menedżer urządzeń StorSimple do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

