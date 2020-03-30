---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 03/19/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: a00291182059506aeab9cde965fa4cbd5177ecf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132109"
---
Jeśli użytkownik nie ma wymaganego dostępu do stosowania tagów, możesz przypisać rolę **Współautor tagów** do użytkownika. Aby uzyskać więcej informacji, zobacz [Samouczek: Udzielanie użytkownikowi dostępu do zasobów platformy Azure przy użyciu funkcji RBAC i witryny Azure portal](../articles/role-based-access-control/quickstart-assign-role-user-portal.md).

1. Aby wyświetlić znaczniki zasobu lub grupy zasobów, poszukaj istniejących tagów w przeglądzie. Jeśli wcześniej nie zastosowano tagów, lista będzie pusta.

   ![Wyświetlanie znaczników dla grupy zasobów lub zasobów](./media/resource-manager-tag-resources/view-tags.png)

1. Aby dodać znacznik, wybierz pozycję **Kliknij tutaj, aby dodać znaczniki**.

1. Podaj nazwę i wartość.

   ![Dodawanie tagu](./media/resource-manager-tag-resources/add-tag.png)

1. Kontynuuj dodawanie tagów w razie potrzeby. Po zakończeniu wybierz pozycję **Zapisz**.

   ![Zapisywanie tagów](./media/resource-manager-tag-resources/save-tags.png)

1. Znaczniki są teraz wyświetlane w przeglądzie.

   ![Pokaż znaczniki](./media/resource-manager-tag-resources/view-new-tags.png)

1. Aby dodać lub usunąć znacznik, wybierz opcję **Zmień**.

1. Aby usunąć znacznik, wybierz ikonę kosza. Następnie wybierz pozycję **Zapisz**.

   ![Usuń znacznik](./media/resource-manager-tag-resources/delete-tag.png)

Aby zbiorczo przypisać znaczniki do wielu zasobów:

1. Z dowolnej listy zasobów zaznacz pole wyboru dla zasobów, które chcesz przypisać tag. Następnie wybierz pozycję **Przypisz znaczniki**.

   ![Zaznaczanie wielu zasobów](./media/resource-manager-tag-resources/select-multiple-resources.png)

1. Dodaj nazwy i wartości. Po zakończeniu wybierz pozycję **Zapisz**.

   ![Wybierz pozycję Przypisz](./media/resource-manager-tag-resources/select-assign.png)

Aby wyświetlić wszystkie zasoby za pomocą tagu:

1. W menu portalu platformy Azure wyszukaj **tagi**. Wybierz go z dostępnych opcji.

   ![Znajdź według tagu](./media/resource-manager-tag-resources/find-tags-general.png)

1. Wybierz znacznik do wyświetlania zasobów.

   ![Wybierz znacznik](./media/resource-manager-tag-resources/select-tag.png)

1. Zostaną wyświetlone wszystkie zasoby z tym tagiem.

   ![Wyświetlanie zasobów według tagu](./media/resource-manager-tag-resources/view-resources-by-tag.png)
