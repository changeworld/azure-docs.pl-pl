---
title: Usługa Azure Edge pole danych, zarządzanie użytkownikami | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak zarządzać użytkownikami na krawędzi sieci Azure Data Box za pomocą witryny Azure portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/11/2019
ms.author: alkohli
ms.openlocfilehash: 68f8ad903f967812c4a416c732b35fa1712404cd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60756702"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-data-box-edge"></a>Użyj witryny Azure portal, aby zarządzać użytkownikami na krawędzi sieci Azure Data Box

W tym artykule opisano, jak zarządzać użytkownikami na krawędzi sieci Azure Data Box. Można zarządzać krawędź pola danych platformy Azure za pośrednictwem witryny Azure portal lub za pomocą lokalnego interfejsu użytkownika sieci web. Witryna Azure Portal umożliwia dodawanie, modyfikowanie i usuwanie użytkowników.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Dodawanie użytkownika
> * Modyfikowanie harmonogramu
> * Usuwanie użytkownika

## <a name="about-users"></a>Informacje o użytkownikach

Użytkownicy mogą mieć prawo tylko do odczytu lub pełne uprawnienia. Jak sugerują nazwy, użytkownicy z prawem tylko do odczytu mogą wyłącznie wyświetlać dane udziału. Użytkownicy z pełnymi uprawnieniami mogą odczytywać dane udziału, dokonywać zapisu w tych udziałach oraz modyfikować lub usuwać dane udziału.

 - **Użytkownik z pełnymi uprawnieniami** — użytkownik lokalny z pełnym dostępem.
 - **Użytkownik z prawem tylko do odczytu** — użytkownik lokalny z dostępem tylko do odczytu. Ci użytkownicy są powiązani z udziałami, które umożliwiają wykonywanie operacji tylko do odczytu.

Uprawnienia użytkownika są definiowane podczas jego tworzenia w czasie tworzenia udziału. Po zdefiniowaniu uprawnień związanych z użytkownikiem można je zmodyfikować za pomocą Eksploratora plików. 


## <a name="add-a-user"></a>Dodawanie użytkownika

Wykonaj następujące czynności w witrynie Azure Portal, aby dodać użytkownika.

1. W witrynie Azure portal przejdź do zasobu krawędź pola danych, a następnie przejdź do **Przegląd > Użytkownicy**. Wybierz **+ Dodaj użytkownika** na pasku poleceń.

    ![Wybieranie pozycji Dodaj użytkownika](media/data-box-edge-manage-users/add-user-1.png)

2. Określ nazwę i hasło użytkownika, którego chcesz dodać. Potwierdź hasło, a następnie wybierz **Dodaj**.

    ![Określ nazwę użytkownika i hasło](media/data-box-edge-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Ci użytkownicy są zastrzeżone przez system i nie powinny być używane: Administrator, EdgeUser EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, konta domyślnego, gościa.  

3. Zostaje wyświetlone powiadomienie, gdy rozpoczyna się tworzenie użytkownika i zakończone. Po utworzeniu użytkownika na pasku poleceń Wybierz **Odśwież** Aby wyświetlić zaktualizowaną listę użytkowników.


## <a name="modify-user"></a>Modyfikowanie harmonogramu

Po utworzeniu użytkownika możesz zmienić przypisane do niego hasło. Wybierz z listy użytkowników. Wprowadź i Potwierdź nowe hasło. Zapisz zmiany.
 
![Modyfikowanie harmonogramu](media/data-box-edge-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Usuwanie użytkownika

Wykonaj następujące czynności w witrynie Azure Portal, aby usunąć użytkownika.


1. W witrynie Azure portal przejdź do zasobu krawędź pola danych, a następnie przejdź do **Przegląd > Użytkownicy**.

    ![Wybierz użytkownika do usunięcia](media/data-box-edge-manage-users/delete-user-1.png)

2. Wybierz użytkownika z listy użytkowników, a następnie wybierz pozycję **Usuń**.  

   ![Wybieranie pozycji Usuń](media/data-box-edge-manage-users/delete-user-2.png)

3. Po wyświetleniu monitu potwierdź usunięcie. 

   ![Potwierdzenie usunięcia](media/data-box-edge-manage-users/delete-user-3.png)

Lista użytkowników zostanie zaktualizowana w celu uwzględnienia usuniętego użytkownika.

![Zaktualizowana lista użytkowników](media/data-box-edge-manage-users/delete-user-4.png)


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [zarządzać przepustowością](data-box-edge-manage-bandwidth-schedules.md).
