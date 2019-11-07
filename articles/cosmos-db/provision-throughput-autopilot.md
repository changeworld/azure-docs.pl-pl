---
title: Utwórz kontenery usługi Azure Cosmos i bazy danych w trybie autopilotażu.
description: Poznaj korzyści, przypadki użycia i sposób aprowizacji baz danych i kontenerów usługi Azure Cosmos w trybie autopilotażu.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3e2d9b892ad42563b481a0b1fe6a468daefad672
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606423"
---
# <a name="create-azure-cosmos-containers-and-databases-in-autopilot-mode-preview"></a>Tworzenie kontenerów i baz danych usługi Azure Cosmos w trybie autopilotażu (wersja zapoznawcza)

Azure Cosmos DB pozwala na inicjowanie obsługi przepływności w kontenerach w trybie ręcznym lub autopilotażowym. W tym artykule opisano zalety i przypadki użycia trybu autopilotażu.

> [!NOTE]
> Tryb autopilotażu jest obecnie dostępny w publicznej wersji zapoznawczej.

Oprócz ręcznego inicjowania obsługi przepływności można teraz skonfigurować kontenery usługi Azure Cosmos w trybie autopilotażu. Kontenery usługi Azure Cosmos i bazy danych skonfigurowane w trybie automatycznego pilotażu będą **automatycznie i natychmiast skalować zainicjowaną przepływność na podstawie potrzeb aplikacji bez naruszenia umowy SLA.**

Nie trzeba już ręcznie zarządzać zainicjowaną przepływność lub obsłużyć problemów z ograniczaniem szybkości. Kontenery usługi Azure Cosmos skonfigurowane w trybie autopilotażu mogą być natychmiast skalowane w odpowiedzi na obciążenie, bez wywierania wpływu na dostępność, opóźnienia, przepływność lub wydajność obciążeń globalnie. W przypadku wysokiego użycia kontenery usługi Azure Cosmos skonfigurowane w trybie autopilotażu można skalować w górę lub w dół bez wywierania wpływu na bieżące operacje.

Podczas konfigurowania kontenerów i baz danych w trybie autopilotażu należy określić maksymalną przepływność, `Tmax` nie można jej przekroczyć. Kontenery mogą następnie szybko skalować w oparciu o potrzeby związane z obciążeniem w zakresie `0.1*Tmax < T < Tmax`. Innymi słowy, kontenery i bazy danych są skalowane szybko w zależności od potrzeb związanych z obciążeniem, od najmniej do 10% maksymalnej skonfigurowanej przepływności oraz do skonfigurowanej maksymalnej wartości przepływności. W dowolnym momencie można zmienić ustawienie maksymalnej przepływności (Tmax) dla bazy danych lub kontenera w systemie.

W trakcie korzystania z wersji zapoznawczej autopilotażu dla określonej maksymalnej przepływności w kontenerze lub w bazie danych system umożliwia działanie w ramach obliczonego limitu magazynu. W przypadku przekroczenia limitu magazynowania maksymalna przepływność jest automatycznie dostosowywana do wyższej wartości. W przypadku korzystania z przepływności poziomu bazy danych z trybem autopilotażu liczba kontenerów dozwolona w ramach bazy danych jest obliczana jako: (0,001 * maksymalna przepływność). Na przykład jeśli zainicjujesz 20 000 autopilotaż RU/s, baza danych może mieć 20 kontenerów.

## <a name="benefits-of-autopilot-mode"></a>Zalety trybu pilotażowego

Kontenery usługi Azure Cosmos, które są skonfigurowane w trybie autopilotażu, mają następujące zalety:

* **Prosty:** Kontenery w trybie autopilotażu usuwają złożoność do ręcznego zarządzania elastyczną przepływność (jednostek ru) i pojemnością dla różnych kontenerów.

* **Skalowalność:** Kontenery w trybie autopilotażu umożliwiają bezproblemowe skalowanie alokowanej przepływności zgodnie z potrzebami. Nie ma przerw w połączeniach klientów, aplikacje i nie wpływają na żadne istniejące umowy SLA.

* **Koszt ekonomiczny:** Gdy korzystasz z kontenerów usługi Azure Cosmos skonfigurowanych w trybie autopilotażu, płacisz tylko za zasoby, których obciążenia są wymagane w ciągu godziny.

* **Wysoka dostępność:** Kontenery usługi Azure Cosmos w trybie pilotażu używają tego samego globalnie dystrybuowanego, odpornego na błędy i zaplecza o wysokiej dostępności, aby zapewnić trwałość danych i wysoką dostępność.

## <a name="use-cases-of-autopilot-mode"></a>Przypadki użycia trybu autopilotażu

Przypadki użycia w przypadku kontenerów usługi Azure Cosmos skonfigurowanych w trybie pilotażu obejmują:

* **Obciążenia zmienne:** Gdy korzystasz z aplikacji ze szczytowym użyciem przez 1 godzinę do kilku godzin, lub kilka razy na rok. Przykłady obejmują aplikacje dla zasobów ludzkich, budżetowania i raportowania operacyjnego. W takich scenariuszach można korzystać z kontenerów skonfigurowanych w trybie autopilotażu, ale nie trzeba już ręcznie udostępniać ich w celu zapewnienia szczytu lub średniej wydajności.

* **Nieprzewidywalne obciążenia:** W przypadku korzystania z obciążeń, w których w ciągu dnia występuje użycie bazy danych, ale także szczytowe działania, które są trudne do przewidywania. Przykład obejmuje lokację ruchu, która widzi wzrost aktywności w przypadku zmiany prognozy pogody. Kontenery skonfigurowane w trybie autopilotażu dostosowują pojemność w celu spełnienia wymagań szczytowego obciążenia aplikacji i skalowania w dół w przypadku przekroczenia działania.

* **Nowe aplikacje:** Jeśli wdrażasz nową aplikację i nie wiesz, jak dużo zainicjowanej przepływności (tj. ilu jednostek ru) potrzebujesz. Przy użyciu kontenerów skonfigurowanych w trybie automatycznego pilotażu można automatycznie skalować do potrzeb i wymagań aplikacji.

* **Rzadko używane aplikacje:** Jeśli masz aplikację, która jest używana tylko przez kilka godzin w ciągu dnia lub tygodnia lub miesiąca, na przykład w przypadku aplikacji/witryny sieci Web/blogu o niskiej wielkości.

* **Tworzenie i testowanie baz danych:** Deweloperzy używają kont usługi Azure Cosmos w godzinach pracy, ale nie potrzebują ich w nocy lub w weekendy. W przypadku kontenerów skonfigurowanych w trybie pilotażowym skalowanie w dół do minimum, gdy nie jest używane.

* **Zaplanowane obciążenia produkcyjne/zapytania:** Jeśli masz serię zaplanowanych żądań/operacji/zapytań w jednym kontenerze i jeśli istnieją bezczynne okresy, w których chcesz uruchamiać bezwzględnie niską przepływność, możesz to zrobić łatwo. Gdy zaplanowana kwerenda/żądanie zostanie przesłane do kontenera skonfigurowanego w trybie autopilotażu, będzie on automatycznie skalowany w górę, jak to konieczne, i uruchomić operację.

Rozwiązania dotyczące poprzednich problemów nie tylko wymagają dużego czasu w implementacji, ale również wprowadzają złożoność w konfiguracji lub kodzie i często wymagają ręcznej interwencji do ich rozwiązania. Tryb autopilotażu włącza powyższe scenariusze, dzięki czemu nie trzeba już martwić się o te problemy.

## <a name="comparison--containers-configured-in-manual-mode-vs-autopilot-mode"></a>Porównanie — kontenery skonfigurowane w trybie ręcznym a trybem autopilotażu

|  | Kontenery skonfigurowane w trybie ręcznym  | Kontenery skonfigurowane w trybie autopilotażu |
|---------|---------|---------|
| **Elastyczna przepływność** | Inicjowane ręcznie | Automatycznie i chwilowo skalowane na podstawie wzorców użycia obciążeń. |
| **Szybkość ograniczania żądań/operacji (429)**  | Może się tak zdarzyć, jeśli zużycie przekracza przypuszczalną pojemność. | Nie nastąpi, jeśli używana przepływność mieści się w maksymalnej przepływności wybranej z trybem autopilotażu.   |
| **Planowanie wydajności** |  Należy wykonać planowanie początkowej pojemności i zapewnić wymaganą przepływność. |    Nie musisz martwić się o planowanie pojemności. System automatycznie zajmuje się planowaniem pojemności i zarządzaniem pojemnością. |
| **Cennik** | Ręcznie aprowizacji RU/s na godzinę. | W przypadku kont z jednym regionem zapisu płacisz za przepływność używaną co godzinę przy użyciu częstotliwości autopilotażowej RU/s na godzinę. <br/><br/>W przypadku kont z wieloma regionami zapisu nie ma dodatkowej opłaty za autopilotaż. Opłata jest naliczana za przepływność użyta co godzinę przy użyciu tego samego kursu wieloskładnikowego RU/s na godzinę. |
| **Najlepiej dopasowane do typów obciążeń** |  Przewidywalne i stabilne obciążenia|   Nieprzewidywalne obciążenia i zmienne  |

## <a name="enable-autopilot-from-azure-portal"></a>Włącz autopilotaż z Azure Portal

Możesz wypróbować autopilotaż na kontach usługi Azure Cosmos, włączając w Azure Portal. Wykonaj następujące kroki, aby włączyć opcję autopilotaż:

1. Zaloguj się do [Azure Portal.](https://portal.azure.com)

2. Przejdź do konta usługi Azure Cosmos i Otwórz kartę **nowe funkcje** . Wybierz pozycję **autopilotaż** i **zarejestruj** , jak pokazano na poniższym zrzucie ekranu:

![Tworzenie kontenera w trybie autopilotażu](./media/provision-throughput-autopilot/enable-autopilot-azure-portal.png)

## <a name="create-a-database-or-a-container-with-autopilot-mode"></a>Tworzenie bazy danych lub kontenera z trybem autopilotażu

Można skonfigurować autopilotaż dla baz danych lub kontenerów podczas ich tworzenia. Wykonaj następujące kroki, aby użyć nowej bazy danych lub kontenera, włączyć program autopilotaż i określić maksymalną przepływność.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) lub w [Eksploratorze usługi Azure Cosmos.](https://cosmos.azure.com/)

1. Przejdź do konta usługi Azure Cosmos i Otwórz kartę **Eksplorator danych** .

1. Wybierz pozycję **nowy kontener**, wprowadź nazwę kontenera, klucz partycji. Wybierz opcję **autopilotaż** i wybierz maksymalną przepływność, której nie może przekroczyć czas użycia opcji autopilotażu.

   ![Tworzenie kontenera w trybie autopilotażu](./media/provision-throughput-autopilot/create-container-autopilot-mode.png)

1. Kliknij przycisk **OK**

W przypadku podobnych kroków można również utworzyć bazę danych z zainicjowaną przepływność w trybie autopilotażu.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat [partycji logicznych](partition-data.md).
* Dowiedz się, jak [zainicjować przepływność na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).
* Dowiedz się, jak [udostępnić przepływność w bazie danych Azure Cosmos](how-to-provision-database-throughput.md).
