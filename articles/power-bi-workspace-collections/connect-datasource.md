---
title: Połączenie ze źródłem danych w kolekcjach obszaru roboczego programu Power BI | Dokumentacja firmy Microsoft
description: Dowiedz się, jak połączyć się ze źródłem danych w kolekcjach obszaru roboczego programu Power BI.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ROBOTS: NOINDEX
ms.assetid: 2a4caeb3-255d-4215-9554-0ca8e3568c13
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 5a154c4899db974645bb7ade028d8bd8f267aad7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="connect-to-a-data-source"></a>Łączenie ze źródłem danych

Z **Power BI obszaru roboczego kolekcje**, raporty można osadzić w aplikacji. Po osadzeniu raportu usługi Power BI w swojej aplikacji raport łączy się z danych przez **importowania** kopii danych lub przez **połączenie bezpośrednio** do źródła danych przy użyciu **DirectQuery**.

> [!IMPORTANT]
> Kolekcje obszarów roboczych usługi Power BI są przestarzałe i będą dostępne do czerwca 2018 roku lub do daty podanej w kontrakcie. Zachęcamy do zaplanowania migracji do usługi Power BI Embedded, aby uniknąć przerw w działaniu aplikacji. Aby uzyskać informacje dotyczące sposobu przeprowadzenia migracji danych do usługi Power BI Embedded, zobacz [How to migrate Power BI Workspace Collections content to Power BI Embedded (Migrowanie zawartości kolekcji obszarów roboczych usługi Power BI do usługi Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Poniżej przedstawiono różnice między **importem** a **trybem DirectQuery**.

| Import | Tryb DirectQuery |
| --- | --- |
| Tabele, kolumny, *i dane* są importowane lub kopiowane do zestawu danych raportu. Aby wyświetlić zmiany, które wystąpiły w danych źródłowych, należy odświeżyć lub zaimportować pełny bieżący zestaw danych ponownie. |Tylko *tabel i kolumn* są importowane lub kopiowane do zestawu danych raportu. Zawsze wyświetlić najbardziej aktualne dane. |

Z kolekcjami obszaru roboczego programu Power BI możesz za pomocą zapytania bezpośredniego źródeł danych w chmurze, ale nie lokalnych źródeł danych w tej chwili.

> [!NOTE]
> Brama danych lokalnego nie jest obsługiwana w tej chwili z kolekcjami obszaru roboczego programu Power BI. Oznacza to, że nie można użyć zapytania bezpośredniego z lokalnych źródeł danych.

## <a name="supported-data-sources"></a>Obsługiwane źródła danych

**Zapytania bezpośredniego**
* Baza danych SQL Azure
* Azure SQL Data Warehouse

**Importowanie**

Możesz zaimportować przy użyciu wszystkich dostępnych źródeł danych w ramach Power BI Desktop. Będzie **nie** można odświeżyć danych w kolekcjach obszaru roboczego programu Power BI. Należy przekazać zmiany do pliku PBIX do kolekcji obszaru roboczego programu Power BI. Jest to spowodowane dostępnej bramy. 

## <a name="benefits-of-using-directquery"></a>Zalety używania zapytania bezpośredniego

Istnieją dwie podstawowe zalety, korzystając z **DirectQuery**:

* **Zapytania bezpośredniego** umożliwiają tworzenie wizualizacji w dużych zestawów danych, gdzie w przeciwnym razie byłoby będzie niemożliwe, aby najpierw należy zaimportować wszystkie dane.
* Bazowy zmian danych może wymagać odświeżenia danych, a w przypadku niektórych raportów potrzeba, aby wyświetlić bieżące dane mogą wymagać dużych transferów danych, co ponownie zaimportować danych będzie niemożliwe. Z kolei **DirectQuery** raporty zawsze używać bieżących danych.

## <a name="limitations-of-directquery"></a>Ograniczenia DirectQuery

Istnieje kilka ograniczeń dotyczących używania **DirectQuery**:

* Wszystkie tabele muszą pochodzić z jednej bazy danych.
* Jeśli zapytanie jest zbyt skomplikowane, wystąpi błąd. Aby rozwiązać ten błąd musi Refaktoryzuj zapytanie, jest mniej złożona. Jeśli zapytanie musi być skomplikowane, należy zaimportować dane zamiast **DirectQuery**.
* Filtrowanie relacji jest ograniczone do jednego kierunku, a nie w obu kierunkach.
* Nie można zmienić typu danych kolumny.
* Domyślnie ograniczenia są umieszczane na dozwolone w miarach wyrażenia języka DAX. Zobacz [zapytania bezpośredniego i środków](#measures).

<a name="measures"/>

## <a name="directquery-and-measures"></a>Zapytania bezpośredniego i miary
Upewnij się, że akceptowalną wydajność kwerend wysyłanych do źródła danych, są ograniczenia nałożone na miary. Korzystając z **Power BI Desktop**zaawansowanego użytkownicy mogą wybrać obejść to ograniczenie, wybierając **Plik > Opcje i Ustawienia > Opcje**. W **opcje** okno dialogowe, wybierz **DirectQuery**i wybierz opcję **Zezwalaj na nieograniczoną środki w trybie zapytania bezpośredniego**. Gdy ta opcja jest wybrana, można dowolne wyrażenie języka DAX, który jest prawidłowy dla miary. Użytkownicy muszą być świadome; jednak, czy niektóre wyrażeń, które również wykonać po zaimportowaniu danych może spowodować w granicach wolnej kwerend do wewnętrznej bazy danych w czasie źródła **zapytania bezpośredniego** tryb. 

## <a name="see-also"></a>Zobacz też

* [Wprowadzenie do programu Microsoft Power BI obszaru roboczego kolekcje](get-started.md)
* [Program Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)

Masz więcej pytań? [Dołącz do społeczności użytkowników usługi Power BI](http://community.powerbi.com/)

