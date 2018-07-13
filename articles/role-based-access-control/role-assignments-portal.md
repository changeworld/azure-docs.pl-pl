---
title: Zarządzanie dostępem przy użyciu kontroli RBAC i witryny Azure Portal | Microsoft Docs
description: Dowiedz się, jak zarządzać dostępem dla użytkowników, grup i aplikacji przy użyciu kontroli dostępu opartej na rolach (RBAC, Role Based Access Control) i witryny Azure Portal. Obejmuje to wyświetlanie dostępu, jego przyznawanie i usuwanie.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/11/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: aaa36d850516ff4d8e40b62c588347468da5c6d2
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008165"
---
# <a name="manage-access-using-rbac-and-the-azure-portal"></a>Zarządzanie dostępem przy użyciu kontroli RBAC i witryny Azure Portal

[Kontrola dostępu oparta na rolach (RBAC, Role Based Access Control)](overview.md) to sposób zarządzania dostępem do zasobów na platformie Azure. W tym artykule opisano, jak zarządzać dostępem dla użytkowników, grup i aplikacji przy użyciu kontroli dostępu opartej na rolach i witryny Azure Portal.

## <a name="list-roles"></a>Tworzenie listy ról

Definicja roli jest kolekcją uprawnień służącą do przypisywania ról. Na platformie Azure jest ponad 60 [ról wbudowanych](built-in-roles.md).

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

1. Wybierz subskrypcję.

1. Wybierz pozycję **Kontrola dostępu (IAM)**.

   ![Opcja Role](./media/role-assignments-portal/list-subscription-access-control.png)

1. Wybierz pozycję **Role**, aby wyświetlić listę wszystkich ról wbudowanych i niestandardowych.

   ![Opcja Role](./media/role-assignments-portal/roles-option.png)

   Widocznych jest wiele użytkowników i grup, których przypisano do poszczególnych ról.

   ![Lista ról](./media/role-assignments-portal/roles-list.png)

## <a name="list-access"></a>Tworzenie listy dostępu

Zarządzanie dostępem obejmuje ustalanie, kto ma dostęp, jakie ma uprawnienia i na jakim poziomie. Aby utworzyć listę dostępu, tworzy się listę przypisań ról.

### <a name="list-role-assignments-for-a-subscription"></a>Tworzenie listy przypisań ról dla subskrypcji

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

1. Wybierz subskrypcję.

1. Wybierz pozycję **Kontrola dostępu (IAM)**.

    W bloku Kontrola dostępu (IAM), określanym też jako „zarządzanie tożsamościami i dostępem”, można sprawdzić, kto ma dostęp do tej subskrypcji i jaką ma rolę.

    ![Blok Kontrola dostępu (IAM)](./media/role-assignments-portal/subscription-access-control.png)

    Klasyczni administratorzy i współadministratorzy są traktowani jako właściciele subskrypcji w modelu kontroli dostępu opartej na rolach.


### <a name="list-role-assignments-for-a-resource-group"></a>Tworzenie listy przypisań ról dla grupy zasobów

1. Na liście nawigacji wybierz pozycję **Grupy zasobów**.

1. Wybierz grupę zasobów, a następnie wybierz pozycję **Kontrola dostępu (IAM)**.

   W bloku Kontrola dostępu (IAM) można sprawdzić, kto ma dostęp do tej grupy zasobów. Należy zauważyć, że niektóre role należą do zakresu **tego zasobu**, a inne są **dziedziczone** z innego zakresu. Dostęp jest przypisywany specjalnie do grupy zasobów albo dziedziczony z przypisania do subskrypcji nadrzędnej.

   ![Grupy zasobów](./media/role-assignments-portal/resource-group-access-control.png)

### <a name="list-role-assignments-for-a-user"></a>Tworzenie listy przypisań ról dla użytkownika

1. Na liście nawigacji wybierz pozycję **Azure Active Directory**.

1. Wybierz pozycję **Użytkownicy**, aby otworzyć obszar **Wszyscy użytkownicy**.

   ![Blok Wszyscy użytkownicy w usłudze Azure Active Directory](./media/role-assignments-portal/aad-all-users.png)

1. Wybierz użytkownika z listy.

1. W sekcji **Zarządzanie** wybierz pozycję **Zasoby platformy Azure**.

   ![Zasoby platformy Azure użytkownika usługi Azure Active Directory](./media/role-assignments-portal/aad-user-azure-resources.png)

   W bloku zasobów platformy Azure zobaczysz przypisania roli dla wybranego użytkownika i wybranej subskrypcji. Ta lista zawiera tylko przypisania roli dla zasobów, które mają uprawnienia do odczytu. Na przykład jeśli użytkownik ma również przypisania ról, których nie można odczytać, te przypisania roli nie pojawi się na liście.

1. Jeśli masz wiele subskrypcji, możesz wybrać **subskrypcji** listy rozwijanej, aby zobaczyć przypisań ról w innej subskrypcji.

## <a name="grant-access"></a>Udzielanie dostępu

Aby udzielić dostępu za pomocą kontroli dostępu opartej na rolach, tworzy się przypisanie roli.

### <a name="create-a-role-assignment-at-a-subscription-scope"></a>Tworzenie przypisania roli w zakresie subskrypcji

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

1. Wybierz subskrypcję.

1. Wybierz pozycję **Kontrola dostępu (IAM)**, aby wyświetlić bieżącą listę przypisań ról w zakresie subskrypcji.

   ![Blok Kontrola dostępu (IAM) dla grupy zasobów](./media/role-assignments-portal/grant-subscription-access-control.png)

1. Wybierz pozycję **Dodaj**, aby otworzyć okienko **Dodaj uprawnienia**.

   Jeśli nie masz uprawnień do przypisywania ról, nie zobaczysz opcji **Dodaj**.

   ![Okienko dodawania uprawnień](./media/role-assignments-portal/add-permissions.png)

1. Z listy rozwijanej **Rola** wybierz rolę, taką jak **Współautor·maszyny·wirtualnej**.

1. Z listy **Wybierz** wybierz użytkownika, grupę lub aplikację. Jeśli nie widzisz podmiotu zabezpieczeń na liście, możesz wpisać tekst w polu **Wybierz**, aby wyszukać w katalogu nazwy wyświetlane, adresy e-mail i identyfikatory obiektów.

1. Wybierz pozycję **Zapisz**, aby utworzyć przypisanie roli.

   Po kilku chwilach podmiotowi zabezpieczeń zostanie przypisana dana rola w zakresie subskrypcji.

### <a name="create-a-role-assignment-at-a-resource-group-scope"></a>Tworzenie przypisania roli w zakresie grupy zasobów

1. Na liście nawigacji wybierz pozycję **Grupy zasobów**.

1. Wybierz grupę zasobów.

1. Wybierz pozycję **Kontrola dostępu (IAM)**, aby wyświetlić bieżącą listę przypisań ról w zakresie grupy zasobów.

   ![Blok Kontrola dostępu (IAM) dla grupy zasobów](./media/role-assignments-portal/grant-resource-group-access-control.png)

1. Wybierz pozycję **Dodaj**, aby otworzyć okienko **Dodaj uprawnienia**.

   Jeśli nie masz uprawnień do przypisywania ról, nie zobaczysz opcji **Dodaj**.

   ![Okienko dodawania uprawnień](./media/role-assignments-portal/add-permissions.png)

1. Z listy rozwijanej **Rola** wybierz rolę, taką jak **Współautor·maszyny·wirtualnej**.

1. Z listy **Wybierz** wybierz użytkownika, grupę lub aplikację. Jeśli nie widzisz podmiotu zabezpieczeń na liście, możesz wpisać tekst w polu **Wybierz**, aby wyszukać w katalogu nazwy wyświetlane, adresy e-mail i identyfikatory obiektów.

1. Wybierz pozycję **Zapisz**, aby utworzyć przypisanie roli.

   Po kilku chwilach podmiotowi zabezpieczeń zostanie przypisana dana rola w zakresie grupy zasobów.

## <a name="remove-access"></a>Usuwanie dostępu

Aby usunąć dostęp za pomocą kontroli dostępu opartej na rolach, usuwa się przypisanie roli.

### <a name="remove-a-role-assignment"></a>Usuwanie przypisania roli

1. Otwórz blok **Kontrola dostępu (IAM)** dla subskrypcji, grupy zasobów lub zasobu, który zawiera przypisanie roli do usunięcia.

1. Na liście przypisań ról dodaj znacznik wyboru obok podmiotu zabezpieczeń z przypisaniem roli, które chcesz usunąć.

   ![Komunikat dotyczący usuwania przypisania roli](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Wybierz pozycję **Usuń**.

   ![Komunikat dotyczący usuwania przypisania roli](./media/role-assignments-portal/remove-role-assignment.png)

1. W komunikacie dotyczącym usuwania przypisania roli wybierz pozycję **Tak**.

Dziedziczonych przypisań ról nie można usunąć. Aby usunąć odziedziczone przypisanie roli, należy to zrobić w zakresie, w którym je utworzono. W kolumnie **Zakres** obok pola **Dziedziczone** znajduje się link umożliwiający przejście do zasobów, w ramach których ta rola została przypisana. Przejdź do zakresu wskazanego w tym miejscu, aby usunąć przypisanie roli.

## <a name="next-steps"></a>Kolejne kroki

* [Szybki start: udzielanie dostępu użytkownikowi za pomocą kontroli dostępu opartej na rolach i witryny Azure Portal](quickstart-assign-role-user-portal.md)
* [Samouczek: udzielanie dostępu użytkownikowi za pomocą kontroli dostępu opartej na rolach i programu Azure PowerShell](tutorial-role-assignments-user-powershell.md)
* [Role wbudowane](built-in-roles.md)
