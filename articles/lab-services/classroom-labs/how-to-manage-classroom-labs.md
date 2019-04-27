---
title: Zarządzanie laboratoriami na potrzeby zajęć w usłudze Azure Lab Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć i skonfigurować laboratorium na potrzeby zajęć, wyświetlać wszystkie laboratorium na potrzeby zajęć, Udostępnij link rejestracji użytkownika laboratorium lub usuwanie laboratorium.
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
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: 332f899f3502f34e46b4f158a6980dc96248140e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60703129"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Zarządzanie laboratoriami na potrzeby zajęć w usłudze Azure Lab Services 
W tym artykule opisano sposób tworzenia i usuwania laboratorium na potrzeby zajęć. On również dowiesz się, jak wyświetlić wszystkie laboratorium na potrzeby zajęć na koncie laboratorium. 

## <a name="prerequisites"></a>Wymagania wstępne
Aby skonfigurować laboratorium na potrzeby zajęć w ramach konta laboratorium, musisz być członkiem roli **Twórca laboratorium** na koncie laboratorium. Konto, którego użyto do utworzenia konta laboratorium, jest automatycznie dodawane do tej roli. Właściciel laboratorium może dodać innych użytkowników do roli Twórca laboratorium, wykonując kroki opisane w następującym artykule: [Dodawanie użytkownika do roli twórcy laboratorium](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Tworzenie laboratorium na potrzeby zajęć

1. Przejdź do [witryny internetowej usługi Azure Lab Services](https://labs.azure.com). Należy pamiętać, że program Internet Explorer 11 nie jest jeszcze obsługiwany. 
2. Wybierz **Zaloguj**. Wybierz lub wprowadź **identyfikator użytkownika** czyli członkiem **twórca laboratorium** roli w środowisku laboratoryjnym konta, a następnie wprowadź hasło. Usługa Azure Lab Services obsługuje konta organizacji i konta Microsoft. 
3. W oknie **Nowe laboratorium** wykonaj następujące czynności: 
    1. Określ **nazwę** dla swojego laboratorium. 
    2. Określ maksymalną **liczbę maszyn wirtualnych** w laboratorium. Można zwiększyć lub zmniejszyć liczbę maszyn wirtualnych w środowisku laboratoryjnym później. 
    6. Wybierz pozycję **Zapisz**.

        ![Tworzenie laboratorium na potrzeby zajęć](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Na stronie **Wybór specyfikacji maszyny wirtualnej** wykonaj następujące czynności:
    1. Wybierz **rozmiar** maszyn wirtualnych tworzonych w laboratorium. Obecnie **małych**, **średni**, **dużych**, i **procesora GPU** rozmiarów są dozwolone.
    2. Wybierz **region**, w którym mają być tworzone maszyny wirtualne. 
    3. Wybierz **obraz maszyny wirtualnej**, którego chcesz używać do tworzenia maszyn wirtualnych w laboratorium. Jeśli wybierzesz obraz systemu Linux, zobaczysz opcję, aby włączyć połączenie pulpitu zdalnego dla niego. Aby uzyskać więcej informacji, zobacz [Włączanie połączeń usług pulpitu zdalnego dla systemu Linux](how-to-enable-remote-desktop-linux.md).
    4. Wybierz opcję **Dalej**.

        ![Określanie specyfikacji maszyny wirtualnej](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. Na stronie **Ustawianie poświadczeń** określ domyślne poświadczenia dla wszystkich maszyn wirtualnych w laboratorium. 
    1. Określ **nazwę użytkownika** dla wszystkich maszyn wirtualnych w laboratorium.
    2. Podaj **hasło** użytkownika. 

        > [!IMPORTANT]
        > Zanotuj nazwę użytkownika i hasło. Nie zostaną one ponownie wyświetlone.
    3. Wybierz pozycję **Utwórz**. 

        ![Ustawianie poświadczeń](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. Na stronie **Konfigurowanie szablonu** zostanie wyświetlony stan procesu tworzenia laboratorium. Tworzenie szablonu w laboratorium trwa maksymalnie 20 minut. Szablon w laboratorium to podstawowy obraz maszyny wirtualnej, który służy do tworzenia maszyn wirtualnych wszystkich użytkowników. Możesz skonfigurować maszynę wirtualną szablonu dokładnie tak jak chcesz dla użytkowników laboratorium.  

    ![Konfigurowanie szablonu](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Po zakończeniu konfiguracji szablonu zostanie wyświetlona następująca strona: 

    ![Strona Konfigurowanie szablonu po zakończeniu konfiguracji](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. Poniższe kroki są opcjonalne w tym samouczku: 
    1. Uruchom maszynę wirtualną szablonu, wybierając pozycję **Uruchom**.
    2. Nawiąż połączenie z maszyną wirtualną szablonu, wybierając pozycję **Połącz**. Jeśli szablon maszyny Wirtualnej systemu Linux, wybierz, czy chcesz nawiązać połączenie przy użyciu klienta SSH lub RDP (jeśli został włączony protokół RDP).
    3. Zainstaluj i skonfiguruj oprogramowania na maszynie wirtualnej szablonu. 
    4. **Zatrzymaj** maszynę wirtualną.  
    5. Wprowadź **opis** szablonu.

        ![Przycisk Dalej na stronie Konfigurowanie szablonu](../media/tutorial-setup-classroom-lab/configure-template-next.png)
9. Wybierz przycisk **Dalej** na stronie szablonu. 
10. Na stronie **Publikowanie szablonu** wykonaj następujące czynności. 
    1. Aby natychmiast opublikować szablon, zaznacz pole wyboru *Rozumiem, że po opublikowaniu szablonu nie można go modyfikować. Ten proces można wykonać tylko raz i może on potrwać do godziny* i wybierz polecenie **Publikuj**.  Opublikuj szablon, aby wystąpienia maszyny wirtualnej szablonu były dostępne dla użytkowników laboratorium.

        > [!WARNING]
        > Nie można cofnąć publikowania szablonu. 
    2. Aby opublikować później, wybierz pozycję **Zachowaj na później**. Maszynę wirtualną szablonu można opublikować po zakończeniu działania kreatora. Szczegółowe informacje na temat konfigurowania i publikowania, gdy kreator zakończy pracę, można znaleźć szczegółowe informacje na temat konfigurowania i publikowania, gdy kreator zakończy pracę, zobacz publikowania w temacie szablon [sposób zarządzania laboratorium na potrzeby zajęć](how-to-manage-classroom-labs.md) artykułu.

        ![Publikowanie szablonu](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Zostanie wyświetlony **postęp publikowania** szablonu. Ten proces może potrwać do godziny. 

    ![Publikowanie szablonu — postęp](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Po pomyślnym opublikowaniu szablonu zostanie wyświetlona następująca strona. Wybierz pozycję **Done** (Gotowe).

    ![Publikowanie szablonu — powodzenie](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Zostanie wyświetlony **pulpit nawigacyjny** laboratorium. 
    
    ![Pulpit nawigacyjny laboratorium na potrzeby zajęć](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Przejdź do strony **Maszyny wirtualne** i sprawdź, czy są widoczne maszyny wirtualne o stanie **Nie przypisano**. Te maszyny wirtualne nie zostały jeszcze przypisane do uczniów. Powinny mieć stan **Zatrzymano**. Z poziomu tej strony możesz uruchomić maszynę wirtualną ucznia, połączyć się z maszyną wirtualną, zatrzymać maszynę wirtualną i usunąć maszynę wirtualną. Maszyny wirtualne możesz uruchomić na tej stronie lub pozwolić, aby zrobili to uczniowie. 

    ![Maszyny wirtualne w stanie Zatrzymano](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="view-all-classroom-labs"></a>Wyświetl wszystkie laboratorium na potrzeby zajęć
1. Przejdź do [portalu usługi Azure Lab Services](https://labs.azure.com).
2. Wybierz **Zaloguj**. Wybierz lub wprowadź **identyfikator użytkownika** czyli członkiem **twórca laboratorium** roli w środowisku laboratoryjnym konta, a następnie wprowadź hasło. Usługa Azure Lab Services obsługuje konta organizacji i konta Microsoft. 
3. Upewnij się, czy widzisz wszystkie laboratoriów w ramach laboratorium wybranego konta. 

    ![Wszystkie warsztaty](../media/how-to-manage-classroom-labs/all-labs.png)
3. Użyj listy rozwijanej u góry, aby wybrać konto różnych laboratorium. Zostanie wyświetlony laboratoriów w ramach laboratorium wybranego konta. 

## <a name="delete-a-classroom-lab"></a>Usuń laboratorium na potrzeby zajęć
1. Na kafelku dla laboratorium wybierz wielokropek (...) w prawym górnym rogu. 

    ![Wybieranie laboratorium](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. Wybierz pozycję **Usuń**. 

    ![Przycisk Usuń](../media/how-to-manage-classroom-labs/delete-button.png)
3. Na **Delete lab** okno dialogowe, wybierz opcję **Usuń**. 

    ![Okno dialogowe Usuwanie](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)

## <a name="switch-to-another-classroom-lab"></a>Przełącz się do innego laboratorium na potrzeby zajęć
Aby przełączyć się do innego laboratorium na potrzeby zajęć z bieżącej, wybierz na liście rozwijanej laboratoriów w ramach konta laboratorium u góry.

![Z listy rozwijanej u góry wybierz laboratorium](../media/how-to-manage-classroom-labs/switch-lab.png)


## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły:

- [As a lab owner, set up and publish templates (Konfigurowanie i tworzenie szablonów jako właściciel laboratorium)](how-to-create-manage-template.md)
- [As a lab owner, configure and control usage of a lab (Konfigurowanie i kontrolowanie użycia laboratorium jako właściciel laboratorium)](how-to-configure-student-usage.md)
- [Jako użytkownik laboratorium dostęp do laboratoriów na potrzeby zajęć](how-to-use-classroom-lab.md)

