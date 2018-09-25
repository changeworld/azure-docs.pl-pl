---
title: Zapłać z góry za zasoby usługi Azure Cosmos DB zaoszczędzić pieniądze | Dokumentacja firmy Microsoft
description: Dowiedz się, jak kupić pojemność zarezerwowane usługi Azure Cosmos DB, aby zaoszczędzić na kosztach infrastruktury obliczeniowej.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 1be2d67d8a1ee51c4883ae1f50b80ad3a9691c2d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46981971"
---
# <a name="prepay-for-azure-cosmos-db-resources-with-reserved-capacity"></a>Zapłać z góry za zasoby usługi Azure Cosmos DB przy użyciu rezerwowanie pojemności

Usługa Azure Cosmos DB zastrzeżone wydajność pomaga oszczędności przez wstępnie płacić za zasoby usługi Azure Cosmos DB przez jeden rok lub trzy lata. Pojemność na platformie Azure Cosmos DB zastrzeżone pozwala uzyskać rabat na przepływnością aprowizowaną dla zasobów usługi Cosmos DB, na przykład bazy danych, kontenery (kolekcje/tabel/wykresy). Pojemność na platformie Azure Cosmos DB zastrzeżone mogą znacznie zmniejszyć koszty usługi Cosmos DB — nawet o 65% w normalnych cen — jeden rok lub trzy lata podejmowania z góry zobowiązań. Rezerwowanie pojemności zniżki rozliczeń i nie ma wpływu na stan środowiska uruchomieniowego zasobów usługi Cosmos DB.

Pojemność na platformie Azure Cosmos DB zarezerwowane obejmuje przepływnością aprowizowaną dla zasobów, nie obejmuje koszty magazynu i sieci. Tak szybko, jak można kupić rezerwację, opłaty za przepustowość, zgodne zastrzeżenia, których atrybuty nie są już wynoszą płatności — jako — można przejść stawki. Aby uzyskać więcej informacji na temat rezerwacji, zobacz [Azure rezerwacje](../billing/billing-save-compute-costs-reservations.md) artykułu. 

Możesz kupić pojemność usługi Azure Cosmos DB, zarezerwowane z [witryny Azure portal](https://portal.azure.com). Aby kupić rezerwowanie pojemności:

* Musisz być w roli właściciela dla co najmniej jeden Enterprise lub subskrypcję z płatnością za rzeczywiste użycie.  
* W przypadku subskrypcji Enterprise, zakup rezerwacji platformy Azure musi być włączona w [portalu EA.](https://ea.azure.com/)  
* Dla programu Cloud Solution Provider (CSP) tylko agentów administratora lub agentom sprzedaży można kupić pojemność usługi Azure Cosmos DB, zastrzeżone.

## <a name="determine-the-required-throughput-before-purchase"></a>Określenia wymaganych informacji o przepływności przed dokonaniem zakupu

Rozmiar rezerwacji powinna być oparta na łączną przepływność używana przez istniejące lub wkrótce do--wdrażanych zasoby usługi Azure Cosmos DB (na przykład bazy danych lub kontenerów — w kolekcji, tabele, wykresy). Można określić przepustowość wymagana w następujący sposób:

* Przejdź do [witryny Azure Portal](https://portal.azure.com), Znajdź swoje konto usługi Azure Cosmos DB, otwórz blok metryk i uzyskiwanie szczegółowych informacji średniej przepływności na sekundę z **przepływności** kartę w ciągu 3 – 6 miesięcy. Przy zakupie należy podać ten rozmiar jako liczba jednostek rezerwowanie pojemności.

Alternatywnie Jeśli masz umowy Enterprise Agreement (EA), można pobrać pliku użycia i odwoływać się do **typ usługi** wartość w **dodatkowe informacje** sekcji plik użycia, aby pobrać serwer usługi Azure Cosmos DB Szczegóły dotyczące przepływności.

Można również zsumowania średniej przepływności dla wszystkich obciążeń na konta usługi Azure Cosmos DB, przewidywanie uruchomiony następny rok lub trzy lata, a następnie użyć tej ilości rezerwacji.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Kup pojemność usługi Azure Cosmos DB zastrzeżone

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).  

2. Wybierz **wszystkich usług** > **rezerwacje** > **Dodaj**.  

3. Z **Wybieranie typu produktu** okienku wybierz **usługi Azure Cosmos DB**, a następnie **wybierz** kupić nową rezerwację.  

4. Wypełnij wymagane pola, zgodnie z opisem w poniższej tabeli:

   ![Wypełnij formularz rezerwowanie pojemności](./media/cosmos-db-reserved-capacity/fill_reserved_capacity_form.png) 

   |Pole  |Opis  |
   |---------|---------|
   |Name (Nazwa)   |    Nazwa rezerwacji. To pole jest wypełniane automatycznie przy użyciu `CosmosDB_Reservation_<timeStamp>`. Można podać inną nazwę podczas tworzenia rezerwacji lub zmień jej nazwę, po utworzeniu rezerwacji.      |
   |Subskrypcja  |   Subskrypcja użytej do zapłacenia za pojemność usługi Azure Cosmos DB zastrzeżone. Sposób zapłaty w wybranej subskrypcji jest używany podczas ładowania ponoszonych z góry kosztów. Typ subskrypcji musi być jedną z następujących czynności: <br/><br/>  [Umowy Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) (numer oferty: MS-AZR - 0017 P) — dla subskrypcji enterprise opłaty są odejmowane od salda zobowiązania pieniężnego rejestracji lub rozliczane jako nadwyżkowe użycie. <br/><br/> [Płatność za rzeczywiste użycie](https://azure.microsoft.com/offers/ms-azr-0003p/) (numer oferty: MS-AZR - 0003 P) — płatność za rzeczywiste użycie subskrypcji, opłaty są naliczane na metodę płatności karty kredytowej lub faktury w ramach subskrypcji.    |
   |Zakres   |   Zakres rezerwacji określa mogą korzystać z ilu subskrypcji skojarzonych z rezerwacji korzyściami dotyczącymi rozliczeń i określa, jak Rezerwacja ma zostać zastosowana do określonej subskrypcji. Rezerwacja ma zakres subskrypcji pojedynczego lub współdzielona. Jeśli wybierzesz:   <br/><br/>  **Subskrypcja pojedyncza** — rabat związany z rezerwacją jest stosowany do wystąpienia usługi Azure Cosmos DB w wybranej subskrypcji. <br/><br/>  **Udostępnione** — rabat związany z rezerwacją jest stosowany do wystąpienia usługi Azure Cosmos DB, działające w dowolnej subskrypcji w ramach kontekstu rozliczeń. Kontekstu rozliczeń jest określana w oparciu o sposób rejestracji w usłudze Azure. Dla klientów korporacyjnych zakres udostępniony jest rejestracji i zawiera wszystkie subskrypcje (z wyjątkiem tworzenia i testowania subskrypcje) w ramach rejestracji. Dla klientów rozliczana według bieżącego użycia udostępnionych zakres jest wszystkie subskrypcje płatność za rzeczywiste użycie utworzonego przez administratora konta.  <br/><br/> Zakres rezerwacji można zmienić po zakupieniu rezerwowanie pojemności.  |
   |Typ pojemności zarezerwowanej   |  Typ rezerwowanie pojemności jest zaaprowizowanej jako liczba jednostek żądania przepływności.|
   |Jednostka pojemności zarezerwowanej  |      Ilość przepustowości, które mają zostać zarezerwowane. Można obliczyć tej wartości po określeniu przepływności wymagane dla wszystkich zasobów usługi Cosmos DB (na przykład bazy danych lub kontenery) na region i należy go przez liczbę regionów, które zostanie skojarzony z bazy danych usługi Cosmos DB pomnożyć.  <br/> Na przykład: Jeśli masz pięciu regionach o 1 mln jednostek RU na sekundę w każdym regionie, wybierz opcję 5 mln jednostek RU na sekundę dla zakupu pojemności rezerwacji.    |
   |Termin  |   Jeden rok lub trzy lata.   |

5. Przejrzyj rabat i cena rezerwacji w **koszty** sekcji. Cena Rezerwacja ma zastosowanie do zasobów usługi Azure Cosmos DB z przepływnością aprowizowaną we wszystkich regionach.  

6. Wybierz pozycję **Kup**. Po pomyślnym zakupu, możesz zobaczyć Poniższy zrzut ekranu. 

   ![Wypełnij formularz rezerwowanie pojemności](./media/cosmos-db-reserved-capacity/reserved_capacity_successful.png) 

Po zakupie rezerwacji, zostanie zastosowana natychmiast do żadnych istniejących zasobów usługi Azure Cosmos DB, zgodne z warunkami rezerwacji. Jeśli nie masz żadnych istniejących zasobów usługi Azure Cosmos DB rezerwacji zostaną zastosowane podczas wdrażania nowego wystąpienia usługi Cosmos DB, odpowiadający warunki rezerwacji. W obu przypadkach okres rezerwacji rozpoczyna się natychmiast po zakupie się pomyślnie. 

Po wygaśnięciu rezerwacji wystąpień usługi Azure Cosmos DB będzie nadal działać i są rozliczane według stawek płatności zgodnie z rzeczywistym regularne.

## <a name="next-steps"></a>Kolejne kroki

Rabat związany z rezerwacją jest automatycznie stosowane do zasobów usługi Azure Cosmos DB, które odpowiadają zakresem rezerwacji i atrybutów. Można zaktualizować zakresu rezerwacji za pośrednictwem witryny Azure portal, programu PowerShell, interfejsu wiersza polecenia lub interfejsu API.

*  Aby dowiedzieć się, jak rezerwowanie pojemności, które są stosowane rabaty za usługą Azure Cosmos DB, zobacz [zrozumieć rezerwacje platformy Azure z rabatami](../billing/billing-understand-cosmosdb-reservation-charges.md)

* Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:

   * [Co to są rezerwacje platformy Azure?](../billing/billing-save-compute-costs-reservations.md)  
   * [Zarządzanie Azure rezerwacje](../billing/billing-manage-reserved-vm-instance.md).  
   * [Opis zastrzeżenia dla Twojej rejestracji Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md)  
   * [Opis zastrzeżenia dla Twojej subskrypcji zgodnie z rzeczywistym użyciem](../billing/billing-understand-reserved-instance-usage.md)
   * [Rezerwacje platformy Azure w programie partnera Centrum Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal masz dodatkowe pytania, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.

