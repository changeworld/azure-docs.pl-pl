---
title: Microsoft Azure StorSimple Manager wirtualnego tablicy administracji | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać wirtualnego StorSimple tablicy w lokalnej za pomocą usługi Menedżer StorSimple urządzenia w portalu Azure.
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
ms.openlocfilehash: a74a160eae88a2d03460a1346479c333d8f9d524
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875844"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-virtual-array"></a>Zarządzać tablica wirtualnego StorSimple przy użyciu usługi Menedżer StorSimple urządzenia
![przepływ procesu instalacji](./media/storsimple-virtual-array-manager-service-administration/manage4.png)

## <a name="overview"></a>Omówienie
W tym artykule opisano interfejs usługi Menedżera urządzeń StorSimple, w tym sposób połączenia usługi oraz różne opcje dostępne oraz zawiera łącza do konkretnych przepływów pracy, które mogą być wykonywane za pośrednictwem tego interfejsu użytkownika.

Po przeczytaniu tego artykułu, będzie wiadomo, jak:

* Połącz się z usługą Menedżera urządzeń StorSimple
* Przejdź do Menedżera urządzeń StorSimple interfejsu użytkownika
* Administrowanie tablica za pośrednictwem usługi Menedżer StorSimple urządzenia wirtualnego StorSimple

> [!NOTE]
> Aby wyświetlić opcji zarządzania dostępnych dla urządzenia serii StorSimple 8000, przejdź do [zarządzać urządzenia StorSimple przy użyciu usługi Menedżer StorSimple](storsimple-manager-service-administration.md).
> 
> 

## <a name="connect-to-the-storsimple-device-manager-service"></a>Połącz się z usługą Menedżera urządzeń StorSimple
Usługa Menedżera urządzeń StorSimple działa na platformie Microsoft Azure i łączy wiele tablic wirtualne StorSimple. Centralnej portalu Microsoft Azure działającego w przeglądarce umożliwia zarządzanie tymi urządzeniami. Aby połączyć się z usługą Menedżera urządzeń StorSimple, wykonaj następujące czynności.

#### <a name="to-connect-to-the-service"></a>Aby połączyć się z usługą
1. Przejdź do [https://ms.portal.azure.com](https://ms.portal.azure.com).
2. Przy użyciu poświadczeń konta Microsoft, zaloguj się do portalu Microsoft Azure (znajdujące się w prawym górnym rogu okienka).
3. Przejdź do przeglądania--> "Filtruj" menedżerów urządzenia StorSimple, aby wyświetlić wszystkie menedżerów urządzeń w ramach danej subskrypcji.

## <a name="use-the-storsimple-device-manager-service-to-perform-management-tasks"></a>Wykonywanie zadań zarządzania przy użyciu usługi Menedżer StorSimple urządzenia
W poniższej tabeli przedstawiono podsumowanie typowych zadań zarządzania i złożonych przepływów pracy, które mogą być wykonywane w ramach bloku podsumowania usługi Menedżer StorSimple urządzenia. Te zadania są podzielone na bloki, na których są inicjowane podstawie.

Aby uzyskać więcej informacji na temat każdego przepływu pracy kliknij odpowiednią procedurę w tabeli.

#### <a name="storsimple-device-manager-workflows"></a>Menedżer urządzeń StorSimple przepływy pracy
| Jeśli chcesz to zrobić... | Użyj tej procedury |
| --- | --- | --- |
| Tworzenie usługi</br>Usuwanie usługi</br>Pobieranie klucza rejestracji usługi</br>Wygeneruj ponownie klucz rejestracji usługi |[Wdrażanie usługi Menedżer StorSimple urządzenia](storsimple-virtual-array-manage-service.md) |
| Wyświetl dzienniki aktywności |[Użyj Podsumowanie usługi StorSimple](storsimple-virtual-array-service-summary.md) |
| Dezaktywuj wirtualnych tablicy</br>Usuń wirtualne tablicy |[Dezaktywację lub usunięcie wirtualnej tablicy](storsimple-virtual-array-deactivate-and-delete-device.md) |
| Tryb failover odzyskiwania i urządzenia po awarii</br>Wymagania wstępne dotyczące trybu failover</br>Odzyskiwanie po awarii ciągłości biznesowej (BCDR)</br>Błędy podczas odzyskiwania po awarii |[Awaryjnego odzyskiwania i urządzenia w trybie failover tablica wirtualnego StorSimple](storsimple-virtual-array-failover-dr.md) |
| Wykonaj kopię zapasową woluminów i udziałów</br>Wykonaj kopię zapasową ręczne</br>Zmień harmonogram tworzenia kopii zapasowych</br>Wyświetlanie istniejących kopii zapasowych |[Tworzenie kopii zapasowej macierzy wirtualnego StorSimple](storsimple-virtual-array-backup.md) |
| Akcje w klonowania z zestawu kopii zapasowych</br>Klonowanie woluminów z zestawu kopii zapasowych</br>Odzyskiwanie na poziomie elementu (tylko w przypadku serwera plików) |[Klonowanie z kopii zapasowej macierzy wirtualnego StorSimple](storsimple-virtual-array-clone.md) |
| Dotyczących kont magazynu</br>Dodawanie konta magazynu</br>Edytuj konto magazynu</br>Usuwanie konta magazynu |[Zarządzanie kontami magazynu dla macierzy wirtualnego StorSimple](storsimple-virtual-array-manage-storage-accounts.md) |
| Informacje o rekordach kontroli dostępu</br>Dodaje lub modyfikuje rekord kontroli dostępu </br>Usuń rekord kontroli dostępu |[Zarządzanie rekordami kontroli dostępu dla tablicy wirtualnego StorSimple](storsimple-virtual-array-manage-acrs.md) |
| Wyświetlanie szczegółów zadania |[Zarządzanie zadaniami tablicy wirtualnego StorSimple](storsimple-virtual-array-manage-jobs.md) |
| Konfigurowanie ustawień alertów</br>Otrzymywanie powiadomień o alertach</br>Zarządzanie alertami</br>Alerty dotyczące weryfikacji |[Wyświetlanie alertów i zarządzanie nimi dla tablicy wirtualnego StorSimple](storsimple-virtual-array-manage-alerts.md) |
| Zmodyfikuj hasło administratora urządzenia |[Zmień hasło administratora urządzenia StorSimple wirtualnego tablicy](storsimple-virtual-array-change-device-admin-password.md) |
| Instalacja aktualizacji oprogramowania |[Aktualizacja sieci wirtualnej tablicy](storsimple-virtual-array-install-update.md) |

> [!NOTE]
> Należy użyć [lokalnego interfejsu użytkownika sieci web](storsimple-ova-web-ui-admin.md) dla następujących zadań:
> 
> * [Pobierz klucz szyfrowania danych usługi](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [Utwórz pakiet pomocy technicznej](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [Zatrzymaj i ponownie uruchom wirtualnego tablicy](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać informacje o interfejsie użytkownika sieci web i jak z niego korzystać, [zarządzać tablica wirtualnego StorSimple przy użyciu interfejsu użytkownika sieci web StorSimple](storsimple-ova-web-ui-admin.md).

