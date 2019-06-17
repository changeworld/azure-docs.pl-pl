---
title: Zarządzanie rekordy kontroli dostępu dla rozwiązania StorSimple Virtual Array | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób zarządzania rekordy kontroli dostępu (rekordów Acr), aby określić, która hostuje podłączeniem do woluminu w macierzy wirtualnej StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: e154bb4f-faab-4d92-a593-900c3ddc9595
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1dfc1b0e0576402624bfe62de0e206d9bd7cd1b0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64724417"
---
# <a name="use-storsimple-device-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>Menedżer urządzeń StorSimple Użyj zarządzania rekordy kontroli dostępu dla rozwiązania StorSimple Virtual Array

## <a name="overview"></a>Omówienie

Rekordy kontroli dostępu (rekordów Acr) umożliwiają określenie, która hostuje nawiązać woluminie rozwiązania StorSimple Virtual Array (znany także jako lokalne urządzenie wirtualne StorSimple). Rekordów Acr są ustawione na określonym woluminie i zawierają iSCSI nazwy kwalifikowanej (nazw IQN) z hostów. Gdy host próbuje nawiązać połączenie z woluminem, urządzenie sprawdza dostępność usługi ACR, skojarzone z tego woluminu dla nazwy IQN, a jeśli istnieje dopasowanie, połączenie zostanie nawiązane. **Rekordy kontroli dostępu** bloku w **konfiguracji** części usługi Menedżer urządzeń wyświetla wszystkie rekordy kontroli dostępu przy użyciu odpowiednich nazw IQN hostów.

![Zarządzanie rekordy kontroli dostępu](./media/storsimple-virtual-array-manage-acrs/ova-manage-acrs.png)

W tym samouczku opisano następujące typowe zadania związane z rejestru Azure container Registry:

* Pobieranie nazwy IQN
* Dodaj rekord kontroli dostępu
* Edytowanie rekordu kontroli dostępu
* Usuwanie rekordu kontroli dostępu

> [!IMPORTANT]
> 
> * Podczas przypisywania rejestru Azure container Registry do woluminu, powinien zachować ostrożność, że wolumin nie jest jednocześnie dostępna przez więcej niż jeden host nieklastrowany, ponieważ może to spowodować uszkodzenie woluminu.
> * Podczas usuwania rejestru Azure container Registry z woluminu, upewnij się, że odpowiadający mu host nie uzyskuje dostęp do woluminu, ponieważ usunięcie może spowodować zakłócenia odczytu i zapisu.


## <a name="get-the-iqn"></a>Pobieranie nazwy IQN

Wykonaj poniższe kroki, aby pobieranie nazwy IQN hosta z systemem Windows z systemem Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Dodawanie rekordu ACR

Możesz użyć **rekordy kontroli dostępu** bloku w **konfiguracji** części usługi Menedżer urządzeń StorSimple do dodawania rekordów Acr. Zazwyczaj należy skojarzyć jedną rejestru Azure container Registry z jednego woluminu.

Aby uzyskać informacji o kojarzeniu rejestru Azure container Registry z woluminem, przejdź do [Dodaj wolumin](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

> [!IMPORTANT]
> Podczas przypisywania rejestru Azure container Registry do woluminu, powinien zachować ostrożność, że wolumin nie jest jednocześnie dostępna przez więcej niż jeden host nieklastrowany, ponieważ może to spowodować uszkodzenie woluminu.


Wykonaj poniższe kroki, aby dodać rekord ACR.

#### <a name="to-add-an-acr"></a>Aby dodać rekord ACR

1. Na stronie docelowej usługi, wybierz usługę, kliknij dwukrotnie nazwę usługi, a następnie w ramach **konfiguracji** , kliknij przycisk **rekordy kontroli dostępu**.
2. W **rekordy kontroli dostępu** bloku kliknij **Dodaj**.
3. W **Dodawanie rekordu ACR** blok, wykonaj następujące czynności:
   
    1. Wypełnij pole **Nazwa** dla rekordu ACR.
    
    2. W obszarze **Nazwa inicjatora iSCSI**, podaj nazwę IQN host z systemem Windows. Aby uzyskać nazwy IQN hosta z systemu Windows Server, wykonaj następujące czynności:
   
    3. Uruchom inicjator iSCSI firmy Microsoft na hoście z systemem Windows. W oknie dialogowym właściwości inicjatora iSCSI na **konfiguracji** kartę, zaznacz i skopiuj ciąg z **Nazwa inicjatora** pola.
    Wklej ten ciąg w **IQN** pole **Dodawanie rekordu ACR** bloku.
   
    6. Kliknij przycisk **Dodaj** można dodać rekordu ACR.  
   
        ![Dodaj rekordy kontroli dostępu](./media/storsimple-virtual-array-manage-acrs/ova-add-acrs.png)
4. Tabelarycznej liście jest aktualizowany w celu odzwierciedlenia tego dodatku.

## <a name="edit-an-acr"></a>Edytuj rejestr ACR

Możesz użyć **rekordy kontroli dostępu** bloku w **konfiguracji** części usługi Menedżer urządzeń w witrynie Azure portal do edytowania rekordów Acr.

> [!NOTE]
> Nie należy modyfikować rekord ACR, który jest obecnie w użyciu. Aby edytować rekord ACR, skojarzone z woluminem, który jest aktualnie używany, należy najpierw podjąć wolumin w tryb offline.


Wykonaj poniższe kroki, aby edytować rejestr ACR.

#### <a name="to-edit-an-acr"></a>Aby edytować rejestr ACR

1. Na stronie docelowej usługi, wybierz usługę, kliknij dwukrotnie nazwę usługi, a następnie w ramach **konfiguracji** sekcji **rekordy kontroli dostępu**.
2. W **rekordy kontroli dostępu** bloku na tabelarycznej liście rekordy kontroli dostępu, kliknij dwukrotnie rejestru Azure container Registry, który chcesz zmodyfikować.
3. W **rekordy kontroli dostępu edycji** blok, wykonaj następujące czynności:
   
    1. Podaj nazwy IQN usługi ACR.
   
    2. Kliknij przycisk **Zapisz** w górnej części bloku Aby zapisać zmodyfikowanej usługi ACR. Zostanie wyświetlony następujący komunikat potwierdzenia:
   
        ![Edycja rekordów kontroli dostępu](./media/storsimple-virtual-array-manage-acrs/ova-edit-acrs.png)

## <a name="delete-an-access-control-record"></a>Usuwanie rekordu kontroli dostępu

Możesz użyć **konfiguracji** strony w witrynie Azure portal, można usunąć rekordów Acr.

> [!NOTE]
> 
> * Nie należy usuwać usługi ACR, który jest obecnie w użyciu. Aby usunąć rekord ACR, skojarzone z woluminem, który jest aktualnie używany, należy najpierw podjąć wolumin w tryb offline.
> * Podczas usuwania rejestru Azure container Registry z woluminu, upewnij się, że odpowiadający mu host nie uzyskuje dostęp do woluminu, ponieważ usunięcie może spowodować zakłócenia odczytu i zapisu.


Wykonaj poniższe kroki, aby usunąć rekord kontroli dostępu.

#### <a name="to-delete-an-access-control-record"></a>Aby usunąć rekord kontroli dostępu

1. Na stronie docelowej usługi, wybierz usługę, kliknij dwukrotnie nazwę usługi, a następnie w ramach **konfiguracji** sekcji **rekordy kontroli dostępu**.

2. W **rekordy kontroli dostępu** bloku na tabelarycznej liście rekordy kontroli dostępu, kliknij dwukrotnie rejestru Azure container Registry, który chcesz usunąć.

3. W bloku rekordy kontroli dostępu do edycji, kliknij **Usuń**.
   
    ![Usuwanie rekordów ACR](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs.png)

4. Po wyświetleniu monitu o potwierdzenie, kliknij przycisk **Usuń** aby kontynuować usuwanie. Tabelarycznej liście jest aktualizowana w celu odzwierciedlenia usunięcia.
   
   ![Komunikat ostrzegawczy](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs-warning.png)

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [Dodawanie woluminów i konfigurowanie rekordów Acr](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

