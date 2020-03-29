---
title: Usługa Azure Data Box Edge zarządza użytkownikami | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak używać witryny Azure Portal do zarządzania użytkownikami w witrynie Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/11/2019
ms.author: alkohli
ms.openlocfilehash: 443ed983a0eec5dfd8f7a917fbc1440cd66c3db3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78946139"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-data-box-edge"></a>Korzystanie z witryny Azure Portal do zarządzania użytkownikami w witrynie Azure Data Box Edge

W tym artykule opisano sposób zarządzania użytkownikami w usłudze Azure Data Box Edge. Usługę Azure Data Box Edge można zarządzać za pośrednictwem witryny Azure portal lub lokalnego interfejsu użytkownika sieci Web. Witryna Azure Portal umożliwia dodawanie, modyfikowanie i usuwanie użytkowników.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Dodawanie użytkownika
> * Modyfikowanie harmonogramu
> * Usuwanie użytkownika

## <a name="about-users"></a>Informacje o użytkownikach

Użytkownicy mogą mieć prawo tylko do odczytu lub pełne uprawnienia. Jak sugerują nazwy, użytkownicy z prawem tylko do odczytu mogą wyłącznie wyświetlać dane udziału. Użytkownicy z pełnymi uprawnieniami mogą odczytywać dane udziału, dokonywać zapisu w tych udziałach oraz modyfikować lub usuwać dane udziału.

 - **Użytkownik z pełnymi uprawnieniami** — użytkownik lokalny z pełnym dostępem.
 - **Użytkownik z prawem tylko do odczytu** — użytkownik lokalny z dostępem tylko do odczytu. Ci użytkownicy są powiązani z udziałami, które umożliwiają wykonywanie operacji tylko do odczytu.

Uprawnienia użytkownika są definiowane podczas jego tworzenia w czasie tworzenia udziału. Modyfikacja uprawnień na poziomie udziału nie jest obecnie obsługiwana.

## <a name="add-a-user"></a>Dodawanie użytkownika

Wykonaj następujące czynności w witrynie Azure Portal, aby dodać użytkownika.

1. W witrynie Azure portal przejdź do zasobu usługi Data Box Edge, a następnie przejdź do **strony Przegląd > użytkowników**. Wybierz **+ Dodaj użytkownika** na pasku poleceń.

    ![Wybierz dodaj użytkownika](media/data-box-edge-manage-users/add-user-1.png)

2. Określ nazwę i hasło użytkownika, którego chcesz dodać. Potwierdź hasło i **wybierz**dodaj .

    ![Określ nazwę użytkownika i hasło](media/data-box-edge-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Oto użytkownicy zarezerwowani przez system, których nie należy używać: Administrator, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Guest.  

3. Powiadomienie jest wyświetlane po rozpoczęciu i zakończeniu tworzenia użytkownika. Po utworzeniu użytkownika na pasku poleceń wybierz pozycję **Odśwież,** aby wyświetlić zaktualizowaną listę użytkowników.


## <a name="modify-user"></a>Modyfikowanie harmonogramu

Po utworzeniu użytkownika możesz zmienić przypisane do niego hasło. Wybierz z listy użytkowników. Wprowadź i potwierdź nowe hasło. Zapisz zmiany.
 
![Modyfikowanie harmonogramu](media/data-box-edge-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Usuwanie użytkownika

Wykonaj następujące czynności w witrynie Azure Portal, aby usunąć użytkownika.


1. W witrynie Azure portal przejdź do zasobu usługi Data Box Edge, a następnie przejdź do **strony Przegląd > użytkowników**.

    ![Wybierz użytkownika do usunięcia](media/data-box-edge-manage-users/delete-user-1.png)

2. Wybierz użytkownika z listy użytkowników, a następnie wybierz pozycję **Usuń**.  

   ![Wybieranie pozycji Usuń](media/data-box-edge-manage-users/delete-user-2.png)

3. Po wyświetleniu monitu potwierdź usunięcie. 

   ![Potwierdzenie usunięcia](media/data-box-edge-manage-users/delete-user-3.png)

Lista użytkowników zostanie zaktualizowana w celu uwzględnienia usuniętego użytkownika.

![Zaktualizowana lista użytkowników](media/data-box-edge-manage-users/delete-user-4.png)


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [zarządzać przepustowością](data-box-edge-manage-bandwidth-schedules.md).
