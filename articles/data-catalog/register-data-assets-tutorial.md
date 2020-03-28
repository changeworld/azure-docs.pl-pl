---
title: Rejestrowanie zasobów danych w usłudze Azure Data Catalog
description: W tym samouczku opisano sposób rejestrowania zasobów danych w wykazie danych platformy Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: tutorial
ms.date: 08/01/2019
ms.openlocfilehash: 4bd2b7093100ff24b21b67ea84613ac9b2ec8299
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68950245"
---
# <a name="tutorial-register-data-assets-in-azure-data-catalog"></a>Samouczek: rejestrowanie zasobów danych w wykazie danych platformy Azure

W tym samouczku za pomocą narzędzia rejestracji do rejestrowania zasobów danych z przykładu bazy danych SQL platformy Azure z wykazem. Rejestracja to proces wyodrębniania kluczowych metadanych strukturalnych, takich jak nazwy, typy i lokalizacje, ze źródła danych i zasobów, które się w nim znajdują, oraz kopiowania tych metadanych do wykazu. Źródło danych i zasoby danych pozostają tam, gdzie się znajdowały, ale metadane są używane przez wykaz, aby można było je łatwiej odnaleźć i zrozumieć.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Rejestrowanie zasobów danych 
> * Wyszukiwanie zasobów danych
> * Dodawanie adnotacji do zasobów danych
> * Łączenie z zasobami danych
> * Zarządzanie zasobami danych
> * Usuwanie zasobów danych

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, należy ukończyć [program Szybki start](register-data-assets-tutorial.md).

* Subskrypcja [platformy Microsoft Azure.](https://azure.microsoft.com/)
* Musisz mieć własną [dzierżawę usługi Azure Active Directory.](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

Aby skonfigurować wykaz danych, musisz być właścicielem lub współwłaścicielem subskrypcji platformy Azure.

## <a name="register-data-assets"></a>Rejestrowanie zasobów danych

### <a name="register-a-data-source"></a>Rejestrowanie źródła danych

Można zarejestrować zasoby danych (tabele) z [przykładowej bazy danych SQL platformy Azure,](../sql-database/sql-database-single-database-get-started.md)ale można użyć dowolnego obsługiwanego źródła danych, jeśli wolisz pracować z danymi, które są znane i istotne dla danej roli. Aby uzyskać listę obsługiwanych źródeł danych, zobacz [Supported data sources](data-catalog-dsr.md) (Obsługiwane źródła danych).

Nazwa bazy danych SQL platformy Azure, których używamy w tym samouczku, to *RLSTest*.

Teraz można zarejestrować zasoby danych z przykładu bazy danych SQL platformy Azure przy użyciu usługi Azure Data Catalog.

1. Przejdź do [strony głównej usługi Azure Data Catalog](http://azuredatacatalog.com) i wybierz pozycję **Publikuj dane**.

   ![Usługa Azure Data Catalog — przycisk Publikuj dane](media/register-data-assets-tutorial/data-catalog-publish-data.png)

2. wybierz **uruchom aplikację,** aby pobrać, zainstalować i uruchomić narzędzie rejestracji na komputerze.

   ![Usługa Azure Data Catalog — przycisk Uruchom](media/register-data-assets-tutorial/data-catalog-launch-application.png)

3. Na stronie **Powitalnej** wybierz pozycję **Zaloguj się** i wprowadź poświadczenia.

    ![Usługa Azure Data Catalog — strona powitalna](media/register-data-assets-tutorial/data-catalog-welcome-dialog.png)

4. Na stronie **Wykaz danych platformy Microsoft Azure** wybierz pozycję SQL **Server** i **Next**.

    ![Usługa Azure Data Catalog — źródła danych](media/register-data-assets-tutorial/data-catalog-data-sources.png)

5. Wprowadź właściwości połączenia programu SQL Server dla przykładu bazy danych SQL platformy Azure i wybierz pozycję **CONNECT**.

   ![Usługa Azure Data Catalog — ustawienia połączenia programu SQL Server](media/register-data-assets-tutorial/data-catalog-sql-server-connection.png)

6. Zarejestruj metadane zasobów danych. W tym przykładzie można zarejestrować **obiekty produktu** z przykładowego obszaru nazw bazy danych SQL azure:

    1. W drzewie **Hierarchia serwerów** rozwiń przykład bazy danych SQL platformy Azure i wybierz pozycję **SalesLT**.

    2. Wybierz **pozycję Produkt**, Kategoria **produktu,** **Opis produktu**i **ProductModel** za pomocą klawiszy Ctrl+select.

    3. wybrać **strzałkę wybraną przez ruch** (**>**). Spowoduje to przeniesienie wszystkich wybranych obiektów na listę **Obiekty do zarejestrowania**.

          ![Samouczek dotyczący usługi Azure Data Catalog — przeglądanie i wybieranie obiektów](media/register-data-assets-tutorial/data-catalog-server-hierarchy.png)

    4. Wybierz pozycję **Dołącz podgląd**, aby dołączyć podgląd danych w postaci migawki. Migawka zawiera maksymalnie 20 rekordów z każdej tabeli i jest kopiowana do katalogu.

    5. Wybierz pozycję **Dołącz profil danych**, aby dołączyć migawkę statystyk obiektów dla profilu danych (na przykład liczbę wierszy czy minimalną, maksymalną i średnią wartość w kolumnie).

    6. W polu **Dodaj znaczniki** wprowadź **sprzedaż, produkt, azure sql**. Spowoduje to dodanie tagów wyszukiwania dla tych zasobów danych. Tagi to doskonały sposób na ułatwienie użytkownikom znalezienia zarejestrowanego źródła danych.

    7. Podaj imię i nazwisko **eksperta** w zakresie przeznaczenia tych danych (opcjonalnie).

          ![Samouczek dotyczący usługi Azure Data Catalog — obiekty do zarejestrowania](media/register-data-assets-tutorial/data-catalog-objects-register.png)

    8. Wybierz **POZYCJĘ ZAREJESTRUJ**. Wybrane obiekty zostaną zarejestrowane za pomocą usługi Azure Data Catalog. W tym ćwiczeniu są rejestrowane wybrane obiekty z przykładowej bazy danych SQL platformy Azure. Za pomocą narzędzia rejestracji metadane są wyodrębniane z zasobów danych i kopiowane do usługi Azure Data Catalog. Dane pozostają tam, gdzie obecnie przebywa. Dane pozostają pod kontrolą administratorów i zasad systemu pochodzenia.

          ![Usługa Azure Data Catalog — zarejestrowane obiekty](media/register-data-assets-tutorial/data-catalog-registered-objects.png)

    9. Aby wyświetlić zarejestrowane obiekty źródła danych, wybierz pozycję **Wyświetl portal**. W portalu usługi Azure Data Catalog upewnij się, że widzisz wszystkie cztery tabele i bazę danych w widoku siatki (sprawdź, czy pasek wyszukiwania jest czysty).

        ![Obiekty w portalu usługi Azure Data Catalog](media/register-data-assets-tutorial/data-catalog-view-portal.png)

W tym ćwiczeniu zarejestrowano obiekty z przykładu bazy danych SQL platformy Azure, dzięki czemu mogą być łatwo odnajdywana przez użytkowników w całej organizacji.

W następnym ćwiczeniu zostanie przedstawiony sposób odnajdowania zarejestrowanych zasobów danych.

## <a name="discover-data-assets"></a>Odnajdywanie zasobów danych

Odnajdywanie w usłudze Azure Data Catalog korzysta z dwóch głównych mechanizmów: wyszukiwania i filtrowania.

Wyszukiwanie zaprojektowano pod kątem intuicyjnej obsługi i zaawansowanych możliwości. Domyślnie wyszukiwane terminy są dopasowywane do wszystkich właściwości w wykazie, w tym adnotacji wprowadzonych przez użytkownika.

Filtrowanie służy jako uzupełnienie wyszukiwania. Wybierając określone cechy, takie jak eksperci, typ źródła danych, typu obiektu i tagi, można wyświetlić pasujące zasoby danych i ograniczyć wyniki wyszukiwania do tych zasobów.

Za pomocą kombinacji wyszukiwania i filtrowania, można szybko poruszać się po źródłach danych, które są zarejestrowane w usłudze Azure Data Catalog.

To ćwiczenie obejmuje korzystanie z portalu usługi Azure Data Catalog w celu odnalezienia zasobów danych zarejestrowanych w poprzednim ćwiczeniu. Aby uzyskać szczegółowe informacje o składni wyszukiwania, zobacz artykuł [Data Catalog Search syntax reference](/rest/api/datacatalog/#search-syntax-reference) (Dokumentacja dotycząca składni wyszukiwania w usłudze Data Catalog).

Poniżej przedstawiono kilka przykładów dotyczących odnajdywania zasobów danych w wykazie.  

### <a name="discover-data-assets-with-basic-search"></a>Odnajdywanie zasobów danych przy użyciu wyszukiwania podstawowego

Wyszukiwanie podstawowe ułatwia przeszukiwanie wykazu za pomocą co najmniej jednego wyszukiwanego terminu. Wyniki obejmują wszystkie zasoby, których dowolne właściwości są zgodne z co najmniej jednym terminem.

1. wybierz **pozycję Strona główna** w portalu usługi Azure Data Catalog. Jeśli przeglądarka internetowa została zamknięta, przejdź do [strony głównej usługi Azure Data Catalog](https://www.azuredatacatalog.com).

2. W polu wyszukiwania wpisz `product` i naciśnij klawisz **ENTER**.

    ![Usługa Azure Data Catalog — podstawowe wyszukiwanie tekstowe](media/register-data-assets-tutorial/data-catalog-basic-text-search.png)

3. Upewnij się, że wszystkie cztery tabele i bazy danych w wynikach. Można przełączać się między **widokiem siatki** a **widokiem listy,** wybierając przyciski na pasku narzędzi, jak pokazano na poniższej ilustracji. Zwróć uwagę, że wyszukiwane słowo kluczowe jest wyróżnione w wynikach wyszukiwania, ponieważ opcja **Wyróżnienie** jest **WŁĄCZONA**. Dodatkowo można określić liczbę **wyników wyszukiwania wyświetlanych na stronie**.

    ![Usługa Azure Data Catalog — wyniki podstawowego wyszukiwania tekstowego](media/register-data-assets-tutorial/data-catalog-basic-text-search-results.png)

    Panel **Wyszukiwania** jest wyświetlany po lewej stronie, a panel **Właściwości** — po prawej stronie. W panelu **Wyszukiwania** można zmienić kryteria wyszukiwania i wyfiltrować wyniki. W panelu **Właściwości** są wyświetlane właściwości obiektu wybranego w siatce lub na liście.

4. wybierz **produkt** w wynikach wyszukiwania. wybierz karty **Podgląd,** **Kolumny,** **Profil danych**i **Dokumentacja** lub wybierz strzałkę, aby rozwinąć dolne okienko.  

    ![Usługa Azure Data Catalog — dolne okienko](media/register-data-assets-tutorial/data-catalog-data-asset-preview.png)

    Na karcie **Podgląd** zostanie wyświetlony podgląd danych z tabeli **Product**.  
5. wybierz kartę **Kolumny,** aby znaleźć szczegółowe informacje o kolumnach (takich jak **nazwa** i **typ danych)** w zasobie danych.

6. wybierz kartę **Profil danych,** aby wyświetlić profilowanie danych (na przykład: liczbę wierszy, rozmiar danych lub minimalną wartość w kolumnie) w zasobie danych.

### <a name="discover-data-assets-with-property-scoping"></a>Odnajdywanie zasobów danych za pomocą wyznaczania zakresu właściwości

Wyznaczanie zakresu właściwości ułatwia odnajdywanie zasobów danych, gdy wyszukiwany termin zostanie dopasowany do określonej właściwości.

1. Wyczyść filtr **Tabela** w obszarze **Typ obiektu** w sekcji **Filtry**.  

2. W polu wyszukiwania wpisz `tags:product` i naciśnij klawisz **ENTER**. Aby uzyskać listę wszystkich właściwości, których można użyć do przeszukiwania wykazu danych, zobacz artykuł [Data Catalog Search syntax reference](/rest/api/datacatalog/#search-syntax-reference) (Dokumentacja dotycząca składni wyszukiwania w usłudze Data Catalog).

3. Upewnij się, że widzisz tabele i bazy danych w wynikach.  

    ![Usługa Data Catalog — wyniki wyszukiwania uzyskane z użyciem wyznaczania zakresu właściwości](media/register-data-assets-tutorial/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>Zapisywanie wyszukiwania

1. W okienku **Wyszukiwania** w sekcji **Bieżące wyszukiwanie** wprowadź nazwę wyszukiwania i wybierz pozycję **Zapisz**.

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

3. Naciśnij **klawisz ENTER**.

4. Upewnij się, że są widoczne tabele **Produkt**, **Kategoria produktu**i **ProductDescription** oraz baza danych SQL platformy Azure zarejestrowana w wynikach wyszukiwania.

    ![Usługa Azure Data Catalog — wyniki wyszukiwania z użyciem porównania](media/register-data-assets-tutorial/data-catalog-comparison-operator-results.png)

Zobacz [Jak odnajdować zasoby danych, aby](data-catalog-how-to-discover.md) uzyskać szczegółowe informacje na temat odnajdowania zasobów danych. Aby uzyskać więcej informacji na temat składni wyszukiwania, zobacz [Odwołanie do składni wyszukiwania wykazu danych](/rest/api/datacatalog/#search-syntax-reference).

## <a name="annotate-data-assets"></a>Dodawanie adnotacji do zasobów danych

W tym ćwiczeniu można użyć portalu usługi Azure Data Catalog do dodawania adnotacji (dodawanie informacji, takich jak opisy, tagi lub eksperci) istniejących zasobów danych w katalogu. Adnotacje uzupełniają metadane strukturalne wyodrębnione ze źródła danych podczas rejestracji. Adnotacja znacznie ułatwia odnajdywanie i zrozumienie zasobów danych.

W tym ćwiczeniu adnotacje zostaną dodane do pojedynczego zasobu danych (tabeli ProductPhoto). Do zasobu danych ProductPhoto zostanie dodana przyjazna nazwa oraz opis.  

1. Przejdź do [strony głównej usługi Azure Data Catalog](https://www.azuredatacatalog.com) i wyszukaj, `tags:product` aby znaleźć zarejestrowane zasoby danych.

2. wybierz **ProductModel** w wynikach wyszukiwania.  

3. W polu **Przyjazna nazwa** wpisz **Obrazy produktów**, a w polu **Opis** wpisz **Zdjęcia produktów do materiałów marketingowych**.

    ![Usługa Azure Data Catalog — opis tabeli ProductPhoto](media/register-data-assets-tutorial/data-catalog-productmodel-description.png)

    Pole **Opis** ułatwia innym osobom odnajdywanie wybranego zasobu danych i zrozumienie, dlaczego i w jaki sposób należy go używać. Możesz również dodać więcej tagów i wyświetlić kolumny. Źródła danych można wyszukiwać i filtrować przy użyciu metadanych opisowych dodanych do katalogu.

Na tej stronie można również wykonać następujące czynności:

* Dodawać ekspertów dla zasobu danych. wybierz **pozycję Dodaj** w obszarze **Eksperci.**

* Dodawać tagi na poziomie zestawu danych. wybierz **pozycję Dodaj** w obszarze **Znaczniki.** Możesz dodawać tagi użytkownika lub tagi słownika. Wersja Standard Edition usługi Data Catalog zawiera słownik biznesowy, który umożliwia administratorom wykazu definiowanie centralnej taksonomii biznesowej. Użytkownicy wykazu mogą następnie dodać adnotacje do zasobów danych za pomocą terminów ze słownika. Aby uzyskać więcej informacji, zobacz [Jak skonfigurować słowniczek biznesowy dla tagowania](data-catalog-how-to-business-glossary.md)

* Dodawać tagi na poziomie kolumny. wybierz **pozycję Dodaj** w obszarze **Znaczniki** dla kolumny, którą chcesz dodać adnotacje.

* Dodawać opisy na poziomie kolumny. W polu **Opis** wprowadź opis kolumny. Możesz również wyświetlić metadane opisu wyodrębnione ze źródła danych.

* Dodawać informacje dotyczące **żądania dostępu**, które ułatwiają użytkownikom uzyskanie dostępu do zasobu danych.
  
* Na karcie **Dokumentacja** podaj informacje dotyczące dokumentacji zasobu danych. Dzięki funkcji dokumentacji usługi Azure Data Catalog możesz użyć wykazu danych jako repozytorium zawartości i stworzyć kompletny opis zasobów danych.
  
Ponadto możesz dodawać adnotacje do wielu zasobów danych. Na przykład możesz zaznaczyć wszystkie zarejestrowane zasoby danych i określić dla nich eksperta.

![Usługa Azure Data Catalog — dodawanie adnotacji do wielu zasobów danych](media/register-data-assets-tutorial/data-catalog-multi-select-annotate.png)

Usługa Azure Data Catalog obsługuje dodawanie adnotacji przez społeczność. Każdy użytkownik wykazu danych może dodawać tagi (użytkownika lub słowniczek), opisy i inne metadane. W ten sposób użytkownicy dodają perspektywę zasobu danych i jego wykorzystania i udostępniają tę perspektywę innym użytkownikom.

Szczegółowe informacje dotyczące dodawania adnotacji do zasobów danych można znaleźć w artykule [How to annotate data assets](data-catalog-how-to-annotate.md) (Jak dodawać adnotacje do zasobów danych).

## <a name="connect-to-data-assets"></a>Łączenie z zasobami danych

To ćwiczenie obejmuje otwieranie zasobów danych za pomocą zintegrowanego narzędzia klienckiego (programu Excel) oraz narzędzia niezintegrowanego (programu SQL Server Management Studio) przy użyciu informacji o połączeniu.

> [!NOTE]
> Należy pamiętać, że usługa Azure Data Catalog nie zapewnia dostępu do rzeczywistego źródła danych, a tylko ułatwia użytkownikom odnalezienie źródła danych i zrozumienie jego przeznaczenia. Podczas nawiązywania połączenia ze źródłem danych wybrana aplikacja kliencka używa poświadczeń systemu Windows lub w razie potrzeby wyświetla monit o ich podanie. Jeśli nie masz udzielonego wcześniej dostępu do źródła danych, musisz go uzyskać, aby nawiązać połączenie.

### <a name="connect-to-a-data-asset-from-excel"></a>Nawiązywanie połączenia z zasobem danych przy użyciu programu Excel

1. Wybierz pozycję **Product** w wynikach wyszukiwania. wybierz **pozycję Otwórz w** na pasku narzędzi i wybierz pozycję **Excel**.

    ![Usługa Azure Data Catalog — nawiązywanie połączenia z zasobem danych](media/register-data-assets-tutorial/data-catalog-connect1.png)

2. Wybierz **pozycję Otwórz** w wyskakującym oknie pobierania. Wygląd elementów interfejsu zależy od przeglądarki.

3. W oknie **Informacja o zabezpieczeniach programu Microsoft Excel** wybierz pozycję **Włącz**.

    ![Usługa Azure Data Catalog — okno podręczne zabezpieczeń programu Excel](media/register-data-assets-tutorial/data-catalog-excel-security-popup.png)

4. Zachowaj wartości domyślne w oknie dialogowym **Importowanie danych** i wybierz przycisk **OK**.

    ![Usługa Azure Data Catalog — importowanie danych w programie Excel](media/register-data-assets-tutorial/data-catalog-excel-import-data.png)

5. Wyświetl źródło danych w programie Excel.

    ![Usługa Azure Data Catalog — tabela produktów w programie Excel](media/register-data-assets-tutorial/data-catalog-connect2.png)

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

W tym ćwiczeniu zostało nawiązane połączenie z zasobami danych odnalezionymi za pomocą usługi Azure Data Catalog. Portal usługi Azure Data Catalog umożliwia bezpośrednie nawiązywanie połączenia przy użyciu aplikacji klienckich zintegrowanych z menu **Otwórz w**. Możesz również nawiązywać połączenia za pomocą dowolnej wybranej aplikacji przy użyciu informacji o lokalizacji połączenia zawartych w metadanych zasobów. Na przykład można użyć SQL Server Management Studio, aby połączyć się z bazą danych SQL platformy Azure, aby uzyskać dostęp do danych w zasobach danych zarejestrowanych w tym samouczku.

1. Otwórz **program SQL Server Management Studio**.

2. W oknie dialogowym **Połączenie z serwerem** wprowadź nazwę serwera z okienka **Właściwości** w portalu usługi Azure Data Catalog.

3. Wybierz właściwą metodę uwierzytelniania i wpisz odpowiednie poświadczenia, aby uzyskać dostęp do zasobu danych. Jeśli nie masz dostępu, skorzystaj z informacji zawartych w polu **Żądanie dostępu**, aby go uzyskać.

    ![Usługa Azure Data Catalog — żądanie dostępu](media/register-data-assets-tutorial/data-catalog-request-access.png)

Wybierz **opcję Wyświetl parametry połączenia,** aby wyświetlić i skopiować ADO.NET, ODBC i OLEDB do schowka do użycia w aplikacji.

## <a name="manage-data-assets"></a>Zarządzanie zasobami danych

Ta procedura obejmuje konfigurowanie zabezpieczeń zasobów danych. Wykaz danych nie daje użytkownikom dostępu do samych danych. Zarządzanie dostępem do danych należy do właściciela źródła danych.

Usługa Data Catalog umożliwia odnajdywanie źródeł danych zarejestrowanych w wykazie i wyświetlanie metadanych powiązanych z tymi źródłami. W niektórych sytuacjach może jednak wystąpić konieczność udostępnienia źródeł danych tylko konkretnym użytkownikom lub członkom określonych grup. W tych scenariuszach można użyć wykazu danych, aby przejąć na własność zarejestrowane zasoby danych i kontrolować widoczność posiadanych zasobów.

> [!NOTE]
> Funkcje zarządzania opisane w tym ćwiczeniu są dostępne tylko w wersji Standard Edition usługi Azure Data Catalog, a nie w wersji Free Edition.
> W usłudze Azure Data Catalog można przejmować na własność zasoby danych, dodawać współwłaścicieli zasobów danych i ustawiać widoczność zasobów danych.

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>Przejmowanie własności do zasobów danych i ograniczanie ich widoczności

1. Przejdź do [strony głównej usługi Azure Data Catalog](https://www.azuredatacatalog.com). W polu **Wyszukiwanie** wpisz `tags:cycles` i naciśnij klawisz **ENTER**.

2. wybierz element na liście wyników i wybierz pozycję **Przejmij na własność** na pasku narzędzi.

3. W sekcji **Zarządzanie** panelu **Właściwości** wybierz pozycję **Przejmij na własność**.

    ![Usługa Azure Data Catalog — przejmowanie własności](media/register-data-assets-tutorial/data-catalog-take-ownership.png)

4. Aby ograniczyć widoczność, wybierz pozycję **Właściciele & ci użytkownicy** w sekcji **Widoczność** i wybierz pozycję **Dodaj**. Wprowadź adresy e-mail użytkowników w polu tekstowym i naciśnij klawisz **ENTER**.

    ![Usługa Azure Data Catalog — ograniczanie dostępu](media/register-data-assets-tutorial/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>Usuwanie zasobów danych

W tym ćwiczeniu portal usługi Azure Data Catalog zostanie użyty do usunięcia danych podglądu z zarejestrowanych zasobów danych i usunięcia zasobów danych z wykazu.

W usłudze Azure Data Catalog można usuwać pojedyncze zasoby lub wiele zasobów.

1. Przejdź do [strony głównej usługi Azure Data Catalog](https://www.azuredatacatalog.com).

2. W polu **tekstowym Wyszukaj** wprowadź `tags:cycles` i wybierz pozycję **ENTER**.

3. Zaznacz element na liście wyników i wybierz pozycję **Usuń** na pasku narzędzi, jak pokazano na poniższej ilustracji:

    ![Usługa Azure Data Catalog — usuwanie elementu siatki](media/register-data-assets-tutorial/data-catalog-delete-grid-item.png)

    Jeśli używasz widoku listy, pole wyboru znajduje się po lewej stronie elementu, jak pokazano na poniższej ilustracji:

    ![Usługa Azure Data Catalog — usuwanie elementu listy](media/register-data-assets-tutorial/data-catalog-delete-list-item.png)

    Można również wybierać i usuwać kilka zasobów danych, jak pokazano na poniższej ilustracji:

    ![Usługa Azure Data Catalog — usuwanie kilku zasobów danych](media/register-data-assets-tutorial/data-catalog-delete-assets.png)

> [!NOTE]
> Domyślnym zachowaniem katalogu jest umożliwianie każdemu użytkownikowi rejestrowania dowolnego źródła danych oraz usuwania wszystkich zarejestrowanych zasobów danych. Funkcje zarządzania dostępne w wersji Standard Edition usługi Azure Data Catalog dodatkowo umożliwiają przejmowania własności do zasobów i ograniczanie grona użytkowników, którzy mogą odnajdywać i usuwać zasoby.

## <a name="summary"></a>Podsumowanie

W tym samouczku zostały przedstawione podstawowe funkcje usługi Azure Data Catalog, w tym rejestrowanie, dodawanie adnotacji, odnajdywanie i zarządzanie firmowymi zasobami danych. Po zakończeniu tego samouczka nadszedł czas na rozpoczęcie pracy. Możesz rozpocząć dzisiaj od zarejestrowania źródeł danych, na którym pracujesz Ty i Twój zespół, oraz od zaproszenia współpracowników do korzystania z wykazu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Obsługiwane źródła danych](data-catalog-dsr.md)
