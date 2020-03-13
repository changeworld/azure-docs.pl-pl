---
title: Utwórz kontenery usługi Azure Cosmos i bazy danych w trybie autopilotażu.
description: Poznaj korzyści, przypadki użycia i sposób aprowizacji baz danych i kontenerów usługi Azure Cosmos w trybie autopilotażu.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 89af30788fe5129cddc6a3607b8c722549b610d1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246658"
---
# <a name="create-azure-cosmos-containers-and-databases-in-autopilot-mode-preview"></a>Tworzenie kontenerów i baz danych usługi Azure Cosmos w trybie autopilotażu (wersja zapoznawcza)

Usługa Azure Cosmos DB umożliwia aprowizowanie przepływności w kontenerach w trybie ręcznym lub trybie Autopilot. W tym artykule opisano zalety i przypadki użycia trybu Autopilot.

> [!NOTE]
> Tryb autopilotażu jest obecnie dostępny w publicznej wersji zapoznawczej. [Funkcję autopilotażu można włączyć tylko dla nowych baz danych i kontenerów](#create-a-database-or-a-container-with-autopilot-mode) . Nie jest on dostępny dla istniejących kontenerów i baz danych.

Oprócz ręcznego inicjowania obsługi przepływności można teraz skonfigurować kontenery usługi Azure Cosmos w trybie autopilotażu. Kontenery i bazy danych skonfigurowane w trybie autopilotażu będą **automatycznie i natychmiast skalować zainicjowaną przepływność na podstawie potrzeb aplikacji, bez wywierania wpływu na dostępność, opóźnienia, przepływność lub wydajność na całym świecie.**

Podczas konfigurowania kontenerów i baz danych w trybie autopilotażu należy określić maksymalną przepływność, `Tmax` nie można jej przekroczyć. Kontenery mogą następnie skalować swoją przepływność w taki sposób, aby `0.1*Tmax < T < Tmax`. Innymi słowy, kontenery i bazy danych są skalowane szybko w zależności od potrzeb związanych z obciążeniem, od do 10% maksymalnej wartości przepływności skonfigurowanej do skonfigurowanej maksymalnej wartości przepływności. W dowolnym momencie można zmienić ustawienie maksymalnej przepływności (`Tmax`) dla bazy danych lub kontenera. Po wybraniu opcji autopilotażu 400 RU/s minimalna przepływność na kontener lub baza danych nie jest już stosowana.

W trakcie korzystania z wersji zapoznawczej autopilotażu dla określonej maksymalnej przepływności w kontenerze lub w bazie danych system umożliwia działanie w ramach obliczonego limitu magazynu. W przypadku przekroczenia limitu magazynowania maksymalna przepływność jest automatycznie dostosowywana do wyższej wartości. W przypadku korzystania z przepływności poziomu bazy danych z trybem pilotażowym liczba kontenerów dozwolonych w ramach bazy danych jest obliczana jako: `0.001*TMax`. Na przykład jeśli zainicjujesz 20 000 autopilotaż RU/s, baza danych może mieć 20 kontenerów.

## <a name="benefits-of-autopilot-mode"></a>Zalety trybu pilotażowego

Kontenery usługi Azure Cosmos, które są skonfigurowane w trybie autopilotażu, mają następujące zalety:

* **Prosty:** Kontenery w trybie autopilotażu usuwają złożoność do ręcznego zarządzania elastyczną przepływność (jednostek ru) i pojemnością dla różnych kontenerów.

* **Skalowalność:** Kontenery w trybie autopilotażu umożliwiają bezproblemowe skalowanie alokowanej przepływności zgodnie z potrzebami. Nie ma przerw w połączeniach klientów, aplikacje i nie wpływają na żadne istniejące umowy SLA.

* **Koszt ekonomiczny:** W przypadku korzystania z kontenerów skonfigurowanych w trybie autopilotażu płacisz tylko za zasoby, których obciążenia wymagają w ciągu godziny.

* **Wysoka dostępność:** Kontenery w trybie pilotażowym używają tego samego globalnie dystrybuowanego, odpornego na błędy i zaplecza o wysokiej dostępności, aby zapewnić trwałość danych i wysoką dostępność.

## <a name="use-cases-of-autopilot-mode"></a>Przypadki użycia trybu autopilotażu

Przypadki użycia w przypadku kontenerów usługi Azure Cosmos skonfigurowanych w trybie pilotażu obejmują:

* **Obciążenia zmienne:** Gdy korzystasz z aplikacji ze szczytowym użyciem przez 1 godzinę do kilku godzin lub kilka razy dziennie. Przykłady obejmują aplikacje dla zasobów ludzkich, budżetowania i raportowania operacyjnego. W takich scenariuszach można używać kontenerów skonfigurowanych w trybie autopilotażu i nie trzeba już ręcznie udostępniać ich wartości szczytowej lub średniej.

* **Nieprzewidywalne obciążenia:** W przypadku korzystania z obciążeń, w których w ciągu dnia występuje użycie bazy danych, ale także szczytowe działania, które są trudne do przewidywania. Przykład obejmuje lokację ruchu, która widzi wzrost aktywności w przypadku zmiany prognozy pogody. Kontenery skonfigurowane w trybie autopilotażu dostosowują pojemność w celu spełnienia wymagań szczytowego obciążenia aplikacji i skalowania w dół w przypadku przekroczenia działania.

* **Nowe aplikacje:** Jeśli wdrażasz nową aplikację i nie wiesz, jak dużo zainicjowanej przepływności (tj. ilu jednostek ru) potrzebujesz. Przy użyciu kontenerów skonfigurowanych w trybie automatycznego pilotażu można automatycznie skalować do potrzeb i wymagań aplikacji.

* **Rzadko używane aplikacje:** Jeśli masz aplikację, która jest używana tylko przez kilka godzin w ciągu dnia lub tygodnia lub miesiąca, na przykład w przypadku aplikacji/witryny sieci Web/blogu o niskiej wielkości.

* **Tworzenie i testowanie baz danych:** Jeśli masz deweloperów korzystających z kontenerów w godzinach pracy, ale nie są one potrzebne w nocy lub w weekendy. W przypadku kontenerów skonfigurowanych w trybie autopilotażu są one skalowane do minimum, gdy nie są używane.

* **Zaplanowane obciążenia produkcyjne/zapytania:** Jeśli masz serię zaplanowanych żądań/operacji/zapytań w jednym kontenerze i jeśli istnieją bezczynne okresy, w których chcesz uruchamiać bezwzględnie niską przepływność, możesz to zrobić łatwo. Gdy zaplanowana kwerenda/żądanie zostanie przesłane do kontenera skonfigurowanego w trybie autopilotażu, będzie on automatycznie skalowany w górę, jak to konieczne, i uruchomić operację.

Rozwiązania dotyczące poprzednich problemów nie tylko wymagają dużego czasu w implementacji, ale również wprowadzają złożoność w konfiguracji lub kodzie i często wymagają ręcznej interwencji do ich rozwiązania. Tryb autopilotażu włącza powyższe scenariusze, dzięki czemu nie trzeba już martwić się o te problemy.

## <a name="comparison--containers-configured-in-manual-mode-vs-autopilot-mode"></a>Porównanie — kontenery skonfigurowane w trybie ręcznym a trybem autopilotażu

|  | Kontenery skonfigurowane w trybie ręcznym  | Kontenery skonfigurowane w trybie autopilotażu |
|---------|---------|---------|
| **Elastyczna przepływność** | Ręcznie zainicjowany. | Automatycznie i chwilowo skalowane na podstawie wzorców użycia obciążeń. |
| **Szybkość ograniczania żądań/operacji (429)**  | Może się tak zdarzyć, jeśli zużycie przekracza przypuszczalną pojemność. | Nie nastąpi, jeśli używana przepływność mieści się w maksymalnej przepływności wybranej z trybem autopilotażu.   |
| **Planowanie wydajności** |  Należy wykonać planowanie początkowej pojemności i zapewnić wymaganą przepływność. |    Nie musisz martwić się o planowanie pojemności. System automatycznie zajmuje się planowaniem pojemności i zarządzaniem pojemnością. |
| **Cennik** | Ręcznie aprowizacji RU/s na godzinę. | W przypadku kont z jednym regionem zapisu płacisz za przepływność używaną co godzinę przy użyciu częstotliwości autopilotażowej RU/s na godzinę. <br/><br/>W przypadku kont z wieloma regionami zapisu nie ma dodatkowej opłaty za autopilotaż. Opłata jest naliczana za przepływność użyta co godzinę przy użyciu tego samego kursu wieloskładnikowego RU/s na godzinę. |
| **Najlepiej dopasowane do typów obciążeń** |  Przewidywalne i stabilne obciążenia|   Nieprzewidywalne obciążenia i zmienne  |

## <a name="create-a-database-or-a-container-with-autopilot-mode"></a>Tworzenie bazy danych lub kontenera z trybem autopilotażu

Można skonfigurować autopilotaż dla nowych baz danych lub kontenerów podczas tworzenia ich za pomocą Azure Portal. Wykonaj następujące kroki, aby utworzyć nową bazę danych lub kontener, włączyć funkcję autopilotaż i określić maksymalną przepływność (RU/s).

1. Zaloguj się do [Azure Portal](https://portal.azure.com) lub [Eksploratora Azure Cosmos DB.](https://cosmos.azure.com/)

1. Przejdź do konta Azure Cosmos DB i Otwórz kartę **Eksplorator danych** .

1. Wybierz pozycję **nowy kontener.** Wprowadź nazwę bazy danych, kontenera i klucza partycji. W obszarze **przepływność**wybierz opcję **autopilotaż** i wybierz maksymalną przepływność (ru/s), którą baza danych lub kontener nie może przekroczyć podczas korzystania z opcji autopilotażu.

   ![Tworzenie kontenera i Konfigurowanie przepływności autopilotażu](./media/provision-throughput-autopilot/create-container-autopilot-mode.png)

1. Kliknij przycisk **OK**.

Można utworzyć udostępnioną bazę danych przepływności z trybem autopilotażu, wybierając opcję **zainicjuj przepływność bazy danych** .

## <a id="autopilot-limits"></a>Limity przepływności i magazynu dla autopilotażu

W poniższej tabeli przedstawiono maksymalne limity limitów w granicach i magazynu dla różnych opcji w trybie autopilotażu:

|Maksymalny limit przepływności  |Maksymalny limit magazynu  |
|---------|---------|
|4000 RU/s  |   50 GB    |
|20 000 RU/s  |  200 GB  |
|100 000 RU/s    |  1 TB   |
|500 000 RU/s    |  5 TB  |

## <a name="next-steps"></a>Następne kroki

* Przejrzyj [często zadawane pytania dotyczące programu pilotażowego](autopilot-faq.md).
* Dowiedz się więcej na temat [partycji logicznych](partition-data.md).
* Dowiedz się, jak [zainicjować przepływność na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).
* Dowiedz się, jak [udostępnić przepływność w bazie danych Azure Cosmos](how-to-provision-database-throughput.md).
