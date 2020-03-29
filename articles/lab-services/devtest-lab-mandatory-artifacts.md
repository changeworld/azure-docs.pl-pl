---
title: Określ obowiązkowe artefakty dla laboratoriów devtest platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak określić obowiązkowe artefakty, które należy zainstalować przed zainstalowaniem jakichkolwiek artefaktów wybranych przez użytkownika na maszynach wirtualnych (VM) w laboratorium.
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: spelluru
ms.openlocfilehash: 090236ec3647c7c3e38eb862780a615f854e952b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60562262"
---
# <a name="specify-mandatory-artifacts-for-your-lab-in-azure-devtest-labs"></a>Określanie obowiązkowych artefaktów dla laboratorium w laboratoriach devtest azure
Jako właściciel laboratorium można określić obowiązkowe artefakty, które są stosowane do każdej maszyny utworzonej w laboratorium. Wyobraź sobie scenariusz, w którym chcesz, aby każdy komputer w laboratorium był połączony z siecią firmową. W takim przypadku każdy użytkownik laboratorium musiałby dodać artefakt sprzężenia domeny podczas tworzenia maszyny wirtualnej, aby upewnić się, że ich maszyna jest połączona z domeną firmową. Innymi słowy, użytkownicy laboratorium będą zasadniczo musieli odtworzyć maszynę w przypadku, gdy zapomną zastosować obowiązkowe artefakty na swoim komputerze. Jako właściciel laboratorium należy wprowadzić artefakt sprzężenia domeny jako obowiązkowy artefakt w laboratorium. Ten krok zapewnia, że każda maszyna jest podłączona do sieci firmowej i oszczędność czasu i wysiłku dla użytkowników laboratorium.
 
Inne obowiązkowe artefakty mogą zawierać typowe narzędzie używane przez zespół lub pakiet zabezpieczeń związany z platformą, który każdy komputer musi mieć domyślnie itp. Krótko mówiąc, każde wspólne oprogramowanie, które musi mieć każda maszyna w laboratorium, staje się obowiązkowym artefaktem. Jeśli utworzysz obraz niestandardowy z komputera, na którym zastosowano obowiązkowe artefakty, a następnie utworzysz nową maszynę z tego obrazu, artefakty obowiązkowe zostaną ponownie zastosowane na komputerze podczas tworzenia. To zachowanie oznacza również, że nawet jeśli obraz niestandardowy jest stary, za każdym razem, gdy tworzysz z niego maszynę, najbardziej zaktualizowana wersja obowiązkowych artefaktów jest stosowana do niego podczas przepływu tworzenia. 
 
Tylko artefakty, które nie mają parametrów są obsługiwane jako obowiązkowe. Użytkownik laboratorium nie trzeba wprowadzać dodatkowe parametry podczas tworzenia laboratorium, a tym samym ułatwia proces tworzenia maszyn wirtualnych. 

## <a name="specify-mandatory-artifacts"></a>Określanie obowiązkowych artefaktów
Artefakty obowiązkowe dla komputerów z systemem Windows i Linux można wybrać oddzielnie. Można również zamieścić kolejność tych artefaktów w zależności od kolejności ich stosowania. 

1. Na stronie głównej laboratorium wybierz **pozycję Konfiguracja i zasady** w obszarze **USTAWIENIA**. 
3. Wybierz **opcję Obowiązkowe artefakty** w obszarze **ZASOBY ZEWNĘTRZNE**. 
4. Wybierz **pozycję Edytuj** w sekcji **Windows** lub w systemie **Linux.** W tym przykładzie użyto opcji **Windows.** 

    ![Strona Obowiązkowe artefakty - przycisk Edytuj](media/devtest-lab-mandatory-artifacts/mandatory-artifacts-edit-button.png)
4. Wybierz artefakt. W tym przykładzie użyto opcji **7-Zip.** 
5. Na stronie **Dodawanie artefaktów** wybierz pozycję **Dodaj**. 

    ![Strona obowiązkowych artefaktów - Dodaj 7-zip](media/devtest-lab-mandatory-artifacts/add-seven-zip.png)
6. Aby dodać kolejny artefakt, zaznacz artykuł i wybierz pozycję **Dodaj**. W tym przykładzie dodano **Chrome** jako drugi obowiązkowy artefakt.

    ![Strona Obowiązkowych artefaktów - Dodaj Chrome](media/devtest-lab-mandatory-artifacts/add-chrome.png)
7. Na stronie **Obowiązkowe artefakty** zostanie wyświetlony komunikat określający liczbę wybranych artefaktów. Po kliknięciu wiadomości zostaną wyświetlane wybrane artefakty. Wybierz **pozycję Zapisz,** aby zapisać. 

    ![Strona Obowiązkowych artefaktów — zapisywanie artefaktów](media/devtest-lab-mandatory-artifacts/save-artifacts.png)
8. Powtórz kroki, aby określić obowiązkowe artefakty dla maszyn wirtualnych z systemem Linux. 
    
    ![Strona obowiązkowych artefaktów - artefakty systemu Windows i Linux](media/devtest-lab-mandatory-artifacts/windows-linux-artifacts.png)
9. Aby **usunąć** artefakt z listy, wybierz **... (wielokropek)** na końcu wiersza, a następnie wybierz pozycję **Usuń**. 
10. Aby **zamiesorować** artefakty na liście, umieść wskaźnik myszy na artefakt, wybierz **... (wielokropek)** który pojawi się na początku wiersza i przeciągnij element do nowej pozycji. 
11. Aby zapisać obowiązkowe artefakty w laboratorium, wybierz pozycję **Zapisz**. 

    ![Strona Obowiązkowych artefaktów — zapisywanie artefaktów w laboratorium](media/devtest-lab-mandatory-artifacts/save-to-lab.png)
12. Zamknij stronę **Konfiguracja i zasady** (wybierz **X** w prawym górnym rogu), aby wrócić do strony głównej laboratorium.  

## <a name="delete-a-mandatory-artifact"></a>Usuwanie obowiązkowego artefaktu
Aby usunąć obowiązkowy artefakt z laboratorium, wykonaj następujące czynności: 

1. Wybierz **pozycję Konfiguracja i zasady** w obszarze **USTAWIENIA**. 
2. Wybierz **opcję Obowiązkowe artefakty** w obszarze **ZASOBY ZEWNĘTRZNE**. 
3. Wybierz **pozycję Edytuj** w sekcji **Windows** lub w systemie **Linux.** W tym przykładzie użyto opcji **Windows.** 
4. Wybierz wiadomość z liczbą obowiązkowych artefaktów u góry. 

    ![Strona Obowiązkowych artefaktów — wybierz komunikat](media/devtest-lab-mandatory-artifacts/select-message-artifacts.png)
5. Na stronie **Wybrane artefakty** wybierz **pozycję ... (wielokropek)** aby usunąć artefakt, a następnie wybierz pozycję **Usuń**. 
    
    ![Strona Artefakty obowiązkowe — usuwanie artefaktu](media/devtest-lab-mandatory-artifacts/remove-artifact.png)
6. Wybierz **przycisk OK,** aby zamknąć stronę **Wybrane artefakty.** 
7. Wybierz **pozycję Zapisz** na stronie **Artefakty obowiązkowe.**
8. W razie potrzeby powtórz kroki dla obrazów **systemu Linux.** 
9. Wybierz **pozycję Zapisz,** aby zapisać wszystkie zmiany w laboratorium. 

## <a name="view-mandatory-artifacts-when-creating-a-vm"></a>Wyświetlanie obowiązkowych artefaktów podczas tworzenia maszyny Wirtualnej
Teraz jako użytkownik laboratorium można wyświetlić listę obowiązkowych artefaktów podczas tworzenia maszyny Wirtualnej w laboratorium. Nie można edytować ani usuwać obowiązkowych artefaktów ustawionych w laboratorium przez właściciela laboratorium.

1. Na stronie głównej laboratorium wybierz **pozycję Przegląd** z menu.
2. Aby dodać maszynę wirtualną do laboratorium, wybierz pozycję **+ Dodaj**. 
3. Wybierz **obraz bazowy**. W tym przykładzie użyto **systemu Windows Server w wersji 1709**.
4. Należy zauważyć, że zostanie wyświetlony komunikat dla **artefaktów** z liczbą obowiązkowych artefaktów zaznaczonych. 
5. Wybierz **artefakty**. 
6. Upewnij się, że są widoczne **artefakty obowiązkowe** określone w konfiguracji i zasadach laboratorium. 

    ![Tworzenie maszyny Wirtualnej — artefakty obowiązkowe](media/devtest-lab-mandatory-artifacts/create-vm-artifacts.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [dodać repozytorium artefaktów Git do laboratorium.](devtest-lab-add-artifact-repo.md)

