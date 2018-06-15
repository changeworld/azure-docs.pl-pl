---
title: Uaktualnianie usługi Analiza dzienników Azure do nowego wyszukiwania dziennika | Dokumentacja firmy Microsoft
description: Nowy język zapytań usługi Analiza dzienników jest prawie tutaj i mogą uczestniczyć w publicznej wersji zapoznawczej.  W tym artykule opisano zalet nowego języka oraz sposobach przekształcania obszaru roboczego.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 3bb54f7897876d656da6f1a4b349c9db202a142d
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2018
ms.locfileid: "28983992"
---
# <a name="azure-log-analytics-upgrade-to-new-log-search"></a>Analiza dzienników Azure uaktualnienia do nowego wyszukiwania dziennika

Nowy język zapytań usługi Analiza dzienników i i obszaru roboczego musi zostać uaktualniony, aby móc korzystać z niego.  Można uaktualnić obszaru roboczego, samodzielnie lub zaczekaj na jej automatycznie uaktualnione w okresie wdrażania rozpoczyna się w październiku opóźnione, który przechodzi przez koniec roku.  W tym artykule opisano zalet nowego języka oraz sposobach przekształcania obszaru roboczego.  

## <a name="why-the-new-language"></a>Dlaczego nowy język?
Zdajemy w dowolnym przejścia jest słabe, czy firma Microsoft nie są po prostu zmiana języka kwerend do zabawy go.  Istnieje kilka przyczyn, które ta zmiana zapewni wartość naszym klientom analizy dzienników.

- **Proste, ale.** Nowy język jest łatwiejsze do zrozumienia i podobne do bazy danych SQL z konstrukcji więcej takich jak języka naturalnego niż starszej wersji języka.
- **Język pełnego przesyłanie potokowe.**  Nowy język ma bardziej rozbudowane funkcje potokowanie niż starszej wersji języka.  Praktycznie dowolne dane wyjściowe mogą być przetwarzane potokowo do innego polecenia do tworzenia złożonych kwerend niż kiedykolwiek wcześniej.
- **Ekstrakcje pola czas wyszukiwania.**  Nowy język obsługuje bardziej zaawansowanych pola obliczeniowego środowiska uruchomieniowego niż starszej wersji języka.  Można używać złożonych obliczeń rozszerzonych polach, a następnie użyć pola obliczeniowe dodatkowych poleceń, w tym sprzęgania i agregacji.
- **Zaawansowane sprzężenia.**  Nowy język zapewnia bardziej zaawansowanych sprzężenia niż język starszej wersji, łącznie z możliwością łączenia tabel według wielu pól, użyj sprzężeń wewnętrznych i zewnętrznych i Dołącz do rozszerzonego pola.
- **Funkcje daty/godziny.**  Nowy język ma bardziej zaawansowane funkcje daty/godziny niż starszej wersji języka.
- **Inteligentne Analytics.**  Nowy język ma zaawansowanych algorytmów do oceny wzorce w zestawach danych i porównanie różnych zestawów danych.
- **Zaawansowane portal analityka.**  Portal analityka zaawansowane oferuje funkcje analizy nie jest dostępne w portalu usługi Analiza dzienników w tym multiline zapytania, dodatkowe wizualizacje i diagnostyki zaawansowanej edycji.
- **Zgodność z innymi aplikacjami.**  Nowy język portalu analityka zaawansowane są już używane i analizy w usłudze Application Insights.  Implementowania Log Analytics zapewnia spójność między usługami Azure.
- **Lepsza integracja z usługą Power BI.** Zapytania w nowym języku mogą być eksportowane do Power BI Desktop, mogą korzystać z jego możliwości przekształcania danych sformatowanego.
- **Wiele więcej.** Zapoznaj się [język zapytań Analiza dzienników Azure](https://docs.loganalytics.io) witryny, aby uzyskać szczegółowe informacje i samouczki dotyczące nowego języka.


## <a name="when-can-i-upgrade"></a>Jeśli można uaktualnić?
Uaktualnianie zostanie wycofana we wszystkich regionach platformy Azure, może on być dostępny w pewnych regionach przed pozostałymi.  Wiadomo, kiedy obszar roboczy jest dostępna do uaktualnienia po wyświetleniu transparent w górnej części obszaru roboczego zaproszenie do uaktualnienia.

![Uaktualnienie w 1](media/log-analytics-log-search-upgrade/upgrade-01a.png)

Obszar roboczy zostaje automatycznie uaktualnione, będą widzieć transparent wskazujący uaktualnieniu podsumowanie identyfikowania, czy wystąpiły problemy.

 ![Automatycznie uaktualnianie](media/log-analytics-log-search-upgrade/auto-upgrade.png)


## <a name="what-happens-after-the-upgrade"></a>Co się stanie po uaktualnieniu?
Po przekonwertowaniu z obszaru roboczego zostały wprowadzone następujące zmiany:

- Wszystkie zapisane wyszukiwania, reguły alertów i widoków, które zostały utworzone za pomocą projektanta widoku są automatycznie konwertowane na nowym języku.  Uwzględnione w rozwiązaniach wyszukiwania nie są konwertowane automatycznie, ale one jest natomiast przekonwertowane na bieżąco po ich otwarciu.  
- [Mój pulpit nawigacyjny](log-analytics-dashboards.md) jest przestarzałe uzyskać [Widok projektanta](log-analytics-view-designer.md) i [pulpity nawigacyjne Azure](../azure-portal/azure-portal-dashboards.md).  Kafelki, które zostały dodane do Mój pulpit nawigacyjny są nadal dostępne, ale ich jest tylko do odczytu.
- [Power BI integracji](log-analytics-powerbi.md) jest zastępowany odciskiem nowego procesu.  Żadnych istniejących harmonogramów usługi Power BI, które zostały utworzone zostanie wyłączone i należy je zastąpić nowy proces.
- Odpowiedzi z [alertów akcje](log-analytics-alerts-actions.md) za pomocą elementów webhook i elementów runbook mają nowy format i konieczne może być odpowiednio zaktualizować regułami alertów.
- Obejrzyj [dziennik wyszukiwania — często zadawane pytania](log-analytics-log-search-faq.md) dla często zadawane pytania dotyczące uaktualnienia.

## <a name="how-do-i-know-if-there-were-any-issues-from-the-upgrade"></a>Jak sprawdzić, czy wystąpiły problemy z uaktualnienia?
Po zakończeniu uaktualniania będzie **podsumowanie uaktualnienia** części ustawień dla obszaru roboczego.  Sprawdź w tej sekcji, aby uzyskać informacje o uaktualnienie i aby wyświetlić wszystkie problemy, które wystąpiły.

 ![Podsumowanie uaktualnienia](media/log-analytics-log-search-upgrade/upgrade-summary.png)

## <a name="how-do-i-manually-perform-the-upgrade"></a>Jak ręcznie przeprowadzić uaktualnienie?
Obszaru roboczego można uaktualnić, gdy zostanie wyświetlony Baner w górnej części portalu.  

1.  Uruchomić proces uaktualniania, klikając na banerze informacją **Dowiedz się więcej i uaktualnić**.

    ![Uaktualnij 2](media/log-analytics-log-search-upgrade/upgrade-01a.png)<br>

2.  Zapoznaj się z artykułem dodatkowych informacji na temat uaktualniania na stronie informacje o uaktualnianiu.

    ![Uaktualnij 2](media/log-analytics-log-search-upgrade/upgrade-03.png)<br>

3.  Kliknij przycisk **Uaktualnij teraz** uruchomiony w celu uaktualnienia.

    ![Uaktualnij 4](media/log-analytics-log-search-upgrade/upgrade-04.png)<br>Pole o powiadomienia w prawym górnym rogu pokazuje stan.
    
    ![Uaktualnij 5](media/log-analytics-log-search-upgrade/upgrade-05.png)

4.  Gotowe.  Przekazywane do strony wyszukiwania dziennika, aby przyjrzeć uaktualnionego obszaru roboczego.

    ![Uaktualnij 6](media/log-analytics-log-search-upgrade/upgrade-06.png)

Jeśli wystąpi problem, który powoduje uaktualnienie kończy się niepowodzeniem, można przejść do [forum dyskusyjne](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) i zgłoś zapytanie lub [Utwórz żądanie obsługi](../azure-supportability/how-to-create-azure-support-request.md) z portalu Azure.

## <a name="how-do-i-learn-the-new-language"></a>Jak dowiedzieć się nowy język
Ponieważ jest on używany przez wiele usług utworzyliśmy [zewnętrznej witryny do obsługi dokumentacji](https://docs.loganalytics.io/) dla nowego języka.  W tym samouczki, przykłady i pełne odwołanie ułatwiające pochodzić szybko. Możesz zapoznać się z samouczkiem nowy język w [wprowadzenie do zapytań](https://go.microsoft.com/fwlink/?linkid=856078) i dokumentacja języka na dostęp [języka zapytań usługi Analiza dzienników](https://go.microsoft.com/fwlink/?linkid=856079).  

Jeśli chcesz wypróbować nowy język w środowisku pokaz licznych przykładowe dane w tym ma przyjrzeć się [Plac zabaw dla środowiska](https://portal.loganalytics.io/demo#/discover/home).

Jeśli znasz już starszych język zapytań usługi Analiza dzienników jednak, można użyć konwertera języka, które jest dodawane do swojego obszaru roboczego w ramach uaktualnienia.  Po prostu wpisz kwerendę starszej wersji, a następnie kliknij przycisk **przekonwertować** wyświetlić przetłumaczonej wersji.  Można następnie albo kliknij przycisk Wyszukaj, aby Uruchom wyszukiwania lub Kopiuj i Wklej zapytanie przekonwertowane do użycia w innym miejscu, takie jak reguły alertu.  Może także zawierać spojrzeć na naszych [ściągawka](log-analytics-log-search-transition.md) który porównuje bezpośrednio typowych zapytań z języka starszej wersji.

![Konwerter języka](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="next-steps"></a>Kolejne kroki
- Zapoznaj się z [samouczka na nowy język](https://go.microsoft.com/fwlink/?linkid=856078).
- Zapoznaj się z artykułem [samouczka na korzystanie z portalu wyszukiwania dziennika](log-analytics-log-search-log-search-portal.md) w nowym języku zapytania.
- Wprowadzenie do nowego [portal analityka zaawansowane](https://go.microsoft.com/fwlink/?linkid=856587).
