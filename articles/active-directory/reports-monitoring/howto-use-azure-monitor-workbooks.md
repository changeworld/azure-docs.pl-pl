---
title: Użyj Azure Monitor skoroszytów dla raportów Azure Active Directoryowych | Microsoft Docs
description: Dowiedz się, jak używać Azure Monitor skoroszytów dla raportów Azure Active Directory.
services: active-directory
author: cawrites
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.openlocfilehash: c1ecdb80263efda4cbbb43caaa4e27a04b261f81
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989803"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Jak używać Azure Monitor skoroszytów dla raportów Azure Active Directory

Czy chcesz:

- Rozumiesz wpływ [zasad dostępu warunkowego](../conditional-access/overview.md) na logowanie użytkowników?

- Rozwiązywanie problemów z błędami logowania w celu uzyskania lepszych wglądu w kondycję logowania w organizacji i szybkie rozwiązywanie problemów?

- Wiedzieć, kto używa starszych uwierzytelnień do logowania się do środowiska? (Przez [blokowanie starszego uwierzytelniania](../conditional-access/block-legacy-authentication.md)można poprawić ochronę dzierżawy).

Aby ułatwić rozwiązanie tych pytań, Active Directory udostępnia skoroszyty do monitorowania. [Azure monitor skoroszyty](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) łączą teksty, zapytania analityczne, metryki i parametry w rozbudowanych raportach interaktywnych. 

W tym artykule:

- Przyjęto założenie, że wiesz już, jak [tworzyć interaktywne raporty przy użyciu skoroszytów monitorowania](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

- Wyjaśnia, jak używać skoroszytów do monitorowania, aby zrozumieć wpływ zasad dostępu warunkowego, rozwiązywać problemy z błędami logowania i identyfikować starsze uwierzytelnienia.
 


## <a name="prerequisites"></a>Wymagania wstępne

Aby używać skoroszytów monitorowania, potrzebne są:

- Dzierżawa Active Directory z licencją premium (P1 lub P2). Dowiedz się [, jak uzyskać licencję Premium](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium).

- [Obszar roboczy log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

## <a name="workbook-access"></a>Dostęp do skoroszytu 

Aby uzyskać dostęp do skoroszytów:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W okienku nawigacji po lewej stronie wybierz pozycję **Azure Active Directory**.

3. W sekcji **monitorowanie** wybierz pozycję **skoroszyty**. 

    ![Wybieranie szczegółowych informacji](./media/howto-use-azure-monitor-workbooks/41.png)

4. Wybierz raport lub szablon lub na pasku narzędzi wybierz pozycję **Otwórz**. 

    ![Wybierz przycisk Otwórz](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>Analiza logowania

Aby uzyskać dostęp do skoroszytu analizy logowania, w sekcji **użycie** wybierz pozycję **logowania**. 

Ten skoroszyt zawiera następujące trendy dotyczące logowania:

- Wszystkie logowania

- Powodzenie

- Oczekuje akcja użytkownika

- Niepowodzenie

Każdy trend można filtrować według następujących kategorii:

- Zakres czasu

- Aplikacje

- Użytkownicy

![Analiza logowania](./media/howto-use-azure-monitor-workbooks/43.png)


Dla każdego trendu otrzymujesz podział według następujących kategorii:

- Location

    ![Logowania według lokalizacji](./media/howto-use-azure-monitor-workbooks/45.png)

- Urządzenie

    ![Logowania według urządzenia](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Logowania przy użyciu starszego uwierzytelniania 


Aby uzyskać dostęp do skoroszytu w celu logowania przy użyciu [starszego uwierzytelniania](../conditional-access/block-legacy-authentication.md), w sekcji **użycie** wybierz pozycję **logowania przy użyciu starszego uwierzytelniania**. 

Ten skoroszyt zawiera następujące trendy dotyczące logowania:

- Wszystkie logowania

- Powodzenie


Każdy trend można filtrować według następujących kategorii:

- Zakres czasu

- Aplikacje

- Użytkownicy

- Protokoły

![Logowania przy użyciu starszego uwierzytelniania](./media/howto-use-azure-monitor-workbooks/47.png)


Dla każdego trendu można uzyskać Podział według aplikacji i protokołu.

![Starsze-uwierzytelnianie logowania według aplikacji i protokołu](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Logowania przez dostęp warunkowy 


Aby uzyskać dostęp do skoroszytu w przypadku logowania za pomocą [zasad dostępu warunkowego](../conditional-access/overview.md), w sekcji **dostęp warunkowy** wybierz pozycję **logowania przez dostęp warunkowy**. 

Ten skoroszyt zawiera trendy dotyczące wyłączonych logowań. Każdy trend można filtrować według następujących kategorii:

- Zakres czasu

- Aplikacje

- Użytkownicy

![Logowania przy użyciu dostępu warunkowego](./media/howto-use-azure-monitor-workbooks/49.png)


W przypadku wyłączonych logowań otrzymujesz podział według stanu dostępu warunkowego.

![Stan dostępu warunkowego](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>Operacje logowania przez przyznanie kontroli

Aby uzyskać dostęp do skoroszytu w celu logowania przez [przyznanie kontroli](../conditional-access/controls.md), w sekcji **dostęp warunkowy** wybierz pozycję **logowania przez przyznanie kontrolek**. 

Ten skoroszyt zawiera następujące wyłączane trendy dotyczące logowania:

- Wymaganie usługi MFA
 
- Wymaganie warunków użytkowania

- Wymagaj zasad zachowania poufności informacji

- Inne


Każdy trend można filtrować według następujących kategorii:

- Zakres czasu

- Aplikacje

- Użytkownicy

![Operacje logowania przez przyznanie kontroli](./media/howto-use-azure-monitor-workbooks/50.png)


Dla każdego trendu można uzyskać Podział według aplikacji i protokołu.

![Podział najnowszych logowań](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Analiza niepowodzeń logowania

Za pomocą skoroszytu **analizy błędów logowania** można rozwiązywać problemy z następującymi kwestiami:

- Logowania
- Zasady dostępu warunkowego
- Starsze uwierzytelnianie 


Aby uzyskać dostęp do logowania za pomocą danych dostępu warunkowego, w sekcji **Rozwiązywanie problemów** wybierz pozycję **logowania przy użyciu starszego uwierzytelniania**. 

Ten skoroszyt zawiera następujące trendy dotyczące logowania:

- Wszystkie logowania

- Powodzenie

- Oczekująca akcja

- Niepowodzenie


Każdy trend można filtrować według następujących kategorii:

- Zakres czasu

- Aplikacje

- Użytkownicy

![Rozwiązywanie problemów z logowaniem](./media/howto-use-azure-monitor-workbooks/52.png)


Aby pomóc w rozwiązywaniu problemów z logowaniem, Azure Monitor zapewnia podział według następujących kategorii:

- Najważniejsze błędy

    ![Podsumowanie najważniejszych błędów](./media/howto-use-azure-monitor-workbooks/53.png)

- Logowania czekające na akcję użytkownika

    ![Podsumowanie logowań oczekujących na akcję użytkownika](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Następne kroki

[Twórz interaktywne raporty przy użyciu skoroszytów monitorowania](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).
