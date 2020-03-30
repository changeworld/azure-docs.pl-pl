---
title: Pozyskiwania danych za pomocą przesyłania strumieniowego do pozyskiwania danych do Eksploratora danych platformy Azure
description: Dowiedz się, jak pozyskiwania (ładowania) danych do usługi Azure Data Explorer przy użyciu pozyskiwania przesyłania strumieniowego.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: d7d2bcf487c37fbb523b648d5aa4c572add5dfa9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297088"
---
# <a name="streaming-ingestion-preview"></a>Przesyłanie strumieniowe (wersja zapoznawcza)

Użyj pozyskiwania przesyłania strumieniowego, gdy potrzebujesz małych opóźnień z czasem pozyskiwania krótszym niż 10 sekund dla różnych danych woluminu. Służy do optymalizacji przetwarzania operacyjnego wielu tabel, w jednej lub więcej baz danych, gdzie strumień danych do każdej tabeli jest stosunkowo mały (kilka rekordów na sekundę), ale całkowita ilość pozyskiwania danych jest wysoka (tysiące rekordów na sekundę). 

Użyj zbiorczego pozyskiwania zamiast pozyskiwania przesyłania strumieniowego, gdy ilość danych wzrasta do więcej niż 1 MB na sekundę na tabelę. Przeczytaj [przegląd pozyskiwania danych,](/azure/data-explorer/ingest-data-overview) aby dowiedzieć się więcej o różnych metodach pozyskiwania.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.
* Zaloguj się do [interfejsu użytkownika sieci Web](https://dataexplorer.azure.com/).
* Tworzenie [klastra i bazy danych usługi Azure Data Explorer](create-cluster-database-portal.md)

## <a name="enable-streaming-ingestion-on-your-cluster"></a>Włączanie pozyskiwania przesyłania strumieniowego w klastrze

> [!WARNING]
> Zapoznaj się z [ograniczeniami](#limitations) przed włączeniem połknięcia na parze.

1. W witrynie Azure portal przejdź do klastra usługi Azure Data Explorer. W **obszarze Ustawienia**wybierz pozycję **Konfiguracje**. 
1. W okienku **Konfiguracje** wybierz pozycję **Włącz,** aby włączyć **pozyskiwanie przesyłania strumieniowego**.
1. Wybierz **pozycję Zapisz**.
 
    ![połknienia strumieniowego na](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. W [interfejsie użytkownika sieci Web](https://dataexplorer.azure.com/)zdefiniuj [zasady pozyskiwania przesyłania strumieniowego](/azure/kusto/management/streamingingestionpolicy) na tabelach lub bazach danych, które będą odbierać dane przesyłane strumieniowo. 

    > [!NOTE]
    > * Jeśli zasady są zdefiniowane na poziomie bazy danych, wszystkie tabele w bazie danych są włączone do pozyskiwania przesyłania strumieniowego.
    > * Zastosowane zasady mogą odwoływać się tylko do nowo pogoń danych, a nie do innych tabel w bazie danych.

## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>Do pozyskiwania danych do klastra za pomocą pozyskiwania przesyłania strumieniowego

Istnieją dwa obsługiwane typy pozyskiwania przesyłania strumieniowego:


* [**Centrum zdarzeń**](/azure/data-explorer/ingest-data-event-hub), które jest używane jako źródło danych
* **Niestandardowe pozyskiwania** wymaga zapisu aplikacji, która używa jednej z bibliotek klienta usługi Azure Data Explorer. Zobacz [próbka pozyskiwania przesyłania strumieniowego](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample) dla przykładowej aplikacji.

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>Wybierz odpowiedni typ pozyskiwania strumieniowego

|   |Centrum zdarzeń  |Niestandardowe pozyskiwania  |
|---------|---------|---------|
|Opóźnienie danych między rozpoczęciem pozyskiwania a danymi dostępnymi dla kwerendy   |    Dłuższe opóźnienie     |   Krótsze opóźnienie      |
|Napowietrznych rozwoju    |   Szybka i łatwa konfiguracja, brak napowietrznych rozwiązań    |   Wysokie obciążenie związane z tworzeniem aplikacji w celu obsługi błędów i zapewnienia spójności danych     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>Wyłączanie pozyskiwania przesyłania strumieniowego w klastrze

> [!WARNING]
> Wyłączenie pozyskiwania strumieniowego może potrwać kilka godzin.

1. Porzucj [zasady pozyskiwania przesyłania strumieniowego](/azure/kusto/management/streamingingestionpolicy) ze wszystkich odpowiednich tabel i baz danych. Usuwanie zasad pozyskiwania przesyłania strumieniowego wyzwala przesyłania strumieniowego pozyskiwania danych przenoszenia z magazynu początkowego do magazynu trwałego w magazynie kolumn (zakresy lub fragmenty). Ruch danych może trwać od kilku sekund do kilku godzin, w zależności od ilości danych w początkowej pamięci masowej i jak procesor i pamięć jest używana przez klaster.
1. W witrynie Azure portal przejdź do klastra usługi Azure Data Explorer. W **obszarze Ustawienia**wybierz pozycję **Konfiguracje**. 
1. W okienku **Konfiguracje** wybierz pozycję **Wyłączone,** aby wyłączyć **przesyłanie strumieniowe**.
1. Wybierz **pozycję Zapisz**.

    ![Streaming połknąć wyłączony](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>Ograniczenia

* Przesyłanie strumieniowe nie obsługuje [kursorów bazy danych](/azure/kusto/management/databasecursor) ani [mapowania danych.](/azure/kusto/management/mappings) Obsługiwane jest tylko [wstępnie utworzone](/azure/kusto/management/create-ingestion-mapping-command) mapowanie danych. 
* Skala wydajności i pojemności pozyskiwania przesyłania strumieniowego ze zwiększonymi rozmiarami maszyn wirtualnych i klastrów. Równoczesne połknięcia są ograniczone do sześciu połknięć na rdzeń. Na przykład dla 16 podstawowych jednostek SKU, takich jak D14 i L16, maksymalne obsługiwane obciążenie wynosi 96 równoczesnych policzeń. Dla dwóch podstawowych jednostek SKU, takich jak D11, maksymalne obsługiwane obciążenie wynosi 12 równoczesnych pojmowania.
* Ograniczenie rozmiaru danych na żądanie pozyskiwania wynosi 4 MB.
* Aktualizacje schematu, takie jak tworzenie i modyfikowanie tabel i mapowania pozyskiwania, może potrwać do pięciu minut dla usługi pozyskiwania przesyłania strumieniowego.
* Włączenie pozyskiwania przesyłania strumieniowego w klastrze, nawet jeśli dane nie są pogoń za pośrednictwem przesyłania strumieniowego, używa części lokalnego dysku SSD maszyn klastrowych do przesyłania strumieniowego danych pozyskiwania i zmniejsza ilość miejsca dostępnego dla gorącej pamięci podręcznej.
* [Tagów extent](/azure/kusto/management/extents-overview#extent-tagging) nie można ustawić na danych pozyskiwania przesyłania strumieniowego.

## <a name="next-steps"></a>Następne kroki

* [wykonywanie zapytań o dane w usłudze Azure Data Explorer](web-query-data.md)
