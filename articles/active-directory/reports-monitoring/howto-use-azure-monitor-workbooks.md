---
title: Użyj usługi Azure Monitor skoroszyty dla raportów usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Azure Monitor skoroszytów w przypadku raportów usługi Azure Active Directory.
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
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 6ae14ec152975717af5d55780bcc39aa87c4b01a
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406593"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Jak używać usługi Azure Monitor skoroszyty dla raportów usługi Azure Active Directory

Czy chcesz:

- Opis wpływu usługi [zasady dostępu warunkowego](../conditional-access/overview.md) na środowisko logowania użytkowników?

- Rozwiązywanie problemów z logowań uzyskanie lepszego widoku kondycji logowania w Twojej organizacji i szybkie rozwiązywanie problemów?

- Tym, kto używa starszej wersji uwierzytelnień zalogować się do środowiska? (Przez [blokowanie uwierzytelniania starszych](../conditional-access/block-legacy-authentication.md), może poprawić ochrony Twojej dzierżawy.)

Aby pomóc rozwiązać te pytania, usługi Active Directory zawiera skoroszytów do monitorowania. [Usługa Azure Monitor skoroszyty](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) połączyć tekst, zapytania analityczne, metryki i parametrów w zaawansowanych interaktywnych raportów. 

W tym artykule:

- Zakłada się, kiedy znasz już sposób [twórz interaktywne raporty za pomocą skoroszytów Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

- Wyjaśnia, jak za pomocą skoroszytów Monitor wpływu zasad dostępu warunkowego do rozwiązywania problemów z błędami logowania, a także identyfikowania uwierzytelnień starszej wersji.
 


## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć skoroszytów monitora, potrzebne są:

- Dzierżawy usługi Active Directory z licencją premium (P1 lub P2). Dowiedz się, jak [Uzyskaj licencję premium](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium).

- A [obszaru roboczego usługi Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

## <a name="workbook-access"></a>Dostęp do skoroszytu 

Dostęp do skoroszytów:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory**.

3. W **monitorowanie** zaznacz **Insights**. 

    ![Wybierz usługę Insights](./media/howto-use-azure-monitor-workbooks/41.png)

4. Wybierz raport lub szablon lub na pasku narzędzi wybierz **Otwórz**. 

    ![Wybierz przycisk Otwórz](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>Analiza logowania

Uzyskiwanie dostępu do skoroszytu logowania analizy w **użycia** zaznacz **logowania**. 

Ten skoroszyt przedstawiono trendy dotyczące następujących logowania:

- Wszystkie logowania

- Sukces

- Oczekuje akcja użytkownika

- Niepowodzenie

Każdy trendu można filtrować według następujących kategorii:

- Zakres czasu

- Aplikacje

- Użytkownicy

![Analiza logowania](./media/howto-use-azure-monitor-workbooks/43.png)


Dla każdego trendu zapewnia następujące korzyści podział według następujących kategorii:

- Lokalizacja

    ![Logowania według lokalizacji](./media/howto-use-azure-monitor-workbooks/45.png)

- Urządzenie

    ![Logowania przez urządzenie](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Logowania przy użyciu starszej wersji uwierzytelniania 


Można uzyskać dostępu do skoroszytu do logowania, które używają [starsze uwierzytelnianie](../conditional-access/block-legacy-authentication.md)w **użycia** zaznacz **logowania przy użyciu starszej wersji uwierzytelniania**. 

Ten skoroszyt przedstawiono trendy dotyczące następujących logowania:

- Wszystkie logowania

- Sukces


Każdy trendu można filtrować według następujących kategorii:

- Zakres czasu

- Aplikacje

- Użytkownicy

- Protokoły

![Logowania przez starsze uwierzytelnianie](./media/howto-use-azure-monitor-workbooks/47.png)


Dla każdego trendu uzyskasz podział przez aplikację i protokołu.

![Starsza wersja uwierzytelniania logowania przez aplikację i protokół](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Logowania przez dostęp warunkowy 


Można uzyskać dostępu do skoroszytu do logowania przez [zasady dostępu warunkowego](../conditional-access/overview.md)w **dostępu warunkowego** zaznacz **logowania przez dostęp warunkowy**. 

Ten skoroszyt zawiera trendy dotyczące wyłączonego logowania. Każdy trendu można filtrować według następujących kategorii:

- Zakres czasu

- Aplikacje

- Użytkownicy

![Logowania przy użyciu dostępu warunkowego](./media/howto-use-azure-monitor-workbooks/49.png)


Do wyłączenia logowania otrzymasz podział według stanu dostępu warunkowego.

![Stan dostępu warunkowego](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>Logowania przez Udziel kontroli

Można uzyskać dostępu do skoroszytu do logowania przez [Udziel kontroli](../conditional-access/controls.md)w **dostępu warunkowego** zaznacz **logowania przez Udziel kontroli**. 

Ten skoroszyt pokazuje następujących wyłączonych logowania trendów:

- Wymaganie usługi MFA
 
- Wymagane warunki użytkowania

- Wymagane zasady zachowania poufności informacji

- Inny


Każdy trendu można filtrować według następujących kategorii:

- Zakres czasu

- Aplikacje

- Użytkownicy

![Logowania przez Udziel kontroli](./media/howto-use-azure-monitor-workbooks/50.png)


Dla każdego trendu uzyskasz podział przez aplikację i protokołu.

![Podział ostatnich operacji logowania](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Analiza niepowodzenia logowania

Użyj **analizy błędów logowania** skoroszytu w celu rozwiązywanie problemów z błędami następującym kodem:

- Logowania
- Zasady dostępu warunkowego
- Starsze uwierzytelnianie 


Uzyskiwanie dostępu do operacji logowania przez dane dostępu warunkowego w **rozwiązywanie** zaznacz **logowania przy użyciu starszej wersji uwierzytelniania**. 

Ten skoroszyt przedstawiono trendy dotyczące następujących logowania:

- Wszystkie logowania

- Sukces

- Oczekująca akcja

- Niepowodzenie


Każdy trendu można filtrować według następujących kategorii:

- Zakres czasu

- Aplikacje

- Użytkownicy

![Rozwiązywanie problemów z operacji logowania](./media/howto-use-azure-monitor-workbooks/52.png)


Aby ułatwić rozwiązywanie problemów z operacji logowania, usługi Azure Monitor zapewnia podział według następujących kategorii:

- Najważniejsze błędy

    ![Podsumowanie najważniejsze błędy](./media/howto-use-azure-monitor-workbooks/53.png)

- Czeka na akcję użytkownika logowania

    ![Podsumowanie logowań czeka na akcję użytkownika](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Kolejne kroki

[Twórz interaktywne raporty za pomocą skoroszytów Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).