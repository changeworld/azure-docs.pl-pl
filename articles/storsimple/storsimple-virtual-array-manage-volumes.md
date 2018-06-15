---
title: Zarządzanie woluminami w macierzy wirtualnego StorSimple | Dokumentacja firmy Microsoft
description: Zawiera opis Menedżera urządzeń StorSimple i wyjaśniono, jak przy jego użyciu zarządzać woluminach macierzy wirtualne StorSimple.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: caa6a26b-b7ba-4a05-b092-1a79450225cf
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: a507bf1866952cb79fa6334fed80c88cd207cd0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876054"
---
# <a name="use-storsimple-device-manager-service-to-manage-volumes-on-the-storsimple-virtual-array"></a>Usługa StorSimple za pomocą Menedżera urządzeń, aby zarządzać woluminy na tablicy wirtualnego StorSimple

## <a name="overview"></a>Omówienie

W tym samouczku opisano sposób korzystania z usługi Menedżer StorSimple urządzenia do tworzenia i zarządzania woluminach macierzy wirtualne StorSimple.

Usługę Menedżer StorSimple urządzenia jest rozszerzeniem w portalu Azure, która umożliwia zarządzanie rozwiązania StorSimple z interfejsem sieci web jednej. Oprócz zarządzania udziałami i woluminami, można użyć usługi Menedżer StorSimple urządzenia, wyświetlanie i zarządzanie urządzeniami, wyświetlanie alertów i wyświetlić oraz zarządzanie nimi zasady tworzenia kopii zapasowej i kopii zapasowej wykazu.

## <a name="volume-types"></a>Typy woluminu

Woluminy StorSimple można:

* **Przypięty lokalnie**: dane w tych woluminów pozostaje w macierzy przez cały czas i nie zostaną przeniesione do chmury.
* **Do warstwy**: dane w tych woluminów mogą zostaną przeniesione do chmury. Podczas tworzenia woluminu warstwowego około 10% miejsca jest inicjowana na warstwie lokalnej i 90% miejsca jest udostępniony w chmurze. Na przykład jeśli zainicjowano obsługę administracyjną wolumin 1 TB, 100 GB, czy znajdują się w lokalnej przestrzeni i 900 GB byłoby używanych w chmurze po warstwy danych. Z kolei oznacza to, że jeśli całe lokalne miejsce na urządzeniu nie można dostarczać wolumin warstwowy (ponieważ 10%, wymagane w warstwie lokalnej nie będzie dostępna).

### <a name="provisioned-capacity"></a>Udostępnione pojemności
Zapoznaj się z poniższej tabeli maksymalną pojemność udostępnione dla każdego typu woluminu.

| **Identyfikator limit**                                       | **Limit**     |
|------------------------------------------------------------|---------------|
| Minimalny rozmiar woluminu warstwowego                            | 500 GB        |
| Maksymalny rozmiar woluminu warstwowego                            | 5 TB          |
| Minimalny rozmiar woluminu przypiętego lokalnie                    | 50 GB         |
| Maksymalny rozmiar woluminu przypiętego lokalnie                    | 500 GB        |

## <a name="the-volumes-blade"></a>Blok woluminów
**Woluminów** menu w bloku podsumowania usługi StorSimple Wyświetla listę woluminów magazynu dla danej tablicy StorSimple i umożliwia zarządzanie nimi.

![Blok woluminów](./media/storsimple-virtual-array-manage-volumes/volumes-blade.png)

Wolumin składa się z szeregu atrybuty:

* **Nazwa woluminu** — opisową nazwę, która musi być unikatowa i pomaga w identyfikacji woluminu.
* **Stan** — może być w trybie online lub offline. Jeśli wolumin jest w trybie offline, nie jest widoczny dla inicjatorów (serwerów), które mają dostęp do korzystać z woluminu.
* **Typ** — wskazuje, czy wolumin jest **warstwowego** (ustawienie domyślne) lub **przypięty lokalnie**.
* **Pojemność** — określa ilość danych używanych w porównaniu z łączną ilość danych, które mogą być przechowywane przez inicjatora (serwer).
* **Kopia zapasowa** — w przypadku tablicy wirtualnego StorSimple, wszystkie woluminy są włączane automatycznie do kopii zapasowej.
* **Połączone hosty** — określa inicjatorów (serwerów), które mają prawo dostępu do tego woluminu.

![Szczegóły ilości](./media/storsimple-virtual-array-manage-volumes/volume-details.png)

Wykonaj instrukcje w tym samouczku, aby wykonywać następujące zadania:

* Dodawanie woluminu
* Modyfikowanie woluminu
* Przełącz do trybu offline woluminu
* Usuwanie woluminu

## <a name="add-a-volume"></a>Dodawanie woluminu

1. W bloku podsumowania usługi StorSimple, kliknij **+ Dodaj wolumin** z paska poleceń. Spowoduje to otwarcie **Dodaj wolumin** bloku.
   
    ![Dodawanie woluminu](./media/storsimple-virtual-array-manage-volumes/add-volume.png)
2. W **Dodaj wolumin** blok, wykonaj następujące czynności:
   
   * W **nazwa woluminu** wprowadź unikatową nazwę dla woluminu. Nazwa musi być ciągiem o długości 3 do 127 znaków.
   * W **typu** listy rozwijanej liście, określ, czy można utworzyć **warstwowego** lub **przypięty lokalnie** woluminu. W przypadku obciążeń, które wymagają lokalnych gwarancji, małych opóźnień i większej wydajności, wybierz **wolumin przypięty lokalnie**. Dla wszystkich innych danych wybierz **warstwowego** woluminu.
   * W **pojemności** Określ rozmiar woluminu. Wolumin warstwowy musi należeć do zakresu od 500 GB i 5 TB i woluminu przypiętego lokalnie musi należeć do zakresu od 50 GB i 500 GB.
   * * Kliknij przycisk **połączone hosty**, zaznacz rekord kontroli dostępu (ACR) odpowiadający inicjatora iSCSI, który chcesz połączyć do tego woluminu, a następnie kliknij przycisk **wybierz**.
3. Aby dodać nowy host połączony, kliknij przycisk **Dodaj nowe**, wprowadź nazwę hosta i jego iSCSI kwalifikowana nazwa (IQN), a następnie kliknij przycisk **Dodaj**.
   
    ![Dodawanie woluminu](./media/storsimple-virtual-array-manage-volumes/volume-add-acr.png)
4. Po zakończeniu konfigurowania woluminu, kliknij przycisk **Utwórz**. Wolumin zostanie utworzony przy użyciu określonych ustawień i otrzymają powiadomienie o pomyślnym utworzeniu tego samego. Domyślnie kopia zapasowa zostanie włączona dla woluminu.
5. Aby upewnić się, że wolumin został utworzony pomyślnie, przejdź do **woluminów** bloku. Powinny pojawić się woluminu na liście.
   
    ![Powodzenie Tworzenie woluminu](./media/storsimple-virtual-array-manage-volumes/volume-success.png)

## <a name="modify-a-volume"></a>Modyfikowanie woluminu

Jeśli musisz zmienić hosty, które uzyskują dostęp do woluminu, należy zmodyfikować woluminu. Inne atrybuty woluminu nie można zmodyfikować po utworzeniu woluminu.

#### <a name="to-modify-a-volume"></a>Aby zmodyfikować woluminu

1. Z **woluminów** ustawić bloku podsumowania usługi StorSimple, wybierz tablicy wirtualny, na którym znajduje się wolumin ma modyfikowania.
2. **Wybierz** wolumin i kliknij przycisk **połączone hosty** Aby wyświetlić aktualnie połączonych hosta, a następnie zmodyfikować go na innym serwerze.
   
    ![Edytuj woluminu](./media/storsimple-virtual-array-manage-volumes/volume-edit-acr.png)
3. Zapisz zmiany, klikając **zapisać** paska poleceń. Określony ustawienia będą stosowane i zostanie wyświetlone powiadomienie.

## <a name="take-a-volume-offline"></a>Przełącz do trybu offline woluminu

Konieczne może być Przełącz wolumin do trybu offline podczas planowania go zmodyfikować lub usunąć. Gdy wolumin jest w trybie offline, nie jest dostępny do odczytu i zapisu. Należy podjąć woluminu w trybie offline na hoście, a także na urządzeniu.

#### <a name="to-take-a-volume-offline"></a>Wolumin przełączenia do trybu offline

1. Upewnij się, że w danym woluminie nie jest używany przed przełączeniem do trybu offline.
2. Zająć pierwszy woluminu w trybie offline na hoście. Eliminuje to potencjalne ryzyko uszkodzeniem danych na woluminie. Aby poznać konkretne kroki zapoznaj się z instrukcjami dla systemu operacyjnego hosta.
3. Po wolumin hosta jest w trybie offline, należy podjąć woluminu w macierzy w trybie offline, wykonując następujące czynności:
   
   * Z **woluminów** ustawić bloku podsumowania usługi StorSimple, wybierz tablicy wirtualny, na którym znajduje się wolumin ma można przełączyć do trybu offline.
   * **Wybierz** wolumin i kliknij przycisk **...**  (Alternatywnie kliknij prawym przyciskiem myszy w tym wierszu) i wybierz z menu kontekstowego **przełączyć do trybu offline**.
     
        ![Wolumin w trybie offline](./media/storsimple-virtual-array-manage-volumes/volume-offline.png)
   * Przejrzyj informacje w **przełączyć do trybu offline** bloku i potwierdzenie akceptacji wykonać operację. Kliknij przycisk **przełączyć do trybu offline** woluminu przełączenia do trybu offline. Zostanie wyświetlone powiadomienie operacji w toku.
   * Aby upewnić się, że wolumin został pomyślnie przełączony w tryb offline, przejdź do **woluminów** bloku. Stan woluminu powinien być widoczny jako w trybie offline.
     
       ![Potwierdzenie woluminu w trybie offline](./media/storsimple-virtual-array-manage-volumes/volume-offline-confirm.png)

## <a name="delete-a-volume"></a>Usuwanie woluminu

> [!IMPORTANT]
> Wolumin można usunąć tylko wtedy, gdy jest w trybie offline.
> 
> 

Wykonaj poniższe kroki, aby usunąć wolumin.

#### <a name="to-delete-a-volume"></a>Aby usunąć wolumin

1. Z **woluminów** ustawić bloku podsumowania usługi StorSimple, wybierz tablicy wirtualny, na którym znajduje się wolumin ma na usuwanie.
2. **Wybierz** wolumin i kliknij przycisk **...**  (Alternatywnie kliknij prawym przyciskiem myszy w tym wierszu) i wybierz z menu kontekstowego **usunąć**.
   
    ![Usuń wolumin](./media/storsimple-virtual-array-manage-volumes/volume-delete.png)
3. Sprawdź stan woluminu, który chcesz usunąć. Jeśli wolumin do usunięcia nie jest w trybie offline, przejść do trybu offline należy wykonać kroki opisane w [Przełącz do trybu offline wolumin](#take-a-volume-offline).
4. Po wyświetleniu monitu o potwierdzenie w **usunąć** bloku, Zaakceptuj potwierdzenie i kliknij przycisk **usunąć**. Wolumin zostanie usunięte i **woluminów** bloku wyświetli zaktualizowaną listę woluminów w tablicy wirtualnego.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [sklonować woluminu StorSimple](storsimple-virtual-array-clone.md).

