---
title: Jak dodać użytkownika jako właściciela laboratorium w Azure Lab Services
description: W tym artykule opisano, jak administrator może dodać użytkownika jako właściciela do laboratorium.
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
ms.date: 12/20/2019
ms.author: spelluru
ms.openlocfilehash: 9e007ee6c95843b7e8ef9d7a701237034811489e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480855"
---
# <a name="how-to-add-a-user-as-an-owner-of-a-classroom-lab-in-azure-lab-services"></a>Jak dodać użytkownika jako właściciela laboratorium zajęć w Azure Lab Services
W tym artykule pokazano, jak dodać użytkownika jako właściciela laboratorium w Azure Lab Services.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>Dodawanie użytkownika do roli czytelnik dla konta laboratorium
1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz pozycję **Wszystkie usługi** w menu po lewej stronie. Wyszukaj pozycję **usługi laboratoryjne**, a następnie wybierz ją.
3. Wybierz z listy **konto laboratorium** . 
2. Na **stronie konto laboratorium**wybierz pozycję **Access Control (IAM)** w menu po lewej stronie. 
2. Na stronie **Kontrola dostępu (IAM)** wybierz pozycję **Dodaj** na pasku narzędzi, a następnie wybierz pozycję **Dodaj przypisanie roli**.

    ![Przypisanie roli dla konta laboratorium ](../media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. Na stronie **Dodawanie przypisania roli** wykonaj następujące czynności: 
    1. Wybierz opcję **czytelnik** dla **roli**. 
    2. Wybierz użytkownika. 
    3. Wybierz pozycję **Zapisz**. 

        ![Dodawanie użytkownika do roli czytelnik dla konta laboratorium ](../media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>Dodawanie użytkownika do roli właściciela laboratorium

1. Na stronie **konto laboratorium** wybierz pozycję **Wszystkie laboratoria** w menu po lewej stronie.
2. Wybierz **laboratorium** , do którego chcesz dodać użytkownika jako właściciela. 
    
    ![Wybieranie laboratorium ](../media/how-to-add-user-lab-owner/select-lab.png)    
3. Na stronie **laboratorium** wybierz pozycję **Kontrola dostępu (IAM)** w menu po lewej stronie.
4. Na stronie **Kontrola dostępu (IAM)** wybierz pozycję **Dodaj** na pasku narzędzi, a następnie wybierz pozycję **Dodaj przypisanie roli**.
5. Na stronie **Dodawanie przypisania roli** wykonaj następujące czynności: 
    1. Wybierz **właściciela** dla **roli**. 
    2. Wybierz użytkownika. 
    3. Wybierz pozycję **Zapisz**. 

## <a name="next-steps"></a>Następne kroki
Upewnij się, że użytkownik widzi laboratorium po zalogowaniu się do [portalu usługi Lab Services](https://labs.azure.com).