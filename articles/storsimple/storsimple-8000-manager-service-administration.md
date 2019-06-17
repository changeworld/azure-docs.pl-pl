---
title: Administracja usługi Menedżer urządzeń StorSimple | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać urządzeniem StorSimple za pomocą usługi Menedżer urządzeń StorSimple w witrynie Azure portal.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60723310"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-device"></a>Usługa Menedżer urządzeń StorSimple do administrowania urządzeniem StorSimple

## <a name="overview"></a>Omówienie

W tym artykule opisano interfejsu usługi Menedżer urządzeń StorSimple, w tym jak połączyć się z jej różnych dostępnych opcji i linki do określonych przepływów pracy, które mogą być wykonywane za pośrednictwem tego interfejsu użytkownika. Niniejsze wskazówki ma zastosowanie do obu; urządzenie fizyczne StorSimple oraz urządzenia w chmurze.

Po przeczytaniu tego artykułu, dowiesz się:

* Nawiązać połączenia z usługą Menedżera urządzeń StorSimple
* Administrowanie urządzeniem StorSimple za pomocą usługi Menedżer urządzeń StorSimple

## <a name="connect-to-storsimple-device-manager-service"></a>Nawiązać połączenia z usługą Menedżera urządzeń StorSimple

Usługa Menedżer urządzeń StorSimple działa na platformie Microsoft Azure i nawiązanie połączenia z wieloma urządzeniami StorSimple. Używasz centralnej portalu Microsoft Azure, korzystania z przeglądarki do zarządzania tymi urządzeniami. Aby połączyć z usługą Menedżera urządzeń StorSimple, wykonaj następujące czynności.

#### <a name="to-connect-to-the-service"></a>Aby połączyć się z usługą
1. Przejdź do strony [https://portal.azure.com/](https://portal.azure.com/).
2. Przy użyciu poświadczeń konta Microsoft, zaloguj się do portalu Microsoft Azure (znajdujące się w prawym górnym rogu okienka).
3. Przewiń w dół w okienku nawigacji po lewej stronie, aby uzyskać dostęp do usługi Menedżer urządzeń StorSimple.


## <a name="administer-storsimple-device-using-storsimple-device-manager-service"></a>Administrowanie urządzeniem StorSimple przy użyciu usługi Menedżer urządzeń StorSimple

W poniższej tabeli przedstawiono podsumowanie typowych zadań zarządzania i złożonych przepływów pracy, które mogą być wykonywane w ramach interfejsu użytkownika usługi Menedżer urządzeń StorSimple. Te zadania są zorganizowane w oparciu o bloki interfejsu użytkownika, na których są inicjowane.

Aby uzyskać więcej informacji na temat każdego przepływu pracy kliknij odpowiednią procedurę w tabeli.

#### <a name="storsimple-device-manager-workflows"></a>Menedżer urządzeń StorSimple przepływów pracy

| Jeśli chcesz to zrobić... | Użyj tej procedury. |
| --- | --- |
| Tworzenie usługi</br>Usuwanie usługi</br>Pobierz klucz rejestracji usługi</br>Wygeneruj ponownie klucz rejestracji usługi |[Wdrażanie usługi Menedżer urządzeń StorSimple](storsimple-8000-manage-service.md) |
| Wyświetlanie dzienników aktywności |[Użyj Podsumowanie usługi Menedżer urządzeń StorSimple](storsimple-8000-service-dashboard.md) |
| Zmień klucz szyfrowania danych usługi</br>Wyświetlanie dzienników operacji |[Pulpit nawigacyjny usługi Menedżer urządzeń StorSimple](storsimple-8000-service-dashboard.md) |
| Dezaktywacja urządzenia</br>Usuwanie urządzenia |[Dezaktywowanie i usuwanie urządzenia](storsimple-8000-deactivate-and-delete-device.md) |
| Więcej informacji na temat trybu failover odzyskiwania i urządzenia po awarii</br>Tryb failover na urządzeniu fizycznym</br>Tryb failover do urządzenia wirtualnego</br>Odzyskiwanie po awarii ciągłość działania firmy (BCDR) |[Tryb failover i odzyskiwanie po awarii dla urządzenia StorSimple](storsimple-8000-device-failover-disaster-recovery.md) |
| Lista kopii zapasowych danych woluminu</br>Wybierz zestaw kopii zapasowych</br>Usuwanie zestawu kopii zapasowych |[Zarządzanie kopiami zapasowymi](storsimple-8000-manage-backup-catalog.md) |
| Klonowanie woluminu |[Klonowanie woluminu](storsimple-8000-clone-volume-u2.md) |
| Przywróć zestaw kopii zapasowych |[Przywróć zestaw kopii zapasowych](storsimple-8000-restore-from-backup-set-u2.md) |
| Dotyczących kont magazynu</br>Dodawanie konta magazynu</br>Edytuj konto magazynu</br>Usuwanie konta magazynu</br>Wymiana kluczy kont magazynu |[Zarządzanie kontami magazynu](storsimple-8000-manage-storage-accounts.md) |
| Informacje o szablonach przepustowości</br>Dodawanie szablonu przepustowości</br>Edytuj szablon przepustowości</br>Usuwanie szablonu przepustowości</br>Użyj domyślnego szablonu przepustowości</br>Utwórz całodzienne szablonu przepustowości, która rozpoczyna się o określonej godzinie |[Zarządzanie szablonami przepustowości](storsimple-8000-manage-bandwidth-templates.md) |
| Informacje o rekordach kontroli dostępu</br>Utwórz rekord kontroli dostępu</br>Edytowanie rekordu kontroli dostępu</br>Usuwanie rekordu kontroli dostępu |[Zarządzanie rekordy kontroli dostępu](storsimple-8000-manage-acrs.md) |
| Wyświetlanie szczegółów zadania</br>Anulowanie zadania |[Zarządzanie zadaniami](storsimple-8000-manage-jobs-u2.md) |
| Otrzymywanie powiadomień o alertach</br>Zarządzanie alertami</br>Przeglądanie alertów |[Wyświetlanie i Zarządzanie alertami usługi StorSimple](storsimple-8000-manage-alerts.md) |
| Tworzenie wykresów monitorowania |[Monitoruj urządzenia StorSimple](storsimple-monitor-device.md) |
| Dodawanie kontenera woluminów</br>Modyfikuj kontener woluminu</br>Usuwanie kontenera woluminów |[Zarządzanie kontenerami woluminów](storsimple-8000-manage-volume-containers.md) |
| Dodawanie woluminu</br>Modyfikowanie woluminu</br>Przełącz wolumin w tryb offline</br>Usuwanie woluminu</br>Monitor woluminu |[Zarządzanie woluminami](storsimple-8000-manage-volumes-u2.md) |
| Modyfikowanie ustawień urządzenia</br>Zmodyfikuj ustawienia czasu</br>Modyfikowanie ustawień DNS.md</br>Konfigurowanie interfejsów sieciowych |[Modyfikowanie konfiguracji urządzenia dla urządzenia StorSimple](storsimple-8000-modify-device-config.md) |
| Wyświetl ustawienia serwera proxy sieci web |[Konfigurowanie serwera proxy sieci web dla urządzenia](storsimple-8000-configure-web-proxy.md) |
| Modyfikowanie hasła administratora urządzenia</br>Modyfikowanie hasło programu StorSimple Snapshot Manager |[Zmienianie haseł usługi StorSimple](storsimple-8000-change-passwords.md) |
| Konfigurowanie zdalnego zarządzania |[Zdalne łączenie się z urządzeniem StorSimple](storsimple-8000-remote-connect.md) |
| Konfigurowanie ustawień alertów |[Wyświetlanie i Zarządzanie alertami usługi StorSimple](storsimple-8000-manage-alerts.md) |
| Konfigurowanie protokołu CHAP dla urządzenia StorSimple |[Konfigurowanie protokołu CHAP dla urządzenia StorSimple](storsimple-configure-chap.md) |
| Dodawanie zasad kopii zapasowych</br>Dodaj lub zmodyfikuj harmonogram</br>Usuwanie zasad kopii zapasowych</br>Utwórz kopię zapasową ręczne</br>Utwórz niestandardowe zasady kopii zapasowych z wieloma woluminami i harmonogramów |[Zarządzanie zasadami tworzenia kopii zapasowych](storsimple-8000-manage-backup-policies-u2.md) |
| Zatrzymaj kontrolery urządzeń</br>Uruchom ponownie kontrolery urządzeń</br>Zamknij kontrolery urządzeń</br>Resetowanie urządzenia do domyślnych ustawień fabrycznych</br>(Powyżej dotyczą tylko urządzenie lokalne) |[Zarządzanie kontrolerem urządzeń StorSimple](storsimple-8000-manage-device-controller.md) |
| Dowiedz się więcej o składniki sprzętowe usługi StorSimple</br>Monitor stanu sprzętu</br>(Powyżej dotyczą tylko urządzenie lokalne) |[Monitor składników sprzętowych](storsimple-8000-monitor-hardware-status.md) |
| Tworzenie pakietu pomocy technicznej |[Tworzenie i zarządzanie nimi pakietu dla pomocy technicznej](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |
| Instalacja aktualizacji oprogramowania |[Aktualizowanie urządzenia](storsimple-update-device.md) |

## <a name="next-steps"></a>Kolejne kroki

Jeśli wystąpią problemy z codziennych operacji urządzenia StorSimple lub dowolny z jego składników sprzętowych, zobacz:

* [Rozwiązywanie problemów przy użyciu narzędzia do diagnostyki](storsimple-8000-diagnostics.md)
* [Użyj usługi StorSimple diod LED wskaźnika monitorowania](storsimple-monitoring-indicators.md)

Jeśli nie można rozwiązać problemy i musisz utworzyć żądanie obsługi, zapoznaj się [skontaktuj się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md).

