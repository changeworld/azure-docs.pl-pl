---
title: Ścieżki usługi Azure Application Insights
description: Dowiedz się, jak za pomocą ścieżek można dowiedzieć się, jak klienci wchodzą w interakcję z aplikacją.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/17/2017
ms.reviewer: mbullwin
ms.openlocfilehash: 89440a6385bab5b917a866b686e8d2ba828c92e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671056"
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>Dowiedz się, jak klienci korzystają z aplikacji za pomocą ścieżek usługi Application Insights

Zrozumienie doświadczenia klienta jest sprawą najwyższej wagi dla Twojej firmy. Jeśli aplikacja obejmuje wiele etapów, należy wiedzieć, czy większość klientów postępują w całym procesie lub jeśli kończą proces w pewnym momencie. Postęp przez szereg kroków w aplikacji sieci web jest znany jako *lejek*. Ścieżki usługi Azure Application Insights umożliwiają uzyskiwanie szczegółowych informacji na temat użytkowników i monitorowanie współczynników konwersji krok po kroku. 

## <a name="create-your-funnel"></a>Stwórz swój lejek
Przed utworzeniem ścieżki zdecyduj się na pytanie, na które chcesz odpowiedzieć. Na przykład można wiedzieć, ilu użytkowników przegląda stronę główną, wyświetla profil klienta i tworzy bilet. W tym przykładzie właściciele firmy Fabrikam Fiber chcą poznać procent klientów, którzy pomyślnie utworzyli bilet odbiorcy.

Oto kroki, które podejmują, aby utworzyć ich lejek.

1. W narzędziu Ścieżki wglądu w aplikacje wybierz pozycję **Nowy**.
1. Z menu rozwijanego **Zakres czasu** wybierz pozycję **Ostatnie 90 dni**. Wybierz **moje ścieżki lub** **ścieżki udostępnione**.
1. Z listy rozwijanej **Krok 1** wybierz pozycję **Indeks**. 
1. Z listy **Krok 2** wybierz pozycję **Klient**.
1. Z listy **Krok 3** wybierz pozycję **Utwórz**.
1. Dodaj nazwę do ścieżki i wybierz pozycję **Zapisz**.

Poniższy zrzut ekranu przedstawia przykład rodzaju danych generowanych przez narzędzie Ścieżki. Właściciele firmy Fabrikam widzą, że w ciągu ostatnich 90 dni 54,3 procent klientów, którzy odwiedzili stronę główną, utworzyło bilet dla klientów. Widzą również, że 2700 ich klientów przyszło do indeksu ze strony głównej. Może to wskazywać na problem z odświeżaniem.


![Zrzut ekranu przedstawiający narzędzie Ścieżki z danymi](media/usage-funnels/funnel1.png)

### <a name="funnels-features"></a>Funkcje ścieżek
Poprzedni zrzut ekranu zawiera pięć wyróżnionych obszarów. Są to cechy lejków. Poniższa lista wyjaśnia więcej informacji o każdym odpowiednim obszarze na zrzucie ekranu:
1. Jeśli aplikacja jest próbkowanie, zobaczysz baner próbkowania. Wybranie transparentu powoduje otwarcie okienka kontekstu z wyjaśnieniem, jak wyłączyć próbkowanie. 
2. Lejek można eksportować do [usługi Power BI](../../azure-monitor/app/export-power-bi.md ).
3. Wybierz krok, aby wyświetlić więcej szczegółów po prawej stronie. 
4. Historyczny wykres konwersji pokazuje współczynniki konwersji z ostatnich 90 dni. 
5. Lepiej poznaj użytkowników, uzyskując dostęp do narzędzia dla użytkowników. Filtry można używać w każdym kroku. 

## <a name="next-steps"></a>Następne kroki
  * [Omówienie użycia](usage-overview.md)
  * [Użytkownicy, sesje i zdarzenia](usage-segmentation.md)
  * [Przechowywanie](usage-retention.md)
  * [Skoroszyty](../../azure-monitor/app/usage-workbooks.md)
  * [Dodawanie kontekstu użytkownika](usage-send-user-context.md)
  * [Eksportowanie do usługi Power BI](../../azure-monitor/app/export-power-bi.md )

