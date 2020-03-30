---
title: Przedpłata za obliczenia z zarezerwowaną pojemnością — usługa Azure Cache for Redis
description: Przedpłata za pamięć podręczną Azure dla zasobów obliczeniowych Redis z pojemnością rezerwową
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: aded023c9f4c045f612e33d32c1e3ac71afddf02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77530304"
---
# <a name="prepay-for-azure-cache-for-redis-compute-resources-with-reserved-capacity"></a>Przedpłata za pamięć podręczną Azure dla zasobów obliczeniowych Redis z pojemnością rezerwową

Usługa Azure Cache for Redis pomaga teraz zaoszczędzić pieniądze, płacąc z góry za zasoby obliczeniowe w porównaniu z cenami płatności zgodnie z rzeczywistym ratami. Za pomocą usługi Azure Cache for Redis zarezerwowana pojemność, należy podjąć zobowiązanie z góry w pamięci podręcznej na okres jednego lub trzech lat, aby uzyskać znaczną zniżkę na koszty obliczeń. Aby zakupić pamięć podręczną Azure Cache dla pojemności zarezerwowanej redis, należy określić region platformy Azure, warstwę usług i termin.

Nie trzeba przypisywać rezerwacji do określonej pamięci podręcznej platformy Azure dla wystąpień Redis. Już uruchomiona usługa Azure Cache dla redis lub te, które są nowo wdrożone automatycznie otrzymają korzyści z zarezerwowanych cen, aż do rozmiaru zarezerwowanej pamięci podręcznej. Kupując rezerwację, płacisz z góry za koszty obliczeń przez okres jednego lub trzech lat. Jak tylko kupisz rezerwację, opłaty obliczeniowe usługi Azure Cache for Redis, które pasują do atrybutów rezerwacji, nie są już naliczane według stawek płatności zgodnie z rzeczywistym i wyjazdem. Rezerwacja nie obejmuje opłat sieciowych ani opłat za magazyn skojarzonych z pamięcią podręczną. Po zakończeniu okresu rezerwacji świadczenie rozliczeniowe wygasa, a usługa Azure Cache for Redis jest rozliczana według ceny płatności zgodnie z rzeczywistymu. Rezerwacje nie są odnawiane automatycznie. Aby uzyskać informacje o cenach, zobacz [Usługa Azure Cache for Redis reserved capacity offering](https://azure.microsoft.com/pricing/details/cache).

W [witrynie Azure Portal](https://portal.azure.com/)można kupić pamięć podręczną Azure Cache dla pojemności zarezerwowanej przez usługę Redis. Aby kupić pojemność rezerwową:

* Musisz być w roli właściciela dla co najmniej jednej subskrypcji Enterprise lub indywidualnej ze stawkami płatności zgodnie z rzeczywistymu polub.
* W przypadku subskrypcji Enterprise w witrynie [EA Portal](https://ea.azure.com/) musi być włączona opcja **Dodaj wystąpienia zarezerwowane**. Lub, jeśli to ustawienie jest wyłączone, musisz być administratorem EA w subskrypcji.
* W przypadku programu Dostawcy rozwiązań w chmurze (CSP) tylko agenci administracyjni lub agenci sprzedaży mogą kupować pamięć podręczną usługi Azure Cache dla pojemności zarezerwowanej przez usługę Redis.

Aby uzyskać szczegółowe informacje na temat sposobu naliczania opłat za zakupy rezerwacji klientom korporacyjnym i klientom korzystającym z płatności zgodnie z rzeczywistym użyciem, [zobacz opis użycia rezerwacji platformy Azure dla rejestracji w przedsiębiorstwie](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) i [zrozumienie użycia rezerwacji platformy Azure dla subskrypcji płatności zgodnie z rzeczywistym użyciem.](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)


## <a name="determine-the-right-cache-size-before-purchase"></a>Określanie odpowiedniego rozmiaru pamięci podręcznej przed zakupem

Rozmiar rezerwacji powinien być oparty na całkowitej ilości obliczeń używanych przez istniejącą lub wkrótce wdrożoną pamięć podręczną w określonym regionie i przy użyciu tej samej warstwy usług.

Załóżmy na przykład, że używasz jednego ogólnego przeznaczenia, Pamięci podręcznej Gen5 – 32 vCore i dwóch zoptymalizowanych pod kątem pamięci, Pamięci podręczne Gen5 – 16 vCore. Ponadto, miejmy przypuszcza, że planujesz wdrożyć w ciągu najbliższego miesiąca dodatkowy ogólny cel, Gen5 – 32 serwer bazy danych vCore i jeden zoptymalizowany pod kątem pamięci, Gen5 – 16 serwer bazy danych vCore. Załóżmy, że wiesz, że będziesz potrzebować tych zasobów przez co najmniej 1 rok. W takim przypadku należy zakupić 64 (2x32) vCore, 1 rok rezerwacji dla pojedynczej bazy danych ogólnego przeznaczenia - Gen5 i 48 (2x16 + 16) vCore 1 rok rezerwacji dla jednej pamięci bazy danych zoptymalizowane - Gen5


## <a name="buy-azure-cache-for-redis-reserved-capacity"></a>Kupowanie pamięci podręcznej platformy Azure dla pojemności zarezerwowanej redis

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Wybierz **pozycję Rezerwacje wszystkich usług** > **Reservations**.
3. Wybierz **pozycję Dodaj,** a następnie w okienku Rezerwacje zakupu wybierz pozycję **Azure Cache for Redis,** aby kupić nową rezerwację dla pamięci podręcznych.
4. Wypełnij wymagane pola. Istniejące lub nowe bazy danych, które pasują do wybranych atrybutów, kwalifikują się do uzyskania rabatu na pojemność rezerwową. Rzeczywista liczba pamięci podręcznej azure dla wystąpień Redis, które uzyskać rabat zależy od wybranego zakresu i ilości.


![Przegląd cen zastrzeżonych](media/cache-reserved-pricing/cache-reserved-price.png)


W poniższej tabeli opisano wymagane pola.

| Pole | Opis |
| :------------ | :------- |
| Subskrypcja   | Subskrypcja używana do płacenia za usługę Azure Cache dla rezerwacji pojemności zarezerwowanej przez usługę Redis. Metoda płatności w ramach subskrypcji jest obciążona kosztami początkowymi dla usługi Azure Cache dla rezerwacji pojemności zarezerwowanej przez usługę Redis. Typ subskrypcji musi być umową enterprise (numery ofert: MS-AZR-0017P lub MS-AZR-0148P) lub indywidualną umową z cenami płatności zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P). W przypadku subskrypcji Enterprise opłaty są odliczane od salda zobowiązania pieniężnego rejestracji lub naliczane jako nadwyżka. W przypadku pojedynczej subskrypcji z ceną płatności zgodnie z rzeczywistym użyciem opłaty są naliczane na kartę kredytową lub metodę płatności faktury w ramach subskrypcji.
| Zakres | Zakres rezerwacji może obejmować jedną subskrypcję lub wiele subskrypcji (zakres współużytkowy). W przypadku wybrania: </br></br> **Udostępnione**, rabat rezerwacji jest stosowany do usługi Azure Cache dla wystąpień Redis uruchomionych w dowolnej subskrypcji w kontekście rozliczeń. Dla klientów korporacyjnych zakresem udostępnionym jest rejestracja i obejmuje wszystkie subskrypcje w ramach rejestracji. W przypadku klientów z płatnością zgodnie z rzeczywistym użyciem zakresem udostępnionym są wszystkie subskrypcje z opcją płatności zgodnie z rzeczywistym użyciem utworzone przez administratora konta.</br></br> **Pojedyncza subskrypcja**, rabat rezerwacji jest stosowany do usługi Azure Cache dla wystąpień Redis w tej subskrypcji. </br></br> **Pojedyncza grupa zasobów**, rabat rezerwacji jest stosowany do usługi Azure Cache dla wystąpień Redis w wybranej subskrypcji i wybranej grupy zasobów w ramach tej subskrypcji.
| Region | Region platformy Azure, który jest objęty pamięci podręcznej Azure dla rezerwacji pojemności zarezerwowanej przez usługę Redis.
| Warstwa cenowa | Warstwa usług dla pamięci podręcznej azure dla serwerów Redis.
| Termin | Rok lub trzy lata
| Liczba | Ilość zasobów obliczeniowych kupowanych w pamięci podręcznej azure dla rezerwacji pojemności zarezerwowanej przez usługę Redis. Ilość jest liczba pamięci podręcznych w wybranym regionie platformy Azure i warstwy usług, które są zarezerwowane i otrzymają rabat rozliczeniowy. Na przykład jeśli używasz lub planujesz uruchomić pamięć podręczną Azure dla serwerów Redis o łącznej pojemności pamięci podręcznej 26 GB w regionie wschodnich stanów USA, należy określić ilość jako 26, aby zmaksymalizować korzyści dla wszystkich pamięci podręcznych.

## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zwrot rezerwacji

Rezerwacje można anulować, wymieniać lub zwracać, jednak obowiązują przy tym pewne ograniczenia. Aby uzyskać więcej informacji, zobacz temat [Self-service exchanges and refunds for Azure Reservations](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund) (Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure).

## <a name="cache-size-flexibility"></a>Elastyczność rozmiaru pamięci podręcznej

Elastyczność rozmiaru pamięci podręcznej ułatwia skalowanie w górę lub w dół w obrębie warstwy usług i regionu, bez utraty korzyści zarezerwowanej pojemności.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

Rabat rezerwacji jest stosowany automatycznie do pamięci podręcznej azure dla wystąpień Redis, które pasują do zakresu rezerwacji i atrybutów. Zakres rezerwacji można zaktualizować za pośrednictwem witryny Azure portal, powershell, interfejsu wiersza polecenia platformy Azure lub interfejsu API.

*  Aby dowiedzieć się, jak rabaty zarezerwowaną pojemności są stosowane do usługi Azure Cache for Redis, zobacz [Opis rabatu rezerwacji platformy Azure](../cost-management-billing/reservations/understand-azure-cache-for-redis-reservation-charges.md)

* Aby dowiedzieć się więcej na temat usługi Azure Reservations, zobacz następujące artykuły:

    * [Co to są rezerwacje platformy Azure?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
    * [Zarządzanie rezerwacjami platformy Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
    * [Informacje na temat rabatu na rezerwacje platformy Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
    * [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mysql)
    * [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
    * [Azure Reservations in Partner Center Cloud Solution Provider (CSP) program](https://docs.microsoft.com/partner-center/azure-reservations) (Rezerwacje platformy Azure w programie Cloud Solution Provider w Centrum partnerskim)

