---
title: Administracja tablicą wirtualną usługi Microsoft Azure StorSimple Manager | Dokumenty firmy Microsoft
description: Dowiedz się, jak zarządzać lokalną macierzą wirtualną StorSimple przy użyciu usługi StorSimple Device Manager w witrynie Azure portal.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "62123809"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-virtual-array"></a>Do administrowania macierzą wirtualną StorSimple za pomocą usługi StorSimple Device Manager
![przepływ procesu instalacji](./media/storsimple-virtual-array-manager-service-administration/manage4.png)

## <a name="overview"></a>Omówienie
W tym artykule opisano interfejs usługi StorSimple Device Manager, w tym sposób łączenia się z nim i różne opcje dostępne i zawiera łącza do określonych przepływów pracy, które mogą być wykonywane za pośrednictwem tego interfejsu użytkownika.

Po przeczytaniu tego artykułu, będziesz wiedzieć, jak:

* Łączenie się z usługą StorSimple Device Manager
* Nawigowanie po interfejsie użytkownika Menedżera urządzeń StorSimple
* Administrowanie macierzą wirtualną StorSimple za pośrednictwem usługi StorSimple Device Manager

> [!NOTE]
> Aby wyświetlić opcje zarządzania dostępne dla urządzenia z serii StorSimple 8000, przejdź do [witryny Użyj usługi StorSimple Manager, aby administrować urządzeniem StorSimple](storsimple-manager-service-administration.md).
> 
> 

## <a name="connect-to-the-storsimple-device-manager-service"></a>Łączenie się z usługą StorSimple Device Manager
Usługa StorSimple Device Manager działa na platformie Microsoft Azure i łączy się z wieloma macierzami wirtualnymi StorSimple. Do zarządzania tymi urządzeniami można użyć centralnego portalu platformy Microsoft Azure działającego w przeglądarce. Aby połączyć się z usługą StorSimple Device Manager, wykonaj następujące czynności.

#### <a name="to-connect-to-the-service"></a>Aby połączyć się z usługą
1. Przejdź [https://ms.portal.azure.com](https://ms.portal.azure.com)do .
2. Korzystając z poświadczeń konta Microsoft, zaloguj się do portalu Microsoft Azure (znajdującego się w prawym górnym rogu okienka).
3. Przejdź do sekcji Przeglądaj - > "Filtruj" na Menedżerach urządzeń StorSimple, aby wyświetlić wszystkich menedżerów urządzeń w danej subskrypcji.

## <a name="use-the-storsimple-device-manager-service-to-perform-management-tasks"></a>Korzystanie z usługi StorSimple Device Manager do wykonywania zadań zarządzania
W poniższej tabeli przedstawiono podsumowanie wszystkich typowych zadań zarządzania i złożonych przepływów pracy, które można wykonać w bloku podsumowania usługi StorSimple Device Manager. Zadania te są zorganizowane na podstawie ostrzy, na których są inicjowane.

Aby uzyskać więcej informacji na temat każdego przepływu pracy, kliknij odpowiednią procedurę w tabeli.

#### <a name="storsimple-device-manager-workflows"></a>Przepływy pracy Menedżera urządzeń StorSimple
| Jeśli chcesz to zrobić ... | Użyj tej procedury |
| --- | --- |
| Tworzenie usługi</br>Usuwanie usługi</br>Pobieranie klucza rejestracji usługi</br>Ponowne generowanie klucza rejestracji usługi |[Wdrażanie usługi StorSimple Device Manager](storsimple-virtual-array-manage-service.md) |
| Wyświetlanie dzienników aktywności |[Użyj podsumowania usługi StorSimple](storsimple-virtual-array-service-summary.md) |
| Dezaktywowanie tablicy wirtualnej</br>Usuwanie tablicy wirtualnej |[Dezaktywowanie lub usuwanie tablicy wirtualnej](storsimple-virtual-array-deactivate-and-delete-device.md) |
| Odzyskiwanie po awarii i funkcja failover urządzenia</br>Wymagania wstępne trybu failover</br>Odzyskiwanie po awarii ciągłości działania (BCDR)</br>Błędy podczas odzyskiwania po awarii |[Odzyskiwanie po awarii i tryb failover urządzenia dla tablicy wirtualnej StorSimple](storsimple-virtual-array-failover-dr.md) |
| Dziele zapasowe udziałów i wolumenów</br>Wykonywanie ręcznej kopii zapasowej</br>Zmienianie harmonogramu tworzenia kopii zapasowych</br>Wyświetlanie istniejących kopii zapasowych |[Czelizowanie kopii zapasowej tablicy wirtualnej StorSimple](storsimple-virtual-array-backup.md) |
| Klonowanie udziałów z zestawu kopii zapasowych</br>Klonowanie woluminów z zestawu kopii zapasowych</br>Odzyskiwanie na poziomie elementu (tylko serwer plików) |[Klonowanie z kopii zapasowej tablicy wirtualnej StorSimple](storsimple-virtual-array-clone.md) |
| Informacje o kontach magazynu</br>Dodawanie konta magazynu</br>Edytowanie konta magazynu</br>Usuwanie konta magazynu |[Zarządzanie kontami magazynu dla tablicy wirtualnej StorSimple](storsimple-virtual-array-manage-storage-accounts.md) |
| Rekordy kontroli dostępu – informacje</br>Dodawanie lub modyfikowanie rekordu kontroli dostępu </br>Usuwanie rekordu kontroli dostępu |[Zarządzanie rekordami kontroli dostępu dla tablicy wirtualnej StorSimple](storsimple-virtual-array-manage-acrs.md) |
| Wyświetlanie szczegółów zadania |[Zarządzanie zadaniami tablicy wirtualnej StorSimple](storsimple-virtual-array-manage-jobs.md) |
| Konfigurowanie ustawień alertów</br>Otrzymywanie powiadomień o alertach</br>Zarządzanie alertami</br>Przeglądanie alertów |[Wyświetlanie alertów dla tablicy wirtualnej StorSimple i zarządzanie nimi](storsimple-virtual-array-manage-alerts.md) |
| Modyfikowanie hasła administratora urządzenia |[Zmienianie hasła administratora urządzenia StorSimple Virtual Array](storsimple-virtual-array-change-device-admin-password.md) |
| Instalowanie aktualizacji oprogramowania |[Aktualizowanie tablicy wirtualnej](storsimple-virtual-array-install-update.md) |

> [!NOTE]
> Lokalnego [interfejsu użytkownika sieci Web](storsimple-ova-web-ui-admin.md) należy używać do wykonywania następujących zadań:
> 
> * [Pobieranie klucza szyfrowania danych usługi](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [Tworzenie pakietu pomocy technicznej](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [Zatrzymywać i ponownie uruchamiać tablicę wirtualną](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać informacje na temat interfejsu użytkownika sieci Web i sposobu jego używania, zobacz [Używanie interfejsu użytkownika sieci Web StorSimple do administrowania tablicą wirtualną StorSimple](storsimple-ova-web-ui-admin.md).

