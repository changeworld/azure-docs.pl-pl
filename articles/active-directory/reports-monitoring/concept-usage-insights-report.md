---
title: Raport dotyczący użycia i szczegółowych informacji | Microsoft Docs
description: Wprowadzenie do raportu dotyczącego użycia i szczegółowych informacji w portalu Azure Active Directory
services: active-directory
documentationcenter: ''
author: cawrites
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
ms.author: chadam
ms.reviewer: dhanyahk
ms.openlocfilehash: 568eaa358f6788af525a69cd8ecda76a0591879f
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820941"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Raport dotyczący użycia i szczegółowych informacji w portalu Azure Active Directory

Korzystając z raportu Użycie i szczegółowe informacje, można uzyskać widok danych logowania skoncentrowany na aplikacjach. Odpowiedzi można znaleźć na następujących pytaniach:

*   Jakie są najczęściej używane aplikacje w mojej organizacji?
*   Jakie aplikacje mają najwięcej nieudanych logowań? 
*   Jakie są najważniejsze błędy logowania dla każdej aplikacji?

## <a name="prerequisites"></a>Wymagania wstępne 

Aby uzyskać dostęp do danych z raportu Użycie i szczegółowe informacje, potrzebne są:

* Dzierżawa usługi Azure AD
* Licencja usługi Azure AD Premium (P1/P2) do wyświetlania danych logowania
* Użytkownik w roli administratora globalnego, administratora zabezpieczeń, czytnika zabezpieczeń lub czytelnika raportu. Ponadto wszyscy użytkownicy (inni niż administratorzy) mogą uzyskać dostęp do swoich logowań. 

## <a name="access-the-usage-and-insights-report"></a>Dostęp do raportu dotyczącego użycia i szczegółowych informacji

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz odpowiedni katalog, a następnie wybierz pozycję **Azure Active Directory** i wybierz pozycję **aplikacje dla przedsiębiorstw**.
3. W sekcji **działanie** wybierz pozycję **użycie & szczegółowych** informacji, aby otworzyć raport. 

![Raport użycia i szczegółowych informacji](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>Korzystanie z raportu

Raport użycie i szczegółowe informacje przedstawia listę aplikacji z co najmniej jedną próbą logowania, a także umożliwia sortowanie według liczby pomyślnych logowań, nieudanych logowań i częstotliwości powodzeń.

Kliknięcie przycisku Załaduj więcej u dołu listy umożliwia wyświetlenie dodatkowych aplikacji na stronie. Możesz wybrać zakres dat, aby wyświetlić wszystkie aplikacje, które zostały użyte w zakresie.

Możesz również ustawić fokus dla określonej aplikacji. Wybierz pozycję **Wyświetl działanie związane z logowaniem** , aby zobaczyć działanie logowania w czasie dla aplikacji, a także najważniejszych błędów.  

Po wybraniu dnia na wykresie użycia aplikacji zostanie wykorzystana Szczegółowa lista działań związanych z logowaniem dla aplikacji.  

![Raport użycia i szczegółowych informacji](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>Następne kroki

* [Raport logowania](concept-sign-ins.md)