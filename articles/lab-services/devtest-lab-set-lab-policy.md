---
title: Zarządzanie zasadami laboratoryjnymi w laboratoriach DevTest Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak zdefiniować zasady laboratorium, takie jak rozmiary maszyn wirtualnych, maksymalna liczba maszyn wirtualnych na użytkownika i automatyzacja zamykania systemu.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 7756aa64-49ca-45a0-9f90-0fd101c7be85
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: aa0ffbd69e73ddbef72e0eabf79f2736079c3d23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270721"
---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>Zarządzanie wszystkimi zasadami dla laboratorium w laboratorium usługi Azure DevTest Labs

Laboratorium devtest platformy Azure umożliwia kontrolowanie kosztów i minimalizowanie odpadów w laboratoriach, zarządzając zasadami (ustawieniami) dla każdego laboratorium. W tym artykule wyjaśniono szczegółowo krok po kroku, jak ustawić każdą zasadę.  

## <a name="set-allowed-virtual-machine-sizes"></a>Zestaw dozwolonych rozmiarów maszyn wirtualnych
Zasady ustawiania dozwolonych rozmiarów maszyn wirtualnych pomaga zminimalizować odpady w laboratorium, umożliwiając określenie, które rozmiary maszyn wirtualnych są dozwolone w laboratorium. Jeśli ta zasada jest aktywowana, tylko rozmiary maszyn wirtualnych z tej listy mogą służyć do tworzenia maszyn wirtualnych.

1. W [witrynie Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)wybierz laboratorium, a następnie wybierz **pozycję Konfiguracja i zasady**.

    ![Dostęp do konfiguracji i zasad laboratorium](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. W okienku **Konfiguracja i zasady** laboratorium wybierz pozycję **Dozwolone rozmiary maszyn wirtualnych**.
   
    ![Dozwolone rozmiary maszyn wirtualnych](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)

1. **Wybierz włącz,** aby włączyć tę zasadę, i **Wyłącz,** aby ją wyłączyć.

1. Jeśli ta zasada zostanie włączona, wybierz jeden lub więcej rozmiarów maszyn wirtualnych, które można utworzyć w laboratorium.

1. Wybierz **pozycję Zapisz**.

## <a name="set-virtual-machines-per-user"></a>Ustawianie maszyn wirtualnych na użytkownika
Zasady dotyczące **maszyn wirtualnych na użytkownika** umożliwia określenie liczby maszyn wirtualnych, które mogą być tworzone przez poszczególnych użytkowników. Jeśli użytkownik próbuje utworzyć lub zgłosić maszynę wirtualną po spełnieniu limitu użytkownika, komunikat o błędzie wskazuje, że nie można utworzyć/oświadczeń o maszynie Wirtualnej. 

1. W okienku **Konfiguracja i zasady** laboratorium wybierz pozycję **Maszyny wirtualne na użytkownika**.
   
    ![Maszyny wirtualne na użytkownika](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Wybierz **opcję Tak,** aby ograniczyć liczbę maszyn wirtualnych na użytkownika. Jeśli nie chcesz ograniczać liczby maszyn wirtualnych na użytkownika, wybierz opcję **Nie**. Jeśli wybierzesz **Tak**, wprowadź wartość liczbową wskazującą liczbę maszyn wirtualnych, które mogą zostać utworzone lub zgłoszone przez użytkownika. 

1. Wybierz **tak,** aby ograniczyć liczbę maszyn wirtualnych, które mogą używać dysku SSD (dysku PÓŁPRZEWODNIKOWEGO). Jeśli nie chcesz ograniczać liczby maszyn wirtualnych, które mogą korzystać z ssd, wybierz opcję **Nie**. Jeśli wybierzesz **Tak**, wprowadź wartość wskazującą liczbę maszyn wirtualnych, które mogą być tworzone przy użyciu ssd. 

1. Wybierz **pozycję Zapisz**.

## <a name="set-virtual-machines-per-lab"></a>Ustawianie maszyn wirtualnych na laboratorium
Zasady dla **maszyn wirtualnych na laboratorium** pozwala określić liczbę maszyn wirtualnych, które mogą być tworzone dla bieżącego laboratorium. Jeśli użytkownik próbuje utworzyć maszynę wirtualną po osiągnięciu limitu laboratorium, komunikat o błędzie wskazuje, że nie można utworzyć maszyny Wirtualnej. 

1. W okienku **Konfiguracja i zasady** laboratorium wybierz pozycję **Maszyny wirtualne na laboratorium**.
   
    ![Maszyny wirtualne na laboratorium](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. Wybierz **opcję Tak,** aby ograniczyć liczbę maszyn wirtualnych w laboratorium. Jeśli nie chcesz ograniczać liczby maszyn wirtualnych w laboratorium, wybierz opcję **Nie**. Jeśli wybierzesz **Tak**, wprowadź wartość liczbową wskazującą liczbę maszyn wirtualnych, które mogą zostać utworzone lub zgłoszone przez użytkownika. 

1. Wybierz **tak,** aby ograniczyć liczbę maszyn wirtualnych, które mogą używać dysku SSD (dysku PÓŁPRZEWODNIKOWEGO). Jeśli nie chcesz ograniczać liczby maszyn wirtualnych, które mogą korzystać z ssd, wybierz opcję **Nie**. Jeśli wybierzesz **Tak**, wprowadź wartość wskazującą liczbę maszyn wirtualnych, które mogą być tworzone przy użyciu ssd. 

1. Wybierz **pozycję Zapisz**.

## <a name="set-auto-shutdown"></a>Ustawianie automatycznego zamykania
Zasady automatycznego zamykania pomaga zminimalizować straty w laboratorium, umożliwiając określenie czasu zamknięcia maszyn wirtualnych w tym laboratorium.

1. W okienku **Konfiguracja i zasady** laboratorium wybierz pozycję **Automatyczne zamykanie**.
   
    ![Automatyczne wyłączanie](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. **Wybierz włącz,** aby włączyć tę zasadę, i **Wyłącz,** aby ją wyłączyć.

1. Jeśli ta zasada zostanie włączona, określ czas (i strefę czasową) do zamknięcia wszystkich maszyn wirtualnych w bieżącym laboratorium.

1. Określ **tak** lub **nie** dla opcji wysyłania powiadomień 15 minut przed określonym czasem automatycznego zamykania. Jeśli wybierzesz **opcję Tak,** wpisz punkt końcowy adresu URL elementu webhook lub adres e-mail określający miejsce, w którym ma zostać opublikowane lub wysłane powiadomienie. Użytkownik otrzymuje powiadomienie i ma możliwość odroczenia zamknięcia systemu.

   Aby uzyskać więcej informacji na temat elementów webhook, zobacz [Tworzenie elementu webhook lub funkcji platformy Azure interfejsu API](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Wybierz **pozycję Zapisz**.

Domyślnie po włączeniu ta zasada ma zastosowanie do wszystkich maszyn wirtualnych w bieżącym laboratorium. Aby usunąć to ustawienie z określonej maszyny Wirtualnej, otwórz okienko zarządzania maszyny Wirtualnej i zmień jego ustawienie **automatycznego zamykania.**

## <a name="set-auto-shutdown-policy"></a>Ustawianie zasad automatycznego zamykania
Jako właściciel laboratorium możesz skonfigurować harmonogram zamykania dla wszystkich maszyn wirtualnych w laboratorium. W ten sposób można zaoszczędzić koszty z uruchomionych maszyn, które nie są używane (bezczynny). Zasady zamykania można wymusić na wszystkich maszynach wirtualnych w laboratorium centralnie, ale także zapisać użytkownikom laboratorium wysiłek z konfigurowania harmonogramu dla ich poszczególnych komputerów. Ta funkcja umożliwia ustawienie zasad w harmonogramie laboratorium, począwszy od oferowania żadnej kontroli do pełnej kontroli, dla użytkowników laboratorium. Jako właściciel laboratorium możesz skonfigurować tę zasadę, wykonując następujące kroki:

1. Na stronie głównej laboratorium wybierz pozycję **Konfiguracja i zasady**.
2. Wybierz **pozycję Zasady automatycznego zamykania** w sekcji **Harmonogramy** w menu po lewej stronie.
3. Wybierz jedną z opcji. W poniższych sekcjach podano więcej szczegółów na temat tych opcji: Zasady zestawu dotyczą tylko nowych maszyn wirtualnych utworzonych w laboratorium, a nie już istniejących maszyn wirtualnych. 

    ![Opcje zasad automatycznego zamykania](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Użytkownik ustala harmonogram i może zrezygnować
Jeśli ustawisz laboratorium do tej zasady, użytkownicy laboratorium można zastąpić lub zrezygnować z harmonogramu laboratorium. Ta opcja zapewnia użytkownikom laboratorium pełną kontrolę nad harmonogramem automatycznego zamykania ich maszyn wirtualnych. Użytkownicy laboratorium nie widzą żadnych zmian na stronie harmonogramu automatycznego zamykania maszyn wirtualnych.

![Opcja zasad automatycznego zamykania - 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Użytkownik ustawia harmonogram i nie może zrezygnować
Jeśli ustawisz laboratorium do tej zasady, użytkownicy laboratorium można zastąpić harmonogram laboratorium. Nie mogą jednak zrezygnować z zasad automatycznego zamykania. Ta opcja zapewnia, że każdy komputer w laboratorium jest w harmonogramie automatycznego zamykania. Użytkownicy laboratorium mogą aktualizować harmonogram automatycznego zamykania swoich maszyn wirtualnych i konfigurowania powiadomień o zamykaniu.

![Opcja zasad automatycznego zamykania - 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>Użytkownik nie ma kontroli nad harmonogramem ustawionym przez administratora laboratorium
Jeśli ustawisz laboratorium na tę zasadę, użytkownicy laboratorium nie mogą zastąpić ani zrezygnować z harmonogramu laboratorium. Ta opcja oferuje administratorowi laboratorium pełną kontrolę zgodnie z harmonogramem dla każdego komputera w laboratorium. Użytkownicy laboratorium mogą konfigurowania tylko powiadomień o automatycznym zamykaniu dla swoich maszyn wirtualnych.

![Opcja zasad automatycznego zamykania - 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="set-autostart"></a>Ustawianie autostartu
Zasady autostartu umożliwia określenie, kiedy maszyny wirtualne w bieżącym laboratorium powinny być uruchamiane.  

1. W okienku **Konfiguracja i zasady** laboratorium wybierz pozycję **Autostart**.
   
    ![Automatyczne uruchamianie](./media/devtest-lab-set-lab-policy/auto-start.png)

2. **Wybierz włącz,** aby włączyć tę zasadę, i **Wyłącz,** aby ją wyłączyć.

3. Jeśli ta zasada zostanie włączona, określ zaplanowaną godzinę rozpoczęcia, strefę czasową i dni tygodnia, dla których obowiązuje czas. 

4. Wybierz **pozycję Zapisz**.

Po włączeniu ta zasada nie jest automatycznie stosowana do żadnych maszyn wirtualnych w bieżącym laboratorium. Aby zastosować to ustawienie do określonej maszyny Wirtualnej, otwórz okienko zarządzania maszyny Wirtualnej i zmień jego ustawienie **Autostart.**

## <a name="set-expiration-date"></a>Ustawianie daty wygaśnięcia
Datę wygaśnięcia można ustawić podczas [tworzenia maszyny Wirtualnej](devtest-lab-add-vm.md). W **obszarze Ustawienia zaawansowane**wybierz ikonę kalendarza, aby określić datę automatycznego usunięcia maszyny wirtualnej. Domyślnie maszyna wirtualna nigdy nie wygasa.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Następne kroki
Po zdefiniowaniu i zastosowaniu różnych ustawień zasad maszyn wirtualnych w laboratorium, oto kilka rzeczy, które należy wypróbować dalej:

* [Poznaj udostępnione adresy IP](devtest-lab-shared-ip.md) — wyjaśniono, jak udostępnione adresy IP są używane w DevTest Labs w celu zminimalizowania liczby publicznych adresów IP wymaganych do nawiązania połączenia z maszynami wirtualnymi w laboratorium.
* [Konfigurowanie zarządzania kosztami](devtest-lab-configure-cost-management.md) — ilustruje sposób korzystania z wykresu **Miesięcznego trendu szacowanego kosztu**  
  , aby wyświetlić szacowany koszt bieżącego miesiąca i przewidywany koszt na koniec miesiąca.
* [Tworzenie obrazu niestandardowego](devtest-lab-create-template.md) — podczas tworzenia maszyny Wirtualnej należy określić bazę, która może być obrazem niestandardowym lub obrazem w portalu Marketplace. W tym artykule pokazano, jak utworzyć obraz niestandardowy z pliku VHD.
* [Konfigurowanie obrazów portalu Marketplace](devtest-lab-configure-marketplace-images.md) — laboratorium devtest platformy Azure obsługuje tworzenie maszyn wirtualnych na podstawie obrazów w portalu Azure Marketplace. W tym artykule pokazano, jak określić, które, jeśli istnieją, obrazy portalu Azure Marketplace mogą być używane podczas tworzenia maszyn wirtualnych w laboratorium.
* [Tworzenie maszyny Wirtualnej w laboratorium](devtest-lab-add-vm.md) — ilustruje sposób tworzenia maszyny Wirtualnej z obrazu podstawowego (niestandardowego lub marketplace) i jak pracować z artefaktami na maszynie wirtualnej.

