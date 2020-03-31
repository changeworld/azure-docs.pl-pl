---
title: Zapisywanie wyszukiwań i przypinanie zasobów danych w wykazie danych platformy Azure
description: Artykuł inscenajda przedstawiający możliwości w usłudze Azure Data Catalog do zapisywania źródeł danych i zasobów danych do późniejszego użycia.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: a8545939fd027989519e0f7a161f750ec676749a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68976820"
---
# <a name="save-searches-and-pin-data-assets-in-azure-data-catalog"></a>Zapisywanie wyszukiwań i przypinanie zasobów danych w wykazie danych platformy Azure
## <a name="introduction"></a>Wprowadzenie
Usługa Azure Data Catalog udostępnia możliwości odnajdowania źródeł danych. Można szybko wyszukiwać i filtrować katalog, aby zlokalizować źródła danych i zrozumieć ich przeznaczenie, ułatwiając znalezienie odpowiednich danych do używanego zadania.

Ale co, jeśli musisz regularnie pracować z tymi samymi danymi? A co, jeśli ty i inni użytkownicy regularnie wnosicie swoją wiedzę do tych samych źródeł danych w katalogu? W takich sytuacjach konieczności wielokrotnego wydawania tych samych wyszukiwań może być nieefektywne. W tym miejscu mogą pomóc zapisane wyszukiwania i przypięte zasoby danych.

## <a name="saved-searches"></a>Zapisane wyszukiwania
Zapisane wyszukiwanie w wykazie danych to definicja wyszukiwania wielokrotnego pożyciem na użytkownika. Można zdefiniować wyszukiwanie, w tym wyszukiwane terminy, znaczniki i inne filtry, a następnie zapisać je. Możesz później ponownie uruchomić zapisaną definicję wyszukiwania, aby zwrócić wszystkie zasoby danych, które odpowiadają kryteriom wyszukiwania.

### <a name="create-a-saved-search"></a>Tworzenie zapisanego wyszukiwania
Aby utworzyć zapisane wyszukiwanie, wykonaj następujące czynności:
1. W portalu wykazu danych platformy Azure w oknie **Bieżące wyszukiwanie** kliknij pozycję **Zapisz**. 

    ![Bieżące ustawienia wyszukiwania Zapisz link](./media/data-catalog-how-to-save-pin/01-save-option.png) 

2. Wprowadź kryteria wyszukiwania, których chcesz użyć ponownie, a następnie kliknij przycisk **Zapisz**.

    ![Bieżące ustawienia wyszukiwania zapisane nazwy wyszukiwania](./media/data-catalog-how-to-save-pin/02-name.png)

3. Po wyświetleniu monitu wprowadź nazwę zapisanego wyszukiwania. Wybierz nazwę, która ma znaczenie i opisuje zasoby danych, które zostaną zwrócone przez wyszukiwanie.

### <a name="manage-saved-searches"></a>Zarządzanie zapisanymi wyszukiwaniami
Po zapisaniu jednego lub kilku wyszukiwań pod polem **Bieżące wyszukiwanie** zostanie wyświetlona opcja **Zapisane wyszukiwania.** Po rozwinięciu listy wyświetlane są wszystkie zapisane wyszukiwania.

 ![Katalog danych — lista zapisanych wyszukiwań](./media/data-catalog-how-to-save-pin/03-list.png)

Wykonaj dowolną z następujących czynności:

* Aby przeprowadzić wyszukiwanie, wybierz zapisane wyszukiwanie na liście.

* Aby wyświetlić listę opcji zarządzania zapisanym wyszukiwaniem, kliknij strzałkę w dół obok nazwy wyszukiwania.

    ![Opcje zarządzania zapisanymi wyszukiwaniami](./media/data-catalog-how-to-save-pin/04-managing.png)

* Aby wprowadzić nową nazwę zapisanego wyszukiwania, wybierz **pozycję Zmień nazwę**. Definicja wyszukiwania nie ulega zmianie.

* Aby usunąć zapisane wyszukiwanie z listy, wybierz pozycję **Usuń**, a następnie potwierdź usunięcie.

* Aby oznaczyć zapisane wyszukiwanie jako wyszukiwanie domyślne, wybierz pozycję **Zapisz jako domyślne**. Jeśli wykonujesz wyszukiwanie "puste" ze strony głównej usługi Azure Data Catalog, zostanie wykonane wyszukiwanie domyślne. Ponadto wyszukiwanie oznaczone jako wyszukiwanie domyślne jest wyświetlane u góry listy **Zapisane wyszukiwania.**

### <a name="organizational-saved-searches"></a>Zapisane wyszukiwania w organizacji
Wszyscy użytkownicy w organizacji mogą zapisywać wyszukiwania na własny użytek. Administratorzy wykazu danych mogą również zapisywać wyszukiwania dla wszystkich użytkowników w organizacji. Gdy administratorzy zapisują wyszukiwanie, otrzymują opcję **Udostępnij w firmie.** Wybranie tej opcji powoduje udostępnienie zapisanego wyszukiwania wszystkim użytkownikom w organizacji.

 ![Katalog danych — wyszukiwarki zapisane w organizacji](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>Przypięte zasoby danych
Dzięki zapisanym wyszukiwaniom można zapisywać i ponownie używać definicji wyszukiwania. Zasoby danych, które są zwracane przez wyszukiwania mogą ulec zmianie wraz ze zmianą zawartości katalogu. Po przypięciu zasobów danych można jawnie identyfikować określone zasoby danych, aby ułatwić im dostęp bez konieczności korzystania z wyszukiwania.

Przypinanie zasobu danych jest proste. Aby dodać zasób danych do przypiętej listy, wystarczy kliknąć ikonę **pinezki.** Ikona jest wyświetlana w rogu kafelka zasobu w widoku kafelków i w lewej kolumnie w widoku listy w portalu usługi Azure Data Catalog.

![Wykaz danych — ikona pinezki zasobów danych](./media/data-catalog-how-to-save-pin/05-pinning.png)

Odłączanie zasobu danych jest równie proste. Wystarczy kliknąć ikonę **odpinać,** aby przełączyć ustawienie dla wybranego zasobu.

![Wykaz danych — ikona odpinania zasobu danych](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="the-my-assets-section"></a>Sekcja Moje zasoby
Strona główna portalu wykazu danych zawiera sekcję **Moje zasoby,** która wyświetla interesujące go zasoby dla bieżącego użytkownika. Ta sekcja zawiera zarówno przypięte zasoby, jak i zapisane wyszukiwania.

![Sekcja Moje zasoby na stronie głównej](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Podsumowanie
Usługa Azure Data Catalog udostępnia funkcje, które ułatwiają odnajdowanie potrzebnych źródeł danych, dzięki czemu Ty i inni członkowie instytucji możecie spędzać mniej czasu na poszukiwaniu danych i więcej czasu na pracę z nim. Zapisane wyszukiwania i przypięte zasoby danych opierają się na tych podstawowych możliwościach, dzięki czemu użytkownicy mogą łatwo identyfikować źródła danych, z którymi pracują wielokrotnie.
