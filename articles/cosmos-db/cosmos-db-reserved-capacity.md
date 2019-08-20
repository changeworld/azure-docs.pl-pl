---
title: Optymalizowanie kosztów Azure Cosmos DB zasobów przy użyciu zarezerwowanej pojemności
description: Dowiedz się, jak kupić Azure Cosmos DB zarezerwowaną pojemność, aby zaoszczędzić na kosztach obliczeniowych.
author: bandersmsft
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: e4d33756f1e09abedbf828deb447fbf14c6bfed7
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616918"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Optymalizacja kosztów z zarezerwowaną pojemnością w Azure Cosmos DB

Azure Cosmos DB zarezerwowana pojemność pomaga zaoszczędzić pieniądze dzięki przedpłaceniu za zasoby Azure Cosmos DB przez jeden rok lub trzy lata. W przypadku Azure Cosmos DB zarezerwowanej pojemności można uzyskać rabat dla zainicjowanej przepustowości dla Cosmos DB zasobów. Przykładami zasobów są bazy danych i kontenery (tabele, kolekcje i wykresy).

Azure Cosmos DB zarezerwowana pojemność może znacząco obniżyć koszty&mdash;Cosmos DB do 65% w regularnych cenach przy użyciu jednego roku lub trzech lat zobowiązania z góry. Zarezerwowana pojemność zapewnia rabat na rozliczenia i nie wpływa na stan czasu wykonywania zasobów Azure Cosmos DB.

Azure Cosmos DB zarezerwowana pojemność obejmuje przepływność zainicjowaną dla zasobów. Nie dotyczy to opłat za magazyn i sieć. Po zakupieniu rezerwacji opłaty za przepływność, które pasują do atrybutów rezerwacji, nie są już naliczane według stawek płatności zgodnie z rzeczywistym użyciem. Aby uzyskać więcej informacji na temat rezerwacji, zobacz artykuł dotyczący zastrzeżeń na [platformie Azure](../billing/billing-save-compute-costs-reservations.md) .

Możesz kupić Azure Cosmos DB zarezerwowaną pojemność z [Azure Portal](https://portal.azure.com). Aby kupić zastrzeżoną pojemność:

* Musisz być w roli właściciela dla co najmniej jednej subskrypcji przedsiębiorstwa lub indywidualnej, która ma stawki płatność zgodnie z rzeczywistym użyciem.  
* W przypadku subskrypcji przedsiębiorstwa w [portalu EA](https://ea.azure.com)należy włączyć opcję **Dodawanie wystąpień zarezerwowanych** . Jeśli to ustawienie jest wyłączone, musisz być administratorem EA w subskrypcji.
* W przypadku programu Cloud Solution Provider (CSP) tylko agenci administracyjni lub agenci sprzedaży mogą kupować Azure Cosmos DB zarezerwowaną pojemność.

## <a name="determine-the-required-throughput-before-purchase"></a>Określ wymaganą przepływność przed zakupem

Rozmiar rezerwacji powinien opierać się na całkowitej ilości przepływności, która będzie używana przez istniejące lub wkrótce wdrożone zasoby Azure Cosmos DB. Możesz określić wymaganą przepływność w następujący sposób:

* Pobierz dane historyczne dotyczące całkowitej alokowanej przepływności dla kont Azure Cosmos DB, baz danych i kolekcji we wszystkich regionach. Na przykład można oszacować dzienną średnią przepływność, pobierając instrukcję codziennego użycia z `https://account.azure.com`.

* Jeśli jesteś klientem z Umowa Enterprise (EA), możesz pobrać plik użycia, aby uzyskać informacje dotyczące Azure Cosmos DB przepływności. Zapoznaj się z wartością **typu usługi** w sekcji **dodatkowe informacje** w pliku użycia.

* Można zsumować średnią przepływność dla wszystkich obciążeń na kontach Azure Cosmos DB, które można uruchamiać w ciągu następnych lub trzech lat. Tej ilości można następnie użyć dla rezerwacji.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Kupowanie Azure Cosmos DB zarezerwowanej pojemności

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).  

2. Wybierz kolejno pozycje **wszystkie usługi** > **rezerwacje** > **Dodaj**.  

3. W okienku **rezerwacje zakupu** wybierz **Azure Cosmos DB** , aby kupić nową rezerwację.  

4. Wypełnij pola wymagane zgodnie z opisem w poniższej tabeli:

   ![Wypełnij formularz zarezerwowanej pojemności](./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png)

   |Pole  |Opis  |
   |---------|---------|
   |Scope   |   Opcja, która kontroluje, ile subskrypcji może korzystać z zalet rozliczeń skojarzonych z rezerwacją. Kontroluje również sposób zastosowania rezerwacji do określonych subskrypcji. <br/><br/>  W przypadku wybrania opcji **udostępnione**rabat jest stosowany do Azure Cosmos DB wystąpień, które są uruchamiane w dowolnej subskrypcji w kontekście rozliczania. Kontekst rozliczania jest oparty na sposobie rejestracji w usłudze Azure. W przypadku klientów korporacyjnych zakresem udostępnionym jest rejestracja i uwzględnianie wszystkich subskrypcji w ramach rejestracji. W przypadku klientów z systemem płatność zgodnie z rzeczywistym użyciem zakresem udostępnionym są wszystkie indywidualne subskrypcje, które są tworzone przez administratora konta.  <br/><br/>  W przypadku wybrania opcji **Pojedyncza subskrypcja**rabat zostanie zastosowany do Azure Cosmos DB wystąpień w ramach wybranej subskrypcji. <br/><br/> W przypadku wybrania opcji **pojedyncze grupy zasobów**rabat rezerwacji zostanie zastosowany do Azure Cosmos DB wystąpień w ramach wybranej subskrypcji i wybranej grupy zasobów w ramach tej subskrypcji. <br/><br/> Można zmienić zakres rezerwacji po zakupie zarezerwowanej pojemności.  |
   |Subscription  |   Subskrypcja, która jest używana do płacenia za Azure Cosmos DB zarezerwowaną pojemność. Metoda płatności w wybranej subskrypcji jest używana w ramach ładowania kosztów z góry. Subskrypcja musi być jednym z następujących typów: <br/><br/>  Umowa Enterprise (numery ofert: MS-AZR-0017P lub MS-AZR-0148P): W przypadku subskrypcji przedsiębiorstwa opłaty są odejmowane od salda zobowiązania pieniężnego rejestracji lub są naliczane jako nadwyżkowe. <br/><br/> Indywidualna subskrypcja z stawką płatność zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P): W przypadku pojedynczej subskrypcji z stawką płatności zgodnie z rzeczywistym użyciem opłaty są naliczane za kartę kredytową lub formę płatności faktury dla subskrypcji.    |
   | Grupa zasobów | Grupa zasobów, w której jest stosowany rabat zarezerwowanej pojemności. |
   |Termin  |   Jeden rok lub trzy lata.   |
   |Typ przepływności   |  Obsługa przepływności jest obsługiwana jako jednostki żądania. Możesz kupić rezerwację dla zainicjowanej przepływności dla obu konfiguracji — zapisu w jednym regionie, a także zapisów w wielu regionach. Typ przepływności ma dwie wartości do wyboru: 100 RU/s na godzinę i 100 wielu głównych RU/s na godzinę.|
   | Jednostki pojemności zarezerwowanej| Ilość przepływności, która ma zostać zarezerwowana. Tę wartość można obliczyć, określając przepływność potrzebną dla wszystkich zasobów Cosmos DB (na przykład baz danych lub kontenerów) na region. Następnie można je pomnożyć przez liczbę regionów, które zostaną skojarzone z bazą danych Cosmos. Przykład: Jeśli masz pięć regionów z 1 000 000 RU/s w każdym regionie, wybierz pozycję 5 000 000 RU/s na potrzeby zakupu zdolności rezerwacji. |


5. Po wypełnieniu formularza jest obliczana cena wymagana do zakupu zarezerwowanej pojemności. W danych wyjściowych jest również wyświetlany procent rabatu uzyskany z wybranymi opcjami. Następnie kliknij pozycję **Wybierz** .

6. W okienku **rezerwacje zakupu** Przejrzyj rabat i cenę rezerwacji. Ta cena rezerwacji dotyczy zasobów Azure Cosmos DB z obsługą przepływności we wszystkich regionach.  

   ![Podsumowanie pojemności zarezerwowanych](./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png)

7. Wybierz pozycję **Recenzja + Kup** , a następnie **Kup teraz**. Po pomyślnym zakupie zostanie wyświetlona następująca strona:

Po zakupieniu rezerwacji zostanie ona natychmiast zastosowana do wszystkich istniejących zasobów Azure Cosmos DB, które pasują do warunków rezerwacji. Jeśli nie masz żadnych istniejących zasobów Azure Cosmos DB, rezerwacja zostanie zastosowana podczas wdrażania nowego wystąpienia Cosmos DB, które pasuje do warunków rezerwacji. W obu przypadkach okres rezerwacji rozpoczyna się natychmiast po pomyślnym zakupie.

Po wygaśnięciu rezerwacji Azure Cosmos DB wystąpienia będą nadal działać i są rozliczane według zwykłych stawek płatności zgodnie z rzeczywistym użyciem.

## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zastrzeżenia zwrotne

Aby uzyskać pomoc dotyczącą identyfikacji odpowiedniej pojemności zarezerwowanej, zobacz [Opis sposobu stosowania rabatu rezerwacji do Azure Cosmos DB](../billing/billing-understand-cosmosdb-reservation-charges.md).

Można anulować, wymienić lub refundować rezerwacje z pewnymi ograniczeniami. Aby uzyskać więcej informacji, zobacz samoobsługowe [weksle i zwroty dla Azure Reservations](../billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="next-steps"></a>Następne kroki

Rabat rezerwacji jest automatycznie stosowany do zasobów Azure Cosmos DB, które pasują do zakresu rezerwacji i atrybutów. Zakres rezerwacji można zaktualizować za pomocą Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API.

*  Aby dowiedzieć się, jak zarezerwowane rabaty pojemności są stosowane do Azure Cosmos DB, zobacz [Opis rabatu rezerwacji platformy Azure](../billing/billing-understand-cosmosdb-reservation-charges.md).

* Aby dowiedzieć się więcej na temat zastrzeżeń platformy Azure, zobacz następujące artykuły:

   * [Co to są rezerwacje platformy Azure?](../billing/billing-save-compute-costs-reservations.md)  
   * [Zarządzanie rezerwacjami platformy Azure](../billing/billing-manage-reserved-vm-instance.md)  
   * [Informacje na temat użycia rezerwacji na potrzeby rejestracji w przedsiębiorstwie](../billing/billing-understand-reserved-instance-usage-ea.md)  
   * [Informacje na temat użycia rezerwacji dla subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem](../billing/billing-understand-reserved-instance-usage.md)
   * [Rezerwacje platformy Azure w programie partnerskim programu Partner Center](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
