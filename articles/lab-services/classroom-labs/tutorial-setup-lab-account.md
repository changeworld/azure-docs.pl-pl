---
title: Konfigurowanie konta laboratorium przy użyciu usługi Azure Lab Services | Microsoft Docs
description: W tym samouczku skonfigurujesz konto laboratorium przy użyciu usługi Azure Lab Services.
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
ms.openlocfilehash: 600be7518bc526d3f147bb16377677854b676f63
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823133"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Samouczek: konfigurowanie konta laboratorium przy użyciu usługi Azure Lab Services
W usłudze Azure Lab Services konto laboratorium służy jako centralne konto, w ramach którego są zarządzane laboratoria organizacji. Na koncie laboratorium możesz nadawać innym użytkownikom uprawnienia do tworzenia laboratoriów oraz określać zasady dotyczące wszystkich laboratoriów w ramach konta laboratorium. W tym samouczku nauczysz się, jak utworzyć konto laboratorium jako administrator laboratorium. 

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Tworzenie konta laboratorium
> * Dodawanie użytkownika do roli twórcy laboratorium

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="create-a-lab-account"></a>Tworzenie konta laboratorium
Następujące kroki ilustrują tworzenie konta laboratorium w usłudze Azure Lab Services przy użyciu witryny Azure Portal. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Z menu głównego po lewej stronie wybierz polecenie **Utwórz zasób**.
3. Wyszukaj pozycję **Lab Services** w witrynie Azure Marketplace, a następnie wybierz pozycję **Lab Services** z listy rozwijanej. 
4. Wybierz pozycję **Lab Services (wersja zapoznawcza)** z przefiltrowanej listy usług. 
1. W oknie **Tworzenie konta laboratorium** wybierz pozycję **Utwórz**.
2. W oknie **Konto laboratorium** wykonaj następujące czynności: 
    1. W obszarze **Nazwa konta laboratorium** wprowadź nazwę. 
    2. Wybierz **subskrypcję platformy Azure**, w której chcesz utworzyć konto laboratorium.
    3. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową** i wprowadź nazwę grupy zasobów.
    4. W obszarze **Lokalizacja** wybierz lokalizację/region, w których chcesz utworzyć konto laboratorium. 
    5. Wybierz pozycję **Utwórz**. 

        ![Okno Tworzenie konta laboratorium](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Jeśli nie widzisz strony konta laboratorium, wybierz przycisk **powiadomień**, a następnie kliknij przycisk **Przejdź do zasobu** w obszarze powiadomień. 

    ![Okno Tworzenie konta laboratorium](../media/tutorial-setup-lab-account/notification-go-to-resource.png)    
6. Zobaczysz następującą stronę **konta laboratorium**:

    ![Strona konta laboratorium](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Dodawanie użytkownika do roli twórcy laboratorium
Aby przyznać nauczycielom uprawnienie do tworzenia laboratoriów na potrzeby zajęć, dodaj ich do roli Twórca laboratorium:

1. Na stronie **Konto laboratorium** wybierz pozycję **(Kontrola dostępu (IAM))** i kliknij pozycję **+ Dodaj** na pasku narzędzi. 

    ![Strona konta laboratorium](../media/tutorial-setup-lab-account/access-control.png)
2. Na stronie **Dodawanie uprawnień** wybierz pozycję **Twórca laboratorium** w obszarze **Rola**, wybierz użytkownika do dodania do roli Twórcy laboratorium, a następnie wybierz pozycję **Zapisz**. 

    ![Dodawanie użytkownika do roli twórcy laboratorium](../media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)


## <a name="next-steps"></a>Następne kroki
W tym samouczku utworzono konto laboratorium. Aby dowiedzieć się więcej o sposobie tworzenia laboratorium na potrzeby zajęć jako zawodu, przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Konfigurowanie laboratorium na potrzeby zajęć](tutorial-setup-classroom-lab.md)

