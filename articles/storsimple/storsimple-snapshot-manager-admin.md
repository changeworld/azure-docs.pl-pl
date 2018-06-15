---
title: Administracja programu StorSimple Snapshot Manager | Dokumentacja firmy Microsoft
description: Zawiera omówienie i linki do informacji na temat zadań administracyjnych w rozwiązaniu StorSimple Snapshot Manager i przepływów pracy.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 1cdbb61d-bd16-4be4-ade2-ceab11508acb
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2016
ms.author: v-sharos
ms.openlocfilehash: a99b3d7336c3dc1a1f249915d6971a49f4b69be6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875431"
---
# <a name="use-storsimple-snapshot-manager-to-administer-your-storsimple-solution"></a>Użyj StorSimple Snapshot Manager do administrowania rozwiązania StorSimple

## <a name="overview"></a>Omówienie
StorSimple Snapshot Manager jest przystawką Microsoft Management Console (MMC), która ułatwia ochronę danych i zarządzania kopiami zapasowymi w środowisku Microsoft Azure StorSimple. StorSimple Snapshot Manager można zarządzać danych Microsoft Azure StorSimple w centrum danych i w chmurze jako pojedynczy magazyn zintegrowanego rozwiązania, w związku z tym uproszczeniu procesów tworzenia kopii zapasowej i zmniejszenie kosztów.

Konsolę zarządzania centralnej StorSimple Snapshot Manager umożliwia tworzenie spójny, w momencie kopii zapasowych lokalnych i w chmurze danych. Na przykład można użyć konsoli:

* Konfigurowanie, tworzenie kopii zapasowej i usuwanie woluminów.
* Konfigurowanie grup woluminu, aby upewnić się, którego kopię zapasową danych jest spójne z aplikacjami.
* Zarządzanie zasad tworzenia kopii zapasowych, dzięki czemu jest wykonywana kopia zapasowa danych na uprzednio określonym harmonogramem.
* Tworzenie niezależnych kopii danych, który może być przechowywane w chmurze i używanych na potrzeby odzyskiwania po awarii.

Ten artykuł zawiera łącza do samouczków, w których opisano StorSimple Snapshot Manager i jak z niego korzystać do wykonania zadań administracyjnych i przepływów pracy.

* Aby uzyskać więcej informacji o składnikach programu StorSimple Snapshot Manager i architektury, zobacz [co to jest StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) 
* Aby pobrać StorSimple Snapshot Manager, przejdź do [strony pobierania programu StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).
* Procedury wdrażania StorSimple Snapshot Manager, przejdź do [wdrażanie StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

> [!NOTE]
> Nie można użyć programu StorSimple Snapshot Manager do zarządzania Microsoft Azure StorSimple wirtualnego tablic (znanej także jako StorSimple lokalnego urządzenia wirtualnego).


## <a name="storsimple-snapshot-manager-tasks-and-workflows"></a>Zadania programu StorSimple Snapshot Manager i przepływów pracy
StorSimple Snapshot Manager umożliwia monitorowanie i zarządzanie nimi bieżących, zaplanowane i zakończonych zadań tworzenia kopii zapasowej. Ponadto StorSimple Snapshot Manager udostępnia katalog maksymalnie 64 kopie zapasowe ukończone. Katalogu służy do znajdowania i Przywracanie woluminów lub pojedynczych plików. 

| JEŚLI CHCESZ TO ZROBIĆ... | UŻYJ TEGO SAMOUCZKA... |
|:--- |:--- |
| Dowiedz się więcej o StorSimple Snapshot Manager |[Co to jest StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) |
| Instalowanie programu StorSimple Snapshot Manager<br>Ponowne zainstalowanie programu StorSimple Snapshot Manager<br>Usuń StorSimple Snapshot Manager |[Wdrażanie programu StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md) |
| Użyj StorSimple Snapshot Manager menu i funkcje:<ul><li>Pasek menu</li><li>Pasek narzędzi</li><li>Okienko zakresu</li><li>W okienku wyników</li><li>W okienku Akcje</li><li>Skróty i nawigacji klawiatury</li></ul> |[Interfejs użytkownika programu StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md) |
| Użyj typowych funkcji MMC zawartych w StorSimple Snapshot Manager:<ul><li>Widok</li><li>Nowe okno w tym miejscu</li><li>Odśwież</li><li>Wyeksportuj listę</li><li>Pomoc</li></ul> |[Używanie akcji menu MMC programu StorSimple Snapshot Manager](storsimple-snapshot-manager-mmc-menu.md) |
| Dodaj lub Zastąp urządzenia<br>Podłącz urządzenie<br>Sprawdź importowany woluminu grup<br>Odśwież połączonych urządzeń<br>Uwierzytelniania urządzenia<br>Wyświetl szczegóły urządzenia<br>Usuwanie konfiguracji urządzenia<br>Zmień hasło urządzenia<br>Zastąp urządzenia nie powiodło się<br> |[Użyj programu StorSimple Snapshot Manager do łączenia i zarządzanie urządzeniami StorSimple](storsimple-snapshot-manager-manage-devices.md) |
| Zainstalować woluminów<br>Wyświetlanie informacji o woluminach<br>Usuwanie woluminu<br>Skanuj woluminów<br>Konfigurowanie i tworzenie kopii zapasowej woluminu podstawowego<br>Konfigurowanie i kopii zapasowej woluminu dublowanego dynamiczne |[StorSimple Snapshot Manager umożliwia wyświetlanie i zarządzanie woluminami](storsimple-snapshot-manager-manage-volumes.md) |
| Wyświetlanie grup woluminu<br>Utwórz grupę woluminu<br>Tworzenie kopii zapasowej grupy woluminu<br>Edytowanie grupy woluminu<br>Usuwanie grupy woluminu |[StorSimple Snapshot Manager umożliwia tworzenie i zarządzanie grupami woluminu](storsimple-snapshot-manager-manage-volume-groups.md) |
| Tworzenie zasad tworzenia kopii zapasowej <br>Edytowanie zasad tworzenia kopii zapasowej<br>Usuń zasady tworzenia kopii zapasowej |[StorSimple Snapshot Manager umożliwia tworzenie i zarządzanie zasadami tworzenia kopii zapasowej](storsimple-snapshot-manager-manage-backup-policies.md) |
| Wyświetl i zarządzaj nimi zaplanowanych zadań kopii zapasowej<br>Wyświetl i zarządzaj nimi ostatnich zadań tworzenia kopii zapasowej<br>Wyświetl i zarządzaj nimi uruchomionych obecnie zadań tworzenia kopii zapasowej |[StorSimple Snapshot Manager umożliwia wyświetlanie i zarządzanie nimi zadania tworzenia kopii zapasowej](storsimple-snapshot-manager-manage-backup-jobs.md) |
| Przywracanie woluminu<br>Klonowanie woluminu lub grupy woluminu<br>Usuwanie kopii zapasowej<br>Odzyskiwanie pliku<br>Przywróć bazę danych programu StorSimple Snapshot Manager |[Użyj StorSimple Snapshot Manager do zarządzania katalogu kopii zapasowej](storsimple-snapshot-manager-manage-backup-catalog.md) |

## <a name="next-steps"></a>Następne kroki
[Pobieranie programu StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).

