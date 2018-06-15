---
title: 'Rozwiązywanie problemów: Brak danych w dzienniku aktywności usługi Azure Active Directory | Microsoft Docs'
description: Lista raportów dostępnych w ramach usługi Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 7cbe4337-bb77-4ee0-b254-3e368be06db7
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 01/15/2018
ms.author: rolyon
ms.reviewer: dhanyahk
ms.openlocfilehash: 1568a7c508dbbb42143d8badf39833af90fcc376
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34589546"
---
# <a name="i-cant-find-some-actions-that-i-performed-in-the-azure-active-directory-activity-log"></a>W dzienniku aktywności usługi Azure Active Directory nie można znaleźć niektórych wykonywanych akcji


## <a name="symptoms"></a>Objawy

W witrynie Azure Portal wykonano pewne akcje, które powinny zostać odzwierciedlone w dziennikach inspekcji w bloku `Activity logs > Audit Logs`, ale nie można ich znaleźć.

 ![Raportowanie](./media/active-directory-reporting-troubleshoot-missing-audit-data/01.png)
 

## <a name="cause"></a>Przyczyna

Akcje nie pojawiają się natychmiast w dzienniku inspekcji aktywności. Dzienniki inspekcji pojawiają się w portalu dopiero po pewnym czasie (od 15 minut do godziny) od momentu wykonania operacji.

## <a name="resolution"></a>Rozwiązanie

Poczekaj od 15 minut do godziny i sprawdź, czy akcje pojawią się w dzienniku. Jeśli nadal nie są one wyświetlane, zgłoś do nas bilet pomocy technicznej, abyśmy mogli przeanalizować tę sytuację.


## <a name="next-steps"></a>Następne kroki
Zobacz temat[Azure Active Directory reporting FAQ](active-directory-reporting-faq.md) (Często zadawane pytania dotyczące raportowania usługi Azure Active Directory).

