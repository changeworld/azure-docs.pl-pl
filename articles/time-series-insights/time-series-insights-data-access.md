---
title: Skonfiguruj zabezpieczenia dostępu i zarządzanie Azure Time Series Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania zabezpieczeń i uprawnień jako dostęp do funkcji zarządzania zasadami i dostępu do danych zasad, aby zabezpieczyć usługi Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 3694ff0db63d685cb63f586062158b4f8acac5cf
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52847613"
---
# <a name="grant-data-access-to-an-environment"></a>Przyznawanie dostępu do danych w środowisku

W tym artykule omówiono dwa rodzaje zasad dostępu usługi Azure Time Series Insights (wersja zapoznawcza).

## <a name="grant-data-access"></a>Przyznawanie dostępu do danych

Wykonaj następujące kroki, aby zezwolić na dostęp do nazwy głównej użytkownika:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
1. Znajdź środowiska usługi Time Series Insights (TSI). Typ `Time Series` w **wyszukiwania** pole. Wybierz **środowiska usługi Time Series** w wynikach wyszukiwania.
1. Wybierz środowisko usługi TSI z listy.
1. Wybierz **zasady dostępu do danych**, a następnie wybierz **+ Dodaj**.

    ![Data access jednego][1]

1. Wybierz **wybierz użytkownika**. Wyszukaj adres nazwy lub adresu e-mail użytkownika zlokalizować użytkownika, którego chcesz dodać. Kliknij przycisk **wybierz** aby potwierdzić wybór.

    ![dane dostępu 2][2]

1. Wybierz **wybierz rolę**. Wybierz rolę odpowiedni dostęp dla użytkownika:

    * Wybierz **Współautor** Jeśli chcesz zezwolić użytkownikowi na zmianę danych referencyjnych i udziału zapisany, zapytań i perspektyw innym użytkownikom środowiska.

    * W przeciwnym razie wybierz **czytnika** do zezwalania na zapytania o dane użytkownika w środowisku i zapisywanie osobistych zapytań (nie udostępnianych) w środowisku.

    Wybierz **Ok** aby potwierdzić wybór roli.

    ![dane dostępu 3][3]

1. Wybierz **Ok** w **wybierz rolę użytkownika** strony.

    ![cztery w przypadku dostępu do danych][4]

1. **Zasady dostępu do danych** strona zawiera listę użytkowników i rolami dla każdego użytkownika.

    ! [data access pięć[5]

## <a name="provide-guest-access-to-a-user-from-another-azure-active-directory-tenant"></a>Dostęp gościa do użytkownika z innej dzierżawy usługi Azure Active Directory

`Guest` nie jest rolą zarządzania; to termin używany dla konta które jednej dzierżawy jest zostali zaproszeni do innej. Po konta gościa został zaproszony do katalogu dzierżawy, może mieć tej samej kontroli dostępu, zastosowano takich jak innego konta, aby udzielić dostępu do zarządzania w środowisku usługi TSI za pomocą bloku kontrola dostępu (IAM) lub do udzielania dostępu do danych w środowisko za pomocą bloku zasady dostępu do danych. Aby uzyskać więcej informacji na temat dostępu gościa dzierżawy usługi Azure Active Directory (AAD), przeczytaj [użytkowników we współpracy Dodawanie usługi Azure Active Directory B2B, w witrynie Azure portal](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Wykonaj następujące kroki, aby udzielić dostępu gościa do środowiska usługi TSI do użytkownika usługi AAD z innej dzierżawy:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Znajdź środowiska TSI. Typ **szeregów czasowych** w polu wyszukiwania. Wybierz **środowiska usługi Time Series** w wynikach wyszukiwania.
1. Wybierz środowisko usługi TSI z listy.
1. Wybierz **zasady dostępu do danych**, a następnie wybierz **+ zaprosić**.

    ![sześć w przypadku dostępu do danych][6]

1. Podaj adres e-mail użytkownika, którą chcesz zaprosić. Należy zauważyć, że powinno to być wiadomość e-mail skojarzony z usługi AAD. Możesz opcjonalnie dołączyć osobistą wiadomość z zaproszeniem.

    ![Data access siódmego][7]

1. Powinien zostać wyświetlony bąbelek potwierdzenia są wyświetlane na ekranie.

    ![Data access osiem][8]

1. Wybierz **wybierz użytkownika**. Wyszukaj adres e-mail użytkownik-Gość zaproszeni do zlokalizowania użytkownika, którego chcesz dodać. Kliknij przycisk **wybierz** aby potwierdzić wybór.

    ![Data access dziewiątego][9]

1. Wybierz **wybierz** roli. Wybierz rolę odpowiedni dostęp dla użytkownika gościa:

    1. Wybierz **Współautor** Jeśli chcesz zezwolić użytkownikowi na zmianę danych referencyjnych i udziału zapisany, zapytań i perspektyw innym użytkownikom środowiska.
    1. W przeciwnym razie wybierz **czytnika** do zezwalania na zapytania o dane użytkownika w środowisku i zapisywanie osobistych zapytań (nie udostępnianych) w środowisku.
    1. Wybierz **Ok** aby potwierdzić wybór roli.

    ![Data access dziesięciu][10]

1. Wybierz **Ok** w **wybierz rolę użytkownika** strony.

1. **Zasady dostępu do danych** strony wyświetla teraz użytkownik-Gość i rolami dla każdego użytkownika-gościa.

    ![Data-access-eleven][11]

1. Teraz użytkownik-Gość będzie trzeba wykonać pewne czynności, aby uzyskiwać dostęp do środowiska, znajduje się w witrynie Azure dzierżawy możesz po prostu zaprosić ich. Po pierwsze muszą zaakceptować zaproszenie, po prostu wysłane. To zaproszenie jest wysyłane za pośrednictwem poczty e-mail na adres e-mail, który zaproszenie w kroku 5. Powinni **wprowadzenie**, aby zaakceptować.

    ![dane access 12][12]

1. Następnie użytkownik-Gość należy zaakceptować uprawnienia związane z organizacji przez administratora.

    ![Data-access trzynaście][13]

1. Po użytkownik-Gość jest zalogowany do adresu e-mail, możesz zaprosić i zaakceptowania zaproszenia, będą one przejdź do insights.azure.com. Jeden raz, muszą kliknąć awatara obok poczty e-mail w prawym górnym rogu ekranu.

    ![Data-access czternastu][14]

1. Następnie użytkownik-Gość wybierze subskrypcji platformy Azure dzierżawy z menu rozwijanego katalogu. To jest dzierżawą, która je, aby zaprosić.

    ![Data-access 15][15]

1. Na koniec po użytkownik-Gość wybierze dzierżawy, zobaczy podanych właśnie im dostęp do środowiska usługi TSI. Teraz powinny mieć wszystkie funkcje związane z rolą, podana w kroku 8.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, [sposób dodawania źródła zdarzeń Centrum zdarzeń](./time-series-insights-how-to-add-an-event-source-eventhub.md) do środowiska Azure TSI.
* Wyślij [zdarzenia do źródła zdarzeń](./time-series-insights-send-events.md).
* Widok [środowiska w Eksploratorze usługi Time Series Insights](./time-series-insights-update-explorer.md).

<!-- Images -->
[1]: media/data-access/data-access-one.png
[2]: media/data-access/data-access-two.png
[3]: media/data-access/data-access-three.png
[4]: media/data-access/data-access-four.png
[5]: media/data-access/data-access-five.png
[6]: media/data-access/data-access-six.png
[7]: media/data-access/data-access-seven.png
[8]: media/data-access/data-access-eight.png
[9]: media/data-access/data-access-nine.png
[10]: media/data-access/data-access-ten.png
[11]: media/data-access/data-access-eleven.png
[12]: media/data-access/data-access-twelve.png
[13]: media/data-access/data-access-thirteen.png
[14]: media/data-access/data-access-fourteen.png
[15]: media/data-access/data-access-fifteen.png