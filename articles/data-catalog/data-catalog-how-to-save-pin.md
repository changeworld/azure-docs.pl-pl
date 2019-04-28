---
title: Zapisywanie wyszukiwań i numer pin zasobów danych w usłudze Azure Data Catalog
description: Artykuł porad wyróżniania możliwości usługi Azure Data Catalog zapisywania źródła danych i zasobów danych w celu późniejszego użycia.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 6bd00a81-820d-4b7c-91fa-ab09e575474c
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: df3220ddb80ebc329ed6b0024ca4eefd2bdfb321
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61002066"
---
# <a name="save-searches-and-pin-data-assets-in-azure-data-catalog"></a>Zapisywanie wyszukiwań i numer pin zasobów danych w usłudze Azure Data Catalog
## <a name="introduction"></a>Wprowadzenie
Usługa Azure Data Catalog udostępnia możliwości Odnajdowanie źródeł danych. Możesz szybko wyszukiwanie i filtrowanie katalogu odnajdywania źródeł danych i zrozumienie ich przeznaczenie, co ułatwia znalezienie odpowiednich danych dla zadania pod ręką.

Ale co zrobić, jeśli należy regularnie pracować z tymi samymi danymi? I co zrobić, jeśli użytkownicy będą mieli regularnie swojej wiedzy, aby te same źródła danych w wykazie? W takich sytuacjach wielokrotnego wydania tej samej operacji wyszukiwania może być mało wydajne. Jest to, gdzie może pomóc zapisanego wyszukiwania i zasoby danych przypięte.

## <a name="saved-searches"></a>Zapisane wyszukiwania
Zapisane wyszukiwanie w usłudze Data Catalog jest wielokrotnego użytku, definicja wyszukiwania dla poszczególnych użytkowników. Można zdefiniować wyszukiwania, w tym wyszukiwane terminy, tagi i inne filtry, a następnie zapisz go. Można ponownie uruchomić zapisane wyszukiwanie definicji później, aby zwrócić wszystkie zasoby danych, które pasują do jej kryteriów wyszukiwania.

### <a name="create-a-saved-search"></a>Tworzenie zapisanego wyszukiwania
Aby utworzyć zapisanego kryterium wyszukiwania, wykonaj następujące czynności:
1. W portalu usługi Azure Data Catalog w **bieżące wyszukiwanie** okna, kliknij przycisk **Zapisz**. 

    ![Bieżące połączenie Zapisz ustawienia wyszukiwania](./media/data-catalog-how-to-save-pin/01-save-option.png) 

2. Wprowadź kryteria wyszukiwania, które chcesz ponownie użyć, a następnie kliknij przycisk **Zapisz**.

    ![Bieżące ustawienia wyszukiwania Nazwa zapisanego wyszukiwania](./media/data-catalog-how-to-save-pin/02-name.png)

3. Po wyświetleniu monitu wprowadź nazwę dla zapisanego wyszukiwania. Wybierz nazwę opisową i opisujący zasobów danych, które zostaną zwrócone przez wyszukiwanie.

### <a name="manage-saved-searches"></a>Zarządzanie zapisanych wyszukiwań
Po zapisaniu jednego lub więcej wyszukiwań **zapisane wyszukiwania** opcja jest wyświetlana poniżej **bieżące wyszukiwanie** pole. Po rozwinięciu listy są wyświetlane wszystkie zapisane wyszukiwania.

 ![Lista zapisanych wyszukiwań](./media/data-catalog-how-to-save-pin/03-list.png)

Wykonaj jedną z następujących czynności:

* Aby wykonać wyszukiwanie, wybierz z listy zapisanej operacji wyszukiwania.

* Aby wyświetlić listę opcji zarządzania dla zapisanego kryterium wyszukiwania, kliknij strzałkę w dół obok nazwy wyszukiwania.

    ![Opcje zarządzania zapisanych wyszukiwań](./media/data-catalog-how-to-save-pin/04-managing.png)

* Aby wprowadzić nową nazwę dla zapisanego wyszukiwania, wybierz opcję **Zmień nazwę**. Definicja wyszukiwania nie jest zmieniany.

* Aby usunąć zapisane wyszukiwanie z listy, wybierz **Usuń**, a następnie Potwierdź usunięcie.

* Aby oznaczyć zapisanego wyszukiwania jako domyślny wyszukiwania, wybierz **Zapisz jako domyślne**. Jeśli wykonujesz to "puste" wyszukiwanie ze strony głównej usługi Azure Data Catalog domyślne wyszukiwanie jest wykonywane. Ponadto, wyszukiwania, która jest oznaczona jako domyślne wyszukiwanie jest wyświetlany w górnej części **zapisane wyszukiwania** listy.

### <a name="organizational-saved-searches"></a>Organizacyjne zapisanych wyszukiwań
Wszystkich użytkowników w organizacji można zapisać wyszukiwania na własny użytek. Administratorzy wykazu danych można również zapisać wyszukanie wszystkich użytkowników w organizacji. Gdy administratorzy Zapisz wyszukiwanie, ich wyświetlone **udziału w firmie** opcji. Wybranie tej opcji udziałów zapisanego wyszukiwania dla wszystkich użytkowników w organizacji.

 ![Organizacyjne zapisanych wyszukiwań](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>Zasoby danych przypięte
Za pomocą zapisanego wyszukiwania można zapisać i wielokrotnie używać definicje wyszukiwania. Zasoby danych, które są zwracane przez wyszukiwanie mogą ulec zmianie w czasie, gdy zawartość zmiany katalogu. Po przypięciu zasobów danych, można zidentyfikować jawnie określonych danych zasobów, aby ułatwić im dostępu bez konieczności korzystania z wyszukiwania.

Przypinanie zasobów danych jest bardzo proste. Aby dodać zasobu danych do przypiętych listy, możesz po prostu kliknij **numeru pin** ikony. Ikona jest wyświetlana w rogiem kafelka zasobu w widoku kafelków, a w kolumnie najdalej po lewej stronie w widoku listy, w portalu usługi Azure Data Catalog.

![Ikonę przypinania zasobów danych](./media/data-catalog-how-to-save-pin/05-pinning.png)

Odpięcie zasobu danych jest równie proste. Po prostu kliknij **Odepnij** ikonę, aby przełączyć ustawienie dla wybranego zasobu.

![Zasobu danych Odepnij ikony](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="the-my-assets-section"></a>W sekcji Moje zasoby
Strona główna portalu Data Catalog zawiera **Moje zasoby** sekcja, która zawiera zasoby, interesujące dla bieżącego użytkownika. Ta sekcja zawiera zarówno przypięte zasoby i zapisane wyszukiwania.

![W sekcji Moje zasoby, na stronie głównej](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Podsumowanie
Usługa Azure Data Catalog oferuje możliwości, które ułatwiają odnajdywanie źródeł danych, których potrzebujesz, dzięki czemu użytkownik i inni członkowie organizacji mogą poświęcać więcej czasu na pracę. Zapisane wyszukiwania, a następnie przypiąć dane, które zasoby kompilacji na tych podstawowych możliwości, dzięki czemu użytkownicy mogą łatwo zidentyfikować źródeł danych, które współpracują z wielokrotnie.
