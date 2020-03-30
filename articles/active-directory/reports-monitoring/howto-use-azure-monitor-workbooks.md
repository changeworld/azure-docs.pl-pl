---
title: Skoroszyty usługi Azure Monitor dla raportów | Dokumenty firmy Microsoft
description: Dowiedz się, jak używać skoroszytów usługi Azure Monitor w raportach usługi Azure Active Directory.
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/30/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 2e94d9f56a865999f9169650f621a6af892c27ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014376"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Jak używać skoroszytów usługi Azure Monitor dla raportów usługi Azure Active Directory

> [!IMPORTANT]
> Aby zoptymalizować podstawowe zapytania w tym skoroszycie, kliknij "Edytuj", kliknij ikonę Ustawienia i wybierz obszar roboczy, w którym chcesz uruchomić te kwerendy. Skoroszyty domyślnie wybierze wszystkie obszary robocze, w których są routing dzienników usługi Azure AD. 

Czy chcesz:

- Rozumiesz wpływ [zasad dostępu warunkowego](../conditional-access/overview.md) na środowisko logowania użytkowników?

- Rozwiązywanie problemów z błędami logowania w celu uzyskania lepszego widoku kondycji logowania w organizacji i szybkiego rozwiązywania problemów?

- Dowiedz się, kto używa starszych uwierzytelniania do logowania się do środowiska? (Blokując [uwierzytelnianie starsze,](../conditional-access/block-legacy-authentication.md)można poprawić ochronę dzierżawy.

- Czy musisz zrozumieć wpływ zasad dostępu warunkowego w dzierżawie?

- Czy chcesz mieć możliwość przeglądania: kwerend dziennika logowania, skoroszytu raportuje, ilu użytkowników otrzymało lub odmówiono dostępu, a także ilu użytkowników ominęły zasady dostępu warunkowego podczas uzyskiwania dostępu do zasobów?

- Chcesz opracować głębsze zrozumienie: szczegóły skoroszytu na warunek, tak aby wpływ zasad może być kontekstualizowane na warunek, w tym platformy urządzenia, stan urządzenia, aplikacji klienckiej, ryzyko logowania, lokalizacji i aplikacji?

- Uzyskaj głębszy wgląd w zapytania dziennika logowania, skoroszyt raportuje, ilu użytkowników otrzymało lub odmówiono dostępu, a także ilu użytkowników ominęły zasady dostępu warunkowego podczas uzyskiwania dostępu do zasobów.

- Aby ułatwić odpowiedzi na te pytania, usługa Active Directory udostępnia skoroszyty do monitorowania. [Skoroszyty usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) łączą tekst, zapytania analityczne, metryki i parametry w zaawansowane raporty interaktywne.



W tym artykule:

- Zakłada się, że znasz sposób [tworzenia raportów interaktywnych przy użyciu skoroszytów Monitora](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

- W tym artykule wyjaśniono, jak używać skoroszytów monitoruj, aby zrozumieć wpływ zasad dostępu warunkowego, rozwiązywać problemy z błędami logowania i identyfikować starsze uwierzytelnianie.
 


## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać ze skoroszytów Monitoruj, potrzebujesz:

- Dzierżawa usługi Active Directory z licencją premium (P1 lub P2). Dowiedz się, jak [uzyskać licencję premium](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium).

- [Obszar roboczy usługi Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

- [Dostęp](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions) do obszaru roboczego analizy dzienników
- Obserwowanie ról w usłudze Azure Active Directory (jeśli uzyskujesz dostęp do usługi Log Analytics za pośrednictwem portalu usługi Azure Active Directory)
    - Administrator zabezpieczeń
    - Czytelnik zabezpieczeń
    - Czytnik raportów
    - Administrator globalny

## <a name="roles"></a>Role
Musisz być w jednej z następujących ról, a także mieć dostęp do podstawowego obszaru roboczego [usługi Log Analytics,](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions) aby zarządzać skoroszytami:
-   Administrator globalny
-   Administrator zabezpieczeń
-   Czytelnik zabezpieczeń
-   Czytnik raportów
-   Administrator aplikacji

## <a name="workbook-access"></a>Dostęp do skoroszytu 

Aby uzyskać dostęp do skoroszytów:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. Przejdź do**skoroszytów****monitorowania** >  **usługi Azure Active Directory** > . 

1. Wybierz raport lub szablon lub na pasku narzędzi **wybierz**otwórz . 

![Znajdowanie skoroszytów usługi Azure Monitor w usłudze Azure AD](./media/howto-use-azure-monitor-workbooks/azure-monitor-workbooks-in-azure-ad.png)

## <a name="sign-in-analysis"></a>Analiza logowania

Aby uzyskać dostęp do skoroszytu analizy logowania, w sekcji **Użycie** wybierz pozycję **Zaloguj się**. 

Ten skoroszyt zawiera następujące trendy logowania:

- Wszystkie logowania

- Powodzenie

- Oczekująca akcja użytkownika

- Niepowodzenie

Każdy trend można filtrować według następujących kategorii:

- Przedział czasu

- Aplikacje

- Użytkownicy

![Analiza logowania](./media/howto-use-azure-monitor-workbooks/43.png)


Dla każdego trendu otrzymujesz podział według następujących kategorii:

- Lokalizacja

    ![Logowania według lokalizacji](./media/howto-use-azure-monitor-workbooks/45.png)

- Urządzenie

    ![Logowanie według urządzenia](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Logowania przy użyciu uwierzytelniania starszego 


Aby uzyskać dostęp do skoroszytu dla logowania korzystających ze [starszego uwierzytelniania,](../conditional-access/block-legacy-authentication.md)w sekcji **Użycie** wybierz pozycję **Zaloguj się przy użyciu uwierzytelniania starszego.** 

Ten skoroszyt zawiera następujące trendy logowania:

- Wszystkie logowania

- Powodzenie


Każdy trend można filtrować według następujących kategorii:

- Przedział czasu

- Aplikacje

- Użytkownicy

- Protokoły

![Logowanie według starszego uwierzytelniania](./media/howto-use-azure-monitor-workbooks/47.png)


Dla każdego trendu otrzymasz podział według aplikacji i protokołu.

![Logowania do uwierzytelniania starszego według aplikacji i protokołu](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Logowania według dostępu warunkowego 


Aby uzyskać dostęp do skoroszytu dla logów według [zasad dostępu warunkowego,](../conditional-access/overview.md)w sekcji **Dostęp warunkowy** wybierz pozycję **Zaloguj się według dostępu warunkowego**. 

Ten skoroszyt zawiera trendy dla wyłączonych logowania. Każdy trend można filtrować według następujących kategorii:

- Przedział czasu

- Aplikacje

- Użytkownicy

![Logowania przy użyciu dostępu warunkowego](./media/howto-use-azure-monitor-workbooks/49.png)


W przypadku osób niepełnosprawnych można uzyskać podział według stanu Dostęp warunkowy.

![Stan dostępu warunkowego](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)


## <a name="conditional-access-insights"></a>Szczegółowe informacje na temat dostępu warunkowego

### <a name="overview"></a>Omówienie

Skoroszyty zawierają zapytania dziennika logowania, które mogą pomóc administratorom IT monitorować wpływ zasad dostępu warunkowego w ich dzierżawie. Masz możliwość raportowania liczby użytkowników, którym udzielono lub odmówiono dostępu. Skoroszyt zawiera szczegółowe informacje na temat liczby użytkowników, którzy omijaliby zasady dostępu warunkowego na podstawie atrybutów tych użytkowników podczas logowania. Zawiera szczegóły na warunek, dzięki czemu wpływ zasad może być kontekstualizowane na warunek, w tym platformy urządzenia, stan urządzenia, aplikacji klienckiej, ryzyko logowania, lokalizacji i aplikacji.

### <a name="instructions"></a>Instrukcje 
Aby uzyskać dostęp do skoroszytu statystyk dostępu warunkowego, wybierz skoroszyt **Statystyka dostępu warunkowego** w sekcji Dostęp warunkowy. Ten skoroszyt pokazuje oczekiwany wpływ każdej zasady dostępu warunkowego w dzierżawie. Wybierz jedną lub więcej zasad dostępu warunkowego z listy rozwijanej i zawęź zakres skoroszytu, stosując następujące filtry: 

- **Zakres czasu**

- **Użytkownik**

- **Aplikacje**

- **Widok danych**

![Stan dostępu warunkowego](./media/howto-use-azure-monitor-workbooks/access-insights.png)


Podsumowanie wpływu pokazuje liczbę użytkowników lub logowania, dla których wybrane zasady miały określony wynik. Suma to liczba użytkowników lub logów, dla których wybrane zasady zostały ocenione w wybranym zakresie czasu. Kliknij kafelek, aby filtrować dane w skoroszycie według tego typu wyników. 

![Stan dostępu warunkowego](./media/howto-use-azure-monitor-workbooks/impact-summary.png)

Ten skoroszyt pokazuje również wpływ wybranych zasad w podziale na każdy z sześciu warunków: 
- **Stan urządzenia**
- **Platforma urządzeń**
- **Aplikacje klienckie**
- **Ryzyko logowania**
- **Lokalizacja**
- **Aplikacje**

![Stan dostępu warunkowego](./media/howto-use-azure-monitor-workbooks/device-platform.png)

Można również zbadać poszczególne logowania, filtrowane według parametrów wybranych w skoroszycie. Wyszukaj poszczególnych użytkowników według częstotliwości logowania i wyświetl odpowiednie zdarzenia logowania. 

![Stan dostępu warunkowego](./media/howto-use-azure-monitor-workbooks/filtered.png)





## <a name="sign-ins-by-grant-controls"></a>Logowania za pomocą formantów dotacji

Aby uzyskać dostęp do skoroszytu logowania za pomocą [formantów dotacji,](../conditional-access/controls.md)w sekcji **Dostęp warunkowy** wybierz pozycję **Zaloguj się za pomocą formantów dotacji**. 

Ten skoroszyt zawiera następujące wyłączone trendy logowania:

- Wymaganie usługi MFA
 
- Wymaganie warunków użytkowania

- Wymagaj zasad zachowania poufności informacji

- Inne


Każdy trend można filtrować według następujących kategorii:

- Przedział czasu

- Aplikacje

- Użytkownicy

![Logowania za pomocą formantów dotacji](./media/howto-use-azure-monitor-workbooks/50.png)


Dla każdego trendu otrzymasz podział według aplikacji i protokołu.

![Podział ostatnich logów](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Analiza błędów logowania

Użyj **skoroszytu analizy błędów logowania,** aby rozwiązywać problemy z błędami:

- Logowania
- Zasady dostępu warunkowego
- Uwierzytelnianie starsze 


Aby uzyskać dostęp do logów według danych dostępu warunkowego, w sekcji **Rozwiązywanie problemów** wybierz pozycję **Logowanie przy użyciu uwierzytelniania starszego.** 

Ten skoroszyt zawiera następujące trendy logowania:

- Wszystkie logowania

- Powodzenie

- Działanie oczekujące

- Niepowodzenie


Każdy trend można filtrować według następujących kategorii:

- Przedział czasu

- Aplikacje

- Użytkownicy

![Rozwiązywanie problemów z logowaniami](./media/howto-use-azure-monitor-workbooks/52.png)


Aby ułatwić rozwiązywanie problemów z logami, usługa Azure Monitor udostępnia podział według następujących kategorii:

- Najważniejsze błędy

    ![Podsumowanie najważniejszych błędów](./media/howto-use-azure-monitor-workbooks/53.png)

- Logowania oczekujące na akcję użytkownika

    ![Podsumowanie logów oczekujących na akcję użytkownika](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Następne kroki

[Tworzenie interaktywnych raportów przy użyciu skoroszytów Monitoruj](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).
