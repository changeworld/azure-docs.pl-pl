---
title: Stosować zalecenia wydajności — baza danych SQL Azure | Dokumentacja firmy Microsoft
description: Użyj portalu Azure, aby znaleźć zalecenia dotyczące wydajności, które można zoptymalizować wydajność bazy danych SQL Azure.
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 3361519c260fe842ae362814cbee62aa9257b9f8
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="find-and-apply-performance-recommendations"></a>Znajdź i stosować zalecenia wydajności

Aby znaleźć zalecenia dotyczące wydajności, które można zoptymalizować wydajność bazy danych SQL Azure lub Popraw niektórych problem wskazany w obciążenie, można użyć portalu Azure. **Zalecenie wydajności** strony w portalu Azure umożliwia znalezienie najlepsze rekomendacje na potencjalnego wpływu na ich podstawie. 

## <a name="viewing-recommendations"></a>Przeglądanie zaleceniami

Aby wyświetlić i stosować zalecenia wydajności, należy poprawny [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md) uprawnienia na platformie Azure. **Czytnik**, **Współautor bazy danych SQL** są wymagane uprawnienia, aby wyświetlić zalecenia, i **właściciela**, **Współautor bazy danych SQL** wymagane są uprawnienia do wykonywać żadnych akcji; Utwórz lub Porzuć indeksy i anulowanie tworzenia indeksu.

Aby znaleźć zalecenia dotyczące wydajności w portalu Azure, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Przejdź do **wszystkie usługi** > **baz danych**i wybierz bazę danych.
3. Przejdź do **wydajności zalecenie** Aby wyświetlić dostępne zalecenia dotyczące wybranej bazy danych.

Zalecenia dotyczące wydajności są wyświetlane w tabeli podobny do przedstawionego na poniższej ilustracji:

![Zalecenia](./media/sql-database-advisor-portal/recommendations.png)

Zalecenia są sortowane według ich potencjalny wpływ na wydajność na następujące kategorie:

| Wpływ | Opis |
|:--- |:--- |
| Wysoka |Duże znaczenie zalecenia powinien zapewnić najbardziej znaczący wpływ na wydajność. |
| Medium |Średni wpływ zalecenia należy zwiększyć wydajność, ale nie w znacznym stopniu. |
| Małe |Niski wpływ zalecenia należy zapewnić lepszą wydajność niż bez, ale ulepszenia może być niewielka. |


> [!NOTE]
> Baza danych SQL Azure należy monitorować działania przynajmniej przez jeden dzień w celu identyfikacji kilka zaleceń. Baza danych SQL Azure łatwiej można zoptymalizować wzorców spójne zapytania nie może uzyskać losowe spotty seria działań. Jeśli zalecenia nie są obecnie dostępne, **wydajności zalecenie** strona zawiera komunikat wyjaśniający, dlaczego.
> 

Można również wyświetlić stan historyczne operacje. Wybierz zalecenie lub stan, aby uzyskać więcej informacji.

Oto przykład "Create index" zalecenia w portalu Azure.

![Utwórz indeks](./media/sql-database-advisor-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>Zalecenia dotyczące stosowania
Baza danych SQL Azure umożliwia pełną kontrolę nad jak zalecenia są włączone, przy użyciu dowolnej z trzech opcji: 

* Zastosuj poszczególne zalecenia co naraz.
* Włączanie automatycznego dostrajania automatycznie stosowania zaleceń.
* Aby ręcznie zaimplementować zalecenia, uruchom zalecane skryptu T-SQL bazy danych.

Wybierz każde zalecenie, aby wyświetlić jego szczegóły, a następnie kliknij przycisk **Wyświetl skrypt** dokładne szczegółowych informacji z tworzenia zalecenia.

Bazy danych pozostaje w trybie online, podczas gdy zalecenie jest stosowany — przy użyciu zalecenie wydajności lub automatycznego dostrajania nigdy nie ma bazy danych w trybie offline.

### <a name="apply-an-individual-recommendation"></a>Zastosuj poszczególne zalecenia.
Można przeczytaj i zaakceptuj zalecenia jednym naraz.

1. Na **zalecenia** wybierz zalecenia.
2. Na **szczegóły** kliknij przycisk **Zastosuj** przycisku.
   
    ![Zastosuj zalecenia](./media/sql-database-advisor-portal/apply.png)

Wybrane zalecenia są stosowane w bazie danych.

### <a name="removing-recommendations-from-the-list"></a>Usunięcie zalecenia z listy
Jeśli lista zaleceń zawiera elementy, które chcesz usunąć z listy, można odrzucić zalecenia:

1. Wybierz na liście zalecenie **zalecenia** można otworzyć szczegółów.
2. Kliknij przycisk **odrzucić** na **szczegóły** strony.

W razie potrzeby można dodać odrzucone elementy z powrotem do **zalecenia** listy:

1. Na **zalecenia** kliknij przycisk **widoku odrzucone**.
2. Wybierz odrzuconego elementu z listy, aby wyświetlić jego szczegóły.
3. Opcjonalnie kliknij **Cofnij odrzucić** można dodać indeksu z powrotem do głównego listę **zalecenia**.

> [!NOTE]
> Należy pamiętać, że jeśli baza danych SQL [automatycznego dostrajania](sql-database-automatic-tuning.md) jest włączona, a jeśli ręcznie usunąć zgodnie z zaleceniami listy tych zaleceń zostanie nigdy nie można automatycznie zastosować. Odrzucanie zalecenie jest to wygodna metoda użytkownicy mogli automatycznego dostrajania, włączyć w przypadkach, gdy wymaganie nie można zastosować określonego zalecenia.
> To zachowanie można przywrócić przez dodanie odrzuconych zaleceń powrót do listy zalecenia, wybierając opcję Cofnij odrzucić.
> 

### <a name="enable-automatic-tuning"></a>Włączanie automatycznego dostrajania
Można ustawić baza danych SQL Azure, aby automatycznie wdrożyć zalecenia. Zgodnie z zaleceniami staną się dostępne, są one automatycznie stosowane. Podobnie jak w przypadku zarządzanych przez usługę, jeśli jego wpływ na wydajność jest ujemny wszystkie zalecenia, zalecenie zostanie przywrócony.

1. Na **zalecenia** kliknij przycisk **automatyzacja**:
   
    ![Ustawienia usługi Advisor](./media/sql-database-advisor-portal/settings.png)
2. Wybierz akcje do automatyzacji:
   
    ![Zalecane indeksy](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Należy pamiętać, że **DROP_INDEX** opcji w tej chwili jest niezgodny z aplikacji przy użyciu wskazówek indeks i przełączanie partycji i nie powinna włączona w tych przypadkach.
>

### <a name="manually-run-the-recommended-t-sql-script"></a>Ręcznie uruchom zalecane skryptu T-SQL
Wybierz każde zalecenie, a następnie kliknij przycisk **Wyświetl skrypt**. Uruchom ten skrypt, aby ręcznie zastosować zalecenie bazy danych.

*Indeksy, które są wykonywane ręcznie nie są monitorowane i weryfikowane pod kątem wpływu na wydajność przez usługę* , zaleca się monitorowanie tych indeksów po utworzeniu, aby sprawdzić ich zapewniają większą wydajność i dostosować lub usuń je, jeśli to konieczne. Aby uzyskać więcej informacji o tworzeniu indeksów, zobacz [CREATE INDEX (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).

### <a name="canceling-recommendations"></a>Anulowanie zalecenia
Zalecenia, które znajdują się w **oczekujące**, **weryfikacji**, lub **Powodzenie** stanu mogą zostać anulowane. Zalecenia dotyczące ze stanem **Executing** nie można anulować.

1. Wybierz zalecenia w **dostrajanie historii** obszaru, aby otworzyć **szczegóły zalecenia** strony.
2. Kliknij przycisk **anulować** aby przerwać proces stosowania zalecenia.

## <a name="monitoring-operations"></a>Operacje monitorowania
Zalecamy stosowanie może odbywa się natychmiast. Portal zawiera szczegóły dotyczące stanu zalecenia. Poniżej przedstawiono możliwe stany, które można indeksu w:

| Stan | Opis |
|:--- |:--- |
| Oczekujące |Zastosuj zalecenie polecenie zostało odebrane i jest zaplanowane do uruchomienia. |
| Wykonywanie |Zalecenia są stosowane. |
| Weryfikowanie |Zalecenie zostało pomyślnie zastosowane i usługa jest pomiaru korzyści. |
| Powodzenie |Zalecenie zostało pomyślnie zastosowane i zostały zmierzone korzyści. |
| Błąd |Wystąpił błąd w trakcie procesu stosowania zalecenia. Może to być przejściowy problem lub schematu zmiany do tabeli i skrypt nie jest już prawidłowy. |
| Cofanie |Zalecenie została zastosowana, ale został uznany za z systemem innym niż wydajność i zostanie automatycznie przywrócony. |
| Cofnięte |Zalecenie została przywrócona. |

Kliknij zalecenie w trakcie z listy, aby uzyskać więcej informacji:

![Zalecane indeksy](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Przywrócenie zalecenia
Jeśli zalecenia dotyczące wydajności jest używana do stosowania zalecenie (co oznacza, że nie zostało ręcznie uruchomione skryptu T-SQL), jego przestawia zmiany w przypadku odnalezienia ich wpływ na wydajność, aby być ujemna. Jakiegokolwiek powodu po prostu chcesz przywrócić zalecenia, można wykonać następujące czynności:

1. Wybierz pomyślnie zastosowane zalecenia w **dostrajanie historii** obszaru.
2. Kliknij przycisk **Przywróć** na **szczegóły zalecenia** strony.

![Zalecane indeksy](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Wpływ na wydajność monitorowania zalecenia dotyczące indeksu
Po pomyślnie zostały zaimplementowane zalecenia (obecnie indeksu operacji i parametryzacja zapytań zalecenia tylko), możesz kliknąć **szczegółowe informacje o zapytań** na podstawie zalecenia szczegóły strony, aby otworzyć [zapytania Szczegółowe informacje o wydajności](sql-database-query-performance.md) i sprawdzić jego wpływ na wydajność kwerend top.

![Wpływ na wydajność monitora](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>Podsumowanie
Baza danych SQL Azure zawiera zalecenia dotyczące poprawy wydajności bazy danych SQL. Zapewniając skryptów T-SQL, możesz uzyskać pomoc przy optymalizacji bazy danych i ostatecznie poprawę wydajności zapytań.

## <a name="next-steps"></a>Kolejne kroki
Monitorowanie zalecenia i zastosować je do ograniczania wydajności w dalszym ciągu. Obciążeń bazy danych są dynamiczne i zmienianie w sposób ciągły. Baza danych SQL Azure kontynuuje monitorowanie i podano zalecenia, które może potencjalnie podnieść wydajność bazy danych. 

* Zobacz [automatycznego dostrajania](sql-database-automatic-tuning.md) Aby dowiedzieć się więcej na temat automatycznego dostrajania w bazie danych SQL Azure.
* Zobacz [zaleceń](sql-database-advisor.md) omówienie zaleceń wydajności bazy danych SQL Azure.
* Zobacz [szczegółowe informacje o wydajności zapytań](sql-database-query-performance.md) Aby dowiedzieć się więcej o wyświetlaniu wpływu na wydajność kwerend top.

## <a name="additional-resources"></a>Zasoby dodatkowe
* [Magazyn zapytań](https://msdn.microsoft.com/library/dn817826.aspx)
* [TWORZENIE INDEKSU](https://msdn.microsoft.com/library/ms188783.aspx)
* [Kontrola dostępu oparta na rolach](../role-based-access-control/overview.md)

