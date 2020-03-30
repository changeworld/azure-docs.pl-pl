---
title: Raport użycia i spostrzeżeń | Dokumenty firmy Microsoft
description: Wprowadzenie do raportu użycia i szczegółowych informacji w portalu usługi Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 3fba300d-18fc-4355-9924-d8662f563a1f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: b3db86137207ae726c7befc393f62590fd1456d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74008261"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Raport użycia i szczegółowych informacji w portalu usługi Azure Active Directory

Dzięki raportowi użycia i szczegółowych informacji możesz uzyskać zorientowany na aplikację widok danych logowania. Odpowiedzi na następujące pytania:

*   Jakie są najlepsze używane aplikacje w mojej organizacji?
*   Jakie aplikacje mają najbardziej nieudane logowania? 
*   Jakie są najważniejsze błędy logowania dla każdej aplikacji?

## <a name="prerequisites"></a>Wymagania wstępne 

Aby uzyskać dostęp do danych z raportu użycia i analizy, potrzebujesz:

* Dzierżawa usługi Azure AD
* Licencja premium usługi Azure AD (P1/P2) do wyświetlania danych logowania
* Użytkownik w rolach administratora globalnego, administratora zabezpieczeń, czytnika zabezpieczeń lub czytnika raportów. Ponadto każdy użytkownik (niebędący administratorami) może uzyskać dostęp do własnych logów. 

## <a name="access-the-usage-and-insights-report"></a>Dostęp do raportu użycia i szczegółowych informacji

1. Przejdź do [witryny Azure portal](https://portal.azure.com).
2. Wybierz odpowiedni katalog, a następnie wybierz **pozycję Azure Active Directory** i wybierz pozycję **Aplikacje przedsiębiorstwa**.
3. W sekcji **Działanie** wybierz **pozycję Użycie & szczegółowych informacji,** aby otworzyć raport. 

![Raport użycia i szczegółowych informacji](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>Korzystanie z raportu

Raport użycia i szczegółowych informacji zawiera listę aplikacji z co najmniej jedną próbą logowania i umożliwia sortowanie według liczby pomyślnych logowania, nieudanych logów i wskaźnika powodzenia.

Kliknięcie przycisku Wczytaj więcej u dołu listy umożliwia wyświetlanie dodatkowych aplikacji na stronie. Można wybrać zakres dat, aby wyświetlić wszystkie aplikacje, które zostały użyte w zakresie.

Można również ustawić fokus na określonej aplikacji. Wybierz **działanie logowania widoku,** aby zobaczyć działanie logowania w czasie dla aplikacji, a także najważniejsze błędy.  

Po wybraniu dnia na wykresie użycia aplikacji, otrzymasz szczegółową listę działań logowania dla aplikacji.  

![Raport użycia i szczegółowych informacji](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>Następne kroki

* [Raport dotyczący logowań](concept-sign-ins.md)