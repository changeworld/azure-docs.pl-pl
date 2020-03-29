---
title: Zarezerwowana pojemność w usłudze Azure Cosmos DB w celu optymalizacji kosztów
description: Dowiedz się, jak kupić pojemność zarezerwowaną usługi Azure Cosmos DB, aby zaoszczędzić na kosztach obliczeniowych.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 16e8f770445218e10ab7e7645a81325d11be55da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505972"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Optymalizacja kosztów z użyciem pojemności zarezerwowanej w usłudze Azure Cosmos DB

Pojemność zarezerwowana usługi Azure Cosmos DB pomaga zaoszczędzić pieniądze dzięki zobowiązaniu do korzystania z rezerwacji zasobów usługi Azure Cosmos DB przez jeden rok lub trzy lata. Dzięki pojemności zarezerwowanej usługi Azure Cosmos DB możesz uzyskać rabat na przepływność zaprowizowaną na potrzeby zasobów usługi Cosmos DB. Przykładami zasobów są bazy danych i kontenery (tabele, kolekcje i wykresy).

Pojemność zarezerwowana usługi Azure Cosmos DB może znacznie obniżyć koszty&mdash;usługi Cosmos DB nawet o 65 procent w porównaniu z cenami regularnymi, przy rocznym lub trzyletnim zobowiązaniu z góry. Zarezerwowana pojemność zapewnia rabat rozliczeniowy i nie wpływa na stan środowiska wykonawczego zasobów usługi Azure Cosmos DB.

Pojemność zarezerwowana usługi Azure Cosmos DB obejmuje przepływność aprowizowaną dla zasobów. Nie dotyczy opłat za magazyn i sieć. Jak tylko kupisz rezerwację, opłaty przepływności zgodne z atrybutami rezerwacji nie są już naliczane według stawek płatności zgodnie z rzeczywistym przejściem. Aby uzyskać więcej informacji na temat rezerwacji, zobacz artykuł [o rezerwacjach platformy Azure.](../cost-management-billing/reservations/save-compute-costs-reservations.md)

Możesz kupić pojemność zarezerwowaną usługi Azure Cosmos DB w [witrynie Azure portal](https://portal.azure.com). Płatność za rezerwację jest wnoszona [z góry lub w ratach miesięcznych](../cost-management-billing/reservations/monthly-payments-reservations.md). Aby kupić pojemność zarezerwowaną:

* Musisz być w roli właściciela dla co najmniej jednej subskrypcji Enterprise lub indywidualnej ze stawkami płatności zgodnie z rzeczywistymu polub.  
* W przypadku subskrypcji Enterprise w witrynie [EA Portal](https://ea.azure.com) musi być włączona opcja **Dodaj wystąpienia zarezerwowane**. Lub, jeśli to ustawienie jest wyłączone, musisz być administratorem EA w subskrypcji.
* W przypadku programu Dostawcy rozwiązań w chmurze (CSP) tylko agenci administracyjni lub agenci sprzedaży mogą kupować pojemność zarezerwowaną usługi Azure Cosmos DB.

## <a name="determine-the-required-throughput-before-purchase"></a>Określ wymaganą przepustowość przed zakupem

Rozmiar zakupu zarezerwowanej pojemności powinien być oparty na całkowitej przepływności, która będzie używana przez istniejące lub wkrótce wdrożone zasoby usługi Azure Cosmos DB co godzinę. Na przykład: Kup 30 000 pojemności zarezerwowanych RU/s, jeśli jest to spójny wzorzec użycia godzinowego. W tym przykładzie wszelkie aprowizowaną przepływność powyżej 30 000 ru/s będą rozliczane przy użyciu stawki płatności zgodnie z rzeczywistym użyciem. Jeśli aprowizowana przepływność jest niższa niż 30 000 RU/s w ciągu godziny, zostanie zmarnowana dodatkowa zarezerwowana pojemność dla tej godziny.

Obliczamy rekomendacje zakupu na podstawie twojego godzinowego wzorca użytkowania. Zalecane jest użycie w ciągu ostatnich 7, 30 i 60 dni, a zaleca się zakup pojemności zarezerwowanej, który maksymalizuje oszczędności. Zalecane rozmiary rezerwacji można wyświetlić w witrynie Azure portal, wykonując następujące kroki:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).  

2. Wybierz **pozycję Wszystkie usługi** > **Rezerwacje** > **Dodaj**.

3. W okienku **Rezerwacje zakupu** wybierz pozycję **Azure Cosmos DB**.

4. Wybierz kartę **Zalecane,** aby wyświetlić zalecane rezerwacje:

Zalecenia można filtrować według następujących atrybutów:

- **Okres (1** rok lub 3 lata)
- **Częstotliwość rozliczeń** (miesięczna lub początkowa)
- **Typ przepływności** (RU vs Multi-master RU)

Ponadto można zakres zalecenia, aby być w ramach jednej grupy zasobów, pojedynczej subskrypcji lub całej rejestracji platformy Azure. 

Oto przykładowe zalecenie:

![Zalecenia dotyczące pojemności zarezerwowanej](./media/cosmos-db-reserved-capacity/reserved-capacity-recommendation.png)

To zalecenie zakupu rezerwacji 30.000 RU / s wskazuje, że wśród 3-letnich rezerwacji, 30.000 RU / s wielkość rezerwacji zmaksymalizuje oszczędności. W takim przypadku zalecenie jest obliczane na podstawie ostatnich 30 dni użycia usługi Azure Cosmos DB. Jeśli ten klient oczekuje, że ostatnie 30 dni użycia usługi Azure Cosmos DB jest reprezentatywne dla przyszłego użycia, będą zmaksymalizować oszczędności, kupując rezerwację 30 000 ru/s.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Kupowanie pojemności zarezerwowanej usługi Azure Cosmos DB

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).  

2. Wybierz **pozycję Wszystkie usługi** > **Rezerwacje** > **Dodaj**.  

3. W okienku **Rezerwacje zakupu** wybierz pozycję **Azure Cosmos DB,** aby kupić nową rezerwację.  

4. Wypełnij wymagane pola zgodnie z opisem w poniższej tabeli:

   ![Wypełnij formularz pojemności zarezerwowanej](./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png)

   |Pole  |Opis  |
   |---------|---------|
   |Zakres   |   Opcja, która określa, ile subskrypcji można użyć korzyści rozliczeniowej skojarzonej z rezerwacją. Kontroluje również sposób stosowania rezerwacji do określonych subskrypcji. <br/><br/>  Jeśli wybierzesz **Udostępnione**, rabat rezerwacji jest stosowany do wystąpień usługi Azure Cosmos DB, które są uruchamiane w dowolnej subskrypcji w kontekście rozliczeń. Kontekst rozliczeń jest oparty na tym, jak zarejestrowałeś się na platformie Azure. Dla klientów korporacyjnych zakresem udostępnionym jest rejestracja i obejmuje wszystkie subskrypcje w ramach rejestracji. W przypadku klientów korzystających z płatności zgodnie z rzeczywistymi oczekiwaniami zakresem współużytkowane są wszystkie subskrypcje indywidualne ze stawkami płatności zgodnie z rzeczywistymu po drodze utworzonymi przez administratora konta.  <br/><br/>  Jeśli wybierzesz **pojedynczą subskrypcję,** rabat rezerwacji zostanie zastosowany do wystąpień usługi Azure Cosmos DB w wybranej subskrypcji. <br/><br/> Jeśli wybierzesz **pojedynczą grupę zasobów,** rabat rezerwacji zostanie zastosowany do wystąpień usługi Azure Cosmos DB w wybranej subskrypcji i wybranej grupy zasobów w ramach tej subskrypcji. <br/><br/> Zakres rezerwacji można zmienić po zakupie zarezerwowanej pojemności.  |
   |Subskrypcja  |   Subskrypcja używana do płacenia za pojemność zarezerwowaną usługi Azure Cosmos DB. Metoda płatności w wybranej subskrypcji jest używana do obciążania kosztów. Subskrypcja musi być jednym z następujących typów: <br/><br/>  Enterprise Agreement (numery ofert: MS-AZR-0017P lub MS-AZR-0148P): W przypadku subskrypcji Enterprise opłaty są odejmowane od salda zobowiązań pieniężnych rejestracji lub naliczane jako nadmierne. <br/><br/> Subskrypcja indywidualna ze stawkami płatności zgodnie z rzeczywistym obciążeniem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P): W przypadku indywidualnej subskrypcji ze stawkami płatności zgodnie z rzeczywistym ratą opłaty są naliczane na podstawie metody płatności kartą kredytową lub fakturą w ramach subskrypcji.    |
   | Grupa zasobów | Grupa zasobów, do której stosowany jest rabat pojemności zarezerwowanej. |
   |Termin  |   Rok lub trzy lata.   |
   |Typ przepływności   |  Przepływność jest aprowizowana jako jednostki żądań. Można kupić rezerwację dla aprowizowanej przepływności dla obu konfiguracji — zapisy w jednym regionie, a także zapisy z wielu regionów. Typ przepływności ma dwie wartości do wyboru: 100 RU/s na godzinę i 100 wielosztowych RU/s na godzinę.|
   | Jednostki pojemności zarezerwowanej| Ilość przepływności, którą chcesz zarezerwować. Tę wartość można obliczyć, określając przepływność potrzebną dla wszystkich zasobów usługi Cosmos DB (na przykład baz danych lub kontenerów) na region. Następnie należy pomnożyć go przez liczbę regionów, które będą skojarzone z bazy danych usługi Cosmos. Na przykład: Jeśli masz pięć regionów z 1 milionem RU/s w każdym regionie, wybierz 5 milionów RU/s dla zakupu zdolności rezerwacji. |


5. Po wypełnieniu formularza obliczana jest cena wymagana do zakupu zarezerwowanej pojemności. Dane wyjściowe pokazują również procent rabatu, który otrzymujesz z wybranymi opcjami. Następne **kliknięcie wybierz**

6. W okienku **Rezerwacje zakupu** przejrzyj rabat i cenę rezerwacji. Ta cena rezerwacji ma zastosowanie do zasobów usługi Azure Cosmos DB z aprowizowaną przepływnością we wszystkich regionach.  

   ![Podsumowanie pojemności zarezerwowanej](./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png)

7. Wybierz **opcję Recenzja + kup,** a następnie kup **teraz**. Po pomyślnym zakupie zostanie wyświetlona następująca strona:

Po zakupie rezerwacji jest natychmiast stosowana do wszystkich istniejących zasobów usługi Azure Cosmos DB, które odpowiadają warunkom rezerwacji. Jeśli nie masz żadnych istniejących zasobów usługi Azure Cosmos DB, rezerwacja zostanie zastosowana podczas wdrażania nowego wystąpienia usługi Cosmos DB, które pasuje do warunków rezerwacji. W obu przypadkach okres rezerwacji rozpoczyna się natychmiast po pomyślnym zakupie.

Po wygaśnięciu rezerwacji wystąpienia usługi Azure Cosmos DB będą nadal działać i są rozliczane według regularnych stawek płatności zgodnie z rzeczywistymu pojechać.

## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zwrot rezerwacji

Rezerwacje można anulować, wymieniać lub zwracać, jednak obowiązują przy tym pewne ograniczenia. Aby uzyskać więcej informacji, zobacz temat [Self-service exchanges and refunds for Azure Reservations](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md) (Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure).

## <a name="next-steps"></a>Następne kroki

Rabat rezerwacji jest stosowany automatycznie do zasobów usługi Azure Cosmos DB, które odpowiadają zakresowi rezerwacji i atrybutom. Zakres rezerwacji można zaktualizować za pośrednictwem witryny Azure portal, powershell, interfejsu wiersza polecenia platformy Azure lub interfejsu API.

*  Aby dowiedzieć się, jak rabaty zarezerwowane są stosowane do usługi Azure Cosmos DB, zobacz [Opis rabatu rezerwacji platformy Azure.](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md)

* Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:

   * [Co to są rezerwacje platformy Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)  
   * [Zarządzanie rezerwacjami platformy Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)  
   * [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)  
   * [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
   * [Rezerwacje platformy Azure w programie CSP Centrum partnerów](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
