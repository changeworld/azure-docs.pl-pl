---
title: Zarządzanie rekordami kontroli dostępu dla tablicy wirtualnego StorSimple | Dokumentacja firmy Microsoft
description: Opisuje sposób zarządzania rekordami kontroli dostępu (ACRs) do określenia, które hosty mogą łączyć się woluminu w macierzy wirtualnych StorSimple z.
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
ms.openlocfilehash: 2ce65aa4efba735305208f7a6d761bc2814d1b8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875942"
---
# <a name="use-storsimple-device-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>Użyj Menedżera urządzeń StorSimple do zarządzania rekordy kontroli dostępu dla tablicy wirtualnego StorSimple

## <a name="overview"></a>Omówienie

Rekordy kontroli dostępu (ACRs) pozwala na określenie hosty, które mogą nawiązywać połączenia do woluminu w macierzy wirtualnego StorSimple (znanej także jako lokalnej urządzenia wirtualnego StorSimple). ACRs to określonego woluminu i zawierać nazwy kwalifikowanej iSCSI (nazw IQN) z hostów. Podczas nawiązywania połączenia z woluminu hosta, urządzenie sprawdza ACR skojarzone z tym woluminie dla nazwy IQN, i jeśli istnieje dopasowanie, połączenie zostanie nawiązane. **Rekordy kontroli dostępu** bloku w **konfiguracji** sekcji usługi Menedżer urządzeń wyświetla wszystkie rekordy kontroli dostępu z odpowiednich nazw IQN hostów.

![Zarządzanie rekordami kontroli dostępu](./media/storsimple-virtual-array-manage-acrs/ova-manage-acrs.png)

W tym samouczku opisano następujące typowe zadania związane z ACR:

* Pobieranie nazwy IQN
* Dodaj rekord kontroli dostępu
* Edytuj rekord kontroli dostępu
* Usuń rekord kontroli dostępu

> [!IMPORTANT]
> 
> * Podczas przypisywania ACR do woluminu, zajmie się, że wolumin nie jest jednocześnie dostępna przez więcej niż jednego hosta z systemem innym niż klastrowane, ponieważ może to spowodować uszkodzenie woluminu.
> * Podczas usuwania ACR z woluminu, upewnij się, że odpowiedniego hosta nie korzysta wolumin usunięcia może powodować zakłócenia odczytu i zapisu.


## <a name="get-the-iqn"></a>Pobieranie nazwy IQN

Wykonaj poniższe kroki, aby pobieranie nazwy IQN hosta z systemem Windows z systemem Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Dodaj ACR

Możesz użyć **rekordy kontroli dostępu** bloku w **konfiguracji** sekcji usługi Menedżer StorSimple urządzenia można dodać ACRs. Zazwyczaj należy skojarzyć jedną ACR z jednego woluminu.

Aby uzyskać informacje o sposobie kojarzenia ACR z woluminu, przejdź do [Dodaj wolumin](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

> [!IMPORTANT]
> Podczas przypisywania ACR do woluminu, zajmie się, że wolumin nie jest jednocześnie dostępna przez więcej niż jednego hosta z systemem innym niż klastrowane, ponieważ może to spowodować uszkodzenie woluminu.


Wykonaj poniższe kroki, aby dodać ACR.

#### <a name="to-add-an-acr"></a>Aby dodać ACR

1. Na stronie docelowej usługi, wybierz usługę, kliknij dwukrotnie nazwę usługi, a następnie w **konfiguracji** kliknij **rekordy kontroli dostępu**.
2. W **rekordy kontroli dostępu** bloku, kliknij przycisk **Dodaj**.
3. W **dodać ACR** blok, wykonaj następujące czynności:
   
    1. Wypełnij pole **Nazwa** dla rekordu ACR.
    
    2. W obszarze **Nazwa inicjatora iSCSI**, podaj nazwę IQN hosta z systemem Windows. Aby uzyskać nazwy IQN hosta z systemu Windows Server, wykonaj następujące czynności:
   
    3. Uruchom inicjator iSCSI firmy Microsoft na hoście z systemem Windows. W oknie właściwości inicjatora iSCSI na **konfiguracji** , wybierz i skopiuj ciąg z **Nazwa inicjatora** pola.
    Wklej ten ciąg w **IQN** w **dodać ACR** bloku.
   
    6. Kliknij przycisk **Dodaj** można dodać ACR.  
   
        ![Dodaj rekordy kontroli dostępu](./media/storsimple-virtual-array-manage-acrs/ova-add-acrs.png)
4. Tabelarycznej listę jest aktualizowany w celu odzwierciedlenia tego dodatku.

## <a name="edit-an-acr"></a>Edytuj ACR

Możesz użyć **rekordy kontroli dostępu** bloku w **konfiguracji** sekcji usługi Menedżera urządzeń w portalu Azure, aby edytować ACRs.

> [!NOTE]
> Nie należy modyfikować ACR, który jest aktualnie używany. Aby edytować ACR skojarzony z woluminem, który jest aktualnie używana, należy najpierw wziąć woluminu w trybie offline.


Wykonaj poniższe kroki, aby edytować ACR.

#### <a name="to-edit-an-acr"></a>Aby edytować ACR

1. Na stronie docelowej usługi, wybierz usługę, kliknij dwukrotnie nazwę usługi, a następnie w **konfiguracji** sekcji, **rekordy kontroli dostępu**.
2. W **rekordy kontroli dostępu** bloku z Tabelaryczny spis rekordy kontroli dostępu, kliknij dwukrotnie ACR, który chcesz zmodyfikować.
3. W **rekordy kontroli dostępu do edycji** blok, wykonaj następujące czynności:
   
    1. Należy podać nazwy IQN ACR.
   
    2. Kliknij przycisk **zapisać** w górnej części bloku, aby zapisać zmodyfikowanych ACR. Zostanie wyświetlony następujący komunikat potwierdzenia:
   
        ![Edytuj rekordy kontroli dostępu](./media/storsimple-virtual-array-manage-acrs/ova-edit-acrs.png)

## <a name="delete-an-access-control-record"></a>Usuń rekord kontroli dostępu

Możesz użyć **konfiguracji** strony w portalu Azure, aby usunąć ACRs.

> [!NOTE]
> 
> * Nie należy usuwać ACR, który jest aktualnie używany. Aby usunąć ACR skojarzony z woluminem, który jest aktualnie używana, należy najpierw wziąć woluminu w trybie offline.
> * Podczas usuwania ACR z woluminu, upewnij się, że odpowiedniego hosta nie korzysta wolumin usunięcia może powodować zakłócenia odczytu i zapisu.


Wykonaj poniższe kroki, aby usunąć rekord kontroli dostępu.

#### <a name="to-delete-an-access-control-record"></a>Aby usunąć rekord kontroli dostępu

1. Na stronie docelowej usługi, wybierz usługę, kliknij dwukrotnie nazwę usługi, a następnie w **konfiguracji** sekcji, **rekordy kontroli dostępu**.

2. W **rekordy kontroli dostępu** bloku z Tabelaryczny spis rekordy kontroli dostępu, kliknij dwukrotnie ACR, który chcesz usunąć.

3. W bloku rekordy kontroli dostępu do edycji, kliknij **usunąć**.
   
    ![Usuń ACRS](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs.png)

4. Po wyświetleniu monitu o potwierdzenie, kliknij przycisk **usunąć** aby kontynuować usuwanie. Tabelarycznej listę jest aktualizowany w celu odzwierciedlenia usunięcia.
   
   ![Komunikat ostrzegawczy](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs-warning.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [Dodawanie woluminów i konfigurowanie ACRs](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

