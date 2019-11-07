---
title: Oszczędność kosztów Azure SQL Database rdzeni wirtualnych
description: Dowiedz się, jak kupić Azure SQL Database zarezerwowaną pojemność, aby zaoszczędzić na kosztach obliczeniowych.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 08/29/2019
ms.openlocfilehash: 70e274d765b409d4024155f8553ba92200598394
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687529"
---
# <a name="save-costs-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Oszczędność kosztów SQL Database zasobów obliczeniowych z zastrzeżoną pojemnością Azure SQL Database

Oszczędność pieniędzy dzięki Azure SQL Database, co umożliwia zatwierdzenie rezerwacji zasobów obliczeniowych w porównaniu z cenami z płatność zgodnie z rzeczywistym użyciem. Dzięki Azure SQL Database zarezerwowanej pojemności zobowiązujesz się do SQL Database użytkowania przez okres jednego lub trzech lat w celu uzyskania znacznego rabatu w kosztach obliczeniowych. Aby kupić SQL Database zarezerwowaną pojemność, należy określić region platformy Azure, typ wdrożenia, warstwę wydajności i termin.


Nie trzeba przypisywać rezerwacji do określonych wystąpień SQL Database (pojedyncze bazy danych, pule elastyczne lub wystąpienia zarządzane). Korzyści wynikające z wystąpienia SQL Database, które są już uruchomione lub które są nowo wdrożone, zostaną automatycznie pobrane. Zakup rezerwacji pozwala na użycie kosztów obliczeniowych przez okres jeden lub trzy lata. Gdy tylko kupisz rezerwację, opłaty za SQL Database obliczeń pasujące do atrybutów rezerwacji nie będą już naliczane według stawek płatności zgodnie z rzeczywistym użyciem. Rezerwacja nie obejmuje opłat za oprogramowanie, sieci lub magazyn związany z wystąpieniem SQL Database. Na koniec okresu rezerwacji korzyść wynikająca z rozliczeń wygaśnie, a bazy danych SQL są rozliczane według ceny płatności zgodnie z rzeczywistym użyciem. Rezerwacje nie są autoodnawiane. Aby uzyskać informacje o cenach, zobacz [SQL Database zarezerwowanej pojemności](https://azure.microsoft.com/pricing/details/sql-database/managed/).

W [Azure Portal](https://portal.azure.com)można kupić Azure SQL Database zarezerwowaną pojemność. Płatność za rezerwację jest wnoszona [z góry lub w ratach miesięcznych](../billing/billing-monthly-payments-reservations.md). Aby kupić SQL Database zarezerwowaną pojemność:

- Musisz być w roli właściciela dla co najmniej jednej subskrypcji przedsiębiorstwa lub indywidualnej, która ma stawki płatność zgodnie z rzeczywistym użyciem.
- W przypadku subskrypcji Enterprise w witrynie **EA Portal** musi być włączona opcja [Dodaj wystąpienia zarezerwowane](https://ea.azure.com). Jeśli to ustawienie jest wyłączone, musisz być administratorem EA w subskrypcji.
- W przypadku programu Cloud Solution Provider (CSP) tylko agenci administracyjni lub agenci sprzedaży mogą zakupić SQL Database zarezerwowaną pojemność.

Szczegółowe informacje na temat tego, w jaki sposób Klienci korporacyjni i klienci korzystający z [usługi](../billing/billing-understand-reserved-instance-usage-ea.md) płatność zgodnie z rzeczywistym użyciem są obciążani opłatami za [zakup rezerwacji. subskrypcja](../billing/billing-understand-reserved-instance-usage.md).

## <a name="determine-the-right-sql-size-before-purchase"></a>Określ właściwy rozmiar bazy danych SQL przed zakupem

Rozmiar rezerwacji powinien opierać się na łącznej ilości obliczeń używanych przez istniejące lub wkrótce wdrożone pojedyncze bazy danych, elastyczne pule lub wystąpienia zarządzane w określonym regionie oraz przy użyciu tej samej warstwy wydajności i generowania sprzętu.

Załóżmy na przykład, że korzystasz z jednego ogólnego celu, 5 rdzeń – 16 rdzeń wirtualny elastycznej puli i dwóch baz danych o znaczeniu krytycznym dla firmy 5 rdzeń — 4 rdzeń wirtualny. Ponadto Załóżmy, że planujesz wdrożenie w następnym miesiącu, w ramach dodatkowego celu ogólnego przeznaczenia, 5 rdzeń – 16 rdzeń wirtualny elastyczną pulę, i jedną z 32 nich. Załóżmy również, że wiesz, że te zasoby będą potrzebne przez co najmniej 1 rok. W takim przypadku należy zakupić 32 (2x16) rdzeni wirtualnych, 1-letnią rezerwację dla pojedynczej bazy danych i elastycznego ogólnego przeznaczenia — 5 rdzeń i 40 (2x4 + 32) rdzeń wirtualny 1 roku dla pojedynczej bazy danych/elastycznej puli firmy krytyczne-5 rdzeń.

## <a name="buy-sql-database-reserved-capacity"></a>Kupowanie SQL Database zarezerwowanej pojemności

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Wszystkie usługi** > **Rezerwacje**.
3. Wybierz pozycję **Dodaj** , a następnie w okienku rezerwacje zakupu wybierz pozycję **SQL Database** , aby zakupić nową rezerwację dla SQL Database.
4. Wypełnij pola wymagane. Istniejące lub nowe pojedyncze bazy danych, elastyczne pule lub wystąpienia zarządzane zgodne z wybranymi atrybutami kwalifikują się do uzyskania rabatu zarezerwowanej pojemności. Rzeczywista liczba wystąpień SQL Database, które pobierają rabat, zależy od wybranego zakresu i ilości.
    ![zrzut ekranu przed przesłaniem SQL Database zakupów pojemności zarezerwowanych](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

W poniższej tabeli opisano wymagane pola.

| Pole      | Opis|
|------------|--------------|
|Subskrypcja|Subskrypcja używana do płacenia za SQL Database zarezerwowaną rezerwację zdolności produkcyjnych. W ramach metody płatności w ramach subskrypcji jest naliczana opłata z tytułu kosztów ponoszonych z góry SQL Database zastrzeżonej rezerwacji. Typ subskrypcji musi być umową Enterprise Agreement (Numer oferty: MS-AZR-0017P lub MS-AZR-0148P) lub indywidualna umowa z cennikiem z opcją płatność zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P). W przypadku subskrypcji Enterprise opłaty są odliczane od salda zobowiązania pieniężnego rejestracji lub naliczane jako nadwyżka. W przypadku indywidualnej subskrypcji z płatnością zgodnie z rzeczywistym użyciem opłaty są naliczane za kartę kredytową lub formę płatności faktury dla subskrypcji.|
|Zakres       |Zakres rezerwacji rdzeń wirtualny może obejmować jedną subskrypcję lub wiele subskrypcji (zakres udostępniony). W przypadku wybrania opcji: <br/><br/>**Udostępniony**, Rabat za rezerwację rdzeń wirtualny jest stosowany do SQL Database wystąpień uruchomionych w ramach dowolnych subskrypcji w kontekście rozliczeń. W przypadku klientów korporacyjnych zakresem udostępnionym jest rejestracja i uwzględnianie wszystkich subskrypcji w ramach rejestracji. W przypadku klientów z płatnością zgodnie z rzeczywistym użyciem zakresem udostępnionym są wszystkie subskrypcje z opcją płatności zgodnie z rzeczywistym użyciem utworzone przez administratora konta.<br/><br/>**Pojedyncza subskrypcja**— rabat rezerwacji rdzeń wirtualny jest stosowany do wystąpień SQL Database w tej subskrypcji. <br/><br/>**Pojedynczej grupy zasobów**, Rabat rezerwacji jest stosowany do SQL Database wystąpień w ramach wybranej subskrypcji i wybranej grupy zasobów w ramach tej subskrypcji.|
|Region      |Region świadczenia usługi Azure objęty SQL Database rezerwacji zarezerwowanej pojemności.|
|Typ wdrożenia|Typ zasobu SQL, dla którego chcesz kupić rezerwację.|
|Warstwa wydajności|Warstwa usługi dla wystąpień SQL Database.
|Termin        |Jeden rok lub trzy lata.|
|Liczba    |Ilość zasobów obliczeniowych zakupionych w ramach rezerwacji SQL Database zarezerwowanej. Ilość to liczba rdzeni wirtualnych w wybranym regionie i warstwie wydajności platformy Azure, które są zarezerwowane i pobierają rabat rozliczeń. Na przykład w przypadku uruchamiania programu lub planowania uruchamiania SQL Database wystąpienia o całkowitej pojemności obliczeniowej 5 rdzeń 16 rdzeni wirtualnych w regionie Wschodnie stany USA należy określić liczbę jako 16, aby zmaksymalizować korzyść dla wszystkich wystąpień. |

1. Zapoznaj się z kosztem rezerwacji SQL Database zarezerwowanej pojemności w sekcji **kosztów** .
1. Wybierz pozycję **Kup**.
1. Wybierz pozycję **Wyświetl to zastrzeżenie** , aby zobaczyć stan zakupu.

## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zwrot rezerwacji

Rezerwacje można anulować, wymieniać lub zwracać, jednak obowiązują przy tym pewne ograniczenia. Aby uzyskać więcej informacji, zobacz temat [Self-service exchanges and refunds for Azure Reservations](../billing/billing-azure-reservations-self-service-exchange-and-refund.md) (Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure).

## <a name="vcore-size-flexibility"></a>elastyczność rozmiaru rdzeń wirtualny

elastyczność rozmiaru rdzeń wirtualny ułatwia skalowanie w górę i w dół w warstwach i regionach wydajności bez utraty korzyści zarezerwowanej pojemności. Zarezerwowana pojemność SQL Database zapewnia również elastyczność tymczasowego przenoszenia gorących baz danych między pulami i pojedynczymi bazami danych w ramach normalnych operacji (w tym samym regionie i warstwie wydajności) bez utraty zarezerwowanej pojemności korzystać. Przez utrzymywanie niezastosowanych buforów w rezerwacji można efektywnie zarządzać wzrostami wydajności bez przekraczania budżetu.

## <a name="limitation"></a>Ograniczenia

Nie można zarezerwować baz danych SQL opartych na jednostkach DTU (podstawowa, standardowa lub Premium).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

Rabat rezerwacji rdzeń wirtualny jest automatycznie stosowany do liczby wystąpień SQL Database, które pasują do SQL Database zakres rezerwacji zarezerwowanej pojemności i atrybuty. Zakres SQL Database zarezerwowanej pojemności można zaktualizować za pomocą [Azure Portal](https://portal.azure.com), programu PowerShell, interfejsu wiersza polecenia lub interfejsu API.

Aby dowiedzieć się, jak zarządzać zarezerwowaną rezerwacją pojemności SQL Database, zobacz [zarządzanie SQL Database zarezerwowaną pojemnością](../billing/billing-manage-reserved-vm-instance.md).

Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:

- [Co to są rezerwacje platformy Azure?](../billing/billing-save-compute-costs-reservations.md)
- [Zarządzanie usługą Azure Reservations](../billing/billing-manage-reserved-vm-instance.md)
- [Informacje na temat rabatu na rezerwacje platformy Azure](../billing/billing-understand-reservation-charges.md)
- [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](../billing/billing-understand-reserved-instance-usage.md)
- [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](../billing/billing-understand-reserved-instance-usage-ea.md)
- [Azure Reservations in Partner Center Cloud Solution Provider (CSP) program](https://docs.microsoft.com/partner-center/azure-reservations) (Rezerwacje platformy Azure w programie Cloud Solution Provider w Centrum partnerskim)
