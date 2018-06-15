---
title: Hasło administratora urządzenia StorSimple wirtualnego tablicy zmiany | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak zmienić hasło administratora urządzenia za pomocą portalu Azure lub interfejsu użytkownika sieci web tablicy wirtualnego StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 11490814-d9fd-4dc7-9c3b-55dd2c23eaf1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 260a23003d705e6598da8c51bb5a96f2539a0014
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875571"
---
# <a name="change-the-storsimple-virtual-array-device-administrator-password-via-storsimple-device-manager"></a>Zmień hasło administratora urządzenia StorSimple wirtualnego tablicy za pomocą Menedżera urządzeń StorSimple

## <a name="overview"></a>Omówienie

Korzystając z interfejsu programu Windows PowerShell do dostępu do tablicy wirtualnego StorSimple, należy wprowadzić hasło administratora urządzenia. Gdy urządzenie StorSimple jest najpierw udostępnione i uruchomiony, jest domyślne hasło *Password1*. Dla bezpieczeństwa danych domyślne hasło wygaśnie po raz pierwszy możesz się zalogować i są wymagane, aby zmienić hasło.

Aby zmienić hasło administratora urządzenia w dowolnej chwili po wdrożeniu urządzenia w środowisku produkcyjnym, można użyć lokalnego interfejsu użytkownika sieci web lub w portalu Azure. W tym artykule opisano każdy z tych procedur.

 ![Blok urządzeń](./media/storsimple-virtual-array-change-device-admin-password/ova-devices-blade.png)

## <a name="use-the-azure-portal-to-change-the-password"></a>Użyj portalu Azure do zmiany hasła

Wykonaj poniższe kroki, aby zmienić hasło administratora urządzenia za pośrednictwem portalu Azure.

#### <a name="to-change-the-device-administrator-password-via-the-azure-portal"></a>Aby zmienić hasło administratora urządzenia za pośrednictwem portalu Azure

1. Na stronie docelowej usługi, wybierz usługę, kliknij dwukrotnie nazwę usługi, a następnie w **zarządzania** kliknij **urządzeń**. Spowoduje to otwarcie **urządzeń** bloku, który zawiera listę wszystkich urządzeń wirtualnych tablicy StorSimple.

2. W **urządzeń** bloku, kliknij dwukrotnie urządzenie wymaga zmiany hasła.

3. W **ustawienia** bloku dla danego urządzenia, kliknij przycisk **zabezpieczeń**.

4. W **ustawienia zabezpieczeń** blok, wykonaj następujące czynności:
   
   1. Przewiń w dół do **hasło administratora urządzenia** sekcji. Podaj hasło administratora, który zawiera od 8 do 15 znaków.
   2. Potwierdź hasło.
   3. Kliknij przycisk **zapisać** w górnej części bloku.

Hasło administratora urządzenia jest już uaktualniona. To hasło modyfikacji służy do uzyskania dostępu do urządzenia lokalnego.

![Blok ustawień zabezpieczeń](./media/storsimple-virtual-array-change-device-admin-password/ova-change-device-pwd.png)

## <a name="use-the-local-web-ui-to-change-the-password"></a>Użyj lokalnego interfejsu użytkownika sieci web, aby zmienić hasło

Wykonaj poniższe kroki, aby zmienić hasło administratora urządzenia za pośrednictwem lokalnego interfejsu użytkownika sieci web.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Aby zmienić hasło administratora urządzenia za pośrednictwem lokalnego interfejsu użytkownika sieci web

1. W lokalnej sieci web interfejsu użytkownika, kliknij przycisk **konserwacji** > **zmiany hasła** dla danego urządzenia.
   
    ![Zmień password1](./media/storsimple-virtual-array-change-device-admin-password/image40.png)
2. Wprowadź **bieżące hasło**.
3. Podaj **nowe hasło**. Hasło musi mieć co najmniej 8 znaków. Musi zawierać 3 z 4 z następujących czynności: wielkie litery, małe litery, liczbowego i znaki specjalne.
   
    Należy pamiętać, że hasło nie może być taka sama jak hasła w ostatnich 24.
4. Wpisz ponownie hasło, aby je potwierdzić.
   
    ![Zmień Hasło2](./media/storsimple-virtual-array-change-device-admin-password/image41.png)
5. W dolnej części strony kliknij **Zastosuj**. Nowe hasło jest teraz stosowane. Jeśli zmiana hasła nie powiedzie się, zostanie wyświetlony następujący błąd:
   
    ![Błąd hasła](./media/storsimple-virtual-array-change-device-admin-password/image42.png)
   
    Po pomyślnej aktualizacji hasła, użytkownik jest powiadamiany. Następnie służy to zmodyfikowane hasło dostępu do urządzenia lokalnego.


## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [administrowania tablica wirtualnego StorSimple](storsimple-ova-web-ui-admin.md).

