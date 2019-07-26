---
title: Omówienie interfejsu API użycia platformy Azure | Microsoft Docs
description: Dowiedz się, jak interfejsy API użycia platformy Azure zapewniają programistyczny dostęp do danych dotyczących kosztów i użycia zasobów platformy Azure.
services: billing
documentationcenter: ''
author: bandersmsft
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
ms.author: banders
ms.openlocfilehash: 2a2922daef5e75af41252119f9a467fae976bcda
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443272"
---
# <a name="azure-consumption-api-overview"></a>Omówienie interfejsu API użycia platformy Azure 

Interfejsy API użycia platformy Azure zapewniają programowy dostęp do danych dotyczących kosztów i użycia zasobów platformy Azure. Te interfejsy API obecnie obsługują tylko rejestracje przedsiębiorstwa i subskrypcje bezpośrednie sieci Web (z kilkoma wyjątkami). Interfejsy API są stale aktualizowane do obsługi innych typów subskrypcji platformy Azure.

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

## <a name="usage-details-api"></a>Interfejs API szczegółów użycia

Użyj interfejsu API szczegóły użycia, aby uzyskać informacje o opłatach i użyciu dla wszystkich zasobów platformy Azure. Informacje są w postaci rekordów zawierających szczegóły użycia, które są aktualnie emitowane raz na licznik na każdy zasób dziennie. Informacje mogą służyć do dodawania kosztów dla wszystkich zasobów lub badania kosztów/użycia dla określonych zasobów. 

Ten interfejs API obejmuje:

-   **Dane dotyczące zużycia na poziomie mierników** — Zobacz dane, w tym koszt użycia, licznik emitujący opłatę oraz zasoby platformy Azure, do których odnosi się opłata. Wszystkie rekordy szczegóły użycia są mapowane na zasobnik dzienny.
-   **Access Control oparte na rolach na platformie Azure** — Konfigurowanie zasad dostępu [Azure Portal](https://portal.azure.com), [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) lub [poleceń cmdlet Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) , aby określić, którzy użytkownicy lub aplikacje mogą uzyskać dostęp do danych użycia subskrypcji. Obiekty wywołujące musi używać standardowych tokenów usługi Azure Active Directory do uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik rozliczenia, Czytelnik, właściciel albo współautora do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure. 
-   **Filtrowanie** — przycinanie wyników interfejsu API do mniejszego zestawu rekordów szczegółów użycia przy użyciu następujących filtrów:
    - Rozpoczęcie/użycie/początek użycia
    - Grupa zasobów
    - Nazwa zasobu
-   **Agregowanie danych** — użycie protokołu OData do zastosowania wyrażeń do agregowania szczegółów użycia według tagów lub właściwości filtru
-   **Użycie dla różnych typów ofert** — informacje szczegółowe dotyczące użycia są obecnie dostępne dla klientów korzystających z usług korporacyjnych i sieci Web.

Aby uzyskać więcej informacji, zobacz specyfikację techniczną [interfejsu API szczegółów użycia](https://docs.microsoft.com/rest/api/consumption/usagedetails).

## <a name="marketplace-charges-api"></a>Interfejs API opłat w witrynie Marketplace

Skorzystaj z interfejsu API opłat w witrynie Marketplace, aby uzyskać dane dotyczące obciążeń i użycia wszystkich zasobów portalu Marketplace (oferty innych firm na platformie Azure). Te dane mogą służyć do dodawania kosztów do wszystkich zasobów witryny Marketplace lub badania kosztów/użycia dla określonych zasobów. 

Ten interfejs API obejmuje:

-   **Dane dotyczące zużycia na poziomie licznika** — Zobacz dane, w tym koszt użycia witryny Marketplace, licznik emitujący opłatę oraz zasoby, do których odnosi się opłata. Wszystkie rekordy szczegóły użycia są mapowane na zasobnik dzienny.
-   **Access Control oparte na rolach na platformie Azure** — Konfigurowanie zasad dostępu [Azure Portal](https://portal.azure.com), [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) lub [poleceń cmdlet Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) , aby określić, którzy użytkownicy lub aplikacje mogą uzyskać dostęp do danych użycia subskrypcji. Obiekty wywołujące musi używać standardowych tokenów usługi Azure Active Directory do uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik rozliczenia, Czytelnik, właściciel albo współautora do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure. 
-   **Filtrowanie** — przycinanie wyników interfejsu API do mniejszego zestawu rekordów portalu Marketplace przy użyciu następujących filtrów:
    - Początek/koniec użycia
    - Grupa zasobów
    - Nazwa zasobu
-   **Użycie dla różnych typów ofert** — informacje o portalu Marketplace są obecnie dostępne dla klientów korzystających z usług korporacyjnych i sieci Web.

Aby uzyskać więcej informacji, zobacz specyfikację techniczną dla [interfejsu API opłat w witrynie Marketplace](https://docs.microsoft.com/rest/api/consumption/marketplaces).

## <a name="balances-api"></a>Interfejs API równoważenia

Klienci korporacyjni mogą korzystać z interfejsu API usługi równoważenia obciążenia, aby uzyskać miesięczne podsumowanie informacji na temat bilansów, nowych zakupów, opłat za usługę Azure Marketplace, korekt i opłat za użycie nadwyżkowe. Te informacje można uzyskać w bieżącym okresie rozliczeniowym lub w dowolnym okresie w przeszłości. Przedsiębiorstwa mogą używać tych danych do przeprowadzenia porównania z ręcznymi obliczanymi opłatami zbiorczymi. Ten interfejs API nie dostarcza informacji dotyczących zasobów i zagregowanego widoku kosztów. 

Ten interfejs API obejmuje:

-   **Access Control oparte na rolach na platformie Azure** — Konfigurowanie zasad dostępu [Azure Portal](https://portal.azure.com), [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) lub [poleceń cmdlet Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) , aby określić, którzy użytkownicy lub aplikacje mogą uzyskać dostęp do danych użycia subskrypcji. Obiekty wywołujące musi używać standardowych tokenów usługi Azure Active Directory do uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik rozliczenia, Czytelnik, właściciel albo współautora do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure. 
-   **Tylko Klienci korporacyjni** Ten interfejs API jest dostępny tylko dla klientów z umowami EA. 
    - Klienci muszą mieć uprawnienia administratora przedsiębiorstwa do wywoływania tego interfejsu API 

Aby uzyskać więcej informacji, zobacz specyfikację techniczną dla [interfejsu API równoważenia](https://docs.microsoft.com/rest/api/consumption/balances).

## <a name="budgets-api"></a>Interfejs API budżetów

Klienci korporacyjni mogą używać tego interfejsu API do tworzenia budżetów kosztów i użycia dla zasobów, grup zasobów lub mierników rozliczeń. Po ustaleniu tych informacji można skonfigurować alerty do powiadamiania o przekroczeniu progów budżetu zdefiniowanych przez użytkownika. 

Ten interfejs API obejmuje:

-   **Access Control oparte na rolach na platformie Azure** — Konfigurowanie zasad dostępu [Azure Portal](https://portal.azure.com), [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) lub [poleceń cmdlet Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) , aby określić, którzy użytkownicy lub aplikacje mogą uzyskać dostęp do danych użycia subskrypcji. Obiekty wywołujące musi używać standardowych tokenów usługi Azure Active Directory do uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik rozliczenia, Czytelnik, właściciel albo współautora do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure. 
-   **Tylko Klienci korporacyjni** — ten interfejs API jest dostępny tylko dla klientów z umowami EA.
-   **Konfigurowalne powiadomienia** — Określ użytkowników, którzy mają otrzymywać powiadomienia o wyzwoleniu budżetu.
-   **Budżety na podstawie użycia lub kosztów** — Tworzenie budżetu na podstawie zużycia lub kosztów potrzebnych przez dany scenariusz.
-   **Filtrowanie** — filtrowanie budżetu do mniejszego podzestawu zasobów przy użyciu następujących konfigurowalnych filtrów
    - Grupa zasobów
    - Nazwa zasobu
    - Zmierz
-   **Konfigurowalne okresy budżetowe** — Określ, jak często ma być resetowany budżet oraz jak długo obowiązuje budżet.

Aby uzyskać więcej informacji, zobacz specyfikację techniczną dla [interfejsu API budżetów](https://docs.microsoft.com/rest/api/consumption/budgets).

## <a name="reservation-recommendations-api"></a>Interfejs API zaleceń dotyczących rezerwacji

Użyj tego interfejsu API, aby uzyskać zalecenia dotyczące zakupu wystąpień zarezerwowanych maszyn wirtualnych. Zalecenia pozwalają klientom analizować oczekiwane oszczędności kosztów i kwoty zakupu. 

Ten interfejs API obejmuje:

-   **Access Control oparte na rolach na platformie Azure** — Konfigurowanie zasad dostępu [Azure Portal](https://portal.azure.com), [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) lub [poleceń cmdlet Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) , aby określić, którzy użytkownicy lub aplikacje mogą uzyskać dostęp do danych użycia subskrypcji. Obiekty wywołujące musi używać standardowych tokenów usługi Azure Active Directory do uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik rozliczenia, Czytelnik, właściciel albo współautora do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure. 
-   **Filtrowanie** — Dostosuj wyniki rekomendacji przy użyciu następujących filtrów:
    - Scope
    - Okres lookback
-   **Informacje o rezerwacji dla różnych typów ofert** — informacje dotyczące rezerwacji są obecnie dostępne dla klientów korzystających z usług korporacyjnych i sieci Web.

Aby uzyskać więcej informacji, zobacz specyfikację techniczną dla [interfejsu API zaleceń dotyczących rezerwacji](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations).

## <a name="reservation-details-api"></a>Interfejs API szczegółów rezerwacji

Użyj interfejsu API szczegóły rezerwacji, aby wyświetlić informacje dotyczące wcześniej zakupionych rezerwacji maszyn wirtualnych, takich jak zużycie zarezerwowane lub rzeczywiste użycie. Dane można zobaczyć na poziomie każdej maszyny wirtualnej. 

Ten interfejs API obejmuje:

-   **Access Control oparte na rolach na platformie Azure** — Konfigurowanie zasad dostępu [Azure Portal](https://portal.azure.com), [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) lub [poleceń cmdlet Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) , aby określić, którzy użytkownicy lub aplikacje mogą uzyskać dostęp do danych użycia subskrypcji. Obiekty wywołujące musi używać standardowych tokenów usługi Azure Active Directory do uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik rozliczenia, Czytelnik, właściciel albo współautora do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure. 
-   **Filtrowanie** — przycinanie wyników interfejsu API do mniejszego zestawu rezerwacji przy użyciu następującego filtra:
    - Zakres dat
-   **Informacje o rezerwacji dla różnych typów ofert** — informacje dotyczące rezerwacji są obecnie dostępne dla klientów korzystających z usług korporacyjnych i sieci Web.

Aby uzyskać więcej informacji, zobacz specyfikację techniczną dla [interfejsu API szczegóły rezerwacji](https://docs.microsoft.com/rest/api/consumption/reservationsdetails).

## <a name="reservation-summaries-api"></a>Interfejs API podsumowań rezerwacji

Użyj tego interfejsu API, aby zobaczyć zagregowane informacje na temat wcześniej zakupionych rezerwacji maszyn wirtualnych, takich jak zużycie zarezerwowane w porównaniu z rzeczywistym użyciem w agregacji. 

Ten interfejs API obejmuje:

-   **Access Control oparte na rolach na platformie Azure** — Konfigurowanie zasad dostępu [Azure Portal](https://portal.azure.com), [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) lub [poleceń cmdlet Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) , aby określić, którzy użytkownicy lub aplikacje mogą uzyskać dostęp do danych użycia subskrypcji. Obiekty wywołujące musi używać standardowych tokenów usługi Azure Active Directory do uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik rozliczenia, Czytelnik, właściciel albo współautora do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure. 
-   **Filtrowanie** — Dostosuj wyniki przy użyciu dziennego ziarna z następującym filtrem:
    - Data wykorzystania
-   **Informacje o rezerwacji dla różnych typów ofert** — informacje dotyczące rezerwacji są obecnie dostępne dla klientów korzystających z usług korporacyjnych i sieci Web.
-   **Agregacje dzienne lub miesięczne** — osoby wywołujące mogą określić, czy chcą, aby dane podsumowania rezerwacji były wykonywane w ciągu dziennych czy miesięcznych.

Aby uzyskać więcej informacji, zobacz specyfikację techniczną dla [interfejsu API podsumowań rezerwacji](https://docs.microsoft.com/rest/api/consumption/reservationssummaries).

## <a name="price-sheet-api"></a>Interfejs API arkusza cen
Klient korporacyjny może użyć tego interfejsu API do pobrania cen niestandardowych dla wszystkich liczników. Przedsiębiorstwa mogą korzystać z tej usługi w połączeniu z szczegółami użycia i informacjami o użyciu portalu Marketplace w celu przeprowadzania obliczeń kosztów za pomocą danych użycia i portalu Marketplace. 

Ten interfejs API obejmuje:

-   **Access Control oparte na rolach na platformie Azure** — Konfigurowanie zasad dostępu [Azure Portal](https://portal.azure.com), [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) lub [poleceń cmdlet Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) , aby określić, którzy użytkownicy lub aplikacje mogą uzyskać dostęp do danych użycia subskrypcji. Obiekty wywołujące musi używać standardowych tokenów usługi Azure Active Directory do uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik rozliczenia, Czytelnik, właściciel albo współautora do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure. 
-   **Tylko Klienci korporacyjni** — ten interfejs API jest dostępny tylko dla klientów z umowami EA. Klienci sieci Web bezpośrednio powinni uzyskać cennik przy użyciu interfejsu API RateCard. 

Aby uzyskać więcej informacji, zobacz specyfikację techniczną dla [interfejsu API arkusza cen](https://docs.microsoft.com/rest/api/consumption/pricesheet).

## <a name="scenarios"></a>Scenariusze

Poniżej przedstawiono niektóre scenariusze, które można wykonać za pośrednictwem interfejsów API użycia:

-   **Uzgadnianie faktur** — czy opłata została naliczona przez firmę Microsoft?  Co to jest mój rachunek i czy mogę go obliczyć samodzielnie?
-   **Opłaty krzyżowe** — teraz wiemy, jak dużo obciążam, kto w organizacji musi płacić?
-   **Optymalizacja kosztów** — wiemy, ile z nich zostało naliczone... Jak mogę uzyskać więcej wydatków na platformę Azure?
-   **Śledzenie kosztów** — chcę zobaczyć, jak dużo wydatków i korzystamy z platformy Azure w miarę upływu czasu. Jakie są trendy? Jak można to zrobić lepiej?
-   **Poświęcane na platformę Azure w ciągu miesiąca** — jaka jest stawka mojego bieżącego miesiąca? Czy muszę wprowadzić wszelkie zmiany wydatków i/lub użycia platformy Azure? Kiedy w ciągu miesiąca zużywam platformę Azure?
-   **Konfigurowanie alertów** — chcę skonfigurować użycie oparte na zasobach lub alerty pieniężne oparte na budżecie.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat korzystania z interfejsy API rozliczeń platformy Azure w celu programowego uzyskiwania wglądu w dane dotyczące użycia platformy Azure, zobacz [Omówienie interfejsu API rozliczeń platformy Azure](billing-usage-rate-card-overview.md).



