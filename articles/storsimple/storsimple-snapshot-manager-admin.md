---
title: Administracja programu StorSimple Snapshot Manager | Dokumentacja firmy Microsoft
description: Zawiera omówienie oraz linki do szczegółowych informacji na temat zadań administracyjnych rozwiązania StorSimple Snapshot Manager i przepływów pracy.
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
ms.openlocfilehash: bc72da98800ef85ef14be0882ba856fbf01386b9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60630023"
---
# <a name="use-storsimple-snapshot-manager-to-administer-your-storsimple-solution"></a>Użyj przystawki StorSimple Snapshot Manager do administrowania rozwiązania StorSimple

## <a name="overview"></a>Omówienie
Przystawki StorSimple Snapshot Manager jest przystawką Microsoft Management Console (MMC), która ułatwia ochronę danych i zarządzania kopiami zapasowymi w środowisku Microsoft Azure StorSimple. StorSimple Snapshot Manager możesz zarządzać danych Microsoft Azure StorSimple w centrum danych i w chmurze jako pojedynczy zintegrowane rozwiązanie do magazynowania, tym samym upraszczanie procesów tworzenia kopii zapasowych i obniżając koszty.

Przystawki StorSimple Snapshot Manager centralną konsolę zarządzania pozwala na tworzenie kopii zapasowych spójne, w momencie lokalnych i danych w chmurze. Na przykład można użyć konsoli:

* Konfigurowanie, tworzenie kopii zapasowej i Usuń woluminy.
* Konfigurowanie grup woluminu, aby upewnić się, którego kopię zapasową danych jest spójnych z aplikacją.
* Zarządzanie zasadami kopii zapasowych, dzięki czemu jest wykonywana kopia zapasowa danych w uprzednio określonym harmonogramem.
* Utwórz niezależnych, pobranych kopii danych, który może być przechowywana w chmurze i używanej na potrzeby odzyskiwania po awarii.

Ten artykuł zawiera linki do samouczków, które opisują programu StorSimple Snapshot Manager i jak z niej korzystać, aby zakończyć zadań administracyjnych i przepływów pracy.

* Aby uzyskać więcej informacji na temat programu StorSimple Snapshot Manager składników i architektury, zobacz [co to jest StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) 
* Aby pobrać przystawki StorSimple Snapshot Manager, przejdź do [stronę pobierania programu StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).
* Aby uzyskać procedury wdrażania programu StorSimple Snapshot Manager, przejdź do [wdrażanie programu StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

> [!NOTE]
> Nie można użyć przystawki StorSimple Snapshot Manager do zarządzania Microsoft Azure macierze wirtualne StorSimple (znany także jako StorSimple w środowisku lokalnym urządzeń wirtualnych).


## <a name="storsimple-snapshot-manager-tasks-and-workflows"></a>Zadania programu StorSimple Snapshot Manager i przepływów pracy
Menedżer migawek StorSimple umożliwia monitorowanie i zarządzanie nimi aktualne, zaplanowane i zakończonych zadań tworzenia kopii zapasowej. Ponadto programu StorSimple Snapshot Manager udostępnia katalog maksymalnie 64 ukończone kopii zapasowych. Wykazu służy do znajdowania i przywracania woluminów lub poszczególne pliki. 

| JEŚLI CHCESZ TO ZROBIĆ... | UŻYJ W TYM SAMOUCZKU... |
|:--- |:--- |
| Dowiedz się więcej na temat programu StorSimple Snapshot Manager |[Co to jest StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) |
| Instalowanie programu StorSimple Snapshot Manager<br>Ponowne zainstalowanie programu StorSimple Snapshot Manager<br>Usuwanie programu StorSimple Snapshot Manager |[Wdrażanie programu StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md) |
| Użyj przystawki StorSimple Snapshot Manager menu i funkcje:<ul><li>Pasek menu</li><li>Pasek narzędzi</li><li>Okienko zakresu</li><li>Okienko wyników</li><li>Okienko akcji</li><li>Nawigowanie przy użyciu klawiatury i skróty</li></ul> |[Interfejs użytkownika programu StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md) |
| Użyj funkcji wspólnych MMC programu StorSimple Snapshot Manager:<ul><li>Widok</li><li>Nowe okno, w tym miejscu</li><li>Odśwież</li><li>Eksportuj listę</li><li>Help</li></ul> |[Używanie akcji menu programu MMC programu StorSimple Snapshot Manager](storsimple-snapshot-manager-mmc-menu.md) |
| Dodawanie lub zastępowanie urządzenia<br>Podłącz urządzenie<br>Sprawdź grupy importowane woluminu<br>Odśwież połączone urządzenia<br>Uwierzytelnienia urządzenia<br>Wyświetlanie szczegółów urządzenia<br>Usuwanie konfiguracji urządzenia<br>Zmień hasło urządzenia<br>Zamień urządzenia nie powiodło się<br> |[Aby nawiązać połączenie i zarządzanie urządzeniami StorSimple za pomocą Menedżera migawek StorSimple](storsimple-snapshot-manager-manage-devices.md) |
| Woluminy instalacji<br>Wyświetlanie informacji o woluminach<br>Usuwanie woluminu<br>Skanuj ponownie woluminów<br>Konfigurowanie i tworzenie kopii zapasowej woluminu podstawowego<br>Konfigurowanie i utworzyć kopię zapasową woluminu dublowanego dynamiczne |[Do wyświetlania i zarządzanie woluminami za pomocą Menedżera migawek StorSimple](storsimple-snapshot-manager-manage-volumes.md) |
| Wyświetlanie grup woluminu<br>Utwórz grupę woluminu<br>Tworzenie kopii zapasowej grupy woluminów<br>Edytuj grupę woluminu<br>Usuwanie grupy woluminów |[Tworzenie i zarządzanie grupami woluminów za pomocą Menedżera migawek StorSimple](storsimple-snapshot-manager-manage-volume-groups.md) |
| Tworzenie zasad kopii zapasowych <br>Edytowanie zasad tworzenia kopii zapasowej<br>Usuwanie zasad kopii zapasowych |[Tworzenie i zarządzanie zasadami kopii zapasowych za pomocą Menedżera migawek StorSimple](storsimple-snapshot-manager-manage-backup-policies.md) |
| Wyświetlanie i zarządzanie nimi zaplanowanych zadań kopii zapasowej<br>Wyświetlanie i zarządzanie nimi ostatnich zadań tworzenia kopii zapasowej<br>Wyświetlanie i zarządzanie nimi uruchomionych obecnie zadań tworzenia kopii zapasowej |[Do przeglądania i zarządzania zadania tworzenia kopii zapasowej za pomocą Menedżera migawek StorSimple](storsimple-snapshot-manager-manage-backup-jobs.md) |
| Przywracanie woluminu<br>Klonowanie woluminu lub wolumin grupy<br>Usuwanie kopii zapasowej<br>Odzyskiwanie pliku<br>Przywracanie bazy danych programu StorSimple Snapshot Manager |[Użyj StorSimple Snapshot Manager Zarządzanie wykazem kopii zapasowych](storsimple-snapshot-manager-manage-backup-catalog.md) |

## <a name="next-steps"></a>Kolejne kroki
[Pobieranie programu StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).

