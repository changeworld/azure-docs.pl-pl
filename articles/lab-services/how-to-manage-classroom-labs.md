---
title: Zarządzanie klasy laboratoriów w usług Azure laboratorium | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć i skonfigurować laboratorium klas, wyświetlić wszystkie klasy labs, plk rejestracji połączyć z użytkownikiem laboratorium lub usunąć laboratorium.
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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: e388045e839c19c68ad2c00f31d74c73e107872c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="manage-classroom-labs-in-azure-lab-services-formerly-azure-devtest-labs"></a>Zarządzanie klasy laboratoriów w usługach laboratorium Azure (poprzednio Azure DevTest Labs)
W tym artykule opisano, jak utworzyć i skonfigurować laboratorium klas, wyświetlić wszystkie klasy labs lub usunąć laboratorium klasy.

## <a name="create-a-classroom-lab"></a>Tworzenie laboratorium na potrzeby zajęć

1. Przejdź do [witryny internetowej usługi Azure Lab Services](https://labs.azure.com).
2. W oknie **Nowe laboratorium** wykonaj następujące czynności: 
    1. Określ **nazwę** laboratorium na potrzeby zajęć. 
    2. Wybierz **rozmiar** maszyny wirtualnej, której zamierzasz używać podczas zajęć.
    3. Wybierz **obraz**, za pomocą którego ma zostać utworzona maszyna wirtualna.
    4. Określ **domyślnych poświadczeń** na potrzeby rejestrowania maszyn wirtualnych w laboratorium.
    7. Wybierz pozycję **Zapisz**.

        ![Tworzenie laboratorium na potrzeby zajęć](./media/how-to-manage-classroom-labs/new-lab-window.png)
1. Zostanie wyświetlona **strona główna** laboratorium. 
    
    ![Strona główna laboratorium używanego podczas zajęć](./media/how-to-manage-classroom-labs/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Konfigurowanie zasad użycia

1. Wybierz pozycję **Zasady użytkowania**. 
2. W ustawieniach **Zasady użytkowania** wprowadź **liczbę użytkowników**, którzy mogą korzystać z laboratorium.
3. Wybierz pozycję **Zapisz**. 

    ![Zasady użytkowania](./media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="set-up-the-template"></a>Konfigurowanie szablonu
Szablon w laboratorium jest obrazem podstawowej maszyny wirtualnej, z którego wszystkich użytkowników maszyny wirtualne są tworzone. Możesz skonfigurować maszynę wirtualną szablonu dokładnie tak jak chcesz dla użytkowników laboratorium. Można podać nazwę i opis szablonu, dostępna dla użytkowników laboratorium. Ustaw widoczność szablonu publiczną, aby udostępnić użytkownikom laboratorium wystąpienia szablonu maszyny Wirtualnej.  

### <a name="set-template-title-and-description"></a>Ustaw szablon tytuł i opis
1. W sekcji **Szablon** wybierz pozycję **Edytuj** (ikona ołówka) dla szablonu. 
2. W oknie **Widok użytkownika** wprowadź **tytuł** szablonu.
3. Wprowadź **opis** szablonu.
4. Wybierz pozycję **Zapisz**.

    ![Opis laboratorium używanego podczas zajęć](./media/how-to-manage-classroom-labs/lab-description.png)

### <a name="make-instances-of-the-template-public"></a>Publiczne udostępnianie wystąpień szablonu 
Gdy ustawisz widoczność szablonu **Publiczny**, usługa Azure Lab Services utworzy maszyny wirtualne w laboratorium przy użyciu szablonu. Liczbę maszyn wirtualnych utworzonych w procesie jest taka sama jak maksymalną liczbę użytkowników, którzy mogą w laboratorium, które można ustawić w zasadach użytkowania laboratorium. Wszystkie maszyny wirtualne mają taką samą konfigurację jak szablon.  

1. Wybierz pozycję **Widoczność** w sekcji **Szablon**. 
2. Na stronie **Dostępność** wybierz pozycję **Publiczny**.
    
    > [!IMPORTANT]
    > Gdy szablon jest dostępny publicznie, nie można zmienić dostępu do niego na prywatny. 
3. Wybierz pozycję **Zapisz**.

    ![Dostępność](./media/how-to-manage-classroom-labs/public-access.png)

## <a name="send-registration-link-to-students"></a>Wysyłanie linków rejestracyjnych do uczniów

1. Wybierz kafelek **Rejestracja użytkownika**.
2. W oknie dialogowym **Rejestracja użytkownika** wybierz przycisk **Kopiuj**. Link zostanie skopiowany do Schowka. Wklej go w edytorze wiadomości e-mail i wyślij wiadomość e-mail do ucznia. 

    ![Link rejestracji ucznia](./media/how-to-manage-classroom-labs/registration-link.png)

## <a name="view-all-classroom-labs"></a>Wyświetl wszystkie laboratoria klas
1. Przejdź do [portalu Azure usługi laboratorium](https://labs.azure.com).
2. Upewnij się, że widoczne wszystkie laboratoriów w ramach laboratorium wybranego konta. 

    ![Wszystkie labs](./media/how-to-manage-classroom-labs/all-labs.png)
3. Użyj listy rozwijanej u góry, aby wybrać konto różnych laboratorium. Zostanie wyświetlony laboratoriów w ramach laboratorium wybranego konta. 

## <a name="delete-a-classroom-lab"></a>Usuń laboratorium klas
1. Na kafelku dla laboratorium wybierz wielokropek (...) w prawym górnym rogu. 

    ![Wybieranie laboratorium](./media/how-to-manage-classroom-labs/select-three-dots.png)
2. Wybierz pozycję **Usuń**. 

    ![Usuwanie przycisku](./media/how-to-manage-classroom-labs/delete-button.png)
3. Na **laboratorium Delete** okno dialogowe, wybierz opcję **usunąć**. 

    ![Okno dialogowe Usuwanie](./media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)
 

## <a name="next-steps"></a>Kolejne kroki
Wprowadzenie do konfigurowania laboratorium przy użyciu usługi Azure Lab Services:

- [Konfigurowanie laboratorium na potrzeby zajęć](how-to-manage-classroom-labs.md)
- [Konfigurowanie laboratorium niestandardowego](tutorial-create-custom-lab.md)
