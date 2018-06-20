---
title: Tworzenie laboratorium przy użyciu usługi Azure DevTest Labs | Microsoft Docs
description: W tym przewodniku Szybki start utworzysz laboratorium za pomocą usługi Azure DevTest Labs.
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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 5a93feec7996fc0ebf742b8d62b159dca5f1c1ed
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636991"
---
# <a name="tutorial-set-up-a-lab-by-using-azure-devtest-labs"></a>Samouczek: konfigurowanie laboratorium przy użyciu usługi Azure DevTest Labs
W tym samouczku utworzysz laboratorium za pomocą witryny Azure Portal. Administrator laboratorium konfiguruje laboratorium w organizacji, tworzy maszyny wirtualne w laboratorium i konfiguruje zasady. Użytkownicy laboratorium (na przykład deweloper i testerzy) przejmują maszyny wirtualne w laboratorium, nawiązują połączenie z nimi i korzystają z nich. 

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Tworzenie laboratorium
> * Dodawanie maszyn wirtualnych do laboratorium
> * Dodawanie użytkownika do roli użytkownika laboratorium

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="create-a-lab"></a>Tworzenie laboratorium
Następujące kroki ilustrują tworzenie laboratorium w usłudze Azure DevTest Labs przy użyciu witryny Azure Portal. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Z menu głównego po lewej stronie wybierz pozycję **Utwórz zasób** (na początku listy), wskaż pozycję **Narzędzia deweloperskie** i kliknij pozycję **DevTest Labs**. 

    ![Menu nowego laboratorium DevTest Labs](./media/tutorial-create-custom-lab/new-custom-lab-menu.png)
1. W oknie **Tworzenie laboratorium DevTest Lab** wykonaj następujące czynności: 
    1. W polu **Nazwa laboratorium** wprowadź nazwę laboratorium. 
    2. W obszarze **Subskrypcja** wybierz subskrypcję, w ramach której chcesz utworzyć laboratorium. 
    3. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową** i wprowadź nazwę grupy zasobów. 
    4. W obszarze **Lokalizacja** wybierz lokalizację/region, w których ma zostać utworzone laboratorium. 
    5. Wybierz pozycję **Utwórz**. 
    6. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**. Utworzone laboratorium zostanie wyświetlone na pulpicie nawigacyjnym. 

        ![Tworzenie sekcji laboratorium usługi DevTest Labs](./media/tutorial-create-custom-lab/create-custom-lab-blade.png)

## <a name="add-a-vm-to-the-lab"></a>Dodawanie maszyny wirtualnej do laboratorium

1. Na stronie **Laboratorium DevTest Lab** wybierz pozycję **+ Dodaj** na pasku narzędzi. 

    ![Przycisk dodawania](./media/tutorial-create-custom-lab/add-vm-to-lab-button.png)
1. Na stronie **Wybieranie bazy** wyszukaj słowo kluczowe **Ubuntu** i wybierz jeden z podstawowych obrazów z listy. 
1. Na stronie **Maszyna wirtualna** wykonaj następujące czynności: 
    1. W polu **Nazwa maszyny wirtualnej** wprowadź nazwę maszyny wirtualnej. 
    2. W polu **Nazwa użytkownika** wprowadź nazwę użytkownika, który ma dostęp do maszyny wirtualnej. 
    3. W polu **Wpisz wartość** wprowadź hasło użytkownika. 
    4. Wybierz pozycję **Ustawienia zaawansowane**.
    5. W obszarze **Oznacz tę maszynę jako możliwą do przejęcia** wybierz pozycję **Tak**.
    6. Upewnij się, że w polu **Liczba wystąpień** ustawiono wartość **1**. Ustawienie wartości **2** spowoduje utworzenie 2 maszyn wirtualnych o następujących nazwach: `<base image name>00' and <base image name>01`. Przykład: `win10vm00` i `win10vm01`. 
    7. Aby zamknąć stronę **Zaawansowane**, kliknij przycisk **OK**. 
    8. Wybierz pozycję **Utwórz**. 

        ![Wybieranie bazy](./media/tutorial-create-custom-lab/new-virtual-machine.png)
    9. Stan maszyny wirtualnej jest wyświetlany na liście **Maszyny wirtualne możliwe do przejęcia**. Tworzenie maszyny wirtualnej może potrwać około 25 minut. Maszyna wirtualna jest tworzona w oddzielnej grupie zasobów platformy Azure, której nazwa rozpoczyna się od nazwy bieżącej grupy zasobów zawierającej laboratorium. Jeśli na przykład laboratorium znajduje się w grupie `labrg`, maszyna wirtualna może zostać utworzona w grupie zasobów `labrg3988722144002`. 

        ![Stan tworzenia maszyny wirtualnej](./media/tutorial-create-custom-lab/vm-creation-status.png)
1. Utworzona maszyna wirtualna jest wyświetlana na liście **Maszyny wirtualne możliwe do przejęcia**. 

## <a name="add-a-user-to-the-lab-user-role"></a>Dodawanie użytkownika do roli użytkownika laboratorium

1. Z menu po lewej stronie wybierz pozycję **Konfiguracja i zasady**. 

    ![Konfiguracja i zasady](./media/tutorial-create-custom-lab/configuration-and-policies-menu.png)
1. Z menu wybierz pozycję **Kontrola dostępu (IAM)**, a następnie wybierz pozycję **+ Dodaj** na pasku narzędzi. 

    ![Kontrola dostępu — przycisk dodawania użytkownika](./media/tutorial-create-custom-lab/access-control-add.png)
1. Na stronie **Dodawanie uprawnień** wykonaj następujące czynności:
    1. W obszarze **Rola** wybierz pozycję **Użytkownik usługi DevTest Labs**. 
    2. Wybierz **użytkownika**, którego chcesz dodać. 
    3. Wybierz pozycję **Zapisz**.

        ![Dodawanie uprawnień](./media/tutorial-create-custom-lab/add-lab-user.png)
4. Aby zamknąć okno **Konfiguracja i zasady — Kontrola dostępu (IAM)**, wybierz pozycję **X** w prawym rogu. 

## <a name="cleanup-resources"></a>Oczyszczanie zasobów
W następnym samouczku pokazano, jak użytkownik laboratorium może przejąć maszynę wirtualną w laboratorium i nawiązać z nią połączenie. Jeśli nie chcesz korzystać z tamtego samouczka i chcesz oczyścić zasoby utworzone w ramach tego samouczka, wykonaj następujące czynności: 

1. W witrynie Azure Portal wybierz z menu pozycję **Grupy zasobów**. 
2. Wybierz grupę zasobów, w której utworzono laboratorium. 
3. Wybierz pozycję **Usuń grupę zasobów** na pasku narzędzi. Usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów w grupie, łącznie z laboratorium. 
4. Powtórz te czynności, aby usunąć dodatkową utworzoną grupę zasobów o nazwie `<your resource group name><random numbers>`. Na przykład: `splab3988722144001`. Maszyny wirtualne są tworzone w tej grupie zasobów, a nie w grupie zasobów, w której istnieje laboratorium. 

## <a name="next-steps"></a>Następne kroki
W tym samouczku utworzono laboratorium z maszyną wirtualną i udzielono użytkownikowi dostępu do laboratorium. Aby dowiedzieć się, jak uzyskać dostęp laboratorium jako użytkownik laboratorium, przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Samouczek: uzyskiwanie dostępu do laboratorium](tutorial-use-custom-lab.md)

