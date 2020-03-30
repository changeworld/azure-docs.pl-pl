---
title: Łącznik Przepływ eksploratora danych platformy Microsoft Azure (wersja zapoznawcza)
description: Dowiedz się więcej o używaniu łącznika usługi Microsoft Flow do tworzenia przepływów automatycznie zaplanowanych lub wyzwalanych zadań.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/15/2020
ms.openlocfilehash: 7745888dcaa1324d4a9d956e93d0504c8da8c026
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501765"
---
# <a name="microsoft-flow-connector-preview"></a>Łącznik usługi Microsoft Flow (wersja zapoznawcza)

Łącznik przepływu eksploratora danych platformy Azure umożliwia eksploratorowi danych platformy Azure korzystanie z [funkcji przepływu automatyzacji systemu Microsoft Power Automate](https://flow.microsoft.com/) do automatycznego uruchamiania zapytań i poleceń Kusto w ramach zaplanowanego lub wyzwalanego zadania.

Typowe scenariusze użycia obejmują:

* Wysyłanie codziennych raportów zawierających tabele i wykresy
* Ustawianie powiadomień na podstawie wyników kwerendy
* Planowanie poleceń sterowania w klastrach
* Eksportowanie i importowanie danych między Eksploratorem danych platformy Azure a innymi bazami danych 

Aby uzyskać więcej informacji, zobacz [przykłady użycia łącznika programu Microsoft Flow](flow-usage.md).

##  <a name="log-in"></a>Logowanie się 

1. Zaloguj się do [programu Microsoft Power Automate](https://flow.microsoft.com/).

1. Podczas nawiązywania połączenia po raz pierwszy zostanie wyświetlony monit o zalogowanie się.

1. Wybierz pozycję **Zaloguj się** i wprowadź swoje poświadczenia.

![Okno dialogowe logowania](./media/flow/flow-signin.png)

## <a name="authentication"></a>Uwierzytelnianie

Można uwierzytelnić za pomocą poświadczeń użytkownika lub aplikacji usługi AAD.

> [!Note]
> Upewnij się, że aplikacja jest [aplikacją AAD](https://docs.microsoft.com/azure/kusto/management/access-control/how-to-provision-aad-app) i jest autoryzowana do wykonywania zapytań w klastrze.

1. Wybierz trzy kropki w prawym górnym rogu ![łącznika usługi Microsoft Flow: Dodawanie połączenia](./media/flow/flow-addconnection.png)

1. Wybierz **pozycję Dodaj nowe połączenie,** a następnie wybierz pozycję **Połącz z jednostką usługi**.
![Okno dialogowe logowania](./media/flow/flow-signin.png)

1. Wprowadź wymagane informacje:
    * Nazwa połączenia: opisowa i znacząca nazwa nowego połączenia
    * Identyfikator klienta: Identyfikator aplikacji
    * Klucz tajny klienta: Klucz aplikacji
    * Dzierżawa: identyfikator katalogu usługi AAD, w którym utworzono aplikację. Na przykład identyfikator dzierżawy firmy Microsoft to: 72f988bf-86f1-41af-91ab-2d7cd011db47

![Uwierzytelnianie aplikacji](./media/flow/flow-appauth.png)

Po zakończeniu uwierzytelniania zobaczysz, że przepływ używa nowo dodanego połączenia.

![Zakończone uwierzytelnianie aplikacji](./media/flow/flow-appauthcomplete.png)

Od teraz ten przepływ będzie uruchamiany przy użyciu tych poświadczeń aplikacji.

## <a name="find-the-azure-kusto-connector"></a>Znajdź łącznik Azure Kusto

Aby użyć łącznika usługi Microsoft Flow, należy najpierw dodać wyzwalacz. Wyzwalacz można zdefiniować na podstawie okresu cyklicznego lub jako odpowiedź na poprzednią akcję przepływu.

1. [Utwórz nowy przepływ](https://flow.microsoft.com/manage/flows/new) lub na stronie głównej wybierz akcję **Moje przepływy,** a następnie wybierz pozycję **+ Nowy**.

    ![Tworzenie nowego przepływu](./media/flow/flow-newflow.png)

1. Dodaj zaplanowane z pustego.

    ![Nowy zaplanowany przepływ](./media/flow/flow-scheduled-from-blank.png)

1. Wprowadź wymagane informacje na stronie Tworzenie zaplanowanego przepływu.
    ![Tworzenie zaplanowanego przepływu](./media/flow/flow-build-scheduled-flow.png)
1. Wybierz **pozycję Utwórz**.
1. Wybierz pozycję **+ Nowy krok**.
1. W polu wyszukiwania wpisz "Kusto".

    ![Wybierz akcje przepływu](./media/flow/flow-actions.png)

1. Wybierz **Eksploratora danych platformy Azure**.

## <a name="flow-actions"></a>Akcje przepływu

Po otwarciu łącznika usługi Azure Data Explorer, istnieją trzy możliwe akcje, które można dodać do przepływu.

W tej sekcji opisano możliwości i parametry dla każdej akcji usługi Microsoft Flow.

![Akcje Eksploratora danych platformy Azure przepływu](./media/flow/flow-adx-actions.png)

### <a name="run-control-command-and-visualize-results"></a>Uruchamianie polecenia sterowania i wizualizowanie wyników

Użyj polecenia Uruchom kontrolkę i wizualizuj akcję wyników, aby uruchomić [polecenie sterujące](https://docs.microsoft.com/azure/kusto/management/index).

1. Określ adres URL klastra. Na przykład: https://clusterName.eastus.kusto.windows.net
1. Wprowadź nazwę bazy danych.
1. Określ polecenie sterowania:
    * Wybieranie zawartości dynamicznej z aplikacji i łączników używanych w przepływie
    * Dodawanie wyrażenia w celu uzyskiwania dostępu do wartości, konwertowania i porównywania ich
1. Aby wysłać wyniki tej akcji pocztą e-mail jako tabelę lub wykres, określ typ wykresu, który może być:
    * Tabela HTML
    * Wykres kołowy
    * Wykres czasu
    * Wykres słupkowy

![Uruchom przepływ, polecenie sterujące](./media/flow/flow-runcontrolcommand.png)

> [!IMPORTANT]
> W polu *Nazwa klastra* wprowadź adres URL klastra.

### <a name="run-query-and-list-results"></a>Uruchamianie wyników kwerendy i listy

> [!Note]
> Jeśli kwerenda zaczyna się od kropki (co oznacza, że jest [to polecenie sterujące),](https://docs.microsoft.com/azure/kusto/management/index)użyj [polecenia Uruchom sterowanie i wizualizuj wyniki](#run-control-command-and-visualize-results)

Ta akcja wysyła zapytanie do klastra Kusto. Akcje, które są dodawane później iteracji nad każdym wierszu wyników kwerendy.

Poniższy przykład wyzwala kwerendę co minutę i wysyła wiadomość e-mail na podstawie wyników kwerendy. Kwerenda sprawdza liczbę wierszy w bazie danych, a następnie wysyła wiadomość e-mail tylko wtedy, gdy liczba wierszy jest większa niż 0. 

![Uruchamianie wyników listy zapytań](./media/flow/flow-runquerylistresults-2.png)

> [!Note]
> Jeśli kolumna ma kilka linii, łącznik będzie uruchamiany dla każdej linii w kolumnie.

### <a name="run-query-and-visualize-results"></a>Uruchamianie kwerendy i wizualizowanie wyników
        
> [!Note]
> Jeśli kwerenda zaczyna się od kropki (co oznacza, że jest [to polecenie sterujące),](https://docs.microsoft.com/azure/kusto/management/index)użyj [polecenia Uruchom sterowanie i wizualizuj wyniki](#run-control-command-and-visualize-results)
        
Użyj uruchom kwerendę i zwizualizuj wyniki akcji, aby wizualizować wynik zapytania Kusto jako tabelę lub wykres. Na przykład użyj tego przepływu, aby otrzymywać codzienne raporty ICM pocztą e-mail. 
    
W tym przykładzie wyniki kwerendy są zwracane jako tabela HTML.
            
![Uruchamianie kwerendy i wizualizowanie wyników](./media/flow/flow-runquery.png)

> [!IMPORTANT]
> W polu *Nazwa klastra* wprowadź adres URL klastra.

## <a name="email-kusto-query-results"></a>Wyślij wiadomość e-mail do wyników zapytań Kusto

Możesz dołączyć krok w dowolnym przepływie, aby wysyłać raporty pocztą e-mail na dowolny adres e-mail. 

1. Wybierz **+ Nowy krok,** aby dodać nowy krok do przepływu.
1. W polu wyszukiwania wprowadź pozycję Office 365 i wybierz pozycję **Office 365 Outlook**.
1. Wybierz akcję **Wyślij wiadomość e-mail**.
1. Wprowadź adres e-mail do miejsca, w którym ma zostać wysłany raport e-mail.
1. Wprowadź temat wiadomości e-mail.
1. W polu *Treść* z pola Zawartość dynamiczna wybierz pozycję **Obiekt**.
1. Wybierz pozycję **Pokaż opcje zaawansowane**.
1. W polu *Nazwa załączników -1* wybierz pozycję **Nazwa załącznika**.
1. W polu *Zawartość załączników* wybierz pozycję **Zawartość załącznika**.
1. W razie potrzeby ustaw poziom ważności.
1. Wybierz **pozycję Zapisz**.

![Wyślij wiadomość e-mail](./media/flow/flow-sendemail.png)

## <a name="check-if-your-flow-succeeded"></a>Sprawdź, czy przepływ się powiódł

Aby sprawdzić, czy przepływ się powiódł, zobacz historię przebiegu przepływu:
1. Przejdź do [strony głównej programu Microsoft Flow](https://flow.microsoft.com/).
1. Z menu głównego wybierz [pozycję Moje przepływy](https://flow.microsoft.com/manage/flows).
    ![Strona Moje przepływy](./media/flow/flow-myflows.png)
1. W wierszu przepływu, który chcesz zbadać, wybierz ikonę więcej poleceń, a następnie **uruchom historię**.

    ![Uruchom menu historii](./media/flow//flow-runhistory.png)

    Wszystkie przebiegi przepływu są wyświetlane z czasem rozpoczęcia, czasem trwania i stanem.
    ![Strona wyników historii uruchamiania](./media/flow/flow-runhistoryresults.png)

    Aby uzyskać szczegółowe informacje na temat przepływu, na stronie [Moje przepływy](https://flow.microsoft.com/manage/flows) wybierz przepływ, który chcesz zbadać.

    ![Uruchamianie pełnej strony wyników historii](./media/flow/flow-fulldetails.png) 

Aby zobaczyć, dlaczego przebieg nie powiódł się, wybierz godzinę rozpoczęcia wykonywania. Pojawi się przepływ, a krok przepływu, który nie powiódł się, jest wskazywany przez czerwony wykrzyknik. Rozwiń krok nie powiodło się, aby wyświetlić jego szczegóły. Okienko po prawej stronie zawiera informacje o awarii, dzięki czemu można go rozwiązać.

![Błąd przepływu](./media/flow/flow-error.png)

## <a name="timeout-exceptions"></a>Wyjątki limitu czasu

Przepływ może zakończyć się niepowodzeniem i zwrócić wyjątek "RequestTimeout", jeśli działa dłużej niż siedem minut.

Dowiedz się więcej o [ograniczeniach usługi Microsoft Flow](#limitations).
    
Ta sama kwerenda może zostać pomyślnie uruchomiony w Eksploratorze danych platformy Azure, gdzie czas nie jest ograniczony i można zmienić.
            
Wyjątek "RequestTimeout" jest pokazany na poniższej ilustracji:
    
![Błąd wyjątku limitu czasu żądania przepływu](./media/flow/flow-requesttimeout.png)
    
Aby rozwiązać problem z limitem czasu, spróbuj uczynić zapytanie bardziej wydajnym, tak aby działa szybciej, lub oddzielić je na fragmenty. Każdy fragment można uruchomić na innej części kwerendy.

Aby uzyskać więcej informacji, przeczytaj artykuł [Dotyczący najlepszych rozwiązań dotyczących kwerendy](https://docs.microsoft.com/azure/kusto/query/best-practices).

## <a name="limitations"></a>Ograniczenia

* Wyniki zwrócone klientowi są ograniczone do 500 000 rekordów. Całkowita pamięć dla tych rekordów nie może przekraczać 64 MB i czasu wykonywania siedmiu minut.
* Łącznik nie obsługuje operatorów [rozwidłowych](https://docs.microsoft.com/azure/kusto/query/forkoperator) i [aspektowych.](https://docs.microsoft.com/azure/kusto/query/facetoperator)
* Usługa Flow działa najlepiej w witrynach Microsoft Edge i Chrome.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [łączniku aplikacji logiki programu Microsoft Azure Explorer,](https://docs.microsoft.com/azure/kusto/tools/logicapps) który jest innym sposobem automatycznego uruchamiania zapytań i poleceń Kusto w ramach zaplanowanego lub wyzwolonego zadania.
