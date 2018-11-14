---
title: 'Rozwiązywanie problemów: Brak danych w pobranych dziennikach aktywności usługi Azure Active Directory | Microsoft Docs'
description: Zawiera rozwiązanie problemu dotyczącego braku danych w pobranych dziennikach aktywności usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 2607c5dacf6f261f27e7805e02df189a2753404c
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51625656"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Nie mogę znaleźć wszystkie dane w dziennikach aktywności usługi Azure Active Directory, pobrany

## <a name="symptoms"></a>Objawy

Pobrano dzienniki aktywności (inspekcji lub logowania), ale nie widać wszystkich rekordów dla wybranego czasu. Dlaczego? 

 ![Raportowanie](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>Przyczyna

Podczas pobierania dzienników aktywności w witrynie Azure portal ograniczamy skalowanie do 5000 rekordów posortowanych według najnowsze na początku. 

## <a name="resolution"></a>Rozwiązanie

Można wykorzystać [interfejsy API raportowania usługi Azure AD](concept-reporting-api.md), aby pobrać do miliona rekordów z dowolnego okresu. Naszym Zalecanym podejściem jest [za pomocą skryptu zgodnie z harmonogramem](tutorial-signin-logs-download-script.md) , wywołuje interfejsy API raportowania w celu pobrania rekordów w sposób przyrostowy okresie (na przykład codziennie lub co tydzień). 

## <a name="next-steps"></a>Kolejne kroki

* [Usługa Azure Active Directory raporty — często zadawane pytania](reports-faq.md)

