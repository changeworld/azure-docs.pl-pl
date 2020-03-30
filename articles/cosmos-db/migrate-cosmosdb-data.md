---
title: Migrowanie setek terabajtów danych do usługi Azure Cosmos DB
description: Ten doc opisuje, jak można migrować 100s terabajtów danych do usługi Cosmos DB
author: bharathsreenivas
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 69b400eb7838c986ac6f275da58c7457179ebea6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72880200"
---
# <a name="migrate-hundreds-of-terabytes-of-data-into-azure-cosmos-db"></a>Migrowanie setek terabajtów danych do usługi Azure Cosmos DB 

Usługa Azure Cosmos DB może przechowywać terabajty danych. Można przeprowadzić migrację danych na dużą skalę, aby przenieść obciążenie produkcyjne do usługi Azure Cosmos DB. W tym artykule opisano wyzwania związane z przenoszeniem danych na dużą skalę do usługi Azure Cosmos DB oraz przedstawiono narzędzie, które pomaga sprostać tym wyzwaniom i migruje dane do usługi Azure Cosmos DB. W tej analizie przypadku klient użył interfejsu SQL API usługi Cosmos DB.  

Przed migracją całego obciążenia do usługi Azure Cosmos DB można przeprowadzić migrację podzbioru danych, aby sprawdzić poprawność niektórych aspektów, takich jak wybór klucza partycji, wydajność zapytań i modelowanie danych. Po sprawdzeniu weryfikacji koncepcji można przenieść całe obciążenie do usługi Azure Cosmos DB.  

## <a name="tools-for-data-migration"></a>Narzędzia do migracji danych 

Strategie migracji usługi Azure Cosmos DB różnią się obecnie na podstawie wyboru interfejsu API i rozmiaru danych. Aby przeprowadzić migrację mniejszych zestawów danych — do sprawdzania poprawności modelowania danych, [Data Migration Tool](import-data.md) wydajności zapytań, wyboru klucza partycji itp. [Azure Data Factory’s Azure Cosmos DB connector](../data-factory/connector-azure-cosmos-db.md) Jeśli znasz spark, można również użyć [łącznika platformy Azure Cosmos DB Spark](spark-connector.md) do migracji danych.

## <a name="challenges-for-large-scale-migrations"></a>Wyzwania związane z migracjami na dużą skalę 

Istniejące narzędzia do migracji danych do usługi Azure Cosmos DB mają pewne ograniczenia, które stają się szczególnie widoczne na dużą skalę:

 * **Ograniczone możliwości skalowania w poziomie:** Aby jak najszybciej przeprowadzić migrację terabajtów danych do usługi Azure Cosmos DB i skutecznie korzystać z całej aprowizowanej przepływności klienci migracji powinni mieć możliwość skalowania w poziomie przez czas nieokreślony.  

* **Brak śledzenia postępu i wskażania kontroli:** Ważne jest, aby śledzić postęp migracji i mieć punkt kontrolny podczas migracji dużych zestawów danych. W przeciwnym razie każdy błąd, który występuje podczas migracji zatrzyma migrację i trzeba rozpocząć proces od podstaw. Ponowne uruchomienie całego procesu migracji nie byłoby produktywne, gdy 99% z nich zostało już ukończonych.  

* **Brak kolejki utraconych wiadomości:** W dużych zestawach danych w niektórych przypadkach mogą wystąpić problemy z częściami danych źródłowych. Ponadto mogą wystąpić przejściowe problemy z klientem lub siecią. Każdy z tych przypadków nie powinien powodować niepowodzenie całej migracji. Mimo że większość narzędzi migracji ma niezawodne możliwości ponawiania prób, które chronią przed sporadyczne problemy, nie zawsze jest wystarczające. Na przykład jeśli mniej niż 0,01% dokumentów danych źródłowych są większe niż 2 MB w rozmiarze, spowoduje to, że zapis dokumentu zakończy się niepowodzeniem w usłudze Azure Cosmos DB. W idealnym przypadku narzędzie do migracji może utrwalić te "nieudane" dokumenty do innej kolejki utraconych wiadomości, które mogą być przetwarzane po migracji. 

Wiele z tych ograniczeń są stałe dla narzędzi, takich jak fabryka danych platformy Azure, usługi migracji danych platformy Azure. 

## <a name="custom-tool-with-bulk-executor-library"></a>Niestandardowe narzędzie z biblioteką zbiorczą executor 

Wyzwania opisane w powyższej sekcji można rozwiązać za pomocą niestandardowego narzędzia, które można łatwo skalować w wielu wystąpieniach i jest odporne na przejściowe awarie. Ponadto narzędzie niestandardowe można wstrzymać i wznowić migrację w różnych punktach kontrolnych. Usługa Azure Cosmos DB udostępnia już [zbiorczą bibliotekę executora,](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview) która zawiera niektóre z tych funkcji. Na przykład biblioteka executora zbiorczego ma już funkcję obsługi błędów przejściowych i można skalować wątki w poziomie w jednym węźle, aby zużywać około 500 jednostek RUs K na węzeł. Biblioteka executora zbiorczego również partycje zestawu danych źródłowych do mikro-partii, które są obsługiwane niezależnie jako forma punktu kontrolnego.  

Narzędzie niestandardowe używa biblioteki executor zbiorczej i obsługuje skalowanie w poziomie wielu klientów i śledzić błędy podczas procesu pozyskiwania. Aby użyć tego narzędzia, dane źródłowe powinny być podzielone na różne pliki w usłudze Azure Data Lake Storage (ADLS), aby różni pracownicy migracji mogli odebrać każdy plik i pozyskiwać je do usługi Azure Cosmos DB. Narzędzie niestandardowe korzysta z oddzielnej kolekcji, która przechowuje metadane dotyczące postępu migracji dla każdego pliku źródłowego w ADLS i śledzi wszelkie błędy skojarzone z nimi.  

Na poniższej ilustracji opisano proces migracji przy użyciu tego narzędzia niestandardowego. Narzędzie jest uruchomione na zestawie maszyn wirtualnych, a każda maszyna wirtualna wysyła zapytanie do kolekcji śledzenia w usłudze Azure Cosmos DB w celu uzyskania dzierżawy na jednej ze źródłowych partycji danych. Po wykonaniu tej funkcji partycja danych źródłowych jest odczytywana przez narzędzie i pozyskiwana w usłudze Azure Cosmos DB przy użyciu biblioteki zbiorczego executora. Następnie kolekcja śledzenia jest aktualizowana w celu rejestrowania postępu pozyskiwania danych i napotkanych błędów. Po przetworzeniu partycji danych narzędzie próbuje zbadać następną dostępną partycję źródłową. Kontynuuje przetwarzanie następnej partycji źródłowej, dopóki wszystkie dane nie zostaną zmigrowane. Kod źródłowy narzędzia jest dostępny [tutaj](https://github.com/Azure-Samples/azure-cosmosdb-bulkingestion).  

 
![Konfiguracja narzędzia migracji](./media/migrate-cosmosdb-data/migrationsetup.png)
 

 

Kolekcja śledzenia zawiera dokumenty, jak pokazano w poniższym przykładzie. Zobaczysz takie dokumenty po jednym dla każdej partycji w danych źródłowych.  Każdy dokument zawiera metadane dla źródłowej partycji danych, takie jak jej lokalizacja, stan migracji i błędy (jeśli występują):  

```json
{ 
  "owner": "25812@bulkimporttest07", 
  "jsonStoreEntityImportResponse": { 
    "numberOfDocumentsReceived": 446688, 
    "isError": false, 
    "totalRequestUnitsConsumed": 3950252.2800000003, 
    "errorInfo": [], 
    "totalTimeTakenInSeconds": 188, 
    "numberOfDocumentsImported": 446688 
  }, 
  "storeType": "AZURE_BLOB", 
  "name": "sourceDataPartition", 
  "location": "sourceDataPartitionLocation", 
  "id": "sourceDataPartitionId", 
  "isInProgress": false, 
  "operation": "unpartitioned-writes", 
  "createDate": { 
    "seconds": 1561667225, 
    "nanos": 146000000 
  }, 
  "completeDate": { 
    "seconds": 1561667515, 
    "nanos": 180000000 
  }, 
  "isComplete": true 
} 
```
 

## <a name="prerequisites-for-data-migration"></a>Wymagania wstępne dotyczące migracji danych 

Przed rozpoczęciem migracji danych istnieje kilka wymagań wstępnych do rozważenia:  

#### <a name="estimate-the-data-size"></a>Oszacuj rozmiar danych:  

Rozmiar danych źródłowych może nie dokładnie mapować rozmiar danych w usłudze Azure Cosmos DB. Kilka przykładowych dokumentów ze źródła można wstawić, aby sprawdzić ich rozmiar danych w usłudze Azure Cosmos DB. W zależności od przykładowego rozmiaru dokumentu można oszacować całkowity rozmiar danych w usłudze Azure Cosmos DB po migracji. 

Na przykład jeśli każdy dokument po migracji w usłudze Azure Cosmos DB wynosi około 1 KB i jeśli istnieje około 60 miliardów dokumentów w źródłowym zestawie danych, oznaczałoby to, że szacowany rozmiar w usłudze Azure Cosmos DB będzie bliski 60 TB. 

 

#### <a name="pre-create-containers-with-enough-rus"></a>Wstępnie utworzyć pojemniki z wystarczającą ilością uprzedów: 

Mimo że usługa Azure Cosmos DB automatycznie skaluje magazyn w poziomie, nie zaleca się uruchamiania od najmniejszego rozmiaru kontenera. Mniejsze kontenery mają niższą dostępność przepływności, co oznacza, że migracja potrwa znacznie dłużej. Zamiast tego jest przydatne do tworzenia kontenerów z ostatecznego rozmiaru danych (zgodnie z szacunkami w poprzednim kroku) i upewnij się, że obciążenie migracji jest w pełni zużywa aprowizowaną przepływność.  

W poprzednim kroku. ponieważ rozmiar danych został oszacowany na około 60 TB, kontener o wartości co najmniej 2,4 mln ru jest wymagany do uwzględnienia całego zestawu danych.  

 

#### <a name="estimate-the-migration-speed"></a>Oszacuj szybkość migracji: 

Przy założeniu, że obciążenie migracji może zużywać całą aprowizowaną przepływność, aprowizacja w całej zapewni oszacowanie szybkości migracji. Kontynuując poprzedni przykład, 5 procesorów RU są wymagane do pisania dokumentu 1 KB do konta interfejsu API sql usługi Azure Cosmos DB.  2,4 mln jednostek RU pozwoliłoby na przeniesienie 480 000 dokumentów na sekundę (lub 480 MB/s). Oznacza to, że całkowita migracja 60 TB zajmie 125 000 sekund lub około 34 godzin.  

W przypadku, gdy chcesz migracji, które mają zostać zakończone w ciągu jednego dnia, należy zwiększyć aprowizowaną przepływność do 5 milionów jednostek RU. 

 

#### <a name="turn-off-the-indexing"></a>Wyłącz indeksowanie:  

Ponieważ migracja powinna zostać zakończona tak szybko, jak to możliwe, zaleca się zminimalizowanie czasu i czasu pracy na procesorach RUs wydanych na tworzenie indeksów dla każdego z przynajmowanych dokumentów.  Usługa Azure Cosmos DB automatycznie indeksuje wszystkie właściwości, warto zminimalizować indeksowanie do wybranych kilku terminów lub całkowicie wyłączyć go w trakcie migracji. Można wyłączyć zasady indeksowania kontenera, zmieniając indexingMode na brak, jak pokazano poniżej:  

 
```
  { 
        "indexingMode": "none" 
  } 
```
 

Po zakończeniu migracji można zaktualizować indeksowanie.  

## <a name="migration-process"></a>Proces migracji 

Po zakończeniu wymagań wstępnych można migrować dane, wykonując następujące czynności:  

1. Najpierw zaimportuj dane ze źródła do usługi Azure Blob Storage. Aby zwiększyć szybkość migracji, warto zrównać się z różnymi partycjami źródłowymi. Przed rozpoczęciem migracji zestaw danych źródłowych powinien zostać podzielony na pliki o rozmiarze około 200 MB.   

2. Biblioteka wykonawca zbiorczy można skalować w górę, aby zużywać 500 000 jednostek RU w maszynie wirtualnej jednego klienta. Ponieważ dostępna przepływność wynosi 5 milionów jednostek RU, 10 maszyn wirtualnych Ubuntu 16.04 (Standard_D32_v3) powinny być aprowizowane w tym samym regionie, w którym znajduje się baza danych usługi Azure Cosmos. Maszyny wirtualne należy przygotować za pomocą narzędzia do migracji i jego pliku ustawień.  

3. Uruchom krok kolejki na jednej z maszyn wirtualnych klienta. Ten krok tworzy kolekcję śledzenia, która skanuje kontener ADLS i tworzy dokument śledzenia postępu dla każdego z plików partycji zestawu danych źródłowych.  

4. Następnie uruchom krok importu na wszystkich maszynach wirtualnych klienta. Każdy z klientów może przejąć na własność partycji źródłowej i pozyskiwania jego danych do usługi Azure Cosmos DB. Po jego zakończeniu i jego stan jest aktualizowany w kolekcji śledzenia, klienci mogą następnie kwerendy dla następnej dostępnej partycji źródłowej w kolekcji śledzenia.  

5. Ten proces jest kontynuowany do momentu pozyskannia całego zestawu partycji źródłowych. Po przetworzeniu wszystkich partycji źródłowych narzędzie należy ponownie uruchomić w trybie korekcji błędów w tej samej kolekcji śledzenia. Ten krok jest wymagany do zidentyfikowania partycji źródłowych, które powinny być ponownie przetwarzane z powodu błędów.  

6. Niektóre z tych błędów może być spowodowane niepoprawne dokumenty w danych źródłowych. Należy je zidentyfikować i ustalić. Następnie należy ponownie uruchomić krok importu na partycjach nie powiodło się, aby ponownie je przymknąć. 

Po zakończeniu migracji można sprawdzić, czy liczba dokumentów w usłudze Azure Cosmos DB jest taka sama jak liczba dokumentów w źródłowej bazie danych. W tym przykładzie całkowity rozmiar w usłudze Azure Cosmos DB okazał się 65 terabajtów. Po migracji, indeksowania można selektywnie włączyć i obiektów ru można obniżyć do poziomu wymaganego przez operacje obciążenia.

## <a name="contact-the-azure-cosmos-db-team"></a>Skontaktuj się z zespołem usługi Azure Cosmos DB
Chociaż można wykonać ten przewodnik, aby pomyślnie przeprowadzić migrację dużych zestawów danych do usługi Azure Cosmos DB, w przypadku migracji na dużą skalę zaleca się skontaktowanie się z zespołem produktów usługi Azure Cosmos DB w celu zweryfikowannia modelowania danych i przeglądu ogólnej architektury. Na podstawie zestawu danych i obciążenia zespół produktu może również zaproponować inne optymalizacje wydajności i kosztów, które mogą mieć zastosowanie do Ciebie. Aby skontaktować się z zespołem usługi Azure Cosmos DB w celu uzyskania pomocy w przypadku migracji na dużą skalę, można otworzyć bilet pomocy technicznej w obszarze problemu "Ogólne doradztwo" typu problemu i podtypu problemu "Migracje duże (TB+", jak pokazano poniżej.

![Temat pomocy technicznej dla migracji](./media/migrate-cosmosdb-data/supporttopic.png)


## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej, wypróbowywając przykładowe aplikacje zużywające bibliotekę zbiorczą executora w [programach .NET](bulk-executor-dot-net.md) i [Java](bulk-executor-java.md). 
* Biblioteka executora zbiorczego jest zintegrowana ze łącznikiem platformy Spark usługi Cosmos DB, aby dowiedzieć się więcej, zobacz artykuł [łącznika platformy Azure Cosmos DB Spark.](spark-connector.md)  
* Skontaktuj się z zespołem produktów usługi Azure Cosmos DB, otwierając bilet pomocy technicznej w obszarze typu problemu "Poradnik ogólny" i podtyp problemu "Duże (TB+), aby uzyskać dodatkową pomoc dotyczącą migracji na dużą skalę. 
