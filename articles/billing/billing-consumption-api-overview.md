---
title: Omówienie użycia platformy Azure, interfejsu API | Dokumentacja firmy Microsoft
description: Dowiedz się, jak interfejsów API użycia platformy Azure zapewniają Ci programistyczny dostęp do kosztów i danych użycia dla zasobów platformy Azure.
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: 68825e85-de64-466d-b11a-8baffde836b5
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 6/07/2018
ms.author: erikre
ms.openlocfilehash: 16e0bdfa0fc70d5239cb4127e61891a013bf54a3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60615897"
---
# <a name="azure-consumption-api-overview"></a>Użycie platformy Azure, interfejsu API — Przegląd 

Interfejsy API użycia platformy Azure zapewniają programowy dostęp do danych dotyczących kosztów i użycia zasobów platformy Azure. Te interfejsy API obecnie obsługują tylko rejestracje Enterprise i subskrypcje Web Direct (z pewnymi wyjątkami). Interfejsy API są ciągle aktualizowane, aby obsłużyć inne typy subskrypcji platformy Azure.

Interfejsy API użycia platformy Azure zapewniają dostęp do następujących elementów:
- Bezpośredni klienci internetowi i klienci korporacyjni 
    - Szczegóły użycia 
    - Opłaty za korzystanie z witryny Marketplace 
    - Rekomendacje dotyczące rezerwacji 
    - Szczegóły rezerwacji 
    - Podsumowania rezerwacji 
- Tylko klienci korporacyjni 
    - Arkusz cen 
    - Budżety 
    - Salda 

## <a name="usage-details-api"></a>Szczegóły użycia interfejsu API

Pobieranie opłat i dane użycia dla wszystkich platformy Azure za pomocą interfejsu API szczegółów użycia 1 zasoby innych firm. Informacje są w formie rekordów szczegółów użycia, które obecnie są emitowane raz na metr poszczególnych zasobów na dzień. Informacje mogą służyć do dodawania kosztów wszystkich zasobów lub badanie kosztów / użycie na określone zasoby. 

Ten interfejs API obejmuje:

-   **Monitoruje poziom użycia danych** — Zobacz dane użycia koszt miernika emitowania opłaty, w tym i jakie zasobów platformy Azure opłaty dotyczą. Wszystkie rekordy szczegółów użycia są mapowane na codzienne przedziału.
-   **Azure Role-based Access Control** — Konfigurowanie zasad dostępu na [witryny Azure portal](https://portal.azure.com), [wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) lub [poleceń cmdlet programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) Aby określić, które Użytkownicy lub aplikacje mogą uzyskać dostęp do danych użycia dla subskrypcji. Obiekty wywołujące musi używać standardowych tokenów usługi Azure Active Directory do uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik rozliczenia, Czytelnik, właściciel albo współautora do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure. 
-   **Filtrowanie** — Trim Twojego interfejsu API zestawu wyników w dół do mniejszy zestaw rekordów szczegółów użycia za pomocą następujących filtrów:
    - Użycie zakończenia / start użycia
    - Grupa zasobów
    - Nazwa zasobu
-   **Agregacja danych** — Użyj OData dotyczą wyrażeń szczegóły użycia agregacji według tagów lub właściwości filtru
-   **Użycia dla typów inną ofertę** -użycia szczegółowych informacji jest obecnie dostępna dla klientów korzystających z przedsiębiorstwa i bezpośredni klienci sieci Web.

Aby uzyskać więcej informacji, zobacz temat specyfikacji technicznej [interfejs API użycia szczegóły](https://docs.microsoft.com/rest/api/consumption/usagedetails).

## <a name="marketplace-charges-api"></a>Interfejs API opłatach w portalu Marketplace

Pobieranie opłat i dane użycia dla wszystkich zasobów w witrynie Marketplace za pomocą interfejsu API opłaty portalu Marketplace (Azure 3rd ofert innych firm). Te dane, może służyć do dodawania kosztów wszystkich zasobów w witrynie Marketplace lub badanie kosztów / użycie na określone zasoby. 

Ten interfejs API obejmuje:

-   **Monitoruje poziom użycia danych** — Zobacz dane użycia witryny marketplace koszt miernika emitowania opłaty, w tym i w jakim zasobem opłata dotyczy. Wszystkie rekordy szczegółów użycia są mapowane na codzienne przedziału.
-   **Azure Role-based Access Control** — Konfigurowanie zasad dostępu na [witryny Azure portal](https://portal.azure.com), [wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) lub [poleceń cmdlet programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) Aby określić, które Użytkownicy lub aplikacje mogą uzyskać dostęp do danych użycia dla subskrypcji. Obiekty wywołujące musi używać standardowych tokenów usługi Azure Active Directory do uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik rozliczenia, Czytelnik, właściciel albo współautora do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure. 
-   **Filtrowanie** — Trim Twojego interfejsu API zestawu wyników w dół do mniejszy zestaw rekordów marketplace przy użyciu następujących filtrów:
    - Użycie rozpoczęcia / zakończenia użycia
    - Grupa zasobów
    - Nazwa zasobu
-   **Użycia dla typów inną ofertę** — informacje portalu Marketplace jest obecnie dostępna dla klientów korzystających z przedsiębiorstwa i bezpośredni klienci sieci Web.

Aby uzyskać więcej informacji, zobacz temat specyfikacji technicznej [API opłaty w portalu Marketplace](https://docs.microsoft.com/rest/api/consumption/marketplaces).

## <a name="balances-api"></a>Interfejs API salda

Klienci korporacyjni można użyć interfejsu API równoważy uzyskać podsumowanie informacji na temat salda, nowe zakupy, opłaty za usługę Azure Marketplace, korekt i opłaty za użycie nadwyżkowe co miesiąc. Możesz uzyskać te informacje w bieżącym okresie rozliczeniowym lub dowolnego okresu w przeszłości. Przedsiębiorstwa mogą używać tych danych do porównania z opłatami podsumowania ręcznie obliczeniowych. Ten interfejs API nie udostępnia informacji specyficznych dla zasobów i zagregowany widok kosztów. 

Ten interfejs API obejmuje:

-   **Azure Role-based Access Control** — Konfigurowanie zasad dostępu na [witryny Azure portal](https://portal.azure.com), [wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) lub [poleceń cmdlet programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) Aby określić, które Użytkownicy lub aplikacje mogą uzyskać dostęp do danych użycia dla subskrypcji. Obiekty wywołujące musi używać standardowych tokenów usługi Azure Active Directory do uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik rozliczenia, Czytelnik, właściciel albo współautora do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure. 
-   **Tylko klienci Enterprise** ten interfejs API jest tylko dostępne umową EA. 
    - Klienci muszą mieć uprawnienia administratora przedsiębiorstwa, aby wywołać ten interfejs API 

Aby uzyskać więcej informacji, zobacz temat specyfikacji technicznej [API salda](https://docs.microsoft.com/rest/api/consumption/balances).

## <a name="budgets-api"></a>Budżetów interfejsu API

Klienci korporacyjni ten interfejs API umożliwia tworzenie budżetów kosztów lub użycia zasobów, grup zasobów lub liczników rozliczeń. Po określeniu tych informacji alertów można skonfigurować do wysyłania powiadomień o przekroczeniu progów budżetu zdefiniowanych przez użytkownika. 

Ten interfejs API obejmuje:

-   **Azure Role-based Access Control** — Konfigurowanie zasad dostępu na [witryny Azure portal](https://portal.azure.com), [wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) lub [poleceń cmdlet programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) Aby określić, które Użytkownicy lub aplikacje mogą uzyskać dostęp do danych użycia dla subskrypcji. Obiekty wywołujące musi używać standardowych tokenów usługi Azure Active Directory do uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik rozliczenia, Czytelnik, właściciel albo współautora do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure. 
-   **Tylko klienci Enterprise** — ten interfejs API jest tylko dostępne umową EA.
-   **Powiadomienia można konfigurować** -Określ użytkowników, aby otrzymywać powiadomienia, gdy budżet jest uruchomiony.
-   **Użycie lub kosztów na podstawie budżetów** — Utwórz swój budżet na podstawie użycia lub koszt odpowiednio do potrzeb danego scenariusza.
-   **Filtrowanie** — filtrowanie swój budżet utworzenie mniejszego podzestawu zasobów przy użyciu następujących filtrów można konfigurować
    - Grupa zasobów
    - Nazwa zasobu
    - Jednostka
-   **Można skonfigurować budżetu okresach** — Określ, jak często należy resetować budżetu i jak długo budżet jest nieprawidłowa dla.

Aby uzyskać więcej informacji, zobacz temat specyfikacji technicznej [API budżetów](https://docs.microsoft.com/rest/api/consumption/budgets).

## <a name="reservation-recommendations-api"></a>Interfejs API zaleceń rezerwacji

Uzyskaj zalecenia zakupu wystąpień zarezerwowanych maszyn wirtualnych za pomocą tego interfejsu API. Zalecenia są przeznaczone do pozwala klientom na analizowanie oczekiwanych oszczędności i kwot zakupu. 

Ten interfejs API obejmuje:

-   **Azure Role-based Access Control** — Konfigurowanie zasad dostępu na [witryny Azure portal](https://portal.azure.com), [wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) lub [poleceń cmdlet programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) Aby określić, które Użytkownicy lub aplikacje mogą uzyskać dostęp do danych użycia dla subskrypcji. Obiekty wywołujące musi używać standardowych tokenów usługi Azure Active Directory do uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik rozliczenia, Czytelnik, właściciel albo współautora do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure. 
-   **Filtrowanie** — Dostosowywanie wyników zalecenie przy użyciu następujących filtrów:
    - Scope
    - Okres Lookback
-   **Informacje o rezerwacji dla typów inną ofertę** — informacje o rezerwacji jest obecnie dostępna dla klientów korzystających z przedsiębiorstwa i bezpośredni klienci sieci Web.

Aby uzyskać więcej informacji, zobacz temat specyfikacji technicznej [interfejsu API zaleceń rezerwacji](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations).

## <a name="reservation-details-api"></a>Szczegóły rezerwacji interfejsu API

Użyj interfejsu API szczegóły rezerwacji, aby wyświetlić informacje dotyczące wcześniej zakupu rezerwacji maszyny Wirtualnej, takie jak a ile zostało zarezerwowane ile zużycie faktycznie jest używana. Możesz zobaczyć dane w poszczególnych maszyn wirtualnych na poziomie szczegółowości. 

Ten interfejs API obejmuje:

-   **Azure Role-based Access Control** — Konfigurowanie zasad dostępu na [witryny Azure portal](https://portal.azure.com), [wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) lub [poleceń cmdlet programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) Aby określić, które Użytkownicy lub aplikacje mogą uzyskać dostęp do danych użycia dla subskrypcji. Obiekty wywołujące musi używać standardowych tokenów usługi Azure Active Directory do uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik rozliczenia, Czytelnik, właściciel albo współautora do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure. 
-   **Filtrowanie** — Trim Twojego interfejsu API zestawu wyników w dół do mniejszy zestaw rezerwacje, za pomocą następujących filtrów:
    - Zakres dat
-   **Informacje o rezerwacji dla typów inną ofertę** — informacje o rezerwacji jest obecnie dostępna dla klientów korzystających z przedsiębiorstwa i bezpośredni klienci sieci Web.

Aby uzyskać więcej informacji, zobacz temat specyfikacji technicznej [API szczegóły rezerwacji](https://docs.microsoft.com/rest/api/consumption/reservationsdetails).

## <a name="reservation-summaries-api"></a>Podsumowanie rezerwacji interfejsu API

Użyj tego interfejsu API, aby wyświetlić informacje agregacji na wcześniej zakupu rezerwacji maszyny Wirtualnej, takich jak ile zużycie zostało zarezerwowane i jaka jest faktycznie używana w agregacji. 

Ten interfejs API obejmuje:

-   **Azure Role-based Access Control** — Konfigurowanie zasad dostępu na [witryny Azure portal](https://portal.azure.com), [wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) lub [poleceń cmdlet programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) Aby określić, które Użytkownicy lub aplikacje mogą uzyskać dostęp do danych użycia dla subskrypcji. Obiekty wywołujące musi używać standardowych tokenów usługi Azure Active Directory do uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik rozliczenia, Czytelnik, właściciel albo współautora do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure. 
-   **Filtrowanie** — Dostosowywanie wyników, korzystając z dokładnością z następujących filtrów:
    - Data wykorzystania
-   **Informacje o rezerwacji dla typów inną ofertę** — informacje o rezerwacji jest obecnie dostępna dla klientów korzystających z przedsiębiorstwa i bezpośredni klienci sieci Web.
-   **Agregacje codziennie lub co miesiąc** — obiekty wywołujące można określić, czy chcą, aby ich dane podsumowań rezerwacji ziarna codziennie lub co miesiąc.

Aby uzyskać więcej informacji, zobacz temat specyfikacji technicznej [API podsumowań z okresu rezerwacji](https://docs.microsoft.com/rest/api/consumption/reservationssummaries).

## <a name="price-sheet-api"></a>Interfejs API arkusza cen
Enterprise odbiorca może używać tego interfejsu API, można pobrać ich niestandardowe ceny dla wszystkich liczników. Przedsiębiorstwa mogą używać to w połączeniu z szczegóły i rynków przeznaczonych użycie informacji o użyciu przeprowadzić obliczenia kosztów przy użyciu danych użycia i Portal marketplace. 

Ten interfejs API obejmuje:

-   **Azure Role-based Access Control** — Konfigurowanie zasad dostępu na [witryny Azure portal](https://portal.azure.com), [wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) lub [poleceń cmdlet programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) Aby określić, które Użytkownicy lub aplikacje mogą uzyskać dostęp do danych użycia dla subskrypcji. Obiekty wywołujące musi używać standardowych tokenów usługi Azure Active Directory do uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik rozliczenia, Czytelnik, właściciel albo współautora do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure. 
-   **Tylko klienci Enterprise** — ten interfejs API jest tylko dostępne umową EA. Za pomocą interfejsu API RateCard bezpośrednich klientów sieci Web należy uzyskać cennik. 

Aby uzyskać więcej informacji, zobacz temat specyfikacji technicznej [interfejsu API arkusza cen](https://docs.microsoft.com/rest/api/consumption/pricesheet).

## <a name="scenarios"></a>Scenariusze

Poniżej przedstawiono scenariusze, które są możliwe za pośrednictwem użycia interfejsów API:

-   **Faktury uzgadniania** — czy Microsoft jest opłata w wysokości mnie odpowiednim proporcjom?  Co to jest Mój rachunek, i można obliczyć je samodzielnie?
-   **Krzyżowe opłaty** — teraz, aby sprawdzić, ile opłata została naliczona, które osoby w mojej organizacji trzeba płacić?
-   **Koszt optymalizacji** -wiem, jak duża już został opłata... jak mogę uzyskać lepiej wykorzystać pieniędzy, które spędzam na platformie Azure?
-   **Śledzenie kosztów** — Chcę zobaczyć, ile jestem wydatków i korzystanie z platformy Azure wraz z upływem czasu. Jakie są trendy? Jak mogę wykonywać czynności lepiej?
-   **Wydatki na platformę Azure w miesiącu** — ile mojego bieżącego miesiąca jest jego wydatków do daty? Należy wprowadzić poprawki w mojej wydatków i/lub użycie platformy Azure? Gdy w miesiącu mam I korzystanie z platformy Azure najczęściej?
-   **Konfigurowanie alertów** — Chcę ustawić oparte na zasobach zużycie lub pieniężnych na podstawie Alertowanie oparte na budżecie.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak programowo uzyskać wgląd w użycie platformy Azure przy użyciu interfejsów API rozliczeń platformy Azure, zobacz [omówienie interfejsu API rozliczeń platformy Azure](billing-usage-rate-card-overview.md).



