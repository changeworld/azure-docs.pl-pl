---
title: Rozpoczynanie pracy z szablonami prywatnymi | Microsoft Docs
description: Dodawanie i udostępnianie szablonów prywatnych oraz zarządzanie nimi przy użyciu portalu Azure, interfejsu wiersza polecenia platformy Azure lub programu PowerShell.
services: marketplace-customer
documentationcenter: ''
author: msmbaldwin
manager: asimm
editor: ''
tags: marketplace, azure-resource-manager
keywords: ''
ms.assetid: 6ec20778-b578-4885-acb5-104b0e51ea1a
ms.service: marketplace
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: mbaldwin
ms.openlocfilehash: c716f54a1361d41dbad00e2e45562d5fbf8fd6ca
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="get-started-with-private-templates-on-the-azure-portal"></a>Rozpoczynanie pracy z szablonami prywatnymi w witrynie Azure Portal
Szablon usługi [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) to deklaracyjny szablon używany do definiowania wdrożenia. Można zdefiniować zasoby w celu wdrożenia rozwiązania oraz określić parametry i zmienne, które umożliwią wprowadzanie wartości dla różnych środowisk. Szablon składa się z kodu JSON i wyrażeń, których można używać do tworzenia wartości na potrzeby wdrożenia.

Aby umożliwić użytkownikom tworzenie i wdrażanie szablonów prywatnych oraz zarządzanie nimi z poziomu biblioteki osobistej, można użyć nowej funkcji **Szablony** w witrynie [Azure Portal](https://portal.azure.com) razem z dostawcą zasobów **Microsoft.Gallery** jako rozszerzenia portalu [Azure Marketplace](https://azure.microsoft.com/marketplace/).

> [!NOTE]
> Firma Microsoft zaleca, aby zamiast korzystać z szablonów prywatnych w portalu, utworzyć aplikację katalogu usług za pośrednictwem usługi [Managed Applications](../managed-applications/overview.md). Aplikację katalogu usług można udostępnić użytkownikom w organizacji.

Ten dokument przeprowadzi Cię przez proces dodawania i udostępniania **szablonu** prywatnego oraz zarządzania nim za pośrednictwem witryny Azure Portal.

## <a name="guidance"></a>Wskazówki
Poniższe sugestie pomogą Ci w pełni wykorzystać możliwości **szablonów** w pracy z rozwiązaniami:

* **Szablon** to hermetyzowany zasób, który zawiera szablon usługi Resource Manager i dodatkowe metadane. Jego zachowanie jest podobne do zachowania elementu w witrynie Marketplace. Najważniejsza różnica polega na tym, że jest to element prywatny, a witryna Marketplace zawiera elementy publiczne.
* Biblioteka **Szablony** jest dobrym rozwiązaniem, jeśli użytkownicy muszą dostosować swoje wdrożenia.
* **Szablony** są dobrym rozwiązaniem dla użytkowników, którzy potrzebują prostego repozytorium na platformie Azure.
* Rozpocznij pracę z istniejącym szablonem usługi Resource Manager. Wyszukaj szablony w witrynie [github](https://github.com/Azure/azure-quickstart-templates) lub [wyeksportuj szablon](../azure-resource-manager/resource-manager-export-template.md) z istniejącej grupy zasobów.
* **Szablony** są powiązane z użytkownikiem, który je opublikował. Nazwa wydawcy jest widoczna dla każdej osoby, która ma uprawnienia do odczytu szablonu.
* **Szablony** to zasoby usługi Resource Manager. Po opublikowaniu nie można zmienić ich nazwy.

## <a name="add-a-template-resource"></a>Dodawanie zasobu Szablon
Istnieją dwa sposoby tworzenia zasobu **Szablon** w witrynie Azure Portal.

### <a name="method-1-create-a-new-template-resource-from-a-running-resource-group"></a>Metoda 1. Tworzenie nowego zasobu Szablon z poziomu uruchomionej grupy zasobów
1. Przejdź do istniejącej grupy zasobów w witrynie Azure Portal. Wybierz pozycję **Eksportuj szablon** w obszarze **Ustawienia**.
2. Po wyeksportowaniu szablonu usługi Resource Manager użyj przycisku **Zapisz szablon** w celu zapisania go w repozytorium **Szablony**. Wszystkie szczegóły eksportowania szablonu możesz znaleźć [tutaj](../azure-resource-manager/resource-manager-export-template.md).
   <br /><br />
   ![Eksportowanie grupy zasobów](media/rg-export-portal1.PNG)
3. Wybierz przycisk polecenia **Zapisz szablon**.
   <br /><br />
4. Wprowadź następujące informacje:
   
   * Nazwa — nazwa obiektu szablonu (UWAGA: w tym polu znajduje się nazwa oparta na usłudze Azure Resource Manager. Obowiązują wszystkie ograniczenia związane z nazewnictwem, a utworzonej nazwy nie można zmienić).
   * Opis — krótkie podsumowanie dotyczące szablonu.
     
     ![Zapisywanie szablonu](media/save-template-portal1.PNG)
5. Kliknij pozycję **Zapisz**.
   
   > [!NOTE]
   > Jeśli wyeksportowany szablon usługi Resource Manager zawiera błędy, w portalu zostaną wyświetlone powiadomienia, ale nadal będzie można zapisać szablon w bibliotece szablonów. Przed ponownym wdrożeniem wyeksportowanego szablonu upewnij się, że wszystkie problemy związane z szablonami usługi Resource Manager zostały rozwiązane.
   > 
   > 

### <a name="method-2-add-a-new-template-resource-from-browse"></a>Metoda 2. Dodawanie nowego szablonu przy użyciu funkcji przeglądania
Możesz także dodać nowy **szablon** od początku, korzystając z przycisku polecenia +Dodaj, dostępnego po wybraniu pozycji **Przeglądaj > Szablony**. Podaj nazwę, opis i kod JSON szablonu usługi Resource Manager.

![Dodawanie szablonu](media/add-template-portal1.PNG)

> [!NOTE]
> Microsoft.Gallery to oparty na dzierżawie dostawca zasobów platformy Azure. Zasób szablonu jest powiązany z użytkownikiem, który go utworzył. Nie jest on związany z żadną subskrypcją. Podczas wdrażania szablonu należy wybrać subskrypcję.
> 
> 

## <a name="view-template-resources"></a>Wyświetlanie zasobów Szablon
Wszystkie dostępne **szablony** można wyświetlić, wybierając pozycje **Przeglądaj > Szablony**. Dostępne szablony to te utworzone przez Ciebie, a także te, które zostały Ci udostępnione z różnymi poziomami uprawnień. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu](#access-control-for-a-tenant-resource-provider).

![Wyświetlanie szablonu](media/view-template-portal1.PNG)

Aby wyświetlić szczegółowe informacje o **szablonie**, kliknij element listy.

![Wyświetlanie szablonu](media/view-template-portal2c.png)

## <a name="edit-a-template-resource"></a>Edytowanie zasobu Szablon
Możesz zainicjować przepływ edycji **szablonu**, klikając prawym przyciskiem myszy element na liście przeglądania lub wybierając przycisk polecenia edycji.

![Edytowanie szablonu](media/edit-template-portal1a.PNG)

Możesz edytować opis lub tekst szablonu usługi Resource Manager. Nie można edytować nazwy, ponieważ jest to nazwa zasobu usługi Resource Manager. Podczas edytowania danych JSON szablonu usługi Resource Manager sprawdzamy, czy dane JSON są prawidłowe. Wybierz kolejno przyciski **OK** i **Zapisz**, aby zapisać zaktualizowany szablon.

![Edytowanie szablonu](media/edit-template-portal2a.PNG)

Po zapisaniu szablonu jest wyświetlane powiadomienie z potwierdzeniem.

![Edytowanie szablonu](media/edit-template-portal3b.png)

## <a name="deploy-a-template-resource"></a>Wdrażanie zasobu Szablon
Możesz wdrożyć dowolny **szablon**, do którego masz uprawnienia do **odczytu**. Wprowadź wartości parametrów szablonu usługi Resource Manager, aby kontynuować wdrażanie.

![Wdrażanie szablonu](media/deploy-template-portal1b.png)

## <a name="share-a-template-resource"></a>Udostępnianie zasobu Szablon
Zasób **Szablon** można udostępniać innym użytkownikom. Udostępnianie działa w sposób podobny do [przypisywania roli dla dowolnego zasobu na platformie Azure](../role-based-access-control/role-assignments-portal.md). Właściciel **szablonu** zapewnia uprawnienia innym użytkownikom, którzy mogą współdziałać z zasobem Szablon. Osoba lub grupa osób, której udostępniono **szablon**, może zobaczyć właściwości szablonu usługi Resource Manager i galerii.

### <a name="access-control-for-the-microsoftgallery-resources"></a>Kontrola dostępu do zasobów Microsoft.Gallery
| Rola | Uprawnienia |
| --- | --- |
| Właściciel |Umożliwia pełną kontrolę zasobu Szablon, w tym udostępnianie. |
| Czytelnik |Umożliwia odczytywanie i wykonywanie (wdrażanie) zasobu Szablon. |
| Współautor |Umożliwia edytowanie i usuwanie zasobu Szablon. Użytkownik nie może udostępniać szablonu innym. |

Wybierz pozycję **Udostępnij** w przeglądanym elemencie, klikając go prawym przyciskiem myszy, lub podczas przeglądania określonego elementu.

![Udostępnianie szablonu](media/share-template-portal1a.png)

 Teraz możesz wybrać rolę i użytkownika lub grupę, aby udzielić im praw dostępu do określonego **szablonu**. Dostępne role to Właściciel, Czytelnik i Współautor.

![Udostępnianie szablonu](media/share-template-portal2b.png)

![Udostępnianie szablonu](media/share-template-portal3b.png)

Kliknij pozycję **Wybierz**, a następnie przycisk **OK**. Możesz teraz przeglądać użytkowników i grupy dodane do zasobu.

![Udostępnianie szablonu](media/share-template-portal4b.png)

> [!NOTE]
> Szablon można udostępniać tylko użytkownikom i grupom w tej samej dzierżawie usługi Azure Active Directory. Użytkownik z adresem e-mail znajdującym się poza tą dzierżawą, któremu udostępniono szablon, otrzymuje zaproszenie do dołączenia do dzierżawy jako gość.
> 
> 

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać informacje na temat tworzenia szablonów usługi Resource Manager, zobacz artykuł [Authoring templates](../azure-resource-manager/resource-group-authoring-templates.md) (Tworzenie szablonów).
* Aby poznać funkcje, których można użyć w szablonie usługi Resource Manager, zobacz [Template functions](../azure-resource-manager/resource-group-template-functions.md) (Funkcje szablonu).

