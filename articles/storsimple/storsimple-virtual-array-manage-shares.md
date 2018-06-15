---
title: Zarządzanie udziałami tablicy wirtualnego StorSimple | Dokumentacja firmy Microsoft
description: Zawiera opis Menedżera urządzeń StorSimple i wyjaśniono, jak przy jego użyciu zarządzać udziałami w macierzy wirtualne StorSimple.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: 0a799c83-fde5-4f3f-af0e-67535d1882b6
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: e5c62689de36baa175001f5f4f70d87568876ef0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876271"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-shares-on-the-storsimple-virtual-array"></a>Korzystania z usługi Menedżera urządzeń StorSimple do zarządzania udziałami w macierzy wirtualnego StorSimple

## <a name="overview"></a>Omówienie

W tym samouczku opisano sposób korzystania z usługi Menedżer StorSimple urządzenia do tworzenia i zarządzania udziałami w macierzy wirtualne StorSimple.

Usługę Menedżer StorSimple urządzenia jest rozszerzeniem w portalu Azure, która umożliwia zarządzanie rozwiązania StorSimple z interfejsem sieci web jednej. Oprócz zarządzania udziałami i woluminami, można użyć usługi Menedżer StorSimple urządzenia, wyświetlanie i zarządzanie urządzeniami, wyświetlanie alertów, Zarządzanie zasadami tworzenia kopii zapasowej oraz zarządzanie nimi katalogu kopii zapasowej.

## <a name="share-types"></a>Typy udziału

Udziały StorSimple można:

* **Przypięty lokalnie**: dane w tych udziałach pozostaje w macierzy przez cały czas i nie zostaną przeniesione do chmury.
* **Do warstwy**: dane w tych udziałach można zostaną przeniesione do chmury. Podczas tworzenia udziału warstwowych około 10% miejsca jest inicjowana na warstwie lokalnej i 90% miejsca jest udostępniony w chmurze. Na przykład jeśli zainicjowano obsługę administracyjną udziału 1 TB, 100 GB, czy znajdują się w lokalnej przestrzeni i 900 GB byłoby używanych w chmurze po warstwy danych. Z kolei oznacza to, że jeśli całe lokalne miejsce na urządzeniu nie można dostarczać warstwowych udziału (ponieważ 10%, wymagane w warstwie lokalnej nie będzie dostępna).

### <a name="provisioned-capacity"></a>Udostępnione pojemności

Zapoznaj się z poniższej tabeli maksymalną pojemność udostępnione dla każdego typu udziału.

| **Identyfikator limit** | **Limit** |
| --- | --- |
| Minimalny rozmiar udziału warstwowych |500 GB |
| Maksymalny rozmiar udziału warstwowych |20 TB |
| Minimalny rozmiar udziału przypiętych lokalnie |50 GB |
| Maksymalny rozmiar udziału przypiętych lokalnie |2 TB |

## <a name="the-shares-blade"></a>Udziałów bloku

**Udziałów** menu w bloku podsumowania usługi StorSimple Wyświetla listę udziałów magazynu w danej macierzy StorSimple i umożliwia zarządzanie nimi.

![Udziałów bloku](./media/storsimple-virtual-array-manage-shares/shares-blade.png)

Udział składa się z szeregu atrybuty:

* **Nazwa udziału** — opisową nazwę, która musi być unikatowa i ułatwia identyfikowanie udziału.
* **Stan** — może być w trybie online lub offline. Jeśli udział jest w trybie offline, użytkownicy udziału nie będą mogli uzyskać do niego dostęp.
* **Typ** — wskazuje, czy udział jest **warstwowego** (ustawienie domyślne) lub **przypięty lokalnie**.
* **Pojemność** — określa ilość danych używanych w porównaniu z łączną ilość danych, które mogą być przechowywane w udziale.
* **Opis elementu** — ustawienie opcjonalne pomaga opis udziału.
* **Uprawnienia** -uprawnienia NTFS do udziału, którymi można zarządzać za pomocą Eksploratora Windows.
* **Kopia zapasowa** — w przypadku tablicy wirtualnego StorSimple, wszystkie udziały są włączane automatycznie do kopii zapasowej.

![Udostępnia szczegóły](./media/storsimple-virtual-array-manage-shares/share-details.png)

Wykonaj instrukcje w tym samouczku, aby wykonywać następujące zadania:

* Dodawanie udziału
* Modyfikowanie udziału
* Przełącz do trybu offline udziału
* Usuń udział

## <a name="add-a-share"></a>Dodawanie udziału

1. W bloku podsumowania usługi StorSimple, kliknij **+ Dodaj udział** z paska poleceń. Spowoduje to otwarcie **Dodaj udział** bloku.

    ![Dodaj udział](./media/storsimple-virtual-array-manage-shares/add-share.png)

2. W **Dodaj udział** blok, wykonaj następujące czynności:
   
    1. W **nazwa udziału** wprowadź unikatową nazwę udziału użytkownika. Nazwa musi być ciągiem o długości 3 do 127 znaków.

    2. Opcjonalny **opis** udziału. Opis pomoże zidentyfikować właścicieli udziału.

    3. W **typu** listy rozwijanej liście, określ, czy można utworzyć **warstwowego** lub **przypięty lokalnie** udziału. W przypadku obciążeń, które wymagają lokalnych gwarancji, małych opóźnień i większej wydajności, wybierz **przypięty lokalnie udziału**. Dla wszystkich innych danych wybierz **warstwowego** udziału.

    4. W **pojemności** Określ rozmiar udziału. Udział warstwowych musi wynosić od 500 GB do 20 TB i przypiętych lokalnie udziału musi należeć do zakresu od 50 GB i 2 TB.

    5. W **ustawioną domyślną pełne uprawnienia** pola, przypisz uprawnienia użytkownika lub grupy, do której uzyskuje dostęp do tego udziału. Określ nazwę użytkownika lub grupy użytkowników w  _john@contoso.com_  format. Zalecane jest użycie grupy użytkowników (zamiast jednego użytkownika) umożliwia uprawnień administratora uzyskać dostęp do tych udziałów. Po przypisaniu uprawnienia w tym miejscu, następnie służy Eksplorator plików do modyfikowania tych uprawnień.
3. Po zakończeniu konfigurowania udziału użytkownika, kliknij przycisk **Utwórz**. Udział zostanie utworzony przy użyciu określonych ustawień i otrzymają powiadomienie. Domyślnie kopia zapasowa mają zostać włączone dla tego udziału.
4. Aby upewnić się, że pomyślnie utworzono udział, przejdź do **udziałów** bloku. Powinny pojawić się udziału na liście.
   
    ![Sukces tworzenia udziału](./media/storsimple-virtual-array-manage-shares/share-success.png)

## <a name="modify-a-share"></a>Modyfikowanie udziału

Zmodyfikuj udział, gdy musisz zmienić opis udziału. Brak właściwości udziału można zmodyfikować po utworzeniu udziału.

#### <a name="to-modify-a-share"></a>Aby zmodyfikować udziału

1. Z **udziałów** ustawić bloku podsumowania usługi StorSimple, wybierz tablicy wirtualny, na którym znajduje się udział ma modyfikowania.
2. **Wybierz** udziału, aby wyświetlić bieżące opis, a następnie zmodyfikować go.
3. Zapisz zmiany, klikając **zapisać** paska poleceń. Określony ustawienia będą stosowane i zostanie wyświetlone powiadomienie.
   
    ![ Edytuj udziału](./media/storsimple-virtual-array-manage-shares/share-edit.png)

## <a name="take-a-share-offline"></a>Przełącz do trybu offline udziału

Konieczne może potrwać udział w trybie offline podczas planowania go zmodyfikować lub usunąć. Jeśli udział jest w trybie offline, nie jest dostępny do odczytu i zapisu. Należy wykonać w trybie offline udział na hoście, a także na urządzeniu.

#### <a name="to-take-a-share-offline"></a>Udział przełączenia do trybu offline

1. Upewnij się, że danego udziału nie jest używany, przed przełączeniem do trybu offline.
2. Przełącz udział w tablicy w trybie offline, wykonując następujące czynności:
   
    1. Z **udziałów** ustawić bloku podsumowania usługi StorSimple, wybierz tablicy wirtualny, na którym znajduje się udział ma można przełączyć do trybu offline.

    2. **Wybierz** udostępniania i kliknij przycisk **...**  (Alternatywnie kliknij prawym przyciskiem myszy w tym wierszu) i wybierz z menu kontekstowego **przełączyć do trybu offline**.
     
        ![Udostępnij w trybie offline](./media/storsimple-virtual-array-manage-shares/shares-offline.png)

    3. Przejrzyj informacje w **przełączyć do trybu offline** bloku i potwierdzenie akceptacji wykonać operację. Kliknij przycisk **przełączyć do trybu offline** udział przełączenia do trybu offline. Zostanie wyświetlone powiadomienie operacji w toku.

    4. Aby upewnić się, że udział pomyślnie został przełączony w tryb offline, przejdź do **udziałów** bloku. Stan udziału powinna zostać wyświetlona jako w trybie offline.

## <a name="delete-a-share"></a>Usuń udział

> [!IMPORTANT]
> Tylko wtedy, gdy jest w trybie offline, można usunąć udziału.


Wykonaj poniższe kroki, aby usunąć udział.

#### <a name="to-delete-a-share"></a>Aby usunąć udział

1. Z **udziałów** ustawić bloku podsumowania usługi StorSimple, wybierz tablicy wirtualny, na którym znajduje się udział do usunięcia.
2. **Wybierz** udostępniania i kliknij przycisk **...**  (Alternatywnie kliknij prawym przyciskiem myszy w tym wierszu) i wybierz z menu kontekstowego **usunąć**.
   
    ![Usuń udział](./media/storsimple-virtual-array-manage-shares/share-delete.png)
3. Sprawdź stan udziału, który chcesz usunąć. Jeśli chcesz usunąć udział nie jest w trybie offline, przejść do trybu offline najpierw. Postępuj zgodnie z instrukcjami [Przełącz do trybu offline udział](#take-a-share-offline).
4. Po wyświetleniu monitu o potwierdzenie w **usunąć** bloku, Zaakceptuj potwierdzenie i kliknij przycisk **usunąć**. Udział zostanie usunięte i **udziałów** blok zawiera zaktualizowaną listę udziały w wirtualnej tablicy.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [sklonować udziału StorSimple](storsimple-virtual-array-clone.md).

