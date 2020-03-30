---
title: Zmienianie haseł StorSimple | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak za pomocą usługi StorSimple Device Manager można zmienić Menedżera migawek StorSimple i hasła administratora urządzeń.
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
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: ab874bbdcd47a4bfa9abfba721afa46d0f23a338
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268030"
---
# <a name="use-the-storsimple-device-manager-service-to-change-your-storsimple-passwords"></a>Zmiana haseł StorSimple za pomocą usługi StorSimple Device Manager

## <a name="overview"></a>Omówienie
Opcja **Ustawienia urządzenia** portalu Azure zawiera wszystkie parametry urządzenia, które można ponownie skonfigurować na urządzeniu StorSimple, które jest zarządzane przez usługę StorSimple Device Manager. W tym samouczku wyjaśniono, jak za pomocą opcji **Zabezpieczenia** w obszarze **Ustawienia urządzenia** można zmienić administratora urządzenia lub hasło Menedżera migawek StorSimple.

## <a name="change-the-device-administrator-password"></a>Zmiana hasła administratora urządzenia
Podczas uzyskiwania dostępu do urządzenia StorSimple za pomocą interfejsu programu Windows PowerShell wymagane jest wprowadzenie hasła administratora urządzenia. Gdy pierwsze urządzenie StorSimple jest zarejestrowane w usłudze, domyślnym hasłem dla tego interfejsu jest *Password1*. Ze względów bezpieczeństwa danych należy zmienić to hasło po zakończeniu procesu rejestracji. Nie można zakończyć procesu rejestracji bez zmiany tego hasła. Aby uzyskać więcej informacji, zobacz [Krok 3: Konfigurowanie i rejestrowanie urządzenia za pośrednictwem programu Windows PowerShell dla storsimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

Hasło, które zostało po raz pierwszy ustawione za pośrednictwem interfejsu programu Windows PowerShell podczas rejestracji można zmienić później za pośrednictwem witryny Azure portal. Wykonaj następujące czynności, aby zmienić hasło administratora urządzenia.

#### <a name="to-change-the-device-administrator-password"></a>Aby zmienić hasło administratora urządzenia
1. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**.

2. Z tabelarycznej listy urządzeń wybierz i kliknij urządzenie, którego hasło zamierzasz zmienić.

    ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. W bloku **Ustawienia** przejdź do pozycji **Ustawienia urządzenia > Security**.

    ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. W bloku **Ustawienia zabezpieczeń** kliknij pozycję **Hasło,** aby zmienić hasło administratora urządzenia.

    ![](./media/storsimple-8000-change-passwords/changepwd3.png)

5. W **bloku Hasło** podaj hasło administratora zawierające od 8 do 15 znaków. Hasło musi być kombinacją 3 lub więcej wielkich, małych liter, znaków liczbowych i specjalnych.

6. Potwierdź hasło.

    ![](./media/storsimple-8000-change-passwords/changepwd4.png)

7. Kliknij **przycisk Zapisz,** a po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**.

    ![](./media/storsimple-8000-change-passwords/changepwd6.png)

Hasło administratora urządzenia powinno zostać zaktualizowane. To zmodyfikowane hasło umożliwia dostęp do interfejsu programu Windows PowerShell.

## <a name="set-the-storsimple-snapshot-manager-password"></a>Ustawianie hasła Menedżera migawek StorSimple
Oprogramowanie StorSimple Snapshot Manager jest zainstalowane na hoście z systemem Windows i umożliwia administratorom zarządzanie kopiami zapasowymi urządzenia StorSimple przez tworzenie migawek lokalnych i w chmurze.

Podczas konfigurowania urządzenia w Menedżerze migawek StorSimple zostanie wyświetlony monit o podanie adresu IP urządzenia i hasła w celu uwierzytelnienia urządzenia magazynującego.

Można ustawić lub zmienić hasło storsimple Menedżera migawek za pośrednictwem witryny Azure portal. Wykonaj następujące kroki, aby ustawić lub zmienić hasło Menedżera migawek StorSimple.

#### <a name="to-set-the-storsimple-snapshot-manager-password"></a>Aby ustawić hasło Menedżera migawek StorSimple
1. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**.

2. Z tabelarycznej listy urządzeń wybierz i kliknij urządzenie, którego hasło Menedżera migawek StorSimple zamierzasz ustawić lub zmienić.

     ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. W bloku **Ustawienia** przejdź do pozycji **Ustawienia urządzenia > Security**.

     ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. W bloku **Ustawienia zabezpieczeń** kliknij pozycję **Hasło,** aby ustawić lub zmienić hasło Menedżera migawek StorSimple.

     ![](./media/storsimple-8000-change-passwords/changepwd3.png) 

5. W bloku **Hasło** wprowadź hasło o masie 14 lub 15 znaków. Upewnij się, że hasło zawiera kombinację 3 lub więcej znaków wielkich, małych, numerycznych i specjalnych.

6. Potwierdź hasło.

     ![](./media/storsimple-8000-change-passwords/changepwd5.png)

7. Kliknij **przycisk Zapisz,** a po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**.

     ![](./media/storsimple-8000-change-passwords/changepwd6.png)

Hasło Menedżera migawek StorSimple powinno zostać zaktualizowane.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [zabezpieczeniach StorSimple](storsimple-8000-security.md).
* Dowiedz się więcej o [modyfikowaniu konfiguracji urządzenia](storsimple-8000-modify-device-config.md).
* Dowiedz się więcej o [administrowaniu urządzeniem StorSimple za pomocą usługi StorSimple Device Manager.](storsimple-8000-manager-service-administration.md)

