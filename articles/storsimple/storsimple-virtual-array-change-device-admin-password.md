---
title: Zmień hasło administratora urządzenia StorSimple Virtual Array | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak zmienić hasło administratora urządzenia za pomocą portalu Azure lub interfejsu użytkownika sieci Web Tablicy wirtualnej StorSimple.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60580414"
---
# <a name="change-the-storsimple-virtual-array-device-administrator-password-via-storsimple-device-manager"></a>Zmienianie hasła administratora urządzenia StorSimple Virtual Array za pośrednictwem Menedżera urządzeń StorSimple

## <a name="overview"></a>Omówienie

Podczas uzyskiwania dostępu do tablicy wirtualnej StorSimple za pomocą interfejsu programu Windows PowerShell wymagane jest wprowadzenie hasła administratora urządzenia. Gdy urządzenie StorSimple jest po raz pierwszy aprowizowane i uruchomione, domyślnym hasłem jest *Password1*. Ze względów bezpieczeństwa danych domyślne hasło wygasa przy pierwszym loguch i konieczności zmiany tego hasła.

Można również użyć lokalnego interfejsu użytkownika sieci web lub witryny Azure portal, aby zmienić hasło administratora urządzenia w dowolnym momencie po wdrożeniu urządzenia w środowisku produkcyjnym. Każda z tych procedur jest opisana w tym artykule.

 ![ostrze urządzeń](./media/storsimple-virtual-array-change-device-admin-password/ova-devices-blade.png)

## <a name="use-the-azure-portal-to-change-the-password"></a>Zmień hasło za pomocą portalu Azure

Wykonaj następujące kroki, aby zmienić hasło administratora urządzenia za pośrednictwem witryny Azure portal.

#### <a name="to-change-the-device-administrator-password-via-the-azure-portal"></a>Aby zmienić hasło administratora urządzenia za pośrednictwem witryny Azure portal

1. Na stronie docelowej usługi wybierz usługę, kliknij dwukrotnie nazwę usługi, a następnie w sekcji **Zarządzanie** kliknij pozycję **Urządzenia**. Spowoduje to otwarcie **urządzenia** bloku, który zawiera listę wszystkich urządzeń StorSimple Virtual Array.

2. W **bloku Urządzenia** kliknij dwukrotnie urządzenie, które wymaga zmiany hasła.

3. W bloku **Ustawienia** urządzenia kliknij pozycję **Zabezpieczenia**.

4. W bloku **Ustawienia zabezpieczeń** wykonaj następujące czynności:
   
   1. Przewiń w dół do sekcji **Hasło administratora urządzenia.** Podaj hasło administratora zawierające od 8 do 15 znaków.
   2. Potwierdź hasło.
   3. Kliknij **pozycję Zapisz** w górnej części bloku.

Hasło administratora urządzenia jest teraz aktualizowane. Możesz użyć tego zmodyfikowanego hasła, aby uzyskać dostęp do urządzenia lokalnie.

![Blok ustawień zabezpieczeń](./media/storsimple-virtual-array-change-device-admin-password/ova-change-device-pwd.png)

## <a name="use-the-local-web-ui-to-change-the-password"></a>Zmiana hasła za pomocą lokalnego interfejsu użytkownika sieci Web

Wykonaj następujące kroki, aby zmienić hasło administratora urządzenia za pośrednictwem lokalnego interfejsu użytkownika sieci Web.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Aby zmienić hasło administratora urządzenia za pośrednictwem lokalnego interfejsu użytkownika sieci Web

1. W lokalnym interfejsie użytkownika sieci Web kliknij pozycję**Zmiana hasła** **konserwacji** > urządzenia.
   
    ![zmienianie hasła1](./media/storsimple-virtual-array-change-device-admin-password/image40.png)
2. Wprowadź **bieżące hasło**.
3. Podaj **nowe hasło**. Hasło musi mieć co najmniej 8 znaków. Musi zawierać 3 z 4 z następujących znaków: wielkie, małe, numeryczne i znaki specjalne.
   
    Pamiętaj, że hasło nie może być takie samo jak ostatnie 24 hasła.
4. Wprowadź ponownie hasło w celu potwierdzenia.
   
    ![zmienianie hasła2](./media/storsimple-virtual-array-change-device-admin-password/image41.png)
5. U dołu strony kliknij pozycję **Zastosuj**. Nowe hasło jest teraz stosowane. Jeśli zmiana hasła nie powiedzie się, zostanie wyświetlony następujący błąd:
   
    ![błąd hasła](./media/storsimple-virtual-array-change-device-admin-password/image42.png)
   
    Po pomyślnym zaktualizowaniu hasła użytkownik zostanie o tym powiadomiony. Następnie można użyć tego zmodyfikowanego hasła, aby uzyskać dostęp do urządzenia lokalnie.


## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [administrować tablicą wirtualną StorSimple](storsimple-ova-web-ui-admin.md).

