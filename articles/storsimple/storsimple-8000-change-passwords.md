---
title: Zmienianie haseł usługi StorSimple | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak zmienić hasło administratora programu StorSimple Snapshot Manager i urządzeń za pomocą usługi Menedżer urządzeń StorSimple.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60638012"
---
# <a name="use-the-storsimple-device-manager-service-to-change-your-storsimple-passwords"></a>Zmienianie haseł usługi StorSimple przy użyciu usługi Menedżer urządzeń StorSimple

## <a name="overview"></a>Omówienie
Witryna Azure portal **ustawienia urządzenia** opcja zawiera wszystkie parametry urządzenia, które można skonfigurować ponownie na urządzeniu StorSimple, który jest zarządzany przez usługę Menedżer urządzeń StorSimple. W tym samouczku wyjaśniono, jak można użyć **zabezpieczeń** opcji w obszarze **ustawienia urządzenia** Aby zmienić administratora urządzenia lub hasło programu StorSimple Snapshot Manager.

## <a name="change-the-device-administrator-password"></a>Zmiana hasła administratora urządzenia
Korzystając z interfejsu programu Windows PowerShell w celu uzyskania dostępu do urządzenia StorSimple, musisz wprowadzić hasło administratora urządzenia. Podczas rejestrowania pierwszego urządzenia StorSimple z usługą, domyślne hasło dla tego interfejsu jest *Password1*. Dla bezpieczeństwa danych są wymagane, aby zmienić to hasło pod koniec procesu rejestracji. Nie można zamknąć w procesie rejestracji, bez konieczności zmieniania hasła. Aby uzyskać więcej informacji, zobacz [krok 3: Konfigurowanie i rejestrowanie urządzenia za pomocą programu Windows PowerShell dla usługi StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

Hasło, które najpierw została ustawiona podczas rejestracji za pośrednictwem interfejsu programu Windows PowerShell można później zmienić w witrynie Azure portal. Wykonaj poniższe kroki, aby zmienić hasło administratora urządzenia.

#### <a name="to-change-the-device-administrator-password"></a>Aby zmienić hasło administratora urządzenia
1. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**.

2. Z tabelarycznej listy urządzeń wybierz i kliknij urządzenie, którego hasło, których zamierzasz zmienić.

    ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. W **ustawienia** przejdź do bloku **ustawienia urządzenia > zabezpieczeń**.

    ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. W **ustawienia zabezpieczeń** bloku kliknij **hasło** Aby zmienić hasło administratora urządzenia.

    ![](./media/storsimple-8000-change-passwords/changepwd3.png)

5. W **hasło** bloku, podaj hasło administratora, który zawiera od 8 do 15 znaków. Hasło musi zawierać kombinację 3 lub więcej wielkie litery, małe litery, cyfry i znaki specjalne.

6. Potwierdź hasło.

    ![](./media/storsimple-8000-change-passwords/changepwd4.png)

7. Kliknij przycisk **Zapisz** i po wyświetleniu monitu o potwierdzenie, kliknij przycisk **tak**.

    ![](./media/storsimple-8000-change-passwords/changepwd6.png)

Hasło administratora urządzenia teraz powinien zostać zaktualizowany. To hasło modyfikacji umożliwia dostęp do interfejsu programu Windows PowerShell.

## <a name="set-the-storsimple-snapshot-manager-password"></a>Ustaw hasło programu StorSimple Snapshot Manager
Oprogramowanie StorSimple Snapshot Manager jest zainstalowane na hoście z systemem Windows i umożliwia administratorom zarządzanie kopiami zapasowymi urządzenia StorSimple przez tworzenie migawek lokalnych i w chmurze.

Podczas konfigurowania urządzenia w programie StorSimple Snapshot Manager, wyświetli się monit o podanie adresu IP urządzenia i hasła w celu uwierzytelnienia urządzenia magazynu.

Można ustawić lub zmienić hasło programu StorSimple Snapshot Manager w witrynie Azure portal. Wykonaj poniższe kroki, aby ustawić lub zmienić hasło programu StorSimple Snapshot Manager.

#### <a name="to-set-the-storsimple-snapshot-manager-password"></a>Aby ustawić hasło programu StorSimple Snapshot Manager
1. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**.

2. Z tabelarycznej listy urządzeń wybierz i kliknij urządzenie, którego hasło programu StorSimple Snapshot Manager, których zamierzasz ustawić lub zmienić.

     ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. W **ustawienia** przejdź do bloku **ustawienia urządzenia > zabezpieczeń**.

     ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. W **ustawienia zabezpieczeń** bloku kliknij **hasło** ustawić lub zmienić hasło programu StorSimple Snapshot Manager.

     ![](./media/storsimple-8000-change-passwords/changepwd3.png) 

5. W **hasło** bloku, wprowadź hasło składające się z 14 lub 15 znaków. Upewnij się, że hasło zawiera kombinację 3 lub więcej wielkie litery, małe litery, cyfry i znaki specjalne.

6. Potwierdź hasło.

     ![](./media/storsimple-8000-change-passwords/changepwd5.png)

7. Kliknij przycisk **Zapisz** i po wyświetleniu monitu o potwierdzenie, kliknij przycisk **tak**.

     ![](./media/storsimple-8000-change-passwords/changepwd6.png)

Hasło programu StorSimple Snapshot Manager teraz powinien zostać zaktualizowany.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [zabezpieczenia usługi StorSimple](storsimple-8000-security.md).
* Dowiedz się więcej o [modyfikowanie konfiguracji urządzenia](storsimple-8000-modify-device-config.md).
* Dowiedz się więcej o [przy użyciu usługi Menedżer urządzeń StorSimple do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

