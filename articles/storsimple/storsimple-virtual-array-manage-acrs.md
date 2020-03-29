---
title: Zarządzanie rekordami kontroli dostępu dla tablicy wirtualnej StorSimple | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób zarządzania rekordami kontroli dostępu (ARS), aby określić, które hosty mogą łączyć się z woluminem w tablicy wirtualnej StorSimple.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "64724417"
---
# <a name="use-storsimple-device-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>Zarządzanie rekordami kontroli dostępu dla tablicy wirtualnej StorSimple za pomocą Menedżera urządzeń StorSimple

## <a name="overview"></a>Omówienie

Rekordy kontroli dostępu (ARS) umożliwiają określenie, które hosty mogą łączyć się z woluminem w tablicy wirtualnej StorSimple (znanej również jako lokalne urządzenie wirtualne StorSimple). ARS są ustawione na określony wolumin i zawierają nazwy kwalifikowane iSCSI (IQNs) hostów. Gdy host próbuje połączyć się z woluminem, urządzenie sprawdza program ACR skojarzony z tym woluminem dla nazwy IQN, a jeśli istnieje dopasowanie, nawiązywanie połączenia jest ustanawiane. Blok **Rekordy kontroli dostępu** w sekcji **Konfiguracja** usługi Menedżer urządzeń wyświetla wszystkie rekordy kontroli dostępu z odpowiednimi nazwami IQNs hostów.

![Zarządzanie rekordami kontroli dostępu](./media/storsimple-virtual-array-manage-acrs/ova-manage-acrs.png)

W tym samouczku wyjaśniono następujące typowe zadania związane z programem ACR:

* Pobierz IQN
* Dodawanie rekordu kontroli dostępu
* Edytowanie rekordu kontroli dostępu
* Usuwanie rekordu kontroli dostępu

> [!IMPORTANT]
> 
> * Podczas przypisywania usługi ACR do woluminu należy zadbać o to, aby wolumin nie był jednocześnie uzyskiwał dostęp do woluminu przez więcej niż jeden host nieklastrowany, ponieważ może to spowodować uszkodzenie woluminu.
> * Podczas usuwania usługi ACR z woluminu upewnij się, że odpowiedni host nie uzyskuje dostępu do woluminu, ponieważ usunięcie może spowodować zakłócenie odczytu i zapisu.


## <a name="get-the-iqn"></a>Pobierz IQN

Wykonaj następujące czynności, aby uzyskać funkcję IQN hosta systemu Windows z systemem Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Dodawanie acr

Bloku **rekordów kontroli dostępu** w sekcji **Konfiguracja** usługi StorSimple Device Manager można dodawać rekordy ARS. Zazwyczaj jeden ACR jest kojarzyny z jednym woluminem.

Aby uzyskać informacje dotyczące kojarzenia usługi ACR z woluminem, przejdź do [dodawania woluminu](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

> [!IMPORTANT]
> Podczas przypisywania usługi ACR do woluminu należy zadbać o to, aby wolumin nie był jednocześnie uzyskiwał dostęp do woluminu przez więcej niż jeden host nieklastrowany, ponieważ może to spowodować uszkodzenie woluminu.


Wykonaj następujące kroki, aby dodać acr.

#### <a name="to-add-an-acr"></a>Aby dodać acr

1. Na stronie docelowej usługi wybierz usługę, kliknij dwukrotnie nazwę usługi, a następnie w sekcji **Konfiguracja** kliknij pozycję **Rekordy kontroli dostępu**.
2. W bloku **Rekordy kontroli dostępu** kliknij pozycję **Dodaj**.
3. W bloku **Dodaj ACR** wykonaj następujące czynności:
   
    1. Wypełnij pole **Nazwa** dla rekordu ACR.
    
    2. W obszarze **Nazwa inicjatora iSCSI**podaj nazwę IQN hosta systemu Windows. Aby uzyskać IQN hosta systemu Windows Server, wykonaj następujące czynności:
   
    3. Uruchom inicjator iSCSI firmy Microsoft na hoście z systemem Windows. W oknie Właściwości inicjatora iSCSI na karcie **Konfiguracja** zaznacz i skopiuj ciąg z pola **Nazwa inicjatora**.
    Wklej ten ciąg w polu **IQN** w bloku **Dodaj ACR.**
   
    6. Kliknij **przycisk Dodaj,** aby dodać acr.  
   
        ![Dodawanie rekordów kontroli dostępu](./media/storsimple-virtual-array-manage-acrs/ova-add-acrs.png)
4. Lista tabelaryczny zostanie zaktualizowana w celu odzwierciedlenia tego dodatku.

## <a name="edit-an-acr"></a>Edytowanie acr

Bloku **rekordów kontroli dostępu** w sekcji **Konfiguracja** usługi Menedżer urządzeń w witrynie Azure portal można edytować rekordy obsługi klienta.

> [!NOTE]
> Nie należy modyfikować ACR, który jest obecnie w użyciu. Aby edytować acr skojarzone z woluminem, który jest obecnie używany, należy najpierw przełącz wolumin do trybu offline.


Wykonaj następujące kroki, aby edytować acr.

#### <a name="to-edit-an-acr"></a>Aby edytować acr

1. Na stronie docelowej usługi wybierz usługę, kliknij dwukrotnie nazwę usługi, a następnie w sekcji **Konfiguracja** **rekordy kontroli dostępu**.
2. W **bloku Rekordy kontroli dostępu** z tabelarycznej listy rekordów kontroli dostępu kliknij dwukrotnie funkcję ACR, którą chcesz zmodyfikować.
3. W bloku **Edytuj rekordy kontroli dostępu** wykonaj następujące czynności:
   
    1. Podaj IQN dla ACR.
   
    2. Kliknij **przycisk Zapisz** w górnej części bloku, aby zapisać zmodyfikowany program ACR. Zostanie wyświetlony następujący komunikat potwierdzający:
   
        ![Edytowanie rekordów kontroli dostępu](./media/storsimple-virtual-array-manage-acrs/ova-edit-acrs.png)

## <a name="delete-an-access-control-record"></a>Usuwanie rekordu kontroli dostępu

Strona **Konfiguracja** w witrynie Azure portal służy do usuwania obiektów ARS.

> [!NOTE]
> 
> * Nie należy usuwać usługi ACR, która jest aktualnie używana. Aby usunąć acr skojarzone z woluminem, który jest obecnie używany, należy najpierw przełącz wolumin do trybu offline.
> * Podczas usuwania usługi ACR z woluminu upewnij się, że odpowiedni host nie uzyskuje dostępu do woluminu, ponieważ usunięcie może spowodować zakłócenie odczytu i zapisu.


Wykonaj następujące kroki, aby usunąć rekord kontroli dostępu.

#### <a name="to-delete-an-access-control-record"></a>Aby usunąć rekord kontroli dostępu

1. Na stronie docelowej usługi wybierz usługę, kliknij dwukrotnie nazwę usługi, a następnie w sekcji **Konfiguracja** **rekordy kontroli dostępu**.

2. W bloku **Rekordy kontroli dostępu** z tabelarycznej listy rekordów kontroli dostępu kliknij dwukrotnie program ACR, który chcesz usunąć.

3. W bloku Edytuj rekordy kontroli dostępu kliknij pozycję **Usuń**.
   
    ![Usuwanie ACRS](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs.png)

4. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Usuń,** aby kontynuować usuwanie. Lista tabelaryczny zostanie zaktualizowana w celu odzwierciedlenia usunięcia.
   
   ![Komunikat ostrzegawczy](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs-warning.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [dodawaniu woluminów i konfigurowaniu ARS](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

