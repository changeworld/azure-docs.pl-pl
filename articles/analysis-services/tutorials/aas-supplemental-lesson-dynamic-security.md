---
title: 'Samouczek Azure Analysis Services: lekcja uzupełniająca — zabezpieczenia dynamiczne | Microsoft Docs'
description: Opisuje sposób korzystania z zabezpieczeń dynamicznych przy użyciu filtrów wierszy w samouczku usług Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2a9a6c36863f7e30c803eecfe793c879d72eb249
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596944"
---
# <a name="supplemental-lesson---dynamic-security"></a>Lekcja uzupełniająca — zabezpieczenia dynamiczne

W tej lekcji uzupełniającej utworzysz dodatkową rolę w celu zaimplementowania zabezpieczeń dynamicznych. Zabezpieczenia dynamiczne zapewniają zabezpieczenia na poziomie wierszy w oparciu o nazwę użytkownika lub identyfikator logowania aktualnie zalogowanego użytkownika. 
  
Aby zaimplementować zabezpieczenia dynamiczne, dodaj do modelu tabelę zawierającą nazwy użytkowników, którzy mogą połączyć się z modelem oraz przeglądać obiekty i dane modelu. Model tworzony przy użyciu tego samouczka znajduje się w kontekście Adventure Works, jednak w celu ukończenia tej lekcji należy dodać tabelę zawierającą użytkowników z własnej domeny. Nie jest wymagane podanie haseł dla dodawanych nazw użytkowników. Aby utworzyć tabelę EmployeeSecurity zawierającą niewielką próbkę użytkowników z własnej domeny, należy za pomocą funkcji Wklej wkleić dane pracowników z arkusza kalkulacyjnego programu Excel. W scenariuszu rzeczywistym tabela zawierająca nazwy użytkowników będzie zazwyczaj tabelą z istniejącej bazy danych użytej jako źródło danych, na przykład rzeczywistą tabelą DimEmployee.  
  
Aby zaimplementować zabezpieczenia dynamiczne, należy użyć dwóch funkcji języka DAX: [USERNAME (DAX)](http://msdn.microsoft.com/22dddc4b-1648-4c89-8c93-f1151162b93f) i [LOOKUPVALUE (DAX)](http://msdn.microsoft.com/73a51c4d-131c-4c33-a139-b1342d10caab). Te funkcje, zastosowane w formule filtra wierszy, są definiowane w nowej roli. Za pomocą funkcji LOOKUPVALUE formuła określa wartość z tabeli EmployeeSecurity. Następnie formuła przekazuje tę wartość funkcji USERNAME, która określa nazwę użytkownika dla zalogowanego użytkownika należącego do tej roli. Użytkownik może następnie przeglądać tylko dane określone przez filtry wierszy danej roli. W tym scenariuszu określisz, że pracownicy działu sprzedaży mogą przeglądać tylko dane sprzedaży internetowej dotyczące regionów sprzedaży, których są członkami.  
  
Zadania, które są unikatowe dla przedstawionego scenariusza modelu tabelarycznego Adventure Works, ale nie zawsze będą miały zastosowanie w przypadku rzeczywistych scenariuszy, zostały odpowiednio oznaczone. Każde zadanie zawiera dodatkowe informacje opisujące jego cel.  
  
Szacowany czas trwania lekcji: **30 minut**  
  
## <a name="prerequisites"></a>Wymagania wstępne  
Ta lekcja uzupełniająca stanowi część samouczka modelowania tabelarycznego, który należy wykonać w podanej kolejności. Przed przystąpieniem do wykonywania zadań w tej lekcji uzupełniającej należy ukończyć wszystkie poprzednie lekcje.  
  
## <a name="add-the-dimsalesterritory-table-to-the-aw-internet-sales-tabular-model-project"></a>Dodawanie tabeli DimSalesTerritory do projektu modelu tabelarycznego sprzedaży internetowej AW  
Aby zaimplementować zabezpieczenia dynamiczne w tym scenariuszu Adventure Works, do modelu należy dodać dwie dodatkowe tabele. Pierwszą z nich jest tabela DimSalesTerritory (jako tabela SalesTerritory) pochodząca z tej samej bazy danych AdventureWorksDW. Później do tabeli SalesTerritory można zastosować filtr wierszy, który definiuje określone dane możliwe do przeglądania przez zalogowanego użytkownika.  
  
#### <a name="to-add-the-dimsalesterritory-table"></a>Dodawanie tabeli DimSalesTerritory  
  
1.  W Eksploratorze modeli tabelarycznych wybierz pozycję **Źródła danych**, kliknij prawym przyciskiem myszy połączenie, a następnie kliknij pozycję **Importuj nowe tabele**.  

    Jeśli zostanie wyświetlone okno dialogowe Poświadczenia personifikacji, wpisz poświadczenia personifikacji używane w lekcji 2 „Dodawanie danych”.
  
2.  W oknie Nawigator wybierz tabelę **DimSalesTerritory**, a następnie kliknij przycisk **OK**.    
  
3.  W edytorze zapytań kliknij zapytanie **DimSalesTerritory**, a następnie usuń kolumnę **SalesTerritoryAlternateKey**.  
  
7.  Kliknij przycisk **Importuj**.  
  
    Do obszaru roboczego modelu zostanie dodana nowa tabela. Następnie obiekty i dane z tabeli źródłowej DimSalesTerritory są importowane do modelu tabelarycznego sprzedaży internetowej AW.  
  
9. Po pomyślnym zaimportowaniu tabeli kliknij przycisk **Zamknij**.  

## <a name="add-a-table-with-user-name-data"></a>Dodawanie tabeli z danymi dotyczącymi nazw użytkowników  
Tabela DimEmployee w przykładowej bazie danych AdventureWorksDW zawiera użytkowników z domeny AdventureWorks. Te nazwy użytkowników nie istnieją w Twoim własnym środowisku. Należy utworzyć w modelu tabelę zawierającą małą próbkę (co najmniej trzy elementy) rzeczywistych użytkowników z Twojej organizacji. Następnie należy dodać tych użytkowników jako członków do nowej roli. Nie ma potrzeby podawania haseł dla przykładowych nazw użytkowników, ale wymagane są nazwy rzeczywistych użytkowników systemu Windows z Twojej domeny.  
  
#### <a name="to-add-an-employeesecurity-table"></a>Dodawanie tabeli EmployeeSecurity  
  
1.  Otwórz program Microsoft Excel i utwórz arkusz.  
  
2.  Skopiuj następującą tabelę z uwzględnieniem wiersza nagłówka, a następnie wklej ją do arkusza.  

    ```
      |EmployeeId|SalesTerritoryId|FirstName|LastName|LoginId|  
      |---------------|----------------------|--------------|-------------|------------|  
      |1|2|<user first name>|<user last name>|\<domain\username>|  
      |1|3|<user first name>|<user last name>|\<domain\username>|  
      |2|4|<user first name>|<user last name>|\<domain\username>|  
      |3|5|<user first name>|<user last name>|\<domain\username>|  
    ```

3.  Zamień imię, nazwisko i domenę\nazwę użytkownika na imiona i nazwiska oraz identyfikatory logowania trzech użytkowników z Twojej organizacji. Umieść tego samego użytkownika w dwóch pierwszych wierszach (EmployeeId 1), wskazując przynależność tego użytkownika do więcej niż jednego regionu sprzedaży. Pozostaw pola EmployeeId i SalesTerritoryId bez zmian.  
  
4.  Zapisz arkusz jako **SampleEmployee**.  
  
5.  W arkuszu zaznacz wszystkie komórki zawierające dane pracowników, w tym również nagłówki, a następnie kliknij prawym przyciskiem myszy zaznaczone dane i kliknij pozycję **Kopiuj**.  
  
6.  W programie SSDT kliknij menu **Edycja**, a następnie kliknij pozycję **Wklej**.  
  
    Jeśli pozycja Wklej jest wyszarzona, w oknie projektanta modeli kliknij dowolną kolumnę w dowolnej tabeli i ponów próbę.  
  
7.  W oknie dialogowym **Podgląd wklejania** w polu **Nazwa tabeli** wpisz **EmployeeSecurity**.  
  
8.  W polu **Dane do wklejenia** sprawdź, czy dane obejmują wszystkie dane użytkownika i nagłówki z arkusza SampleEmployee.  
  
9. Sprawdź, czy pole **Użyj pierwszego wiersza jako nagłówków kolumn** jest zaznaczone, a następnie kliknij przycisk **OK**.  
  
    Zostanie utworzona nowa tabela o nazwie EmployeeSecurity, która zawiera dane pracowników skopiowane z arkusza SampleEmployee.  
  
## <a name="create-relationships-between-factinternetsales-dimgeography-and-dimsalesterritory-table"></a>Tworzenie relacji między tabelami FactInternetSales, DimGeography i DimSalesTerritory  
Tabele FactInternetSales, DimGeography i DimSalesTerritory zawierają tę samą kolumnę SalesTerritoryId. Kolumna SalesTerritoryId w tabeli DimSalesTerritory zawiera wartości identyfikatorów dla każdego regionu sprzedaży.  
  
#### <a name="to-create-relationships-between-the-factinternetsales-dimgeography-and-the-dimsalesterritory-table"></a>Tworzenie relacji między tabelami FactInternetSales, DimGeography i DimSalesTerritory  
  
1.  W widoku diagramu w tabeli **DimGeography** kliknij i przytrzymaj kolumnę **SalesTerritoryId**, przeciągnij kursor do kolumny **SalesTerritoryId** w tabeli **DimSalesTerritory**, a następnie zwolnij kursor.  
  
2.  W tabeli **FactInternetSales** kliknij i przytrzymaj kolumnę **SalesTerritoryId**, przeciągnij kursor do kolumny **SalesTerritoryId** w tabeli **DimSalesTerritory**, a następnie zwolnij kursor.  
  
    Należy zauważyć, że właściwość Aktywne dla tej relacji ma wartość Fałsz, co oznacza, że jest nieaktywna. Tabela FactInternetSales ma już inną aktywną relację.  
  
## <a name="hide-the-employeesecurity-table-from-client-applications"></a>Ukrywanie tabeli EmployeeSecurity w aplikacjach klienckich  
W tym zadaniu ukryjesz tabelę EmployeeSecurity, aby uniemożliwić jej wyświetlanie na liście pól aplikacji klienckiej. Pamiętaj, że ukrycie tabeli nie jest równoważne z jej zabezpieczeniem. Użytkownicy mogą nadal wyszukiwać dane w tabeli EmployeeSecurity, jeśli znają odpowiedni sposób. Aby zabezpieczyć dane tabeli EmployeeSecurity, uniemożliwiając użytkownikom wykonywanie zapytań dotyczących zawartych w niej danych, należy zastosować filtr, co jest opisane w późniejszym zadaniu.  
  
#### <a name="to-hide-the-employeesecurity-table-from-client-applications"></a>Ukrywanie tabeli EmployeeSecurity w aplikacjach klienckich  
  
-   W projektancie modeli, w widoku diagramu kliknij prawym przyciskiem myszy nagłówek tabeli **Employee**, a następnie kliknij pozycję **Ukryj przed narzędziami klienta**.  
  
## <a name="create-a-sales-employees-by-territory-user-role"></a>Tworzenie roli użytkownika Sales Employees by Territory  
W tym zadaniu utworzysz rolę użytkownika. Ta rola zawiera filtr wierszy, który definiuje wiersze w tabeli DimSalesTerritory widoczne dla użytkowników. Ten filtr jest następnie stosowany w kierunku relacji jeden-do-wielu w odniesieniu do wszystkich pozostałych tabel powiązanych z tabelą DimSalesTerritory. Zastosowany zostanie także filtr, który zabezpiecza całą tabelę EmployeeSecurity przed obsługą zapytań wykonywanych przez dowolnego użytkownika będącego członkiem roli.  
  
> [!NOTE]  
> Utworzona w tej lekcji rola Sales Employees by Territory (Pracownicy sprzedaży według regionu) ogranicza przeglądanie i wykonywanie zapytań przez członków wyłącznie do danych sprzedaży dotyczących regionu, do którego należą. Jeśli jako członek roli Sales Employees by Territory zostanie dodany użytkownik, który jest także członkiem roli utworzonej w [lekcji 11 „Tworzenie ról”](../tutorials/aas-lesson-11-create-roles.md), uzyskujemy kombinację uprawnień. W przypadku użytkownika będącego członkiem wielu ról następuje kumulacja uprawnień i filtrów wierszy zdefiniowanych dla każdej roli. Oznacza to, że użytkownik ma większe uprawnienia określone przez kombinację ról.  
  
#### <a name="to-create-a-sales-employees-by-territory-user-role"></a>Tworzenie roli użytkownika Sales Employees by Territory  
  
1.  W programie SSDT kliknij menu **Model**, a następnie kliknij pozycję **Role**.  
  
2.  W **Menedżerze ról** kliknij przycisk **Nowa**.  
  
    Do listy zostanie dodana nowa rola z uprawnieniami Brak.  
  
3.  Kliknij nową rolę, a następnie w kolumnie **Nazwa** zmień nazwę roli na **Sales Employees by Territory**.  
  
4.  W kolumnie **Uprawnienia** kliknij listę rozwijaną i wybierz uprawnienie **Odczyt**.  
  
5.  Kliknij kartę **Członkowie**, a następnie kliknij pozycję **Dodaj**.  
  
6.  W oknie dialogowym **Wybieranie użytkownika lub grupy**, w obszarze **Wprowadź nazwę obiektu do wybrania** wpisz pierwszą przykładową nazwę użytkownika użytą podczas tworzenia tabeli EmployeeSecurity. Kliknij przycisk **Sprawdź nazwy**, aby sprawdzić poprawność nazwy użytkownika, a następnie kliknij przycisk **OK**.  
  
    Powtórz tę czynność, aby dodać inne przykładowe nazwy użytkowników użyte podczas tworzenia tabeli EmployeeSecurity.  
  
7.  Kliknij kartę **Filtry wierszy**.  
  
8.  W tabeli **EmployeeSecurity** w kolumnie **Filtr języka DAX** wpisz następującą formułę:  
  
    ```
      =FALSE()  
    ```
  
    Ta formuła określa, że żadna kolumna nie spełnia warunku logicznego. Żadna kolumna tabeli EmployeeSecurity nie może być przeszukiwana przez członka roli użytkownika Sales Employees by Territory.  
  
9. W odniesieniu do tabeli **DimSalesTerritory** wpisz następującą formułę:  

    ```  
    ='Sales Territory'[Sales Territory Id]=LOOKUPVALUE('Employee Security'[Sales Territory Id], 
      'Employee Security'[Login Id], USERNAME(), 
      'Employee Security'[Sales Territory Id], 
      'Sales Territory'[Sales Territory Id]) 
    ```
  
    W tej formule funkcja LOOKUPVALUE zwraca wszystkie wartości kolumny DimEmployeeSecurity[SalesTerritoryId], gdzie wartość EmployeeSecurity[LoginId] jest taka sama, jak nazwa aktualnie zalogowanego użytkownika systemu Windows, a wartość EmployeeSecurity[SalesTerritoryId] jest taka sama, jak wartość DimSalesTerritory[SalesTerritoryId].  
  
    Zestaw identyfikatorów regionów sprzedaży zwrócony przez funkcję LOOKUPVALUE służy następnie do ograniczenia wierszy widocznych w tabeli DimSalesTerritory. Wyświetlane są tylko te wiersze, których wartość SalesTerritoryID znajduje się w zestawie identyfikatorów zwróconych przez funkcję LOOKUPVALUE.  
  
10. W obszarze Menedżer ról kliknij przycisk **OK**.  
  
## <a name="test-the-sales-employees-by-territory-user-role"></a>Testowanie roli użytkownika Sales Employees by Territory  
W tym zadaniu przetestujesz skuteczność roli użytkownika Sales Employees by Territory, korzystając z funkcji Analiza w programie Excel dostępnej w programie SSDT. Należy podać jedną z nazw użytkowników, która została dodana do tabeli EmployeeSecurity i jako członek roli. Ta nazwa użytkownika jest następnie używana jako obowiązująca nazwa użytkownika w połączeniu utworzonym między programem Excel a modelem.  
  
#### <a name="to-test-the-sales-employees-by-territory-user-role"></a>Testowanie roli użytkownika Sales Employees by Territory  
  
1.  W programie SSDT kliknij menu **Model**, a następnie kliknij opcję **Analizuj w programie Excel**.  
  
2.  W oknie dialogowym **Analiza w programie Excel**, w obszarze **Określ nazwę lub rolę użytkownika, która będzie używana do nawiązywania połączenia z modelem** wybierz pozycję **Inny użytkownik systemu Windows**, a następnie kliknij przycisk **Przeglądaj**.  
  
3.  W oknie dialogowym **Wybieranie użytkownika lub grupy**, w obszarze **Wprowadź nazwę obiektu do wybrania** wpisz nazwę użytkownika dołączoną do tabeli EmployeeSecurity, a następnie kliknij opcję **Sprawdź nazwę**.  
  
4.  Kliknij przycisk **OK**, aby zamknąć okno dialogowe **Wybieranie użytkownika lub grupy**, a następnie kliknij przycisk **OK**, aby zamknąć okno dialogowe **Analiza w programie Excel**.  
  
    Zostanie otwarty nowy skoroszyt w programie Excel. Automatycznie utworzona zostanie tabela przestawna. Lista pól tabeli przestawnej zawiera większość pól danych dostępnych w nowym modelu.  
  
    Należy zauważyć, że tabela EmployeeSecurity nie jest widoczna na liście pól tabeli przestawnej. Ta tabela została w poprzednim zadaniu ukryta przed narzędziami klienckimi.  
  
5.  Na liście **Pola**, w obszarze **∑ Internet Sales** (miary) wybierz miarę **InternetTotalSales**. Miara jest wprowadzana w polach **Wartości**.  
  
6.  Wybierz kolumnę **SalesTerritoryId** z tabeli **DimSalesTerritory**. Ta kolumna jest wprowadzana w polach **Etykiety wierszy**.  
  
    Warto zwrócić uwagę, że wyniki sprzedaży internetowej są wyświetlane tylko dla jednego regionu, do którego należy nazwa aktualnego użytkownika. W przypadku wybrania innej kolumny, na przykład kolumny City z tabeli DimGeography, jako pola Etykieta wiersza wyświetlane są tylko miasta należące do regionu sprzedaży, do którego należy aktualny użytkownik.  
  
    Ten użytkownik nie może przeglądać ani wyszukiwać danych sprzedaży internetowej dotyczących regionów innych niż ten, do którego sam należy. To ograniczenie obowiązuje, ponieważ filtr wierszy zdefiniowany dla tabeli DimSalesTerritory w roli użytkownika Sales Employees by Territory zabezpiecza wszystkie dane dotyczące pozostałych regionów sprzedaży.  
  
## <a name="see-also"></a>Zobacz też  
[Funkcja USERNAME (DAX)](https://msdn.microsoft.com/library/hh230954.aspx)  
[Funkcja LOOKUPVALUE (DAX)](https://msdn.microsoft.com/library/gg492170.aspx)  
[Funkcja CUSTOMDATA (DAX)](https://msdn.microsoft.com/library/hh213140.aspx)  