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
ms.date: 11/15/2017
ms.openlocfilehash: c9bddf8e20524433b31793e277efd954a5d1320e
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2018
ms.locfileid: "47423378"
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Przyznawanie dostępu do danych w środowisku usługi Time Series Insights przy użyciu witryny Azure Portal

W tym artykule omówiono dwa rodzaje zasad dostępu usługi Time Series Insights.

## <a name="video"></a>Wideo: 

### <a name="in-this-video-we-cover-creating-and-managing-access-policies-within-time-series-insights-br"></a>W tym filmie wideo firma Microsoft obejmuje tworzenie i zarządzanie zasadami dostępu w ramach usługi Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/5zTIdyHMJW8]

Środowiska usługi Time Series Insights udostępniają dwa niezależne rodzaje zasad dostępu:

* Zasady dostępu do zarządzania
* Zasady dostępu do danych

Oba rodzaje zasad umożliwiają przyznawanie podmiotom usługi Azure Active Directory (użytkownikom i aplikacjom) różnych uprawnień w określonym środowisku. Podmioty (Użytkownicy i aplikacje) muszą należeć do usługi active directory (tzn. dzierżawy platformy Azure) skojarzonego z subskrypcją zawierającą środowisko.

Zasady dostępu do zarządzania pozwalają przyznawać uprawnienia związane z konfiguracją środowiska, takie jak
*   tworzenie i usuwanie środowiska, źródła zdarzeń, zestawy danych referencyjnych i
*   zarządzanie zasadami dostępu do danych.

Zasady dostępu do danych umożliwiają przyznawanie uprawnień do wysyłania zapytań dotyczących danych, manipulowania danymi referencyjnymi w środowisku oraz udostępniania zapisanych zapytań i perspektyw skojarzonych ze środowiskiem.

Dwa rodzaje zasad pozwalają wyraźnie oddzielić dostęp do zarządzania środowiskiem od dostępu do danych znajdujących się w tym środowisku. Na przykład istnieje możliwość konfigurowania środowiska w taki sposób, że właściciel/twórca środowiska jest usuwany z dostępu do danych. Ponadto użytkowników i usług, które mogą odczytać danych ze środowiska może otrzymać dostęp do konfiguracji środowiska.

## <a name="grant-data-access"></a>Przyznawanie dostępu do danych
Wykonaj następujące kroki, aby zezwolić na dostęp do nazwy głównej użytkownika:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Znajdź środowiska usługi Time Series Insights. Typ **szeregów czasowych** w **wyszukiwania** pole. Wybierz **środowiska usługi Time Series** w wynikach wyszukiwania. 

3. Wybierz środowisko usługi Time Series Insights z listy.
   
4. Wybierz **zasady dostępu do danych**, a następnie wybierz **+ Dodaj**.
  ![Zarządzanie źródłem usługi Time Series Insights — środowisko](media/data-access/getstarted-grant-data-access1.png)

5. Wybierz **wybierz użytkownika**.  Wyszukaj adres nazwy lub adresu e-mail użytkownika zlokalizować użytkownika, którego chcesz dodać. Kliknij przycisk **wybierz** aby potwierdzić wybór. 

   ![Zarządzanie źródłem usługi Time Series Insights — dodawanie](media/data-access/getstarted-grant-data-access2.png)

6. Wybierz **wybierz rolę**. Wybierz rolę odpowiedni dostęp dla użytkownika:
   - Wybierz **Współautor** Jeśli chcesz zezwolić użytkownikowi na zmianę danych referencyjnych i udziału zapisany, zapytań i perspektyw innym użytkownikom środowiska. 
   - W przeciwnym razie wybierz **czytnika** do zezwalania na zapytania o dane użytkownika w środowisku i zapisywanie osobistych zapytań (nie udostępnianych) w środowisku.

   Wybierz **Ok** aby potwierdzić wybór roli.

   ![Zarządzanie źródłem usługi Time Series Insights — wybieranie użytkownika](media/data-access/getstarted-grant-data-access3.png)

8. Wybierz **Ok** w **wybierz rolę użytkownika** strony.

   ![Zarządzanie źródłem usługi Time Series Insights — wybieranie roli](media/data-access/getstarted-grant-data-access4.png)

9. **Zasady dostępu do danych** strona zawiera listę użytkowników i rolami dla każdego użytkownika.

   ![Zarządzanie źródłem usługi Time Series Insights — wyniki](media/data-access/getstarted-grant-data-access5.png)

## <a name="provide-guest-access-to-a-user-from-another-aad-tenant"></a>Dostęp gościa do użytkownika z innej dzierżawy usługi AAD

"Gość" nie jest rolą zarządzania; to termin używany dla konta które jednej dzierżawy jest zostali zaproszeni do innej. Po konta gościa został zaproszony do katalogu dzierżawy, może mieć tej samej kontroli dostępu, zastosowano takich jak innego konta, aby udzielić dostępu do zarządzania w środowisku usługi TSI za pomocą bloku kontrola dostępu (IAM) lub do udzielania dostępu do danych w środowisko za pomocą bloku zasady dostępu do danych. Aby uzyskać więcej informacji na temat dostępu gościa dzierżawy usługi AAD, zobacz ten [dokumentu](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Wykonaj następujące kroki, aby udzielić dostępu gościa do środowiska usługi Time Series Insights do użytkownika usługi AAD z innej dzierżawy:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Znajdź środowiska usługi Time Series Insights. Typ **szeregów czasowych** w **wyszukiwania** pole. Wybierz **środowiska usługi Time Series** w wynikach wyszukiwania.

3. Wybierz środowisko usługi Time Series Insights z listy.

4. Wybierz **zasady dostępu do danych**, a następnie wybierz + **zaprosić**.

    ![Zarządzanie źródłem usługi Time Series Insights — zaproszenie użytkownika](media/data-access/getstarted-grant-data-access6.png)

5. Podaj adres e-mail użytkownika, którą chcesz zaprosić. Należy zauważyć, że powinno to być wiadomość e-mail skojarzony z usługi AAD. Możesz opcjonalnie dołączyć osobistą wiadomość z zaproszeniem.

    ![Zarządzanie źródłem usługi Time Series Insights — wybieranie użytkownika](media/data-access/getstarted-grant-data-access7.png)

6. Powinien zostać wyświetlony bąbelek potwierdzenia są wyświetlane na ekranie.

    ![Zarządzanie źródłem usługi Time Series Insights — potwierdzenie użytkownika](media/data-access/getstarted-grant-data-access8.png)

7. Wybierz **wybierz użytkownika**. Wyszukaj adres e-mail użytkownik-Gość zaproszeni do zlokalizowania użytkownika, którego chcesz dodać. Kliknij przycisk **wybierz** aby potwierdzić wybór.
  
    ![Zarządzanie źródłem usługi Time Series Insights — potwierdzenie użytkownika](media/data-access/getstarted-grant-data-access9.png)

8. Wybierz **wybierz rolę**. Wybierz rolę odpowiedni dostęp dla użytkownika gościa:

    * Wybierz **Współautor** Jeśli chcesz zezwolić użytkownikowi na zmianę danych referencyjnych i udziału zapisany, zapytań i perspektyw innym użytkownikom środowiska.

    * W przeciwnym razie wybierz **czytnika** do zezwalania na zapytania o dane użytkownika w środowisku i zapisywanie osobistych zapytań (nie udostępnianych) w środowisku.

    Wybierz **Ok** aby potwierdzić wybór roli.

    ![Zarządzanie źródłem usługi Time Series Insights — wybieranie roli](media/data-access/getstarted-grant-data-access10.png)

9. Wybierz **Ok** w **wybierz rolę użytkownika** strony.

10. **Zasady dostępu do danych** strony wyświetla teraz użytkownik-Gość i rolami dla każdego użytkownika-gościa.

    ![Zarządzanie źródłem usługi Time Series Insights — potwierdzenie roli](media/data-access/getstarted-grant-data-access11.png)

11. Teraz użytkownik-Gość będzie trzeba wykonać pewne czynności, aby uzyskiwać dostęp do środowiska, znajduje się w witrynie Azure dzierżawy możesz po prostu zaprosić ich. Po pierwsze muszą zaakceptować zaproszenie, po prostu wysłane. To zaproszenie jest wysyłane za pośrednictwem poczty e-mail na adres e-mail, który zaproszenie w kroku 5. Powinny one kliknij "Zacznij pracę," Aby zaakceptować.

    ![Zarządzanie źródłem usługi Time Series Insights — zaproszenie użytkownika](media/data-access/getstarted-grant-data-access12.png)

12. Następnie użytkownik-Gość należy zaakceptować uprawnienia związane z organizacji przez administratora.

    ![Zarządzanie źródłem usługi Time Series Insights — zaakceptowania uprawnień](media/data-access/getstarted-grant-data-access13.png)

13. Po użytkownik-Gość jest zalogowany do adresu e-mail, możesz zaprosić i zaakceptowania zaproszenia, będą one przejdź do insights.azure.com. Jeden raz, muszą kliknąć awatara obok poczty e-mail w prawym górnym rogu ekranu. 

    ![Zarządzanie źródłem usługi Time Series Insights — zaakceptowania uprawnień](media/data-access/getstarted-grant-data-access14.png)

14. Następnie użytkownik-Gość wybierze subskrypcji platformy Azure dzierżawy z menu rozwijanego katalogu. To jest dzierżawą, która je, aby zaprosić. 

    ![Zarządzanie źródłem usługi Time Series Insights — zaakceptowania uprawnień](media/data-access/getstarted-grant-data-access15.png)

15. Na koniec po użytkownik-Gość wybierze dzierżawy, zobaczy środowisko usługi Time Series Insights, w którym możesz po prostu udostępniony im dostęp do. Teraz powinny mieć wszystkie funkcje związane z rolą, podana w kroku 8.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, [sposób dodawania źródła zdarzeń Centrum zdarzeń do środowiska usługi Azure Time Series Insights](time-series-insights-how-to-add-an-event-source-eventhub.md).
* [Wysyłanie zdarzeń](time-series-insights-send-events.md) do źródła zdarzenia.
* Wyświetlanie środowiska w [Eksploratora usługi Time Series Insights](https://insights.timeseries.azure.com).
