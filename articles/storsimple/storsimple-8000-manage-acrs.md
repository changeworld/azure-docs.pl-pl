---
title: Zarządzanie rekordami kontroli dostępu w StorSimple | Dokumentacja firmy Microsoft
description: Informacje dotyczące używania rekordy kontroli dostępu (ACRs) do określenia, których hostów można połączyć do woluminu w urządzeniu StorSimple.
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
ms.openlocfilehash: 9173e34f889ce1c082b20bb382cb6ca9a03dd797
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874906"
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>Zarządzanie rekordami kontroli dostępu przy użyciu usługi Menedżer StorSimple

## <a name="overview"></a>Omówienie
Rekordy kontroli dostępu (ACRs) pozwala na określenie, których hostów można połączyć się wolumin w urządzeniu StorSimple. ACRs to określonego woluminu i zawierać nazwy kwalifikowanej iSCSI (nazw IQN) z hostów. Gdy host próbuje nawiązać połączenia z woluminu, urządzenie sprawdza ACR skojarzona z tego woluminu dla nazwy IQN, jeśli istnieje dopasowanie, połączenie zostanie nawiązane. Rejestruje kontroli dostępu w **konfiguracji** sekcji z bloku usługi Menedżer StorSimple urządzenia wyświetlić wszystkie rekordy kontroli dostępu z odpowiednich nazw IQN hostów.

W tym samouczku opisano następujące typowe zadania związane z ACR:

* Dodaj rekord kontroli dostępu
* Edytuj rekord kontroli dostępu
* Usuń rekord kontroli dostępu

> [!IMPORTANT]
> * Podczas przypisywania ACR do woluminu, zajmie się, że wolumin nie jest jednocześnie dostępna przez więcej niż jednego hosta z systemem innym niż klastrowane, ponieważ może to spowodować uszkodzenie woluminu.
> * Podczas usuwania ACR z woluminu, upewnij się, że odpowiedniego hosta nie korzysta wolumin usunięcia może powodować zakłócenia odczytu i zapisu.

## <a name="get-the-iqn"></a>Pobieranie nazwy IQN

Wykonaj poniższe kroki, aby pobieranie nazwy IQN hosta z systemem Windows z systemem Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]


## <a name="add-an-access-control-record"></a>Dodaj rekord kontroli dostępu
Możesz użyć **konfiguracji** części bloku usługi Menedżera urządzeń StorSimple, aby dodać ACRs. Zazwyczaj co ACR zostanie skojarzony z jednego woluminu.

Wykonaj poniższe kroki, aby dodać ACR.

#### <a name="to-add-an-acr"></a>Aby dodać ACR

1. Przejdź do usługi Menedżer urządzeń StorSimple, kliknij dwukrotnie nazwę usługi, a następnie w **konfiguracji** kliknij **rekordy kontroli dostępu**.
2. W **rekordy kontroli dostępu** bloku, kliknij przycisk **+ Dodaj ACR**.

    ![Kliknij przycisk Dodaj ACR](./media/storsimple-8000-manage-acrs/createacr1.png)

3. W **dodać ACR** blok, wykonaj następujące czynności:

    1. Podaj nazwę dla rekordu ACR.
    
    2. Podaj nazwę IQN hosta z systemem Windows Server w obszarze **iSCSI Nazwa inicjatora (IQN)**.

    3. Kliknij przycisk **Dodaj** utworzyć ACR.

        ![Kliknij przycisk Dodaj ACR](./media/storsimple-8000-manage-acrs/createacr2.png)

4.  Nowo dodany ACR będzie wyświetlana w Tabelaryczny spis ACRs.

    ![Kliknij przycisk Dodaj ACR](./media/storsimple-8000-manage-acrs/createacr5.png)


## <a name="edit-an-access-control-record"></a>Edytuj rekord kontroli dostępu
Możesz użyć **konfiguracji** części bloku usługi Menedżera urządzeń StorSimple, aby edytować ACRs.

> [!NOTE]
> Zalecane jest zmodyfikowanie tych ACRs, które nie są obecnie w użyciu. Aby edytować ACR skojarzony z woluminem, który jest obecnie używany, musisz najpierw wykonać woluminu w trybie offline.

Wykonaj poniższe kroki, aby edytować ACR.

#### <a name="to-edit-an-access-control-record"></a>Aby edytować rekord kontroli dostępu
1.  Przejdź do usługi Menedżer urządzeń StorSimple, kliknij dwukrotnie nazwę usługi, a następnie w **konfiguracji** kliknij **rekordy kontroli dostępu**.

    ![Przejdź do rekordy kontroli dostępu](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Tabelaryczne liście rekordy kontroli dostępu, kliknij i zaznacz ACR, który chcesz zmodyfikować.

    ![Edytuj rekordy kontroli dostępu](./media/storsimple-8000-manage-acrs/editacr1.png)

3. W **rekord kontroli dostępu edycji** bloku, podaj inną IQN, odpowiadającego do innego hosta.

    ![Edytuj rekordy kontroli dostępu](./media/storsimple-8000-manage-acrs/editacr2.png)

4. Kliknij pozycję **Zapisz**. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**. 

    ![Edytuj rekordy kontroli dostępu](./media/storsimple-8000-manage-acrs/editacr3.png)

5. Użytkownik jest powiadamiany o zaktualizowaniu ACR. Tabelarycznej listę również aktualizuje, aby odzwierciedlić zmiany.

   
## <a name="delete-an-access-control-record"></a>Usuń rekord kontroli dostępu
Możesz użyć **konfiguracji** części bloku usługi Menedżera urządzeń StorSimple, aby usunąć ACRs.

> [!NOTE]
> Można usunąć tylko ACRs, które nie są obecnie w użyciu. Aby usunąć ACR skojarzony z woluminem, który jest obecnie używany, musisz najpierw wykonać woluminu w trybie offline.

Wykonaj poniższe kroki, aby usunąć rekord kontroli dostępu.

#### <a name="to-delete-an-access-control-record"></a>Aby usunąć rekord kontroli dostępu
1.  Przejdź do usługi Menedżer urządzeń StorSimple, kliknij dwukrotnie nazwę usługi, a następnie w **konfiguracji** kliknij **rekordy kontroli dostępu**.

    ![Przejdź do rekordy kontroli dostępu](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Tabelaryczne liście rekordy kontroli dostępu, kliknij i zaznacz ACR, który chcesz usunąć.

    ![Przejdź do rekordy kontroli dostępu](./media/storsimple-8000-manage-acrs/deleteacr1.png)

3. Kliknij prawym przyciskiem myszy, aby wywołać menu kontekstowego, a następnie wybierz **usunąć**.

    ![Przejdź do rekordy kontroli dostępu](./media/storsimple-8000-manage-acrs/deleteacr2.png)

4. Po wyświetleniu monitu o potwierdzenie, przejrzyj informacje, a następnie kliknij przycisk **usunąć**.

    ![Przejdź do rekordy kontroli dostępu](./media/storsimple-8000-manage-acrs/deleteacr3.png)

5. Użytkownik jest powiadamiany o zakończeniu usuwania. Tabelarycznej listę jest aktualizowany w celu odzwierciedlenia usunięcia.

    ![Przejdź do rekordy kontroli dostępu](./media/storsimple-8000-manage-acrs/deleteacr5.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [zarządzania woluminami StorSimple](storsimple-8000-manage-volumes-u2.md).
* Dowiedz się więcej o [przy użyciu usługi Menedżer StorSimple do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

