---
title: Zmienianie haseł StorSimple | Dokumentacja firmy Microsoft
description: Informacje dotyczące używania usługi Menedżer StorSimple urządzeń można zmienić hasła administratora StorSimple Snapshot Manager i urządzeń.
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
ms.openlocfilehash: 7762f8499c67672f0a2ffed99e98baea4c940fa0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874808"
---
# <a name="use-the-storsimple-device-manager-service-to-change-your-storsimple-passwords"></a>Umożliwia zmianę hasła StorSimple usługi Menedżer StorSimple urządzenia

## <a name="overview"></a>Omówienie
Azure portal **ustawienia urządzenia** opcja zawiera wszystkie parametry urządzenia, które można skonfigurować na urządzeniu StorSimple, które jest zarządzane przez usługę Menedżera urządzeń StorSimple. W tym samouczku opisano, jak używasz **zabezpieczeń** opcję w obszarze **ustawienia urządzenia** można zmienić administratora urządzenia i hasło programu StorSimple Snapshot Manager.

## <a name="change-the-device-administrator-password"></a>Zmiana hasła administratora urządzenia
Korzystając z interfejsu programu Windows PowerShell do uzyskania dostępu do urządzenia StorSimple, należy wprowadzić hasło administratora urządzenia. Po zarejestrowaniu pierwszego urządzenia StorSimple z usługą domyślne hasło dla tego interfejsu jest *Password1*. Dla bezpieczeństwa danych należy zmienić hasło po zakończeniu procesu rejestracji. W procesie rejestracji nie może zamknąć bez zmiany hasła. Aby uzyskać więcej informacji, zobacz [krok 3: Konfigurowanie i rejestrowanie urządzenia za pomocą środowiska Windows PowerShell dla StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

Hasło, które najpierw została ustawiona podczas rejestracji za pośrednictwem interfejsu programu Windows PowerShell można później zmienić za pośrednictwem portalu Azure. Wykonaj poniższe kroki, aby zmienić hasło administratora urządzenia.

#### <a name="to-change-the-device-administrator-password"></a>Aby zmienić hasło administratora urządzenia
1. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**.

2. Z tabelarycznej listę urządzeń, wybierz, a następnie kliknij urządzenie, którego chcesz zmienić hasło.

    ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. W **ustawienia** bloku, przejdź do **ustawienia urządzenia > zabezpieczeń**.

    ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. W **ustawienia zabezpieczeń** bloku, kliknij przycisk **hasło** Aby zmienić hasło administratora urządzenia.

    ![](./media/storsimple-8000-change-passwords/changepwd3.png)

5. W **hasło** bloku hasło administratora, który zawiera od 8 do 15 znaków. Hasło musi zawierać kombinację 3 lub więcej znaków wielkie litery, małe litery, liczbowych i specjalnych.

6. Potwierdź hasło.

    ![](./media/storsimple-8000-change-passwords/changepwd4.png)

7. Kliknij przycisk **zapisać** i po wyświetleniu monitu o potwierdzenie, kliknij przycisk **tak**.

    ![](./media/storsimple-8000-change-passwords/changepwd6.png)

Teraz należy uaktualnić hasło administratora urządzenia. To hasło modyfikacji umożliwia dostęp do interfejsu programu Windows PowerShell.

## <a name="set-the-storsimple-snapshot-manager-password"></a>Ustaw hasło programu StorSimple Snapshot Manager
Oprogramowanie StorSimple Snapshot Manager jest zainstalowane na hoście z systemem Windows i umożliwia administratorom zarządzanie kopiami zapasowymi urządzenia StorSimple przez tworzenie migawek lokalnych i w chmurze.

Podczas konfigurowania urządzenia StorSimple Snapshot Manager, pojawi się monit o podanie adresu IP urządzenia i hasła do uwierzytelnienia urządzenia magazynu.

Możesz ustawić lub zmienić hasło programu StorSimple Snapshot Manager za pośrednictwem portalu Azure. Wykonaj poniższe kroki, aby ustawić lub zmienić hasło programu StorSimple Snapshot Manager.

#### <a name="to-set-the-storsimple-snapshot-manager-password"></a>Aby ustawić hasło programu StorSimple Snapshot Manager
1. Przejdź do usługi Menedżer urządzeń StorSimple i kliknij pozycję **Urządzenia**.

2. Z tabelarycznej listę urządzeń, wybierz, a następnie kliknij urządzenie, którego chcesz ustawić lub zmienić hasło programu StorSimple Snapshot Manager.

     ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. W **ustawienia** bloku, przejdź do **ustawienia urządzenia > zabezpieczeń**.

     ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. W **ustawienia zabezpieczeń** bloku, kliknij przycisk **hasło** ustawić lub zmienić hasło programu StorSimple Snapshot Manager.

     ![](./media/storsimple-8000-change-passwords/changepwd3.png) 

5. W **hasło** bloku, wprowadź hasło składające się z 14 do 15 znaków. Upewnij się, że hasło zawiera kombinację 3 lub więcej znaków wielkie litery, małe litery, liczbowych i specjalnych.

6. Potwierdź hasło.

     ![](./media/storsimple-8000-change-passwords/changepwd5.png)

7. Kliknij przycisk **zapisać** i po wyświetleniu monitu o potwierdzenie, kliknij przycisk **tak**.

     ![](./media/storsimple-8000-change-passwords/changepwd6.png)

Hasło programu StorSimple Snapshot Manager teraz powinny zostać uaktualnione.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [zabezpieczenia usługi StorSimple](storsimple-8000-security.md).
* Dowiedz się więcej o [modyfikowanie konfiguracji urządzenia](storsimple-8000-modify-device-config.md).
* Dowiedz się więcej o [przy użyciu usługi Menedżer StorSimple urządzenia do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

