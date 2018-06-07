---
title: Modelowanie relacje w projektowaniu tabel usługi Azure storage | Dokumentacja firmy Microsoft
description: Opis procesu modelowania podczas projektowania rozwiązania magazynu tabeli.
services: storage
documentationcenter: na
author: MarkMcGeeAtAquent
manager: kfile
ms.assetid: 8e228b0c-2998-4462-8101-9f16517393ca
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/23/2018
ms.author: sngun
ms.openlocfilehash: 561281375273135009a956fd27dfe9f193ab92ac
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660916"
---
# <a name="modeling-relationships"></a>Relacje modelowania
W tym artykule omówiono proces modelowania, aby ułatwić projektowanie rozwiązań magazynu tabel Azure.

Tworzenie modeli domeny jest klucza krok w projektowaniu złożonych systemów. Zwykle Użyj procesu modelowania do identyfikowania jednostki i relacje między nimi sposób zrozumieć domeny biznesowych i poinformować o podczas projektowania systemu. Tej części przedstawiono sposób niektórych typowych relacji w modelach domen do projektów dla usługi tabel translacji. Proces mapowania z logiczną modelu danych do fizycznego danych NoSQL oparta-modelu różni się od używanego podczas projektowania relacyjnej bazy danych. Projekt relacyjnych baz danych zwykle zakłada proces normalizacji danych zoptymalizowane pod kątem minimalizując nadmiarowość — i deklaratywne możliwość podczas badania, która abstracts jak implementacja jak bazy danych działa.  

## <a name="one-to-many-relationships"></a>Relacje jeden do wielu
Relacje jeden do wielu obiektów domeny biznesowych występować często: na przykład w jednym dziale ma wielu pracowników. Istnieje kilka sposobów do zaimplementowania relacje jeden do wielu tabela usługi każdego z zalet i wad, które mogą być istotne dla konkretnego scenariusza.  

Rozważmy przykład dużych korporacji międzynarodowej z dziesiątkami tysięcy działów i podmiotów pracownika, gdzie każdy dział ma wiele pracowników i pracowników, poszczególnych, jak skojarzony z jednym określonym dziale. Jednym z podejść jest do przechowywania działu oddzielne i jednostek pracownika, takich jak te:  


![Dział oddzielny magazyn i jednostki pracownika](media/storage-table-design-guide/storage-table-design-IMAGE01.png)

W tym przykładzie pokazano niejawnych relacji jeden do wielu między typami na podstawie **PartitionKey** wartości. Każdy dział może mieć wielu pracowników.  

Ten przykład przedstawia również jednostki działu i jego jednostek pokrewnych pracowników w tej samej partycji. Użytkownik może używać różnych partycji, tabel lub kont magazynu nawet dla typów inną jednostkę.  

Informacje o innym podejściu jest denormalize danych i przechowywanie tylko jednostki pracowników z działu nieznormalizowany danych, jak pokazano w poniższym przykładzie. W tym scenariuszu określonego takie podejście nieznormalizowany nie może być najlepiej, jeśli masz wymaganie, aby można było zmienić szczegóły manager działu, ponieważ w tym celu należy zaktualizować każdy pracownik działu.  

![Jednostka pracownika](media/storage-table-design-guide/storage-table-design-IMAGE02.png)

Aby uzyskać więcej informacji, zobacz [wzorzec Denormalization](table-storage-design-patterns.md#denormalization-pattern) dalszej części tego przewodnika.  

Poniższa tabela zawiera podsumowanie zalet i wad każdej z metod opisanych powyżej do przechowywania pracowników i działu obiektów, które mają relacji jeden do wielu. Należy również rozważyć, jak często mają wykonywać różne operacje: może być akceptowane ma projekt, który zawiera kosztowna operacja, jeśli ta operacja odbywa się tylko rzadko.  

<table>
<tr>
<th>Podejście</th>
<th>Specjaliści</th>
<th>Wady</th>
</tr>
<tr>
<td>Oddzielne typy jednostek, tej samej partycji, tej samej tabeli</td>
<td>
<ul>
<li>Można aktualizować jednostek działu z jednej operacji.</li>
<li>EGT służy do zapewniania spójności, jeśli nie można zmodyfikować jednostki działu zawsze, gdy użytkownik aktualizacji/insert/usuwania jednostki pracownika. Na przykład, jeśli liczba pracowników działów, dla każdego działu.</li>
</ul>
</td>
<td>
<ul>
<li>Konieczne może być pobieranie zarówno pracownika, jak i do działu jednostki w przypadku niektórych działań klienta.</li>
<li>Operacje magazynu pojawiają się w tej samej partycji. W transakcji wysokiej woluminów może to spowodować punktu aktywnego.</li>
<li>Nie można przenieść pracownika działu nowe przy użyciu EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Typy oddzielnych jednostek, różnych partycji lub tabel lub kont magazynu</td>
<td>
<ul>
<li>Należy zaktualizować działu jednostki lub jednostek pracowników z jednej operacji.</li>
<li>W transakcji wysokiej woluminów to może pomóc rozłożyć obciążenie na więcej partycji.</li>
</ul>
</td>
<td>
<ul>
<li>Konieczne może być pobieranie zarówno pracownika, jak i do działu jednostki w przypadku niektórych działań klienta.</li>
<li>Nie można użyć EGTs, aby zachować spójność podczas możesz aktualizacji/insert/usuwania pracownik i aktualizacji działu. Na przykład aktualizowania liczba pracowników w jednostce działu.</li>
<li>Nie można przenieść pracownika działu nowe przy użyciu EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Denormalize do pojedynczej jednostki typu</td>
<td>
<ul>
<li>Można pobrać wszystkich informacji potrzebnych z pojedynczym żądaniem.</li>
</ul>
</td>
<td>
<ul>
<li>Może być kosztowne do zapewniania spójności, aby zaktualizować informacje o działu (to wymaga aktualizacji wszystkich pracowników działu).</li>
</ul>
</td>
</tr>
</table>

Jak można wybrać te opcje, a które z zalet i wad są najważniejsze, zależy od scenariuszy określonej aplikacji. Na przykład jak często należy można modyfikować jednostek działu; czy wszystkie zapytania pracowników muszą dodatkowe informacje dla działu; jak blisko czy do limity skalowalności partycji lub Twoje konto magazynu?  

## <a name="one-to-one-relationships"></a>Relacje jeden do jednego
Modele domeny może obejmować jeden do jednego relacje między obiektami. Jeśli musisz wdrożyć relacją w usłudze tabel, musisz wybrać jak połączyć dwóch powiązanych jednostek, gdy trzeba je pobrać. To łącze może być niejawna, oparte na Konwencji w wartości klucza lub jawne, przechowując łącze w formie **PartitionKey** i **RowKey** wartości w każdej jednostki do jego obiektu pokrewnego. Aby uzyskać informacje dotyczące tego, czy należy przechowywać powiązanych jednostek w tej samej partycji, zobacz sekcję [jeden do wielu relacji](#one-to-many-relationships).  

Dostępne są również uwagi dotyczące implementacji, które mogą prowadzić do zaimplementowania relacje jeden do jednego w usłudze tabel:  

* Obsługa dużych jednostek (Aby uzyskać więcej informacji, zobacz [dużych jednostek wzorzec](table-storage-design-patterns.md#large-entities-pattern)).  
* Wdrożenie kontroli dostępu (Aby uzyskać więcej informacji, zobacz [kontrolowanie dostępu przy użyciu sygnatury dostępu współdzielonego](#controlling-access-with-shared-access-signatures)).  

## <a name="join-in-the-client"></a>Dołącz do klienta
Mimo że istnieją sposoby do modelowania relacji w usłudze tabel, należy nie zapomnij, czy dwa główne powody przy użyciu usługi tabel skalowalność i wydajność. Jeśli okaże się, że są modelowania wiele relacji, które negatywnie wpłynąć na wydajność i skalowalność rozwiązania, należy poprosić samodzielnie Jeśli jest niezbędne do utworzenia relacji danych w projekcie tabeli. Można uprościć projektu i zwiększyć skalowalność i wydajność rozwiązania, jeśli umożliwisz aplikacja kliencka wykonać wszelkie niezbędne sprzężenia.  

Na przykład jeśli masz małych tabel, które zawierają dane, które nie zmienia się często, następnie można pobrać te dane raz i pamięci podręcznej go na komputerze klienckim. Można to uniknąć wielokrotnego dwukierunkowe przesyłanie danych do pobrania tych samych danych. W przykładach, które firma Microsoft sprawdzono, w tym przewodniku prawdopodobnie zbiór działów w organizacji małych małe i zmienić rzadko co odpowiednimi kandydatami dla danych, który aplikacja kliencka można pobrać raz i pamięci podręcznej jako wyszukiwania danych.  

## <a name="inheritance-relationships"></a>Relacje dziedziczenia
Jeśli aplikacja kliencka używa zestawu klas, które stanowią część relacji dziedziczenia do reprezentowania jednostki biznesowe, można łatwo utrwalić tych jednostek w usłudze tabel. Przykładowo może istnieć następujący zestaw klas zdefiniowanych w aplikacji klienta gdzie **osoby** jest klasą abstrakcyjną.

![Klasa abstrakcyjna osoby](media/storage-table-design-guide/storage-table-design-IMAGE03.png)

Można ją utrwalić wystąpienia dwóch klas konkretnych w usłudze tabel za pomocą pojedynczej tabeli osoby za pomocą jednostek tego wyglądają następująco:  

![Osoba tabeli](media/storage-table-design-guide/storage-table-design-IMAGE04.png)

Aby uzyskać więcej informacji na temat pracy z wieloma typami jednostek w tej samej tabeli w kodzie klienta, zobacz sekcję [Praca z typami jednostek heterogenicznych](#working-with-heterogeneous-entity-types) dalszej części tego przewodnika. Zapewnia to przykłady rozpoznaje typ jednostki w kodu klienta.  


## <a name="next-steps"></a>Kolejne kroki

- [Wzorce projektowe tabeli](table-storage-design-patterns.md)
- [Projekt do wykonywania zapytań](table-storage-design-for-query.md)
- [Szyfrowanie danych tabeli](table-storage-design-encrypt-data.md)
- [Projekt do modyfikacji danych](table-storage-design-for-modification.md)