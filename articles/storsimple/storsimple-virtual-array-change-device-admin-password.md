---
title: Zmienianie hasła administratora urządzenia StorSimple Virtual Array | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak zmienić hasło administratora urządzenia za pomocą witryny Azure portal lub interfejsu użytkownika sieci web macierzy wirtualnej StorSimple.
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
ms.openlocfilehash: 5308badf439254062a8aefca1840eb21bc234ace
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60580414"
---
# <a name="change-the-storsimple-virtual-array-device-administrator-password-via-storsimple-device-manager"></a>Zmień hasło administratora urządzenia StorSimple Virtual Array za pośrednictwem Menedżera urządzeń StorSimple

## <a name="overview"></a>Omówienie

Korzystając z interfejsu programu Windows PowerShell na dostęp do macierzy wirtualnej StorSimple, musisz wprowadzić hasło administratora urządzenia. Gdy urządzenie StorSimple jest najpierw zainicjowano obsługę administracyjną i uruchomiony, jest domyślne hasło *Password1*. Dla bezpieczeństwa danych domyślne hasło wygaśnie, logowania i są wymagane, aby zmienić to hasło po raz pierwszy.

Aby zmienić hasło administratora urządzenia w dowolnym momencie po wdrożeniu urządzenia w środowisku produkcyjnym, można użyć lokalnego Interfejsu w przeglądarce lub w portalu Azure. W tym artykule opisano każdy z tych procedur.

 ![Blok urządzeń](./media/storsimple-virtual-array-change-device-admin-password/ova-devices-blade.png)

## <a name="use-the-azure-portal-to-change-the-password"></a>Użyj witryny Azure portal, aby zmienić hasło

Wykonaj poniższe kroki, aby zmienić hasło administratora urządzenia za pośrednictwem witryny Azure portal.

#### <a name="to-change-the-device-administrator-password-via-the-azure-portal"></a>Aby zmienić hasło administratora urządzenia za pośrednictwem witryny Azure portal

1. Na stronie docelowej usługi, wybierz usługę, kliknij dwukrotnie nazwę usługi i następnie w ramach **zarządzania** kliknij **urządzeń**. Spowoduje to otwarcie **urządzeń** bloku, który zawiera listę wszystkich urządzeń StorSimple Virtual Array.

2. W **urządzeń** bloku, kliknij dwukrotnie urządzenie wymaga zmiany hasła.

3. W **ustawienia** bloku dla urządzenia, kliknij przycisk **zabezpieczeń**.

4. W **ustawienia zabezpieczeń** blok, wykonaj następujące czynności:
   
   1. Przewiń w dół do **hasło administratora urządzenia** sekcji. Podaj hasło administratora, który zawiera od 8 do 15 znaków.
   2. Potwierdź hasło.
   3. Kliknij pozycję **Zapisz** w górnej części bloku.

Hasło administratora urządzenia jest już uaktualniona. To hasło zmodyfikowane służy do uzyskania dostępu do urządzenia lokalnego.

![Blok ustawienia zabezpieczeń](./media/storsimple-virtual-array-change-device-admin-password/ova-change-device-pwd.png)

## <a name="use-the-local-web-ui-to-change-the-password"></a>Aby zmienić hasło przy użyciu lokalnego Interfejsu w przeglądarce

Wykonaj poniższe kroki, aby zmienić hasło administratora urządzenia za pomocą lokalnego Interfejsu w przeglądarce.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Aby zmienić hasło administratora urządzenia za pomocą lokalnego internetowego interfejsu użytkownika

1. W lokalnym internetowym interfejsie użytkownika, kliknij przycisk **konserwacji** > **zmiany hasła** dla Twojego urządzenia.
   
    ![Zmień password1](./media/storsimple-virtual-array-change-device-admin-password/image40.png)
2. Wprowadź **bieżące hasło**.
3. Podaj **nowe hasło**. Hasło musi mieć co najmniej 8 znaków. Musi zawierać 3 z 4 z następujących: wielkie litery, małe litery, cyfry i znaki specjalne.
   
    Należy pamiętać, hasło nie może być taka sama jak ostatnie 24 haseł.
4. Wprowadź ponownie hasło w celu potwierdzenia.
   
    ![Zmień password2](./media/storsimple-virtual-array-change-device-admin-password/image41.png)
5. W dolnej części strony kliknij **Zastosuj**. Nowego hasła są teraz stosowane. Jeśli zmiany hasła zakończy się niepowodzeniem, zostanie wyświetlony następujący błąd:
   
    ![Błąd hasła](./media/storsimple-virtual-array-change-device-admin-password/image42.png)
   
    Po pomyślnym zaktualizowaniu hasło, otrzymasz powiadomienie. Następnie służy to zmodyfikowane hasło dostępu do tego urządzenia lokalnie.


## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak [administrowania rozwiązania StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

