---
title: Stosowanie zaleceń dotyczących wydajności
description: Skorzystaj z witryny Azure Portal, aby znaleźć zalecenia dotyczące wydajności, które mogą zoptymalizować wydajność bazy danych SQL azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 12/19/2018
ms.openlocfilehash: b0452d51dc472e100ef52536d8e3814ff395292b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214177"
---
# <a name="find-and-apply-performance-recommendations"></a>Znajdowanie i stosowanie zaleceń dotyczących wydajności

Za pomocą witryny Azure Portal można znaleźć zalecenia dotyczące wydajności, które mogą zoptymalizować wydajność bazy danych SQL platformy Azure lub rozwiązać niektóre problemy zidentyfikowane w obciążeniu. Strona **rekomendacji wydajności** w witrynie Azure portal umożliwia znajdowanie najlepszych zaleceń na podstawie ich potencjalnego wpływu.

## <a name="viewing-recommendations"></a>Wyświetlanie rekomendacji

Aby wyświetlić i zastosować zalecenia dotyczące wydajności, potrzebujesz odpowiednich uprawnień [kontroli dostępu opartych na rolach na](../role-based-access-control/overview.md) platformie Azure. **Czytnik**, **uprawnienia współautora bazy danych SQL** są wymagane do wyświetlania zaleceń, a **właściciel**, **uprawnienia współautora bazy danych SQL** są wymagane do wykonywania wszelkich akcji; tworzenie lub upuszczanie indeksów i anulowanie tworzenia indeksu.

Skorzystaj z następujących kroków, aby znaleźć zalecenia dotyczące wydajności w witrynie Azure portal:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Przejdź do **wszystkich usług** > **baz danych SQL**i wybierz bazę danych.
3. Przejdź do **zalecenia wydajności,** aby wyświetlić dostępne zalecenia dla wybranej bazy danych.

Zalecenia dotyczące wydajności są wyświetlane w tabeli podobnej do przedstawionej na poniższym rysunku:

![Zalecenia](./media/sql-database-advisor-portal/recommendations.png)

Zalecenia są sortowane według ich potencjalnego wpływu na wydajność w następujących kategoriach:

| Wpływ | Opis |
|:--- |:--- |
| Wysoka |Zalecenia dotyczące wysokiego wpływu powinny mieć największy wpływ na wydajność. |
| Medium |Zalecenia dotyczące średniego wpływu powinny poprawić wydajność, ale nie w znacznym stopniu. |
| Małe |Zalecenia o niskim wpływie powinny zapewniać lepszą wydajność niż bez, ale ulepszenia mogą nie być znaczące. |

> [!NOTE]
> Usługa Azure SQL Database musi monitorować działania co najmniej przez jeden dzień, aby zidentyfikować niektóre zalecenia. Usługa Azure SQL Database można łatwiej zoptymalizować pod kątem spójnych wzorców zapytań, niż może dla losowych wyprysków serii aktywności. Jeśli zalecenia nie są obecnie dostępne, strona **zalecenia dotyczące wydajności** zawiera komunikat wyjaśniający, dlaczego.

Można również wyświetlić stan operacji historycznych. Wybierz zalecenie lub stan, aby wyświetlić więcej informacji.

Oto przykład zalecenia "Utwórz indeks" w witrynie Azure portal.

![tworzenie indeksu](./media/sql-database-advisor-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>Stosowanie zaleceń

Usługa Azure SQL Database zapewnia pełną kontrolę nad tym, jak zalecenia są włączone przy użyciu dowolnej z następujących trzech opcji:

* Stosuj indywidualne zalecenia pojedynczo.
* Włącz automatyczne dostrajanie, aby automatycznie stosować zalecenia.
* Aby zaimplementować zalecenie ręcznie, uruchom zalecany skrypt T-SQL względem bazy danych.

Wybierz dowolną rekomendację, aby wyświetlić jego szczegóły, a następnie kliknij przycisk **Wyświetl skrypt,** aby przejrzeć dokładne szczegóły sposobu tworzenia rekomendacji.

Baza danych pozostaje w trybie online, gdy zalecenie jest stosowane — przy użyciu zalecenia dotyczące wydajności lub automatycznego dostrajania nigdy nie przełącza bazy danych w tryb offline.

### <a name="apply-an-individual-recommendation"></a>Stosowanie indywidualnego zalecenia

Rekomendacje można przeglądać i akceptować po jednym naraz.

1. Na stronie **Zalecenia** wybierz zalecenie.
2. Na stronie **Szczegóły** kliknij przycisk **Zastosuj.**

   ![Zastosuj zalecenie](./media/sql-database-advisor-portal/apply.png)

Wybrane zalecenie są stosowane w bazie danych.

### <a name="removing-recommendations-from-the-list"></a>Usuwanie zaleceń z listy

Jeśli lista rekomendacji zawiera elementy, które chcesz usunąć z listy, możesz odrzucić zalecenie:

1. Wybierz zalecenie na liście **Rekomendacje,** aby otworzyć szczegóły.
2. Kliknij **pozycję Odrzuć** na stronie **Szczegóły.**

W razie potrzeby można dodać odrzucone elementy z powrotem do listy **Rekomendacje:**

1. Na stronie **Zalecenia** kliknij pozycję **Wyświetl odrzucone**.
2. Wybierz odrzucony element z listy, aby wyświetlić jego szczegóły.
3. Opcjonalnie kliknij przycisk **Cofnij odrzuć,** aby dodać indeks z powrotem do głównej listy **rekomendacji**.

> [!NOTE]
> Należy pamiętać, że jeśli [funkcja automatycznego dostrajania](sql-database-automatic-tuning.md) bazy danych SQL jest włączona, a zalecenie zostało ręcznie odrzucone z listy, takie zalecenie nigdy nie zostanie zastosowane automatycznie. Odrzucenie zalecenia jest przydatnym sposobem dla użytkowników, aby automatyczne dostrajanie włączone w przypadkach, gdy wymaga, aby określone zalecenie nie powinny być stosowane.
> To zachowanie można przywrócić, dodając odrzucone zalecenia z powrotem do listy Zalecenia, wybierając opcję Cofnij odrzuć.

### <a name="enable-automatic-tuning"></a>Włączanie automatycznego dostrajania

Można ustawić usługi Azure SQL Database, aby automatycznie implementować zalecenia. W miarę udostępniania rekomendacji są one automatycznie stosowane. Podobnie jak w przypadku wszystkich zaleceń zarządzanych przez usługę, jeśli wpływ na wydajność jest negatywny, zalecenie zostanie wycofane.

1. Na stronie **Zalecenia** kliknij pozycję **Automatyzuj**:

   ![Ustawienia doradcy](./media/sql-database-advisor-portal/settings.png)
2. Wybierz akcje do automatyzacji:

   ![Zalecane indeksy](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Należy pamiętać, że **opcja DROP_INDEX** nie jest obecnie zgodna z aplikacjami korzystającymi z przełączania partycji i wskazówek dotyczących indeksu.

Po wybraniu żądanej konfiguracji kliknij przycisk Zastosuj.

### <a name="manually-apply-recommendations-through-t-sql"></a>Ręczne stosowanie zaleceń za pośrednictwem języka T-SQL

Zaznacz dowolną rekomendację, a następnie kliknij pozycję **Wyświetl skrypt**. Uruchom ten skrypt względem bazy danych, aby ręcznie zastosować zalecenie.

*Indeksy, które są wykonywane ręcznie nie są monitorowane i sprawdzane pod kątem wpływu na wydajność przez usługę,* więc sugeruje się, aby monitorować te indeksy po utworzeniu, aby sprawdzić, czy zapewniają wzrost wydajności i dostosować lub usunąć je w razie potrzeby. Aby uzyskać szczegółowe informacje na temat tworzenia indeksów, zobacz [TWORZENIE INDEKSU (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql). Ponadto ręcznie stosowane zalecenia pozostaną aktywne i przedstawione na liście zaleceń na 24-48 godzin. zanim system automatycznie je wycofa. Jeśli chcesz usunąć zalecenie wcześniej, możesz ręcznie je odrzucić.

### <a name="canceling-recommendations"></a>Anulowanie zaleceń

Zalecenia, które znajdują się w **oczekujące,** **Sprawdzanie poprawności**lub **Powodzenie** stanu można anulować. Rekomendacje o stanie **Wykonywanie** nie mogą zostać anulowane.

1. Wybierz zalecenie w obszarze **Historia strojenia,** aby otworzyć stronę **szczegółów rekomendacji.**
2. Kliknij **przycisk Anuluj,** aby przerwać proces stosowania zalecenia.

## <a name="monitoring-operations"></a>Operacje monitorowania

Stosowanie zalecenia może nie nastąpić natychmiast. Portal zawiera szczegółowe informacje dotyczące stanu rekomendacji. Poniżej przedstawiono możliwe stany, w których może znajdować się indeks:

| Stan | Opis |
|:--- |:--- |
| Oczekujące |Zastosuj polecenie zalecenie zostało odebrane i jest zaplanowane do wykonania. |
| Wykonywanie |Zalecenie jest stosowane. |
| Sprawdzanie poprawności |Zalecenie zostało pomyślnie zastosowane, a usługa mierzy korzyści. |
| Powodzenie |Zalecenie zostało pomyślnie zastosowane i zmierzono korzyści. |
| Błąd |Wystąpił błąd podczas procesu stosowania zalecenia. Może to być przejściowy problem lub ewentualnie zmiana schematu do tabeli i skrypt nie jest już prawidłowy. |
| Przywracanie |Zalecenie zostało zastosowane, ale zostało uznane za niekonserwacyjne i jest automatycznie przywracane. |
| Przywrócone |Zalecenie zostało przywrócone. |

Kliknij zalecenie w trakcie procesu z listy, aby wyświetlić więcej informacji:

![Zalecane indeksy](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Przywrócenie zalecenia

Jeśli użyto zalecenia dotyczące wydajności, aby zastosować zalecenie (co oznacza, że nie ręcznie uruchomić skrypt T-SQL), automatycznie przywraca zmiany, jeśli okaże się, że wpływ na wydajność jest ujemny. Jeśli z jakiegoś powodu po prostu chcesz przywrócić zalecenie, możesz wykonać następujące czynności:

1. Wybierz pomyślnie zastosowane zalecenie w obszarze **Historia strojenia.**
2. Kliknij pozycję **Przywróć** na stronie **szczegółów rekomendacji.**

![Zalecane indeksy](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Monitorowanie wpływu zaleceń dotyczących indeksów na wydajność

Po pomyślnym zaimplementowaniu rekomendacji (obecnie tylko operacje indeksowania i parametryzowanie zaleceń dotyczących zapytań) można kliknąć **witrynę Query Insights** na stronie szczegółów rekomendacji, aby otworzyć [statystykę wydajności kwerendy](sql-database-query-performance.md) i wyświetlić wpływ na wydajność najważniejszych zapytań.

![Monitorowanie wpływu na wydajność](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>Podsumowanie

Usługa Azure SQL Database zawiera zalecenia dotyczące poprawy wydajności bazy danych SQL. Zapewniając skrypty T-SQL, można uzyskać pomoc w optymalizacji bazy danych i ostatecznie zwiększenie wydajności kwerendy.

## <a name="next-steps"></a>Następne kroki

Monitoruj zalecenia i kontynuuj stosowanie ich w celu zawężenia wydajności. Obciążenia bazy danych są dynamiczne i zmieniają się w sposób ciągły. Usługa Azure SQL Database nadal monitoruje i dostarcza zalecenia, które mogą potencjalnie poprawić wydajność bazy danych.

* Zobacz [Automatyczne dostrajanie,](sql-database-automatic-tuning.md) aby dowiedzieć się więcej o automatycznym dostrajaniu w bazie danych SQL azure.
* Zobacz [zalecenia dotyczące wydajności,](sql-database-advisor.md) aby zapoznać się z zaleceniami dotyczącymi wydajności usługi Azure SQL Database.
* Zobacz [Statystyki wydajności kwerendy,](sql-database-query-performance.md) aby dowiedzieć się więcej o wyświetlaniu wpływu na wydajność najważniejszych zapytań.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Magazyn zapytań](https://msdn.microsoft.com/library/dn817826.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [Kontrola dostępu oparta na rolach](../role-based-access-control/overview.md)
