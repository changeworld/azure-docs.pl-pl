---
title: Zarządzanie rekordami kontroli dostępu w StorSimple | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób używania rekordów kontroli dostępu (ARS) do określania, które hosty mogą łączyć się z woluminem na urządzeniu StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: alkohli
ms.openlocfilehash: ade7da25d2307a382c17e7a3cbb26b601c34ef78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "64693235"
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>Zarządzanie rekordami kontroli dostępu za pomocą usługi StorSimple Manager

## <a name="overview"></a>Omówienie
Rekordy kontroli dostępu (ARS) umożliwiają określenie, które hosty mogą łączyć się z woluminem na urządzeniu StorSimple. ARS są ustawione na określony wolumin i zawierają nazwy kwalifikowane iSCSI (IQNs) hostów. Gdy host próbuje połączyć się z woluminem, urządzenie sprawdza program ACR skojarzony z tym woluminem dla nazwy IQN, a jeśli istnieje dopasowanie, nawiązywanie połączenia jest ustanawiane. Rekordy kontroli dostępu w sekcji **Konfiguracja** bloku usługi StorSimple Device Manager wyświetlają wszystkie rekordy kontroli dostępu z odpowiednimi nazwami IQN hostów.

W tym samouczku wyjaśniono następujące typowe zadania związane z programem ACR:

* Dodawanie rekordu kontroli dostępu
* Edytowanie rekordu kontroli dostępu
* Usuwanie rekordu kontroli dostępu

> [!IMPORTANT]
> * Podczas przypisywania usługi ACR do woluminu należy zadbać o to, aby wolumin nie był jednocześnie uzyskiwał dostęp do woluminu przez więcej niż jeden host nieklastrowany, ponieważ może to spowodować uszkodzenie woluminu.
> * Podczas usuwania usługi ACR z woluminu upewnij się, że odpowiedni host nie uzyskuje dostępu do woluminu, ponieważ usunięcie może spowodować zakłócenie odczytu i zapisu.

## <a name="get-the-iqn"></a>Pobierz IQN

Wykonaj następujące czynności, aby uzyskać funkcję IQN hosta systemu Windows z systemem Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]


## <a name="add-an-access-control-record"></a>Dodawanie rekordu kontroli dostępu
Użyj **konfiguracji** sekcji w StorSimple Device Manager bloku usługi, aby dodać ARS. Zazwyczaj jeden ACR jest kojarzyny z jednym woluminem.

Wykonaj następujące kroki, aby dodać acr.

#### <a name="to-add-an-acr"></a>Aby dodać acr

1. Przejdź do usługi StorSimple Device Manager, kliknij dwukrotnie nazwę usługi, a następnie w sekcji **Konfiguracja** kliknij pozycję **Rekordy kontroli dostępu**.
2. W bloku **Rekordy kontroli dostępu** kliknij przycisk + Dodaj program **ACR**.

    ![Kliknij dodaj ACR](./media/storsimple-8000-manage-acrs/createacr1.png)

3. W bloku **Dodaj ACR** wykonaj następujące czynności:

    1. Podaj nazwę usługi ACR.
    
    2. Podaj nazwę IQN hosta systemu Windows Server w obszarze **Nazwa inicjatora iSCSI (IQN)**.

    3. Kliknij **przycisk Dodaj,** aby utworzyć acr.

        ![Kliknij dodaj ACR](./media/storsimple-8000-manage-acrs/createacr2.png)

4.  Nowo dodany ACR będzie wyświetlany na tabelarycznej liście ARS.

    ![Kliknij dodaj ACR](./media/storsimple-8000-manage-acrs/createacr5.png)


## <a name="edit-an-access-control-record"></a>Edytowanie rekordu kontroli dostępu
Do edycji usług ARS należy użyć sekcji **Konfiguracja** w bloku usługi StorSimple Device Manager.

> [!NOTE]
> Zaleca się modyfikowanie tylko tych ARS, które obecnie nie są używane. Aby edytować acr skojarzone z woluminem, który jest obecnie używany, należy najpierw przełącz wolumin do trybu offline.

Wykonaj następujące kroki, aby edytować acr.

#### <a name="to-edit-an-access-control-record"></a>Aby edytować rekord kontroli dostępu
1.  Przejdź do usługi StorSimple Device Manager, kliknij dwukrotnie nazwę usługi, a następnie w sekcji **Konfiguracja** kliknij pozycję **Rekordy kontroli dostępu**.

    ![Przejdź do rekordów kontroli dostępu](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Na tabelarycznej liście rekordów kontroli dostępu kliknij i wybierz acr, który chcesz zmodyfikować.

    ![Edytowanie rekordów kontroli dostępu](./media/storsimple-8000-manage-acrs/editacr1.png)

3. W bloku **rekordu kontroli dostępu edytowania** podaj inny identyfikator IQN odpowiadający innemu hostowi.

    ![Edytowanie rekordów kontroli dostępu](./media/storsimple-8000-manage-acrs/editacr2.png)

4. Kliknij przycisk **Zapisz**. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**. 

    ![Edytowanie rekordów kontroli dostępu](./media/storsimple-8000-manage-acrs/editacr3.png)

5. Użytkownik zostanie powiadomiony o aktualizacji usługi ACR. Lista tabelaryczny jest również aktualizowana w celu odzwierciedlenia zmiany.

   
## <a name="delete-an-access-control-record"></a>Usuwanie rekordu kontroli dostępu
Użyj **konfiguracji** sekcji w StorSimple Device Manager bloku usługi, aby usunąć ARs.

> [!NOTE]
> Można usunąć tylko te ARS, które obecnie nie są używane. Aby usunąć acr skojarzone z woluminem, który jest obecnie używany, należy najpierw przełącz wolumin do trybu offline.

Wykonaj następujące kroki, aby usunąć rekord kontroli dostępu.

#### <a name="to-delete-an-access-control-record"></a>Aby usunąć rekord kontroli dostępu
1.  Przejdź do usługi StorSimple Device Manager, kliknij dwukrotnie nazwę usługi, a następnie w sekcji **Konfiguracja** kliknij pozycję **Rekordy kontroli dostępu**.

    ![Przejdź do rekordów kontroli dostępu](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Na tabelarycznej liście rekordów kontroli dostępu kliknij i wybierz acr, który chcesz usunąć.

    ![Przejdź do rekordów kontroli dostępu](./media/storsimple-8000-manage-acrs/deleteacr1.png)

3. Kliknij prawym przyciskiem myszy, aby wywołać menu kontekstowe i wybierz polecenie **Usuń**.

    ![Przejdź do rekordów kontroli dostępu](./media/storsimple-8000-manage-acrs/deleteacr2.png)

4. Po wyświetleniu monitu o potwierdzenie przejrzyj informacje, a następnie kliknij przycisk **Usuń**.

    ![Przejdź do rekordów kontroli dostępu](./media/storsimple-8000-manage-acrs/deleteacr3.png)

5. Użytkownik zostanie powiadomiony po zakończeniu usuwania. Lista tabelaryczny zostanie zaktualizowana w celu odzwierciedlenia usunięcia.

    ![Przejdź do rekordów kontroli dostępu](./media/storsimple-8000-manage-acrs/deleteacr5.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [zarządzaniu woluminami StorSimple](storsimple-8000-manage-volumes-u2.md).
* Dowiedz się więcej o [administrowaniu urządzeniem StorSimple za pomocą usługi StorSimple Manager.](storsimple-8000-manager-service-administration.md)

