---
title: Tworzenie kontenerów i baz danych usługi Azure Cosmos w trybie autopilota.
description: Dowiedz się więcej o korzyściach, przypadkach użycia i sposobie aprowizowania baz danych i kontenerów usługi Azure Cosmos w trybie autopilota.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 89af30788fe5129cddc6a3607b8c722549b610d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246658"
---
# <a name="create-azure-cosmos-containers-and-databases-in-autopilot-mode-preview"></a>Create Azure Cosmos containers and databases in autopilot mode (Tworzenie kontenerów i baz danych usługi Azure Cosmos w trybie Autopilot — wersja zapoznawcza)

Usługa Azure Cosmos DB umożliwia aprowizowanie przepływności w kontenerach w trybie ręcznym lub trybie Autopilot. W tym artykule opisano zalety i przypadki użycia trybu Autopilot.

> [!NOTE]
> Tryb autopilota jest obecnie dostępny w publicznej wersji zapoznawczej. Autopilot można włączyć tylko [dla nowych baz danych i kontenerów.](#create-a-database-or-a-container-with-autopilot-mode) Nie jest dostępna dla istniejących kontenerów i baz danych.

Oprócz ręcznego inicjowania obsługi administracyjnej przepływności można teraz skonfigurować kontenery usługi Azure Cosmos w trybie autopilota. Kontenery i bazy danych skonfigurowane w trybie autopilota będą **automatycznie i natychmiast skalować aprowizowaną przepływność na podstawie potrzeb aplikacji bez wpływu na dostępność, opóźnienie, przepływność lub wydajność obciążenia na całym świecie.**

Podczas konfigurowania kontenerów i baz danych w trybie `Tmax` autopilota należy określić maksymalną przepustowość, która nie zostanie przekroczona. Kontenery mogą następnie skalować `0.1*Tmax < T < Tmax`swoją przepustowość tak, aby . Innymi słowy kontenery i bazy danych skalują się natychmiast w zależności od potrzeb obciążenia, od zaledwie 10% maksymalnej wartości przepływności skonfigurowanej do skonfigurowanej maksymalnej wartości przepływności. Ustawienie maksymalnej przepustowości`Tmax`( ) w bazie danych lub kontenerze autopilota można zmienić w dowolnym momencie. W przypadku opcji autopilota minimalna przepustowość 400 RU/s na kontener lub bazę danych nie ma już zastosowania.

Podczas podglądu autopilota dla określonej maksymalnej przepustowości kontenera lub bazy danych system umożliwia działanie w ramach obliczonego limitu miejsca. Jeśli limit magazynowania zostanie przekroczony, maksymalna przepustowość jest automatycznie dostosowywana do wyższej wartości. W przypadku korzystania z przepustowości na poziomie bazy danych w trybie `0.001*TMax`autopilota liczba kontenerów dozwolonych w bazie danych jest obliczana jako: . Na przykład jeśli aprowizujesz 20 000 autopilota RU/s, baza danych może mieć 20 kontenerów.

## <a name="benefits-of-autopilot-mode"></a>Zalety trybu autopilota

Kontenery usługi Azure Cosmos skonfigurowane w trybie autopilota mają następujące zalety:

* **Proste:** Kontenery w trybie autopilota usuwają złożoność zarządzania aprowizowaną przepływnością (RUs) i pojemnością ręcznie dla różnych kontenerów.

* **Skalowalne:** Kontenery w trybie autopilota bezproblemowo skalują pojemność aprowizowaną przepływności w razie potrzeby. Nie ma żadnych zakłóceń połączeń klientów, aplikacji i nie mają one wpływu na istniejące ławy SLA.

* **Opłacalne:** Korzystając z kontenerów skonfigurowanych w trybie autopilota, płacisz tylko za zasoby, których obciążenia potrzebują na godzinę.

* **Wysoka dostępna:** Kontenery w trybie autopilota używają tego samego globalnie rozproszonego, odpornego na uszkodzenia, wysoce dostępnego zaplecza, aby zapewnić trwałość danych i wysoką dostępność.

## <a name="use-cases-of-autopilot-mode"></a>Przypadki użycia w trybie autopilota

Przypadki użycia kontenerów usługi Azure Cosmos skonfigurowanych w trybie autopilota obejmują:

* **Zmienne obciążenia:** Podczas pracy lekko używane aplikacji z szczytowym użyciem od 1 godziny do kilku godzin kilka razy dziennie lub kilka razy w roku. Przykłady obejmują aplikacje dotyczące zasobów ludzkich, budżetowania i raportowania operacyjnego. W takich scenariuszach można używać kontenerów skonfigurowanych w trybie autopilota i nie trzeba już ręcznie aprowizować dla maksymalnej lub średniej pojemności.

* **Nieprzewidywalne obciążenia:** Podczas uruchamiania obciążeń, gdzie istnieje użycie bazy danych przez cały dzień, ale również szczyty aktywności, które są trudne do przewidzenia. Przykład obejmuje witrynę ruchu, która widzi wzrost aktywności, gdy zmienia się prognoza pogody. Kontenery skonfigurowane w trybie autopilota dostosowują pojemność do potrzeb szczytowego obciążenia aplikacji i skalują się w dół po zakończeniu przypływu aktywności.

* **Nowe aplikacje:** Jeśli wdrażasz nową aplikację i nie masz pewności, ile aprowizowana przepływność (tj. ile jednostek RU) jest potrzebnych. Kontenery skonfigurowane w trybie autopilota można automatycznie skalować do potrzeb pojemności i wymagań aplikacji.

* **Rzadko używane aplikacje:** Jeśli masz aplikację, która jest używana tylko przez kilka godzin kilka razy dziennie, tydzień lub miesiąc, na przykład aplikacja o małej objętości / witryna sieci Web / bloga.

* **Rozwój i testowanie baz danych:** Jeśli masz programistów korzystających z kontenerów w godzinach pracy, ale nie potrzebujesz ich w nocy lub w weekendy. W przypadku kontenerów skonfigurowanych w trybie autopilota są one skalowane do minimum, gdy nie są używane.

* **Zaplanowane obciążenia/kwerendy produkcyjne:** Jeśli masz serię zaplanowanych żądań/operacji/kwerend w jednym kontenerze i jeśli istnieją okresy bezczynności, w których chcesz uruchomić z absolutnie niską przepływnością, możesz teraz to łatwo zrobić. Gdy zaplanowane zapytanie/żądanie zostanie przesłane do kontenera skonfigurowanego w trybie autopilota, automatycznie skaluje się w górę tak bardzo, jak jest to potrzebne i uruchamia operację.

Rozwiązania poprzednich problemów nie tylko wymagają ogromnej ilości czasu w implementacji, ale również wprowadzają złożoność konfiguracji lub kodu i często wymagają ręcznej interwencji, aby je rozwiązać. Tryb autopilota włącza powyższe scenariusze po wyjęciu z pudełka, dzięki czemu nie musisz już martwić się o te problemy.

## <a name="comparison--containers-configured-in-manual-mode-vs-autopilot-mode"></a>Porównanie – kontenery skonfigurowane w trybie ręcznym a trybie autopilota

|  | Kontenery skonfigurowane w trybie ręcznym  | Kontenery skonfigurowane w trybie autopilota |
|---------|---------|---------|
| **Aprowizowana przepływność** | Ręcznie aprowizacji. | Automatycznie i natychmiast skalowane na podstawie wzorców użycia obciążenia. |
| **Ograniczenie szybkości żądań/operacji (429)**  | Może się zdarzyć, jeśli zużycie przekracza aprowizowanych zdolności produkcyjnych. | Nie stanie się, jeśli przepływność zużyta mieści się w maksymalnej przepływności, którą wybierzesz w trybie autopilota.   |
| **Planowanie pojemności** |  Należy wykonać początkowe planowanie zdolności produkcyjnych i dostarczanie przepływności, której potrzebujesz. |    Nie musisz się martwić o planowanie pojemności. System automatycznie zajmuje się planowaniem pojemności i zarządzaniem pojemnościami. |
| **Cennik** | Ręcznie aprowiowane RU/s na godzinę. | W przypadku kont regionu pojedynczego zapisu płacisz za przepustowość używaną co godzinę, korzystając z stawki automatycznego ru/s za godzinę. <br/><br/>W przypadku kont z wieloma regionami zapisu nie ma dodatkowych opłat za autopilota. Płacisz za przepustowość używaną co godzinę przy użyciu tej samej wielowymiernej stawki RU/s za godzinę. |
| **Najlepiej nadaje się do typów obciążeń** |  Przewidywalne i stabilne obciążenia|   Nieprzewidywalne i zmienne obciążenia  |

## <a name="create-a-database-or-a-container-with-autopilot-mode"></a>Tworzenie bazy danych lub kontenera w trybie autopilota

Podczas tworzenia ich za pośrednictwem witryny Azure Portal można skonfigurować autopilota dla nowych baz danych lub kontenerów. Poniższe kroki umożliwiają utworzenie nowej bazy danych lub kontenera, włączenie autopilota i określenie maksymalnej przepływności (RU/s).

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) lub [eksploratora usługi Azure Cosmos DB.](https://cosmos.azure.com/)

1. Przejdź do konta usługi Azure Cosmos DB i otwórz kartę **Eksploratora danych.**

1. Wybierz **nowy kontener.** Wprowadź nazwę bazy danych, kontenera i klucza partycji. W obszarze **Przepływność**wybierz opcję **Autopilot** i wybierz maksymalną przepustowość (RU/s), których baza danych lub kontener nie może przekroczyć podczas korzystania z opcji autopilota.

   ![Tworzenie kontenera i konfigurowanie przepływności autopilota](./media/provision-throughput-autopilot/create-container-autopilot-mode.png)

1. Kliknij przycisk **OK**.

Udostępnionej bazy danych przepływności można utworzyć w trybie autopilota, wybierając opcję **Aprowizuj przepływność bazy danych.**

## <a name="throughput-and-storage-limits-for-autopilot"></a><a id="autopilot-limits"></a>Limity przepustowości i pamięci masowej dla autopilota

W poniższej tabeli przedstawiono maksymalne limity w całym i limity pamięci masowej dla różnych opcji w trybie autopilota:

|Maksymalny limit przepustowości  |Maksymalny limit miejsca do magazynowania  |
|---------|---------|
|4000 ru/s  |   50 GB    |
|20 000 RU/s  |  200 GB  |
|100 000 RU/s    |  1 TB   |
|500 000 RU/s    |  5 TB  |

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z [często zadawanymi pytaniami dotyczącymi autopilota](autopilot-faq.md).
* Dowiedz się więcej o [partycjach logicznych](partition-data.md).
* Dowiedz się, jak [aprowizować przepływność w kontenerze usługi Azure Cosmos.](how-to-provision-container-throughput.md)
* Dowiedz się, jak [aprowizować przepływność w bazie danych usługi Azure Cosmos.](how-to-provision-database-throughput.md)
