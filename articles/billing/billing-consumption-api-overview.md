---
title: Przegląd wykorzystania platformy Azure, interfejsu API | Dokumentacja firmy Microsoft
description: Dowiedz się, jak interfejsów API użycia usługi Azure zapewniają dostęp programistyczny umożliwiający kosztów i dane użycia dla zasobów platformy Azure.
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
ms.openlocfilehash: becc6d6e86fd292cfbc919f1eee36a4f5edb711c
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34851334"
---
# <a name="azure-consumption-api-overview"></a>Przegląd wykorzystania platformy Azure, interfejsu API 

Interfejsy API Azure zużycie zapewniają programowy dostęp do danych i użycia zasobów platformy Azure. Te interfejsy API obecnie obsługują tylko rejestracji Enterprise i subskrypcje bezpośrednio w sieci Web (z pewnymi wyjątkami). Interfejsy API są stale aktualizowane do obsługi innych typów subskrypcji platformy Azure.

Azure przez interfejsy API zapewniają dostęp do:
- Skierować klientów sieci Web i Enterprise 
    - Szczegóły użycia 
    - Opłat w witrynie Marketplace 
    - Zalecenia dotyczące zastrzeżenia 
    - Szczegóły zastrzeżenia 
    - Podsumowanie rezerwacji 
- Tylko przedsiębiorstwa. 
    - Arkusz cen 
    - Budżety 
    - Równoważy 

## <a name="usage-details-api"></a>Szczegóły użycia interfejsu API

Za pomocą interfejsu API szczegóły użycia można pobrać bezpłatnie i dane użycia dla wszystkich Azure 1 zasobów firmy. Informacje są w formie rekordów szczegółów użycia, które są aktualnie emitowane raz na metr poszczególnych zasobów na dzień. Informacje mogą służyć do dodawania kosztów we wszystkich zasobów lub zbadać koszty / użycie na określonych zasobów. 

Interfejs API zawiera:

-   **Poziom użycia danych pomiaru** — Zobacz dane użycia koszt licznik emitowanie opłaty, w tym i jakie zasobów platformy Azure opłata dotyczy. Wszystkie rekordy użycia są mapowane na codzienne zasobnika.
-   **Azure kontroli dostępu opartej na rolach** -skonfigurować zasady dostępu na [portalu Azure](https://portal.azure.com), [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) lub [poleceń cmdlet programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) Aby określić, które użytkowników lub aplikacji można uzyskać dostępu do danych użycia subskrypcji. Obiekty wywołujące należy użyć standardowego tokeny usługi Azure Active Directory do uwierzytelniania. Dodaj element wywołujący do albo czytnika rozliczeń, czytnika, właściciela lub współautora roli do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure. 
-   **Filtrowanie** — przycinany interfejsu API zestaw wyników, aby mniejszy zestaw rekordów szczegółów użycia za pomocą następujące filtry:
    - Końcowy użycia / start użycia
    - Grupa zasobów
    - Nazwa zasobu
-   **Agregacja danych** -OData Użyj wyrażenia dotyczą szczegóły agregacji użycia według znaczników lub właściwości filtru
-   **Użycie typów inną ofertę** — szczegółowe informacje na temat wykorzystania jest obecnie dostępny dla klientów, Enterprise i bezpośredniego sieci Web.

Aby uzyskać więcej informacji, zobacz specyfikacji technicznej [API szczegóły użycia](https://docs.microsoft.com/rest/api/consumption/usagedetails).

## <a name="marketplace-charges-api"></a>Opłat w witrynie Marketplace interfejsu API

Uzyskaj bezpłatnie i dane użycia na wszystkie zasoby w witrynie Marketplace za pomocą interfejsu API opłat witryny Marketplace (Azure 3 ofert strony). Te dane służy do dodawania kosztów przez wszystkie zasoby w witrynie Marketplace lub zbadać koszty / użycie na określonych zasobów. 

Interfejs API zawiera:

-   **Poziom użycia danych pomiaru** — Zobacz dane użycia marketplace koszt licznik emitowanie opłaty, w tym i zasobów, które dotyczą opłaty. Wszystkie rekordy użycia są mapowane na codzienne zasobnika.
-   **Azure kontroli dostępu opartej na rolach** -skonfigurować zasady dostępu na [portalu Azure](https://portal.azure.com), [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) lub [poleceń cmdlet programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) Aby określić, które użytkowników lub aplikacji można uzyskać dostępu do danych użycia subskrypcji. Obiekty wywołujące należy użyć standardowego tokeny usługi Azure Active Directory do uwierzytelniania. Dodaj element wywołujący do albo czytnika rozliczeń, czytnika, właściciela lub współautora roli do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure. 
-   **Filtrowanie** — przycinany interfejsu API zestaw wyników, aby mniejszy zestaw rekordów marketplace przy użyciu następujące filtry:
    - Użycie rozpoczęcia / zakończenia użycia
    - Grupa zasobów
    - Nazwa zasobu
-   **Użycie typów inną ofertę** — informacje Marketplace jest obecnie dostępny dla klientów, Enterprise i bezpośredniego sieci Web.

Aby uzyskać więcej informacji, zobacz specyfikacji technicznej [Marketplace opłat API](https://docs.microsoft.com/rest/api/consumption/marketplaces).

## <a name="balances-api"></a>Równoważy interfejsu API

Klienci korporacyjni można użyć interfejsu API równoważy Aby uzyskać podsumowanie informacji na temat salda, nowych zakupów, opłaty za usługę Azure Marketplace, zmiany i nadwyżkowe opłat co miesiąc. Możesz uzyskać te informacje dla bieżącego okresu rozliczeniowego lub dowolnym okresie w przeszłości. Przedsiębiorstwa mogą używać tych danych do przeprowadzenia porównania z ręcznie obliczeniowej podsumowania opłat. Ten interfejs API nie zawiera informacje dotyczące zasobów i zagregowany widok kosztów. 

Interfejs API zawiera:

-   **Azure kontroli dostępu opartej na rolach** -skonfigurować zasady dostępu na [portalu Azure](https://portal.azure.com), [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) lub [poleceń cmdlet programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) Aby określić, które użytkowników lub aplikacji można uzyskać dostępu do danych użycia subskrypcji. Obiekty wywołujące należy użyć standardowego tokeny usługi Azure Active Directory do uwierzytelniania. Dodaj element wywołujący do albo czytnika rozliczeń, czytnika, właściciela lub współautora roli do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure. 
-   **Tylko klienci Enterprise** ten interfejs API jest tylko dostępne EA klientów. 
    - Klienci muszą mieć uprawnienia administratora przedsiębiorstwa można wywołać tego interfejsu API 

Aby uzyskać więcej informacji, zobacz specyfikacji technicznej [API salda](https://docs.microsoft.com/rest/api/consumption/getbalancesbybillingaccount).

## <a name="budgets-api"></a>Budżetów interfejsu API

Klienci korporacyjni mogą umożliwia utworzenie budżetów koszt lub użycia zasobów, grupy zasobów lub metod rozliczeń tego interfejsu API. Po określeniu tych informacji alerty można skonfigurować do wysyłania powiadomień o przekroczeniu progów budżetu zdefiniowane przez użytkownika. 

Interfejs API zawiera:

-   **Azure kontroli dostępu opartej na rolach** -skonfigurować zasady dostępu na [portalu Azure](https://portal.azure.com), [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) lub [poleceń cmdlet programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) Aby określić, które użytkowników lub aplikacji można uzyskać dostępu do danych użycia subskrypcji. Obiekty wywołujące należy użyć standardowego tokeny usługi Azure Active Directory do uwierzytelniania. Dodaj element wywołujący do albo czytnika rozliczeń, czytnika, właściciela lub współautora roli do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure. 
-   **Tylko klienci Enterprise** — ten interfejs API jest tylko dostępne EA klientów.
-   **Powiadomienia można konfigurować** — Określ użytkowników, aby otrzymać powiadomienie, gdy budżetu jest uruchomiony.
-   **Obciążenie lub budżetów na podstawie koszt** — tworzenie budżet na podstawie zużycia lub koszt odpowiednio do potrzeb danego scenariusza.
-   **Filtrowanie** -filtrować budżet do mniejszego podzestawu zasobów przy użyciu następujących filtrów można konfigurować
    - Grupa zasobów
    - Nazwa zasobu
    - Jednostka
-   **Można skonfigurować budżetu okresów** — Określ, jak często budżetu należy resetować i jak długo budżetu jest nieprawidłowa dla.

Aby uzyskać więcej informacji, zobacz specyfikacji technicznej [API budżetów](https://docs.microsoft.com/rest/api/consumption/budgets).

## <a name="reservation-recommendations-api"></a>Zalecenia dotyczące zastrzeżenia interfejsu API

Ten interfejs API umożliwia uzyskać zalecenia dotyczące zakupów maszyny Wirtualnej zarezerwowana wystąpień. Zalecenia są przeznaczone do pozwala analizować oczekiwanych oszczędności i kwoty zakupów. 

Interfejs API zawiera:

-   **Azure kontroli dostępu opartej na rolach** -skonfigurować zasady dostępu na [portalu Azure](https://portal.azure.com), [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) lub [poleceń cmdlet programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) Aby określić, które użytkowników lub aplikacji można uzyskać dostępu do danych użycia subskrypcji. Obiekty wywołujące należy użyć standardowego tokeny usługi Azure Active Directory do uwierzytelniania. Dodaj element wywołujący do albo czytnika rozliczeń, czytnika, właściciela lub współautora roli do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure. 
-   **Filtrowanie** -dostosować wyniki zalecenie przy użyciu następujące filtry:
    - Zakres
    - Okres Lookback
-   **Informacje o rezerwacji dla typów inną ofertę** — informacje o rezerwacji jest obecnie dostępny dla klientów, Enterprise i bezpośredniego sieci Web.

Aby uzyskać więcej informacji, zobacz specyfikacji technicznej [API zalecenia rezerwacji](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations).

## <a name="reservation-details-api"></a>Szczegóły rezerwacji interfejsu API

Za pomocą interfejsu API szczegóły rezerwacji wcześniej wyświetlić informacje na zakupione maszyny Wirtualnej rezerwacji, takich jak ilość zużycia został zarezerwowany lub ilość faktycznie jest używany. Można wyświetlić dane w dla maszyny Wirtualnej poziom szczegółów. 

Interfejs API zawiera:

-   **Azure kontroli dostępu opartej na rolach** -skonfigurować zasady dostępu na [portalu Azure](https://portal.azure.com), [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) lub [poleceń cmdlet programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) Aby określić, które użytkowników lub aplikacji można uzyskać dostępu do danych użycia subskrypcji. Obiekty wywołujące należy użyć standardowego tokeny usługi Azure Active Directory do uwierzytelniania. Dodaj element wywołujący do albo czytnika rozliczeń, czytnika, właściciela lub współautora roli do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure. 
-   **Filtrowanie** -Trim interfejsu API zestaw wyników, aby mniejszy zestaw przy użyciu filtru następujące zastrzeżenia:
    - Zakres dat
-   **Informacje o rezerwacji dla typów inną ofertę** — informacje o rezerwacji jest obecnie dostępny dla klientów, Enterprise i bezpośredniego sieci Web.

Aby uzyskać więcej informacji, zobacz specyfikacji technicznej [API szczegóły rezerwacji](https://docs.microsoft.com/rest/api/consumption/reservationsdetails).

## <a name="reservation-summaries-api"></a>Podsumowanie rezerwacji interfejsu API

Użyj tego interfejsu API, aby wyświetlić informacje agregacji na wcześniej zakupu zastrzeżenia maszyny Wirtualnej, takie jak ilość zużycia jest zarezerwowany i jaka jest rzeczywiście używany w całkowitym. 

Interfejs API zawiera:

-   **Azure kontroli dostępu opartej na rolach** -skonfigurować zasady dostępu na [portalu Azure](https://portal.azure.com), [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) lub [poleceń cmdlet programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) Aby określić, które użytkowników lub aplikacji można uzyskać dostępu do danych użycia subskrypcji. Obiekty wywołujące należy użyć standardowego tokeny usługi Azure Active Directory do uwierzytelniania. Dodaj element wywołujący do albo czytnika rozliczeń, czytnika, właściciela lub współautora roli do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure. 
-   **Filtrowanie** -dostosować wyniki podczas korzystania z dokładnością do dnia z następujący filtr:
    - Data wykorzystania
-   **Informacje o rezerwacji dla typów inną ofertę** — informacje o rezerwacji jest obecnie dostępny dla klientów, Enterprise i bezpośredniego sieci Web.
-   **Codziennie lub co miesiąc agregacji** — wywołań można określić, czy chcą, aby ich danych podsumowania rezerwacji w ziarna codziennie lub co miesiąc.

Aby uzyskać więcej informacji, zobacz specyfikacji technicznej [API podsumowania rezerwacji](https://docs.microsoft.com/rest/api/consumption/reservationssummaries).

## <a name="price-sheet-api"></a>Interfejs API arkusza cen
Odbiorca przedsiębiorstwa może używać tego interfejsu API można pobrać ich niestandardowych ceny dla wszystkich liczników. Przedsiębiorstwa umożliwia to w połączeniu z użycia szczegóły rynków użycia informacji o i obliczeń kosztów przy użyciu danych użycia i marketplace. 

Interfejs API zawiera:

-   **Azure kontroli dostępu opartej na rolach** -skonfigurować zasady dostępu na [portalu Azure](https://portal.azure.com), [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) lub [poleceń cmdlet programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) Aby określić, które użytkowników lub aplikacji można uzyskać dostępu do danych użycia subskrypcji. Obiekty wywołujące należy użyć standardowego tokeny usługi Azure Active Directory do uwierzytelniania. Dodaj element wywołujący do albo czytnika rozliczeń, czytnika, właściciela lub współautora roli do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure. 
-   **Tylko klienci Enterprise** — ten interfejs API jest tylko dostępne EA klientów. Za pomocą interfejsu API RateCard skierować klientów sieci Web należy pobrać cenach. 

Aby uzyskać więcej informacji, zobacz specyfikacji technicznej [API arkusza cen](https://docs.microsoft.com/rest/api/consumption/pricesheet).

## <a name="scenarios"></a>Scenariusze

Oto niektóre scenariusze, które są możliwe przez użycie interfejsów API:

-   **Faktura uzgadniania** -Microsoft zostało obciążania mnie jednostkom?  Co to jest mojego rachunku i można obliczyć go samodzielnie?
-   **Krzyżowe opłat** — teraz, aby sprawdzić, ile I używam dodatkowych opłat, kto w mojej organizacji musi zwrócić?
-   **Koszt optymalizacji** -sprawdzić ile został I została obciążona... jak można uzyskać więcej poza pieniędzy, które am I wydatków na platformie Azure?
-   **Śledzenie kosztów** — Aby wyświetlić ile am I wydatków i korzysta z platformy Azure w czasie. Co to są trendów? Jak można I realizacji lepiej?
-   **Spędzają na platformie Azure w miesiącu** — jaka jest mojego bieżącego miesiąca na spędzają na datę? Należy wprowadzić zmiany w mojej wydatków i/lub użycia usługi Azure? Kiedy w miesiącu am I korzystanie z usługi Azure najbardziej?
-   **Konfigurowanie alertów** — Chcę ustawić zużycie zasobów na podstawie lub pieniężne na podstawie alerty oparte na budżetu.

## <a name="next-steps"></a>Następne kroki

- Informacje o programowane uzyskiwanie wglądu w wykorzystanie sieci platformy Azure za pomocą interfejsów API usługi Azure rozliczeń, zobacz [Przegląd rozliczeń interfejsu API Azure](billing-usage-rate-card-overview.md).



