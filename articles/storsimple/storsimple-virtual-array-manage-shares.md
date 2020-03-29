---
title: Zarządzanie udziałami w tablicy wirtualnej StorSimple | Dokumenty firmy Microsoft
description: W tym artykule opisano StorSimple Device Manager i wyjaśnia, jak go używać do zarządzania udziałami w storsimple virtual array.
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
ms.openlocfilehash: 82a6cdb6c9a39a0d196049a7ba662681ea06b36a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "62116870"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-shares-on-the-storsimple-virtual-array"></a>Use StorSimple Device Manager service to manage shares on the StorSimple Virtual Array (Zarządzanie wirtualną tablicą udziałów za pomocą usługi StorSimple DeviceManager)

## <a name="overview"></a>Omówienie

W tym samouczku wyjaśniono, jak używać usługi StorSimple Device Manager do tworzenia udziałów i zarządzania nimi w tablicy wirtualnej StorSimple.

Usługa StorSimple Device Manager jest rozszerzeniem w portalu Azure, które umożliwia zarządzanie rozwiązaniem StorSimple za pomocą jednego interfejsu internetowego. Oprócz zarządzania udziałami i woluminami można używać usługi StorSimple Device Manager do wyświetlania urządzeń i zarządzania nimi, wyświetlania alertów, zarządzania zasadami tworzenia kopii zapasowych i zarządzania katalogiem kopii zapasowych.

## <a name="share-types"></a>Typy akcji

Udziały StorSimple mogą być:

* **Lokalnie przypięte:** Dane w tych akcjach pozostają na tablicy przez cały czas i nie wyciekają do chmury.
* **Warstwowe:** Dane w tych udziałach mogą wyciekać do chmury. Podczas tworzenia udziału warstwowego około 10 % miejsca jest aprowizowana w warstwie lokalnej, a 90 % miejsca jest aprowiowane w chmurze. Na przykład jeśli aprowizowana udział 1 TB, 100 GB będzie znajdować się w przestrzeni lokalnej i 900 GB będą używane w chmurze, gdy warstwy danych. To z kolei oznacza, że jeśli zabraknie całej przestrzeni lokalnej na urządzeniu, nie można aprowizować udział warstwowy (ponieważ 10 % wymagane w warstwie lokalnej nie będą dostępne).

### <a name="provisioned-capacity"></a>Pojemność aprowizowana

Zobacz poniższą tabelę, aby uzyskać maksymalną pojemność aprowizacji dla każdego typu udziału.

| **Identyfikator limitu** | **Limit** |
| --- | --- |
| Minimalny rozmiar udziału warstwowego |500 GB |
| Maksymalny rozmiar udziału warstwowego |20 TB |
| Minimalny rozmiar lokalnie przypiętego udziału |50 GB |
| Maksymalny rozmiar lokalnie przypiętego udziału |2 TB |

## <a name="the-shares-blade"></a>Tarcza akcji

Menu **Udziały** w bloku podsumowania usługi StorSimple wyświetla listę udziałów magazynu w danej tablicy StorSimple i umożliwia zarządzanie nimi.

![Dzieli ostrze](./media/storsimple-virtual-array-manage-shares/shares-blade.png)

Udział składa się z serii atrybutów:

* **Nazwa udziału** — opisowa nazwa, która musi być unikatowa i pomaga zidentyfikować udział.
* **Status** — może być w trybie online lub offline. Jeśli udział jest w trybie offline, użytkownicy udziału nie będą mogli uzyskać do niego dostępu.
* **Typ** — wskazuje, czy udział jest **warstwowy** (domyślnie) czy **przypięty lokalnie.**
* **Pojemność** — określa ilość danych używanych w porównaniu do całkowitej ilości danych, które mogą być przechowywane w udziale.
* **Opis** — opcjonalne ustawienie, które pomaga opisać udział.
* **Uprawnienia** — uprawnienia NTFS do udziału, którymi można zarządzać za pośrednictwem Eksploratora Windows.
* **Kopia zapasowa** — w przypadku tablicy wirtualnej StorSimple wszystkie udziały są automatycznie włączane do tworzenia kopii zapasowych.

![Szczegóły dotyczące akcji](./media/storsimple-virtual-array-manage-shares/share-details.png)

Użyj instrukcji w tym samouczku, aby wykonać następujące zadania:

* Dodawanie udziału
* Modyfikowanie udziału
* Przejmij udział w tryb offline
* Usuwanie udziału

## <a name="add-a-share"></a>Dodawanie udziału

1. W bloku podsumowanie usługi StorSimple kliknij przycisk **+ Dodaj udział** na pasku poleceń. Spowoduje to otwarcie **bloku Dodaj udział.**

    ![Dodaj udział](./media/storsimple-virtual-array-manage-shares/add-share.png)

2. W **bloku Dodaj udział** wykonaj następujące czynności:
   
   1. W polu **Nazwa udziału** wprowadź unikatową nazwę udziału. Nazwa musi być ciągiem zawierającym od 3 do 127 znaków.

   2. Opcjonalny **opis** udziału. Opis pomoże zidentyfikować właścicieli udziałów.

   3. Na liście rozwijanej **Typ** określ, czy chcesz utworzyć udział **warstwowy,** czy **lokalnie przypięty.** W przypadku obciążeń, które wymagają gwarancji lokalnych, niskich opóźnień i wyższej wydajności, wybierz **opcję Lokalnie przypięty udział**. W przypadku wszystkich innych danych wybierz udział **warstwowy.**

   4. W polu **Pojemność** określ rozmiar udziału. Udział warstwowy musi mieć od 500 GB do 20 TB, a udział przypięty lokalnie musi mieć od 50 GB do 2 TB.

   5. W polu **Ustaw domyślne pełne uprawnienia do** przypisania uprawnień do użytkownika lub grupy uzyskującej dostęp do tego udziału. Określ nazwę użytkownika lub grupy _john@contoso.com_ użytkowników w formacie. Zaleca się użycie grupy użytkowników (zamiast jednego użytkownika), aby zezwolić uprawnienia administratorowi na dostęp do tych udziałów. Po przypisaniu uprawnień w tym miejscu możesz następnie zmodyfikować te uprawnienia przy użyciu Eksploratora plików.
3. Po zakończeniu konfigurowania udziału kliknij przycisk **Utwórz**. Zostanie utworzony udział z określonymi ustawieniami i zostanie wyświetlone powiadomienie. Domyślnie kopia zapasowa zostanie włączona dla udziału.
4. Aby potwierdzić, że akcja została pomyślnie utworzona, przejdź do **bloku Shares.** Powinieneś zobaczyć udział na liście.
   
    ![Udostępnij sukces tworzenia](./media/storsimple-virtual-array-manage-shares/share-success.png)

## <a name="modify-a-share"></a>Modyfikowanie udziału

Zmodyfikuj udział, gdy trzeba zmienić opis udziału. Po utworzeniu udziału nie można modyfikować żadnych innych właściwości udziału.

#### <a name="to-modify-a-share"></a>Aby zmodyfikować udział

1. W ustawieniach **Udziały** w bloku podsumowania usługi StorSimple wybierz tablicę wirtualną, na której znajduje się udział, który chcesz zmodyfikować.
2. **Wybierz** udział, aby wyświetlić bieżący opis i zmodyfikować go.
3. Zapisz zmiany, klikając pasek poleceń **Zapisz.** Określone ustawienia zostaną zastosowane, a zostanie wyświetlone powiadomienie.
   
    ![ Edytuj udział](./media/storsimple-virtual-array-manage-shares/share-edit.png)

## <a name="take-a-share-offline"></a>Przejmij udział w tryb offline

Może być konieczne przełączeniu udziału w tryb offline, gdy planujesz go zmodyfikować lub usunąć. Gdy udział jest w trybie offline, nie jest dostępny dla dostępu do odczytu i zapisu. Musisz przetraktować udział w trybie offline na hoście, a także na urządzeniu.

#### <a name="to-take-a-share-offline"></a>Aby przetraktować udostępnianie w trybie offline

1. Upewnij się, że dany udział nie jest używany przed przełączeniem go w tryb offline.
2. Przejmij udział w tablicy w trybie offline, wykonując następujące kroki:
   
    1. W ustawieniach **Udziały** w bloku podsumowania usługi StorSimple wybierz tablicę wirtualną, na której znajduje się udział, który chcesz przełączyć do trybu offline.

    2. **Wybierz** udział i Kliknij **...** (na przemian kliknij prawym przyciskiem myszy w tym wierszu) i z menu kontekstowego wybierz pozycję **Przejmij do trybu offline**.
     
        ![Udział w trybie offline](./media/storsimple-virtual-array-manage-shares/shares-offline.png)

    3. Przejrzyj informacje w bloku **Przejmij tryb offline** i potwierdź akceptację operacji. Kliknij **pozycję Przejmij do trybu offline,** aby przetraktować udział w trybie offline. Zostanie wyświetlone powiadomienie o operacji w toku.

    4. Aby potwierdzić, że akcja została pomyślnie przesuń w tryb offline, przejdź do **bloku Akcje.** Stan udziału powinien być widoczny w trybie offline.

## <a name="delete-a-share"></a>Usuwanie udziału

> [!IMPORTANT]
> Udział można usunąć tylko wtedy, gdy jest w trybie offline.


Wykonaj następujące kroki, aby usunąć udział.

#### <a name="to-delete-a-share"></a>Aby usunąć udział

1. W ustawieniach **Udziały** w bloku podsumowania usługi StorSimple wybierz tablicę wirtualną, na której znajduje się udział, który chcesz usunąć.
2. **Wybierz** udział i Kliknij **...** (na przemian kliknij prawym przyciskiem myszy w tym wierszu) i z menu kontekstowego wybierz polecenie **Usuń**.
   
    ![Usuń udział](./media/storsimple-virtual-array-manage-shares/share-delete.png)
3. Sprawdź stan udziału, który chcesz usunąć. Jeśli udział, który chcesz usunąć, nie jest w trybie offline, należy najpierw przejść do trybu offline. Wykonaj kroki opisane w [obszarze Udostępnianie w trybie offline](#take-a-share-offline).
4. Po wyświetleniu monitu o potwierdzenie w bloku **Usuń** zaakceptuj potwierdzenie i kliknij przycisk **Usuń**. Udział zostanie usunięty, a blok **Shares** zawiera zaktualizowaną listę udziałów w tablicy wirtualnej.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [sklonować udział StorSimple](storsimple-virtual-array-clone.md).

