---
title: Zarządzanie zasadami laboratorium Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak definiowanie zasad laboratorium, takie jak rozmiarów maszyn wirtualnych, maksymalna maszyn wirtualnych na użytkownika i automatyzację zamknięcia.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60636523"
---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>Zarządzanie wszystkimi zasadami dla laboratorium w usłudze Azure DevTest Labs

Azure DevTest Labs umożliwia, decydują o koszcie i minimalizuje odpadów w laboratorium, umożliwiając zarządzanie zasadami (ustawienia) w każdym środowisku laboratoryjnym. W tym artykule omówiono szczegółowo w instrukcje krok po kroku Ustaw każdej zasady.  

## <a name="set-allowed-virtual-machine-sizes"></a>Zestaw dozwolone rozmiary maszyn wirtualnych
Zasad do ustawiania dozwolonych rozmiarów maszyn wirtualnych pomaga zminimalizować straty laboratorium, dzięki czemu można określić rozmiary maszyn wirtualnych, które są dozwolone w środowisku laboratoryjnym. Jeśli zasada ta jest aktywna, tylko rozmiary maszyn wirtualnych z tej listy może służyć do tworzenia maszyn wirtualnych.

1. W [witryny Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040), wybierz laboratorium, a następnie wybierz **konfiguracji i zasad**.

    ![Dostęp do konfiguracji i zasad laboratorium](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. W laboratorium **konfiguracji i zasad** okienku wybierz **dozwolone rozmiary maszyn wirtualnych**.
   
    ![Rozmiary maszyn wirtualnych dozwolony](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)

1. Wybierz **na** można włączyć te zasady i **poza** go wyłączyć.

1. Włączenie tych zasad, wybierz rozmiary maszyn wirtualnych, które mogą być tworzone w środowisku laboratoryjnym.

1. Wybierz pozycję **Zapisz**.

## <a name="set-virtual-machines-per-user"></a>Zestaw maszyn wirtualnych na użytkownika
Zasady dla **maszyn wirtualnych na użytkownika** umożliwia określenie liczby maszyn wirtualnych, które mogą być tworzone przez użytkownika. Jeśli użytkownik próbuje utworzyć lub oświadczeń maszyny Wirtualnej, po osiągnięciu limitu użytkownik, komunikat o błędzie wskazuje, że maszyna wirtualna nie może być utworzony zgłoszone. 

1. W laboratorium **konfiguracji i zasad** okienku wybierz **maszyn wirtualnych na użytkownika**.
   
    ![Maszyny wirtualne na użytkownika](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Wybierz **tak** ograniczyć liczbę maszyn wirtualnych na użytkownika. Jeśli chcesz ograniczyć liczbę maszyn wirtualnych na użytkownika, wybierz opcję **nie**. Jeśli wybierzesz **tak**, podaj wartość liczbową określającą liczbę maszyn wirtualnych, które mogą być tworzone lub zgłoszone przez użytkownika. 

1. Wybierz **tak** Aby ograniczyć liczbę maszyn wirtualnych używających dysków SSD (dysk SSD). Jeśli nie chcesz ograniczyć liczbę maszyn wirtualnych, które można używać dysków SSD, wybierz **nie**. Jeśli wybierzesz **tak**, wprowadź wartość określającą liczbę maszyn wirtualnych, które mogą być tworzone przy użyciu dysków SSD. 

1. Wybierz pozycję **Zapisz**.

## <a name="set-virtual-machines-per-lab"></a>Zestaw maszyn wirtualnych na laboratorium
Zasady dla **maszyn wirtualnych na laboratorium** umożliwia określenie liczby maszyn wirtualnych, które mogą być tworzone w bieżącym środowisku laboratoryjnym. Jeśli użytkownik podejmie próbę utworzenia maszyny Wirtualnej, po osiągnięciu limitu laboratorium, komunikat o błędzie wskazuje, nie można utworzyć maszyny Wirtualnej. 

1. W laboratorium **konfiguracji i zasad** okienku wybierz **maszyn wirtualnych na laboratorium**.
   
    ![Maszyn wirtualnych na laboratorium](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. Wybierz **tak** ograniczyć liczbę maszyn wirtualnych na laboratorium. Jeśli chcesz ograniczyć liczbę maszyn wirtualnych na laboratorium, wybierz opcję **nie**. Jeśli wybierzesz **tak**, podaj wartość liczbową określającą liczbę maszyn wirtualnych, które mogą być tworzone lub zgłoszone przez użytkownika. 

1. Wybierz **tak** Aby ograniczyć liczbę maszyn wirtualnych używających dysków SSD (dysk SSD). Jeśli nie chcesz ograniczyć liczbę maszyn wirtualnych, które można używać dysków SSD, wybierz **nie**. Jeśli wybierzesz **tak**, wprowadź wartość określającą liczbę maszyn wirtualnych, które mogą być tworzone przy użyciu dysków SSD. 

1. Wybierz pozycję **Zapisz**.

## <a name="set-auto-shutdown"></a>Ustaw automatyczne zamknięcie
Zasady automatycznego zamykania, ułatwia minimalizację strat laboratorium, umożliwiając umożliwia określenie czasu zamykania maszyn wirtualnych w tym laboratorium.

1. W laboratorium **konfiguracji i zasad** okienku wybierz **automatyczne zamknięcie**.
   
    ![Automatyczne zamknięcie](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Wybierz **na** można włączyć te zasady i **poza** go wyłączyć.

1. Włączenie tych zasad, określ czas (i strefy czasowej) do zamykania wszystkich maszyn wirtualnych w bieżącym środowisku laboratoryjnym.

1. Określ **tak** lub **nie** dla opcji wysłać powiadomienie z 15 minut przed upływem czasu zamknięcia określona automatycznie. Jeśli wybierzesz **tak**, wprowadź punkt końcowy adres URL elementu webhook lub adres e-mail, określający, którego powiadomienia mają być opublikowane lub wysyłane. Użytkownik odbiera powiadomienie i znajduje się opcja można odroczyć zamknięcia systemu.

   Aby uzyskać więcej informacji na temat elementów webhook, zobacz [Tworzenie elementu webhook lub interfejsu API usługi Azure Function](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Wybierz pozycję **Zapisz**.

Domyślnie po włączeniu te zasady mają zastosowanie do wszystkich maszyn wirtualnych w bieżącym środowisku laboratoryjnym. Aby usunąć ustawienie z określonej maszyny Wirtualnej, otwórz okienko zarządzania maszyny Wirtualnej i zmień jego **automatyczne zamknięcie** ustawienie.

## <a name="set-auto-shutdown-policy"></a>Ustawienie automatycznego zamykania zasad
Jako właściciel laboratorium możesz skonfigurować harmonogram zamykania dla wszystkich maszyn wirtualnych w środowisku laboratoryjnym. Dzięki temu można zmniejszyć koszty z uruchomiono (Bezczynność) maszyn, które nie są używane. Można wymusić zasady zamykanie na wszystkich maszyn wirtualnych laboratorium centralnie, ale także zapisać użytkownicy laboratorium nakład pracy od skonfigurowania harmonogramu dla poszczególnych maszyn. Ta funkcja pozwala ustawić zasady w dogodnym laboratorium od oferty nie kontroli Pełna kontrola dla użytkowników laboratorium. Jako właściciel laboratorium możesz skonfigurować te zasady, wykonując następujące czynności:

1. Na stronie głównej dla swojego laboratorium wybierz **konfiguracji i zasad**.
2. Wybierz **automatyczne zamknięcie zasad** w **harmonogramy** części menu po lewej stronie.
3. Wybierz jedną z opcji. Poniższe sekcje zapewniają więcej szczegółów na temat tych opcji: Ustawianie zasad ma zastosowanie tylko do nowych maszyn wirtualnych utworzonych w laboratorium, a nie już istniejących maszyn wirtualnych. 

    ![Opcje zasad zamykania automatycznie](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Użytkownik ustawia harmonogram i zrezygnować z niego
Jeśli ustawisz środowiska laboratoryjnego do tych zasad, użytkownicy laboratorium można zastąpić, lub zrezygnować z harmonogramu laboratorium. Tej opcji użytkownicy laboratorium przyznaje pełną kontrolę nad harmonogramem zamykania automatycznie ich maszyn wirtualnych. Użytkownicy laboratorium Zobacz nie zmieni się ich maszyn wirtualnych automatycznego zamykania harmonogram strony.

![Opcja zasad zamykania Auto - 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Użytkownik ustawia harmonogram i nie można zrezygnować
Jeśli ustawisz środowiska laboratoryjnego do tych zasad, użytkownicy laboratorium mogą przesłaniać harmonogram laboratorium. Jednak nie mogą zrezygnować z automatycznego zamykania zasad. Ta opcja zapewnia się, że do każdej maszyny w środowisku laboratoryjnym w ramach automatycznego zamykania harmonogramu. Użytkownicy laboratorium można zaktualizować automatycznego zamykania harmonogramu ich maszyn wirtualnych i Konfigurowanie powiadomień zamykanie systemu.

![Opcja zasad zamykania Auto - 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>Użytkownik nie ma kontroli nad harmonogramu ustalonego przez administratora laboratorium
Jeśli ustawisz środowiska laboratoryjnego do tych zasad, użytkownicy laboratorium nie można zastąpić, lub zrezygnować z harmonogramu laboratorium. Ta opcja oferuje administratora laboratorium pełną kontrolę na harmonogram dla każdej maszyny w środowisku laboratoryjnym. Użytkownicy laboratorium można skonfigurować tylko automatyczne zamknięcie powiadomienia dla swoich maszyn wirtualnych.

![Opcja zasad zamykania Auto - 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="set-autostart"></a>Ustaw automatyczne uruchamianie
Zasady autostart pozwala określić, kiedy mają być uruchamiane maszyny wirtualne w bieżącym środowisku laboratoryjnym.  

1. W laboratorium **konfiguracji i zasad** okienku wybierz **Autostart**.
   
    ![Automatyczne uruchamianie](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Wybierz **na** można włączyć te zasady i **poza** go wyłączyć.

3. Włączenie tych zasad, należy określić zaplanowany czas rozpoczęcia, strefę czasową i dni tygodnia, do którego odnosi się czas. 

4. Wybierz pozycję **Zapisz**.

Po włączeniu tych zasad nie są automatycznie stosowane do maszyn wirtualnych w bieżącym środowisku laboratoryjnym. Aby zastosować to ustawienie do określonej maszyny Wirtualnej, otwórz okienko zarządzania maszyny Wirtualnej i zmień jego **Autostart** ustawienie.

## <a name="set-expiration-date"></a>Ustaw datę wygaśnięcia
Można ustawić wygaśnięcie datę, gdy użytkownik [utworzyć maszynę Wirtualną](devtest-lab-add-vm.md). W **Zaawansowane ustawienia**, wybierz ikonę kalendarza, aby określić datę, na którym maszyna wirtualna zostanie automatycznie usunięty. Domyślnie nigdy nie wygasa maszyny Wirtualnej.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Kolejne kroki
Po zdefiniowanych i zastosować różne ustawienia zasad maszyny Wirtualnej dla swojego laboratorium, Oto kilka rzeczy, aby wypróbować następne:

* [Zrozumienie współużytkowanych adresach IP](devtest-lab-shared-ip.md) — wyjaśnia, jak udostępnione adresów IP adresy są używane w usłudze DevTest Labs, aby zminimalizować liczbę publicznych adresów IP, wymagane do połączenia z maszyn wirtualnych laboratorium.
* [Konfigurowanie zarządzania kosztami](devtest-lab-configure-cost-management.md) -ilustruje sposób użycia **trendu kosztów szacowany miesięczny** wykresu  
  Aby wyświetlić bieżący miesiąc szacowany koszt do daty i przewidywany koszt koniec miesiąca.
* [Tworzenie obrazu niestandardowego](devtest-lab-create-template.md) — podczas tworzenia maszyny Wirtualnej, należy określić podstawowy, który może być niestandardowego obrazu lub obrazu z witryny Marketplace. W tym artykule pokazano, jak utworzyć niestandardowy obraz z pliku wirtualnego dysku twardego.
* [Konfigurowanie obrazów z witryny Marketplace](devtest-lab-configure-marketplace-images.md) — usłudze Azure DevTest Labs obsługuje tworzenia maszyn wirtualnych opartych na obrazach portalu Azure Marketplace. W tym artykule pokazano, jak określić, które ewentualnie obrazów portalu Azure Marketplace, mogą być używane podczas tworzenia maszyn wirtualnych w laboratorium.
* [Tworzenie maszyny Wirtualnej w laboratorium](devtest-lab-add-vm.md) -pokazano, jak utworzyć maszynę Wirtualną z obrazu podstawowego (albo niestandardowe lub portalu Marketplace) oraz sposób pracy z artefaktami w maszynie Wirtualnej.

