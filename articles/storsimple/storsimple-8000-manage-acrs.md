---
title: Zarządzanie rekordy kontroli dostępu w urządzeniu StorSimple | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak używać rekordów kontroli dostępu (rekordów Acr) do określenia hosta, którego można połączyć do woluminu na urządzeniu StorSimple.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64693235"
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>Usługa StorSimple Manager umożliwia zarządzanie rekordy kontroli dostępu

## <a name="overview"></a>Omówienie
Rekordy kontroli dostępu (rekordów Acr) umożliwiają określenie, która hostuje nawiązać woluminu na urządzeniu StorSimple. Rekordów Acr są ustawione na określonym woluminie i zawierają iSCSI nazwy kwalifikowanej (nazw IQN) z hostów. Gdy host próbuje nawiązać połączenie z woluminem, urządzenie sprawdza dostępność usługi ACR skojarzone z tego woluminu dla nazwy IQN, a jeśli istnieje dopasowanie, połączenie zostanie nawiązane. Kontrola dostępu rejestruje w **konfiguracji** sekcji bloku usługi Menedżer urządzeń StorSimple wyświetlić wszystkie rekordy kontroli dostępu przy użyciu odpowiednich nazw IQN hostów.

W tym samouczku opisano następujące typowe zadania związane z rejestru Azure container Registry:

* Dodaj rekord kontroli dostępu
* Edytowanie rekordu kontroli dostępu
* Usuwanie rekordu kontroli dostępu

> [!IMPORTANT]
> * Podczas przypisywania rejestru Azure container Registry do woluminu, powinien zachować ostrożność, że wolumin nie jest jednocześnie dostępna przez więcej niż jeden host nieklastrowany, ponieważ może to spowodować uszkodzenie woluminu.
> * Podczas usuwania rejestru Azure container Registry z woluminu, upewnij się, że odpowiadający mu host nie uzyskuje dostęp do woluminu, ponieważ usunięcie może spowodować zakłócenia odczytu i zapisu.

## <a name="get-the-iqn"></a>Pobieranie nazwy IQN

Wykonaj poniższe kroki, aby pobieranie nazwy IQN hosta z systemem Windows z systemem Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]


## <a name="add-an-access-control-record"></a>Dodaj rekord kontroli dostępu
Możesz użyć **konfiguracji** sekcji w bloku usługi Menedżer urządzeń StorSimple do dodawania rekordów Acr. Zazwyczaj jeden ACR zostanie skojarzony z jednego woluminu.

Wykonaj poniższe kroki, aby dodać rekord ACR.

#### <a name="to-add-an-acr"></a>Aby dodać rekord ACR

1. Przejdź do usługi Menedżer urządzeń StorSimple, kliknij dwukrotnie nazwę usługi, a następnie w ramach **konfiguracji** , kliknij przycisk **rekordy kontroli dostępu**.
2. W **rekordy kontroli dostępu** bloku kliknij **+ Dodaj ACR**.

    ![Kliknij przycisk Dodaj, rejestru Azure container Registry](./media/storsimple-8000-manage-acrs/createacr1.png)

3. W **Dodawanie rekordu ACR** blok, wykonaj następujące czynności:

    1. Podaj nazwę dla rekordu ACR.
    
    2. Podaj nazwę IQN hosta Windows Server w ramach **iSCSI Nazwa inicjatora (IQN)** .

    3. Kliknij przycisk **Dodaj** do tworzenia usługi ACR.

        ![Kliknij przycisk Dodaj, rejestru Azure container Registry](./media/storsimple-8000-manage-acrs/createacr2.png)

4.  Nowo dodanego rekordu ACR zostanie wyświetlona na tabelarycznej liście rekordów Acr.

    ![Kliknij przycisk Dodaj, rejestru Azure container Registry](./media/storsimple-8000-manage-acrs/createacr5.png)


## <a name="edit-an-access-control-record"></a>Edytowanie rekordu kontroli dostępu
Możesz użyć **konfiguracji** sekcji w bloku usługi Menedżer urządzeń StorSimple do edytowania rekordów Acr.

> [!NOTE]
> Zaleca się, że modyfikować tylko tych rekordów Acr, które nie są obecnie w użyciu. Aby edytować rekord ACR, skojarzone z woluminem, który jest aktualnie używany, należy najpierw wykonać wolumin w tryb offline.

Wykonaj poniższe kroki, aby edytować rejestr ACR.

#### <a name="to-edit-an-access-control-record"></a>Aby edytować rekord kontroli dostępu
1.  Przejdź do usługi Menedżer urządzeń StorSimple, kliknij dwukrotnie nazwę usługi, a następnie w ramach **konfiguracji** , kliknij przycisk **rekordy kontroli dostępu**.

    ![Przejdź do rekordów kontroli dostępu](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Na tabelarycznej liście rekordy kontroli dostępu, kliknij przycisk, a następnie wybierz usługi ACR, który chcesz zmodyfikować.

    ![Edycja rekordów kontroli dostępu](./media/storsimple-8000-manage-acrs/editacr1.png)

3. W **rekordu kontroli dostępu edycji** bloku, podaj inną nazwę IQN, odpowiadający innego hosta.

    ![Edycja rekordów kontroli dostępu](./media/storsimple-8000-manage-acrs/editacr2.png)

4. Kliknij pozycję **Zapisz**. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**. 

    ![Edycja rekordów kontroli dostępu](./media/storsimple-8000-manage-acrs/editacr3.png)

5. Otrzymasz powiadomienie po zaktualizowaniu usługi ACR. Tabelarycznej listę również aktualizacji w celu odzwierciedlenia zmiany.

   
## <a name="delete-an-access-control-record"></a>Usuwanie rekordu kontroli dostępu
Możesz użyć **konfiguracji** sekcji w bloku usługi Menedżer urządzeń StorSimple można usunąć rekordów Acr.

> [!NOTE]
> Można usunąć tylko tych rekordów Acr, które nie są obecnie w użyciu. Aby usunąć rekord ACR, skojarzone z woluminem, który jest aktualnie używany, należy najpierw wykonać wolumin w tryb offline.

Wykonaj poniższe kroki, aby usunąć rekord kontroli dostępu.

#### <a name="to-delete-an-access-control-record"></a>Aby usunąć rekord kontroli dostępu
1.  Przejdź do usługi Menedżer urządzeń StorSimple, kliknij dwukrotnie nazwę usługi, a następnie w ramach **konfiguracji** , kliknij przycisk **rekordy kontroli dostępu**.

    ![Przejdź do rekordów kontroli dostępu](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Na tabelarycznej liście rekordy kontroli dostępu, kliknij przycisk, a następnie wybierz usługi ACR, który chcesz usunąć.

    ![Przejdź do rekordów kontroli dostępu](./media/storsimple-8000-manage-acrs/deleteacr1.png)

3. Kliknij prawym przyciskiem myszy, aby wywołać menu kontekstowe i wybrać **Usuń**.

    ![Przejdź do rekordów kontroli dostępu](./media/storsimple-8000-manage-acrs/deleteacr2.png)

4. Po wyświetleniu monitu o potwierdzenie, zapoznaj się z informacjami, a następnie kliknij przycisk **Usuń**.

    ![Przejdź do rekordów kontroli dostępu](./media/storsimple-8000-manage-acrs/deleteacr3.png)

5. Otrzymasz powiadomienie po zakończeniu usuwania. Tabelarycznej liście jest aktualizowana w celu odzwierciedlenia usunięcia.

    ![Przejdź do rekordów kontroli dostępu](./media/storsimple-8000-manage-acrs/deleteacr5.png)

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [Zarządzanie woluminów StorSimple](storsimple-8000-manage-volumes-u2.md).
* Dowiedz się więcej o [do administrowania urządzeniem StorSimple przy użyciu usługi StorSimple Manager](storsimple-8000-manager-service-administration.md).

