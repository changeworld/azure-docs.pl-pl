---
title: Modelowanie relacji w projekcie usługi Azure Table Storage | Dokumenty firmy Microsoft
description: Poznaj proces modelowania podczas projektowania rozwiązania do przechowywania tabel.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 25082c107fbc0feeb533aa2b4fc56cff960e778d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457567"
---
# <a name="modeling-relationships"></a>Modelowanie relacji
W tym artykule omówiono proces modelowania, aby ułatwić projektowanie rozwiązań magazynu tabel platformy Azure.

Tworzenie modeli domen jest kluczowym krokiem w projektowaniu złożonych systemów. Zazwyczaj proces modelowania służy do identyfikowania jednostek i relacji między nimi jako sposób na zrozumienie domeny biznesowej i poinformowanie o projekcie systemu. W tej sekcji skupiono się na tym, jak można przetłumaczyć niektóre typowe typy relacji znalezione w modelach domen na projekty usługi tabel. Proces mapowania z logicznego modelu danych do fizycznego modelu danych opartego na nosql różni się od procesu używanego podczas projektowania relacyjnej bazy danych. Projekt relacyjnych baz danych zazwyczaj zakłada proces normalizacji danych zoptymalizowany pod kątem minimalizacji nadmiarowości i deklaratywną funkcję wykonywania zapytań, która abstrakcji jak implementacja jak działa bazy danych.  

## <a name="one-to-many-relationships"></a>Relacje jeden-do-wielu
Relacje jeden-do-wielu między obiektami domeny biznesowej występują często: na przykład jeden dział ma wielu pracowników. Istnieje kilka sposobów, aby zaimplementować relacje jeden do wielu w usłudze tabeli każdy z zalet i wad, które mogą być istotne dla określonego scenariusza.  

Rozważmy przykład dużej korporacji wielonarodowej z dziesiątkami tysięcy działów i jednostek pracowniczych, w których każdy dział ma wielu pracowników, a każdy pracownik jest związany z jednym konkretnym działem. Jednym z podejść jest przechowywanie oddzielnych jednostek działu i pracowników, takich jak te:  


![Oddzielne działy i jednostki pracowników sklepu](media/storage-table-design-guide/storage-table-design-IMAGE01.png)

W tym przykładzie pokazano niejawną relację jeden do wielu między typami na podstawie **partitionKey** wartości. Każdy dział może mieć wielu pracowników.  

W tym przykładzie pokazano również jednostki działu i jego powiązanych jednostek pracowników w tej samej partycji. Można użyć różnych partycji, tabel, a nawet kont magazynu dla różnych typów jednostek.  

Alternatywnym podejściem jest denormalize danych i przechowywania tylko jednostek pracowników z zdezormalizowane dane działu, jak pokazano w poniższym przykładzie. W tym konkretnym scenariuszu to zdenormalizowane podejście może nie być najlepsze, jeśli masz wymóg, aby móc zmienić szczegóły kierownika działu, ponieważ w tym celu należy zaktualizować każdego pracownika w dziale.  

![Jednostka pracownika](media/storage-table-design-guide/storage-table-design-IMAGE02.png)

Aby uzyskać więcej informacji, zobacz [wzorzec denormalizacji](table-storage-design-patterns.md#denormalization-pattern) w dalszej części tego przewodnika.  

W poniższej tabeli podsumowano zalety i minusy każdego z rozwiązań opisanych powyżej w celu przechowywania jednostek pracowników i działów, które mają relację jeden do wielu. Należy również wziąć pod uwagę, jak często można oczekiwać, aby wykonać różne operacje: może być dopuszczalne, aby mieć projekt, który zawiera kosztowną operację, jeśli ta operacja występuje rzadko.  

<table>
<tr>
<th>Podejście</th>
<th>Zalety</th>
<th>Wady</th>
</tr>
<tr>
<td>Oddzielne typy jednostek, ta sama partycja, ta sama tabela</td>
<td>
<ul>
<li>Można zaktualizować jednostkę działu za pomocą jednej operacji.</li>
<li>Za pomocą EUWT można zachować spójność, jeśli masz obowiązek modyfikowania jednostki działu przy każdej aktualizacji/wstawiania/usuwania jednostki pracownika. Na przykład, jeśli zachowasz liczbę pracowników działów dla każdego działu.</li>
</ul>
</td>
<td>
<ul>
<li>Może być konieczne pobranie zarówno pracownika, jak i jednostki działu dla niektórych działań klienta.</li>
<li>Operacje magazynowania odbywa się w tej samej partycji. Przy dużych wolumenach transakcji może to spowodować hotspot.</li>
<li>Nie można przenieść pracownika do nowego działu przy użyciu EUWT.</li>
</ul>
</td>
</tr>
<tr>
<td>Oddzielne typy jednostek, różne partycje lub tabele lub konta magazynu</td>
<td>
<ul>
<li>Można zaktualizować jednostkę działu lub jednostkę pracownika za pomocą jednej operacji.</li>
<li>Przy dużych wolumenach transakcji może to pomóc rozłożyć obciążenie na więcej partycji.</li>
</ul>
</td>
<td>
<ul>
<li>Może być konieczne pobranie zarówno pracownika, jak i jednostki działu dla niektórych działań klienta.</li>
<li>Egt nie można używać do zachowania spójności podczas aktualizowania/wstawiania/usuwania pracownika i aktualizowania działu. Na przykład aktualizowanie liczby pracowników w encji działu.</li>
<li>Nie można przenieść pracownika do nowego działu przy użyciu EUWT.</li>
</ul>
</td>
</tr>
<tr>
<td>Denormalize do pojedynczego typu jednostki</td>
<td>
<ul>
<li>Wszystkie potrzebne informacje można pobrać za pomocą jednego żądania.</li>
</ul>
</td>
<td>
<ul>
<li>Utrzymanie spójności może być kosztowne, jeśli konieczne jest zaktualizowanie informacji o dziale (wymagałoby to aktualizacji wszystkich pracowników w dziale).</li>
</ul>
</td>
</tr>
</table>

Sposób wyboru między tymi opcjami i które z zalet i wad są najbardziej znaczące, zależy od konkretnych scenariuszy aplikacji. Na przykład, jak często modyfikujesz jednostki działu; wykonaj wszystkie zapytania pracowników potrzebują dodatkowych informacji działowych; jak blisko jesteś do limitów skalowalności na partycjach lub koncie magazynu?  

## <a name="one-to-one-relationships"></a>Relacje jeden-do-jednego
Modele domeny mogą zawierać relacje jeden-do-jednego między jednostkami. Jeśli trzeba zaimplementować relację jeden do jednego w usłudze tabel, należy również wybrać sposób łączenia dwóch powiązanych jednostek, gdy trzeba pobrać je zarówno. To łącze może być niejawne, oparte na konwencji w wartościach klucza lub jawne przez przechowywanie łącza w postaci **PartitionKey** i **RowKey** wartości w każdej jednostce do jej jednostki pokrewnej. Aby zapoznać się z kwestią, czy encje pokrewne należy [przechowywać](#one-to-many-relationships)w tej samej partycji, zobacz sekcję Relacje jeden do wielu .  

Istnieją również zagadnienia dotyczące implementacji, które mogą prowadzić do zaimplementowania relacji jeden-do-jednego w usłudze tabeli:  

* Obsługa dużych elementów (aby uzyskać więcej informacji, zobacz [Wzorzec dużych elementów).](table-storage-design-patterns.md#large-entities-pattern)  
* Implementowanie kontroli dostępu (aby uzyskać więcej informacji, zobacz Kontrolowanie dostępu za pomocą podpisów dostępu współdzielonego).  

## <a name="join-in-the-client"></a>Dołącz do klienta
Chociaż istnieją sposoby modelowania relacji w usłudze tabel, nie należy zapominać, że dwa główne powody korzystania z usługi tabeli są skalowalność i wydajność. Jeśli okaże się, że modelowanie wielu relacji, które zagrażają wydajności i skalowalności rozwiązania, należy zadać sobie pytanie, czy jest to konieczne do utworzenia wszystkich relacji danych w projekcie tabeli. Możesz uprościć projekt i poprawić skalowalność i wydajność rozwiązania, jeśli pozwolisz aplikacji klienckiej wykonać wszelkie niezbędne sprzężenia.  

Na przykład jeśli masz małe tabele, które zawierają dane, które nie zmieniają się często, następnie można pobrać te dane raz i pamięci podręcznej na kliencie. Może to uniknąć powtarzających się objazdów, aby pobrać te same dane. W przykładach przyjrzeliśmy się w tym przewodniku, zestaw działów w małej organizacji może być mały i zmieniać rzadko co czyni go dobrym kandydatem dla danych, które aplikacja kliencka można pobrać raz i pamięci podręcznej jako wyszukiwania danych.  

## <a name="inheritance-relationships"></a>Relacje dziedziczenia
Jeśli aplikacja kliencka używa zestawu klas, które stanowią część relacji dziedziczenia do reprezentowania jednostek biznesowych, można łatwo utrwalić te jednostki w usłudze tabel. Na przykład może mieć następujący zestaw klas zdefiniowanych w aplikacji klienckiej, gdzie **Osoba** jest klasą abstrakcyjną.

![Klasa osoba abstrakcyjna](media/storage-table-design-guide/storage-table-design-IMAGE03.png)

Można utrwalić wystąpienia dwóch konkretnych klas w table service przy użyciu pojedynczej person tabeli przy użyciu jednostek w tym wyglądać następująco:  

![Tabela osób](media/storage-table-design-guide/storage-table-design-IMAGE04.png)

Aby uzyskać więcej informacji na temat pracy z wieloma typami jednostek w tej samej tabeli w kodzie klienta, zobacz sekcję Praca z heterogenicznych typów jednostek w dalszej części tego przewodnika. Zawiera przykłady rozpoznawania typu jednostki w kodzie klienta.  


## <a name="next-steps"></a>Następne kroki

- [Wzorce projektowe tabel](table-storage-design-patterns.md)
- [Projektowanie pod kątem wykonywania zapytań](table-storage-design-for-query.md)
- [Szyfruj dane tabeli](table-storage-design-encrypt-data.md)
- [Projektowanie pod kątem modyfikacji danych](table-storage-design-for-modification.md)
