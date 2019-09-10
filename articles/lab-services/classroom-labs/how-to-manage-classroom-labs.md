---
title: Zarządzanie laboratoriami klas w Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak utworzyć i skonfigurować laboratorium zajęć, wyświetlić wszystkie laboratoria zajęć, udostępnić link rejestracji użytkownikowi laboratorium lub usunąć laboratorium.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2019
ms.author: spelluru
ms.openlocfilehash: 1f9cb82abd5bc0823f5e7bc23fe437007bccc8e0
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70873584"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Zarządzanie pracownią w Azure Lab Services 
W tym artykule opisano sposób tworzenia i usuwania laboratorium zajęć. Przedstawiono w nim również sposób wyświetlania wszystkich laboratoriów zajęć na koncie laboratorium. 

## <a name="prerequisites"></a>Wymagania wstępne
Aby skonfigurować laboratorium na potrzeby zajęć w ramach konta laboratorium, musisz być członkiem roli **Twórca laboratorium** na koncie laboratorium. Konto, którego użyto do utworzenia konta laboratorium, jest automatycznie dodawane do tej roli. Właściciel laboratorium może dodać innych użytkowników do roli Twórca laboratorium, wykonując kroki opisane w następującym artykule: [Dodawanie użytkownika do roli twórcy laboratorium](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Tworzenie laboratorium na potrzeby zajęć

1. Przejdź do [witryny internetowej usługi Azure Lab Services](https://labs.azure.com). Należy pamiętać, że program Internet Explorer 11 nie jest jeszcze obsługiwany. 
2. Wybierz pozycję **Zaloguj**. Wybierz lub wprowadź **Identyfikator użytkownika** , który jest członkiem roli **twórca laboratorium** na koncie laboratorium, a następnie wprowadź hasło. Usługa Azure Lab Services obsługuje konta organizacji i konta Microsoft. 
3. W oknie **Nowe laboratorium** wykonaj następujące czynności: 
    1. Określ **nazwę** dla swojego laboratorium. 
    2. Określ maksymalną **liczbę maszyn wirtualnych** w laboratorium. Możesz później zwiększyć lub zmniejszyć liczbę maszyn wirtualnych w środowisku laboratoryjnym. 
    6. Wybierz pozycję **Zapisz**.

        ![Tworzenie laboratorium na potrzeby zajęć](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Na stronie **Wybór specyfikacji maszyny wirtualnej** wykonaj następujące czynności:
    1. Wybierz **rozmiar** maszyn wirtualnych tworzonych w laboratorium. Dozwolone są obecnie, **małe**, **średnie**, **średnie (Wirtualizacja)** , **duże**i **GPU** . Aby uzyskać szczegółowe informacje, zobacz sekcję [rozmiary maszyn wirtualnych](#vm-sizes) .
    1. Wybierz **region**, w którym mają być tworzone maszyny wirtualne. 
    1. Wybierz **obraz maszyny wirtualnej**, którego chcesz używać do tworzenia maszyn wirtualnych w laboratorium. W przypadku wybrania obrazu systemu Linux zostanie wyświetlona opcja włączenia dla niego połączenia pulpitu zdalnego. Aby uzyskać szczegółowe informacje, zobacz [Włączanie usługi Podłączanie pulpitu zdalnego dla systemu Linux](how-to-enable-remote-desktop-linux.md).
    1. Wybierz opcję **Dalej**.

        ![Określanie specyfikacji maszyny wirtualnej](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. Na stronie **Ustawianie poświadczeń** określ domyślne poświadczenia dla wszystkich maszyn wirtualnych w laboratorium. 
    1. Określ **nazwę użytkownika** dla wszystkich maszyn wirtualnych w laboratorium.
    2. Podaj **hasło** użytkownika. 

        > [!IMPORTANT]
        > Zanotuj nazwę użytkownika i hasło. Nie zostaną one ponownie wyświetlone.
    3. Wyłącz opcję **Użyj tego samego hasła dla wszystkich maszyn wirtualnych** , jeśli chcesz, aby studenci ustawili swoje hasła. Ten krok jest **opcjonalny**. 

        Nauczyciel może wybrać użycie tego samego hasła dla wszystkich maszyn wirtualnych w laboratorium lub zezwolić uczniom na ustawianie haseł dla swoich maszyn wirtualnych. Domyślnie to ustawienie jest włączone dla wszystkich obrazów systemów Windows i Linux z wyjątkiem Ubuntu. W przypadku wybrania maszyny wirtualnej **Ubuntu** to ustawienie jest wyłączone, więc uczniowie będą monitowani o ustawienie hasła podczas pierwszego logowania.
    1. Wybierz pozycję **Utwórz**. 

        ![Ustaw poświadczenia](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. Na stronie **Konfigurowanie szablonu** zostanie wyświetlony stan procesu tworzenia laboratorium. Tworzenie szablonu w laboratorium trwa maksymalnie 20 minut. Szablon w laboratorium to podstawowy obraz maszyny wirtualnej, który służy do tworzenia maszyn wirtualnych wszystkich użytkowników. Możesz skonfigurować maszynę wirtualną szablonu dokładnie tak jak chcesz dla użytkowników laboratorium.  

    ![Konfigurowanie szablonu](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Po zakończeniu konfiguracji szablonu zostanie wyświetlona następująca strona: 

    ![Strona Konfigurowanie szablonu po zakończeniu konfiguracji](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. Poniższe kroki są opcjonalne w tym samouczku: 
    2. Nawiąż połączenie z maszyną wirtualną szablonu, wybierając pozycję **Połącz**. Jeśli jest to maszyna wirtualna z szablonem systemu Linux, wybierz, czy chcesz nawiązać połączenie przy użyciu protokołu SSH, czy RDP (Jeśli włączono protokół RDP).
    1. Wybierz pozycję **zresetuj hasło** , aby zresetować hasło dla maszyny wirtualnej. 
    1. Zainstaluj i skonfiguruj oprogramowania na maszynie wirtualnej szablonu. 
    1. **Zatrzymaj** maszynę wirtualną.  
    1. Wprowadź **opis** szablonu.
9. Wybierz przycisk **Dalej** na stronie szablonu. 
10. Na stronie **Publikowanie szablonu** wykonaj następujące czynności. 
    1. Aby natychmiast opublikować szablon, zaznacz pole wyboru *Rozumiem, że po opublikowaniu szablonu nie można go modyfikować. Ten proces można wykonać tylko raz i może on potrwać do godziny* i wybierz polecenie **Publikuj**.  Opublikuj szablon, aby wystąpienia maszyny wirtualnej szablonu były dostępne dla użytkowników laboratorium.

        > [!WARNING]
        > Nie można cofnąć publikowania szablonu. 
    2. Aby opublikować później, wybierz pozycję **Zachowaj na później**. Maszynę wirtualną szablonu można opublikować po zakończeniu działania kreatora. Aby uzyskać szczegółowe informacje na temat konfigurowania i publikowania po zakończeniu pracy kreatora, zobacz temat aby uzyskać szczegółowe informacje na temat konfigurowania i publikowania po zakończeniu działania kreatora, zobacz sekcję publikowanie szablonu w artykule [jak zarządzać z pomocą laboratoriów zajęć](how-to-manage-classroom-labs.md) .

        ![Publikowanie szablonu](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Zostanie wyświetlony **postęp publikowania** szablonu. Ten proces może potrwać do godziny. 

    ![Publikowanie szablonu — postęp](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Po pomyślnym opublikowaniu szablonu zostanie wyświetlona następująca strona. Wybierz pozycję **Done** (Gotowe).

    ![Publikowanie szablonu — powodzenie](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Zostanie wyświetlony **pulpit nawigacyjny** laboratorium. 
    
    ![Pulpit nawigacyjny laboratorium na potrzeby zajęć](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Przejdź do strony **Maszyny wirtualne** i sprawdź, czy są widoczne maszyny wirtualne o stanie **Nie przypisano**. Te maszyny wirtualne nie zostały jeszcze przypisane do uczniów. Powinny mieć stan **Zatrzymano**. Z poziomu tej strony możesz uruchomić maszynę wirtualną ucznia, połączyć się z maszyną wirtualną, zatrzymać maszynę wirtualną i usunąć maszynę wirtualną. Maszyny wirtualne możesz uruchomić na tej stronie lub pozwolić, aby zrobili to uczniowie. 

    ![Maszyny wirtualne w stanie Zatrzymano](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

### <a name="vm-sizes"></a>Rozmiary maszyn wirtualnych  

| Size | Rdzenie | Pamięć RAM | Opis | 
| ---- | ----- | --- | ----------- | 
| Małe | 2 | 3,5 GB | Ten rozmiar najlepiej nadaje się w przypadku wiersza polecenia, otwierania przeglądarki sieci Web, serwerów sieci Web o małym ruchu, małych i średnich baz danych. |
| Średni | 4 | 7 GB | Ten rozmiar jest najlepiej dostosowany do relacyjnych baz danych, buforowania w pamięci i analiz | 
| Średni (Wirtualizacja zagnieżdżona) | 4 | 16 GB | Ten rozmiar jest najlepiej dostosowany do relacyjnych baz danych, buforowania w pamięci i analizy. Ten rozmiar obsługuje również wirtualizację zagnieżdżoną. <p>Tego rozmiaru można używać w scenariuszach, w których każdy student potrzebuje wielu maszyn wirtualnych. Nauczyciele mogą używać wirtualizacji zagnieżdżonej, aby skonfigurować kilka małych zagnieżdżonych maszyn wirtualnych w ramach maszyny wirtualnej. </p> |
| Duże | 8 | 32 GB | Ten rozmiar najlepiej nadaje się w przypadku aplikacji wymagających szybszych procesorów CPU, lepszej wydajności dysków lokalnych, dużych baz danych i dużych pamięci podręcznych pamięci. Ten rozmiar obsługuje również wirtualizację zagnieżdżoną |  
| Mały procesor GPU (wizualizacja) | 6 | 56 GB | Ten rozmiar najlepiej nadaje się do zdalnej wizualizacji, przesyłania strumieniowego, gier, kodowania przy użyciu struktur, takich jak OpenGL i DirectX. | 
| Mały procesor GPU (obliczenia) | 6 | 56 GB | Ten rozmiar najlepiej nadaje się w przypadku aplikacji intensywnie korzystających z mocy obliczeniowej i intensywnie korzystających z sieci, takich jak sztuczne inteligencje i aplikacje | 
| Średni procesor GPU (wizualizacja) | 12 | 112 GB | Ten rozmiar najlepiej nadaje się do zdalnej wizualizacji, przesyłania strumieniowego, gier, kodowania przy użyciu struktur, takich jak OpenGL i DirectX. | 

## <a name="view-all-classroom-labs"></a>Wyświetl wszystkie pracownice klasy
1. Przejdź do [portalu Azure Lab Services](https://labs.azure.com).
2. Wybierz pozycję **Zaloguj**. Wybierz lub wprowadź **Identyfikator użytkownika** , który jest członkiem roli **twórca laboratorium** na koncie laboratorium, a następnie wprowadź hasło. Usługa Azure Lab Services obsługuje konta organizacji i konta Microsoft. 
3. Upewnij się, że wszystkie laboratoria zostały wyświetlone na wybranym koncie laboratorium. 

    ![Wszystkie laboratoria](../media/how-to-manage-classroom-labs/all-labs.png)
3. Użyj listy rozwijanej u góry, aby wybrać inne konto laboratorium. Zobaczysz laboratoria na wybranym koncie laboratorium. 

## <a name="delete-a-classroom-lab"></a>Usuwanie laboratorium zajęć
1. Na kafelku laboratorium wybierz trzy kropki (...) w rogu. 

    ![Wybieranie laboratorium](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. Wybierz pozycję **Usuń**. 

    ![Usuwanie przycisku](../media/how-to-manage-classroom-labs/delete-button.png)
3. W oknie dialogowym **usuwanie laboratorium** wybierz pozycję **Usuń**. 

    ![Usuń okno dialogowe](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)

## <a name="switch-to-another-classroom-lab"></a>Przełącz do innego laboratorium zajęć
Aby przełączyć się do innego laboratorium zajęć z bieżącego, wybierz listę rozwijaną laboratoriów na koncie laboratorium w górnej części.

![Wybierz z listy rozwijanej Lab (laboratorium) w górnej części](../media/how-to-manage-classroom-labs/switch-lab.png)


## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [As a lab owner, set up and publish templates (Konfigurowanie i tworzenie szablonów jako właściciel laboratorium)](how-to-create-manage-template.md)
- [As a lab owner, configure and control usage of a lab (Konfigurowanie i kontrolowanie użycia laboratorium jako właściciel laboratorium)](how-to-configure-student-usage.md)
- [Jako użytkownik laboratorium Uzyskuj dostęp do laboratoriów zajęć](how-to-use-classroom-lab.md)

