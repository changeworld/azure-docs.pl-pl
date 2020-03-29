---
title: Administracja usługą Menedżera urządzeń StorSimple | Dokumenty firmy Microsoft
description: Dowiedz się, jak zarządzać urządzeniem StorSimple przy użyciu usługi StorSimple Device Manager w witrynie Azure portal.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: alkohli
ms.openlocfilehash: b5490c4e79ee1458b498f539c0db2cc189fce7f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60723310"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-device"></a>Administrowanie urządzeniem StorSimple za pomocą usługi StorSimple Device Manager

## <a name="overview"></a>Omówienie

W tym artykule opisano interfejs usługi StorSimple Device Manager, w tym sposób łączenia się z nim, różne opcje dostępne i łączy się z określonych przepływów pracy, które mogą być wykonywane za pośrednictwem tego interfejsu użytkownika. Niniejsze wytyczne mają zastosowanie do obu tych wskazówek; urządzenia fizycznego StorSimple i urządzenia w chmurze.

Po przeczytaniu tego artykułu nauczysz się:

* Łączenie się z usługą StorSimple Device Manager
* Administrowanie urządzeniem StorSimple za pośrednictwem usługi StorSimple Device Manager

## <a name="connect-to-storsimple-device-manager-service"></a>Łączenie się z usługą StorSimple Device Manager

Usługa StorSimple Device Manager działa na platformie Microsoft Azure i łączy się z wieloma urządzeniami StorSimple. Do zarządzania tymi urządzeniami można użyć centralnego portalu platformy Microsoft Azure działającego w przeglądarce. Aby połączyć się z usługą StorSimple Device Manager, wykonaj następujące czynności.

#### <a name="to-connect-to-the-service"></a>Aby połączyć się z usługą
1. Przejdź [https://portal.azure.com/](https://portal.azure.com/)do pliku .
2. Korzystając z poświadczeń konta Microsoft, zaloguj się do portalu Microsoft Azure (znajdującego się w prawym górnym rogu okienka).
3. Przewiń w dół lewego okienka nawigacji, aby uzyskać dostęp do usługi StorSimple Device Manager.


## <a name="administer-storsimple-device-using-storsimple-device-manager-service"></a>Administrowanie urządzeniem StorSimple przy użyciu usługi StorSimple Device Manager

W poniższej tabeli przedstawiono podsumowanie wszystkich typowych zadań zarządzania i złożonych przepływów pracy, które można wykonać w interfejsie użytkownika usługi Menedżer urządzeń StorSimple. Te zadania są zorganizowane na podstawie bloków interfejsu użytkownika, na których są inicjowane.

Aby uzyskać więcej informacji na temat każdego przepływu pracy, kliknij odpowiednią procedurę w tabeli.

#### <a name="storsimple-device-manager-workflows"></a>Przepływy pracy Menedżera urządzeń StorSimple

| Jeśli chcesz to zrobić ... | Użyj tej procedury. |
| --- | --- |
| Tworzenie usługi</br>Usuwanie usługi</br>Pobierz klucz rejestracji usługi</br>Ponowne generowanie klucza rejestracji usługi |[Wdrażanie usługi StorSimple Device Manager](storsimple-8000-manage-service.md) |
| Wyświetlanie dzienników aktywności |[Użyj podsumowania usługi Menedżer urządzeń StorSimple](storsimple-8000-service-dashboard.md) |
| Zmienianie klucza szyfrowania danych usługi</br>Wyświetlanie dzienników operacji |[Korzystanie z pulpitu nawigacyjnego usługi Menedżer urządzeń StorSimple](storsimple-8000-service-dashboard.md) |
| Dezaktywowanie urządzenia</br>Usuwanie urządzenia |[Dezaktywowanie lub usuwanie urządzenia](storsimple-8000-deactivate-and-delete-device.md) |
| Dowiedz się więcej o odzyskiwaniu po awarii i pracy awaryjnej urządzenia</br>Praca awaryjna na urządzeniu fizycznym</br>Przewijalenie awaryjne na urządzeniu wirtualnym</br>Odzyskiwanie po awarii ciągłości działania (BCDR) |[Tryb failover i odzyskiwanie po awarii dla urządzenia StorSimple](storsimple-8000-device-failover-disaster-recovery.md) |
| Wyświetlanie kopii zapasowych woluminu</br>Wybieranie zestawu kopii zapasowych</br>Usuwanie zestawu kopii zapasowych |[Zarządzanie kopiami zapasowymi](storsimple-8000-manage-backup-catalog.md) |
| Klonowanie woluminu |[Klonowanie woluminu](storsimple-8000-clone-volume-u2.md) |
| Przywracanie zestawu kopii zapasowych |[Przywracanie zestawu kopii zapasowych](storsimple-8000-restore-from-backup-set-u2.md) |
| Informacje o kontach magazynu</br>Dodawanie konta magazynu</br>Edytowanie konta magazynu</br>Usuwanie konta magazynu</br>Rotacja kluczy kont magazynu |[Zarządzanie kontami magazynu](storsimple-8000-manage-storage-accounts.md) |
| Szablony przepustowości — informacje</br>Dodawanie szablonu przepustowości</br>Edytowanie szablonu przepustowości</br>Usuwanie szablonu przepustowości</br>Używanie domyślnego szablonu przepustowości</br>Tworzenie caedytowego szablonu przepustowości, który rozpoczyna się o określonej godzinie |[Zarządzanie szablonami przepustowości](storsimple-8000-manage-bandwidth-templates.md) |
| Rekordy kontroli dostępu – informacje</br>Tworzenie rekordu kontroli dostępu</br>Edytowanie rekordu kontroli dostępu</br>Usuwanie rekordu kontroli dostępu |[Zarządzanie rekordami kontroli dostępu](storsimple-8000-manage-acrs.md) |
| Wyświetlanie szczegółów zadania</br>Anulowanie zadania |[Zarządzanie zadaniami](storsimple-8000-manage-jobs-u2.md) |
| Otrzymywanie powiadomień o alertach</br>Zarządzanie alertami</br>Przeglądanie alertów |[Wyświetlanie alertów StorSimple i zarządzanie nimi](storsimple-8000-manage-alerts.md) |
| Tworzenie wykresów monitorowania |[Monitorowanie urządzenia StorSimple](storsimple-monitor-device.md) |
| Dodawanie kontenera woluminów</br>Modyfikowanie kontenera woluminu</br>Usuwanie kontenera woluminu |[Zarządzanie kontenerami woluminów](storsimple-8000-manage-volume-containers.md) |
| Dodawanie woluminu</br>Modyfikowanie woluminu</br>Przejań wolumin do trybu offline</br>Usuwanie woluminu</br>Monitorowanie woluminu |[Zarządzanie woluminami](storsimple-8000-manage-volumes-u2.md) |
| Modyfikowanie ustawień urządzenia</br>Modyfikowanie ustawień czasu</br>Modyfikowanie ustawień DNS.md</br>Konfigurowanie interfejsów sieciowych |[Modyfikowanie konfiguracji urządzenia StorSimple](storsimple-8000-modify-device-config.md) |
| Wyświetlanie ustawień serwera proxy w sieci Web |[Konfigurowanie serwera proxy sieci Web dla urządzenia](storsimple-8000-configure-web-proxy.md) |
| Modyfikowanie hasła administratora urządzenia</br>Modyfikowanie hasła Menedżera migawek StorSimple |[Zmienianie haseł StorSimple](storsimple-8000-change-passwords.md) |
| Konfigurowanie zdalnego zarządzania |[Zdalne łączenie się z urządzeniem StorSimple](storsimple-8000-remote-connect.md) |
| Konfigurowanie ustawień alertów |[Wyświetlanie alertów StorSimple i zarządzanie nimi](storsimple-8000-manage-alerts.md) |
| Konfigurowanie protokołu CHAP dla urządzenia StorSimple |[Konfigurowanie protokołu CHAP dla urządzenia StorSimple](storsimple-configure-chap.md) |
| Dodawanie zasad kopii zapasowych</br>Dodawanie lub modyfikowanie harmonogramu</br>Usuwanie zasad tworzenia kopii zapasowych</br>Wykonywanie ręcznej kopii zapasowej</br>Tworzenie niestandardowych zasad tworzenia kopii zapasowych z wieloma woluminami i harmonogramami |[Zarządzanie zasadami tworzenia kopii zapasowych](storsimple-8000-manage-backup-policies-u2.md) |
| Kontrolery urządzeń zatrzymania</br>Ponowne uruchamianie kontrolerów urządzeń</br>Wyłącz kontrolery urządzeń</br>Resetowanie ustawień fabrycznych urządzenia</br>(Powyższe dotyczy tylko urządzeń lokalnych) |[Zarządzanie kontrolerem urządzeń StorSimple](storsimple-8000-manage-device-controller.md) |
| Dowiedz się więcej o składnikach sprzętowych StorSimple</br>Monitorowanie stanu sprzętu</br>(Powyższe dotyczy tylko urządzeń lokalnych) |[Monitorowanie składników sprzętowych](storsimple-8000-monitor-hardware-status.md) |
| Tworzenie pakietu pomocy technicznej |[Tworzenie pakietu pomocy technicznej i zarządzanie nim](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |
| Instalowanie aktualizacji oprogramowania |[Aktualizowanie urządzenia](storsimple-update-device.md) |

## <a name="next-steps"></a>Następne kroki

W przypadku jakichkolwiek problemów z codziennym działaniem urządzenia StorSimple lub któregokolwiek z jego składników sprzętowych, należy zapoznać się z:

* [Rozwiązywanie problemów przy użyciu narzędzia Diagnostyka](storsimple-8000-diagnostics.md)
* [Korzystanie z diod LED monitorowania StorSimple](storsimple-monitoring-indicators.md)

Jeśli nie można rozwiązać problemów i musisz utworzyć żądanie usługi, zapoznaj się [z kontaktem z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md).

