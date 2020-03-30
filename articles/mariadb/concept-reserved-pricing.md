---
title: Przedpłata za obliczenia z zarezerwowaną pojemnością — usługa Azure Database dla mariadb
description: Przedpłata za zasoby obliczeniowe MariaDB dla usługi Azure Database o pojemności zarezerwowanej
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 9a6bd3cab41c69075f5170a8a3aad4f059d970e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159051"
---
# <a name="prepay-for-azure-database-for-mariadb-compute-resources-with-reserved-capacity"></a>Przedpłata za zasoby obliczeniowe MariaDB dla usługi Azure Database o pojemności zarezerwowanej

Usługa Azure Database for MariaDB pomaga teraz zaoszczędzić pieniądze, płacąc z góry za zasoby obliczeniowe w porównaniu z cenami płatności zgodnie z rzeczywistym ratami. Dzięki zarezerwowanej pojemności usługi Azure Database dla mariadb użytkownik podejmuje zobowiązanie z góry na serwerach MariaDB na okres jednego lub trzech lat, aby uzyskać znaczną zniżkę na koszty obliczeń. Aby zakupić usługę Azure Database dla pojemności zarezerwowanej MariaDB, należy określić region platformy Azure, typ wdrożenia, warstwę wydajności i termin. </br>

Nie trzeba przypisywać rezerwacji do określonej bazy danych platformy Azure dla serwerów MariaDB. Już uruchomiona usługa Azure Database dla mariadb lub te, które są nowo wdrożone, automatycznie uzyskać korzyści z zarezerwowanych cen. Kupując rezerwację, płacisz z góry za koszty obliczeń przez okres jednego lub trzech lat. Jak tylko kupisz rezerwację, bazy danych platformy Azure dla MariaDB opłat obliczeniowych, które pasują do atrybutów rezerwacji nie są już naliczane według stawek płatności zgodnie z rzeczywistymu. Rezerwacja nie obejmuje opłat za oprogramowanie, sieci ani pamięci masowej skojarzonych z serwerem bazy danych MariaDB. Po zakończeniu okresu rezerwacji świadczenie rozliczeniowe wygasa, a usługa Azure Database dla mariadb jest rozliczana według ceny płatności zgodnie z rzeczywistymi oczekiwaniami. Rezerwacje nie są odnawiane automatycznie. Aby uzyskać informacje o cenach, zobacz [usługę Azure Database for MariaDB reserved capacity offering](https://azure.microsoft.com/pricing/details/mariadb/). </br>

Usługę Azure Database for MariaDB można kupić w [witrynie Azure portal](https://portal.azure.com/). Aby kupić pojemność rezerwową:

* Musisz być w roli właściciela dla co najmniej jednej subskrypcji Enterprise lub indywidualnej ze stawkami płatności zgodnie z rzeczywistymu polub.
* W przypadku subskrypcji Enterprise w witrynie [EA Portal](https://ea.azure.com/) musi być włączona opcja **Dodaj wystąpienia zarezerwowane**. Lub, jeśli to ustawienie jest wyłączone, musisz być administratorem EA w subskrypcji.
* W przypadku programu Dostawcy rozwiązań w chmurze (CSP) tylko agenci administracyjni lub agenci sprzedaży mogą kupować usługę Azure Database dla pojemności zarezerwowanej mariadb. </br>

Szczegółowe informacje na temat sposobu naliczanie opłat za zakupy rezerwacji klientom korporacyjnym i klientom korzystającym z płatności zgodnie z rzeczywistym użyciem, zobacz [opis użycia rezerwacji platformy Azure dla rejestracji w przedsiębiorstwie](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) i zrozumienie użycia rezerwacji platformy Azure dla subskrypcji płatności zgodnie z rzeczywistym [użyciem.](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)


## <a name="determine-the-right-server-size-before-purchase"></a>Określ odpowiedni rozmiar serwera przed zakupem

Rozmiar rezerwacji powinien być oparty na całkowitej ilości obliczeń używanych przez istniejące lub wkrótce wdrożone wystąpienie baz danych w określonym regionie i przy użyciu tej samej warstwy wydajności i generowania sprzętu.</br>

Załóżmy na przykład, że używasz jednego ogólnego przeznaczenia, Gen5 – 32 vCore MariaDB bazy danych i dwie pamięci zoptymalizowane, Gen5 – 16 vCore MariaDB baz danych. Ponadto, miejmy przypuszcza, że planujesz wdrożyć w ciągu najbliższego miesiąca dodatkowy ogólny cel, Gen5 – 32 serwer bazy danych vCore i jeden zoptymalizowany pod kątem pamięci, Gen5 – 16 serwer bazy danych vCore. Załóżmy, że wiesz, że będziesz potrzebować tych zasobów przez co najmniej 1 rok. W takim przypadku należy zakupić 64 (2x32) vCore, 1 rok rezerwacji dla pojedynczej bazy danych ogólnego przeznaczenia - Gen5 i 48 (2x16 + 16) vCore 1 rok rezerwacji dla jednej pamięci bazy danych zoptymalizowane - Gen5


## <a name="buy-azure-database-for-mariadb-reserved-capacity"></a>Kupowanie pojemności zarezerwowanej bazy danych Azure database dla bazy danych MariaDB

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Wybierz **pozycję Rezerwacje wszystkich usług** > **Reservations**.
3.  Wybierz **pozycję Dodaj,** a następnie w okienku Rezerwacje zakupu wybierz pozycję **Azure Database for MariaDB,** aby kupić nową rezerwację dla baz danych MariaDB.
4.  Wypełnij wymagane pola. Istniejące lub nowe bazy danych, które pasują do wybranych atrybutów, kwalifikują się do uzyskania rabatu na pojemność rezerwową. Rzeczywista liczba usługi Azure Database dla serwerów MariaDB, które otrzymują rabat zależy od wybranego zakresu i ilości.


![Przegląd cen zastrzeżonych](media/concepts-reserved-pricing/mariadb-reserved-price.png)


W poniższej tabeli opisano wymagane pola.

| Pole | Opis |
| :------------ | :------- |
| Subskrypcja   | Subskrypcja używana do płacenia za usługę Azure Database dla rezerwacji pojemności zarezerwowanej mariadb. Metoda płatności w ramach subskrypcji jest obciążona kosztami początkowymi dla usługi Azure Database dla rezerwacji pojemności zarezerwowanej MariaDB. Typ subskrypcji musi być umową enterprise (numery ofert: MS-AZR-0017P lub MS-AZR-0148P) lub indywidualną umową z cenami płatności zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P). W przypadku subskrypcji Enterprise opłaty są odliczane od salda zobowiązania pieniężnego rejestracji lub naliczane jako nadwyżka. W przypadku pojedynczej subskrypcji z ceną płatności zgodnie z rzeczywistym użyciem opłaty są naliczane na kartę kredytową lub metodę płatności faktury w ramach subskrypcji.
| Zakres | Zakres rezerwacji vCore może obejmować jedną subskrypcję lub wiele subskrypcji (zakres udostępniony). W przypadku wybrania: </br></br> **Współużytkowany**rabat rezerwacji vCore jest stosowany do usługi Azure Database dla serwerów MariaDB uruchomionych w dowolnej subskrypcji w kontekście rozliczeń. Dla klientów korporacyjnych zakresem udostępnionym jest rejestracja i obejmuje wszystkie subskrypcje w ramach rejestracji. W przypadku klientów z płatnością zgodnie z rzeczywistym użyciem zakresem udostępnionym są wszystkie subskrypcje z opcją płatności zgodnie z rzeczywistym użyciem utworzone przez administratora konta.</br></br> **Pojedyncza subskrypcja**, rabat rezerwacji vCore jest stosowany do usługi Azure Database dla serwerów MariaDB w tej subskrypcji. </br></br> **Pojedyncza grupa zasobów**, rabat rezerwacji jest stosowany do usługi Azure Database dla serwerów MariaDB w wybranej subskrypcji i wybranej grupy zasobów w ramach tej subskrypcji.
| Region | Region platformy Azure, który jest objęty usługą Azure Database dla rezerwacji pojemności zarezerwowanej MariaDB.
| Typ wdrożenia | Typ zasobu usługi Azure Database dla mariadb, dla którego chcesz kupić rezerwację.
| Warstwa wydajności | Warstwa usług dla usług Azure Database dla serwerów MariaDB.
| Termin | Jeden rok
| Liczba | Ilość zasobów obliczeniowych kupowanych w ramach usługi Azure Database dla rezerwacji pojemności zarezerwowanej MariaDB. Ilość jest liczbą owo wirtualnych w wybranym regionie platformy Azure i warstwy wydajności, które są zarezerwowane i otrzymają rabat rozliczeniowy. Na przykład jeśli używasz lub planujesz uruchomić usługę Azure Database dla serwerów MariaDB o całkowitej pojemności obliczeniowej gen5 16 ykonietów wirtualnych w regionie Wschodnich stanów USA, należy określić ilość jako 16, aby zmaksymalizować korzyści dla wszystkich serwerów.

## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zwrot rezerwacji

Rezerwacje można anulować, wymieniać lub zwracać, jednak obowiązują przy tym pewne ograniczenia. Aby uzyskać więcej informacji, zobacz temat [Self-service exchanges and refunds for Azure Reservations](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund) (Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure).

## <a name="vcore-size-flexibility"></a>Elastyczność rozmiaru vCore

Elastyczność rozmiaru klasy wirtualnej ułatwia skalowanie w górę lub w dół w obrębie warstwy wydajności i regionu bez utraty korzyści z zarezerwowanej pojemności. 

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

Rabat rezerwacji vCore jest stosowany automatycznie do liczby serwerów usługi Azure Database dla MariaDB, które są zgodne z usługą Azure Database dla zakresu rezerwacji zarezerwowanej pojemności MariaDB i atrybutów. Można zaktualizować zakres bazy danych platformy Azure dla rezerwacji pojemności zarezerwowanej MariaDB za pośrednictwem witryny Azure portal, PowerShell, cli lub za pośrednictwem interfejsu API. </br></br>
Aby dowiedzieć się, jak zarządzać usługą Azure Database dla pojemności zarezerwowanej MariaDB, zobacz zarządzanie usługą Azure Database dla pojemności zarezerwowanej MariaDB.

Aby dowiedzieć się więcej na temat usługi Azure Reservations, zobacz następujące artykuły:

* [Co to są rezerwacje platformy Azure?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
* [Zarządzanie rezerwacjami platformy Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Informacje na temat rabatu na rezerwacje platformy Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mariadb)
* [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Azure Reservations in Partner Center Cloud Solution Provider (CSP) program](https://docs.microsoft.com/partner-center/azure-reservations) (Rezerwacje platformy Azure w programie Cloud Solution Provider w Centrum partnerskim)
