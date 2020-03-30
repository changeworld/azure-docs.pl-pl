---
title: Zarządzanie woluminami w macierzy wirtualnej StorSimple | Dokumenty firmy Microsoft
description: W tym artykule opisano StorSimple Device Manager i wyjaśnia, jak go używać do zarządzania woluminami w storsimple virtual array.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "62125801"
---
# <a name="use-storsimple-device-manager-service-to-manage-volumes-on-the-storsimple-virtual-array"></a>Use StorSimple Device Manager service to manage volumes on the StorSimple Virtual Array (Zarządzanie wirtualną tablicą woluminów za pomocą usługi StorSimple Device Manager)

## <a name="overview"></a>Omówienie

W tym samouczku wyjaśniono, jak używać usługi StorSimple Device Manager do tworzenia woluminów w macierzy wirtualnej StorSimple i zarządzania nimi.

Usługa StorSimple Device Manager jest rozszerzeniem w portalu Azure, które umożliwia zarządzanie rozwiązaniem StorSimple za pomocą jednego interfejsu internetowego. Oprócz zarządzania udziałami i woluminami można użyć usługi StorSimple Device Manager do wyświetlania urządzeń i zarządzania nimi, wyświetlania alertów oraz wyświetlania zasad tworzenia kopii zapasowych i zarządzania nimi oraz zarządzania nimi.

## <a name="volume-types"></a>Typy woluminów

StorSimple woluminy mogą być:

* **Lokalnie przypięte:** Dane w tych woluminach pozostają na tablicy przez cały czas i nie wyciekają do chmury.
* **Warstwowe:** Dane w tych woluminach mogą wyciekać do chmury. Podczas tworzenia woluminu warstwowego około 10 % miejsca jest aprowizowana w warstwie lokalnej, a 90 % miejsca jest aprowiowane w chmurze. Na przykład jeśli aprowizowana wolumin 1 TB, 100 GB będzie znajdować się w przestrzeni lokalnej i 900 GB będą używane w chmurze, gdy warstwy danych. To z kolei oznacza, że jeśli zabraknie całego miejsca lokalnego na urządzeniu, nie można aprowizować wolumin warstwowy (ponieważ 10 % wymagane w warstwie lokalnej nie będą dostępne).

### <a name="provisioned-capacity"></a>Pojemność aprowizowana
Zobacz poniższą tabelę, aby uzyskać maksymalną pojemność aprowizacji dla każdego typu woluminu.

| **Identyfikator limitu**                                       | **Limit**     |
|------------------------------------------------------------|---------------|
| Minimalny rozmiar woluminu warstwowego                            | 500 GB        |
| Maksymalny rozmiar woluminu warstwowego                            | 5 TB          |
| Minimalny rozmiar woluminu przypiętego lokalnie                    | 50 GB         |
| Maksymalny rozmiar woluminu przypiętego lokalnie                    | 500 GB        |

## <a name="the-volumes-blade"></a>Ostrze Woluminy
Menu **Woluminy** w bloku podsumowania usługi StorSimple wyświetla listę woluminów magazynu w danej macierzy StorSimple i umożliwia zarządzanie nimi.

![Tarcza objętościowa](./media/storsimple-virtual-array-manage-volumes/volumes-blade.png)

Wolumin składa się z serii atrybutów:

* **Nazwa woluminu** — opisowa nazwa, która musi być unikatowa i pomaga zidentyfikować wolumin.
* **Status** — może być w trybie online lub offline. Jeśli wolumin jest w trybie offline, nie jest widoczny dla inicjatorów (serwerów), którzy mają dostęp do korzystania z woluminu.
* **Typ** — wskazuje, czy wolumin jest **warstwowy** (domyślnie) czy **przypięty lokalnie.**
* **Pojemność** — określa ilość danych używanych w porównaniu do całkowitej ilości danych, które mogą być przechowywane przez inicjatora (serwera).
* **Kopia zapasowa** — w przypadku tablicy wirtualnej StorSimple wszystkie woluminy są automatycznie włączane do tworzenia kopii zapasowych.
* **Połączone hosty** — określa inicjatorów (serwery), które mają dostęp do tego woluminu.

![Szczegóły woluminów](./media/storsimple-virtual-array-manage-volumes/volume-details.png)

Użyj instrukcji w tym samouczku, aby wykonać następujące zadania:

* Dodawanie woluminu
* Modyfikowanie woluminu
* Przejań wolumin do trybu offline
* Usuwanie woluminu

## <a name="add-a-volume"></a>Dodawanie woluminu

1. W bloku podsumowanie usługi StorSimple kliknij przycisk **+ Dodaj wolumin** z paska poleceń. Spowoduje to otwarcie bloku **Dodaj wolumin.**
   
    ![Dodawanie woluminu](./media/storsimple-virtual-array-manage-volumes/add-volume.png)
2. W bloku **Dodaj głośność** wykonaj następujące czynności:
   
   * W polu **Nazwa woluminu** wprowadź unikatową nazwę woluminu. Nazwa musi być ciągiem zawierającym od 3 do 127 znaków.
   * Na liście rozwijanej **Typ** określ, czy wolumin **warstwowy** lub **lokalnie przypięty.** W przypadku obciążeń wymagających gwarancji lokalnych, małych opóźnień i wyższej wydajności wybierz **opcję Lokalnie przypięty wolumin**. W przypadku wszystkich innych danych wybierz wolumin **warstwowy.**
   * W polu **Pojemność** określ rozmiar woluminu. Wolumin warstwowy musi mieć wartość od 500 GB do 5 TB, a wolumin przypięty lokalnie musi mieć od 50 GB do 500 GB.
   * * Kliknij **pozycję Połączone hosty**, wybierz rekord kontroli dostępu (ACR) odpowiadający inicjatorowi iSCSI, który chcesz połączyć z tym woluminem, a następnie kliknij przycisk **Wybierz**.
3. Aby dodać nowego połączonego hosta, kliknij pozycję **Dodaj nowy**, wprowadź nazwę hosta i jego nazwę kwalifikowaną iSCSI (IQN), a następnie kliknij przycisk **Dodaj**.
   
    ![Dodawanie woluminu](./media/storsimple-virtual-array-manage-volumes/volume-add-acr.png)
4. Po zakończeniu konfigurowania woluminu kliknij przycisk **Utwórz**. Wolumin zostanie utworzony z określonymi ustawieniami i zostanie wyświetlone powiadomienie o pomyślnym utworzeniu tego samego. Domyślnie kopia zapasowa zostanie włączona dla woluminu.
5. Aby potwierdzić, że wolumin został pomyślnie utworzony, przejdź do **bloku Woluminy.** Wolumin powinien być widoczny na liście.
   
    ![Tworzenie woluminów powodem sukcesu](./media/storsimple-virtual-array-manage-volumes/volume-success.png)

## <a name="modify-a-volume"></a>Modyfikowanie woluminu

Zmodyfikuj wolumin, gdy trzeba zmienić hosty uzyskujące dostęp do woluminu. Inne atrybuty woluminu nie mogą być modyfikowane po utworzeniu woluminu.

#### <a name="to-modify-a-volume"></a>Aby zmodyfikować wolumin

1. W **ustawieniach Woluminy** w bloku podsumowania usługi StorSimple wybierz tablicę wirtualną, na której znajduje się wolumin, który chcesz zmodyfikować.
2. **Wybierz** wolumin i kliknij pozycję **Połączone hosty,** aby wyświetlić aktualnie połączony host i zmodyfikować go na innym serwerze.
   
    ![Edytuj głośność](./media/storsimple-virtual-array-manage-volumes/volume-edit-acr.png)
3. Zapisz zmiany, klikając pasek poleceń **Zapisz.** Określone ustawienia zostaną zastosowane, a zostanie wyświetlone powiadomienie.

## <a name="take-a-volume-offline"></a>Przejań wolumin do trybu offline

Może być konieczne przełączeniu woluminu w tryb offline, gdy planujesz go zmodyfikować lub usunąć. Gdy wolumin jest w trybie offline, nie jest dostępny dla dostępu do odczytu i zapisu. Wolumin należy wykonać w trybie offline na hoście, a także na urządzeniu.

#### <a name="to-take-a-volume-offline"></a>Aby przetraktować wolumin w trybie offline

1. Upewnij się, że dany wolumin nie jest używany przed przełączeniem go w tryb offline.
2. Najpierw przejmij wolumin w tryb offline na hoście. Eliminuje to wszelkie potencjalne ryzyko uszkodzenia danych na woluminie. Aby uzyskać szczegółowe kroki, zapoznaj się z instrukcjami dotyczącymi systemu operacyjnego hosta.
3. Po przejadaniu woluminu na hoście w trybie offline przejmij wolumin w tablicy w tryb offline, wykonując następujące czynności:
   
   * W **ustawieniach Woluminy** w bloku podsumowania usługi StorSimple wybierz tablicę wirtualną, na której znajduje się wolumin, który chcesz przełączyć do trybu offline.
   * **Wybierz** wolumin i kliknij **...** (na przemian kliknij prawym przyciskiem myszy w tym wierszu) i z menu kontekstowego wybierz pozycję **Przejmij do trybu offline**.
     
        ![Wolumin w trybie offline](./media/storsimple-virtual-array-manage-volumes/volume-offline.png)
   * Przejrzyj informacje w bloku **Przejmij tryb offline** i potwierdź akceptację operacji. Kliknij **pozycję Przejmij do trybu offline,** aby przetraktować wolumin. Zostanie wyświetlone powiadomienie o operacji w toku.
   * Aby potwierdzić, że wolumin został pomyślnie przesuń do trybu offline, przejdź do **bloku Woluminy.** Stan woluminu powinien być widoczny w trybie offline.
     
       ![Potwierdzenie woluminu w trybie offline](./media/storsimple-virtual-array-manage-volumes/volume-offline-confirm.png)

## <a name="delete-a-volume"></a>Usuwanie woluminu

> [!IMPORTANT]
> Wolumin można usunąć tylko wtedy, gdy jest w trybie offline.
> 
> 

Wykonaj następujące kroki, aby usunąć wolumin.

#### <a name="to-delete-a-volume"></a>Aby usunąć wolumin

1. W **ustawieniach Woluminy** w bloku podsumowania usługi StorSimple wybierz tablicę wirtualną, na której znajduje się wolumin, który chcesz usunąć.
2. **Wybierz** wolumin i kliknij **...** (na przemian kliknij prawym przyciskiem myszy w tym wierszu) i z menu kontekstowego wybierz polecenie **Usuń**.
   
    ![Usuwanie woluminu](./media/storsimple-virtual-array-manage-volumes/volume-delete.png)
3. Sprawdź stan woluminu, który chcesz usunąć. Jeśli wolumin, który chcesz usunąć, nie jest w trybie offline, najpierw przejmij go w tryb offline, wykonując czynności opisane w [obszarze Przemij wolumin](#take-a-volume-offline)w tryb offline .
4. Po wyświetleniu monitu o potwierdzenie w bloku **Usuń** zaakceptuj potwierdzenie i kliknij przycisk **Usuń**. Wolumin zostanie teraz usunięty, a w bloku **Woluminy** zostanie wyświetlona zaktualizowana lista woluminów w macierzy wirtualnej.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [sklonować wolumin StorSimple](storsimple-virtual-array-clone.md).

