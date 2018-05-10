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
ms.date: 04/20/2018
ms.author: spelluru
ms.openlocfilehash: 17544275f921486529518e37eb171cd0b4f26791
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="manage-classroom-labs-in-azure-lab-services-formerly-azure-devtest-labs"></a>Zarządzanie klasy laboratoriów w usługach laboratorium Azure (poprzednio Azure DevTest Labs)
W tym artykule opisano, jak utworzyć i skonfigurować laboratorium klas, wyświetlić wszystkie klasy labs lub usunąć laboratorium klasy.

## <a name="create-a-classroom-lab"></a>Tworzenie laboratorium klas

1. Przejdź do [witryny sieci Web usług Azure laboratorium](https://labs.azure.com).
2. W **nowego laboratorium** okna, wykonaj następujące czynności: 
    1. Określ **nazwa** dla laboratorium klasy. 
    2. Wybierz **rozmiar** maszyny wirtualnej, które będą używane w klasie.
    3. Wybierz **obrazu** na potrzeby tworzenia maszyny wirtualnej.
    4. Określ **domyślnych poświadczeń** na potrzeby rejestrowania maszyn wirtualnych w laboratorium.
    7. Wybierz pozycję **Zapisz**.

        ![Tworzenie laboratorium klas](./media/how-to-manage-classroom-labs/new-lab-window.png)
1. Zostanie wyświetlony **strony głównej** dla laboratorium. 
    
    ![Strona główna laboratorium klas](./media/how-to-manage-classroom-labs/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Konfigurowanie użycia zasad

1. Wybierz **zasadach użytkowania**. 
2. W **zasadach użytkowania**, ustawień, wprowadź **liczby użytkowników** mogli korzystać z laboratorium.
3. Wybierz pozycję **Zapisz**. 

    ![Użycie zasad](./media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="set-up-the-template"></a>Konfigurowanie szablonu
Szablon laboratorium jest tworzone są maszyny wirtualne wszystkich użytkowników. Konfigurowanie szablonu maszyny wirtualnej tak, że jest ona skonfigurowana z dokładnie co chcesz umożliwić użytkownikom laboratorium. Podaj nazwę i opis szablonu laboratorium użytkownicy widzą i ustawiać widoczność na wartość "Publiczne", aby udostępnić użytkownikom laboratorium wystąpienia szablonu maszyny Wirtualnej.  

## <a name="set-template-title-and-description"></a>Ustaw szablon tytuł i opis
1. W **szablonu** zaznacz **Edytuj** (ikonę ołówka) dla szablonu. 
2. W **widoku użytkownika** okna, wprowadź **tytuł** dla szablonu.
3. Wprowadź **opis** dla szablonu.
4. Wybierz pozycję **Zapisz**.

    ![Opis elementu klasy laboratorium](./media/how-to-manage-classroom-labs/lab-description.png)

### <a name="make-instances-of-the-template-public"></a>Udostępnić wystąpienia szablonu 
Po ustawieniu widoczność szablonu do **publicznych**, usługi laboratorium Azure tworzy maszyny wirtualne w laboratorium przy użyciu szablonu. Liczbę maszyn wirtualnych utworzonych w procesie są takie same jak maksymalną liczbę użytkowników, którzy mogą w laboratorium, które można ustawić w zasadach użytkowania laboratorium. Wszystkie maszyny wirtualne mają taką samą konfigurację jako szablonu.  

1. Wybierz **widoczność** w **szablonu** sekcji. 
2. W **dostępności** wybierz pozycję **publicznego**.
    
    > [!IMPORTANT]
    > Gdy szablon jest dostępna publicznie, dostęp do niej nie można zmienić na prywatną. 
3. Wybierz pozycję **Zapisz**.

    ![Dostępność](./media/how-to-manage-classroom-labs/public-access.png)

## <a name="send-registration-link-to-students"></a>Wyślij łącze do uczniów lub studentów

1. Wybierz **rejestracja użytkownika** kafelka.
2. W **rejestracja użytkownika** okno dialogowe, wybierz opcję **kopiowania** przycisku. Łącze jest kopiowany do Schowka. Wklej go w edytorze poczty e-mail i Wyślij wiadomość e-mail do studenta. 

    ![Łącze rejestracji dla użytkowników domowych](./media/how-to-manage-classroom-labs/registration-link.png)

## <a name="view-all-classroom-labs"></a>Wyświetl wszystkie laboratoria klas
1. Przejdź do [portalu Azure usługi laboratorium](https://labs.azure.com).
2. Upewnij się, że widoczne wszystkie laboratoriów w ramach laboratorium wybranego konta. 

    ![Wszystkie labs](./media/how-to-manage-classroom-labs/all-labs.png)
3. Użyj listy rozwijanej u góry, aby wybrać konto różnych laboratorium. Zostanie wyświetlony laboratoriów w ramach laboratorium wybranego konta. 

## <a name="delete-a-classroom-lab"></a>Usuń laboratorium klas
1. Na kafelku dla laboratorium wybierz wielokropek (...) w prawym górnym rogu. 

    ![Wybierz laboratorium](./media/how-to-manage-classroom-labs/select-three-dots.png)
2. Wybierz pozycję **Usuń**. 

    ![Usuwanie przycisku](./media/how-to-manage-classroom-labs/delete-button.png)
3. Na **laboratorium Delete** okno dialogowe, wybierz opcję **usunąć**. 

    ![Okno dialogowe Usuwanie](./media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)
 

## <a name="next-steps"></a>Kolejne kroki
Wprowadzenie do konfigurowania laboratorium przy użyciu usług Azure laboratorium:

- [Konfigurowanie laboratorium klas](how-to-manage-classroom-labs.md)
- [Konfigurowanie niestandardowych laboratorium](tutorial-create-custom-lab.md)
