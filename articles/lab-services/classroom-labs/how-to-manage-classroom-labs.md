---
title: Zarządzanie laboratoriami w klasie w usługach Azure Lab | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć i skonfigurować laboratorium w klasie, wyświetlić wszystkie laboratoria w klasie, udostępnić łącze rejestracyjne użytkownikowi laboratorium lub usunąć laboratorium.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/10/2020
ms.author: spelluru
ms.openlocfilehash: 46c53c99c12ade986ab913bf013b652a931a4d22
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257746"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Zarządzanie laboratoriami w klasie w usługach Azure Lab Services 
W tym artykule opisano sposób tworzenia i usuwania laboratorium w klasie. Pokazuje również, jak wyświetlić wszystkie laboratoria w klasie na koncie laboratorium. 

## <a name="prerequisites"></a>Wymagania wstępne
Aby skonfigurować laboratorium na potrzeby zajęć w ramach konta laboratorium, musisz być członkiem roli **Twórca laboratorium** na koncie laboratorium. Konto, którego użyto do utworzenia konta laboratorium, jest automatycznie dodawane do tej roli. Właściciel laboratorium może dodać innych użytkowników do roli Twórca laboratorium, wykonując kroki opisane w następującym artykule: [Dodawanie użytkownika do roli twórcy laboratorium](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Tworzenie laboratorium na potrzeby zajęć

1. Przejdź do [witryny internetowej usługi Azure Lab Services](https://labs.azure.com). Program Internet Explorer 11 nie jest jeszcze obsługiwany. 
2. Wybierz pozycję **Zaloguj się** i wprowadź swoje poświadczenia. Wybierz lub wprowadź **identyfikator użytkownika,** który jest członkiem roli **Twórca laboratorium** na koncie laboratorium, i wprowadź hasło. Usługa Azure Lab Services obsługuje konta organizacji i konta Microsoft. 
3. Wybierz **pozycję Nowe laboratorium**. 
    
    ![Tworzenie laboratorium na potrzeby zajęć](../media/tutorial-setup-classroom-lab/new-lab-button.png)
3. W oknie **Nowe laboratorium** wykonaj następujące czynności: 
    1. Określ **nazwę** dla swojego laboratorium. 
    2. Wybierz **rozmiar maszyn wirtualnych,** które są potrzebne dla klasy. Aby uzyskać listę dostępnych rozmiarów, zobacz sekcję [Rozmiary maszyn wirtualnych.](#vm-sizes) 
    3. Wybierz **obraz maszyny wirtualnej,** którego chcesz użyć w laboratorium klasy. Jeśli wybierzesz obraz systemu Linux, zostanie wyświetlenie opcji włączenia dla niego połączenia pulpitu zdalnego. Aby uzyskać szczegółowe informacje, zobacz [Włączanie połączenia pulpitu zdalnego dla systemu Linux](how-to-enable-remote-desktop-linux.md).

        Jeśli zalogowano się przy użyciu poświadczeń właściciela konta laboratorium, zostanie wyświetlna opcja, aby włączyć więcej obrazów dla laboratorium. Aby uzyskać więcej informacji, zobacz [Włączanie obrazów w momencie tworzenia laboratorium](specify-marketplace-images.md#enable-images-at-the-time-of-lab-creation).
    4. Przejrzyj **całkowitą cenę za godzinę** wyświetlaną na stronie. 
    6. Wybierz **pozycję Zapisz**.

        ![Nowe okno laboratorium](../media/tutorial-setup-classroom-lab/new-lab-window.png)

        > [!NOTE]
        > Zostanie wyświetlona opcja wybrania lokalizacji dla laboratorium, jeśli konto laboratorium zostało skonfigurowane tak, aby [umożliwić twórcy laboratorium wybranie](allow-lab-creator-pick-lab-location.md) opcji lokalizacji laboratorium. 
4. Na stronie **poświadczenia maszyny wirtualnej** określ domyślne poświadczenia dla wszystkich maszyn wirtualnych w laboratorium.
    1. Określ **nazwę użytkownika** dla wszystkich maszyn wirtualnych w laboratorium.
    2. Podaj **hasło** użytkownika. 

        > [!IMPORTANT]
        > Zanotuj nazwę użytkownika i hasło. Nie zostaną one ponownie wyświetlone.
    3. Wyłącz **Użyj tego samego hasła dla wszystkich maszyn wirtualnych** opcji, jeśli chcesz, aby uczniowie, aby ustawić własne hasła. Ten krok jest **opcjonalny**. 

        Nauczyciel może użyć tego samego hasła dla wszystkich maszyn wirtualnych w laboratorium lub zezwolić uczniom na ustawianie haseł dla swoich maszyn wirtualnych. Domyślnie to ustawienie jest włączone dla wszystkich obrazów systemu Windows i Linux z wyjątkiem Ubuntu. Po wybraniu maszyny Wirtualnej **Ubuntu** to ustawienie jest wyłączone, więc uczniowie zostaną poproszeni o ustawienie hasła podczas logowania się po raz pierwszy.  

        ![Nowe okno laboratorium](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
    4. Następnie wybierz przycisk **Dalej** na stronie **Poświadczenia maszyny wirtualnej.** 
5. Na stronie **Zasady laboratorium** wykonaj następujące czynności:
    1. Wprowadź liczbę godzin przydzielonych dla każdego użytkownika **(przydział dla każdego użytkownika)** poza zaplanowanym czasem dla laboratorium. 
    2. W przypadku opcji **Automatyczne zamykanie maszyn wirtualnych** określ, czy maszyna wirtualna ma być automatycznie zamykana po rozłączeniu użytkownika. Można również określić, jak długo maszyna wirtualna powinna czekać na ponowne połączenie użytkownika przed automatycznym zamknięciem.. Aby uzyskać więcej informacji, zobacz [Włączanie automatycznego zamykania maszyn wirtualnych przy rozłączaniu](how-to-enable-shutdown-disconnect.md).
    3. Następnie wybierz pozycję **Zakończ**. 

        ![Przydział dla każdego użytkownika](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
    
5. Powinien zostać wyświetlony następujący ekran, który pokazuje stan tworzenia maszyny wirtualnej szablonu. Tworzenie szablonu w laboratorium trwa maksymalnie 20 minut. 

    ![Stan tworzenia maszyny wirtualnej szablonu](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Na stronie **Szablon** wykonaj następujące kroki: Te kroki są **opcjonalne** dla samouczka.

    2. Nawiąż połączenie z maszyną wirtualną szablonu, wybierając pozycję **Połącz**. Jeśli jest to maszyna wirtualna szablonu systemu Linux, możesz wybrać, czy chcesz połączyć się przy użyciu protokołu SSH lub RDP (jeśli protokół RDP jest włączony).
    1. Wybierz **resetowanie hasła,** aby zresetować hasło maszyny Wirtualnej. 
    1. Zainstaluj i skonfiguruj oprogramowania na maszynie wirtualnej szablonu. 
    1. **Zatrzymaj** maszynę wirtualną.  
    1. Wprowadź **opis** szablonu.
10. Na stronie **Szablon** wybierz pozycję **Publikuj** na pasku narzędzi. 

    ![Przycisk Opublikuj szablon](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Nie można cofnąć publikowania szablonu. 
8. Na stronie **Publikowanie szablonu** wprowadź liczbę maszyn wirtualnych, które chcesz utworzyć w laboratorium, a następnie wybierz pozycję **Publikuj**. 

    ![Szablon publikowania — liczba maszyn wirtualnych](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Zostanie wyświetlony **stan publikowania** szablonu na stronie. Ten proces może potrwać do godziny. 

    ![Publikowanie szablonu — postęp](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Przełącz się do strony **puli maszyn wirtualnych,** wybierając maszyny wirtualne w menu po lewej stronie lub wybierając kafelek maszyny wirtualne. Sprawdź, czy są widoczne maszyny wirtualne o stanie **Nie przypisano**. Te maszyny wirtualne nie zostały jeszcze przypisane do uczniów. Powinny mieć stan **Zatrzymano**. Z poziomu tej strony możesz uruchomić maszynę wirtualną ucznia, połączyć się z maszyną wirtualną, zatrzymać maszynę wirtualną i usunąć maszynę wirtualną. Maszyny wirtualne możesz uruchomić na tej stronie lub pozwolić, aby zrobili to uczniowie. 

    ![Maszyny wirtualne w stanie Zatrzymano](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    Wykonaj następujące zadania na tej stronie (nie wykonaj tych kroków dla samouczka. Te kroki służą wyłącznie informacjom. 
    
    1. Aby zmienić pojemność laboratorium (liczba maszyn wirtualnych w laboratorium), wybierz **pojemność laboratorium** na pasku narzędzi.
    2. Aby uruchomić wszystkie maszyny wirtualne naraz, wybierz **rozpocznij wszystko** na pasku narzędzi. 
    3. Aby uruchomić określoną maszynę wirtualną, wybierz strzałkę w dół w **stanie**, a następnie wybierz przycisk **Start**. Maszynę wirtualną można również uruchomić, wybierając maszynę wirtualną w pierwszej kolumnie, a następnie wybierając **przycisk Start** na pasku narzędzi.                

### <a name="vm-sizes"></a>Rozmiary maszyn wirtualnych  

| Rozmiar | Rdzenie | Pamięć RAM | Opis | 
| ---- | ----- | --- | ----------- | 
| Small | 2 | 3,5 GB | Ten rozmiar najlepiej nadaje się do wiersza polecenia, otwierania przeglądarki internetowej, serwerów internetowych o małym natężeniu ruchu, małych i średnich baz danych. |
| Medium | 4 | 7 GB | Ten rozmiar najlepiej nadaje się do relacyjnych baz danych, buforowania w pamięci i analizy | 
| Średni (wirtualizacja zagnieżdżona) | 4 | 16 GB | Ten rozmiar najlepiej nadaje się do relacyjnych baz danych, buforowania w pamięci i analizy. Ten rozmiar obsługuje również zagnieżdżoną wirtualizację. <p>Ten rozmiar może służyć w scenariuszach, w których każdy uczeń potrzebuje wielu maszyn wirtualnych. Nauczyciele mogą używać zagnieżdżonej wirtualizacji do skonfigurowania kilku małych zagnieżdżonych maszyn wirtualnych wewnątrz maszyny wirtualnej. </p> |
| Large | 8 | 32 GB | Ten rozmiar najlepiej nadaje się do aplikacji, które potrzebują szybszych procesorów, lepszej wydajności dysku lokalnego, dużych baz danych, dużych pamięci podręcznych. Ten rozmiar obsługuje również zagnieżdżoną wirtualizację |  
| Mały procesor graficzny (wizualizacja) | 6 | 56 GB | Ten rozmiar najlepiej nadaje się do zdalnej wizualizacji, przesyłania strumieniowego, gier, kodowania przy użyciu takich struktur jak OpenGL i DirectX. | 
| Mały procesor graficzny (compute) | 6 | 56 GB | Ten rozmiar najlepiej nadaje się do aplikacji intensywnie korzystających z danych obliczeniowych i sieciowych, takich jak sztuczna inteligencja i aplikacje do uczenia głębokiego. | 
| Średni procesor graficzny (wizualizacja) | 12 | 112 GB | Ten rozmiar najlepiej nadaje się do zdalnej wizualizacji, przesyłania strumieniowego, gier, kodowania przy użyciu takich struktur jak OpenGL i DirectX. | 

> [!NOTE]
> Usługa Azure Lab Services automatycznie instaluje i konfiguruje niezbędne sterowniki procesora GPU podczas tworzenia laboratorium z obrazami GPU.  

## <a name="view-all-classroom-labs"></a>Zobacz wszystkie laboratoria szkolne
1. Przejdź do [portalu usługi Azure Lab Services](https://labs.azure.com).
2. Wybierz pozycję **Zaloguj się**. Wybierz lub wprowadź **identyfikator użytkownika,** który jest członkiem roli **Twórca laboratorium** na koncie laboratorium, i wprowadź hasło. Usługa Azure Lab Services obsługuje konta organizacji i konta Microsoft. 
3. Upewnij się, że wszystkie laboratoria są widoczne na wybranym koncie laboratorium. Na kafelku laboratorium zostanie wyświetlona liczba maszyn wirtualnych w laboratorium i przydział dla każdego użytkownika (poza zaplanowanym czasem).

    ![Wszystkie laboratoria](../media/how-to-manage-classroom-labs/all-labs.png)
3. Użyj listy rozwijanej u góry, aby wybrać inne konto laboratorium. Zostaną wyświetlane laboratoria na wybranym koncie laboratorium. 

## <a name="delete-a-classroom-lab"></a>Usuwanie laboratorium w klasie
1. Na kafelku dla laboratorium wybierz trzy kropki (...) w rogu, a następnie wybierz pozycję **Usuń**. 

    ![Przycisk Usuń](../media/how-to-manage-classroom-labs/delete-button.png)
3. W oknie dialogowym **Usuwanie laboratorium** wybierz pozycję **Usuń,** aby kontynuować usuwanie. 

## <a name="switch-to-another-classroom-lab"></a>Przejście do innego laboratorium w klasie
Aby przełączyć się do innego laboratorium w klasie z bieżącego, wybierz listę rozwijaną laboratoriów na koncie laboratorium u góry.

![Wybierz laboratorium z listy rozwijanej u góry](../media/how-to-manage-classroom-labs/switch-lab.png)

Można również utworzyć nowe laboratorium przy użyciu **nowego laboratorium** na tej liście rozwijanej. 

> [!NOTE]
> Modułu Programu PowerShell Az.LabServices (wersja zapoznawcza) można również użyć do zarządzania laboratoriami. Aby uzyskać więcej informacji, zobacz [stronę główną Az.LabServices w serwisie GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

Aby przełączyć się na inne konto laboratorium, wybierz pozycję rozwijaną obok konta laboratorium i wybierz inne konto laboratorium. 

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [As a lab owner, set up and publish templates (Konfigurowanie i tworzenie szablonów jako właściciel laboratorium)](how-to-create-manage-template.md)
- [As a lab owner, configure and control usage of a lab (Konfigurowanie i kontrolowanie użycia laboratorium jako właściciel laboratorium)](how-to-configure-student-usage.md)
- [Jako użytkownik laboratorium, uzyskaj dostęp do laboratoriów szkolnych](how-to-use-classroom-lab.md)

