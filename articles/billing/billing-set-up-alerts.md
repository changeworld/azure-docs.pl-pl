---
title: Ustawianie alertów dotyczących rozliczeń lub środków dla subskrypcji platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak można ustawić alerty na rachunku dotyczącym platformy Azure, dzięki czemu można uniknąć zaskoczenia po otrzymaniu rozliczenia.
keywords: środki alertu, alert rozliczeń
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: adpick
ms.openlocfilehash: 094bfe041ae04e52b6d998ccfd1d964abf192d6a
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35943477"
---
# <a name="set-up-billing-or-credit-alerts-for-your-microsoft-azure-subscriptions"></a>Ustawianie alertów dotyczących rozliczeń lub środków dla subskrypcji Microsoft Azure
Jeśli jesteś administratorem konta dla subskrypcji platformy Azure, można użyć usługa alertu dotyczącego rozliczeń platformy Azure, aby tworzyć niestandardowe alerty dotyczące rozliczeń, które ułatwiają monitorowanie i zarządzanie nią działania rozliczeniowe dla kont systemu Azure.

Ta usługa jest dostępna w wersji zapoznawczej, więc należy ją najpierw włączyć na stronie funkcji w wersji zapoznawczej.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="set-the-alert-threshold-and-email-recipients"></a>Ustaw wartość progową i adres e-mail odbiorcy alertów
1. Odwiedź stronę [strona funkcje w wersji zapoznawczej](https://account.windowsazure.com/PreviewFeatures) i włączyć **usługa alertu dotyczącego rozliczeń**.

1. Po otrzymaniu potwierdzenia poczty e-mail, który usługa rozliczeń jest włączona dla Twojej subskrypcji, odwiedź stronę [strony subskrypcje](https://account.windowsazure.com/Subscriptions) w portalu konta. Kliknij subskrypcję, którą chcesz monitorować, a następnie kliknij przycisk **alerty**.

    ![Zrzut ekranu przedstawiający widok subskrypcji Centrum konta platformy Azure z wyróżnioną alertów][Image1]

2. Następnie kliknij przycisk **Dodaj Alert** utworzyć Twoje pierwsze z nich. Można skonfigurować w sumie pięć alerty dotyczące rozliczeń dla subskrypcji, za pomocą różnych wartości progowej i maksymalnie dwóch adresatów wiadomości e-mail o każdym alercie.

    ![Zrzut ekranu przedstawiający widok alertów, w którym można dodać alert][Image2]

3. Po dodaniu alertu, nadaj jej unikatową nazwę, wybierz próg wydatków i wybrać adresy e-mail, gdy alerty są wysyłane. Podczas ustawiania wartości progowej, możesz wybrać dowolną **rozliczeń całkowita** lub **środków pieniężnych** z **alertów dla** listy. Dla danych całkowitych rozliczeń alert jest wysyłana, gdy wydatków subskrypcji przekracza wartość progową. Aby uzyskać środki pieniężne alertu jest wysyłana, gdy środków pieniężnych spadnie poniżej limitu. Stosowane są zazwyczaj środki pieniężne do subskrypcji bezpłatnej wersji próbnej i programu Visual Studio.

    ![Zrzut ekranu widoku alertów dodatkowe, którym można skonfigurować adresatów][Image3]

Platforma Azure obsługuje dowolny adres e-mail, ale nie weryfikuje, czy działa adres e-mail, dlatego dokładnie literówki.

## <a name="check-on-your-alerts"></a>Sprawdź alerty
Po skonfigurowaniu alerty Centrum kont wyświetli je i pokazuje, jak wiele, więcej możesz skonfigurować. Dla każdego alertu zobaczysz daty i godziny, który została wysłana, czy jest to alert dotyczący rozliczeń całkowitych lub środków pieniężnych oraz limit, można skonfigurować. Format daty i godziny jest koordynowanie uniwersalnego czasu 24-godzinny (UTC) i daty to rrrr mm-dd format. Kliknij znak plus dla alertu na liście, aby go edytować lub Kosza, aby go usunąć.

## <a name="delete-alerts-or-email-addresses-from-the-azure-billing-alert-service"></a>Usuń alertów lub adresy e-mail z usługa alertu dotyczącego rozliczeń platformy Azure
Jeśli trzeba będzie usunąć wszelkie informacje z usługi, zaktualizować adres e-mail w pliku lub całkowicie usunąć alert.

   ![Zrzut ekranu przedstawiający widok usunięcia alertu, gdzie można usunąć dane osobowe][Image4]

## <a name="billing-alerts-for-enterprise-agreement-ea-customers"></a>Alerty dotyczące rozliczeń dla klientów z umową Enterprise Agreement (EA)
Subskrypcji EA nie są obsługiwane przez tę usługę, zamiast tego klienci z umowami EA mogą otrzymywać alerty dotyczące każdego działu, w obszarze rejestracji, ustawiając limity przydziału wydatków. Zobacz [przydziały wydatków działu](https://ea.azure.com/helpdocs/departmentSpendingQuotas) w portalu EA, aby rozpocząć pracę.

## <a name="learn-more-about-azure-cost-management"></a>Dowiedz się więcej o usłudze Azure cost management firmy
- Szacowanie kosztów za pomocą [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/), [całkowity koszt posiadania Kalkulator](https://aka.ms/azure-tco-calculator), a po dodaniu usługi.
- [Przeglądanie użycia i kosztów regularnie w witrynie Azure portal](billing-getting-started.md#costs).
- Włącz [usługi Azure Advisor koszt zalecenia](../advisor/advisor-cost-recommendations.md).

Aby dowiedzieć się więcej, zobacz [Azure cost management wskazówki](billing-getting-started.md).

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png 
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png 
[Image4]: ./media/azure-billing-set-up-alerts/AlertsDeleteScreen1.PNG
