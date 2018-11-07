---
title: Rozpoczynanie pracy z usługą Azure Data Catalog
description: Ten kompleksowy samouczek zawiera scenariusze dotyczące usługi Azure Data Catalog i opis jej możliwości.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.assetid: 03332872-8d84-44a0-8a78-04fd30e14b18
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: dd3cb108844cd009d74de42630511073021d94b1
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238492"
---
# <a name="get-started-with-azure-data-catalog"></a>Rozpoczynanie pracy z usługą Azure Data Catalog
Azure Data Catalog to w pełni zarządzana usługa w chmurze służąca jako system rejestracji i odnajdywania zasobów danych przedsiębiorstwa. Szczegółowe omówienie tej usługi można znaleźć w artykule [Co to jest usługa Azure Data Catalog?](data-catalog-what-is-data-catalog.md).

Ten samouczek ułatwia rozpoczęcie pracy z usługą Azure Data Catalog i obejmuje następujące procedury:

| Procedura | Opis |
|:--- |:--- |
| [Aprowizowanie wykazu danych](#provision-data-catalog) |Ta procedura obejmuje aprowizację lub konfigurację usługi Azure Data Catalog. Należy ją wykonać tylko w przypadku, gdy wykaz nie został wcześniej skonfigurowany. W ramach jednej organizacji (domeny usługi Microsoft Azure Active Directory) może istnieć tylko jeden wykaz danych, nawet jeśli z kontem platformy Azure jest powiązanych wiele subskrypcji. |
| [Rejestrowanie zasobów danych](#register-data-assets) |W tej procedurze w wykazie danych zostaną zarejestrowane zasoby z przykładowej bazy danych AdventureWorks2014. Rejestracja to proces wyodrębniania kluczowych metadanych strukturalnych, takich jak nazwy, typy i lokalizacje, ze źródła danych oraz kopiowania tych metadanych do wykazu. Źródło danych i zasoby danych pozostają tam, gdzie się znajdowały, ale metadane są używane przez wykaz, aby można było je łatwiej odnaleźć i zrozumieć. |
| [Odnajdywanie zasobów danych](#discover-data-assets) |Ta procedura obejmuje korzystanie z portalu usługi Azure Data Catalog w celu odnalezienia zasobów danych zarejestrowanych w poprzednim kroku. Po zarejestrowaniu źródła danych w usłudze Azure Data Catalog jego metadane są indeksowane przez tę usługę, co ułatwia użytkownikom wyszukiwanie potrzebnych danych. |
| [Dodawanie adnotacji do zasobów danych](#annotate-data-assets) |Ta procedura obejmuje dodawanie adnotacji (informacji, takich jak opisy, tagi, dokumentacja i eksperci) do zasobów danych. Informacje te stanowią uzupełnienie metadanych wyodrębnionych ze źródła danych i sprawiają, że źródło danych jest bardziej zrozumiałe dla użytkowników. |
| [Łączenie z zasobami danych](#connect-to-data-assets) |Ta procedura obejmuje otwieranie zasobów danych za pomocą zintegrowanych narzędzi klienckich (na przykład programu Excel i narzędzi SQL Server Data Tools) oraz narzędzia niezintegrowanego (programu SQL Server Management Studio). |
| [Zarządzanie zasobami danych](#manage-data-assets) |Ta procedura dotyczy konfigurowania zabezpieczeń zasobów danych. Usługa Data Catalog nie zapewnia użytkownikom dostępu do danych. Zarządzanie dostępem do danych należy do właściciela źródła danych. <br/><br/> Usługa Data Catalog umożliwia odnajdywanie źródeł danych zarejestrowanych w wykazie i wyświetlanie **metadanych** powiązanych z tymi źródłami. W niektórych sytuacjach może jednak wystąpić konieczność udostępnienia źródeł danych tylko konkretnym użytkownikom lub członkom określonych grup. Można wtedy skonfigurować usługę Data Catalog pod kątem przejęcia praw własności do zasobów danych zarejestrowanych w wykazie i umożliwienia jej sterowania dostępem do posiadanych zasobów. |
| [Usuwanie zasobów danych](#remove-data-assets) |Ta procedura obejmuje usuwanie zasobów danych z wykazu danych. |

## <a name="tutorial-prerequisites"></a>Wymagania wstępne dotyczące samouczka
### <a name="azure-subscription"></a>Subskrypcja platformy Azure
Aby skonfigurować usługę Azure Data Catalog, musisz być właścicielem lub współwłaścicielem subskrypcji platformy Azure.

Subskrypcje platformy Azure umożliwiają konfigurowanie dostępu do zasobów usług w chmurze, takich jak usługa Azure Data Catalog. Subskrypcje te ułatwiają również zarządzanie raportowaniem i rozliczaniem użycia zasobów oraz regulowaniem płatności za to użycie. Poszczególne subskrypcje mogą mieć różne ustawienia rozliczeń i płatności, co pozwala na korzystanie z wielu subskrypcji i planów dostosowanych do potrzeb konkretnych działów, projektów, biur regionalnych itp. Z każdą usługą w chmurze jest powiązana subskrypcja. Musisz mieć subskrypcję, aby móc rozpocząć konfigurowanie usługi Azure Data Catalog. Aby dowiedzieć się więcej, zobacz artykuł [Manage accounts, subscriptions, and administrative roles](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) (Zarządzanie kontami, subskrypcjami i rolami administracyjnymi).

Jeśli nie masz subskrypcji, możesz utworzyć konto bezpłatnej wersji próbnej w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

### <a name="azure-active-directory"></a>Usługa Azure Active Directory
Aby skonfigurować usługę Azure Data Catalog, musisz zalogować się za pomocą konta użytkownika usługi Azure Active Directory (Azure AD). Użytkownik ten musi być właścicielem lub współwłaścicielem subskrypcji platformy Azure.  

Usługa Azure AD umożliwia firmom łatwe zarządzanie tożsamościami i dostępem, zarówno w chmurze, jak i lokalnie. Za pomocą jednego konta służbowego użytkownicy mogą logować się do dowolnej aplikacji internetowej, działającej w chmurze lub lokalnie. Uwierzytelnianie logowania w usłudze Azure Data Catalog odbywa się za pośrednictwem usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Co to jest usługa Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md).

### <a name="azure-active-directory-policy-configuration"></a>Konfiguracja zasad usługi Azure Active Directory
W niektórych sytuacjach po zalogowaniu się w portalu usługi Azure Data Catalog przy próbie logowania się za pomocą narzędzia do rejestracji źródła danych występuje komunikat o błędzie, który uniemożliwia logowanie. Może się to zdarzyć zarówno podczas nawiązywania połączenia w sieci firmowej, jak i spoza niej.

Narzędzie rejestracji używa *uwierzytelniania formularzy* do weryfikowania logowania użytkowników w usłudze Azure Active Directory. Aby logowanie się powiodło, administrator usługi Azure Active Directory musi włączyć uwierzytelnianie formularzy za pomocą *globalnych zasad uwierzytelniania*.

Pozwoli to na włączenie uwierzytelniania oddzielnie dla połączeń intranetowych i ekstranetowych, jak pokazano na poniższej ilustracji. Błędy logowania mogą występować, jeśli nie włączono uwierzytelniania formularzy dla sieci, w której jest nawiązywane połączenie.

 ![Globalne zasady uwierzytelniania usługi Azure Active Directory](./media/data-catalog-prerequisites/global-auth-policy.png)

Aby uzyskać więcej informacji, zobacz artykuł [Konfigurowanie zasad uwierzytelniania](https://technet.microsoft.com/library/dn486781.aspx).

## <a name="provision-data-catalog"></a>Aprowizowanie wykazu danych
W ramach organizacji — domeny usługi Azure Active Directory — można aprowizować tylko jeden wykaz danych. W związku z tym jeśli któryś właściciel lub współwłaściciel subskrypcji platformy Azure, który należy do tej domeny usługi Azure Active Directory, już utworzył wykaz, nie będziesz w stanie utworzyć dodatkowego wykazu, nawet jeśli masz kilka subskrypcji platformy Azure. Aby zobaczyć, czy w domenie usługi Azure Active Directory został utworzony wykaz danych, przejdź do [strony głównej usługi Azure Data Catalog](http://azuredatacatalog.com) i sprawdź, czy wykaz jest widoczny. Jeśli wykaz został już utworzony, pomiń poniższą procedurę i przejdź do następnej sekcji.    

1. Przejdź do [strony usługi Data Catalog](https://azure.microsoft.com/services/data-catalog) i kliknij pozycję **Rozpocznij**.
   
    ![Usługa Azure Data Catalog — marketingowa strona docelowa](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)
2. Zaloguj się przy użyciu konta użytkownika, który jest właścicielem lub współwłaścicielem subskrypcji platformy Azure. Po zalogowaniu zostanie wyświetlona poniższa strona.
   
    ![Usługa Azure Data Catalog — aprowizowanie wykazu danych](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)
3. Określ **nazwę** i **lokalizację** wykazu danych oraz **subskrypcję**, której chcesz używać.
4. Rozwiń sekcję **Cennik** i wybierz **wersję** usługi Azure Data Catalog (Bezpłatna lub Standardowa).
    ![Usługa Azure Data Catalog — wybieranie wersji](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)
5. Rozwiń sekcję **Użytkownicy wykazu** i kliknij pozycję **Dodaj**, aby dodać użytkowników wykazu danych. Twoje członkostwo w tej grupie jest tworzone automatycznie.
    ![Usługa Azure Data Catalog — użytkownicy](media/data-catalog-get-started/data-catalog-add-catalog-user.png)
6. Rozwiń sekcję **Administratorzy wykazu** i kliknij pozycję **Dodaj**, aby dodać administratorów wykazu danych. Twoje członkostwo w tej grupie jest tworzone automatycznie.
    ![Usługa Azure Data Catalog — administratorzy](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)
7. Kliknij pozycję **Utwórz wykaz**, aby utworzyć wykaz danych dla swojej organizacji. Po utworzeniu wykazu danych zostanie wyświetlona jego strona główna.
    ![Usługa Azure Data Catalog — utworzony wykaz](media/data-catalog-get-started/data-catalog-created.png)    

### <a name="find-a-data-catalog-in-the-azure-portal"></a>Znajdowanie wykazu danych w portalu Azure
1. Na osobnej karcie lub w osobnym oknie przeglądarki sieci Web przejdź do [portalu Azure](https://portal.azure.com) i zaloguj się przy użyciu tego samego konta, którego użyto do utworzenia wykazu danych w poprzednim kroku.
2. Wybierz pozycję **Przeglądaj**, a następnie kliknij pozycję **Wykaz danych**.
   
    ![Usługa Azure Data Catalog — przeglądanie portalu Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png) Zobaczysz utworzony wykaz danych.
   
    ![Usługa Azure Data Catalog — wykaz widoczny na liście](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)
3. Kliknij utworzony wykaz. W portalu pojawi się blok **Wykaz danych**.
   
   ![Usługa Azure Data Catalog — blok w portalu ](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)
4. Właściwości wykazu danych można wyświetlać i aktualizować. Możesz na przykład kliknąć pozycję **Warstwa cenowa** i zmienić wersję.
   
    ![Usługa Azure Data Catalog — warstwa cenowa](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

### <a name="adventure-works-sample-database"></a>Przykładowa baza danych firmy Adventure Works
W tym samouczku na potrzeby aparatu bazy danych programu SQL Server zostaną zarejestrowane zasoby danych (tabele) z przykładowej bazy danych AdventureWorks2014. Możesz jednak użyć dowolnego obsługiwanego źródła danych, jeśli wolisz pracować z danymi, które są Ci znane i dotyczą Twojej roli. Aby uzyskać listę obsługiwanych źródeł danych, zobacz [Supported data sources](data-catalog-dsr.md) (Obsługiwane źródła danych).

### <a name="install-the-adventure-works-2014-oltp-database"></a>Instalowanie bazy danych OLTP Adventure Works 2014
Baza danych firmy Adventure Works obsługuje standardowe scenariusze przetwarzania transakcji online dla fikcyjnego producenta rowerów (firmy Adventure Works Cycles), które obejmują produkty, sprzedaż i zakup. W tym samouczku informacje o produktach zostaną zarejestrowane w usłudze Azure Data Catalog.

Aby zainstalować przykładową bazę danych firmy Adventure Works:

1. Pobierz plik [Adventure Works 2014 Full Database Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661) z witryny CodePlex.
2. Aby przywrócić bazę danych na swoim komputerze, postępuj zgodnie z instrukcjami zawartymi w artykule [Restore a Database Backup by using SQL Server Management Studio](https://msdn.microsoft.com/library/ms177429.aspx) (Przywracanie kopii zapasowej bazy danych przy użyciu programu SQL Server Management Studio) lub wykonaj następujące czynności:
   1. Otwórz program SQL Server Management Studio i połącz się z aparatem bazy danych programu SQL Server.
   2. Kliknij prawym przyciskiem myszy pozycję **Bazy danych** i kliknij polecenie **Przywróć bazę danych**.
   3. W obszarze **Przywracanie bazy danych** kliknij opcję **Urządzenie** w sekcji **Źródło** i kliknij przycisk **Przeglądaj**.
   4. W obszarze **Wybieranie urządzeń kopii zapasowej** kliknij pozycję **Dodaj**.
   5. Przejdź do folderu, w którym znajduje się plik **AdventureWorks2014.bak**. Wybierz ten plik i kliknij przycisk **OK**, aby zamknąć okno dialogowe **Znajdowanie pliku kopii zapasowej**.
   6. Kliknij przycisk **OK**, aby zamknąć okno dialogowe **Wybieranie urządzeń kopii zapasowej**.    
   7. Kliknij przycisk **OK**, aby zamknąć okno dialogowe **Przywracanie bazy danych**.

Za pomocą usługi Azure Data Catalog możesz teraz zarejestrować zasoby danych z przykładowej bazy danych firmy Adventure Works.

## <a name="register-data-assets"></a>Rejestrowanie zasobów danych
W tym ćwiczeniu zasoby danych z bazy danych firmy Adventure Works zostaną zarejestrowane w wykazie za pomocą narzędzia rejestracji. Rejestracja to proces wyodrębniania kluczowych metadanych strukturalnych, takich jak nazwy, typy i lokalizacje, ze źródła danych i zasobów, które się w nim znajdują, oraz kopiowania tych metadanych do wykazu. Źródło danych i zasoby danych pozostają tam, gdzie się znajdowały, ale metadane są używane przez wykaz, aby można było je łatwiej odnaleźć i zrozumieć.

### <a name="register-a-data-source"></a>Rejestrowanie źródła danych
1. Przejdź do [strony głównej usługi Azure Data Catalog](http://azuredatacatalog.com) i kliknij pozycję **Publikuj dane**.
   
   ![Usługa Azure Data Catalog — przycisk Publikuj dane](media/data-catalog-get-started/data-catalog-publish-data.png)
2. Kliknij pozycję **Uruchom aplikację**, aby pobrać, zainstalować i uruchomić narzędzie rejestracji na komputerze.
   
   ![Usługa Azure Data Catalog — przycisk Uruchom](media/data-catalog-get-started/data-catalog-launch-application.png)
3. Na stronie **powitalnej** kliknij pozycję **Zaloguj**, a następnie wprowadź swoje poświadczenia.     
   
    ![Usługa Azure Data Catalog — strona powitalna](media/data-catalog-get-started/data-catalog-welcome-dialog.png)
4. Na stronie **Microsoft Azure Data Catalog** kliknij kolejno pozycje **SQL Server** i **Dalej**.
   
    ![Usługa Azure Data Catalog — źródła danych](media/data-catalog-get-started/data-catalog-data-sources.png)
5. Wprowadź właściwości połączenia programu SQL Server dla bazy danych **AdventureWorks2014** (zobacz poniższy przykład), a następnie kliknij pozycję **POŁĄCZ**.
   
   ![Usługa Azure Data Catalog — ustawienia połączenia programu SQL Server](media/data-catalog-get-started/data-catalog-sql-server-connection.png)
6. Zarejestruj metadane zasobów danych. W tym przykładzie należy zarejestrować obiekty **Production/Product** z przestrzeni nazw Production bazy danych Adventure Works.
   
   1. W drzewie **Hierarchia serwera** rozwiń węzeł **AdventureWorks2014** i kliknij pozycję **Production**.
   2. Naciśnij klawisz Ctrl i kliknij pozycje **Product**, **ProductCategory**, **ProductDescription** i **ProductPhoto**.
   3. Kliknij **strzałkę przeniesienia** (**>**). Spowoduje to przeniesienie wszystkich wybranych obiektów na listę **Obiekty do zarejestrowania**.
      
      ![Samouczek dotyczący usługi Azure Data Catalog — przeglądanie i wybieranie obiektów](media/data-catalog-get-started/data-catalog-server-hierarchy.png)
   4. Wybierz pozycję **Dołącz podgląd**, aby dołączyć podgląd danych w postaci migawki. Migawka jest kopiowana do wykazu i zawiera maksymalnie 20 rekordów z każdej tabeli.
   5. Wybierz pozycję **Dołącz profil danych**, aby dołączyć migawkę statystyk obiektów dla profilu danych (na przykład liczbę wierszy czy minimalną, maksymalną i średnią wartość w kolumnie).
   6. W polu **Dodaj tagi** wprowadź wartość **adventure works, cycles**. Spowoduje to dodanie tagów wyszukiwania dla tych zasobów danych. Tagi to doskonały sposób na ułatwienie użytkownikom znalezienia zarejestrowanego źródła danych.
   7. Podaj imię i nazwisko **eksperta** w zakresie przeznaczenia tych danych (opcjonalnie).
      
      ![Samouczek dotyczący usługi Azure Data Catalog — obiekty do zarejestrowania](media/data-catalog-get-started/data-catalog-objects-register.png)
   8. Kliknij pozycję **ZAREJESTRUJ**. Wybrane obiekty zostaną zarejestrowane za pomocą usługi Azure Data Catalog. W tym ćwiczeniu są rejestrowane wybrane obiekty z bazy danych firmy Adventure Works. Za pomocą narzędzia rejestracji metadane są wyodrębniane z zasobów danych i kopiowane do usługi Azure Data Catalog. Dane pozostają w miejscu, w którym aktualnie się znajdują, i podlegają kontroli administratorów i zasadom obowiązującym w danym systemie.
      
      ![Usługa Azure Data Catalog — zarejestrowane obiekty](media/data-catalog-get-started/data-catalog-registered-objects.png)
   9. Aby wyświetlić zarejestrowane obiekty źródła danych, kliknij pozycję **Wyświetl portal**. Otwórz portal usługi Azure Data Catalog i sprawdź, czy w widoku siatki są widoczne wszystkie cztery tabele i baza danych.
      
      ![Obiekty w portalu usługi Azure Data Catalog ](media/data-catalog-get-started/data-catalog-view-portal.png)

W tym ćwiczeniu zarejestrowano obiekty z przykładowej bazy danych firmy Adventure Works, aby użytkownicy w organizacji mogli je łatwo odnaleźć. W następnym ćwiczeniu zostanie przedstawiony sposób odnajdowania zarejestrowanych zasobów danych.

## <a name="discover-data-assets"></a>Odnajdywanie zasobów danych
Odnajdywanie w usłudze Azure Data Catalog korzysta z dwóch głównych mechanizmów: wyszukiwania i filtrowania.

Wyszukiwanie zaprojektowano pod kątem intuicyjnej obsługi i zaawansowanych możliwości. Domyślnie wyszukiwane terminy są dopasowywane do wszystkich właściwości w wykazie, w tym adnotacji wprowadzonych przez użytkownika.

Filtrowanie służy jako uzupełnienie wyszukiwania. Wybierając określone cechy, takie jak eksperci, typ źródła danych, typu obiektu i tagi, można wyświetlić pasujące zasoby danych i ograniczyć wyniki wyszukiwania do tych zasobów.

Dzięki połączeniu wyszukiwania i filtrowania można szybko nawigować po źródłach danych zarejestrowanych w usłudze Azure Data Catalog w celu odnajdywania potrzebnych zasobów danych.

To ćwiczenie obejmuje korzystanie z portalu usługi Azure Data Catalog w celu odnalezienia zasobów danych zarejestrowanych w poprzednim ćwiczeniu. Aby uzyskać szczegółowe informacje o składni wyszukiwania, zobacz artykuł [Data Catalog Search syntax reference](https://msdn.microsoft.com/library/azure/mt267594.aspx) (Dokumentacja dotycząca składni wyszukiwania w usłudze Data Catalog).

Poniżej przedstawiono kilka przykładów dotyczących odnajdywania zasobów danych w wykazie.  

### <a name="discover-data-assets-with-basic-search"></a>Odnajdywanie zasobów danych przy użyciu wyszukiwania podstawowego
Wyszukiwanie podstawowe ułatwia przeszukiwanie wykazu za pomocą co najmniej jednego wyszukiwanego terminu. Wyniki obejmują wszystkie zasoby, których dowolne właściwości są zgodne z co najmniej jednym terminem.

1. Kliknij pozycję **Strona główna** w portalu usługi Azure Data Catalog. Jeśli przeglądarka sieci Web została zamknięta, przejdź do [strony głównej usługi Azure Data Catalog](https://www.azuredatacatalog.com).
2. W polu wyszukiwania wpisz `cycles` i naciśnij klawisz **ENTER**.
   
    ![Usługa Azure Data Catalog — podstawowe wyszukiwanie tekstowe](media/data-catalog-get-started/data-catalog-basic-text-search.png)
3. Upewnij się, że w wynikach są widoczne wszystkie cztery tabele i baza danych (AdventureWorks2014). Za pomocą przycisków na pasku narzędzi można przełączać się między **widokiem siatki** i **widokiem listy**, jak pokazano na poniższej ilustracji. Zwróć uwagę, że wyszukiwane słowo kluczowe jest wyróżnione w wynikach wyszukiwania, ponieważ opcja **Wyróżnienie** jest **WŁĄCZONA**. Dodatkowo można określić liczbę **wyników wyszukiwania wyświetlanych na stronie**.
   
    ![Usługa Azure Data Catalog — wyniki podstawowego wyszukiwania tekstowego](media/data-catalog-get-started/data-catalog-basic-text-search-results.png)
   
    Panel **Wyszukiwania** jest wyświetlany po lewej stronie, a panel **Właściwości** — po prawej stronie. W panelu **Wyszukiwania** można zmienić kryteria wyszukiwania i wyfiltrować wyniki. W panelu **Właściwości** są wyświetlane właściwości obiektu wybranego w siatce lub na liście.
4. Kliknij pozycję **Product** w wynikach wyszukiwania. Kliknij karty **Podgląd**, **Kolumny**, **Profil danych** i **Dokumentacja** lub kliknij strzałkę, aby rozwinąć dolne okienko.  
   
    ![Usługa Azure Data Catalog — dolne okienko](media/data-catalog-get-started/data-catalog-data-asset-preview.png)
   
    Na karcie **Podgląd** zostanie wyświetlony podgląd danych z tabeli **Product**.  
5. Kliknij kartę **Kolumny**, aby wyświetlić szczegóły kolumn (takie jak **nazwa** i **typ danych**) w zasobie danych.
6. Kliknij kartę **Profil danych**, aby wyświetlić profil danych (na przykład liczbę wierszy, rozmiar danych lub wartość minimalną w kolumnie) w zasobie danych.
7. Za pomocą sekcji **Filtry** znajdującej się po lewej stronie można wyfiltrować wyniki. Na przykład kliknięcie opcji **Tabela** w obszarze **Typ obiektu** spowoduje wyświetlenie tylko czterech tabel, bez bazy danych.
   
    ![Usługa Azure Data Catalog — filtrowanie wyników wyszukiwania](media/data-catalog-get-started/data-catalog-filter-search-results.png)

### <a name="discover-data-assets-with-property-scoping"></a>Odnajdywanie zasobów danych za pomocą wyznaczania zakresu właściwości
Wyznaczanie zakresu właściwości ułatwia odnajdywanie zasobów danych, gdy wyszukiwany termin zostanie dopasowany do określonej właściwości.

1. Wyczyść filtr **Tabela** w obszarze **Typ obiektu** w sekcji **Filtry**.  
2. W polu wyszukiwania wpisz `tags:cycles` i naciśnij klawisz **ENTER**. Aby uzyskać listę wszystkich właściwości, których można użyć do przeszukiwania wykazu danych, zobacz artykuł [Data Catalog Search syntax reference](https://msdn.microsoft.com/library/azure/mt267594.aspx) (Dokumentacja dotycząca składni wyszukiwania w usłudze Data Catalog).
3. Upewnij się, że w wynikach są widoczne wszystkie cztery tabele i baza danych (AdventureWorks2014).  
   
    ![Usługa Data Catalog — wyniki wyszukiwania uzyskane z użyciem wyznaczania zakresu właściwości](media/data-catalog-get-started/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>Zapisywanie wyszukiwania
1. W okienku **Wyszukiwania** w sekcji **Bieżące wyszukiwanie** wprowadź nazwę wyszukiwania i kliknij pozycję **Zapisz**.
   
    ![Usługa Azure Data Catalog — zapisywanie wyszukiwania](media/data-catalog-get-started/data-catalog-save-search.png)
2. Upewnij się, że zapisane wyszukiwanie jest wyświetlane w sekcji **Zapisane wyszukiwania**.
   
    ![Usługa Azure Data Catalog — zapisane wyszukiwania](media/data-catalog-get-started/data-catalog-saved-search.png)
3. Wybierz jedną z akcji, które można zastosować do zapisanego wyszukiwania (**Zmień nazwę**, **Usuń**, **Zapisz jako domyślne**).
   
    ![Usługa Azure Data Catalog — opcje dotyczące zapisanego wyszukiwania](media/data-catalog-get-started/data-catalog-saved-search-options.png)

### <a name="boolean-operators"></a>Operatory logiczne
Za pomocą operatorów logicznych można rozszerzyć lub zawęzić wyszukiwanie.

1. W polu wyszukiwania wpisz `tags:cycles AND objectType:table` i naciśnij klawisz **ENTER**.
2. Upewnij się, że w wynikach są wyświetlane tylko tabele, bez bazy danych.  
   
    ![Usługa Azure Data Catalog — zastosowanie operatora logicznego w wyszukiwaniu](media/data-catalog-get-started/data-catalog-search-boolean-operator.png)

### <a name="grouping-with-parentheses"></a>Grupowanie za pomocą nawiasów
Nawiasy umożliwiają grupowanie części zapytania w celu uzyskania izolacji logicznej, szczególnie w połączeniu z operatorami logicznymi.

1. W polu wyszukiwania wpisz `name:product AND (tags:cycles AND objectType:table)` i naciśnij klawisz **ENTER**.
2. Upewnij się, że wyniki wyszukiwania obejmują tylko wiersze z tabeli **Product**.
   
    ![Usługa Azure Data Catalog — wyszukiwanie przy użyciu grupowania](media/data-catalog-get-started/data-catalog-grouping-search.png)   

### <a name="comparison-operators"></a>Operatory porównania
Operatory porównania pozwalają używać innych porównań niż równość dla właściwości, które mają numeryczne i datowe typy danych.

1. W polu wyszukiwania wpisz `lastRegisteredTime:>"06/09/2016"`.
2. Wyczyść filtr **Tabela** w obszarze **Typ obiektu**.
3. Naciśnij klawisz **ENTER**.
4. Upewnij się, że w wynikach wyszukiwania są wyświetlane tabele **Product**, **ProductCategory**, **ProductDescription** i **ProductPhoto** oraz zarejestrowana baza danych AdventureWorks2014.
   
    ![Usługa Azure Data Catalog — wyniki wyszukiwania z użyciem porównania](media/data-catalog-get-started/data-catalog-comparison-operator-results.png)

Szczegółowe informacje dotyczące odnajdywania zasobów danych można znaleźć w artykule [How to discover data assets](data-catalog-how-to-discover.md) (Jak odnajdywać zasoby danych). Informacje na temat składni wyszukiwania zawiera artykuł [Data Catalog Search syntax reference](https://msdn.microsoft.com/library/azure/mt267594.aspx) (Dokumentacja dotycząca składni wyszukiwania w usłudze Data Catalog).

## <a name="annotate-data-assets"></a>Dodawanie adnotacji do zasobów danych
W tym ćwiczeniu portal usługi Azure Data Catalog zostanie użyty do dodawania adnotacji (na przykład opisów, tagów czy informacji dotyczących ekspertów) do zasobów danych, które zostały wcześniej zarejestrowane w wykazie. Adnotacje uzupełniają i rozszerzają metadane strukturalne wyodrębnione ze źródła podczas rejestracji i znacznie ułatwiają odnajdywanie i zrozumienie zasobów danych.

W tym ćwiczeniu adnotacje zostaną dodane do pojedynczego zasobu danych (tabeli ProductPhoto). Do zasobu danych ProductPhoto zostanie dodana przyjazna nazwa oraz opis.  

1. Przejdź do [strony głównej usługi Azure Data Catalog](https://www.azuredatacatalog.com) i wpisz `tags:cycles` w polu wyszukiwania, aby znaleźć zarejestrowane zasoby danych.  
2. Kliknij pozycję **ProductPhoto** w wynikach wyszukiwania.  
3. W polu **Przyjazna nazwa** wpisz **Obrazy produktów**, a w polu **Opis** wpisz **Zdjęcia produktów do materiałów marketingowych**.
   
    ![Usługa Azure Data Catalog — opis tabeli ProductPhoto](media/data-catalog-get-started/data-catalog-productphoto-description.png)
   
    Pole **Opis** ułatwia innym osobom odnajdywanie wybranego zasobu danych i zrozumienie, dlaczego i w jaki sposób należy go używać. Możesz również dodać więcej tagów i wyświetlić kolumny. Teraz możesz spróbować przeprowadzić wyszukiwanie i filtrowanie, aby odnaleźć zasoby danych przy użyciu metadanych opisowych, które zostały dodane do wykazu.

Na tej stronie możesz również:

* Dodawać ekspertów dla zasobu danych. Kliknij pozycję **Dodaj** w obszarze **Eksperci**.
* Dodawać tagi na poziomie zestawu danych. Kliknij pozycję **Dodaj** w obszarze **Tagi**. Możesz dodawać tagi użytkownika lub tagi słownika. Wersja Standard Edition usługi Data Catalog zawiera słownik biznesowy, który umożliwia administratorom wykazu definiowanie centralnej taksonomii biznesowej. Użytkownicy wykazu mogą następnie dodać adnotacje do zasobów danych za pomocą terminów ze słownika. Aby uzyskać więcej informacji, zobacz [How to set up the Business Glossary for Governed Tagging](data-catalog-how-to-business-glossary.md) (Jak skonfigurować słownik biznesowy na potrzeby znakowania zarządzanego).
* Dodawać tagi na poziomie kolumny. Kliknij pozycję **Dodaj** w obszarze **Tagi** dla kolumny, do której chcesz dodać adnotację.
* Dodawać opisy na poziomie kolumny. W polu **Opis** wprowadź opis kolumny. Możesz również wyświetlić metadane opisu wyodrębnione ze źródła danych.
* Dodawać informacje dotyczące **żądania dostępu**, które ułatwiają użytkownikom uzyskanie dostępu do zasobu danych.
  
    ![Usługa Azure Data Catalog — dodawanie tagów, opisów](media/data-catalog-get-started/data-catalog-add-tags-experts-descriptions.png)
* Na karcie **Dokumentacja** podaj informacje dotyczące dokumentacji zasobu danych. Dzięki funkcji dokumentacji usługi Azure Data Catalog możesz użyć wykazu danych jako repozytorium zawartości i stworzyć kompletny opis zasobów danych.
  
    ![Usługa Azure Data Catalog — karta Dokumentacja](media/data-catalog-get-started/data-catalog-documentation.png)

Ponadto możesz dodawać adnotacje do wielu zasobów danych. Na przykład możesz zaznaczyć wszystkie zarejestrowane zasoby danych i określić dla nich eksperta.

![Usługa Azure Data Catalog — dodawanie adnotacji do wielu zasobów danych](media/data-catalog-get-started/data-catalog-multi-select-annotate.png)

Usługa Azure Data Catalog obsługuje dodawanie adnotacji przez społeczność. Wszyscy użytkownicy usługi Azure Data Catalog mogą dodawać tagi (użytkownika lub słownika), opisy i inne metadane, dzięki czemu każdy użytkownik z perspektywą zasobu danych i jego użycia może przechwycić taką perspektywę i udostępnić ją innym użytkownikom.

Szczegółowe informacje dotyczące dodawania adnotacji do zasobów danych można znaleźć w artykule [How to annotate data assets](data-catalog-how-to-annotate.md) (Jak dodawać adnotacje do zasobów danych).

## <a name="connect-to-data-assets"></a>Łączenie z zasobami danych
To ćwiczenie obejmuje otwieranie zasobów danych za pomocą zintegrowanego narzędzia klienckiego (programu Excel) oraz narzędzia niezintegrowanego (programu SQL Server Management Studio) przy użyciu informacji o połączeniu.

> [!NOTE]
> Należy pamiętać, że usługa Azure Data Catalog nie zapewnia dostępu do rzeczywistego źródła danych, a tylko ułatwia użytkownikom odnalezienie źródła danych i zrozumienie jego przeznaczenia. Podczas nawiązywania połączenia ze źródłem danych wybrana aplikacja kliencka używa poświadczeń systemu Windows lub w razie potrzeby wyświetla monit o ich podanie. Jeśli nie masz udzielonego wcześniej dostępu do źródła danych, musisz go uzyskać, aby nawiązać połączenie.
> 
> 

### <a name="connect-to-a-data-asset-from-excel"></a>Nawiązywanie połączenia z zasobem danych przy użyciu programu Excel
1. Wybierz pozycję **Product** w wynikach wyszukiwania. Kliknij pozycję **Otwórz w** na pasku narzędzi, a następnie kliknij pozycję **Excel**.
   
    ![Usługa Azure Data Catalog — nawiązywanie połączenia z zasobem danych](media/data-catalog-get-started/data-catalog-connect1.png)
2. Kliknij pozycję **Otwórz** w oknie podręcznym pobierania. Wygląd elementów interfejsu zależy od przeglądarki.
   
    ![Usługa Azure Data Catalog — pobrany plik połączenia programu Excel](media/data-catalog-get-started/data-catalog-download-open.png)
3. W oknie **Powiadomienie o zabezpieczeniach programu Microsoft Excel** kliknij pozycję **Włącz**.
   
    ![Usługa Azure Data Catalog — okno podręczne zabezpieczeń programu Excel](media/data-catalog-get-started/data-catalog-excel-security-popup.png)
4. Pozostaw niezmienione wartości domyślne w oknie dialogowym **Importowanie danych** i kliknij przycisk **OK**.
   
    ![Usługa Azure Data Catalog — importowanie danych w programie Excel](media/data-catalog-get-started/data-catalog-excel-import-data.png)
5. Wyświetl źródło danych w programie Excel.
   
    ![Usługa Azure Data Catalog — tabela produktów w programie Excel](media/data-catalog-get-started/data-catalog-connect2.png)

W tym ćwiczeniu zostało nawiązane połączenie z zasobami danych odnalezionymi za pomocą usługi Azure Data Catalog. Portal usługi Azure Data Catalog umożliwia bezpośrednie nawiązywanie połączenia przy użyciu aplikacji klienckich zintegrowanych z menu **Otwórz w**. Możesz również nawiązywać połączenia za pomocą dowolnej wybranej aplikacji przy użyciu informacji o lokalizacji połączenia zawartych w metadanych zasobów. Na przykład za pomocą programu SQL Server Management Studio możesz połączyć się z bazą danych AdventureWorks2014, aby uzyskać dostęp do zasobów danych zarejestrowanych w tym samouczku.

1. Otwórz program **SQL Server Management Studio**.
2. W oknie dialogowym **Połączenie z serwerem** wprowadź nazwę serwera z okienka **Właściwości** w portalu usługi Azure Data Catalog.
3. Wybierz właściwą metodę uwierzytelniania i wpisz odpowiednie poświadczenia, aby uzyskać dostęp do zasobu danych. Jeśli nie masz dostępu, skorzystaj z informacji zawartych w polu **Żądanie dostępu**, aby go uzyskać.
   
    ![Usługa Azure Data Catalog — żądanie dostępu](media/data-catalog-get-started/data-catalog-request-access.png)

Kliknij pozycję **Wyświetl parametry połączenia**, aby wyświetlić parametry połączeń ADF.NET, ODBC i OLEDB oraz skopiować je do schowka w celu użycia ich w aplikacji.

## <a name="manage-data-assets"></a>Zarządzanie zasobami danych
Ta procedura obejmuje konfigurowanie zabezpieczeń zasobów danych. Usługa Data Catalog nie zapewnia użytkownikom dostępu do danych. Zarządzanie dostępem do danych należy do właściciela źródła danych.

Usługa Data Catalog umożliwia odnajdywanie źródeł danych zarejestrowanych w wykazie i wyświetlanie metadanych powiązanych z tymi źródłami. W niektórych sytuacjach może jednak wystąpić konieczność udostępnienia źródeł danych tylko konkretnym użytkownikom lub członkom określonych grup. Można wtedy skonfigurować usługę Data Catalog pod kątem przejęcia praw własności do zasobów danych zarejestrowanych w wykazie i umożliwienia jej sterowania dostępem do posiadanych zasobów.

> [!NOTE]
> Funkcje zarządzania opisane w tym ćwiczeniu są dostępne tylko w wersji Standard Edition usługi Azure Data Catalog, a nie w wersji Free Edition.
> W usłudze Azure Data Catalog można przejmować na własność zasoby danych, dodawać współwłaścicieli zasobów danych i ustawiać widoczność zasobów danych.
> 
> 

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>Przejmowanie własności do zasobów danych i ograniczanie ich widoczności
1. Przejdź do [strony głównej usługi Azure Data Catalog](https://www.azuredatacatalog.com). W polu **Wyszukiwanie** wpisz `tags:cycles` i naciśnij klawisz **ENTER**.
2. Kliknij element na liście wyników, a następnie kliknij pozycję **Przejmij na własność** na pasku narzędzi.
3. W sekcji **Zarządzanie** na panelu **Właściwości** kliknij pozycję **Przejmij na własność**.
   
    ![Usługa Azure Data Catalog — przejmowanie własności](media/data-catalog-get-started/data-catalog-take-ownership.png)
4. Aby ograniczyć widoczność, wybierz pozycję **Właściciele i ci użytkownicy** w sekcji **Widoczność** i kliknij pozycję **Dodaj**. Wprowadź adresy e-mail użytkowników w polu tekstowym i naciśnij klawisz **ENTER**.
   
    ![Usługa Azure Data Catalog — ograniczanie dostępu](media/data-catalog-get-started/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>Usuwanie zasobów danych
W tym ćwiczeniu portal usługi Azure Data Catalog zostanie użyty do usunięcia danych podglądu z zarejestrowanych zasobów danych i usunięcia zasobów danych z wykazu.

W usłudze Azure Data Catalog można usuwać pojedyncze zasoby lub wiele zasobów.

1. Przejdź do [strony głównej usługi Azure Data Catalog](https://www.azuredatacatalog.com).
2. W polu **Wyszukiwanie** wpisz `tags:cycles` i naciśnij klawisz **ENTER**.
3. Wybierz element na liście wyników i kliknij pozycję **Usuń** na pasku narzędzi, jak pokazano na poniższej ilustracji:
   
    ![Usługa Azure Data Catalog — usuwanie elementu siatki](media/data-catalog-get-started/data-catalog-delete-grid-item.png)
   
    W widoku listy pole wyboru znajduje się na lewo od elementu, jak pokazano na poniższej ilustracji:
   
    ![Usługa Azure Data Catalog — usuwanie elementu listy](media/data-catalog-get-started/data-catalog-delete-list-item.png)
   
    Można również wybierać i usuwać kilka zasobów danych, jak pokazano na poniższej ilustracji:
   
    ![Usługa Azure Data Catalog — usuwanie kilku zasobów danych](media/data-catalog-get-started/data-catalog-delete-assets.png)

> [!NOTE]
> Domyślnym zachowaniem katalogu jest umożliwianie każdemu użytkownikowi rejestrowania dowolnego źródła danych oraz usuwania wszystkich zarejestrowanych zasobów danych. Funkcje zarządzania dostępne w wersji Standard Edition usługi Azure Data Catalog dodatkowo umożliwiają przejmowania własności do zasobów i ograniczanie grona użytkowników, którzy mogą odnajdywać i usuwać zasoby.
> 
> 

## <a name="summary"></a>Podsumowanie
W tym samouczku zostały przedstawione podstawowe funkcje usługi Azure Data Catalog, w tym rejestrowanie, dodawanie adnotacji, odnajdywanie i zarządzanie firmowymi zasobami danych. Po zakończeniu tego samouczka nadszedł czas na rozpoczęcie pracy. Możesz rozpocząć dzisiaj od zarejestrowania źródeł danych, na którym pracujesz Ty i Twój zespół, oraz od zaproszenia współpracowników do korzystania z wykazu.

## <a name="references"></a>Dokumentacja
* [Jak rejestrować zasoby danych](data-catalog-how-to-register.md)
* [Jak odnajdywać zasoby danych](data-catalog-how-to-discover.md)
* [Jak dodawać adnotacje do zasobów danych](data-catalog-how-to-annotate.md)
* [Jak dokumentować zasoby danych](data-catalog-how-to-documentation.md)
* [Jak łączyć się z zasobami danych](data-catalog-how-to-connect.md)
* [Jak zarządzać zasobami danych](data-catalog-how-to-manage.md)

