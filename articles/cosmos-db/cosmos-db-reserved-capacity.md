---
title: Optymalizuj koszt zasobów usługi Azure Cosmos DB dzięki rezerwowanie pojemności
description: Dowiedz się, jak kupić pojemność zarezerwowane usługi Azure Cosmos DB, aby zaoszczędzić na kosztach infrastruktury obliczeniowej.
author: bandersmsft
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: b74fcc2e08f02be7adeeab4cfee5f36d5194392c
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508633"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Optymalizowanie kosztów rezerwowanie pojemności w usłudze Azure Cosmos DB

Usługa Azure Cosmos DB zastrzeżone wydajność pomaga zaoszczędzić płacąc wstępnie dla zasobów usługi Azure Cosmos DB za rok lub trzy lata. Pojemność usługi Azure Cosmos DB, zarezerwowana możesz uzyskać rabat w wysokości na przepływnością aprowizowaną dla zasobów usługi Cosmos DB. Przykładami zasobów są baz danych i kontenerów (tabele, kolekcji i wykresy).

Pojemność na platformie Azure Cosmos DB zastrzeżone mogą znacznie zmniejszyć koszty usługi Cosmos DB&mdash;do 65% w normalnych cen za pomocą jednego roku lub trzech lat zobowiązań z góry. Rezerwowanie pojemności zniżki rozliczeń i nie ma wpływu na stan środowiska uruchomieniowego zasobów usługi Azure Cosmos DB.

Pojemność na platformie Azure Cosmos DB zarezerwowane obejmuje przepływnością aprowizowaną dla zasobów. Nie omówiono magazynu i są opłaty za sieć. Tak szybko, jak można kupić rezerwację, opłaty za przepustowość, zgodne zastrzeżenia, których atrybuty nie są już wynoszą płatności — jako — można przejść stawki. Aby uzyskać więcej informacji na temat rezerwacji, zobacz [Azure rezerwacje](../billing/billing-save-compute-costs-reservations.md) artykułu.

Możesz kupić pojemność usługi Azure Cosmos DB, zarezerwowane z [witryny Azure portal](https://portal.azure.com). Aby kupić rezerwowanie pojemności:

* Musisz być w roli właściciela dla co najmniej jeden Enterprise lub indywidualnej subskrypcji z płatnością za rzeczywiste użycie współczynnikami.  
* W przypadku subskrypcji Enterprise **Dodawanie wystąpień zarezerwowanych** musi być włączona w [portalu EA portal](https://ea.azure.com). Lub, jeśli to ustawienie jest wyłączone, musi być administratorem subskrypcji umowy EA.
* Dla programu Cloud Solution Provider (CSP) jedynie agenci administratora lub agentom sprzedaży kupić pojemność usługi Azure Cosmos DB, zastrzeżone.

## <a name="determine-the-required-throughput-before-purchase"></a>Określenia wymaganych informacji o przepływności przed dokonaniem zakupu

Rozmiar rezerwacji powinna być oparta na łączną przepływność, która będzie używana przez istniejące lub wkrótce do--wdrażanych zasobów usługi Azure Cosmos DB. Można określić przepustowość wymagana w następujący sposób:

* Uzyskać dane historyczne łączna aprowizowana przepływność dla konta usługi Azure Cosmos DB, bazy danych i kolekcje we wszystkich regionach. Na przykład możesz ocenić dzienny średni aprowizowanej przepływności, pobierając instrukcji dzienne użycie z `https://account.azure.com`.

* Jeśli jesteś klientem z umową Enterprise Agreement (EA) możesz pobrać pliku użycia, aby uzyskać szczegółowe informacje przepływności usługi Azure Cosmos DB. Zapoznaj się **typ usługi** wartość w **dodatkowe informacje** części pliku użycia.

* Średnia przepływność dla wszystkich obciążeń można zsumowania na kontach usługi Azure Cosmos DB, które zamierzasz uruchomić następny rok lub trzy lata. Następnie można ilość rezerwacji.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Kup pojemność usługi Azure Cosmos DB zastrzeżone

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).  

2. Wybierz **wszystkich usług** > **rezerwacje** > **Dodaj**.  

3. Z **kupować rezerwacje** okienku wybierz **usługi Azure Cosmos DB** kupić nową rezerwację.  

4. Wypełnij wymagane pola, zgodnie z opisem w poniższej tabeli:

   ![Wypełnij formularz rezerwowanie pojemności](./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png)

   |Pole  |Opis  |
   |---------|---------|
   |Scope   |   Opcja, która określa, jak wiele subskrypcji, można użyć korzyściami dotyczącymi rozliczeń skojarzone z rezerwacji. To ustawienie steruje również jak Rezerwacja ma zostać zastosowana do określonej subskrypcji. <br/><br/>  Jeśli wybierzesz **Shared**, rabat związany z rezerwacją jest stosowany do wystąpienia usługi Azure Cosmos DB, które działają w dowolnej subskrypcji w ramach kontekstu rozliczeń. Kontekstu rozliczeń zależy od sposobu zalogowania na platformie Azure. Dla klientów korporacyjnych zakres udostępniony jest rejestracji i zawiera wszystkie subskrypcje w ramach rejestracji. W przypadku klientów zgodnie z rzeczywistym użyciem zakres udostępniony jest wszystkie poszczególne subskrypcje przy użyciu stawki utworzonego przez administratora konta.  <br/><br/>  Jeśli wybierzesz **subskrypcja pojedyncza**, rabat związany z rezerwacją jest stosowany do wystąpienia usługi Azure Cosmos DB w wybranej subskrypcji. <br/><br/> Jeśli wybierzesz **pojedyncza grupa zasobów**, rabat związany z rezerwacją jest stosowany do wystąpienia usługi Azure Cosmos DB w wybranej subskrypcji i wybranej grupy zasobów w ramach tej subskrypcji. <br/><br/> Po zakupie rezerwowanie pojemności, można zmienić zakres rezerwacji.  |
   |Subskrypcja  |   Subskrypcja, która jest używane do płacenia za usługi Azure Cosmos DB wydajności rezerwowej. Sposób zapłaty w wybranej subskrypcji jest używany podczas ładowania ponoszonych z góry kosztów. Typ subskrypcji musi być jedną z następujących czynności: <br/><br/>  Umowy Enterprise Agreement (oferują liczb: MS-AZR-0017P lub MS-AZR - 0148 P): Dla subskrypcji Enterprise opłaty są odejmowane od salda zobowiązania pieniężnego rejestracji lub rozliczane jako nadwyżkowe użycie. <br/><br/> Indywidualnej subskrypcji z płatnością za rzeczywiste użycie współczynnikami (oferują liczb: MS-AZR-0003P lub MS-AZR - 0023 P): Dla indywidualnej subskrypcji z płatnością za rzeczywiste użycie kursów opłaty są naliczane na metodę płatności karty kredytowej lub faktury w ramach subskrypcji.    |
   | Grupa zasobów | Grupa zasobów, do którego jest stosowany rabat w wysokości rezerwowanie pojemności. |
   |Termin  |   Jeden rok lub trzy lata.   |
   |Typ przepływności   |  Przepływność jest aprowizowany w jednostkach żądania. Możesz kupić Rezerwacja aprowizowana przepływność dla obu konfiguracji — jednym regionie zapisuje również jako wiele region zapisu. Typ przepływności ma dwie wartości do wyboru: 100 jednostek RU/s na godzinę i 100 Multi-Master jednostek RU/s za godzinę.|
   | Zastrzeżone jednostki pojemności| Ilość przepustowości, które mają zostać zarezerwowane. Tę wartość można obliczyć, ustalając przepływności wymagane dla wszystkich zasobów usługi Cosmos DB (na przykład bazy danych lub kontenery), na region. Możesz następnie należy go pomnożyć przez liczbę regionów, które zostanie skojarzony z bazą danych Cosmos DB. Na przykład: Jeśli masz pięć regionach o 1 mln jednostek RU na sekundę w każdym regionie świadczenia wybierz 5 mln jednostek RU na sekundę dla zakupu rezerwacji wydajności. |
 

5. Po wypełnieniu formularza, jest obliczana cena musi kupować rezerwowanie pojemności. Dane wyjściowe pokazują również procent rabatu, który jest pobierany za pomocą opcji wybranej. Następnie kliknij przycisk **wybierz**

6. W **kupować rezerwacje** okienku przejrzyj rabat i cena rezerwacji. Cena rezerwacji ma zastosowanie do zasobów usługi Azure Cosmos DB z przepływnością aprowizowaną we wszystkich regionach.  

   ![Rezerwowanie pojemności podsumowania](./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png)

7. Wybierz **przeglądu i Kup** i następnie **Kup teraz**. Zobacz następującą stronę, po pomyślnym zakupu:

Po użytkownik kupić rezerwację, jest stosowana od razu do żadnych istniejących zasobów usługi Azure Cosmos DB, zgodne z warunkami rezerwacji. Jeśli nie masz żadnych istniejących zasobów usługi Azure Cosmos DB rezerwacji zostaną zastosowane podczas wdrażania nowego wystąpienia usługi Cosmos DB, odpowiadający warunki rezerwacji. W obu przypadkach okres rezerwacji rozpoczyna się natychmiast po zakupie się pomyślnie.

Po wygaśnięciu rezerwacji wystąpień usługi Azure Cosmos DB będą nadal działać i są rozliczane według stawek płatności zgodnie z rzeczywistym regularne.

## <a name="cancellation-and-exchanges"></a>Anulowanie i wymiany

Aby uzyskać pomoc w identyfikacji bezpośrednio rezerwowanie pojemności, zobacz [zrozumieć, jak rabat związany z rezerwacją jest stosowany do usługi Azure Cosmos DB](../billing/billing-understand-cosmosdb-reservation-charges.md). Jeśli musisz anulować lub wymiany rezerwacji usługi Azure Cosmos DB, zobacz [wymiany rezerwacji i zwroty](../billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="next-steps"></a>Kolejne kroki

Rabat związany z rezerwacją jest automatycznie stosowane do zasobów usługi Azure Cosmos DB, które odpowiadają zakresem rezerwacji i atrybutów. Można zaktualizować zakresu rezerwacji za pośrednictwem witryny Azure portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API.

*  Aby dowiedzieć się, jak rezerwowanie pojemności, które są stosowane rabaty za usługą Azure Cosmos DB, zobacz [zrozumieć rabat związany z rezerwacją Azure](../billing/billing-understand-cosmosdb-reservation-charges.md).

* Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:

   * [Co to są rezerwacje platformy Azure?](../billing/billing-save-compute-costs-reservations.md)  
   * [Zarządzanie rezerwacjami platformy Azure](../billing/billing-manage-reserved-vm-instance.md)  
   * [Opis zastrzeżenia dla Twojej rejestracji Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md)  
   * [Opis zastrzeżenia dla Twojej subskrypcji zgodnie z rzeczywistym użyciem](../billing/billing-understand-reserved-instance-usage.md)
   * [Rezerwacje platformy Azure w programie CSP Centrum partnerskiego](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
