---
title: Konfigurowanie laboratorium na potrzeby zajęć przy użyciu usługi Azure Lab Services | Microsoft Docs
description: W tym samouczku skonfigurujesz laboratorium do użycia podczas zajęć.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/19/2018
ms.author: spelluru
ms.openlocfilehash: 3e9f8d118c4413ec93b7dffcfa41b6ad4381b052
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Samouczek: konfigurowanie laboratorium na potrzeby zajęć 
W tym samouczku skonfigurujesz laboratorium na potrzeby zajęć z zestawem maszyn wirtualnych używanych przez uczniów podczas zajęć.  

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Tworzenie laboratorium na potrzeby zajęć
> * Konfigurowanie laboratorium na potrzeby zajęć
> * Wysyłanie linków rejestracyjnych do uczniów

## <a name="create-a-classroom-lab"></a>Tworzenie laboratorium na potrzeby zajęć

1. Przejdź do [witryny internetowej usługi Azure Lab Services](https://labs.azure.com).
2. W oknie **Nowe laboratorium** wykonaj następujące czynności: 
    1. Określ **nazwę** laboratorium na potrzeby zajęć. 
    2. Wybierz **rozmiar** maszyny wirtualnej, której zamierzasz używać podczas zajęć.
    3. Wybierz **obraz**, za pomocą którego ma zostać utworzona maszyna wirtualna.
    4. Określ **domyślne poświadczenia** logowania do maszyn wirtualnych w laboratorium. 
    7. Wybierz pozycję **Zapisz**.

        ![Tworzenie laboratorium na potrzeby zajęć](./media/tutorial-setup-classroom-lab/new-lab-window.png)
1. Zostanie wyświetlona **strona główna** laboratorium. 
    
    ![Strona główna laboratorium używanego podczas zajęć](./media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Konfigurowanie zasad użycia

1. Wybierz pozycję **Zasady użytkowania**. 
2. W ustawieniach **Zasady użytkowania** wprowadź **liczbę użytkowników**, którzy mogą korzystać z laboratorium.
3. Wybierz pozycję **Zapisz**. 

    ![Zasady użytkowania](./media/tutorial-setup-classroom-lab/usage-policy-settings.png)

## <a name="set-up-the-template"></a>Konfigurowanie szablonu 
Szablon w laboratorium służy do tworzenia maszyn wirtualnych wszystkich użytkowników. Możesz skonfigurować maszynę wirtualną szablonu dokładnie tak jak chcesz dla użytkowników laboratorium. Możesz podać nazwę i opis szablonu widoczne dla użytkowników laboratorium oraz ustawić publiczną widoczność, aby udostępnić użytkownikom laboratorium wystąpienia maszyny wirtualnej szablonu. 

### <a name="set-title-and-description"></a>Ustawianie tytułu i opisu
1. W sekcji **Szablon** wybierz pozycję **Edytuj** (ikona ołówka) dla szablonu. 
2. W oknie **Widok użytkownika** wprowadź **tytuł** szablonu.
3. Wprowadź **opis** szablonu.
4. Wybierz pozycję **Zapisz**.

    ![Opis laboratorium używanego podczas zajęć](./media/tutorial-setup-classroom-lab/lab-description.png)

### <a name="make-instances-of-the-template-public"></a>Publiczne udostępnianie wystąpień szablonu
Gdy ustawisz widoczność szablonu **Publiczny**, usługa Azure Lab Services utworzy maszyny wirtualne w laboratorium przy użyciu szablonu. Liczba maszyn wirtualnych utworzonych w ramach tego procesu jest taka sama jak maksymalna liczba użytkowników, którzy mogą korzystać z laboratorium. Tę liczbę można ustawić w zasadach użytkowania laboratorium. Wszystkie maszyny wirtualne mają taką samą konfigurację jak szablon. 

1. Wybierz pozycję **Widoczność** w sekcji **Szablon**. 
2. Na stronie **Dostępność** wybierz pozycję **Publiczny**.
    
    > [!IMPORTANT]
    > Gdy szablon jest dostępny publicznie, nie można zmienić dostępu do niego na prywatny. 
3. Wybierz pozycję **Zapisz**.

    ![Dostępność](./media/tutorial-setup-classroom-lab/public-access.png)

## <a name="send-registration-link-to-students"></a>Wysyłanie linków rejestracyjnych do uczniów

1. Wybierz kafelek **Rejestracja użytkownika**.
2. W oknie dialogowym **Rejestracja użytkownika** wybierz przycisk **Kopiuj**. Link zostanie skopiowany do Schowka. Wklej go w edytorze wiadomości e-mail i wyślij wiadomość e-mail do ucznia. 

    ![Link rejestracji ucznia](./media/tutorial-setup-classroom-lab/registration-link.png)

## <a name="next-steps"></a>Następne kroki
W tym samouczku utworzono i skonfigurowano laboratorium na potrzeby zajęć. Aby dowiedzieć się, jak uczeń może uzyskać dostęp do maszyny wirtualnej w laboratorium przy użyciu linku rejestracyjnego, przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z maszyną wirtualną w laboratorium używanym podczas zajęć](tutorial-connect-virtual-machine-classroom-lab.md)

