---
title: Jak zabezpieczyć dostęp do usługi Azure Data Catalog
description: W tym artykule wyjaśniono, jak zabezpieczyć usługa data catalog i jej zasobów danych.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 6c09b509399647f4cacbc96427200da5a1b00ac9
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60010756"
---
# <a name="how-to-secure-access-to-data-catalog-and-data-assets"></a>Jak zabezpieczyć dostęp do wykazu danych i zasobów danych
> [!IMPORTANT]
> Ta funkcja jest dostępna tylko w wersji standard edition usługi Azure Data Catalog.

Usługa Azure Data Catalog pozwala określić, kto ma dostęp do wykazu danych i jakie operacje (rejestrowanie, dodawanie adnotacji, przejęcie na własność) mogli wykonywać na metadanych w katalogu. 

## <a name="catalog-users-and-permissions"></a>Użytkownicy wykazu i uprawnienia
Aby nadać użytkownik lub Grupa dostępu do wykazu danych i ustawić uprawnienia:

1. Na [strony głównej usługi data Catalog](https://www.azuredatacatalog.com), kliknij przycisk **ustawienia** na pasku narzędzi.

    ![wykaz danych — ustawienia](media/data-catalog-how-to-secure-catalog/data-catalog-settings.png)
2. Na stronie ustawień, rozwiń węzeł **użytkowników wykazu** sekcji.
    ![Katalog użytkowników — dodawanie](media/data-catalog-how-to-secure-catalog/data-catalog-add-button.png)
3. Kliknij pozycję **Add** (Dodaj).
4. Wprowadź w pełni kwalifikowaną **nazwa_użytkownika** lub nazwa **grupy zabezpieczeń** w usłudze Azure Active Directory (AAD) skojarzony z katalogiem. Użyj przecinka (",") jako separatora, dodając więcej niż jednego użytkownika lub grupę.
    ![Użytkownicy wykazu — użytkownicy lub grupy](media/data-catalog-how-to-secure-catalog/data-catalog-users-groups.png)
5. Naciśnij klawisz **ENTER** lub **kartę** poza pole tekstowe. 
6.  Upewnij się, że wszystkie uprawnienia (**Adnotuj**, **zarejestrować**, i **Przejmij na własność**) są domyślnie przypisane do tych użytkowników lub grup. Oznacza to, że użytkownik lub grupa może [zarejestrowane zasoby danych]( data-catalog-how-to-register.md), [dodawać adnotacje do zasobów danych]( data-catalog-how-to-annotate.md), i [przejęcie na własność zasoby danych]( data-catalog-how-to-manage.md). 
    ![Użytkownicy wykazu — uprawnienia domyślne](media/data-catalog-how-to-secure-catalog/data-catalog-default-permissions.png)
7.  Aby dać użytkownikowi lub grupie dostęp tylko do odczytu do katalogu, wyczyść **Adnotuj** opcji dla tego użytkownika lub grupy. Jeśli tak zrobisz, użytkownik lub grupa nie może dodawać adnotacje do zasobów danych w katalogu, ale mogą je wyświetlać. 
8.  Aby uniemożliwić użytkownikowi lub grupie rejestrowanie zasobów danych, wyczyść **zarejestrować** opcji dla tego użytkownika lub grupy.
9.  Aby zablokować użytkownika z przejmowania własności zasobów danych, wyczyść **przejęcie na własność** opcji dla tego użytkownika lub grupy. 
10. Aby usunąć użytkownika/grupy użytkowników z katalogu, kliknij przycisk **x** dla użytkownika/grupy w dolnej części listy. 
    ![Użytkownicy w katalogu — Usuń użytkownika](media/data-catalog-how-to-secure-catalog/data-catalog-delete-user.png)

    > [!IMPORTANT]
    > Firma Microsoft zaleca dodanie grupy zabezpieczeń bezpośrednio katalogu użytkowników, a nie do dodawania użytkowników i przypisywania uprawnień. Następnie należy dodać użytkowników do grup zabezpieczeń, które odpowiadają ich ról i ich wymagany dostęp do wykazu.

## <a name="special-considerations"></a>Specjalne uwagi

- Uprawnienia przypisane do grupy zabezpieczeń są dodatku. Załóżmy, że użytkownik znajduje się w dwóch grupach. Jedna grupa ma dodawać adnotacje do uprawnień, a inna grupa nie ma adnotacji uprawnień. Następnie użytkownik ma adnotacje uprawnień. 
- Uprawnienia jawnie przypisane do użytkownika musi zostać zastąpiona uprawnienia przypisane do grup, do których należy użytkownik. W poprzednim przykładzie załóżmy, jawnie dodawania użytkownika do katalogu użytkowników i czy nie Przypisz uprawnienia dodawania do nich adnotacji. Użytkownik nie może dodawać adnotacje do zasobów danych, nawet jeśli użytkownik jest członkiem grupy, który ma uprawnienia dodawania do nich adnotacji.

## <a name="next-steps"></a>Kolejne kroki
- [Rozpoczynanie pracy z usługą Azure Data Catalog](data-catalog-get-started.md)

