---
title: Oszczędność kosztów
description: Dowiedz się, jak kupić pojemność rezerwową usługi Azure SQL Database, aby zaoszczędzić na kosztach obliczeniowych.
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
ms.openlocfilehash: 0713e1ed98cc87b95cad1f84672148cd062e0b58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979986"
---
# <a name="save-costs-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Oszczędzaj koszty zasobów obliczeniowych bazy danych SQL dzięki pojemności zarezerwowanej usługi Azure SQL Database

Oszczędzaj pieniądze dzięki usłudze Azure SQL Database, zobowiązując się do rezerwacji zasobów obliczeniowych w porównaniu z cenami płatności zgodnie z rzeczywistym ratunkiem. Dzięki pojemności zarezerwowanej usługi Azure SQL Database użytkownik podejmuje zobowiązanie do korzystania z bazy danych SQL przez okres jednego lub trzech lat, aby uzyskać znaczny rabat na koszty obliczeń. Aby zakupić pojemność zarezerwowaną bazy danych SQL, należy określić region platformy Azure, typ wdrożenia, warstwę wydajności i termin.


Nie trzeba przypisywać rezerwacji do określonych wystąpień bazy danych SQL (pojedyncze bazy danych, puli elastycznych lub wystąpień zarządzanych). Pasujące wystąpienia bazy danych SQL, które są już uruchomione lub te, które są nowo wdrożone, automatycznie otrzymają korzyści. Kupując rezerwację, zobowiązujesz się do użycia kosztów obliczeń przez okres jednego lub trzech lat. Jak tylko kupisz rezerwację, sql database obliczeń opłat, które pasują do atrybutów rezerwacji nie są już naliczane według stawek płatności zgodnie z rzeczywistym dzieje. Rezerwacja nie obejmuje opłat za oprogramowanie, sieci lub magazynu skojarzonych z wystąpieniem bazy danych SQL. Po zakończeniu okresu rezerwacji świadczenie rozliczeniowe wygasa, a bazy danych SQL są rozliczane według ceny płatności zgodnie z rzeczywistym i wyjazdem. Rezerwacje nie są odnawiane automatycznie. Aby uzyskać informacje o cenach, zobacz [ofertę pojemności zarezerwowanej bazy danych SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Zarezerwowaną pojemność usługi Azure SQL Database można kupić w [witrynie Azure portal](https://portal.azure.com). Płatność za rezerwację jest wnoszona [z góry lub w ratach miesięcznych](../cost-management-billing/reservations/monthly-payments-reservations.md). Aby kupić pojemność rezerwową bazy danych SQL:

- Musisz być w roli właściciela dla co najmniej jednej subskrypcji Enterprise lub indywidualnej ze stawkami płatności zgodnie z rzeczywistymu polub.
- W przypadku subskrypcji Enterprise w witrynie [EA Portal](https://ea.azure.com) musi być włączona opcja **Dodaj wystąpienia zarezerwowane**. Lub, jeśli to ustawienie jest wyłączone, musisz być administratorem EA w subskrypcji.
- W przypadku programu Dostawcy rozwiązań w chmurze (CSP) tylko agenci administracyjni lub agenci sprzedaży mogą kupować pojemność rezerwową bazy danych SQL.

Szczegółowe informacje na temat sposobu naliczanie opłat za zakupy rezerwacji klientom korporacyjnym i klientom korzystającym z płatności zgodnie z rzeczywistym użyciem, zobacz [opis użycia rezerwacji platformy Azure dla rejestracji w przedsiębiorstwie](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) i zrozumienie użycia rezerwacji platformy Azure dla subskrypcji płatności zgodnie z rzeczywistym [użyciem.](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="determine-the-right-sql-size-before-purchase"></a>Określ odpowiedni rozmiar SQL przed zakupem

Rozmiar rezerwacji powinien być oparty na całkowitej ilości obliczeń używanych przez istniejące lub wkrótce wdrożone pojedyncze bazy danych, pule elastyczne lub wystąpienia zarządzane w określonym regionie i przy użyciu tej samej warstwy wydajności i generowania sprzętu.

Załóżmy na przykład, że używasz jednego ogólnego przeznaczenia, Gen5 – 16 vCore elastycznej puli i dwóch krytycznych dla firmy, Gen5 — 4 vCore pojedynczych baz danych. Ponadto, miejmy przypuszcza, że planujesz wdrożyć w ciągu następnego miesiąca dodatkowy ogólnego przeznaczenia, Gen5 - 16 vCore elastycznej puli i jeden biznesowy krytyczny, Gen5 – 32 vCore elastycznej puli. Załóżmy również, że wiesz, że będziesz potrzebować tych zasobów przez co najmniej 1 rok. W takim przypadku należy zakupić 32 (2x16) vCores, 1 rok rezerwacji dla pojedynczej bazy danych/ puli elastycznej ogólnego przeznaczenia - Gen5 i 40 (2x4 + 32) vCore 1 rok rezerwacji dla pojedynczej bazy danych / elastycznej puli biznesowej krytyczne - Gen5.

## <a name="buy-sql-database-reserved-capacity"></a>Kupowanie wydajności rezerwowej usługi SQL Database

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz **pozycję Rezerwacje wszystkich usług** > **Reservations**.
3. Wybierz **pozycję Dodaj,** a następnie w okienku Rezerwacje zakupu wybierz pozycję **Baza danych SQL,** aby kupić nową rezerwację dla bazy danych SQL.
4. Wypełnij wymagane pola. Istniejące lub nowe pojedyncze bazy danych, pule elastyczne lub wystąpienia zarządzane, które pasują do wybranych atrybutów, kwalifikują się do uzyskania rabatu zarezerwowaną. Rzeczywista liczba wystąpień bazy danych SQL, które otrzymują rabat zależy od wybranego zakresu i ilości.
    ![Zrzut ekranu przed przesłaniem zakupu pojemności zarezerwowanej bazy danych SQL](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

W poniższej tabeli opisano wymagane pola.

| Pole      | Opis|
|------------|--------------|
|Subskrypcja|Subskrypcja używana do płacenia za rezerwację pojemności zarezerwowanej bazy danych SQL. Metoda płatności w ramach subskrypcji jest obciążona kosztami początkowymi za rezerwację pojemności zarezerwowanej bazy danych SQL. Typ subskrypcji musi być umową enterprise (numery ofert: MS-AZR-0017P lub MS-AZR-0148P) lub indywidualną umową z cenami płatności zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P). W przypadku subskrypcji Enterprise opłaty są odliczane od salda zobowiązania pieniężnego rejestracji lub naliczane jako nadwyżka. W przypadku pojedynczej subskrypcji z ceną płatności zgodnie z rzeczywistym użyciem opłaty są naliczane na kartę kredytową lub metodę płatności faktury w ramach subskrypcji.|
|Zakres       |Zakres rezerwacji vCore może obejmować jedną subskrypcję lub wiele subskrypcji (zakres udostępniony). W przypadku wybrania: <br/><br/>**Współdzielony**rabat rezerwacji vCore jest stosowany do wystąpień bazy danych SQL uruchomionych w dowolnej subskrypcji w kontekście rozliczeń. Dla klientów korporacyjnych zakresem udostępnionym jest rejestracja i obejmuje wszystkie subskrypcje w ramach rejestracji. W przypadku klientów z płatnością zgodnie z rzeczywistym użyciem zakresem udostępnionym są wszystkie subskrypcje z opcją płatności zgodnie z rzeczywistym użyciem utworzone przez administratora konta.<br/><br/>**Pojedyncza subskrypcja**, rabat rezerwacji vCore jest stosowany do wystąpień bazy danych SQL w tej subskrypcji. <br/><br/>**Pojedyncza grupa zasobów**, rabat rezerwacji jest stosowany do wystąpień bazy danych SQL w wybranej subskrypcji i wybranej grupy zasobów w ramach tej subskrypcji.|
|Region      |Region platformy Azure, który jest objęty rezerwacją pojemności zarezerwowanej bazy danych SQL.|
|Typ wdrożenia|Typ zasobu SQL, dla którego chcesz kupić rezerwację.|
|Warstwa wydajności|Warstwa usług dla wystąpień bazy danych SQL.
|Termin        |Rok lub trzy lata.|
|Liczba    |Ilość zasobów obliczeniowych kupowanych w ramach rezerwacji pojemności zarezerwowanej bazy danych SQL. Ilość jest liczbą owo wirtualnych w wybranym regionie platformy Azure i warstwy wydajności, które są zarezerwowane i otrzymają rabat rozliczeniowy. Na przykład jeśli są uruchomione lub planuje uruchomić sql database wystąpień o całkowitej pojemności obliczeniowej Gen5 16 vCores w regionie wschodnich stanów USA, a następnie należy określić ilość jako 16, aby zmaksymalizować korzyści dla wszystkich wystąpień. |

1. Przejrzyj koszt rezerwacji zarezerwowanej pojemności bazy danych SQL w sekcji **Koszty.**
1. Wybierz pozycję **Kup**.
1. Wybierz **pozycję Wyświetl tę rezerwację,** aby zobaczyć stan zakupu.

## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zwrot rezerwacji

Rezerwacje można anulować, wymieniać lub zwracać, jednak obowiązują przy tym pewne ograniczenia. Aby uzyskać więcej informacji, zobacz temat [Self-service exchanges and refunds for Azure Reservations](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md) (Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure).

## <a name="vcore-size-flexibility"></a>Elastyczność rozmiaru vCore

Elastyczność rozmiaru klasy wirtualnej ułatwia skalowanie w górę lub w dół w obrębie warstwy wydajności i regionu bez utraty korzyści z zarezerwowanej pojemności. Zdolność do zarezerwowania bazy danych SQL zapewnia również elastyczność tymczasowego przenoszenia gorących baz danych między pulami i pojedynczymi bazami danych w ramach normalnych operacji (w tym samym regionie i warstwie wydajności) bez utraty zarezerwowanej pojemności Korzyści. Utrzymując nierozliszczowany bufor w rezerwacji, można skutecznie zarządzać skoki wydajności bez przekraczania budżetu.

## <a name="limitation"></a>Ograniczenia

Nie można zarezerwować baz danych SQL opartych na uJ (podstawowych, standardowych lub premium).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

Rabat rezerwacji vCore jest stosowany automatycznie do liczby wystąpień bazy danych SQL, które są zgodne z zakresem rezerwacji i atrybutami rezerwacji zarezerwowanej pojemności bazy danych SQL. Zakres rezerwacji pojemności zarezerwowanej bazy danych SQL można zaktualizować za pośrednictwem [witryny Azure portal](https://portal.azure.com), programu PowerShell, interfejsu wiersza polecenia lub interfejsu API.

Aby dowiedzieć się, jak zarządzać rezerwacją pojemności zarezerwowanej bazy danych SQL, zobacz [zarządzanie pojemnością rezerwową bazy danych SQL](../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Aby dowiedzieć się więcej na temat usługi Azure Reservations, zobacz następujące artykuły:

- [Co to są rezerwacje platformy Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Zarządzanie rezerwacjami platformy Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Informacje na temat rabatu na rezerwacje platformy Azure](../cost-management-billing/reservations/understand-reservation-charges.md)
- [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Azure Reservations in Partner Center Cloud Solution Provider (CSP) program](https://docs.microsoft.com/partner-center/azure-reservations) (Rezerwacje platformy Azure w programie Cloud Solution Provider w Centrum partnerskim)
