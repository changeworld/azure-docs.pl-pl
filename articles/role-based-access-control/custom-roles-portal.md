---
title: Tworzenie lub aktualizowanie ról niestandardowych platformy Azure przy użyciu portalu Azure (Preview) — Azure RBAC
description: Dowiedz się, jak utworzyć role niestandardowe platformy Azure dla kontroli dostępu opartej na rolach platformy Azure (Azure RBAC) przy użyciu witryny Azure portal. Obejmuje to sposób tworzenia, tworzenia, aktualizowania i usuwania ról niestandardowych.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.author: rolyon
ms.openlocfilehash: 3204cdf51f3f37588f684f801a811f569b337d13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77674872"
---
# <a name="create-or-update-azure-custom-roles-using-the-azure-portal-preview"></a>Tworzenie lub aktualizowanie ról niestandardowych platformy Azure przy użyciu witryny Azure Portal (Wersja zapoznawcza)

> [!IMPORTANT]
> Role niestandardowe platformy Azure przy użyciu witryny Azure portal jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jeśli [wbudowane role platformy Azure](built-in-roles.md) nie spełniają określonych potrzeb organizacji, możesz utworzyć własne role niestandardowe platformy Azure. Podobnie jak wbudowane role, można przypisać role niestandardowe do użytkowników, grup i podmiotów usługi w zakresach subskrypcji i grup zasobów. Role niestandardowe są przechowywane w katalogu usługi Azure Active Directory (Azure AD) i mogą być współużytkowane w ramach subskrypcji. Każdy katalog może mieć maksymalnie 5000 ról niestandardowych. Role niestandardowe można tworzyć za pomocą witryny Azure Portal, Azure PowerShell, Interfejsu wiersza polecenia platformy Azure lub interfejsu API REST. W tym artykule opisano sposób tworzenia ról niestandardowych przy użyciu witryny Azure portal (obecnie w wersji zapoznawczej).

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć role niestandardowe, potrzebujesz:

- Uprawnienia do tworzenia ról niestandardowych, takie jak [Właściciel](built-in-roles.md#owner) lub [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator)

## <a name="step-1-determine-the-permissions-you-need"></a>Krok 1: Określ uprawnienia, których potrzebujesz

Platforma Azure ma tysiące uprawnień, które potencjalnie można uwzględnić w roli niestandardowej. Oto cztery sposoby określania uprawnień, które chcesz dodać do roli niestandardowej:

| Metoda | Opis |
| --- | --- |
| Przyjrzyj się istniejącym rolom | Możesz przyjrzeć się istniejącym rolom, aby zobaczyć, jakie uprawnienia są używane. Aby uzyskać więcej informacji, zobacz [wbudowane role platformy Azure](built-in-roles.md). |
| Wyszukiwanie uprawnień według słów kluczowych | Podczas tworzenia roli niestandardowej przy użyciu witryny Azure portal, można wyszukiwać uprawnienia według słów kluczowych. Na przykład można wyszukać *uprawnienia maszyny wirtualnej* lub *rozliczeń.* Ta funkcja wyszukiwania jest opisana w dalszej części [kroku 4: Uprawnienia](#step-4-permissions). |
| Pobierz wszystkie uprawnienia | Podczas tworzenia roli niestandardowej przy użyciu witryny Azure portal, można pobrać wszystkie uprawnienia jako plik CSV, a następnie przeszukać ten plik. W okienku **Dodawanie uprawnień** kliknij przycisk **Pobierz wszystkie uprawnienia,** aby pobrać wszystkie uprawnienia. Aby uzyskać więcej informacji na temat okienka Dodawanie uprawnień, zobacz [Krok 4: Uprawnienia](#step-4-permissions). |
| Wyświetlanie uprawnień w docs | Dostępne uprawnienia można wyświetlić w [operacjach dostawcy zasobów usługi Azure Resource Manager](resource-provider-operations.md). |

## <a name="step-2-choose-how-to-start"></a>Krok 2: Wybierz sposób uruchamiania

Istnieją trzy sposoby, które można rozpocząć tworzenie roli niestandardowej. Można sklonować istniejącą rolę, rozpocząć od zera lub rozpocząć od pliku JSON. Najprostszym sposobem jest znalezienie istniejącej roli, która ma większość uprawnień, których potrzebujesz, a następnie sklonować i zmodyfikować ją dla twojego scenariusza. 

### <a name="clone-a-role"></a>Klonowanie roli

Jeśli istniejąca rola nie ma uprawnień, których potrzebujesz, można ją sklonować, a następnie zmodyfikować uprawnienia. Wykonaj następujące kroki, aby rozpocząć klonowanie roli.

1. W witrynie Azure portal otwórz subskrypcję lub grupę zasobów, w której chcesz, aby rola niestandardowa była przypisywana, a następnie otwórz **formę kontroli dostępu (IAM)**.

    Poniższy zrzut ekranu przedstawia stronę kontroli dostępu (IAM) otwartą dla subskrypcji.

    ![Strona kontroli dostępu (IAM) dla subskrypcji](./media/custom-roles-portal/access-control-subscription.png)

1. Kliknij kartę **Role,** aby wyświetlić listę wszystkich ról wbudowanych i niestandardowych.

1. Wyszukaj rolę, którą chcesz sklonować, taką jak rola Czytnik rozliczeń.

1. Na końcu wiersza kliknij wielokropek (**...**), a następnie kliknij przycisk **Klonuj**.

    ![Menu kontekstowe Klonuj](./media/custom-roles-portal/clone-menu.png)

    Spowoduje to otwarcie edytora ról niestandardowych z wybraną opcją **Klonuj rolę.**

1. Przejdź do [kroku 3: Podstawy](#step-3-basics).

### <a name="start-from-scratch"></a>Rozpoczynanie od zera

Jeśli wolisz, możesz wykonać następujące kroki, aby rozpocząć rolę niestandardową od podstaw.

1. W witrynie Azure portal otwórz subskrypcję lub grupę zasobów, w której chcesz, aby rola niestandardowa była przypisywana, a następnie otwórz **formę kontroli dostępu (IAM)**.

1. Kliknij **pozycję Dodaj,** a następnie kliknij pozycję **Dodaj rolę niestandardową (wersja zapoznawcza)**.

    ![Dodawanie niestandardowego menu roli](./media/custom-roles-portal/add-custom-role-menu.png)

    Spowoduje to otwarcie edytora ról niestandardowych z wybraną opcją **Start od zera.**

1. Przejdź do [kroku 3: Podstawy](#step-3-basics).

### <a name="start-from-json"></a>Zacznij od JSON

Jeśli wolisz, możesz określić większość wartości roli niestandardowej w pliku JSON. Można otworzyć plik w edytorze ról niestandardowych, wprowadzić dodatkowe zmiany, a następnie utworzyć rolę niestandardową. Wykonaj następujące kroki, aby rozpocząć od pliku JSON.

1. Utwórz plik JSON o następującym formacie:

    ```json
    {
        "properties": {
            "roleName": "",
            "description": "",
            "assignableScopes": [],
            "permissions": [
                {
                    "actions": [],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```

1. W pliku JSON określ wartości dla różnych właściwości. Oto przykład z niektórych wartości dodanych. Aby uzyskać informacje o różnych właściwościach, zobacz [Rozumienie definicji ról](role-definitions.md).

    ```json
    {
        "properties": {
            "roleName": "Billing Reader Plus",
            "description": "Read billing data and download invoices",
            "assignableScopes": [
                "/subscriptions/11111111-1111-1111-1111-111111111111"
            ],
            "permissions": [
                {
                    "actions": [
                        "Microsoft.Authorization/*/read",
                        "Microsoft.Billing/*/read",
                        "Microsoft.Commerce/*/read",
                        "Microsoft.Consumption/*/read",
                        "Microsoft.Management/managementGroups/read",
                        "Microsoft.CostManagement/*/read",
                        "Microsoft.Support/*"
                    ],
                    "notActions": [],
                    "dataActions": [],
                    "notDataActions": []
                }
            ]
        }
    }
    ```
    
1. W witrynie Azure portal otwórz stronę **kontroli dostępu (IAM).**

1. Kliknij **pozycję Dodaj,** a następnie kliknij pozycję **Dodaj rolę niestandardową (wersja zapoznawcza)**.

    ![Dodawanie niestandardowego menu roli](./media/custom-roles-portal/add-custom-role-menu.png)

    Spowoduje to otwarcie edytora ról niestandardowych.

1. Na karcie Podstawy w obszarze **Uprawnienia Według planu bazowego**wybierz pozycję **Start z JSON**.

1. Obok pola Wybierz plik kliknij przycisk folderu, aby otworzyć okno dialogowe Otwieranie.

1. Wybierz plik JSON, a następnie kliknij przycisk **Otwórz**.

1. Przejdź do [kroku 3: Podstawy](#step-3-basics).

## <a name="step-3-basics"></a>Krok 3: Podstawy

Na karcie **Podstawy** można określić nazwę, opis i uprawnienia linii bazowej dla roli niestandardowej.

1. W polu **Nazwa roli niestandardowej** określ nazwę roli niestandardowej. Nazwa musi być unikatowa dla katalogu usługi Azure AD. Nazwa może zawierać litery, cyfry, spacje i znaki specjalne.

1. W polu **Opis** określ opcjonalny opis roli niestandardowej. Stanie się to etykietką narzędzia dla roli niestandardowej.

    Opcja **Uprawnienia według planu bazowego** powinna być już ustawiona na podstawie poprzedniego kroku, ale można ją zmienić.

    ![Karta Podstawowe z określonymi wartościami](./media/custom-roles-portal/basics-values.png)

## <a name="step-4-permissions"></a>Krok 4: Uprawnienia

Na karcie **Uprawnienia** można określić uprawnienia dla roli niestandardowej. W zależności od tego, czy sklonowano rolę, czy też rozpoczęto korzystanie z usługi JSON, karta Uprawnienia może już wyświetlić listę niektórych uprawnień.

![Karta Uprawnienia tworzenia roli niestandardowej](./media/custom-roles-portal/permissions.png)

### <a name="add-or-remove-permissions"></a>Dodawanie lub usuwanie uprawnień

Wykonaj następujące kroki, aby dodać lub usunąć uprawnienia do roli niestandardowej.

1. Aby dodać uprawnienia, kliknij pozycję **Dodaj uprawnienia,** aby otworzyć okienko Dodaj uprawnienia.

    To okienko zawiera listę wszystkich dostępnych uprawnień pogrupowanych w różne kategorie w formacie karty. Każda kategoria reprezentuje *dostawcę zasobów*, który jest usługą, która dostarcza zasoby platformy Azure.

1. W polu **Wyszukaj uprawnienia** wpisz ciąg, aby wyszukać uprawnienia. Na przykład wyszukaj *fakturę,* aby znaleźć uprawnienia związane z fakturą.

    Lista kart dostawcy zasobów będzie wyświetlana na podstawie ciągu wyszukiwania. Aby uzyskać listę sposobu mapowania dostawców zasobów do usług platformy Azure, zobacz [Dostawcy zasobów dla usług platformy Azure.](../azure-resource-manager/management/azure-services-resource-providers.md)

    ![Okienko Dodawanie uprawnień z dostawcą zasobów](./media/custom-roles-portal/add-permissions-provider.png)

1. Kliknij kartę dostawcy zasobów, która może mieć uprawnienia, które chcesz dodać do roli niestandardowej, na przykład **Microsoft Billing**.

    Lista uprawnień do zarządzania dla tego dostawcy zasobów jest wyświetlana na podstawie ciągu wyszukiwania.

    ![Dodaj listę uprawnień](./media/custom-roles-portal/add-permissions-list.png)

1. Jeśli szukasz uprawnień, które mają zastosowanie do płaszczyzny danych, kliknij pozycję **Akcje danych**. W przeciwnym razie pozostaw akcje przełącze się ustawione na **Akcje,** aby wyświetlić listę uprawnień, które mają zastosowanie do płaszczyzny zarządzania. Aby uzyskać więcej informacji na temat różnic między płaszczyzną zarządzania a płaszczyzną danych, zobacz [Zarządzanie i operacje danych](role-definitions.md#management-and-data-operations).

1. W razie potrzeby zaktualizuj ciąg wyszukiwania, aby jeszcze bardziej zawęzić wyszukiwanie.

1. Po znalezieniu jednego lub więcej uprawnień, które chcesz dodać do roli niestandardowej, dodaj znacznik wyboru obok uprawnień. Na przykład dodaj znacznik wyboru obok **pozycji Inne : Pobierz fakturę,** aby dodać uprawnienie do pobierania faktur.

1. Kliknij **przycisk Dodaj,** aby dodać uprawnienia do listy uprawnień.

    Uprawnienie zostanie dodane `Actions` jako `DataActions`lub .

    ![Dodano uprawnienie](./media/custom-roles-portal/permissions-list-add.png)

1. Aby usunąć uprawnienia, kliknij ikonę usuwania na końcu wiersza. W tym przykładzie, ponieważ użytkownik nie będzie potrzebował `Microsoft.Support/*` możliwości tworzenia biletów pomocy technicznej, uprawnienie można usunąć.

### <a name="add-wildcard-permissions"></a>Dodawanie uprawnień symboli wieloznacznych

W zależności od sposobu rozpoczęcia może znajdować się uprawnienia\*z symbolami wieloznaczymi ( ) na liście uprawnień. Symbol wieloznaczny (\*) rozszerza uprawnienie na wszystko, co pasuje do ciągu, który podajesz. Załóżmy na przykład, że chcesz dodać wszystkie uprawnienia związane z usługą Azure Cost Management i eksportuje. Można dodać wszystkie te uprawnienia:

```
Microsoft.CostManagement/exports/action
Microsoft.CostManagement/exports/read
Microsoft.CostManagement/exports/write
Microsoft.CostManagement/exports/delete
Microsoft.CostManagement/exports/run/action
```

Zamiast dodawać wszystkie te uprawnienia, można po prostu dodać uprawnienia symboli wieloznacznych. Na przykład następujące uprawnienie symboli wieloznacznych jest równoważne z poprzednimi pięcioma uprawnieniami. Obejmowałoby to również wszelkie przyszłe uprawnienia eksportu, które mogą zostać dodane.

```
Microsoft.CostManagement/exports/*
```

Jeśli chcesz dodać nowe uprawnienie symboli wieloznacznych, nie możesz go dodać za pomocą okienka **Dodaj uprawnienia.** Aby dodać uprawnienie symboli wieloznacznych, należy dodać je ręcznie za pomocą karty **JSON.** Aby uzyskać więcej informacji, zobacz [krok 6: JSON](#step-6-json).

### <a name="exclude-permissions"></a>Wykluczanie uprawnień

Jeśli twoja rola ma\*uprawnienie symboli wieloznacznych ( ) i chcesz wykluczyć lub odjąć określone uprawnienia z tego uprawnienia symboli wieloznacznych, można je wykluczyć. Załóżmy na przykład, że masz następujące uprawnienia symboli wieloznacznych:

```
Microsoft.CostManagement/exports/*
```

Jeśli nie chcesz zezwalać na usunięcie eksportu, możesz wykluczyć następujące uprawnienie do usuwania:

```
Microsoft.CostManagement/exports/delete
```

Po wykluczeniu uprawnienia jest ono `NotActions` `NotDataActions`dodawane jako lub . Efektywne uprawnienia do zarządzania są `Actions` obliczane przez dodanie wszystkich, a następnie odjęcie `NotActions`wszystkich . Uprawnienia do danych efektywnych są obliczane przez dodanie `DataActions` wszystkich, `NotDataActions`a następnie odjęcie wszystkich .

> [!NOTE]
> Wykluczanie uprawnienia nie jest tym samym, co odmowa. Wykluczanie uprawnień jest po prostu wygodnym sposobem odejmowania uprawnień od uprawnień symboli wieloznacznych.

1. Aby wykluczyć lub odjąć uprawnienie od dozwolonego uprawnienia symboli wieloznacznych, kliknij pozycję **Wyklucz uprawnienia,** aby otworzyć okienko Wyklucz uprawnienia.

    W tym okienku należy określić uprawnienia do zarządzania lub danych, które są wykluczone lub odjęte.

1. Po znalezieniu jednego lub kilku uprawnień, które chcesz wykluczyć, dodaj znacznik wyboru obok uprawnień, a następnie kliknij przycisk **Dodaj.**

    ![Okienko Wyklucz uprawnienia - zaznaczone uprawnienie](./media/custom-roles-portal/exclude-permissions-select.png)

    Uprawnienie zostanie dodane `NotActions` `NotDataActions`jako lub .

    ![Wyłączone uprawnienia](./media/custom-roles-portal/exclude-permissions-list-add.png)

## <a name="step-5-assignable-scopes"></a>Krok 5: Zakresy podlegające możliwości przypisania

Na karcie **Zakresy podlegające możliwości przypisania** można określić, gdzie rola niestandardowa jest dostępna dla przypisania, na przykład subskrypcji lub grupy zasobów. W zależności od sposobu rozpoczęcia tej karty może być wyświetlona lista zakresu, w którym otwarto stronę kontroli dostępu (IAM). Ustawienie zakresu przypisywanego do zakresu głównego ("/") nie jest obsługiwane. W tej wersji zapoznawczej nie można dodać grupy zarządzania jako zakresu podlegania cesji.

1. Kliknij **pozycję Dodaj zakresy podlegające możliwości przypisania,** aby otworzyć okienko Dodaj zakresy, które można przypisać.

    ![Karta Zakresy podlegające możliwości przypisania](./media/custom-roles-portal/assignable-scopes.png)

1. Kliknij co najmniej jeden zakres, którego chcesz użyć, zazwyczaj subskrypcję.

    ![Dodawanie zakresów podlegania cesji](./media/custom-roles-portal/add-assignable-scopes.png)

1. Kliknij przycisk **Dodaj,** aby dodać zakres, który można przypisać.

## <a name="step-6-json"></a>Krok 6: JSON

Na karcie **JSON** zobaczysz rolę niestandardową sformatowaną w u. JSON. Jeśli chcesz, możesz bezpośrednio edytować JSON. Aby dodać symbol wieloznaczny\*( ) , należy użyć tej karty.

1. Aby edytować JSON, kliknij przycisk **Edytuj**.

    ![Karta JSON z rolą niestandardową](./media/custom-roles-portal/json.png)

1. Wprowadzanie zmian w JSON.

    Jeśli JSON nie jest poprawnie sformatowany, na pionowej rynnie pojawi się czerwona postrzępiona linia i wskaźnik.

1. Po zakończeniu edycji kliknij przycisk **Zapisz**.

## <a name="step-7-review--create"></a>Krok 7: Recenzja + tworzenie

Na karcie **Recenzja + tworzenie** możesz przejrzeć ustawienia ról niestandardowych.

1. Przejrzyj niestandardowe ustawienia ról.

    ![Karta Recenzja + tworzenie](./media/custom-roles-portal/review-create.png)

1. Kliknij **przycisk Utwórz,** aby utworzyć rolę niestandardową.

    Po kilku chwilach pojawi się okno komunikatu informujące o pomyślnym utworzeniu roli niestandardowej.

    ![Tworzenie niestandardowego komunikatu roli](./media/custom-roles-portal/custom-role-success.png)

    Jeśli zostaną wykryte błędy, zostanie wyświetlony komunikat.

    ![Recenzja + błąd tworzenia](./media/custom-roles-portal/review-create-error.png)

1. Wyświetl nową rolę niestandardową na liście **Role.** Jeśli nie widzisz roli niestandardowej, kliknij przycisk **Odśwież**.

     Może upłynąć kilka minut, aby twoja rola niestandardowa pojawiła się wszędzie.

## <a name="list-custom-roles"></a>Wyświetlanie ról niestandardowych

Wykonaj następujące kroki, aby wyświetlić role niestandardowe.

1. Otwórz subskrypcję lub grupę zasobów, a następnie otwórz **formę kontroli dostępu (IAM).**

1. Kliknij kartę **Role,** aby wyświetlić listę wszystkich ról wbudowanych i niestandardowych.

1. Na liście **Typ** wybierz **pozycję Niestandardowerole,** aby wyświetlić swoje role niestandardowe.

    Jeśli właśnie utworzono rolę niestandardową i nie widzisz jej na liście, kliknij przycisk **Odśwież**.

    ![Niestandardowa lista ról](./media/custom-roles-portal/custom-role-list.png)

## <a name="update-a-custom-role"></a>Aktualizacja roli niestandardowej

1. Jak opisano wcześniej w tym artykule, otwórz listę ról niestandardowych.

1. Kliknij wielokropek (**...**) dla roli niestandardowej, którą chcesz zaktualizować, a następnie kliknij przycisk **Edytuj**. Należy pamiętać, że nie można zaktualizować wbudowanych ról.

    Rola niestandardowa jest otwierana w edytorze.

    ![Menu roli niestandardowej](./media/custom-roles-portal/edit-menu.png)

1. Użyj różnych kart, aby zaktualizować rolę niestandardową.

1. Po zakończeniu zmian kliknij kartę **Przejrzyj + utwórz,** aby przejrzeć zmiany.

1. Kliknij przycisk **Aktualizuj,** aby zaktualizować rolę niestandardową.

## <a name="delete-a-custom-role"></a>Usuwanie roli niestandardowej

1. Jak opisano wcześniej w tym artykule, otwórz listę ról niestandardowych.

1. Usuń wszystkie przypisania ról przy użyciu roli niestandardowej.

1. Kliknij wielokropek (**...**) dla roli niestandardowej, którą chcesz usunąć, a następnie kliknij przycisk **Usuń**.

    ![Menu roli niestandardowej](./media/custom-roles-portal/delete-menu.png)

    Całkowite usunięcie roli niestandardowej może potrwać kilka minut.

## <a name="next-steps"></a>Następne kroki

- [Samouczek: tworzenie roli niestandardowej przy użyciu programu Azure PowerShell](tutorial-custom-role-powershell.md)
- [Role niestandardowe na platformie Azure](custom-roles.md)
- [Operacje dostawcy zasobów usługi Azure Resource Manager](resource-provider-operations.md)
