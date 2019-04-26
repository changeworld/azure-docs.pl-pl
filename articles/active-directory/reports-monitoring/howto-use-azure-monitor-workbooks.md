---
title: Użyj usługi Azure Monitor skoroszyty dla raportów usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak użyć skoroszytów, usługi Azure Monitor dla raportów usługi Azure Active Directory
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
ms.openlocfilehash: 2c9b3d0ef110fea0629af345a71d0d7b7cce7313
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60287322"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Instrukcje: Użyj usługi Azure Monitor skoroszyty dla raportów usługi Azure Active Directory

Czy chcesz:

- Zrozumienie wpływu usługi [zasady dostępu warunkowego](../conditional-access/overview.md) na środowisko logowania użytkowników?

- Rozwiązywanie problemów z błędami w rejestrowania, uzyskanie lepszego widoku kondycji logowania Twojej organizacji, a także szybkie rozwiązywanie problemów?

- Tym, kto używa starszej wersji uwierzytelnienia do logowania się na Twoje środowisko? Przez [blokowanie uwierzytelniania starszych](../conditional-access/block-legacy-authentication.md), może poprawić ochrony Twojej dzierżawy.


[Usługa Azure Monitor skoroszyty](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) łączenie tekstu, zapytania usługi Analytics, metryk usługi Azure i parametrów w zaawansowanych interaktywnych raportów. Usługa Azure Active Directory zapewnia skoroszytów do monitorowania, które ułatwiają znajdowanie odpowiedzi na pytania powyżej.

W tym artykule:

- Przyjęto założenie, że czytelnik zna sposób [tworzenie interaktywnych raportów, skoroszytów w usłudze Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

- Wyjaśnia, jak można używać usługi Azure Monitor skoroszyty informacji na temat monitorowania odpowiedzi na pytania powyżej.
 


## <a name="prerequisites"></a>Wymagania wstępne

Aby używać tej funkcji, potrzebujesz następujących elementów:

- Dzierżawę usługi Azure Active Directory z licencją premium (P1/P2). Dowiedz się, jak [Uzyskaj licencję premium](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium).

- A [obszaru roboczego usługi Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

## <a name="access-workbooks"></a>Dostęp do skoroszytów 

Dostęp do skoroszytów:

1. Zaloguj się do Twojej [witryny Azure portal](https://portal.azure.com).

2. Na lewym pasku nawigacyjnym kliknij **usługi Azure Active Directory**.

3. W **monitorowanie** kliknij **Insights**. 

    ![Insights](./media/howto-use-azure-monitor-workbooks/41.png)

4. Kliknij raport lub szablonu, lub kliknij **Otwórz** na pasku narzędzi. 

    ![Galeria](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>Analiza logowania

Aby uzyskać dostęp do skoroszytu analizy logowania, kliknij przycisk **logowania** w **użycia** sekcji. 

Ten skoroszyt przedstawiono trendy dotyczące następujących logowania:

- Wszystkie logowania

- Powodzenie

- Oczekuje akcja użytkownika

- Niepowodzenie

Można filtrować każdego tendencja według:

- Przedział czasu

- Aplikacje

- Użytkownicy

![Galeria](./media/howto-use-azure-monitor-workbooks/43.png)


Dla każdego trendu można pobrać informacji o podziale:

- Lokalizacja

    ![Galeria](./media/howto-use-azure-monitor-workbooks/45.png)

- Urządzenie

    ![Galeria](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Logowania przy użyciu starszej wersji uwierzytelniania 


Dostęp do operacji logowania za pomocą [starsze uwierzytelnianie](../conditional-access/block-legacy-authentication.md) skoroszytu, kliknij przycisk **logowania przy użyciu starszej wersji uwierzytelniania** w **użycia** sekcji. 

Ten skoroszyt przedstawiono trendy dotyczące następujących logowania:

- Wszystkie logowania

- Powodzenie


Można filtrować każdego tendencja według:

- Przedział czasu

- Aplikacje

- Użytkownicy

- Protokoły 

![Galeria](./media/howto-use-azure-monitor-workbooks/47.png)


Dla każdego trendu uzyskasz podział przez aplikację i protokołu.

![Galeria](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Logowania przez dostęp warunkowy 


Logowania, dostępu do [zasady dostępu warunkowego](../conditional-access/overview.md) skoroszytu, kliknij przycisk **logowania przez dostęp warunkowy** w **dostępu warunkowego** sekcji. 

Ten skoroszyt zawiera trend dla wyłączonego logowania.

Można filtrować każdego tendencja według:

- Przedział czasu

- Aplikacje

- Użytkownicy

![Galeria](./media/howto-use-azure-monitor-workbooks/49.png)


Do logowania wyłączone uzyskasz podział według stanu dostępu warunkowego.

![Stan dostępu warunkowego](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>Logowania przez Udziel kontroli

Logowania, dostępu do [Udziel kontroli](../conditional-access/controls.md) skoroszytu, kliknij przycisk **logowania przez Udziel kontroli** w **dostępu warunkowego** sekcji. 

Ten skoroszyt pokazuje następujących wyłączonych logowania trendów:

- Wymaganie usługi MFA
 
- Wymagane warunki użytkowania

- Wymagane zasady zachowania poufności informacji

- Inne


Można filtrować każdego tendencja według:

- Przedział czasu

- Aplikacje

- Użytkownicy

![Galeria](./media/howto-use-azure-monitor-workbooks/50.png)


Dla każdego trendu uzyskasz podział przez aplikację i protokołu.

![Galeria](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Analiza niepowodzenia logowania

Użyj **analizy błędów logowania** skoroszytu w celu rozwiązywania problemów za pomocą:

- Logowania
- Zasady dostępu warunkowego
- Uwierzytelnianie w starszej wersji. 


Dostęp warunkowy dostęp do danych operacji logowania, kliknij przycisk **logowania przy użyciu starszej wersji uwierzytelniania** w **rozwiązywanie** sekcji. 

Ten skoroszyt przedstawiono trendy dotyczące następujących logowania:

- Wszystkie logowania

- Powodzenie

- Oczekująca akcja

- Niepowodzenie


Można filtrować każdego tendencja według:

- Przedział czasu

- Aplikacje

- Użytkownicy

![Galeria](./media/howto-use-azure-monitor-workbooks/52.png)


Aby rozwiązać problemy logowania, można pobrać informacji o podziale:

- Najważniejsze błędy

    ![Galeria](./media/howto-use-azure-monitor-workbooks/53.png)

- Czeka na akcję użytkownika logowania

    ![Galeria](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie interaktywnych raportów, skoroszytów w usłudze Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)