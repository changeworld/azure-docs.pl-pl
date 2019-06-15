---
title: Modelowanie relacji w projektowaniu tabel usługi Azure storage | Dokumentacja firmy Microsoft
description: Podczas projektowania rozwiązania magazynu tabeli, należy zrozumieć proces modelowania.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 5d83e61282d2f21a3016997e324d0f58eff15e78
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60502540"
---
# <a name="modeling-relationships"></a>Modelowanie relacji
W tym artykule omówiono proces modelowania, aby ułatwić projektowanie rozwiązania usługi Azure Table storage.

Tworzenie modeli domeny jest krokiem w projekcie złożonych systemów. Zazwyczaj używasz procesie modelowania do identyfikowania jednostek i relacji między nimi jako sposób na zrozumienie domeny biznesowej i natychmiastowego informowania projektu systemu. Ta sekcja koncentruje się na sposób niektóre popularne typy relacji w modeli domeny do projektów usługi tabeli translacji. Proces mapowanie logiczny model danych do fizycznego opartych na NoSQL — model danych różni się od używanej podczas projektowania relacyjnej bazy danych. Projektowania relacyjnych baz danych zwykle zakłada proces normalizacji danych zoptymalizowana pod kątem minimalizując nadmiarowość — i deklaratywne możliwości zapytań, zapewniający abstrakcję jak implementacja jak baza danych działa.  

## <a name="one-to-many-relationships"></a>Relacje jeden do wielu
Relacje jeden do wielu między obiektami domeny biznesowej często występują: na przykład jednego działu ma wielu pracowników. Istnieje kilka sposobów, aby zaimplementować relacji jeden do wielu w usłudze Table service każdego z zalet i wad, które mogą być istotne dla konkretnego scenariusza.  

Rozważmy przykład dużych przedsiębiorstw wielonarodowym dziesiątki tysięcy działów i jednostki pracowników, gdzie każdy dział ma wielu pracowników i każdego pracownika, jako skojarzone z określonym dziale. Jednym z podejść jest do przechowywania działu oddzielne i pracowników jednostki, takie jak te:  


![Działu oddzielne Store i jednostek pracownika](media/storage-table-design-guide/storage-table-design-IMAGE01.png)

Ten przykład przedstawia niejawnych relacji jeden do wielu między typami, na podstawie **PartitionKey** wartości. Każdy dział może mieć wielu pracowników.  

Ten przykład pokazuje także jednostki działu i jego jednostek powiązanych pracowników w tej samej partycji. Można korzystać z różnych partycji, tabel lub kont magazynu nawet dla typów innej jednostki.  

Alternatywnym podejściem jest zdenormalizowanie danych i przechowywać tylko jednostki pracowników z działu nieznormalizowany danych, jak pokazano w poniższym przykładzie. W tym scenariuszu to nieznormalizowanym podejściu nie może być najlepiej, jeśli masz wymaganie, aby można było zmienić szczegóły kierownik działu, ponieważ w tym celu należy zaktualizować każdy pracownik działu.  

![Jednostka pracownika](media/storage-table-design-guide/storage-table-design-IMAGE02.png)

Aby uzyskać więcej informacji, zobacz [wzorzec Denormalizacja](table-storage-design-patterns.md#denormalization-pattern) później w tym przewodniku.  

Poniższa tabela zawiera podsumowanie zalet i wad każdej z metod opisanych powyżej do przechowywania pracowników i obiektów działu, które mają relacje jeden do wielu. Należy również rozważyć, jak często chcą wykonywać różne operacje: może być akceptowalne, aby projekt, który zawiera kosztowna operacja, jeśli ta operacja odbywa się tylko rzadko.  

<table>
<tr>
<th>Podejście</th>
<th>Specjaliści</th>
<th>Wady</th>
</tr>
<tr>
<td>Oddziel typów jednostek, tę samą partycję, w tej samej tabeli</td>
<td>
<ul>
<li>Należy zaktualizować jednostki działu, przy użyciu jednej operacji.</li>
<li>EGT służy do zapewniania spójności, jeśli masz wymaganie, aby zmodyfikować jednostki działu zawsze wtedy, gdy użytkownik aktualizacji/insert/usuwania jednostki pracowników. Na przykład, jeśli to Ty masz liczba pracowników działów dla każdego działu.</li>
</ul>
</td>
<td>
<ul>
<li>Może być konieczne pobrać jednostki działu, w przypadku niektórych działań klienta i pracownika.</li>
<li>Magazyn — liczba operacji pojawiają się w tej samej partycji. Na woluminach dużo transakcji może to spowodować punkt największej aktywności.</li>
<li>Nie można przenieść pracownika nowy dział przy użyciu EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Typy osobne jednostki, różne partycje lub tabele lub konta magazynu</td>
<td>
<ul>
<li>Należy zaktualizować jednostki działu lub pracowników przy użyciu jednej operacji.</li>
<li>Na woluminach dużo transakcji może to pomóc rozłożenie obciążenia na więcej partycji.</li>
</ul>
</td>
<td>
<ul>
<li>Może być konieczne pobrać jednostki działu, w przypadku niektórych działań klienta i pracownika.</li>
<li>Nie można użyć EGTs, aby zachować spójność po użytkownik aktualizacji/insert/delete pracownika i zaktualizuj dział. Na przykład aktualizowanie liczba pracowników w jednostce działu.</li>
<li>Nie można przenieść pracownika nowy dział przy użyciu EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Denormalizowanie w pojedynczej jednostki typu</td>
<td>
<ul>
<li>Możesz pobrać wszystkie informacje potrzebne przy użyciu pojedynczego żądania.</li>
</ul>
</td>
<td>
<ul>
<li>Może to być drogie w utrzymaniu spójności, jeśli konieczne zaktualizowanie informacji działu (wymagałoby to zaktualizowania wszystkich pracowników działu).</li>
</ul>
</td>
</tr>
</table>

Jak wybrać między te opcje, a które z zalet i wad są najbardziej istotne, zależy od Twoich scenariuszy określonej aplikacji. Na przykład jak często należy możesz modyfikować jednostek działu; czy wszystkie zapytania pracowników muszą dodatkowe informacje dla działu; jak blisko czy limitów skalowalności na partycji lub konta magazynu?  

## <a name="one-to-one-relationships"></a>Relacje jeden do jednego
Modeli domeny może zawierać jeden do jednego relacje między jednostkami. Musisz zaimplementować relacja jeden do jednego w usłudze Table service, należy także wybrać jak połączyć dwie jednostki powiązane, gdy trzeba pobrać ich obu. Ten link może być niejawne, zależnie od Konwencji w wartościach klucza lub jawne, przechowując link w formie **PartitionKey** i **RowKey** wartości w każdej jednostki do jego powiązanej jednostki. Aby uzyskać omówienie tego, czy należy przechowywać powiązanych jednostek w tej samej partycji, zobacz sekcję [jeden do wielu relacji](#one-to-many-relationships).  

Dostępne są również istotne informacje dotyczące implementacji, które mogą prowadzić do zaimplementowania relacji jeden do jednego w usłudze Table service:  

* Obsługa dużych jednostek (Aby uzyskać więcej informacji, zobacz [dużych jednostek wzorzec](table-storage-design-patterns.md#large-entities-pattern)).  
* Implementowanie kontroli dostępu (Aby uzyskać więcej informacji, zobacz Kontrola dostępu za pomocą sygnatur dostępu współdzielonego).  

## <a name="join-in-the-client"></a>Dołącz do klienta
Mimo że istnieją sposoby relacje modelu w usłudze Table service, należy nie zapominać, że dwie główne przyczyny za pomocą usługi Table jest skalowalność i wydajność. Jeśli okaże się, że są modelowania wiele relacji, które negatywnie wpłynąć na wydajność i skalowalność rozwiązania, należy poprosić samodzielnie Jeśli jest niezbędne do utworzenia relacji danych w projekcie tabeli. Można Uproszczenie projektowania i poprawy skalowalności i wydajności rozwiązania, jeśli zezwolisz aplikacji klienckiej wykonać wszelkie niezbędne sprzężenia.  

Na przykład jeśli masz małe tabele, które zawierają dane, która nie zmienia się często, następnie można pobrać te dane jeden raz i Buforuj go na komputerze klienckim. To uniknąć wielokrotnego natężenie ruchu do pobrania tych samych danych. W przykładach, które mają przyjrzeliśmy się w tym przewodniku zbiór działów w organizacji małych prawdopodobnie jest mały i zmienić rzadko co dobrym kandydatem do danych, które aplikacja kliencka może pobrać raz i pamięci podręcznej jako wyszukiwania danych.  

## <a name="inheritance-relationships"></a>Relacje dziedziczenia
Jeśli Twoja aplikacja kliencka korzysta z zestawu klas, które stanowią część relacji dziedziczenia do reprezentowania jednostek biznesowych, można łatwo utrwalić tych jednostek w usłudze Table service. Na przykład może być przedstawiony poniżej zestaw klas zdefiniowanych w aplikacji klienckiej gdzie **osoby** jest klasą abstrakcyjną.

![Abstrakcyjna klasa osoby](media/storage-table-design-guide/storage-table-design-IMAGE03.png)

Jednak można utrwalić wystąpieniami dwóch klas konkretnych w usłudze Table service, za pomocą pojedynczej tabeli osoby za pomocą jednostek w tym wyglądał następująco:  

![Tabela osoby](media/storage-table-design-guide/storage-table-design-IMAGE04.png)

Aby uzyskać więcej informacji na temat pracy z wieloma typami jednostki w tej samej tabeli, w kodzie klienta, zobacz sekcję Praca z typami encji heterogenicznych w dalszej części tego przewodnika. Zapewnia to przykłady sposobu rozpoznaje typ jednostki w kodzie klienta.  


## <a name="next-steps"></a>Kolejne kroki

- [Wzorce projektowe oparte na tabelę](table-storage-design-patterns.md)
- [Projektowanie pod kątem zapytań](table-storage-design-for-query.md)
- [Szyfrowanie danych w tabelach](table-storage-design-encrypt-data.md)
- [Projektowanie pod kątem modyfikacji danych](table-storage-design-for-modification.md)
