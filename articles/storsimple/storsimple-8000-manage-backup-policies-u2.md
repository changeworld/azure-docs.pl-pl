---
title: Zarządzanie zasadami tworzenia kopii zapasowych serii StorSimple 8000 | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak za pomocą usługi StorSimple Device Manager można tworzyć ręczne kopie zapasowe, harmonogramy tworzenia kopii zapasowych i przechowywanie kopii zapasowych na urządzeniu z serii StorSimple 8000 i zarządzać nimi.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267796"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-manage-backup-policies"></a>Zarządzanie zasadami tworzenia kopii zapasowych za pomocą usługi StorSimple Device Manager w witrynie Azure portal


## <a name="overview"></a>Omówienie

W tym samouczku wyjaśniono, jak używać bloku **zasad zasad** usługi StorSimple Device Manager do kontrolowania procesów tworzenia kopii zapasowych i przechowywania kopii zapasowych woluminów StorSimple. Opisano w nim również sposób wykonywania ręcznej kopii zapasowej.

Podczas tworzenia kopii zapasowej woluminu można utworzyć migawkę lokalną lub migawkę w chmurze. Jeśli wykonywanie kopii zapasowej woluminu przypiętego lokalnie zaleca się określenie migawki w chmurze. Biorąc dużą liczbę migawek lokalnych woluminu przypięty lokalnie w połączeniu z zestawem danych, który ma dużo zmian spowoduje sytuację, w której można szybko zabraknąć miejsca lokalnego. Jeśli zdecydujesz się na wykonanie migawek lokalnych, zaleca się wykonanie mniejszej liczby codziennych migawek w celu utworzenia kopii zapasowej najnowszego stanu, zachowanie ich przez jeden dzień, a następnie ich usunięcie.

Podczas robienia migawki chmury woluminu przypiętego lokalnie, można skopiować tylko zmienione dane do chmury, gdzie jest deduplikowane i kompresowane.

## <a name="the-backup-policy-blade"></a>Blok zasad tworzenia kopii zapasowych

Blok **zasad kopii zapasowej** dla urządzenia StorSimple umożliwia zarządzanie zasadami tworzenia kopii zapasowych i planowanie migawek lokalnych i w chmurze. Zasady tworzenia kopii zapasowych służą do konfigurowania harmonogramów tworzenia kopii zapasowych i przechowywania kopii zapasowych dla kolekcji woluminów. Zasady tworzenia kopii zapasowych umożliwiają wykonywanie migawek wielu woluminów jednocześnie. Oznacza to, że kopie zapasowe utworzone przez zasady tworzenia kopii zapasowych będą kopiami spójnymi z awariami.

Tabelaryczne zasady tworzenia kopii zapasowych umożliwiają również filtrowanie istniejących zasad tworzenia kopii zapasowych według co najmniej jednego z następujących pól:

* **Nazwa zasad** — nazwa skojarzona z zasadami. Różne typy zasad obejmują:

  * Zaplanowane zasady, które są jawnie tworzone przez użytkownika.
  * Zaimportowane zasady, które zostały pierwotnie utworzone w Menedżerze migawek StorSimple. Mają one tag, który opisuje Host Menedżera migawek StorSimple, z których zostały zaimportowane zasady.

  > [!NOTE]
  > Automatyczne lub domyślne zasady tworzenia kopii zapasowych nie są już włączone w momencie tworzenia woluminu.

* **Ostatnia pomyślna kopia zapasowa** — data i godzina ostatniej pomyślnej kopii zapasowej, która została podjęta z tą zasadą.

* **Następna kopia zapasowa** — data i godzina następnej zaplanowanej kopii zapasowej, która zostanie zainicjowana przez tę zasadę.

* **Woluminy** — woluminy skojarzone z zasadami. Wszystkie woluminy skojarzone z zasadami tworzenia kopii zapasowych są zgrupowane razem podczas tworzenia kopii zapasowych.

* **Harmonogramy** — liczba harmonogramów skojarzonych z zasadami tworzenia kopii zapasowych.

Często używane operacje, które można wykonać dla zasad tworzenia kopii zapasowych są:

* Dodawanie zasad kopii zapasowych
* Dodawanie lub modyfikowanie harmonogramu
* Dodawanie lub usuwanie woluminu
* Usuwanie zasad tworzenia kopii zapasowych
* Wykonywanie ręcznej kopii zapasowej

## <a name="add-a-backup-policy"></a>Dodawanie zasad kopii zapasowych

Dodaj zasady tworzenia kopii zapasowych, aby automatycznie zaplanować tworzenie kopii zapasowych. Podczas pierwszego tworzenia woluminu nie ma żadnych domyślnych zasad tworzenia kopii zapasowych skojarzonych z woluminem. Należy dodać i przypisać zasady tworzenia kopii zapasowych, aby chronić dane woluminu.

Wykonaj następujące kroki w witrynie Azure portal, aby dodać zasady tworzenia kopii zapasowych dla urządzenia StorSimple. Po dodaniu zasad można zdefiniować harmonogram (zobacz [Dodawanie lub modyfikowanie harmonogramu](#add-or-modify-a-schedule)).

[!INCLUDE [storsimple-8000-add-backup-policy-u2](../../includes/storsimple-8000-add-backup-policy-u2.md)]

## <a name="add-or-modify-a-schedule"></a>Dodawanie lub modyfikowanie harmonogramu

Można dodać lub zmodyfikować harmonogram dołączony do istniejących zasad tworzenia kopii zapasowych na urządzeniu StorSimple. Wykonaj następujące kroki w witrynie Azure portal, aby dodać lub zmodyfikować harmonogram.

[!INCLUDE [storsimple-8000-add-modify-backup-schedule](../../includes/storsimple-8000-add-modify-backup-schedule-u2.md)]


## <a name="add-or-remove-a-volume"></a>Dodawanie lub usuwanie woluminu

Wolumin przypisany do zasad kopii zapasowej na urządzeniu StorSimple można dodać lub usunąć. Wykonaj następujące kroki w witrynie Azure portal, aby dodać lub usunąć wolumin.

[!INCLUDE [storsimple-8000-add-volume-backup-policy-u2](../../includes/storsimple-8000-add-remove-volume-backup-policy-u2.md)]


## <a name="delete-a-backup-policy"></a>Usuwanie zasad tworzenia kopii zapasowych

Wykonaj następujące kroki w witrynie Azure portal, aby usunąć zasady tworzenia kopii zapasowych na urządzeniu StorSimple.

[!INCLUDE [storsimple-8000-delete-backup-policy](../../includes/storsimple-8000-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>Wykonywanie ręcznej kopii zapasowej

Wykonaj następujące kroki w witrynie Azure portal, aby utworzyć kopię zapasową na żądanie (ręczną) dla pojedynczego woluminu.

[!INCLUDE [storsimple-8000-create-manual-backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [administrowaniu urządzeniem StorSimple za pomocą usługi StorSimple Device Manager.](storsimple-8000-manager-service-administration.md)

