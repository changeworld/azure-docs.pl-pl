---
title: Ustawianie alertów rozliczeń lub faktury dla subskrypcji platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak można skonfigurować alerty na rachunku Azure, aby uniknąć niespodzianki rozliczeń.
keywords: środki alert, alert rozliczeń
services: ''
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.assetid: 9b7b3eeb-cd9d-4690-86a3-51b1e2a8974f
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/9/2017
ms.author: adpick
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9f20de2c8a8870568ac624846f6a8a4f8d72b5c0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="set-up-billing-or-credit-alerts-for-your-microsoft-azure-subscriptions"></a>Ustawianie alertów rozliczeń lub faktury dla subskrypcji platformy Microsoft Azure
Jeśli jesteś administratorem konta subskrypcji platformy Azure, aby utworzyć dostosowany, można użyć Usługa alertów rozliczenia Azure alerty dotyczące rozliczeń, które ułatwiają monitorowanie działań i zarządzania nimi rozliczeń dla konta platformy Azure.

Ta usługa jest w wersji zapoznawczej, więc należy ją najpierw włączyć na stronie funkcji w wersji zapoznawczej.

## <a name="set-the-alert-threshold-and-email-recipients"></a>Ustawiony próg i adres e-mail odbiorcy alertów
1. Odwiedź stronę [strona funkcje w wersji zapoznawczej](https://account.windowsazure.com/PreviewFeatures) i Włącz **rozliczeń Usługa alertów**.

1. Po otrzymaniu potwierdzenie adresu e-mail, który usługa rozliczeń jest włączona dla Twojej subskrypcji, odwiedź stronę [stronie subskrypcji](https://account.windowsazure.com/Subscriptions) w portalu konta. Kliknij subskrypcję, którą chcesz monitorować, a następnie kliknij przycisk **alerty**.

    ![Zrzut ekranu przedstawiający widok subskrypcji Centrum konta platformy Azure, z wyróżnionym alerty][Image1]

2. Następnie kliknij przycisk **dodać alertu** utworzyć pierwsza. Można skonfigurować łącznie pięć alerty dotyczące rozliczeń dla subskrypcji, z różnych próg i maksymalnie dwóch adresatów wiadomości e-mail o każdym alercie.

    ![Zrzut ekranu przedstawiający widok alertów, w którym można dodać alertu][Image2]

3. Po dodaniu alert nadaj unikatową nazwę, wybierz próg wydatków i wybrać adresy e-mail, gdy alerty są wysyłane. Podczas ustawiania wartości progowej, można wybrać jedną **rozliczeń całkowita** lub **środki pieniężne** z **alertów dla** listy. Łącznie rozliczeń alert jest wysyłany, gdy subskrypcja wydatków przekracza wartość progową. Uzyskać środki pieniężne alert jest wysyłany, gdy kwota środków pieniężnych spadnie poniżej limitu. Kwota środków pieniężnych zazwyczaj mają zastosowanie do subskrypcji bezpłatnej wersji próbnej i Visual Studio.

    ![Zrzut ekranu przedstawiający widok dodawania alertów, którym można skonfigurować adresatów][Image3]

Azure obsługuje dowolnego adresu e-mail, ale nie Sprawdź, czy działa adres e-mail, więc dokładnie literówki.

## <a name="check-on-your-alerts"></a>Sprawdź alerty
Po skonfigurowaniu alerty Centrum konta je i pokazuje, ile więcej można skonfigurować. Dla każdego alertu Zobacz Data i godzina wysłania, czy jest alert dotyczący rozliczeń całkowita lub środki pieniężne i skonfigurowanego limitu. Format daty i godziny jest 24-godzinnym koordynować czasu uniwersalnego (UTC), a data jest w formacie rrrr mm-dd. Kliknij znak plus alertu na liście, aby go edytować, lub kliknij przycisk Kosz go usunąć.

## <a name="billing-alerts-for-enterprise-agreement-ea-customers"></a>Karta alerty dla klientów Enterprise Agreement (EA)
Subskrypcje EA nie są obsługiwane przez tę usługę, zamiast tego EA klienci mogą uzyskać alerty dla każdego działu w ramach rejestracji przez ustawienie wydatków przydziałów. Zobacz [przydziały wydatków działu](https://ea.azure.com/helpdocs/departmentSpendingQuotas) w portalu EA, aby rozpocząć pracę.

## <a name="learn-more-about-azure-cost-management"></a>Dowiedz się więcej na temat zarządzania Azure koszt
- Oszacowanie kosztów przy użyciu [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/), [całkowity koszt posiadania kalkulatora](https://aka.ms/azure-tco-calculator), a po dodaniu usługi.
- [Przejrzyj użycia i kosztów regularnie w portalu Azure](billing-getting-started.md#costs).
- Włącz [Azure Advisor koszt zalecenia](../advisor/advisor-cost-recommendations.md).

Aby dowiedzieć się więcej, zobacz [Azure koszt wskazówki dotyczące zarządzania](billing-getting-started.md).

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png 
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png 
