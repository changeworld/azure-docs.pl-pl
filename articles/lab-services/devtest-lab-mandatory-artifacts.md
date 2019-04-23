---
title: Określ artefaktów obowiązkowe dla usługi Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak określić obowiązkowy artefaktów, które trzeba zainstalować przed zainstalowaniem wszelkie artefakty wybrane przez użytkownika na maszynach wirtualnych (VM) w środowisku laboratoryjnym.
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
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795410"
---
# <a name="specify-mandatory-artifacts-for-your-lab-in-azure-devtest-labs"></a>Określ obowiązkowe artefaktów dla laboratorium w usłudze Azure DevTest Labs
Jako właściciel laboratorium możesz określić obowiązkowy artefaktów, które są stosowane do każdej maszyny, utworzone w środowisku laboratoryjnym. Wyobraź sobie scenariusz, w której chcesz poszczególnych maszyn w środowisku laboratoryjnym, które będą podłączone do sieci firmowej. W takim przypadku każdy użytkownik laboratorium będą musiały dodać artefaktu przyłączanie do domeny podczas tworzenia maszyny wirtualnej, aby upewnić się, że ich maszyna jest podłączona do domeny firmy. Innymi słowy użytkownicy laboratorium zasadniczo musi ponownie utworzyć maszynę w przypadku, gdy zapomną zastosowanie obowiązkowe artefaktów na swoim komputerze. Jako właściciel laboratorium należy artefaktu przyłączanie do domeny jako obowiązkowe artefakt w laboratorium. Ten krok pozwala się upewnić, że każda maszyna jest połączona z siecią firmową i zapisywanie czas i nakład pracy użytkowników laboratorium.
 
Inne artefakty obowiązkowe mogą obejmować popularnego narzędzia, z której korzystają członkowie zespołu lub pakiet zabezpieczeń związane z platformą, który każda maszyna musi mieć domyślnie itp. Krótko mówiąc nazwy wspólnej programów, które muszą mieć do każdej maszyny w środowisku laboratoryjnym staje się obowiązkowe artefaktu. Jeśli tworzenie obrazu niestandardowego z maszynie, która zawiera artefakty obowiązkowe zastosowano, a następnie utworzyć nowej maszynie z tego obrazu, obowiązkowe artefaktów zostaną ponownie zastosowane na komputerze podczas tworzenia. To zachowanie również oznacza, że, mimo że niestandardowego obrazu jest stary, każdym razem, gdy tworzysz maszynę z niego najbardziej zaktualizowaną wersję artefaktów obowiązkowe są do niego zastosowane podczas tworzenia przepływu. 
 
Tylko te artefakty, które nie mają parametrów są obsługiwane jako obowiązkowe z nich. Użytkownika usługi laboratoryjnej nie trzeba wprowadzić dodatkowe parametry podczas tworzenia laboratorium i czyniąc prosty proces tworzenia maszyny Wirtualnej. 

## <a name="specify-mandatory-artifacts"></a>Określanie obowiązkowych artefaktów
Obowiązkowe artefaktów dla maszyn Windows i Linux można wybrać oddzielnie. Można również zmieniać kolejność tych artefaktów, w zależności od kolejności, w której chcesz zastosować. 

1. Na stronie głównej w środowisku laboratoryjnym wybierz **konfiguracji i zasad** w obszarze **ustawienia**. 
3. Wybierz **obowiązkowe artefaktów** w obszarze **zasobów zewnętrznych**. 
4. Wybierz **Edytuj** w **Windows** sekcji lub **Linux** sekcji. W tym przykładzie użyto **Windows** opcji. 

    ![Strona obowiązkowe artefaktów — przycisk Edytuj](media/devtest-lab-mandatory-artifacts/mandatory-artifacts-edit-button.png)
4. Wybierz artefakt. W tym przykładzie użyto **7 Zip** opcji. 
5. Na **Dodawanie artefaktu** wybierz opcję **Dodaj**. 

    ![Obowiązkowe artefaktów stronie — Dodawanie 7 zip](media/devtest-lab-mandatory-artifacts/add-seven-zip.png)
6. Aby dodać innego artefaktu, wybierz artykuł, a następnie wybierz **Dodaj**. Ten przykład dodaje **Chrome** jako drugi obowiązkowy artefakt.

    ![Strona obowiązkowe artefaktów — Dodaj dla programu Chrome](media/devtest-lab-mandatory-artifacts/add-chrome.png)
7. Na **obowiązkowe artefaktów** strony, zostanie wyświetlony komunikat, który określa liczbę artefaktów, które wybrano. Jeśli możesz kliknąć komunikat, zostanie wyświetlony artefaktów, które zostały wybrane. Wybierz **Zapisz** do zapisania. 

    ![Strona obowiązkowe artefaktów — Zapisz artefaktów](media/devtest-lab-mandatory-artifacts/save-artifacts.png)
8. Powtórz kroki, aby określić obowiązkowy artefaktów dla maszyn wirtualnych systemu Linux. 
    
    ![Strona obowiązkowe artefaktów — artefaktów Windows i Linux](media/devtest-lab-mandatory-artifacts/windows-linux-artifacts.png)
9. Aby **Usuń** artefaktu z listy wybierz **... (wielokropek)**  na końcu wiersza, a następnie wybierz pozycję **Usuń**. 
10. Aby **kolejności** artefaktów na liście, umieść kursor myszy nad artefaktu, wybierz **... (wielokropek)**  pojawia się na początku wiersza i przeciągnij element do nowej pozycji. 
11. Aby zapisać obowiązkowe artefakty w laboratorium, zaznacz **Zapisz**. 

    ![Strona obowiązkowe artefaktów — Zapisz artefakty w laboratorium](media/devtest-lab-mandatory-artifacts/save-to-lab.png)
12. Zamknij **konfiguracji i zasad** strony (wybierz **X** w prawym górnym rogu), aby wrócić do strony głównej dla swojego laboratorium.  

## <a name="delete-a-mandatory-artifact"></a>Usuń obowiązkowe artefaktu
Aby usunąć artefakt obowiązkowe z laboratorium, wykonaj następujące czynności: 

1. Wybierz **konfiguracji i zasad** w obszarze **ustawienia**. 
2. Wybierz **obowiązkowe artefaktów** w obszarze **zasobów zewnętrznych**. 
3. Wybierz **Edytuj** w **Windows** sekcji lub **Linux** sekcji. W tym przykładzie użyto **Windows** opcji. 
4. Zaznacz wiadomość z liczbą artefaktów obowiązkowe u góry. 

    ![Strona obowiązkowe artefaktów — wybierz komunikat](media/devtest-lab-mandatory-artifacts/select-message-artifacts.png)
5. Na **wybrane artefaktów** wybierz opcję **... (wielokropek)**  dla artefaktu do usunięcia, a następnie wybierz pozycję **Usuń**. 
    
    ![Strona obowiązkowe artefaktów — usuwanie artefaktów](media/devtest-lab-mandatory-artifacts/remove-artifact.png)
6. Wybierz **OK** zamknąć **wybrane artefaktów** strony. 
7. Wybierz **Zapisz** na **obowiązkowe artefaktów** strony.
8. Powtórz kroki dla **Linux** obrazy w razie potrzeby. 
9. Wybierz **Zapisz** można zapisać wszystkich zmian w środowisku laboratoryjnym. 

## <a name="view-mandatory-artifacts-when-creating-a-vm"></a>Widok artefaktów obowiązkowy podczas tworzenia maszyny Wirtualnej
Teraz jako użytkownik laboratorium można wyświetlić listę artefaktów obowiązkowy podczas tworzenia maszyny Wirtualnej w środowisku laboratoryjnym. Nie można edytować ani usuwać artefakty obowiązkowe ustawione w środowisku laboratoryjnym przez właściciela usługi laboratorium.

1. Na stronie głównej dla swojego laboratorium wybierz **Przegląd** z menu.
2. Aby dodać Maszynę wirtualną laboratorium, wybierz **+ Dodaj**. 
3. Wybierz **obrazu podstawowego**. W tym przykładzie użyto **w systemie Windows Server w wersji 1709**.
4. Należy zauważyć, że zostanie wyświetlony komunikat dla **artefaktów** z liczbą artefaktów obowiązkowe wybrane. 
5. Wybierz **artefaktów**. 
6. Upewnij się, że **obowiązkowe artefaktów** określony w konfiguracji i zasad laboratorium. 

    ![Tworzenie maszyny Wirtualnej — obowiązkowe artefaktów](media/devtest-lab-mandatory-artifacts/create-vm-artifacts.png)

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [Dodawanie repozytorium artefaktów usługi Git do laboratorium](devtest-lab-add-artifact-repo.md).

