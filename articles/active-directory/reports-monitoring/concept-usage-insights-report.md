---
title: Raport użycia i szczegółowych informacji w portalu Azure Active Directory | Dokumentacja firmy Microsoft
description: Wprowadzenie do raportów użycia i szczegółowych informacji w portalu Azure Active Directory
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
ms.openlocfilehash: 3fe1e72d277bffd4bc9b38ac377e33b341967e17
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65806359"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Raport użycia i szczegółowych informacji w portalu Azure Active Directory

Raport użycia i szczegółowe informacje można uzyskać widokowi skoncentrowanemu na aplikacji, dane logowania. Możesz znaleźć odpowiedzi na następujące pytania:

*   Co to są górnej używane aplikacje w mojej organizacji?
*   Jakie aplikacje mają najbardziej Nieudane logowania? 
*   Jakie są najważniejsze błędy logowania dla każdej aplikacji?

## <a name="prerequisites"></a>Wymagania wstępne 

Aby uzyskać dostęp do danych o użyciu i raportów, potrzebne są:

* Dzierżawa usługi Azure AD
* Licencja premium (P1/P2) usługi Azure AD, aby wyświetlić dane logowania
* Użytkownik w administratora globalnego, administratora zabezpieczeń, Czytelnik zabezpieczeń lub ról czytnika raportu. Ponadto wszyscy użytkownicy (inni niż administratorzy) mogą korzystać własnych logowań. 

## <a name="access-the-usage-and-insights-report"></a>Dostęp do raportów użycia i szczegółowych informacji

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz katalog, w prawo, a następnie wybierz **usługi Azure Active Directory** i wybierz polecenie **aplikacje dla przedsiębiorstw**.
3. Z **działania** zaznacz **użycia & insights** aby otworzyć raport. 

![Raport użycia i szczegółowych informacji](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>Użyj raportu

Raport użycia i szczegółowych informacji z listą aplikacji za pomocą co najmniej jeden znak podczas próby i umożliwia sortowanie według liczby pomyślnych logowań, Nieudane logowania i Częstotliwość powodzeń.

Kliknięcie przycisku obciążenia więcej w dolnej części listy umożliwia wyświetlenie dodatkowych aplikacji na stronie. Można wybrać zakres dat, aby wyświetlić wszystkie aplikacje, które zostały użyte w zakresie.

Można również ustawić fokus na konkretnej aplikacji. Wybierz **wyświetlanie aktywności logowania** się znak w działaniu wraz z upływem czasu dla aplikacji, a także najważniejsze błędy.  

Po wybraniu dnia na wykresie użycia aplikacji, możesz uzyskać szczegółową listę działań logowania w aplikacji.  

![Raport użycia i szczegółowych informacji](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>Kolejne kroki

* [Raport logowań](concept-sign-ins.md)