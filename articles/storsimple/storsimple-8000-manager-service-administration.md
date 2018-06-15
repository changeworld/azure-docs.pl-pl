---
title: Administracja usługi Menedżera urządzenia StorSimple | Dokumentacja firmy Microsoft
description: Informacje o sposobie zarządzania urządzeniem StorSimple przy użyciu usługi Menedżer StorSimple urządzenia w portalu Azure.
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
ms.openlocfilehash: 0e7d7f44a70278a7777ba6c32c8e546074953fdc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875039"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-device"></a>Użyj usługi Menedżer StorSimple urządzenia do administrowania urządzenia StorSimple

## <a name="overview"></a>Omówienie

W tym artykule opisano interfejs usługi Menedżera urządzeń StorSimple, w tym sposobu łączenia go różne opcje dostępne i łączy się do określonych przepływów pracy, które mogą być wykonywane za pośrednictwem tego interfejsu użytkownika. W tych wskazówkach ma zastosowanie do obu; urządzenia fizycznego StorSimple oraz urządzenia chmury.

Po przeczytaniu tego artykułu, dowiesz się:

* Połączyć się z usługą Menedżera urządzeń StorSimple
* Administrowanie urządzenia StorSimple za pośrednictwem usługi Menedżer StorSimple urządzenia

## <a name="connect-to-storsimple-device-manager-service"></a>Połączyć się z usługą Menedżera urządzeń StorSimple

Usługa Menedżera urządzeń StorSimple działa na platformie Microsoft Azure i łączy się z wieloma urządzeniami StorSimple. Centralnej portalu Microsoft Azure działającego w przeglądarce umożliwia zarządzanie tymi urządzeniami. Aby połączyć się z usługą Menedżera urządzeń StorSimple, wykonaj następujące czynności.

#### <a name="to-connect-to-the-service"></a>Aby połączyć się z usługą
1. Przejdź do [https://portal.azure.com/](https://portal.azure.com/).
2. Przy użyciu poświadczeń konta Microsoft, zaloguj się do portalu Microsoft Azure (znajdujące się w prawym górnym rogu okienka).
3. Przewiń w dół w okienku nawigacji po lewej stronie do uzyskania dostępu do usługi Menedżer StorSimple urządzenia.


## <a name="administer-storsimple-device-using-storsimple-device-manager-service"></a>Administrowanie urządzenia StorSimple przy użyciu usługi Menedżer StorSimple urządzenia

W poniższej tabeli przedstawiono podsumowanie typowych zadań zarządzania i złożonych przepływów pracy, które mogą być wykonywane w ramach usługi Menedżer StorSimple urządzenia interfejsu użytkownika. Te zadania są podzielone na bloki interfejsu użytkownika, na których są inicjowane podstawie.

Aby uzyskać więcej informacji na temat każdego przepływu pracy kliknij odpowiednią procedurę w tabeli.

#### <a name="storsimple-device-manager-workflows"></a>Menedżer urządzeń StorSimple przepływy pracy

| Jeśli chcesz to zrobić... | Użyj tej procedury. |
| --- | --- |
| Tworzenie usługi</br>Usuwanie usługi</br>Pobierz klucz rejestracji usługi</br>Wygeneruj ponownie klucz rejestracji usługi |[Wdrażanie usługi Menedżer StorSimple urządzenia](storsimple-8000-manage-service.md) |
| Wyświetl dzienniki aktywności |[Użyj Menedżera urządzeń StorSimple Podsumowanie usługi](storsimple-8000-service-dashboard.md) |
| Zmiana klucza szyfrowania danych usługi</br>Sprawdź dzienniki operacji |[Pulpit nawigacyjny usługi Menedżer StorSimple urządzenia](storsimple-8000-service-dashboard.md) |
| Dezaktywować urządzenie</br>Usuwanie urządzenia |[Dezaktywuj lub usuń urządzenie](storsimple-8000-deactivate-and-delete-device.md) |
| Dowiedz się więcej o pracy awaryjnej odzyskiwania i urządzenia po awarii</br>Tryb failover urządzenia fizycznego</br>Urządzenie wirtualne w tryb failover</br>Odzyskiwanie po awarii ciągłości biznesowej (BCDR) |[Tryb failover i odzyskiwania po awarii dla urządzenia StorSimple](storsimple-8000-device-failover-disaster-recovery.md) |
| Kopie zapasowe woluminu z listy</br>Wybierz zestaw kopii zapasowych</br>Usuń zestaw kopii zapasowych |[Zarządzanie kopiami zapasowymi](storsimple-8000-manage-backup-catalog.md) |
| Klonowanie woluminu |[Klonowanie woluminu](storsimple-8000-clone-volume-u2.md) |
| Przywróć zestaw kopii zapasowych |[Przywróć zestaw kopii zapasowych](storsimple-8000-restore-from-backup-set-u2.md) |
| Dotyczących kont magazynu</br>Dodawanie konta magazynu</br>Edytuj konto magazynu</br>Usuwanie konta magazynu</br>Rotacją kluczy kont magazynu |[Zarządzanie kontami magazynu](storsimple-8000-manage-storage-accounts.md) |
| Informacje o szablonach przepustowości</br>Dodaj szablon przepustowości</br>Edytuj szablon przepustowości</br>Usuń szablon przepustowości</br>Użyj domyślnego szablonu przepustowości</br>Tworzenie szablonu przepustowości całodzienne, która rozpoczyna się od określonego czasu |[Zarządzanie szablonami przepustowości](storsimple-8000-manage-bandwidth-templates.md) |
| Informacje o rekordach kontroli dostępu</br>Utwórz rekord kontroli dostępu</br>Edytuj rekord kontroli dostępu</br>Usuń rekord kontroli dostępu |[Zarządzanie rekordami kontroli dostępu](storsimple-8000-manage-acrs.md) |
| Wyświetlanie szczegółów zadania</br>Anulowanie zadania |[Zarządzanie zadaniami](storsimple-8000-manage-jobs-u2.md) |
| Otrzymywanie powiadomień o alertach</br>Zarządzanie alertami</br>Alerty dotyczące weryfikacji |[Wyświetl alerty i zarządzaj nimi StorSimple](storsimple-8000-manage-alerts.md) |
| Tworzenie wykresów monitorowania |[Monitorowanie urządzenia StorSimple](storsimple-monitor-device.md) |
| Dodaj kontener woluminów</br>Modyfikowanie kontenera woluminów</br>Usunięcie kontenera woluminów |[Zarządzanie kontenerami woluminów](storsimple-8000-manage-volume-containers.md) |
| Dodawanie woluminu</br>Modyfikowanie woluminu</br>Przełącz do trybu offline woluminu</br>Usuwanie woluminu</br>Monitor woluminu |[Zarządzanie woluminami](storsimple-8000-manage-volumes-u2.md) |
| Zmodyfikuj ustawienia urządzenia</br>Zmodyfikuj ustawienia czasu</br>Zmodyfikuj ustawienia DNS.md</br>Skonfiguruj interfejsy sieciowe |[Modyfikowanie konfiguracji urządzenia dla urządzenia StorSimple](storsimple-8000-modify-device-config.md) |
| Wyświetl ustawienia serwera proxy sieci web |[Skonfiguruj serwer proxy sieci web dla urządzenia](storsimple-8000-configure-web-proxy.md) |
| Zmodyfikuj hasło administratora urządzenia</br>Zmodyfikuj hasło programu StorSimple Snapshot Manager |[Zmienianie haseł usługi StorSimple](storsimple-8000-change-passwords.md) |
| Konfigurowanie zdalnego zarządzania |[Zdalne nawiązywanie połączenia urządzenia StorSimple](storsimple-8000-remote-connect.md) |
| Konfigurowanie ustawień alertów |[Wyświetl alerty i zarządzaj nimi StorSimple](storsimple-8000-manage-alerts.md) |
| Konfigurowanie protokołu CHAP dla urządzenia StorSimple |[Konfigurowanie protokołu CHAP dla urządzenia StorSimple](storsimple-configure-chap.md) |
| Dodawanie zasad kopii zapasowych</br>Dodaj lub zmodyfikuj harmonogram</br>Usuń zasady tworzenia kopii zapasowej</br>Wykonaj kopię zapasową ręczne</br>Tworzenie niestandardowych zasad tworzenia kopii zapasowej z wieloma woluminami i harmonogramów |[Zarządzanie zasadami tworzenia kopii zapasowych](storsimple-8000-manage-backup-policies-u2.md) |
| Zatrzymaj kontrolery urządzeń</br>Uruchom ponownie kontrolery urządzeń</br>Zamknij kontrolery urządzeń</br>Resetowanie urządzenia do ustawień fabrycznych</br>(Powyżej są do lokalnego urządzenia) |[Zarządzanie kontrolera urządzenia StorSimple](storsimple-8000-manage-device-controller.md) |
| Dowiedz się więcej o składniki sprzętowe StorSimple</br>Monitor stanu sprzętu</br>(Powyżej są do lokalnego urządzenia) |[Monitor składników sprzętowych](storsimple-8000-monitor-hardware-status.md) |
| Utwórz pakiet pomocy technicznej |[Tworzenie i zarządzanie nimi pakietu obsługi](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |
| Instalacja aktualizacji oprogramowania |[Aktualizowanie urządzenia](storsimple-update-device.md) |

## <a name="next-steps"></a>Następne kroki

Jeśli występują problemy z codziennych operacji urządzenia StorSimple lub za pomocą dowolnego z jego składniki sprzętowe, zobacz:

* [Rozwiązywanie problemów przy użyciu narzędzia diagnostycznego](storsimple-8000-diagnostics.md)
* [Użyj StorSimple monitorowania wskaźników](storsimple-monitoring-indicators.md)

Jeśli nie można rozwiązać problemy, należy utworzyć żądania obsługi dotyczą [skontaktuj się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md).

