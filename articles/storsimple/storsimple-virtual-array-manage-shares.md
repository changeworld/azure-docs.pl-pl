---
title: Zarządzanie udziałami macierzy wirtualnej StorSimple | Dokumentacja firmy Microsoft
description: W tym artykule opisano Menedżera urządzeń StorSimple i wyjaśniono, jak przy jego użyciu zarządzać udziałami w rozwiązania StorSimple Virtual Array.
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
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62116870"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-shares-on-the-storsimple-virtual-array"></a>Usługa Menedżer urządzeń StorSimple do zarządzania udziałami w ramach macierzy wirtualnej StorSimple

## <a name="overview"></a>Omówienie

W tym samouczku wyjaśniono, jak tworzyć i zarządzać udziałami na rozwiązania StorSimple Virtual Array za pomocą usługi Menedżer urządzeń StorSimple.

Usługa Menedżer urządzeń StorSimple jest rozszerzeniem w witrynie Azure portal, która umożliwia zarządzanie rozwiązania StorSimple z jednej sieci web interfejsu. Oprócz zarządzania udziały i woluminy, można użyć usługi Menedżer urządzeń StorSimple do wyświetlania i zarządzania urządzeniami, wyświetlanie alertów, Zarządzanie zasadami tworzenia kopii zapasowych i Zarządzanie wykazem kopii zapasowych.

## <a name="share-types"></a>Udostępnianie typów

Udziały StorSimple może być:

* **Przypięty lokalnie**: Dane w tych udziałach pozostaje w obrębie macierzy przez cały czas i nie zostaną przeniesione do chmury.
* **Warstwowe**: Dane w tych udziałów można zostaną przeniesione do chmury. Po utworzeniu udziału warstwowego około 10% wolnego miejsca jest inicjowana na warstwie lokalnej i 90% miejsca są aprowizowane w chmurze. Na przykład, jeśli aprowizowano 1 TB udział 100 GB będzie znajdować się w lokalnej przestrzeni i 900 GB będzie używana w chmurze podczas warstwy danych. Z kolei oznacza to, że jeśli zabraknie całe lokalne miejsce na urządzeniu nie można dostarczać udziału warstwowego (ponieważ 10%, wymagane w warstwie lokalnej nie będą dostępne).

### <a name="provisioned-capacity"></a>Zaprowizowaną pojemnością

Można znaleźć w poniższej tabeli, aby uzyskać maksymalną pojemność aprowizowaną dla każdego typu udziału.

| **Identyfikator limitu** | **Limit** |
| --- | --- |
| Minimalny rozmiar udziału warstwowego |500 GB |
| Maksymalny rozmiar udziału warstwowego |20 TB |
| Minimalny rozmiar udziału przypięty lokalnie |50 GB |
| Maksymalny rozmiar udziału przypięty lokalnie |2 TB |

## <a name="the-shares-blade"></a>Blok akcji

**Udziałów** menu w bloku podsumowania usługi StorSimple Wyświetla listę udziałów magazynu w danej macierzy StorSimple oraz pozwala nimi zarządzać.

![Udziałów bloku](./media/storsimple-virtual-array-manage-shares/shares-blade.png)

Udział składa się z szeregu atrybuty:

* **Nazwa udziału** — nazwę opisową, muszą być unikatowe, która ułatwia zidentyfikowanie udziału.
* **Stan** — może być w trybie online lub offline. Jeśli udział jest w trybie offline użytkownicy udziału nie będą mogli uzyskać do niego dostęp.
* **Typ** — wskazuje, czy udział jest **warstwowy** (ustawienie domyślne) lub **przypięty lokalnie**.
* **Pojemność** — określa ilość danych używanych w porównaniu do całkowitej ilości danych, które mogą być przechowywane w udziale.
* **Opis** — to ustawienie opcjonalne, ułatwiające opisują udziału.
* **Uprawnienia** — uprawnienia NTFS do udziału pliku, którymi można zarządzać za pomocą Eksploratora Windows.
* **Kopii zapasowej** — w przypadku, gdy macierzy wirtualnej StorSimple wszystkie udziały są włączane automatycznie do kopii zapasowej.

![Szczegóły akcji](./media/storsimple-virtual-array-manage-shares/share-details.png)

Skorzystaj z instrukcji w tym samouczku, aby wykonać następujące zadania:

* Dodawanie udziału
* Modyfikowanie udział
* Przełącz do trybu offline udział
* Usuwanie udziału

## <a name="add-a-share"></a>Dodawanie udziału

1. W bloku podsumowania usługi StorSimple kliknij **+ Dodaj udział** na pasku poleceń. Spowoduje to otwarcie **Dodaj udział** bloku.

    ![Dodawanie udziału](./media/storsimple-virtual-array-manage-shares/add-share.png)

2. W **Dodaj udział** blok, wykonaj następujące czynności:
   
   1. W **nazwa udziału** wprowadź unikatową nazwę udziału. Nazwa musi być ciąg, który zawiera 3 do 127 znaków.

   2. Opcjonalny **opis** udziału. Opis pomoże zidentyfikować właścicieli udziału.

   3. W **typu** listę rozwijaną listę, określ, czy należy utworzyć **warstwowy** lub **przypięty lokalnie** udostępniania. Dla obciążeń, które wymagają lokalnych gwarancji, małych opóźnień i większej wydajności, wybierz **przypięty lokalnie udziału**. Dla wszystkich innych danych wybierz **warstwowy** udostępniania.

   4. W **pojemności** Określ rozmiar udziału. Udziału warstwowego musi wynosić od 500 GB do 20 TB i udział przypiętego lokalnie muszą należeć do zakresu od 50 GB i 2 TB.

   5. W **Ustaw domyślne pełne uprawnienia** pola, należy przypisać uprawnienia użytkownika lub grupy, który uzyskuje dostęp do tego udziału. Określ nazwę użytkownika lub grupy użytkowników w _john@contoso.com_ formatu. Zalecamy użycie grupy użytkowników (zamiast jednego użytkownika) umożliwia uprawnień administratora dostępu do tych udziałów. Po przypisaniu uprawnień w tym miejscu możesz następnie zmodyfikować te uprawnienia przy użyciu Eksploratora plików.
3. Po zakończeniu konfigurowania udziału, kliknij przycisk **Utwórz**. Udział zostanie utworzony z określonymi ustawieniami, a następnie zostanie wyświetlone powiadomienie. Domyślnie kopia zapasowa zostanie włączona dla udziału.
4. Aby upewnić się, że udział został pomyślnie utworzony, przejdź do **udziałów** bloku. Powinien zostać wyświetlony udziału na liście.
   
    ![Udostępnij tworzenie sukces](./media/storsimple-virtual-array-manage-shares/share-success.png)

## <a name="modify-a-share"></a>Modyfikowanie udział

Zmodyfikuj udział, gdy trzeba zmienić opis udziału. Nie właściwości udziału można zmodyfikować po utworzeniu udziału.

#### <a name="to-modify-a-share"></a>Aby zmodyfikować udział

1. Z **udziałów** ustawienia w bloku podsumowania usługi StorSimple, wybierz macierz wirtualna, na którym znajduje się udział, chcesz, możesz zmodyfikować.
2. **Wybierz** do udziału, aby wyświetlić bieżące opis i zmodyfikuj go.
3. Zapisz zmiany, klikając **Zapisz** paska poleceń. Swoje określone ustawienia zostaną zastosowane, a następnie zostanie wyświetlone powiadomienie.
   
    ![ Edytuj udziału](./media/storsimple-virtual-array-manage-shares/share-edit.png)

## <a name="take-a-share-offline"></a>Przełącz do trybu offline udział

Może być konieczne przełączyć udział w tryb offline podczas planowania go zmodyfikować lub usunąć plik. Gdy udział jest w trybie offline, nie jest dostępny do odczytu i zapisu. Należy wykonać udział w tryb offline na hoście, a także na urządzeniu.

#### <a name="to-take-a-share-offline"></a>Aby przełączyć udział w trybie offline

1. Upewnij się, że w danym udziale nie jest używany przed przełączeniem jej w tryb offline.
2. Wykonaj udziału w macierzy w trybie offline, wykonując następujące czynności:
   
    1. Z **udziałów** ustawienia w bloku podsumowania usługi StorSimple, wybierz macierz wirtualna, na którym znajduje się udział, chcesz, możesz się przełączyć do trybu offline.

    2. **Wybierz** udziału i kliknij przycisk **...**  (Alternatywnie kliknij prawym przyciskiem myszy w tym wierszu) z menu kontekstowego wybierz **przełączyć do trybu offline**.
     
        ![Udostępnij w trybie offline](./media/storsimple-virtual-array-manage-shares/shares-offline.png)

    3. Zapoznaj się z informacjami w **przełączyć do trybu offline** bloku i upewnij się, zaakceptowania przez operację. Kliknij przycisk **przełączyć do trybu offline** do przełączenia do udziału w tryb offline. Zostanie wyświetlone powiadomienie operacji w toku.

    4. Aby upewnić się, że udział pomyślnie przejdzie w tryb offline, przejdź do **udziałów** bloku. Powinien zostać wyświetlony stan udziału jako offline.

## <a name="delete-a-share"></a>Usuwanie udziału

> [!IMPORTANT]
> Możesz usunąć udział, tylko wtedy, gdy jest w trybie offline.


Wykonaj poniższe kroki, aby usunąć udział.

#### <a name="to-delete-a-share"></a>Usuwanie udziału

1. Z **udziałów** ustawienia w bloku podsumowania usługi StorSimple, wybierz macierz wirtualna, na którym znajduje się udział, którą chcesz usunąć.
2. **Wybierz** udziału i kliknij przycisk **...**  (Alternatywnie kliknij prawym przyciskiem myszy w tym wierszu) z menu kontekstowego wybierz **Usuń**.
   
    ![Usuwanie udziału](./media/storsimple-virtual-array-manage-shares/share-delete.png)
3. Sprawdź stan udział, który chcesz usunąć. Jeśli udział, który chcesz usunąć, nie jest w trybie offline, zrób to w trybie offline najpierw. Postępuj zgodnie z instrukcjami w [przełączyć udział w tryb offline](#take-a-share-offline).
4. Po wyświetleniu monitu o potwierdzenie w **Usuń** bloku Zaakceptuj potwierdzenie i kliknij przycisk **Usuń**. Udostępnianie zostanie usunięte i **udziałów** blok zawiera zaktualizowaną listę udziałów w ramach macierzy wirtualnej.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak [Klonuj udział StorSimple](storsimple-virtual-array-clone.md).

