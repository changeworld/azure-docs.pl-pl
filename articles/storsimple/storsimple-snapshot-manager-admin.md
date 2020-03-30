---
title: Administracja Menedżera migawek StorSimple | Dokumenty firmy Microsoft
description: Zawiera omówienie i łącza do więcej informacji na temat zadań administracyjnych i przepływów pracy programu StorSimple Snapshot Manager.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 1cdbb61d-bd16-4be4-ade2-ceab11508acb
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2016
ms.author: twooley
ms.openlocfilehash: b8fe87a2df633af310bc661308813a60987e77d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933279"
---
# <a name="use-storsimple-snapshot-manager-to-administer-your-storsimple-solution"></a>Do administrowania rozwiązaniem StorSimple za pomocą Menedżera migawek StorSimple

## <a name="overview"></a>Omówienie
StorSimple Snapshot Manager to przystawka programu Microsoft Management Console (MMC), która upraszcza ochronę danych i zarządzanie kopiami zapasowymi w środowisku Microsoft Azure StorSimple. Dzięki StorSimple Snapshot Manager możesz zarządzać danymi usługi Microsoft Azure StorSimple w centrum danych i w chmurze jako pojedyncze zintegrowane rozwiązanie pamięci masowej, upraszczając w ten sposób procesy tworzenia kopii zapasowych i obniżając koszty.

Centralna konsola zarządzania StorSimple Snapshot Manager umożliwia tworzenie spójnych kopii zapasowych danych lokalnych i w chmurze w czasie. Na przykład można użyć konsoli do:

* Konfigurowanie woluminów, ich utworzenie kopii zapasowej i usuwanie.
* Skonfiguruj grupy woluminów, aby upewnić się, że kopie zapasowe danych są spójne z aplikacjami.
* Zarządzanie zasadami tworzenia kopii zapasowych, tak aby kopia zapasowa danych była archiwiza w określonym harmonogramie.
* Tworzenie niezależnych kopii danych, które mogą być przechowywane w chmurze i używane do odzyskiwania po awarii.

Ten artykuł zawiera łącza do samouczków opisujących StorSimple Snapshot Manager i jak go używać do wykonywania zadań administracyjnych systemu i przepływów pracy.

* Aby uzyskać więcej informacji na temat składników i architektury Menedżera migawek StorSimple, zobacz [Co to jest Menedżer migawek StorSimple?](storsimple-what-is-snapshot-manager.md) 
* Aby pobrać StorSimple Snapshot Manager, przejdź do [StorSimple Snapshot Manager strony pobierania](https://www.microsoft.com/download/details.aspx?id=44220).
* W przypadku procedur wdrażania programu StorSimple Snapshot Manager przejdź do [programu Deploy StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

> [!NOTE]
> Nie można używać Menedżera migawek StorSimple do zarządzania macierzami wirtualnymi usługi Microsoft Azure StorSimple (nazywanymi również lokalnymi urządzeniami wirtualnymi StorSimple).


## <a name="storsimple-snapshot-manager-tasks-and-workflows"></a>StorSimple Migawka — zadania i przepływy pracy
Menedżer migawek StorSimple służy do monitorowania bieżących, zaplanowanych i ukończonych zadań tworzenia kopii zapasowych oraz zarządzania nimi. Ponadto StorSimple Snapshot Manager udostępnia katalog do 64 ukończonych kopii zapasowych. Za pomocą katalogu można znaleźć i przywrócić woluminy lub pojedyncze pliki. 

| JEŚLI CHCESZ TO ZROBIĆ... | UŻYJ TEGO SAMOUCZKA... |
|:--- |:--- |
| Dowiedz się więcej o Menedżerze migawek StorSimple |[Co to jest StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) |
| Zainstaluj Menedżera migawek StorSimple<br>Ponowne instalowanie Menedżera migawek StorSimple<br>Usuń Menedżera migawek StorSimple |[Wdrażanie Menedżera migawek StorSimple](storsimple-snapshot-manager-deployment.md) |
| Użyj menu i funkcji Menedżera migawek StorSimple:<ul><li>Pasek menu</li><li>Pasek narzędzi</li><li>Okienko zakresu</li><li>Okienko Wyniki</li><li>Okienko Akcje</li><li>Nawigacja za pomocą klawiatury i skróty</li></ul> |[Interfejs użytkownika Menedżera migawek StorSimple](storsimple-use-snapshot-manager.md) |
| Użyj typowych funkcji programu MMC zawartych w Menedżerze migawek StorSimple:<ul><li>Widok</li><li>Nowe okno w tym miejscu</li><li>Odświeżanie</li><li>Lista eksportu</li><li>Pomoc</li></ul> |[Używanie akcji menu programu MMC w Menedżerze migawek StorSimple](storsimple-snapshot-manager-mmc-menu.md) |
| Dodawanie lub zamienianie urządzenia<br>Łączenie urządzenia<br>Weryfikowanie importowanych grup woluminów<br>Odświeżanie podłączonych urządzeń<br>Uwierzytelnianie urządzenia<br>Wyświetlanie szczegółów urządzenia<br>Usuwanie konfiguracji urządzenia<br>Zmienianie hasła urządzenia<br>Zastępowanie nieudanego urządzenia<br> |[Użyj Menedżera migawek StorSimple do łączenia urządzeń StorSimple i zarządzania nimi](storsimple-snapshot-manager-manage-devices.md) |
| Mocowanie woluminów<br>Wyświetlanie informacji o woluminach<br>Usuwanie woluminu<br>Ponowne przeskanowywki<br>Konfigurowanie i konfigurowanie kopii zapasowej woluminu podstawowego<br>Konfigurowanie i tworzenie kopii zapasowych woluminu z wiercem lustrzanym dynamicznym |[Wyświetlanie woluminów i zarządzanie nimi za pomocą Menedżera migawek StorSimple](storsimple-snapshot-manager-manage-volumes.md) |
| Wyświetlanie grup woluminów<br>Tworzenie grupy woluminów<br>3.<br>Edytowanie grupy woluminów<br>Usuwanie grupy woluminów |[Tworzenie grup woluminów i zarządzanie nimi za pomocą Menedżera migawek StorSimple](storsimple-snapshot-manager-manage-volume-groups.md) |
| Tworzenie zasad kopii zapasowych <br>Edytowanie zasad tworzenia kopii zapasowych<br>Usuwanie zasad tworzenia kopii zapasowych |[Tworzenie zasad tworzenia kopii zapasowych i zarządzanie nimi za pomocą Menedżera migawek StorSimple](storsimple-snapshot-manager-manage-backup-policies.md) |
| Wyświetlanie zaplanowanych zadań tworzenia kopii zapasowych i zarządzanie nimi<br>Wyświetlanie ostatnich zadań tworzenia kopii zapasowych i zarządzanie nimi<br>Wyświetlanie aktualnie uruchomionych zadań tworzenia kopii zapasowych i zarządzanie nimi |[Użyj Menedżera migawek StorSimple, aby wyświetlać zadania tworzenia kopii zapasowych i zarządzać nimi](storsimple-snapshot-manager-manage-backup-jobs.md) |
| Przywracanie woluminu<br>Klonowanie grupy woluminów lub woluminów<br>Usuwanie kopii zapasowej<br>Odzyskiwanie pliku<br>Przywracanie bazy danych Menedżera migawek StorSimple |[Zarządzanie katalogiem kopii zapasowych za pomocą Menedżera migawek StorSimple](storsimple-snapshot-manager-manage-backup-catalog.md) |

## <a name="next-steps"></a>Następne kroki
[Pobierz StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).

