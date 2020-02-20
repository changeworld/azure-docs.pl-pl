---
title: W jaki sposób rabat za rezerwacje jest stosowany do usługi Azure Database for MySQL
description: W jaki sposób rabat za rezerwacje jest stosowany do usługi Azure Database for MySQL
author: kummanish
ms.author: manishku
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/13/2020
ms.openlocfilehash: ed067e4bbde737cb08afe7c9429854e54e2ef3d9
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199317"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-mysql"></a>W jaki sposób rabat za rezerwacje jest stosowany do usługi Azure Database for MySQL

Po zakupie pojemności zarezerwowanej usługi Azure Database for MySQL rabat za rezerwację jest automatycznie stosowany do serwerów MySQL zgodnych z atrybutami i ilością rezerwacji. Rezerwacja obejmuje tylko koszty obliczeniowe pojedynczego serwera usługi Azure Database for MySQL. Opłata jest naliczana za magazyn i sieć według normalnych stawek.

## <a name="how-reservation-discount-is-applied"></a>Jak jest naliczany rabat za rezerwację

Rabat za rezerwację jest dostępny na zasadzie ***wykorzystaj lub strać***. Zatem jeśli w ciągu jakiejś godziny nie będziesz mieć pasujących zasobów, utracisz ilość rezerwacji dla tej godziny. Niewykorzystanych godzin zarezerwowanych nie można przenieść na później.</br>

Po wyłączeniu zasobu rabat za rezerwację automatycznie stosuje się do innego pasującego zasobu w określonym zakresie. Jeśli w określonym zakresie nie uda się znaleźć pasujących zasobów, zarezerwowane godziny zostaną utracone.

## <a name="discount-applied-to-azure-database-for-mysql"></a>Rabat zastosowany do usługi Azure Database for MySQL

Rabat za pojemność zarezerwowaną usługi Azure Database for MySQL jest stosowany do działających serwerów MySQL z rozliczeniem godzinowym. Kupowana rezerwacja jest dopasowywana do użycia zasobów obliczeniowych emitowanych przez działające pojedyncze serwery usługi Azure Database for MySQL. W przypadku serwerów MySQL, które nie działają przez pełną godzinę, rezerwacja jest automatycznie stosowana do innych serwerów usługi Azure Database for MySQL pasujących do atrybutów rezerwacji. Rabat może być stosowany do serwerów usługi Azure Database for MySQL, które działają równolegle. Jeśli nie masz serwera MySQL, który działa przez pełną godzinę i pasuje do atrybutów rezerwacji, nie wykorzystasz w pełni korzyści z rabatu za rezerwację w tej godzinie.

W poniższych przykładach pokazano, w jaki sposób rabat za pojemność zarezerwowaną usługi Azure Database for MySQL jest stosowany w zależności od liczby zakupionych rdzeni oraz czasu ich działania.

* **Przykład 1**: Kupujesz pojemność zarezerwowaną usługi Azure Database for MySQL dla 8 rdzeni wirtualnych. W przypadku korzystania z serwera usługi Azure Database for MySQL z 16 rdzeniami wirtualnymi zgodnego z resztą atrybutów rezerwacji opłata jest naliczana według stawek płatności zgodnie z rzeczywistym użyciem za osiem rdzeni wirtualnych użycia mocy obliczeniowej serwera MySQL, a rabat za rezerwację otrzymujesz na 1 godzinę użycia mocy obliczeniowej serwera MySQL z 8 rdzeniami wirtualnymi.</br>

W pozostałych przykładach przyjęto założenie, że zakupiona pojemność zarezerwowana usługi Azure Database for MySQL dotyczy usługi Azure Database for MySQL z 16 rdzeniami wirtualnymi, a pozostałe atrybuty rezerwacji są zgodne z uruchomionymi serwerami MySQL.

* **Przykład 2**: Uruchamiasz 2 pojedyncze serwery usługi Azure Database for MySQL, każdy z 8 rdzeniami wirtualnymi, na 1 godzinę. Rabat za rezerwację z 16 rdzeniami wirtualnymi jest stosowany do użycia mocy obliczeniowej dla obu serwerów usługi Azure Database for MySQL z 8 rdzeniami wirtualnymi.

* **Przykład 3**: Uruchamiasz jeden serwer usługi Azure Database for MySQL z 16 rdzeniami wirtualnymi od godziny 13:00 do 13:30. Uruchamiasz kolejny serwer usługi Azure Database for MySQL z 16 rdzeniami wirtualnymi od godziny 13:30 do 14:00. Obie te bazy danych są objęte rabatem na rezerwację.

* **Przykład 4**: Uruchamiasz jeden serwer usługi Azure Database for MySQL z 16 rdzeniami wirtualnymi od godziny 13:00 do 13:45. Uruchamiasz kolejny serwer usługi Azure Database for MySQL z 16 rdzeniami wirtualnymi od godziny 13:30 do 14:00. Za 15-minutowy okres jednoczesnego działania obu baz danych jest naliczana opłata według stawek płatności zgodnie z rzeczywistym użyciem. Na użycie zasobów obliczeniowych przez resztę czasu jest stosowany rabat na rezerwację.

Aby poznać zastosowanie swoich rezerwacji platformy Azure w raportach rozliczeń użycia i przejrzeć je, zobacz [Omówienie użycia rezerwacji platformy Azure](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami
Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).
