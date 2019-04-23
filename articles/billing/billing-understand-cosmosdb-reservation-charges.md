---
title: Zrozumienie, jak rabat związany z rezerwacją jest stosowany do usługi Azure Cosmos DB | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rabat związany z rezerwacją jest stosowany do aprowizowana przepływność (RU/s) w usłudze Azure Cosmos DB.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.reviewer: sngun
ms.openlocfilehash: d5a13e4466234d73bafe8dbe76cae92955cf64bd
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60010300"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-cosmos-db"></a>Zrozumienie, jak rabat związany z rezerwacją jest stosowany do usługi Azure Cosmos DB

Po możesz kupić pojemność usługi Azure Cosmos DB, zarezerwowane, rabat związany z rezerwacją jest automatycznie stosowany do zasobów usługi Azure Cosmos DB, które pasują do atrybutów i ilość rezerwacji. Rezerwacja obejmuje przepływnością aprowizowaną dla zasobów usługi Azure Cosmos DB. Nie obejmuje oprogramowanie, sieci, magazynu lub wstępnie zdefiniowane opłaty kontenera.

## <a name="how-reservation-discount-is-applied"></a>Jaki jest stosowany rabat związany z rezerwacją

Rabat związany z rezerwacją jest "*Użyj it lub utracić — it*". Tak Jeśli nie masz żadnych godzinę dopasowywania zasobów, następnie traci ilość rezerwacji dla danej godziny. Nie można wykonać nieużywane zastrzeżone godzin przekazywania.

Podczas zamykania zasobem rabat związany z rezerwacją jest automatycznie stosuje do innego zasobu zgodnego z określonego zakresu. Jeśli żadne pasujące zasoby znajdują się w określonym zakresie, a następnie są zarezerwowane godzin *utracone*.

## <a name="reservation-discount-applied-to-azure-cosmos-db-accounts"></a>Rabat związany z rezerwacją zastosować do kont usługi Azure Cosmos DB

Rabat związany z rezerwacją jest stosowany do [aprowizowanej przepływności](../cosmos-db/request-units.md) pod względem jednostki żądania na sekundę (RU/s) na podstawie godzina po godzinie. Dla zasobów usługi Azure Cosmos DB, które nie korzystają z całą godzinę rabat związany z rezerwacją jest automatycznie stosowany do innych zasobów usługi Cosmos DB, które odpowiada atrybuty rezerwacji. Rabat można zastosować do zasobów usługi Azure Cosmos DB, które są uruchomione jednocześnie. Jeśli nie masz zasoby usługi Cosmos DB, uruchomione przez całą godzinę, które spełniają atrybuty rezerwacji nie uzyskasz pełni czerpać korzyści płynące z rezerwacją dla danej godziny.

Rabaty są organizowane w warstwy. Rezerwacji z wyższej jednostki żądań zapewnia wyższe rabaty.

Zakup rezerwacji będą dotyczyć rabaty we wszystkich regionach współczynnik odpowiednikiem ceny regionalne na żądanie. Aby rezerwacji współczynniki rabatu w każdym regionie, zobacz [rabat związany z rezerwacją regionu](#reservation-discount-per-region) dalszej części tego artykułu.

## <a name="reservation-discount-per-region"></a>Rabat związany z rezerwacją na region
Rabat związany z rezerwacją jest stosowany do kosztów przepustowości godzina po godzinie usługi Azure Cosmos DB. Jest ono stosowane w jednej subskrypcji lub zakres zarejestrowane konta. Rabat związany z rezerwacją mają zastosowanie do mierzenia użycia w różnych regionach, zgodnie z poniższym:

|Opis elementu miernika  |Region |Współczynnik  |
|---------|---------|---------|
|Azure Cosmos DB — 100 RU/s/godz. — wschodni Region Azji i południowo-wschodnia  |  Południowo-Wschodni region Azji i Pacyfiku    |   1      |
|Azure Cosmos DB — 100 RU/s/godz. — wschodni Region Azji i Pacyfiku |   Wschodni region Azji i Pacyfiku   |    1     |
|Azure Cosmos DB — 100 RU/s/godz. — Europa Północna|  Europa Północna       |    1     |
|Azure Cosmos DB — 100 RU/s/godz. — Korea Południowa|    Korea Południowa     |     1    |
|Azure Cosmos DB — 100 RU/s/godz. — Europa Zachodnia|    Europa Zachodnia     |      1   |
|Azure Cosmos DB — 100 RU/s/godz. — Korea środkowa|   Korea Środkowa    |       1  |
|Azure Cosmos DB — 100 RU/s/godz. — UK Południowa|   Południowe Zjednoczone Królestwo      |     1    |
|Azure Cosmos DB — 100 RU/s/godz. — UK-Zachodnia|   Zachodnie Zjednoczone Królestwo      |    1     |
|Azure Cosmos DB — 100 RU/s/godz. — UK Północna |   Północne Zjednoczone Królestwo    |     1    |
|Azure Cosmos DB — 100 RU/s/godz. — UK południe 2|   Południowe Zjednoczone Królestwo 2      |     1    |
|Usługa Azure Cosmos DB — 100 RU/s/godz. — wschodnie stany USA 2|  Wschodnie stany USA 2     |     1    |
|Usługa Azure Cosmos DB — 100 RU/s/godz. — północno-środkowe stany USA|   Północno-środkowe stany USA      |     1    |
|Usługa Azure Cosmos DB — 100 RU/s/godz. — zachodnie stany USA|   Zachodnie stany USA      |     1    |
|Usługa Azure Cosmos DB — 100 RU/s/godz. — środkowe stany USA| Środkowe stany USA        |     1    |
|Usługa Azure Cosmos DB — 100 RU/s/godz. — zachodnie stany USA 2|   Zachodnie stany USA 2      |      1   |
|Usługa Azure Cosmos DB — 100 RU/s/godz. — zachodnio-środkowe stany USA|   Zachodnio-środkowe stany USA      |       1  |
|Usługa Azure Cosmos DB — 100 RU/s/godz. — wschodnie stany USA|   Wschodnie stany USA      |  1       |
|Północna RPA - 100 RU/s/godz. — Azure Cosmos DB|     Północna RPA    |   1      |
|Zachodnia RPA - 100 RU/s/godz. — Azure Cosmos DB |    Zachodnia RPA      |    1     |
|Usługa Azure Cosmos DB — 100 RU/s/godz. — Indie Południowe|    Indie Południowe     |    1.0375    |
|Azure Cosmos DB — 100 RU/s/godz. — Kanada Wschodnia|   Kanada Wschodnia      |    1.1      |
|Azure Cosmos DB — 100 RU/s/godz. — Japonia Wschodnia|   Japonia Wschodnia      |    1.125     |
|Azure Cosmos DB — 100 RU/s/godz. — Japonia Zachodnia|     Japonia Zachodnia    |   1.125       |
|Usługa Azure Cosmos DB — 100 RU/s/godz. — Indie Zachodnie|     Indie Zachodnie    |    1.1375     |
|Usługa Azure Cosmos DB — 100 RU/s/godz. — Indie środkowe|    Indie Środkowe     |  1.1375       |
|Azure Cosmos DB — 100 RU/s/godz. — Australia Wschodnia|     Australia Wschodnia    |   1.15       |
|Azure Cosmos DB — 100 RU/s/godz. — Kanada Środkowa|  Kanada Środkowa       |   1.2       |
|Azure Cosmos DB — 100 RU/s/godz. — Francja środkowa|   Francja Środkowa      |    1.25      |
|Azure Cosmos DB — 100 RU/s/godz. — Brazylia Południowa|  Brazylia Południowa       |   1.5      |
|Azure Cosmos DB — 100 RU/s/godz. — Australia Środkowa|   Australia Środkowa      |   1.5      |
|Azure Cosmos DB — 100 RU/s/godz. — Australia Środkowa 2| Australia Środkowa 2        |    1.5     |
|Azure Cosmos DB — 100 RU/s/godz. — Francja Południowa|    Francja Południowa     |    1.625     |

## <a name="scenarios-that-show-how-the-reservation-discount-is-applied"></a>Scenariusze, które pokazują, jak stosowany jest rabat związany z rezerwacją

Należy wziąć pod uwagę następujące wymagania dla rezerwacji:

* Wymagana przepustowość: 50 000 jednostek RU/s  
* Regiony używane: 2

W takich przypadkach Twoje łączne opłaty na żądanie dotyczą 500 ilość miernika 100 jednostek RU/s w tych dwóch regionach. Całkowite zużycie RU/s jest 100 000 na godzinę.

**Scenariusz 1**

Na przykład załóżmy, że wymagane wdrożenia usługi Azure Cosmos DB w regionach północno-środkowych stanów USA i zachodnie stany USA. W każdym regionie istnieje zużycia przepustowości z 50 000 jednostek RU/s. Zakup rezerwacji 100 000 jednostek RU/s będzie całkowicie saldo opłat na żądanie.

Rabat, który obejmuje rezerwacja jest obliczana jako: zużycie przepustowości * reservation_discount_ratio_for_that_region. W regionach północno-środkowych stanów USA i zachodnie stany USA współczynnik rabat w wysokości rezerwacji jest 1. Dlatego całkowita rabatem jednostek RU/s są 100 000. Ta wartość jest obliczana jako: 50 000 * 1 + 50 000 * 1 = 100 000 jednostek RU/s. Nie trzeba płacić zwykłe stawki żadnych dodatkowych kosztów.

|Opis elementu miernika | Region |Użycie przepływność (RU/s) |Rabat związany z rezerwacją zastosowane do jednostek RU/s |
|---------|---------|---------|---------|
|Usługa Azure Cosmos DB — 100 RU/s/godz. — północno-środkowe stany USA  |   Północno-środkowe stany USA  | 50,000  | 50,000  |
|Usługa Azure Cosmos DB — 100 RU/s/godz. — zachodnie stany USA  |  Zachodnie stany USA   |  50,000  |  50,000 |

**Scenariusz 2**

Na przykład załóżmy, że wymagane wdrożenia usługi Azure Cosmos DB w regionach Australia Środkowa 2 i Francja Południowa. W każdym regionie istnieje zużycia przepustowości z 50 000 jednostek RU/s. Zakup rezerwacji 100 000 jednostek RU/s będzie stosowane w następujący sposób (przy założeniu, że użycie Australia Środkowa 2 z rabatem najpierw):

|Opis elementu miernika | Region |Użycie przepływność (RU/s) |Rabat związany z rezerwacją zastosowane do jednostek RU/s |
|---------|---------|---------|---------|
|Azure Cosmos DB — 100 RU/s/godz. — Australia Środkowa 2  |  Australia Środkowa 2   |  50,000  |  50,000   |
|Azure Cosmos DB — 100 RU/s/godz. — Francja Południowa  |  Francja Południowa   |  50,000 |  15,384  |

Użycie jednostek 50 000 w regionie Australia Środkowa 2 odnosi się do jednostek RU/s 75,000 płatne użycie (lub użycie znormalizowanych). Ta wartość jest obliczana jako: zużycie przepustowości * reservation_discount_ratio_for_that_region. Obliczenie jest równa 75,000 jednostek RU/s użycie płatnych lub znormalizowana. Ta wartość jest obliczana jako: 50 000 * 1.5 = 75 000 jednostek RU/s.

100 000 jednostek RU/s zakupu rezerwacji spowoduje przesunięcie 75,000 jednostek RU/s w Australia Środkowa 2. Pozostawia 25 000 jednostek RU/s do regionu Francja Południowa. Z pozostałych 25 000 jednostek RU/s rabat rezerwacji 15,384 jednostek RU/s jest stosowany do regionu Francja Południowa. Wartość rabatu jest obliczana jako: 25 000 / 1.625 = 15,384 jednostek RU/s. Pozostałe 34,616 jednostek RU/s w regionie, Francja Południowa są naliczane według normalnych stawek za użycie zgodnie z rzeczywistym użyciem.

Systemu rozliczeń platformy Azure spowoduje przypisanie z rezerwacji dotyczącymi rozliczeń korzyścią dla pierwszego wystąpienia, który jest przetwarzany i uwzględnia konfiguracji rezerwacji. Na przykład jest Australia Środkowa 2 w tym przypadku.

Aby zrozumieć i wyświetlić aplikację Azure rezerwacji w rozliczeniach raporty użycia, zobacz [użycia rezerwacji Understand Azure](../billing/billing-understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:

* [Co to są rezerwacji dla platformy Azure](../billing/billing-save-compute-costs-reservations.md)  
* [Zapłać z góry za zasoby usługi Azure Cosmos DB z pojemnością zarezerwowane usługi Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)  
* [Prepay for SQL Database compute resources with Azure SQL Database reserved capacity (Opłacanie zasobów obliczeniowych usługi SQL Database z góry przy użyciu zarezerwowanej pojemności usługi Azure SQL Database)](../sql-database/sql-database-reserved-capacity.md)  
* [Zarządzanie rezerwacji dla platformy Azure](../billing/billing-manage-reserved-vm-instance.md)  
* [Opis zastrzeżenia dla Twojej subskrypcji zgodnie z rzeczywistym użyciem](../billing/billing-understand-reserved-instance-usage.md)  
* [Opis zastrzeżenia dla Twojej rejestracji Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md)
* [Opis zastrzeżenia dla subskrypcji programu CSP](https://docs.microsoft.com/partner-center/azure-reservations)
