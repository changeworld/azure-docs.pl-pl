---
title: Wykorzystywanie pozyskiwania strumieniowego do pozyskiwania danych w usłudze Azure Eksplorator danych
description: Dowiedz się, jak pozyskiwanie (ładować) danych do platformy Azure Eksplorator danych przy użyciu pozyskiwania strumieniowego.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 1857c1154af5e3de72803f297e8a3151b0dd7aeb
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78330978"
---
# <a name="streaming-ingestion-preview"></a>Pozyskiwanie strumieniowe (wersja zapoznawcza)

Pozyskiwanie strumieniowe jest stosowane w przypadku niewielkich opóźnień i czasu pozyskiwania mniejszego niż 10 sekund w przypadku różnych danych woluminu. Służy do optymalizowania przetwarzania operacyjnego wielu tabel w co najmniej jednej bazie danych, gdzie strumień danych do każdej tabeli jest stosunkowo mały (liczba rekordów na sekundę), ale ogólny wolumin pozyskiwania danych jest wysoki (tysiące rekordów na sekundę). 

Użyj pozyskiwania zbiorczego zamiast pozyskiwania strumieniowego, gdy ilość danych rośnie do ponad 1 MB na sekundę na tabelę. Zapoznaj się z omówieniem pozyskiwania [danych](/azure/data-explorer/ingest-data-overview) , aby dowiedzieć się więcej o różnych metodach pozyskiwania.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* Zaloguj się do [interfejsu użytkownika sieci Web](https://dataexplorer.azure.com/).
* Tworzenie [klastra usługi Azure Eksplorator danych i bazy danych](create-cluster-database-portal.md)

## <a name="enable-streaming-ingestion-on-your-cluster"></a>Włącz pozyskiwanie strumieniowe w klastrze

> [!WARNING]
> Zapoznaj się z [ograniczeniami](#limitations) przed włączeniem pozyskiwania pary.

1. W Azure Portal przejdź do klastra usługi Azure Eksplorator danych. W obszarze **Ustawienia**wybierz pozycję **konfiguracje**. 
1. W okienku **konfiguracje** wybierz pozycję **włączone** , aby włączyć pozyskiwanie **strumieniowe**.
1. Wybierz pozycję **Zapisz**.
 
    ![pozyskiwanie przesyłania strumieniowego](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. W [interfejsie użytkownika sieci Web](https://dataexplorer.azure.com/)Zdefiniuj zasady pozyskiwania [strumieniowego](/azure/kusto/management/streamingingestionpolicy) dla tabel lub baz danych, które będą odbierać dane przesyłane strumieniowo. 

    > [!NOTE]
    > * Jeśli zasady są zdefiniowane na poziomie bazy danych, wszystkie tabele w bazie danych są włączone na potrzeby pozyskiwania strumieniowego.
    > * Zastosowane zasady mogą odwoływać się tylko do nowo wprowadzonych danych, a nie innych tabel w bazie danych.

## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>Wykorzystywanie pozyskiwania strumieniowego do pozyskiwania danych do klastra

Istnieją dwa obsługiwane typy pozyskiwania strumieniowego:


* [**Centrum zdarzeń**](/azure/data-explorer/ingest-data-event-hub), które jest używane jako źródło danych
* Wprowadzanie **niestandardowe** wymaga zapisania aplikacji korzystającej z jednej z bibliotek klienta Eksplorator danych platformy Azure. Zobacz [przykład pozyskiwania strumieniowego](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample) dla przykładowej aplikacji.

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>Wybierz odpowiedni typ pozyskiwania strumieniowego

|   |Centrum zdarzeń  |Pozyskiwanie niestandardowe  |
|---------|---------|---------|
|Opóźnienie danych między rozpoczęciem pozyskiwania a danymi dostępnymi dla zapytania   |    dłuższe opóźnienie     |   krótsze opóźnienie      |
|Nakłady programistyczne    |   Szybka i łatwa konfiguracja, brak nakładu na programowanie    |   duże obciążenie dla aplikacji w celu obsługi błędów i zapewnienia spójności danych     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>Wyłączanie pozyskiwania przesyłania strumieniowego w klastrze

> [!WARNING]
> Wyłączenie pozyskiwania przesyłania strumieniowego może potrwać kilka godzin.

1. Porzuć zasady pozyskiwania [strumieniowego](/azure/kusto/management/streamingingestionpolicy) ze wszystkich odpowiednich tabel i baz danych. Usunięcie zasad pozyskiwania strumieniowego spowoduje wyzwolenie przepływu danych pozyskiwania strumieniowego z magazynu początkowego do magazynu trwałego w magazynie kolumn (zakresy lub fragmentów). Przenoszenie danych może trwać od kilku sekund do kilku godzin, w zależności od ilości danych w magazynie początkowym oraz sposobu użycia procesora i pamięci przez klaster.
1. W Azure Portal przejdź do klastra usługi Azure Eksplorator danych. W obszarze **Ustawienia**wybierz pozycję **konfiguracje**. 
1. W okienku **konfiguracje** wybierz pozycję **wyłączone** , aby wyłączyć pozyskiwanie **strumieniowe**.
1. Wybierz pozycję **Zapisz**.

    ![Pozyskiwanie przesyłania strumieniowego](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>Ograniczenia

* Pozyskiwanie strumieniowe nie obsługuje [kursorów bazy danych](/azure/kusto/management/databasecursor) ani [mapowania danych](/azure/kusto/management/mappings). Obsługiwane jest tylko [wstępnie utworzone](/azure/kusto/management/tables#create-ingestion-mapping) mapowanie danych. 
* Skalowanie wydajności i pojemności pozyskiwania strumieniowego przy użyciu zwiększonych rozmiarów maszyn wirtualnych i klastrów. Współbieżne pozyskiwania są ograniczone do sześciu pozyskań na rdzeń. Na przykład dla 16 podstawowych jednostek SKU, takich jak D14 i L16, Maksymalne obsługiwane obciążenie to 96 współbieżności. W przypadku dwóch podstawowych jednostek SKU, takich jak D11, Maksymalne obsługiwane obciążenie to 12 współbieżnych operacji pozyskiwania.
* Limit rozmiaru danych na żądanie pozyskiwania wynosi 4 MB.
* Aktualizacje schematu, takie jak tworzenie i modyfikowanie tabel i mapowania pozyskiwania, mogą potrwać do 5 minut w przypadku usługi pozyskiwania strumieniowego.
* Włączenie pozyskiwania przesyłania strumieniowego w klastrze, nawet w przypadku, gdy dane nie są pozyskiwane za pośrednictwem przesyłania strumieniowego, program używa części lokalnego dysku SSD maszyn klastra do przesyłania strumieniowego danych pozyskiwania i zmniejsza ilość miejsca dostępnego na gorącą pamięć podręczną.
* Nie można ustawić [tagów zakresu](/azure/kusto/management/extents-overview#extent-tagging) w danych pozyskiwania strumieniowego.

## <a name="next-steps"></a>Następne kroki

* [Wykonywanie zapytań dotyczących danych w usłudze Azure Eksplorator danych](web-query-data.md)
