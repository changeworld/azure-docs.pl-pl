---
title: Zarządzanie woluminami na macierz wirtualną StorSimple | Dokumentacja firmy Microsoft
description: W tym artykule opisano Menedżera urządzeń StorSimple i wyjaśniono, jak przy jego użyciu zarządzać woluminy na rozwiązania StorSimple Virtual Array.
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
ms.openlocfilehash: a233a9deb58a7c1abc87a622a4f1f2581ee2e477
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125801"
---
# <a name="use-storsimple-device-manager-service-to-manage-volumes-on-the-storsimple-virtual-array"></a>Usługa Menedżer urządzeń StorSimple użycia do zarządzania woluminach macierzy wirtualnej StorSimple

## <a name="overview"></a>Omówienie

W tym samouczku wyjaśniono, jak tworzyć i zarządzać nimi woluminów na rozwiązania StorSimple Virtual Array za pomocą usługi Menedżer urządzeń StorSimple.

Usługa Menedżer urządzeń StorSimple jest rozszerzeniem w witrynie Azure portal, która umożliwia zarządzanie rozwiązania StorSimple z jednej sieci web interfejsu. Oprócz zarządzania udziały i woluminy, można użyć usługi Menedżer urządzeń StorSimple do wyświetlanie i zarządzanie urządzeniami, wyświetlanie alertów, wyświetlanie i zarządzanie zasadami tworzenia kopii zapasowych i wykazem kopii zapasowych.

## <a name="volume-types"></a>Typy woluminów

Woluminy StorSimple może być:

* **Przypięty lokalnie**: Dane w tych woluminach pozostaje w obrębie macierzy przez cały czas i nie zostaną przeniesione do chmury.
* **Warstwowe**: Dane w tych woluminach można zostaną przeniesione do chmury. Podczas tworzenia woluminu warstwowego około 10% wolnego miejsca jest inicjowana na warstwie lokalnej i 90% miejsca są aprowizowane w chmurze. Na przykład, jeśli aprowizowano 1 TB woluminu 100 GB będzie znajdować się w lokalnej przestrzeni i 900 GB będzie używana w chmurze podczas warstwy danych. Z kolei oznacza to, że jeśli zabraknie całe lokalne miejsce na urządzeniu nie można dostarczać woluminu warstwowego (ponieważ 10%, wymagane w warstwie lokalnej nie będą dostępne).

### <a name="provisioned-capacity"></a>Zaprowizowaną pojemnością
Można znaleźć w poniższej tabeli, aby uzyskać maksymalną pojemność aprowizowaną dla każdego typu woluminu.

| **Identyfikator limitu**                                       | **Limit**     |
|------------------------------------------------------------|---------------|
| Minimalny rozmiar woluminu warstwowego                            | 500 GB        |
| Maksymalny rozmiar woluminu warstwowego                            | 5 TB          |
| Minimalny rozmiar woluminu przypiętego lokalnie                    | 50 GB         |
| Maksymalny rozmiar woluminu przypiętego lokalnie                    | 500 GB        |

## <a name="the-volumes-blade"></a>W bloku woluminów
**Woluminów** menu w bloku podsumowania usługi StorSimple Wyświetla listę woluminów pamięci masowej w danej macierzy StorSimple oraz pozwala nimi zarządzać.

![Blok woluminów](./media/storsimple-virtual-array-manage-volumes/volumes-blade.png)

Wolumin składa się z szeregu atrybuty:

* **Nazwa woluminu** — nazwę opisową, muszą być unikatowe, która ułatwia zidentyfikowanie woluminu.
* **Stan** — może być w trybie online lub offline. Jeśli wolumin jest w trybie offline, nie jest widoczny dla inicjatorów (serwerów), które mogą uzyskiwać dostęp do korzystać z woluminu.
* **Typ** — wskazuje, czy wolumin jest **warstwowy** (ustawienie domyślne) lub **przypięty lokalnie**.
* **Pojemność** — określa ilość danych używanych w porównaniu z całkowitą ilość danych, które mogą być przechowywane przez inicjatora (serwer).
* **Kopii zapasowej** — w przypadku, gdy macierzy wirtualnej StorSimple, wszystkie woluminy są włączane automatycznie do kopii zapasowej.
* **Połączone hosty** — określa inicjatorów (serwerów), które mogą uzyskiwać dostęp do tego woluminu.

![Szczegóły ilości](./media/storsimple-virtual-array-manage-volumes/volume-details.png)

Skorzystaj z instrukcji w tym samouczku, aby wykonać następujące zadania:

* Dodawanie woluminu
* Modyfikowanie woluminu
* Przełącz wolumin w tryb offline
* Usuwanie woluminu

## <a name="add-a-volume"></a>Dodawanie woluminu

1. W bloku podsumowania usługi StorSimple kliknij **+ Dodaj wolumin** na pasku poleceń. Spowoduje to otwarcie **Dodaj wolumin** bloku.
   
    ![Dodawanie woluminu](./media/storsimple-virtual-array-manage-volumes/add-volume.png)
2. W **Dodaj wolumin** blok, wykonaj następujące czynności:
   
   * W **nazwa woluminu** wprowadź unikatową nazwę dla woluminu. Nazwa musi być ciąg, który zawiera 3 do 127 znaków.
   * W **typu** listę rozwijaną listę, określ, czy należy utworzyć **warstwowy** lub **przypięty lokalnie** woluminu. Dla obciążeń, które wymagają lokalnych gwarancji, małych opóźnień i większej wydajności, wybierz **przypięty lokalnie woluminie**. Dla wszystkich innych danych wybierz **warstwowy** woluminu.
   * W **pojemności** Określ rozmiar woluminu. Wolumin warstwowy musi wynosić od 500 GB do 5 TB, a następnie wolumin przypięty lokalnie muszą należeć do zakresu od 50 do 500 GB.
   * * Kliknij przycisk **połączone hosty**, zaznacz rekord kontroli dostępu (ACR) odpowiadający inicjatora iSCSI, którą chcesz nawiązać połączenie z tego woluminu, a następnie kliknij przycisk **wybierz**.
3. Aby dodać nowy host połączony, kliknij **Dodaj nowe**, wprowadź nazwę hosta i jego iSCSI kwalifikowana nazwa (IQN), a następnie kliknij **Dodaj**.
   
    ![Dodawanie woluminu](./media/storsimple-virtual-array-manage-volumes/volume-add-acr.png)
4. Po zakończeniu konfigurowania woluminu, kliknij przycisk **Utwórz**. Wolumin zostanie utworzony z określonymi ustawieniami, a następnie zostanie wyświetlone powiadomienie po pomyślnym utworzeniu tego samego. Domyślnie kopia zapasowa zostanie włączona dla woluminu.
5. Aby upewnić się, że wolumin został utworzony pomyślnie, przejdź do **woluminów** bloku. Wolumin, na liście powinien zostać wyświetlony.
   
    ![Wolumin tworzenie sukces](./media/storsimple-virtual-array-manage-volumes/volume-success.png)

## <a name="modify-a-volume"></a>Modyfikowanie woluminu

Gdy zachodzi potrzeba zmiany hostów, które dostęp do woluminu, należy zmodyfikować woluminu. Inne atrybuty woluminu nie można zmodyfikować po utworzeniu woluminu.

#### <a name="to-modify-a-volume"></a>Aby zmodyfikować woluminu

1. Z **woluminów** ustawienia w bloku podsumowania usługi StorSimple, wybierz macierz wirtualna, na którym znajduje się wolumin, chcesz, możesz zmodyfikować.
2. **Wybierz** woluminu i kliknij przycisk **połączone hosty** Aby wyświetlić aktualnie połączony host i zmodyfikuj go na inny serwer.
   
    ![Edytuj woluminu](./media/storsimple-virtual-array-manage-volumes/volume-edit-acr.png)
3. Zapisz zmiany, klikając **Zapisz** paska poleceń. Swoje określone ustawienia zostaną zastosowane, a następnie zostanie wyświetlone powiadomienie.

## <a name="take-a-volume-offline"></a>Przełącz wolumin w tryb offline

Może być konieczne przełączyć wolumin w tryb offline podczas planowania go zmodyfikować lub usunąć plik. Gdy wolumin jest w trybie offline, nie jest dostępny do odczytu i zapisu. Należy wykonać wolumin w tryb offline na hoście, a także na urządzeniu.

#### <a name="to-take-a-volume-offline"></a>Aby przełączyć wolumin w trybie offline

1. Upewnij się, że w danym woluminie nie jest używany przed przełączeniem jej w tryb offline.
2. Wykonaj pierwszy wolumin w tryb offline na hoście. Pozwala to wyeliminować wszelkie potencjalne ryzyko uszkodzenia danych na woluminie. Aby poznać konkretne kroki zapoznaj się z instrukcjami w systemie operacyjnym hosta.
3. Po wolumin hosta jest w trybie offline, przełącz go w macierzy w trybie offline, wykonując następujące czynności:
   
   * Z **woluminów** ustawienia w bloku podsumowania usługi StorSimple, wybierz macierz wirtualna, na którym znajduje się wolumin, chcesz, możesz się przełączyć do trybu offline.
   * **Wybierz** woluminu i kliknij przycisk **...**  (Alternatywnie kliknij prawym przyciskiem myszy w tym wierszu) z menu kontekstowego wybierz **przełączyć do trybu offline**.
     
        ![Wolumin w trybie offline](./media/storsimple-virtual-array-manage-volumes/volume-offline.png)
   * Zapoznaj się z informacjami w **przełączyć do trybu offline** bloku i upewnij się, zaakceptowania przez operację. Kliknij przycisk **przełączyć do trybu offline** do przełączenia woluminu w tryb offline. Zostanie wyświetlone powiadomienie operacji w toku.
   * Aby upewnić się, że wolumin pomyślnie przejdzie w tryb offline, przejdź do **woluminów** bloku. Powinien zostać wyświetlony stan woluminu jako offline.
     
       ![Potwierdzenie wolumin w trybie offline](./media/storsimple-virtual-array-manage-volumes/volume-offline-confirm.png)

## <a name="delete-a-volume"></a>Usuwanie woluminu

> [!IMPORTANT]
> Wolumin można usunąć tylko wtedy, gdy jest w trybie offline.
> 
> 

Wykonaj poniższe kroki, aby usunąć wolumin.

#### <a name="to-delete-a-volume"></a>Aby usunąć wolumin

1. Z **woluminów** ustawienia w bloku podsumowania usługi StorSimple, wybierz macierz wirtualna, na którym znajduje się wolumin, chcesz, możesz usunąć.
2. **Wybierz** woluminu i kliknij przycisk **...**  (Alternatywnie kliknij prawym przyciskiem myszy w tym wierszu) z menu kontekstowego wybierz **Usuń**.
   
    ![Usuń wolumin](./media/storsimple-virtual-array-manage-volumes/volume-delete.png)
3. Sprawdź stan woluminów, które chcesz usunąć. Jeśli wolumin, który chcesz usunąć, nie jest w trybie offline, przejść do trybu offline, wykonaj czynności w [przełączyć wolumin w tryb offline](#take-a-volume-offline).
4. Po wyświetleniu monitu o potwierdzenie w **Usuń** bloku Zaakceptuj potwierdzenie i kliknij przycisk **Usuń**. Wolumin zostanie usunięte i **woluminów** bloku pokaże zaktualizowaną listę woluminów w ramach macierzy wirtualnej.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [klonowania woluminu StorSimple](storsimple-virtual-array-clone.md).

