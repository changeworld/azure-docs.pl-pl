---
title: Stosowanie zaleceń dotyczących wydajności — usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Użyj witryny Azure portal, aby znaleźć zalecenia dotyczące wydajności, które można zoptymalizować wydajność usługi Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: d80581aae56fc9d65d6f24d21f2c582cb74b3f2d
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2019
ms.locfileid: "58863206"
---
# <a name="find-and-apply-performance-recommendations"></a>Znajdowanie i stosowanie zaleceń dotyczących wydajności

Można użyć witryny Azure portal, aby znaleźć zalecenia dotyczące wydajności, które można zoptymalizować wydajność usługi Azure SQL Database lub rozwiązać jakiś problem w obciążenia. **Zalecenie dotyczące wydajności** strony w witrynie Azure portal umożliwia znalezienie najlepszych zaleceń, w oparciu o ich potencjalny wpływ. 

## <a name="viewing-recommendations"></a>Wyświetlanie zaleceń

Aby wyświetlić i stosowanie zaleceń dotyczących wydajności, należy poprawny [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md) uprawnień na platformie Azure. **Czytnik**, **Współautor bazy danych SQL** są wymagane uprawnienia, aby wyświetlić zalecenia, a **właściciela**, **Współautor bazy danych SQL** uprawnienia są wymagane do wykonywać żadnych akcji; Utwórz lub Usuń indeksy i anulować tworzenie indeksów.

Aby znaleźć zalecenia dotyczące wydajności w witrynie Azure portal, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Przejdź do **wszystkich usług** > **baz danych SQL**i wybierz swoją bazę danych.
3. Przejdź do **zalecenie dotyczące wydajności** Aby wyświetlić dostępne zalecenia dotyczące wybranej bazy danych.

Zalecenia dotyczące wydajności są wyświetlane w tabeli jest podobny do przedstawionego na poniższym rysunku:

![Zalecenia](./media/sql-database-advisor-portal/recommendations.png)

Zalecenia są sortowane według ich potencjalny wpływ na wydajność na następujące kategorie:

| Wpływ | Opis |
|:--- |:--- |
| Wysoka |Zalecenia o dużym znaczeniu powinien zapewnić najbardziej znaczącego wpływu na wydajność. |
| Medium |Średni wpływ zalecenia należy poprawić wydajność, ale nie w znacznym stopniu. |
| Małe |Mały wpływ zalecenia należy zapewnić lepszą wydajność bez, ale ulepszenia może nie być istotne. |


> [!NOTE]
> Usługa Azure SQL Database musi monitorować działania co najmniej jeden dzień w celu identyfikowania niektóre zalecenia. Usługi Azure SQL Database można łatwiej Optymalizuj pod kątem wzorców zapytań spójne, niż może wzmożeniach spotty losowe działania. Jeśli zalecenia nie są obecnie dostępne **zalecenie dotyczące wydajności** strona zawiera również komunikat wyjaśniający, dlaczego.
> 

Można również wyświetlić stan historyczne operacje. Wybierz zalecenie lub stan, aby uzyskać więcej informacji.

Oto przykład "Create index" zalecenia w witrynie Azure portal.

![tworzenie indeksu](./media/sql-database-advisor-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>Stosowanie zalecenia
Usługa Azure SQL Database zapewnia pełną kontrolę nad jak zalecenia są włączone, przy użyciu dowolnej z następujących trzech opcji: 

* Zastosowania poszczególnych zaleceń jeden w czasie.
* Włączanie automatycznego dostrajania automatycznego stosowania zaleceń.
* Aby zaimplementować zalecenie ręcznie, należy uruchomić zalecane skryptu T-SQL względem bazy danych.

Wybierz każde zalecenie, aby wyświetlić jego szczegóły, a następnie kliknij przycisk **wyświetlić skryptu** przejrzeć szczegółowymi informacjami na temat sposobu tworzenia zalecenia.

Baza danych pozostaje w trybie online, podczas gdy jest stosowany zalecany — przy użyciu zalecenie dotyczące wydajności lub dostrajania automatycznego nigdy nie ma bazy danych w trybie offline.

### <a name="apply-an-individual-recommendation"></a>Zastosowania poszczególnych zaleceń.
Można przejrzeć i zaakceptuj zalecenia jednego naraz.

1. Na **zalecenia** wybierz zalecenie.
2. Na **szczegóły** kliknij **Zastosuj** przycisku.
   
    ![Stosowanie zalecenia](./media/sql-database-advisor-portal/apply.png)

Wybrane zalecenie są stosowane w bazie danych.

### <a name="removing-recommendations-from-the-list"></a>Usunięcie zalecenia z listy

Jeśli lista zaleceń zawiera elementy, które chcesz usunąć z listy, możesz odrzucić zalecenia:

1. Wybierz zalecenie na liście **zalecenia** można otworzyć szczegóły.
2. Kliknij przycisk **odrzucić** na **szczegóły** strony.

Jeśli to konieczne, można dodać odrzucone elementy z powrotem do **zalecenia** listy:

1. Na **zalecenia** kliknij **Wyświetl odrzucone**.
2. Wybierz element odrzuconych z listy, aby wyświetlić jego szczegóły.
3. Opcjonalnie kliknij **Cofnij odrzucenie** dodać indeks powrót do listy głównego **zalecenia**.

> [!NOTE]
> Należy pamiętać, że jeśli bazy danych SQL [dostrajania automatycznego](sql-database-automatic-tuning.md) jest włączona, jeśli ręcznie odrzuconych rekomendacji z listy tych zaleceń nigdy nie być stosowane automatycznie. Odrzucanie zalecenie to wygodny sposób użytkownikom ma włączone w przypadkach, w przypadku konieczności konkretne zalecenie nie można zastosować dostrajania automatycznego.
> To zachowanie można przywrócić, dodając odrzuconych rekomendacji powrót do listy zalecenia, wybierając opcję Cofnij odrzucenie.
> 

### <a name="enable-automatic-tuning"></a>Włączanie automatycznego dostrajania
Można ustawić automatyczne wdrażanie zaleceń usługi Azure SQL Database. W miarę udostępniania zaleceń są one automatycznie stosowane. Podobnie jak w przypadku wszystkich zaleceń dotyczących zarządzanych przez usługę, jeśli negatywny wpływ na wydajność jest ujemna, zalecenie zostało wycofane.

1. Na **zalecenia** kliknij **automatyzacja**:
   
    ![Ustawienia usługi Advisor](./media/sql-database-advisor-portal/settings.png)
2. Wybierz akcje w celu zautomatyzowania:
   
    ![Zalecane indeksów](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Należy pamiętać, że **DROP_INDEX** obecnie opcja nie jest zgodna z aplikacjami przy użyciu wskazówek przełączanie i indeksu partycji. 
>

Po wybraniu wymaganą konfigurację, kliknij przycisk Zastosuj.

### <a name="manually-apply-recommendations-through-t-sql"></a>Ręcznie stosować zalecenia za pomocą języka T-SQL

Wybierz każde zalecenie, a następnie kliknij przycisk **wyświetlić skryptu**. Uruchom ten skrypt względem bazy danych ręcznie zastosować zalecenie.

*Indeksy ręcznie wykonywanych nie są monitorowane i zweryfikowane dla negatywny wpływ na wydajność przez usługę* więc monitorować te indeksy po utworzeniu, aby sprawdzić, mogą zapewnić wzrost wydajności i dostosować lub je usunąć, jeśli to konieczne. Aby uzyskać szczegółowe informacje na temat tworzenia indeksów, zobacz [CREATE INDEX (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx). Ponadto Ręczne stosowanie zalecenia pozostaną aktywne i widoczne na liście zaleceń przez 24-48 godzin. zanim system automatycznie wycofuje je. Jeśli chcesz usunąć wcześniej zalecenia, możesz ręcznie odrzucić je.

### <a name="canceling-recommendations"></a>Trwa anulowanie zalecenia

Zalecenia, które znajdują się w **oczekujące**, **weryfikowanie**, lub **Powodzenie** stanu może być anulowany. Zalecenia dotyczące ze stanem **Executing** nie może zostać anulowana.

1. Wybierz zalecenie w **Historia dostrajania** obszar, aby otworzyć **Szczegóły rekomendacji** strony.
2. Kliknij przycisk **anulować** przerwanie procesu stosowania zalecenia.

## <a name="monitoring-operations"></a>Operacje monitorowania

Stosowanie zalecenia nie może być realizowane natychmiast. Portal zawiera szczegóły dotyczące stanu zalecenie ma do nich. Poniżej przedstawiono możliwe stany, indeks może być:

| Stan | Opis |
|:--- |:--- |
| Oczekujące |Stosowanie zalecenia polecenie zostało odebrane i jest zaplanowane do wykonania. |
| Wykonywanie |Trwa stosowanie zalecenia. |
| Sprawdzanie poprawności |Zalecenie zostało pomyślnie zastosowane i usługa jest mierzenie korzyści. |
| Powodzenie |Zalecenie zostało pomyślnie zastosowane i zostały zmierzone korzyści. |
| Błąd |Wystąpił błąd w trakcie procesu stosowania zalecenia. Może to być przejściowy problem lub prawdopodobnie schematu zmiany do tabeli i skrypt nie jest już prawidłowy. |
| Cofanie |Zalecenia zostały zastosowane, ale został uznany za wydajne niż i zostanie automatycznie przywrócona. |
| Cofnięte |Zalecenie zostało wycofane. |

Kliknij przycisk rekomendacji w trakcie z listy, aby uzyskać więcej informacji:

![Zalecane indeksów](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Cofanie zaleceń
Jeśli używano zalecenia dotyczące wydajności w celu zastosowania zalecenia (co oznacza, że nie ręcznie uruchomić skryptu T-SQL), automatycznie powraca on zmiany w przypadku odnalezienia wpływ na wydajność, aby być ujemna. Jeśli z jakiegoś powodu chcesz po prostu przywrócić zalecenie, należy wykonać następujące:

1. Wybierz zalecenie pomyślnie zastosowano w **Historia dostrajania** obszaru.
2. Kliknij przycisk **Przywróć** na **szczegóły zalecenia** strony.

![Zalecane indeksów](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Monitorowanie wydajności wpływ zalecenia dotyczące indeksu
Po pomyślnym wprowadzeniu zalecenia (obecnie indeksu operacji i sparametryzuj zapytania tylko zalecenia dotyczące), możesz kliknąć pozycję **szczegółowe informacje o zapytaniach** na zalecenie szczegóły strony, aby otworzyć [zapytania Szczegółowe informacje o wydajności](sql-database-query-performance.md) i zobaczyć wpływ na wydajność najczęściej używane zapytania.

![Wpływ na wydajność monitora](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>Podsumowanie
Usługa Azure SQL Database udostępnia zalecenia dotyczące poprawy wydajności bazy danych SQL. Dostarczając skryptów T-SQL, możesz uzyskać pomoc przy Optymalizowanie bazy danych, a ostatecznie poprawę wydajności zapytań.

## <a name="next-steps"></a>Kolejne kroki
Monitoruj zalecenia i stosować je do uściślić wydajności w dalszym ciągu. Różnych obciążeń bazy danych są dynamiczne i zmiany w sposób ciągły. Usługa Azure SQL Database w dalszym ciągu monitorować i przedstawić zalecenia w zakresie, które może potencjalnie podnieść wydajność bazy danych. 

* Zobacz [dostrajania automatycznego](sql-database-automatic-tuning.md) Aby dowiedzieć się więcej na temat dostrajania automatycznego usługi Azure SQL Database.
* Zobacz [zalecenia dotyczące wydajności](sql-database-advisor.md) omówienie zalecenia dotyczące wydajności usługi Azure SQL Database.
* Zobacz [szczegółowe informacje o wydajności zapytań](sql-database-query-performance.md) Aby dowiedzieć się więcej o wyświetlaniu wpływ na wydajność najczęściej używane zapytania.

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Magazyn zapytań](https://msdn.microsoft.com/library/dn817826.aspx)
* [TWORZENIE INDEKSU](https://msdn.microsoft.com/library/ms188783.aspx)
* [Kontrola dostępu oparta na rolach](../role-based-access-control/overview.md)

