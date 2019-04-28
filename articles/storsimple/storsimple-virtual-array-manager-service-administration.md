---
title: Microsoft Azure StorSimple Manager Virtual Array administracji | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać macierz wirtualna StorSimple w środowisku lokalnym za pomocą usługi Menedżer urządzeń StorSimple w witrynie Azure portal.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 958244a5-f9f5-455e-b7ef-71a65558872e
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: bb6bb491ca71e5ced5aecc8137e9e1cbd950e80b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123809"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-virtual-array"></a>Używanie usługi Menedżer urządzeń StorSimple do administrowania rozwiązania StorSimple Virtual Array
![przepływ procesu instalacji](./media/storsimple-virtual-array-manager-service-administration/manage4.png)

## <a name="overview"></a>Omówienie
Ten artykuł zawiera opis interfejsu usługi Menedżer urządzeń StorSimple, w tym sposób nawiązywania połączeń i różnych dostępnych opcjach i zawiera łącza do określonych przepływów pracy, które mogą być wykonywane za pośrednictwem tego interfejsu użytkownika.

Po przeczytaniu tego artykułu, będzie wiadomo, jak:

* Połącz się z usługą Menedżera urządzeń StorSimple
* Przejdź do Menedżera urządzeń StorSimple interfejsu użytkownika
* Administrowanie rozwiązania StorSimple Virtual Array za pośrednictwem usługi Menedżer urządzeń StorSimple

> [!NOTE]
> Aby wyświetlić opcje zarządzania dostępne dla urządzenia StorSimple 8000 series, przejdź do [administrowania urządzeniem StorSimple przy użyciu usługi StorSimple Manager](storsimple-manager-service-administration.md).
> 
> 

## <a name="connect-to-the-storsimple-device-manager-service"></a>Połącz się z usługą Menedżera urządzeń StorSimple
Usługa Menedżer urządzeń StorSimple działa na platformie Microsoft Azure i łączy wiele macierzy wirtualnej StorSimple. Używasz centralnej portalu Microsoft Azure, korzystania z przeglądarki do zarządzania tymi urządzeniami. Aby połączyć z usługą Menedżera urządzeń StorSimple, wykonaj następujące czynności.

#### <a name="to-connect-to-the-service"></a>Aby połączyć się z usługą
1. Przejdź do obszaru [https://ms.portal.azure.com](https://ms.portal.azure.com) (Ustawienia — Integracje i usługi).
2. Przy użyciu poświadczeń konta Microsoft, zaloguj się do portalu Microsoft Azure (znajdujące się w prawym górnym rogu okienka).
3. Przejdź do obszaru Przeglądaj--> "Filtrowanie" na menedżerów urządzeń StorSimple, aby wyświetlić menedżerów urządzeń w ramach danej subskrypcji.

## <a name="use-the-storsimple-device-manager-service-to-perform-management-tasks"></a>Usługa Menedżer urządzeń StorSimple do wykonywania zadań administracyjnych
Poniższa tabela zawiera podsumowanie typowych zadań zarządzania i złożonych przepływów pracy, które mogą być wykonywane w bloku podsumowania usługi Menedżer urządzeń StorSimple. Te zadania są zorganizowane w oparciu o bloki, które są inicjowane.

Aby uzyskać więcej informacji na temat każdego przepływu pracy kliknij odpowiednią procedurę w tabeli.

#### <a name="storsimple-device-manager-workflows"></a>Menedżer urządzeń StorSimple przepływów pracy
| Jeśli chcesz to zrobić... | Użyj tej procedury |
| --- | --- |
| Tworzenie usługi</br>Usuwanie usługi</br>Pobieranie klucza rejestracji usługi</br>Wygeneruj ponownie klucz rejestracji usługi |[Wdrażanie usługi Menedżer urządzeń StorSimple](storsimple-virtual-array-manage-service.md) |
| Wyświetlanie dzienników aktywności |[Użyj Podsumowanie usługi StorSimple](storsimple-virtual-array-service-summary.md) |
| Dezaktywuj macierzy wirtualnej</br>Usuń macierz wirtualna |[Dezaktywacji lub usunięcia macierzy wirtualnej](storsimple-virtual-array-deactivate-and-delete-device.md) |
| Tryb failover odzyskiwania i urządzenia po awarii</br>Wymagania wstępne dotyczące trybu failover</br>Odzyskiwanie po awarii ciągłość działania firmy (BCDR)</br>Błędy podczas odzyskiwania po awarii |[Awaryjnego odzyskiwania i urządzenia w trybie failover rozwiązania StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md) |
| Tworzenie kopii zapasowej udziały i woluminy</br>Utwórz kopię zapasową ręczne</br>Zmień harmonogram tworzenia kopii zapasowych</br>Wyświetl istniejące kopie zapasowe |[Tworzenie kopii zapasowej rozwiązania StorSimple Virtual Array](storsimple-virtual-array-backup.md) |
| Klonowanie udziałów z zestawu kopii zapasowych</br>Klonowanie woluminów z zestawu kopii zapasowych</br>Odzyskiwanie na poziomie elementu (tylko serwer plików) |[Klonowanie z kopii zapasowej rozwiązania StorSimple Virtual Array](storsimple-virtual-array-clone.md) |
| Dotyczących kont magazynu</br>Dodawanie konta magazynu</br>Edytuj konto magazynu</br>Usuwanie konta magazynu |[Zarządzanie kontami magazynu dla macierzy wirtualnej StorSimple](storsimple-virtual-array-manage-storage-accounts.md) |
| Informacje o rekordach kontroli dostępu</br>Dodaje lub modyfikuje rekord kontroli dostępu </br>Usuwanie rekordu kontroli dostępu |[Zarządzanie rekordy kontroli dostępu dla rozwiązania StorSimple Virtual Array](storsimple-virtual-array-manage-acrs.md) |
| Wyświetlanie szczegółów zadania |[Zarządzanie zadaniami macierzy wirtualnej StorSimple](storsimple-virtual-array-manage-jobs.md) |
| Konfigurowanie ustawień alertów</br>Otrzymywanie powiadomień o alertach</br>Zarządzanie alertami</br>Przeglądanie alertów |[Wyświetlanie alertów i zarządzanie nimi dla rozwiązania StorSimple Virtual Array](storsimple-virtual-array-manage-alerts.md) |
| Modyfikowanie hasła administratora urządzenia |[Zmień hasło administratora urządzenia StorSimple Virtual Array](storsimple-virtual-array-change-device-admin-password.md) |
| Instalacja aktualizacji oprogramowania |[Aktualizuj macierz wirtualna](storsimple-virtual-array-install-update.md) |

> [!NOTE]
> Należy użyć [lokalnego Interfejsu w przeglądarce](storsimple-ova-web-ui-admin.md) dla następujących zadań:
> 
> * [Pobierz klucz szyfrowania danych usługi](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [Tworzenie pakietu pomocy technicznej](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [Zatrzymaj i ponownie uruchom macierz wirtualną](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>Kolejne kroki
Informacje dotyczące interfejsu użytkownika sieci web i jak z niej korzystać, przejdź do [Użyj interfejsu użytkownika sieci web usługi StorSimple do administrowania rozwiązania StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

