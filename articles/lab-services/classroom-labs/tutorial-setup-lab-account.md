---
title: Konfigurowanie konta laboratorium przy użyciu usługi Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak skonfigurować konto laboratorium w usłudze Azure Lab Services, dodać twórcę laboratorium i określić obrazy portalu Marketplace, które mają być używane przez laboratoria na koncie laboratorium.
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
ms.date: 02/10/2020
ms.author: spelluru
ms.openlocfilehash: dba6a4c07691f3d7ec88d8b889e68d6ac7116f07
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239448"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Samouczek: konfigurowanie konta laboratorium przy użyciu usługi Azure Lab Services
W usłudze Azure Lab Services konto laboratorium służy jako centralne konto, w ramach którego są zarządzane laboratoria organizacji. Na koncie laboratorium możesz nadawać innym użytkownikom uprawnienia do tworzenia laboratoriów oraz określać zasady dotyczące wszystkich laboratoriów w ramach konta laboratorium. W tym samouczku dowiesz się, jak utworzyć konto laboratorium. 

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Tworzenie konta laboratorium
> * Dodawanie użytkownika do roli twórcy laboratorium

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="create-a-lab-account"></a>Tworzenie konta laboratorium
Następujące kroki ilustrują tworzenie konta laboratorium w usłudze Azure Lab Services przy użyciu witryny Azure Portal. 

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz pozycję **Wszystkie usługi** w menu po lewej stronie. Wybierz **devops** z **kategorii**. Następnie wybierz pozycję **Usługi laboratoryjne**. Jeśli wybierzesz`*`gwiazdkę ( ) obok **pozycji Usługi laboratoryjne,** zostanie ono dodane do sekcji **ULUBIONE** w menu po lewej stronie. Od następnego razu wybierz **usługi lab** w obszarze **ULUBIONE**.

    ![Wszystkie usługi -> Usługi laboratoryjne](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Na stronie **Usługi labe** wybierz pozycję **Dodaj** na pasku narzędzi lub wybierz pozycję **Utwórz konto laboratorium** na stronie. 

    ![Wybieranie pozycji Dodaj na stronie Konta laboratorium](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Na karcie **Podstawy** na stronie **Tworzenie konta w laboratorium** wykonaj następujące czynności: 
    1. W obszarze **Nazwa konta laboratorium** wprowadź nazwę. 
    2. Wybierz **subskrypcję platformy Azure**, w której chcesz utworzyć konto laboratorium.
    3. W obszarze **Grupa zasobów**wybierz istniejącą grupę zasobów lub wybierz pozycję **Utwórz nowy**i wprowadź nazwę grupy zasobów.
    4. W obszarze **Lokalizacja**wybierz lokalizację/region, w którym chcesz utworzyć konto laboratorium. 

        ![Konto laboratorium - strona podstawy](../media/tutorial-setup-lab-account/lab-account-basics-page.png)
    5. Wybierz pozycję **Przegląd + utwórz**.
    6. Przejrzyj podsumowanie i wybierz pozycję **Utwórz**. 

        ![Recenzja + tworzenie -> Tworzenie](../media/tutorial-setup-lab-account/create-button.png)    
5. Po zakończeniu wdrażania rozwiń węzeł **Następne kroki**i wybierz pozycję **Przejdź do zasobu**. 

    ![Przejdź do strony konta laboratorium](../media/tutorial-setup-lab-account/go-to-lab-account.png)
6. Upewnij się, że widzisz stronę **Konto laboratorium.** 

    ![Strona konta laboratorium](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Dodawanie użytkownika do roli twórcy laboratorium
Aby skonfigurować laboratorium na potrzeby zajęć w ramach konta laboratorium, użytkownik musi być członkiem roli **Twórca laboratorium** na koncie laboratorium. Aby przyznać nauczycielom uprawnienie do tworzenia laboratoriów na potrzeby zajęć, dodaj ich do roli **Twórca laboratorium**:

> [!NOTE]
> Konto, którego użyto do utworzenia konta laboratorium, jest automatycznie dodawane do tej roli. Jeśli planujesz użyć tego samego konta użytkownika do utworzenia laboratorium w klasie w tym samouczku, pomiń ten krok. 

1. Na stronie **Konto laboratorium** wybierz pozycję **Kontrola dostępu (IAM)**, wybierz pozycję **+ Dodaj** na pasku narzędzi, a następnie wybierz pozycję **+ Dodaj przypisanie roli** na pasku narzędzi. 

    ![Kontrola dostępu -> przycisk Dodaj przypisanie roli](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Na stronie **Dodawanie przypisania roli** wybierz pozycję **Twórca laboratorium** w obszarze **Rola**, wybierz użytkownika do dodania do roli Twórcy laboratorium, a następnie wybierz pozycję **Zapisz**. 

    ![Dodawanie twórcy laboratorium](../media/tutorial-setup-lab-account/add-lab-creator.png)


## <a name="next-steps"></a>Następne kroki
W tym samouczku utworzono konto laboratorium. Aby dowiedzieć się, jak utworzyć laboratorium w klasie jako profesor, przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Konfigurowanie laboratorium na potrzeby zajęć](tutorial-setup-classroom-lab.md)

