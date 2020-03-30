---
title: Jak zabezpieczyć dostęp do wykazu danych platformy Azure
description: W tym artykule wyjaśniono, jak zabezpieczyć wykaz danych i jego zasoby danych w usłudze Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: b0972be2b8a6e05d3d90cde7354b4890ea95cbe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68976758"
---
# <a name="how-to-secure-access-to-data-catalog-and-data-assets"></a>Jak zabezpieczyć dostęp do katalogu danych i zasobów danych

> [!IMPORTANT]
> Ta funkcja jest dostępna tylko w standardowej wersji usługi Azure Data Catalog.

Usługa Azure Data Catalog umożliwia określenie, kto może uzyskać dostęp do katalogu danych i jakie operacje (rejestrowanie, dodawanie adnotacji, przejęcie na własność) mogą wykonywać na metadanych w katalogu. 

## <a name="catalog-users-and-permissions"></a>Użytkownicy katalogu i uprawnienia

Aby dać użytkownikowi lub grupie dostęp do wykazu danych i ustawić uprawnienia:

1. Na [stronie głównej katalogu danych](https://www.azuredatacatalog.com)kliknij pozycję **Ustawienia** na pasku narzędzi.

   ![Przycisk Ustawienia strony głównej usługi Azure Data Catalog](media/data-catalog-how-to-secure-catalog/data-catalog-settings.png)

2. Na stronie ustawień rozwiń sekcję **Użytkownicy katalogu.**

   ![Przycisk Dodaj użytkowników usługi Azure Data Catalog](media/data-catalog-how-to-secure-catalog/data-catalog-add-button.png)

3. Kliknij przycisk **Dodaj**.

4. Wprowadź w pełni kwalifikowaną **nazwę użytkownika** lub nazwę **grupy zabezpieczeń** w usłudze Azure Active Directory (AAD) skojarzonej z katalogiem. Użyj przecinka (',') jako separatora, jeśli dodajesz więcej niż jednego użytkownika lub grupę.

   ![Użytkownicy usługi Azure Data Catalog — użytkownicy lub grupy](media/data-catalog-how-to-secure-catalog/data-catalog-users-groups.png)

5. Naciśnij **klawisz ENTER** lub **TAB** po wyjęciu z pola tekstowego. 

6. Upewnij się, że wszystkie uprawnienia **(Adnotacje,** **Zarejestruj się**i **Przejmij na własność)** są domyślnie przypisane do tych użytkowników lub grup. Oznacza to, że użytkownik lub grupa może [rejestrować zasoby danych,]( data-catalog-how-to-register.md) [dodawać adnotacje do zasobów danych]( data-catalog-how-to-annotate.md)i [przejmować na własność zasoby danych.]( data-catalog-how-to-manage.md) 

   ![Użytkownicy usługi Azure Data Catalog — uprawnienia domyślne](media/data-catalog-how-to-secure-catalog/data-catalog-default-permissions.png)

7. Aby nadać użytkownikowi lub grupie tylko dostęp do odczytu do katalogu, wyczyść opcję **adnotacji** dla tego użytkownika lub grupy. Po wykonaniu tej tej tezy użytkownik lub grupa nie może dodawać adnotacji do zasobów danych w katalogu, ale mogą je wyświetlać. 

8. Aby uniemożliwić użytkownikowi lub grupie rejestrowanie zasobów danych, wyczyść opcję **rejestru** dla tego użytkownika lub grupy.

9. Aby uniemożliwić użytkownikowi przejęcie na własność zasobu danych, wyczyść opcję **przejęcia własności** dla tego użytkownika lub grupy. 

10. Aby usunąć użytkownika/grupę z użytkowników katalogu, kliknij **x** dla użytkownika/grupy u dołu listy. 

   ![Użytkownicy wykazu usługi Azure Data Catalog — usuń ikonę użytkownika X](media/data-catalog-how-to-secure-catalog/data-catalog-delete-user.png)

   > [!IMPORTANT]
   > Zaleca się dodawanie grup zabezpieczeń do katalogowania użytkowników, a nie dodawanie użytkowników bezpośrednio i przypisywanie uprawnień. Następnie dodaj użytkowników do grup zabezpieczeń, które pasują do ich ról i ich wymaganego dostępu do katalogu.

## <a name="special-considerations"></a>Uwagi szczególne

- Uprawnienia przypisane do grup zabezpieczeń są addytywne. Załóżmy, że użytkownik jest w dwóch grupach. Jedna grupa ma uprawnienia adnotacji, a inna grupa nie ma uprawnień do adnotacji. Następnie użytkownik ma uprawnienia do dotów. 
- Uprawnienia przypisane jawnie do użytkownika zastępują uprawnienia przypisane do grup, do których należy użytkownik. W poprzednim przykładzie, powiedzmy, jawnie dodano użytkownika do użytkowników katalogu i nie przypisują adnotacji uprawnień. Użytkownik nie może dodawać adnotacji do zasobów danych, nawet jeśli użytkownik jest członkiem grupy, która ma uprawnienia do dotów.

## <a name="next-steps"></a>Następne kroki

- [Rozpoczynanie pracy z usługą Azure Data Catalog](data-catalog-get-started.md)
