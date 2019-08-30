---
title: Wykorzystywanie pozyskiwania strumieniowego do pozyskiwania danych w usłudze Azure Eksplorator danych
description: Dowiedz się, jak pozyskiwanie (ładować) danych do platformy Azure Eksplorator danych przy użyciu pozyskiwania strumieniowego.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: b716cbf3efb044da68d4dd1dcb724369855d1ed1
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173653"
---
# <a name="streaming-ingestion-preview"></a>Pozyskiwanie strumieniowe (wersja zapoznawcza)

Pozyskiwanie strumieniowe jest przeznaczone dla scenariuszy wymagających małych opóźnień i czasu pozyskiwania mniejszego niż 10 sekund w przypadku różnych danych woluminu. Służy do optymalizacji przetwarzania operacyjnego wielu tabel w co najmniej jednej bazie danych, w której strumień danych do każdej tabeli jest stosunkowo mały (liczba rekordów na sekundę), ale ogólny wolumin pozyskiwania danych jest wysoki (tysiące rekordów na sekundę).

Użyj operacji pozyskiwania klasycznego (zbiorczego) zamiast pozyskiwania strumieniowego, gdy ilość danych rośnie do ponad 1 MB na sekundę na tabelę. Zapoznaj się z omówieniem pozyskiwania [danych](/azure/data-explorer/ingest-data-overview) , aby dowiedzieć się więcej o różnych metodach pozyskiwania.

> [!NOTE]
> Pozyskiwanie strumieniowe nie obsługuje następujących funkcji:
> * [Kursory bazy danych](/azure/kusto/management/databasecursor).
> * [Mapowanie danych](/azure/kusto/management/mappings). Obsługiwane jest tylko [wstępnie utworzone](/azure/kusto/management/tables#create-ingestion-mapping) mapowanie danych. 

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* Zaloguj się do [interfejsu użytkownika sieci Web](https://dataexplorer.azure.com/).
* Tworzenie [klastra usługi Azure Eksplorator danych i bazy danych](create-cluster-database-portal.md)

## <a name="enable-streaming-ingestion-on-your-cluster"></a>Włącz pozyskiwanie strumieniowe w klastrze

1. W Azure Portal przejdź do klastra usługi Azure Eksplorator danych. W obszarze **Ustawienia**wybierz pozycję **konfiguracje**. 
1. W okienku **konfiguracje** wybierz pozycję **włączone** , aby włączyćpozyskiwanie strumieniowe.
1. Wybierz pozycję **Zapisz**.
 
    ![pozyskiwanie przesyłania strumieniowego](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. W [interfejsie użytkownika sieci Web](https://dataexplorer.azure.com/)Zdefiniuj [zasady](/azure/kusto/concepts/streamingingestionpolicy) pozyskiwania strumieniowego dla tabel lub baz danych, które będą odbierać dane przesyłane strumieniowo. 

    > [!TIP]
    > Jeśli zasady są zdefiniowane na poziomie bazy danych, wszystkie tabele w bazie danych są włączone na potrzeby pozyskiwania strumieniowego.

## <a name="supported-streaming-ingestion-types"></a>Obsługiwane typy pozyskiwania strumieniowego

Istnieją dwa obsługiwane typy pozyskiwania strumieniowego:

* [Centrum zdarzeń](/azure/data-explorer/ingest-data-event-hub) używane jako źródło danych
* Wprowadzanie niestandardowe wymaga napisania aplikacji korzystającej z jednej z bibliotek klienckich Eksplorator danych platformy Azure. Zobacz [przykład](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample) pozyskiwania strumieniowego dla przykładowej aplikacji.

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>Wybierz odpowiedni typ pozyskiwania strumieniowego

|   |Centrum zdarzeń  |Pozyskiwanie niestandardowe  |
|---------|---------|---------|
|Opóźnienie danych między rozpoczęciem pozyskiwania a danymi dostępnymi dla zapytania   |    dłuższe opóźnienie     |   krótsze opóźnienie      |
|Nakłady programistyczne    |   Szybka i łatwa konfiguracja, brak nakładu na programowanie    |   duże obciążenie dla aplikacji w celu obsługi błędów i zapewnienia spójności danych     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>Wyłączanie pozyskiwania przesyłania strumieniowego w klastrze

> [!WARNING]
> Wyłączenie pozyskiwania przesyłania strumieniowego może potrwać kilka godzin.

1. Porzuć [zasady](/azure/kusto/concepts/streamingingestionpolicy) pozyskiwania strumieniowego ze wszystkich odpowiednich tabel i baz danych. Usunięcie zasad pozyskiwania strumieniowego spowoduje wyzwolenie przepływu danych pozyskiwania strumieniowego z magazynu początkowego do magazynu trwałego w magazynie kolumn (zakresy lub fragmentów). Przenoszenie danych może trwać od kilku sekund do kilku godzin, w zależności od ilości danych w magazynie początkowym oraz użycia procesora CPU i pamięci w klastrze.
1. W Azure Portal przejdź do klastra usługi Azure Eksplorator danych. W obszarze **Ustawienia**wybierz pozycję **konfiguracje**. 
1. W okienku **konfiguracje** wybierz pozycję **wyłączone** , aby wyłączyćpozyskiwanie strumieniowe.
1. Wybierz pozycję **Zapisz**.

    ![Pozyskiwanie przesyłania strumieniowego](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>Ograniczenia

* Skalowanie wydajności i pojemności pozyskiwania strumieniowego przy użyciu zwiększonych rozmiarów maszyn wirtualnych i klastrów. W przypadku jednego węzła D11 zalecanym obciążeniem jest maksymalnie 20 żądań na sekundę. W przypadku jednego węzła D14 zalecanym obciążeniem jest maksymalnie 150 żądań na sekundę.
* Obecnie pomoc techniczna jest dostępna tylko dla 8 i 16 podstawowych jednostek SKU (D13, D14, P8 i L16).
* Limit rozmiaru danych na żądanie pozyskiwania wynosi 4 MB.
* Aktualizacje schematu, takie jak tworzenie i modyfikowanie tabel i mapowania pozyskiwania, mogą potrwać do 5 minut w przypadku usługi pozyskiwania strumieniowego.
* Włączenie pozyskiwania przesyłania strumieniowego w klastrze, nawet w przypadku, gdy dane nie są pozyskiwane za pośrednictwem przesyłania strumieniowego, program używa części lokalnego dysku SSD maszyn klastra do przesyłania strumieniowego danych pozyskiwania i zmniejsza ilość miejsca dostępnego na gorącą pamięć podręczną.

## <a name="next-steps"></a>Następne kroki

* [Wykonywanie zapytań dotyczących danych w usłudze Azure Eksplorator danych](web-query-data.md)