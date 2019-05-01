---
title: Skonfiguruj zabezpieczenia dostępu i zarządzanie Azure czas Series Insights w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania zabezpieczeń i uprawnień jako dostęp do funkcji zarządzania zasadami i dostępu do danych zasad, aby zabezpieczyć Azure czas Series Insights w wersji zapoznawczej.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/26/2018
ms.custom: seodec18
ms.openlocfilehash: dd4c5e1652eb4dbff66591aa4bbe74e51be3e6c0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64716716"
---
# <a name="grant-data-access-to-an-environment"></a>Przyznawanie dostępu do danych w środowisku

W tym artykule omówiono dwa rodzaje zasad dostępu Azure czas Series Insights w wersji zapoznawczej.

## <a name="sign-in-to-tsi"></a>Zaloguj się w usłudze TSI

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Znajdź środowiska usługi Time Series Insights. Wprowadź `Time Series` w **wyszukiwania** pole. Wybierz **środowiska usługi Time Series** w wynikach wyszukiwania.
1. Wybierz środowisko usługi Time Series Insights z listy.

## <a name="grant-data-access"></a>Przyznawanie dostępu do danych

Wykonaj następujące kroki, aby zezwolić na dostęp do nazwy głównej użytkownika.

1. Wybierz **zasady dostępu do danych**, a następnie wybierz pozycję **+ Dodaj**.

    ![Data access jednego][1]

1. Wybierz **wybierz użytkownika**. Wyszukaj adres nazwy lub adresu e-mail użytkownika zlokalizować użytkownika, którego chcesz dodać. Kliknij przycisk **wybierz** aby potwierdzić wybór.

    ![dane dostępu 2][2]

1. Wybierz **wybierz rolę**. Wybierz rolę odpowiedni dostęp dla użytkownika:

    * Wybierz **Współautor** Jeśli chcesz zezwolić użytkownikowi na zmianę danych referencyjnych i udziału zapisany, zapytań i perspektyw innym użytkownikom środowiska.

    * W przeciwnym razie wybierz **czytnika** zezwala użytkownikowi na zapytania o dane w środowisku i zapisywanie osobistych, nie udostępnionych kwerend w środowisku.

   Wybierz **OK** aby potwierdzić wybór roli.

    ![dane dostępu 3][3]

1. Wybierz **OK** na **wybierz rolę użytkownika** strony.

    ![cztery w przypadku dostępu do danych][4]

1. Upewnij się, że **zasady dostępu do danych** strona zawiera listę użytkowników i rolami dla każdego użytkownika.

    ![Pięć w przypadku dostępu do danych][5]

## <a name="provide-guest-access-from-another-aad-tenant"></a>Dostęp gościa z innej dzierżawy usługi AAD

`Guest` nie jest rolą zarządzania. To termin używany dla konta które zostało zaproszone jednej dzierżawy do innego. Po konta gościa otrzymał zaproszenie do katalogu dzierżawy, może mieć tej samej kontroli dostępu, zastosowano podobnie jak inne konto. Za udzielić dostępu do zarządzania i środowiska usługi Time Series Insights za pomocą bloku kontrola dostępu (IAM). Lub możesz udzielić dostępu do danych w środowisku za pomocą bloku zasady dostępu do danych. Aby uzyskać więcej informacji na temat dostępu gościa dzierżawy usługi Azure Active Directory (Azure AD), przeczytaj [użytkowników we współpracy Dodawanie usługi Azure Active Directory B2B, w witrynie Azure portal](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Wykonaj następujące kroki, aby udzielić dostępu gościa do środowiska usługi Time Series Insights do użytkownika usługi Azure AD z innej dzierżawy.

1. Wybierz **zasady dostępu do danych**, a następnie wybierz pozycję **+ zaprosić**.

    ![Data-access-six][6]

1. Wprowadź adres e-mail użytkownika, które chcesz zaprosić. Ten adres e-mail musi być skojarzony z usługą Azure AD. Możesz opcjonalnie dołączyć osobistą wiadomość z zaproszeniem.

    ![Data access siódmego][7]

1. Zwróć uwagę na bąbelek potwierdzenie, który pojawia się na ekranie.

    ![Data access osiem][8]

1. Wybierz **wybierz użytkownika**. Wyszukaj adres e-mail użytkownika gościa, zaprosić do zlokalizowania użytkownika, którego chcesz dodać. Kliknij przycisk **wybierz** aby potwierdzić wybór.

    ![Data access dziewiątego][9]

1. Wybierz **wybierz rolę**. Wybierz rolę odpowiedni dostęp dla użytkownika gościa:

    * Wybierz **Współautor** Jeśli chcesz zezwolić użytkownikowi na zmianę danych referencyjnych i udziału zapisany, zapytań i perspektyw innym użytkownikom środowiska.

    * W przeciwnym razie wybierz **czytnika** zezwala użytkownikowi na zapytania o dane w środowisku i zapisywanie osobistych, nie udostępnionych kwerend w środowisku.

   Wybierz **OK** aby potwierdzić wybór roli.

    ![Data access dziesięciu][10]

1. Wybierz **OK** na **wybierz rolę użytkownika** strony.

1. Upewnij się, że **zasady dostępu do danych** strona zawiera listę użytkownik-Gość i role dla każdego użytkownika-gościa.

    ![Data-access-eleven][11]

1. Teraz użytkownik-Gość musi wykonać kroki, aby uzyskiwać dostęp do środowiska, znajduje się w dzierżawie platformy Azure, do którego zaproszenie. Po pierwsze mogą zaakceptować zaproszenie, wysłane. To zaproszenie jest wysyłane za pośrednictwem poczty e-mail na adres e-mail, którego użyto w kroku 5. Wybierają **wprowadzenie** zaakceptować.

    ![Data-access-twelve][12]

1. Następnie użytkownik-Gość akceptuje uprawnienia związane z organizacji przez administratora.

    ![Data-access trzynaście][13]

1. Po użytkownik-Gość jest zalogowany na adres e-mail używany Aby zaprosić go i ich zaakceptowanie zaproszenia, komputery przechodzą do insights.azure.com. Jeden raz, wybierają awatara obok swojego adresu e-mail w prawym górnym rogu ekranu.

    ![Data-access-fourteen][14]

1. Następnie wybiera użytkownika gościa dzierżawy platformy Azure, z menu rozwijanego katalogu. Ta dzierżawa jest jeden, do którego zaproszenie.

    ![Data-access-fifteen][15]

Po użytkownik-Gość wybierze dzierżawy, użytkownik zobaczy środowiska usługi Time Series Insights, do którego zostanie udostępniony im dostęp. Klienci mają teraz wszystkie funkcje, które są skojarzone z rolą podanym je w **kroku 5**.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, [sposób dodawania źródła zdarzeń usługi Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) do środowiska usługi Time Series Insights.

* Wyślij [zdarzenia do źródła zdarzeń](./time-series-insights-send-events.md).

* Widok [środowiska w Eksploratorze czasu Series Insights w wersji zapoznawczej](./time-series-insights-update-explorer.md).

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
