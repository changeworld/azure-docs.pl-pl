---
title: Rejestrowanie zasobów danych w usłudze Azure Data Catalog
description: Jak rejestrować zasoby danych w usługi Azure Data Catalog
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: tutorial
ms.date: 04/08/2019
ms.openlocfilehash: 91f7967915fd19ae47ca207913f979aa56fcd27f
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60011711"
---
# <a name="tutorial-register-data-assets-in-azure-data-catalog"></a>Samouczek: Rejestrowanie zasobów danych w usłudze Azure Data Catalog

W tym samouczku Użyj narzędzia rejestracji można zarejestrować zasoby danych z przykładowej bazy danych Azure SQL w wykazie. Rejestracja to proces wyodrębniania kluczowych metadanych strukturalnych, takich jak nazwy, typy i lokalizacje, ze źródła danych i zasobów, które się w nim znajdują, oraz kopiowania tych metadanych do wykazu. Źródło danych i zasoby danych pozostają tam, gdzie się znajdowały, ale metadane są używane przez wykaz, aby można było je łatwiej odnaleźć i zrozumieć.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Rejestrowanie zasobów danych 
> * Wyszukiwania zasobów danych
> * Dodawanie adnotacji do zasobów danych
> * Łączenie z zasobami danych
> * Zarządzanie zasobami danych
> * Usunięcia zasobów danych

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, należy wykonać [Szybki Start](register-data-assets-tutorial.md).

* A [Microsoft Azure](https://azure.microsoft.com/) subskrypcji.
* Musisz mieć własne [dzierżawy usługi Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

Aby skonfigurować wykazu danych, musi być właścicielem lub współwłaścicielem subskrypcji platformy Azure.

## <a name="register-data-assets"></a>Rejestrowanie zasobów danych

### <a name="register-a-data-source"></a>Rejestrowanie źródła danych

Należy zarejestrować zasoby danych (tabele) z [przykładowej bazy danych Azure SQL](../sql-database/sql-database-single-database-get-started.md), ale można użyć dowolnego obsługiwanego źródła danych, jeśli wolisz pracować z danymi, które są Ci znane i dotyczą Twojej roli. Aby uzyskać listę obsługiwanych źródeł danych, zobacz [Supported data sources](data-catalog-dsr.md) (Obsługiwane źródła danych).

Nazwa bazy danych Azure SQL, firma Microsoft korzysta z tego samouczka jest *RLSTest*.

Możesz teraz zarejestrować zasoby danych z przykładowej bazy danych Azure SQL przy użyciu usługi Azure Data Catalog.

1. Przejdź do [strony głównej usługi Azure Data Catalog](http://azuredatacatalog.com) i wybierz **Publikuj dane**.

   ![Usługa Azure Data Catalog — przycisk Publikuj dane](media/register-data-assets-tutorial/data-catalog-publish-data.png)

2. Wybierz **Uruchom aplikację** Aby pobrać, zainstalować i uruchomić narzędzie rejestracji na komputerze.

   ![Usługa Azure Data Catalog — przycisk Uruchom](media/register-data-assets-tutorial/data-catalog-launch-application.png)

3. Na **powitalnej** wybierz opcję **Zaloguj** i wprowadź swoje poświadczenia.

    ![Usługa Azure Data Catalog — strona powitalna](media/register-data-assets-tutorial/data-catalog-welcome-dialog.png)

4. Na **Microsoft Azure Data Catalog** wybierz opcję **programu SQL Server** i **dalej**.

    ![Usługa Azure Data Catalog — źródła danych](media/register-data-assets-tutorial/data-catalog-data-sources.png)

5. Wprowadź właściwości połączenia programu SQL Server dla Twojego przykładu bazy danych Azure SQL, a następnie wybierz pozycję **CONNECT**.

   ![Usługa Azure Data Catalog — ustawienia połączenia programu SQL Server](media/register-data-assets-tutorial/data-catalog-sql-server-connection.png)

6. Zarejestruj metadane zasobów danych. W tym przykładzie należy zarejestrować **produktu** obiektów z przestrzeni nazw przykładowej bazy danych Azure SQL:

    1. W **hierarchii serwerów** drzewa rozwiń przykładu bazy danych Azure SQL i wybierz **SalesLT**.

    2. Wybierz **produktu**, **ProductCategory**, **ProductDescription**, i **ProductModel** za pomocą klawiszy Ctrl + wybierz.

    3. Wybierz **Przenieś wybraną strzałkę** (**>**). Spowoduje to przeniesienie wszystkich wybranych obiektów na listę **Obiekty do zarejestrowania**.

          ![Samouczek dotyczący usługi Azure Data Catalog — przeglądanie i wybieranie obiektów](media/register-data-assets-tutorial/data-catalog-server-hierarchy.png)

    4. Wybierz pozycję **Dołącz podgląd**, aby dołączyć podgląd danych w postaci migawki. Migawka zawiera maksymalnie 20 rekordów z każdej tabeli i jest kopiowany do katalogu.

    5. Wybierz pozycję **Dołącz profil danych**, aby dołączyć migawkę statystyk obiektów dla profilu danych (na przykład liczbę wierszy czy minimalną, maksymalną i średnią wartość w kolumnie).

    6. W **Oznakuj** wprowadź **sprzedaż, produkt, usługa azure sql**. Spowoduje to dodanie tagów wyszukiwania dla tych zasobów danych. Tagi to doskonały sposób na ułatwienie użytkownikom znalezienia zarejestrowanego źródła danych.

    7. Podaj imię i nazwisko **eksperta** w zakresie przeznaczenia tych danych (opcjonalnie).

          ![Samouczek dotyczący usługi Azure Data Catalog — obiekty do zarejestrowania](media/register-data-assets-tutorial/data-catalog-objects-register.png)

    8. Wybierz **ZAREJESTROWAĆ**. Wybrane obiekty zostaną zarejestrowane za pomocą usługi Azure Data Catalog. W tym ćwiczeniu są rejestrowane wybrane obiekty z przykładu bazy danych Azure SQL. Za pomocą narzędzia rejestracji metadane są wyodrębniane z zasobów danych i kopiowane do usługi Azure Data Catalog. Dane pozostają gdzie obecnie pozostaje. Dane pozostają w ramach kontroli administratorów i system źródłowy zgodnie z zasadami.

          ![Usługa Azure Data Catalog — zarejestrowane obiekty](media/register-data-assets-tutorial/data-catalog-registered-objects.png)

    9. Aby wyświetlić swoje zarejestrowane obiekty źródła danych, wybierz **Wyświetl Portal**. W portalu usługi Azure Data Catalog, upewnij się, że wszystkie cztery tabele i baza danych w widoku siatki (Sprawdź, czy pasek wyszukiwania wyczyść).

        ![Obiekty w portalu usługi Azure Data Catalog](media/register-data-assets-tutorial/data-catalog-view-portal.png)

W tym ćwiczeniu zarejestrowano obiekty z przykładowej bazy danych Azure SQL, aby mogli je łatwo odnaleźć przez użytkowników całej organizacji.

W następnym ćwiczeniu zostanie przedstawiony sposób odnajdowania zarejestrowanych zasobów danych.

## <a name="discover-data-assets"></a>Odnajdywanie zasobów danych

Odnajdywanie w usłudze Azure Data Catalog korzysta z dwóch głównych mechanizmów: wyszukiwania i filtrowania.

Wyszukiwanie zaprojektowano pod kątem intuicyjnej obsługi i zaawansowanych możliwości. Domyślnie wyszukiwane terminy są dopasowywane do wszystkich właściwości w wykazie, w tym adnotacji wprowadzonych przez użytkownika.

Filtrowanie służy jako uzupełnienie wyszukiwania. Wybierając określone cechy, takie jak eksperci, typ źródła danych, typu obiektu i tagi, można wyświetlić pasujące zasoby danych i ograniczyć wyniki wyszukiwania do tych zasobów.

Dzięki połączeniu wyszukiwania i filtrowania, można szybko nawigować źródeł danych, które są zarejestrowane w usłudze Azure Data Catalog.

To ćwiczenie obejmuje korzystanie z portalu usługi Azure Data Catalog w celu odnalezienia zasobów danych zarejestrowanych w poprzednim ćwiczeniu. Aby uzyskać szczegółowe informacje o składni wyszukiwania, zobacz artykuł [Data Catalog Search syntax reference](/rest/api/datacatalog/#search-syntax-reference) (Dokumentacja dotycząca składni wyszukiwania w usłudze Data Catalog).

Poniżej przedstawiono kilka przykładów dotyczących odnajdywania zasobów danych w wykazie.  

### <a name="discover-data-assets-with-basic-search"></a>Odnajdywanie zasobów danych przy użyciu wyszukiwania podstawowego

Wyszukiwanie podstawowe ułatwia przeszukiwanie wykazu za pomocą co najmniej jednego wyszukiwanego terminu. Wyniki obejmują wszystkie zasoby, których dowolne właściwości są zgodne z co najmniej jednym terminem.

1. Wybierz **Home** w portalu usługi Azure Data Catalog. Jeśli po zamknięciu przeglądarki sieci web, przejdź do strony [strony głównej usługi Azure Data Catalog](https://www.azuredatacatalog.com).

2. W polu wyszukiwania wpisz `product` i naciśnij klawisz **ENTER**.

    ![Usługa Azure Data Catalog — podstawowe wyszukiwanie tekstowe](media/register-data-assets-tutorial/data-catalog-basic-text-search.png)

3. Upewnij się, że wszystkie cztery tabele i baza danych w wynikach. Możesz przełączać się między **widoku siatki** i **widok listy** , wybierając przyciski na pasku narzędzi, jak pokazano na poniższej ilustracji. Zwróć uwagę, że wyszukiwane słowo kluczowe jest wyróżnione w wynikach wyszukiwania, ponieważ opcja **Wyróżnienie** jest **WŁĄCZONA**. Dodatkowo można określić liczbę **wyników wyszukiwania wyświetlanych na stronie**.

    ![Usługa Azure Data Catalog — wyniki podstawowego wyszukiwania tekstowego](media/register-data-assets-tutorial/data-catalog-basic-text-search-results.png)

    Panel **Wyszukiwania** jest wyświetlany po lewej stronie, a panel **Właściwości** — po prawej stronie. W panelu **Wyszukiwania** można zmienić kryteria wyszukiwania i wyfiltrować wyniki. W panelu **Właściwości** są wyświetlane właściwości obiektu wybranego w siatce lub na liście.

4. Wybierz **produktu** w wynikach wyszukiwania. Wybierz **Podgląd**, **kolumn**, **profil danych**, i **dokumentacji** karty lub wybierz strzałkę, aby rozwinąć dolne okienko.  

    ![Usługa Azure Data Catalog — dolne okienko](media/register-data-assets-tutorial/data-catalog-data-asset-preview.png)

    Na karcie **Podgląd** zostanie wyświetlony podgląd danych z tabeli **Product**.  
5. Wybierz **kolumn** kartę, aby wyświetlić szczegóły kolumn (takie jak **nazwa** i **— typ danych**) w zasobie danych.

6. Wybierz **profil danych** kartę, aby wyświetlić profil danych (na przykład: liczba wierszy, rozmiar danych oraz minimalną wartość w kolumnie) w zasobie danych.

### <a name="discover-data-assets-with-property-scoping"></a>Odnajdywanie zasobów danych za pomocą wyznaczania zakresu właściwości

Wyznaczanie zakresu właściwości ułatwia odnajdywanie zasobów danych, gdy wyszukiwany termin zostanie dopasowany do określonej właściwości.

1. Wyczyść filtr **Tabela** w obszarze **Typ obiektu** w sekcji **Filtry**.  

2. W polu wyszukiwania wpisz `tags:product` i naciśnij klawisz **ENTER**. Aby uzyskać listę wszystkich właściwości, których można użyć do przeszukiwania wykazu danych, zobacz artykuł [Data Catalog Search syntax reference](/rest/api/datacatalog/#search-syntax-reference) (Dokumentacja dotycząca składni wyszukiwania w usłudze Data Catalog).

3. Upewnij się, że tabele i baza danych w wynikach.  

    ![Usługa Data Catalog — wyniki wyszukiwania uzyskane z użyciem wyznaczania zakresu właściwości](media/register-data-assets-tutorial/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>Zapisywanie wyszukiwania

1. W **wyszukiwania** okienka **bieżące wyszukiwanie** , wprowadź nazwę wyszukiwania i wybierz pozycję **Zapisz**.

    ![Usługa Azure Data Catalog — zapisywanie wyszukiwania](media/register-data-assets-tutorial/data-catalog-save-search.png)

2. Upewnij się, że zapisane wyszukiwanie jest wyświetlane w sekcji **Zapisane wyszukiwania**.

3. Wybierz jedną z akcji, które można zastosować do zapisanego wyszukiwania (**Zmień nazwę**, **Usuń**, **Zapisz jako domyślne**).

### <a name="grouping-with-parentheses"></a>Grupowanie za pomocą nawiasów

Nawiasy umożliwiają grupowanie części zapytania w celu uzyskania izolacji logicznej, szczególnie w połączeniu z operatorami logicznymi.

1. W polu wyszukiwania wpisz `name:product AND (tags:product AND objectType:table)` i naciśnij klawisz **ENTER**.

2. Upewnij się, że wyniki wyszukiwania obejmują tylko wiersze z tabeli **Product**.

    ![Usługa Azure Data Catalog — wyszukiwanie przy użyciu grupowania](media/register-data-assets-tutorial/data-catalog-grouping-search.png)

### <a name="comparison-operators"></a>Operatory porównania

Operatory porównania pozwalają używać innych porównań niż równość dla właściwości, które mają numeryczne i datowe typy danych.

1. W polu wyszukiwania wpisz `lastRegisteredTime:>"06/09/2016"`.

2. Wyczyść filtr **Tabela** w obszarze **Typ obiektu**.

3. Naciśnij klawisz **ENTER**.

4. Upewnij się, że **produktu**, **ProductCategory**, i **ProductDescription** tabel i Azure SQL database została zarejestrowana w wynikach wyszukiwania.

    ![Usługa Azure Data Catalog — wyniki wyszukiwania z użyciem porównania](media/register-data-assets-tutorial/data-catalog-comparison-operator-results.png)

Zobacz [jak odnajdywać zasoby danych](data-catalog-how-to-discover.md) szczegółowe informacje dotyczące odnajdywania zasobów danych. Aby uzyskać więcej informacji na temat składni wyszukiwania, zobacz [informacje o składni wyszukiwania wykazu danych](/rest/api/datacatalog/#search-syntax-reference).

## <a name="annotate-data-assets"></a>Dodawanie adnotacji do zasobów danych

W tym ćwiczeniu używasz dodawać adnotacje do portalu usługi Azure Data Catalog (Dodaj informacje, takie jak opisy, tagi lub ekspertów) istniejących zasobów danych w wykazie. Adnotacje uzupełniają metadane strukturalne wyodrębnione ze źródła danych podczas rejestracji. Adnotacja sprawia, że zasoby danych jest znacznie łatwiejsze do odnalezienia i zrozumienia.

W tym ćwiczeniu adnotacje zostaną dodane do pojedynczego zasobu danych (tabeli ProductPhoto). Do zasobu danych ProductPhoto zostanie dodana przyjazna nazwa oraz opis.  

1. Przejdź do [strony głównej usługi Azure Data Catalog](https://www.azuredatacatalog.com) i wyszukiwanie przy użyciu `tags:product` , aby znaleźć zasoby danych został zarejestrowany.

2. Wybierz **ProductModel** w wynikach wyszukiwania.  

3. W polu **Przyjazna nazwa** wpisz **Obrazy produktów**, a w polu **Opis** wpisz **Zdjęcia produktów do materiałów marketingowych**.

    ![Usługa Azure Data Catalog — opis tabeli ProductPhoto](media/register-data-assets-tutorial/data-catalog-productmodel-description.png)

    Pole **Opis** ułatwia innym osobom odnajdywanie wybranego zasobu danych i zrozumienie, dlaczego i w jaki sposób należy go używać. Możesz również dodać więcej tagów i wyświetlić kolumny. Możesz wyszukiwać i filtrowanie źródeł danych przy użyciu metadanych opisowych, które zostały dodane do wykazu.

Można również wykonać następujące czynności na tej stronie:

* Dodawać ekspertów dla zasobu danych. Wybierz **Dodaj** w **ekspertów** obszaru.

* Dodawać tagi na poziomie zestawu danych. Wybierz **Dodaj** w **tagi** obszaru. Możesz dodawać tagi użytkownika lub tagi słownika. Wersja Standard Edition usługi Data Catalog zawiera słownik biznesowy, który umożliwia administratorom wykazu definiowanie centralnej taksonomii biznesowej. Użytkownicy wykazu mogą następnie dodać adnotacje do zasobów danych za pomocą terminów ze słownika. Aby uzyskać więcej informacji, zobacz [How to set up the Business Glossary for Governed Tagging](data-catalog-how-to-business-glossary.md) (Jak skonfigurować słownik biznesowy na potrzeby znakowania zarządzanego).

* Dodawać tagi na poziomie kolumny. Wybierz **Dodaj** w obszarze **tagi** dla kolumny, której chcesz dodać adnotację.

* Dodawać opisy na poziomie kolumny. W polu **Opis** wprowadź opis kolumny. Możesz również wyświetlić metadane opisu wyodrębnione ze źródła danych.

* Dodawać informacje dotyczące **żądania dostępu**, które ułatwiają użytkownikom uzyskanie dostępu do zasobu danych.
  
* Na karcie **Dokumentacja** podaj informacje dotyczące dokumentacji zasobu danych. Dzięki funkcji dokumentacji usługi Azure Data Catalog możesz użyć wykazu danych jako repozytorium zawartości i stworzyć kompletny opis zasobów danych.
  
Ponadto możesz dodawać adnotacje do wielu zasobów danych. Na przykład możesz zaznaczyć wszystkie zarejestrowane zasoby danych i określić dla nich eksperta.

![Usługa Azure Data Catalog — dodawanie adnotacji do wielu zasobów danych](media/register-data-assets-tutorial/data-catalog-multi-select-annotate.png)

Usługa Azure Data Catalog obsługuje dodawanie adnotacji przez społeczność. Każdy użytkownik wykazu danych można dodać tagi (użytkownika lub słownika), opisy i inne metadane. Dzięki temu użytkownicy dodać perspektywy na zasobu danych i jego użycia i udostępniać innym użytkownikom taką perspektywę.

Szczegółowe informacje dotyczące dodawania adnotacji do zasobów danych można znaleźć w artykule [How to annotate data assets](data-catalog-how-to-annotate.md) (Jak dodawać adnotacje do zasobów danych).

## <a name="connect-to-data-assets"></a>Łączenie z zasobami danych

To ćwiczenie obejmuje otwieranie zasobów danych za pomocą zintegrowanego narzędzia klienckiego (programu Excel) oraz narzędzia niezintegrowanego (programu SQL Server Management Studio) przy użyciu informacji o połączeniu.

> [!NOTE]
> Należy pamiętać, że usługa Azure Data Catalog nie zapewnia dostępu do rzeczywistego źródła danych, a tylko ułatwia użytkownikom odnalezienie źródła danych i zrozumienie jego przeznaczenia. Podczas nawiązywania połączenia ze źródłem danych wybrana aplikacja kliencka używa poświadczeń systemu Windows lub w razie potrzeby wyświetla monit o ich podanie. Jeśli nie masz udzielonego wcześniej dostępu do źródła danych, musisz go uzyskać, aby nawiązać połączenie.

### <a name="connect-to-a-data-asset-from-excel"></a>Nawiązywanie połączenia z zasobem danych przy użyciu programu Excel

1. Wybierz pozycję **Product** w wynikach wyszukiwania. Wybierz **Otwórz** na pasku narzędzi i wybierz **Excel**.

    ![Usługa Azure Data Catalog — nawiązywanie połączenia z zasobem danych](media/register-data-assets-tutorial/data-catalog-connect1.png)

2. Wybierz **Otwórz** w oknie podręcznym pobierania. Wygląd elementów interfejsu zależy od przeglądarki.

3. W **powiadomienie o zabezpieczeniach programu Microsoft Excel** wybierz **Włącz**.

    ![Usługa Azure Data Catalog — okno podręczne zabezpieczeń programu Excel](media/register-data-assets-tutorial/data-catalog-excel-security-popup.png)

4. Pozostaw wartości domyślne **importu danych** okno dialogowe, a następnie wybierz **OK**.

    ![Usługa Azure Data Catalog — importowanie danych w programie Excel](media/register-data-assets-tutorial/data-catalog-excel-import-data.png)

5. Wyświetl źródło danych w programie Excel.

    ![Usługa Azure Data Catalog — tabela produktów w programie Excel](media/register-data-assets-tutorial/data-catalog-connect2.png)

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

W tym ćwiczeniu zostało nawiązane połączenie z zasobami danych odnalezionymi za pomocą usługi Azure Data Catalog. Portal usługi Azure Data Catalog umożliwia bezpośrednie nawiązywanie połączenia przy użyciu aplikacji klienckich zintegrowanych z menu **Otwórz w**. Możesz również nawiązywać połączenia za pomocą dowolnej wybranej aplikacji przy użyciu informacji o lokalizacji połączenia zawartych w metadanych zasobów. Na przykład można użyć programu SQL Server Management Studio do łączenia z bazą danych Azure SQL na dostęp do danych zasobów danych zarejestrowanych w tym samouczku.

1. Otwórz program **SQL Server Management Studio**.

2. W oknie dialogowym **Połączenie z serwerem** wprowadź nazwę serwera z okienka **Właściwości** w portalu usługi Azure Data Catalog.

3. Wybierz właściwą metodę uwierzytelniania i wpisz odpowiednie poświadczenia, aby uzyskać dostęp do zasobu danych. Jeśli nie masz dostępu, skorzystaj z informacji zawartych w polu **Żądanie dostępu**, aby go uzyskać.

    ![Usługa Azure Data Catalog — żądanie dostępu](media/register-data-assets-tutorial/data-catalog-request-access.png)

Wybierz **Wyświetl parametry połączenia** do wyświetlenia, a następnie skopiuj parametry połączenia ADO.NET, ODBC i OLEDB do Schowka w celu użycia ich w aplikacji.

## <a name="manage-data-assets"></a>Zarządzanie zasobami danych

Ta procedura obejmuje konfigurowanie zabezpieczeń zasobów danych. Data Catalog nie zapewnia użytkownikom dostępu do danych. Zarządzanie dostępem do danych należy do właściciela źródła danych.

Usługa Data Catalog umożliwia odnajdywanie źródeł danych zarejestrowanych w wykazie i wyświetlanie metadanych powiązanych z tymi źródłami. W niektórych sytuacjach może jednak wystąpić konieczność udostępnienia źródeł danych tylko konkretnym użytkownikom lub członkom określonych grup. Dla tych scenariuszy używania usługi Data Catalog można przejmować na własność zasobów danych zarejestrowanych i kontrolować widoczność zasobów, których jesteś właścicielem.

> [!NOTE]
> Funkcje zarządzania opisane w tym ćwiczeniu są dostępne tylko w wersji Standard Edition usługi Azure Data Catalog, a nie w wersji Free Edition.
> W usłudze Azure Data Catalog można przejmować na własność zasoby danych, dodawać współwłaścicieli zasobów danych i ustawiać widoczność zasobów danych.

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>Przejmowanie własności do zasobów danych i ograniczanie ich widoczności

1. Przejdź do [strony głównej usługi Azure Data Catalog](https://www.azuredatacatalog.com). W polu **Wyszukiwanie** wpisz `tags:cycles` i naciśnij klawisz **ENTER**.

2. Wybierz element na liście wyników, a następnie wybierz **Przejmij na własność** na pasku narzędzi.

3. W **zarządzania** części **właściwości** panelu wybierz **Przejmij na własność**.

    ![Usługa Azure Data Catalog — przejmowanie własności](media/register-data-assets-tutorial/data-catalog-take-ownership.png)

4. Aby ograniczyć widoczność, wybierz opcję **właściciele i Ci użytkownicy** w **widoczność** i wybierz pozycję **Dodaj**. Wprowadź adresy e-mail użytkowników w polu tekstowym i naciśnij klawisz **ENTER**.

    ![Usługa Azure Data Catalog — ograniczanie dostępu](media/register-data-assets-tutorial/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>Usuwanie zasobów danych

W tym ćwiczeniu portal usługi Azure Data Catalog zostanie użyty do usunięcia danych podglądu z zarejestrowanych zasobów danych i usunięcia zasobów danych z wykazu.

W usłudze Azure Data Catalog można usuwać pojedyncze zasoby lub wiele zasobów.

1. Przejdź do [strony głównej usługi Azure Data Catalog](https://www.azuredatacatalog.com).

2. W **wyszukiwania** tekstu wprowadź `tags:cycles` i wybierz **ENTER**.

3. Wybierz element na liście wyników, a następnie wybierz **Usuń** na pasku narzędzi, jak pokazano na poniższej ilustracji:

    ![Usługa Azure Data Catalog — usuwanie elementu siatki](media/register-data-assets-tutorial/data-catalog-delete-grid-item.png)

    Jeśli używasz widoku listy pole wyboru jest na lewo od elementu jak pokazano na poniższej ilustracji:

    ![Usługa Azure Data Catalog — usuwanie elementu listy](media/register-data-assets-tutorial/data-catalog-delete-list-item.png)

    Można również wybierać i usuwać kilka zasobów danych, jak pokazano na poniższej ilustracji:

    ![Usługa Azure Data Catalog — usuwanie kilku zasobów danych](media/register-data-assets-tutorial/data-catalog-delete-assets.png)

> [!NOTE]
> Domyślnym zachowaniem katalogu jest umożliwianie każdemu użytkownikowi rejestrowania dowolnego źródła danych oraz usuwania wszystkich zarejestrowanych zasobów danych. Funkcje zarządzania dostępne w wersji Standard Edition usługi Azure Data Catalog dodatkowo umożliwiają przejmowania własności do zasobów i ograniczanie grona użytkowników, którzy mogą odnajdywać i usuwać zasoby.

## <a name="summary"></a>Podsumowanie

W tym samouczku zostały przedstawione podstawowe funkcje usługi Azure Data Catalog, w tym rejestrowanie, dodawanie adnotacji, odnajdywanie i zarządzanie firmowymi zasobami danych. Po zakończeniu tego samouczka nadszedł czas na rozpoczęcie pracy. Możesz rozpocząć dzisiaj od zarejestrowania źródeł danych, na którym pracujesz Ty i Twój zespół, oraz od zaproszenia współpracowników do korzystania z wykazu.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Obsługiwane źródła danych](data-catalog-dsr.md)