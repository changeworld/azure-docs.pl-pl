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
ms.openlocfilehash: d4bfd684792e5ec13b2a4a020fa21249f1888657
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226356"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Samouczek: konfigurowanie konta laboratorium przy użyciu usługi Azure Lab Services
W usłudze Azure Lab Services konto laboratorium służy jako centralne konto, w ramach którego są zarządzane laboratoria organizacji. Na koncie laboratorium możesz nadawać innym użytkownikom uprawnienia do tworzenia laboratoriów oraz określać zasady dotyczące wszystkich laboratoriów w ramach konta laboratorium. W tym samouczku nauczysz się, jak utworzyć konto laboratorium jako administrator laboratorium. 

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Tworzenie konta laboratorium
> * Dodawanie użytkownika do roli twórcy laboratorium
> * Określanie obrazów w portalu Marketplace dostępnych dla właścicieli laboratorium

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
Aby skonfigurować laboratorium na potrzeby zajęć w ramach konta laboratorium, użytkownik musi być członkiem roli **Twórca laboratorium** na koncie laboratorium. Konto, którego użyto do utworzenia konta laboratorium, jest automatycznie dodawane do tej roli. Jeśli planujesz użyć tego samego konta użytkownika w celu utworzenia laboratorium na potrzeby zajęć, możesz pominąć ten krok. Aby użyć innego konta użytkownika do utworzenia laboratorium na potrzeby zajęć, wykonaj następujące czynności: 

Aby przyznać nauczycielom uprawnienie do tworzenia laboratoriów na potrzeby zajęć, dodaj ich do roli **Twórca laboratorium**:

1. Na stronie **Konto laboratorium** wybierz pozycję **(Kontrola dostępu (IAM))** i kliknij pozycję **+ Dodaj** na pasku narzędzi. 

    ![Strona konta laboratorium](../media/tutorial-setup-lab-account/access-control.png)
2. Na stronie **Dodawanie uprawnień** wybierz pozycję **Twórca laboratorium** w obszarze **Rola**, wybierz użytkownika do dodania do roli Twórcy laboratorium, a następnie wybierz pozycję **Zapisz**. 

    ![Dodawanie użytkownika do roli twórcy laboratorium](../media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)

## <a name="specify-marketplace-images-available-to-lab-owners"></a>Określanie obrazów w portalu Marketplace dostępnych dla właścicieli laboratorium
W tej sekcji określisz obrazy w portalu Marketplace, których właściciele laboratorium będą mogli używać do tworzenia laboratorium na potrzeby zajęć. 

1. W menu po lewej stronie wybierz pozycję **Obrazy w portalu Marketplace**. Domyślnie zobaczysz pełną listę obrazów (włączonych i wyłączonych). Tę listę można filtrować, aby wyświetlić tylko obrazy włączone/wyłączone, wybierając z listy rozwijanej u góry opcję **Tylko włączone**/**Tylko wyłączone**. 

    ![Strona Obrazy w portalu Marketplace](../media/tutorial-setup-lab-account/marketplace-images-page.png)
2. Aby w portalu Marketplace **wyłączyć** obraz, który zostały włączony, wykonaj jedną z następujących czynności: 
    1. Wybierz pozycję **... (wielokropek)** w ostatniej kolumnie, a następnie polecenie **Wyłącz obraz**. 

        ![Wyłączanie jednego obrazu](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Wybierz jeden lub większą liczbę obrazów z listy przez zaznaczenie pól wyboru przed nazwami obrazów, a następnie wybierz pozycję **Wyłącz wybrane obrazy**. 

        ![Wyłączanie wielu obrazów](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. Podobnie, aby w portalu Marketplace **włączyć** obraz, wykonaj jedną z następujących czynności: 
    1. Wybierz pozycję **... (wielokropek)** w ostatniej kolumnie, a następnie polecenie **Włącz obraz**. 
    2. Wybierz jeden lub większą liczbę obrazów z listy przez zaznaczenie pól wyboru przed nazwami obrazów, a następnie wybierz pozycję **Włącz wybrane obrazy**. 

## <a name="next-steps"></a>Następne kroki
W tym samouczku utworzono konto laboratorium. Aby dowiedzieć się więcej o sposobie tworzenia laboratorium na potrzeby zajęć jako zawodu, przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Konfigurowanie laboratorium na potrzeby zajęć](tutorial-setup-classroom-lab.md)

