---
title: Konfigurowanie zabezpieczeń w celu uzyskania dostępu do wersji zapoznawczej Azure Time Series Insights i zarządzania nią | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania zabezpieczeń i uprawnień jako zasad dostępu do zarządzania i zasad dostępu do danych w celu zabezpieczenia Azure Time Series Insights wersji zapoznawczej.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 4455d499ab4c52a27a7d9cf878e8130ff38b1c62
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846950"
---
# <a name="grant-data-access-to-an-environment"></a>Przyznawanie dostępu do danych w środowisku

W tym artykule omówiono dwa typy zasad dostępu w wersji zapoznawczej Azure Time Series Insights.

## <a name="sign-in-to-time-series-insights"></a>Zaloguj się do Time Series Insights

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Znajdź środowisko Time Series Insights. Wprowadź `Time Series` w polu **wyszukiwania** . Wybierz pozycję **środowisko szeregów czasowych** w wynikach wyszukiwania.
1. Wybierz środowisko usługi Time Series Insights z listy.

## <a name="grant-data-access"></a>Przyznawanie dostępu do danych

Wykonaj następujące kroki, aby udzielić dostępu do danych dla podmiotu zabezpieczeń.

1. Wybierz pozycję **zasady dostępu do danych**, a następnie wybierz pozycję **+ Dodaj**.

    [![Dostęp do danych — jeden](media/data-access/data-access-one.png)](media/data-access/data-access-one.png#lightbox)

1. Wybierz **pozycję Wybierz użytkownika**. Wyszukaj nazwę użytkownika lub adres e-mail, aby zlokalizować użytkownika, który chcesz dodać. Wybierz pozycję **Wybierz** , aby potwierdzić wybór.

    [![Dostęp do danych — dwa](media/data-access/data-access-two.png)](media/data-access/data-access-two.png#lightbox)

1. Wybierz **pozycję Wybierz rolę**. Wybierz odpowiednią rolę dostępu dla użytkownika:

    * Wybierz opcję **współautor** , jeśli chcesz zezwolić użytkownikowi na zmianę danych referencyjnych i udostępnianie zapisanych zapytań i perspektyw innym użytkownikom środowiska.

    * W przeciwnym razie wybierz opcję **czytelnik** , aby zezwolić użytkownikowi na wykonywanie zapytań dotyczących danych w środowisku i zapisywanie osobistych, nieudostępnianych zapytań w środowisku.

   Wybierz **przycisk OK** , aby potwierdzić wybór roli.

    [![Dostęp do danych — trzy](media/data-access/data-access-three.png)](media/data-access/data-access-three.png#lightbox)

1. Na stronie **Wybieranie roli użytkownika** wybierz **przycisk OK** .

    [![Dostęp do danych — cztery](media/data-access/data-access-four.png)](media/data-access/data-access-four.png#lightbox)

1. Upewnij się, że na stronie **zasady dostępu do danych** są wyświetlane Użytkownicy i role dla każdego użytkownika.

    [![Dostęp do danych — pięć](media/data-access/data-access-five.png)](media/data-access/data-access-five.png#lightbox)

## <a name="provide-guest-access-from-another-aad-tenant"></a>Zapewnianie dostępu gościa z innej dzierżawy usługi AAD

`Guest`nie jest rolą zarządzania. Jest to termin używany w przypadku konta zaproszonego z jednej dzierżawy do innej. Po zaproszeniu konta gościa do katalogu dzierżawy może on mieć taką samą kontrolę dostępu, jak dowolne inne konto. Można udzielić dostępu do środowiska Time Series Insights za pomocą bloku Access Control (IAM). Można też udzielić dostępu do danych w środowisku za pomocą bloku zasady dostępu do danych. Aby uzyskać więcej informacji na temat dostępu gościa dzierżawy Azure Active Directory (Azure AD), przeczytaj temat [dodawanie Azure Active Directory użytkowników współpracy B2B w Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Wykonaj następujące kroki, aby przyznać dostęp gościa do środowiska Time Series Insights do użytkownika usługi Azure AD z innej dzierżawy.

1. Wybierz pozycję **zasady dostępu do danych**, a następnie wybierz pozycję **+ Zaproś**.

    [![Dostęp do danych — sześć](media/data-access/data-access-six.png)](media/data-access/data-access-six.png#lightbox)

1. Wprowadź adres e-mail użytkownika, który chcesz zaprosić. Ten adres e-mail musi być skojarzony z usługą Azure AD. Opcjonalnie możesz dołączyć osobistą wiadomość z zaproszeniem.

    [![Dostęp do danych — siedem](media/data-access/data-access-seven.png)](media/data-access/data-access-seven.png#lightbox)

1. Wyszukaj dymek potwierdzenia, który pojawia się na ekranie.

    [![Dostęp do danych — osiem](media/data-access/data-access-eight.png)](media/data-access/data-access-eight.png#lightbox)

1. Wybierz **pozycję Wybierz użytkownika**. Wyszukaj adres e-mail zaproszonego użytkownika-gościa, aby zlokalizować użytkownika, który chcesz dodać. Następnie **Wybierz pozycję** , aby potwierdzić wybór.

    [![Dostęp do danych — dziewięć](media/data-access/data-access-nine.png)](media/data-access/data-access-nine.png#lightbox)

1. Wybierz **pozycję Wybierz rolę**. Wybierz odpowiednią rolę dostępu dla użytkownika-gościa:

    * Wybierz opcję **współautor** , jeśli chcesz zezwolić użytkownikowi na zmianę danych referencyjnych i udostępnianie zapisanych zapytań i perspektyw innym użytkownikom środowiska.

    * W przeciwnym razie wybierz opcję **czytelnik** , aby zezwolić użytkownikowi na wykonywanie zapytań dotyczących danych w środowisku i zapisywanie osobistych, nieudostępnianych zapytań w środowisku.

   Wybierz **przycisk OK** , aby potwierdzić wybór roli.

    [![Dostęp do danych — dziesięć](media/data-access/data-access-ten.png)](media/data-access/data-access-ten.png#lightbox)

1. Na stronie **Wybieranie roli użytkownika** wybierz **przycisk OK** .

1. Upewnij się, że na stronie **zasady dostępu do danych** jest wyświetlana lista użytkowników-Gości i role poszczególnych użytkowników-Gości.

    [![Dostęp do danych — jedenaście](media/data-access/data-access-eleven.png)](media/data-access/data-access-eleven.png#lightbox)

1. Teraz użytkownik-Gość musi wykonać kroki, aby uzyskać dostęp do środowiska znajdującego się w dzierżawie platformy Azure, do którego zostały zaproszone. Najpierw akceptują przesłane przez Ciebie zaproszenia. To zaproszenie jest wysyłane pocztą e-mail na adres e-mail użyty w kroku 5. Wybierają pozycję **Rozpocznij** , aby zaakceptować.

    [![Dostęp do danych — dwanaście](media/data-access/data-access-twelve.png)](media/data-access/data-access-twelve.png#lightbox)

1. Następnie użytkownik-Gość akceptuje uprawnienia skojarzone z organizacją administratora.

    [![Dostęp do danych-trzynaście](media/data-access/data-access-thirteen.png)](media/data-access/data-access-thirteen.png#lightbox)

1. Gdy użytkownik-Gość zostanie zalogowany na adres e-mail, który został użyty do jego zaproszenia i zaakceptuje zaproszenie, przejdzie do insights.azure.com. Po tym miejscu wybierz awatar obok swojego adresu e-mail w prawym górnym rogu ekranu.

    [![Dostęp do danych-czternaście](media/data-access/data-access-fourteen.png)](media/data-access/data-access-fourteen.png#lightbox)

1. Następnie użytkownik-Gość wybiera dzierżawę platformy Azure z menu rozwijanego katalog. Ta dzierżawa jest taka, do której zostały zaproszone.

    [![Dostęp do danych — piętnaście](media/data-access/data-access-fifteen.png)](media/data-access/data-access-fifteen.png#lightbox)

Po wybraniu dzierżawy przez użytkownika Gość zobaczysz środowisko Time Series Insights, do którego podano dostęp. Mają teraz wszystkie możliwości skojarzone z rolą podaną w **kroku 5**.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, [jak dodać źródło zdarzeń Event Hubs platformy Azure](./time-series-insights-how-to-add-an-event-source-eventhub.md) do środowiska Time Series Insights.

* Wyślij [zdarzenia do źródła zdarzeń](./time-series-insights-send-events.md).

* Wyświetl [swoje środowisko w eksploratorze Time Series Insights w wersji](./time-series-insights-update-explorer.md)zapoznawczej.
