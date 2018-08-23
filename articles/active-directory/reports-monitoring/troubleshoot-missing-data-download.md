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
ms.date: 01/15/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 9138da42eeb87e45b86be10aff67792ee6de09b4
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2018
ms.locfileid: "42055409"
---
# <a name="i-cant-find-any-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Nie można znaleźć żadnych danych w dziennikach aktywności usługi Azure Active Directory, pobrany


## <a name="symptoms"></a>Objawy

Pobrano dzienniki aktywności (inspekcji lub logowania), ale nie widać wszystkich rekordów dla wybranego czasu. Dlaczego? 

 ![Raportowanie](./media/troubleshoot-missing-data-download/01.png)
 

## <a name="cause"></a>Przyczyna

Podczas pobierania dzienników aktywności w witrynie Azure portal ograniczamy skalowanie do 5000 rekordów posortowanych według najnowsze na początku. 

## <a name="resolution"></a>Rozwiązanie

Można wykorzystać [interfejsy API raportowania usługi Azure AD](concept-reporting-api.md), aby pobrać do miliona rekordów z dowolnego okresu. Naszym Zalecanym podejściem jest do uruchomienia skryptu, zgodnie z harmonogramem, który wywołuje interfejsy API raportowania w celu pobrania rekordów w sposób przyrostowy okresie (na przykład codziennie lub co tydzień).

## <a name="next-steps"></a>Kolejne kroki
Zobacz temat[Azure Active Directory reporting FAQ](reports-faq.md) (Często zadawane pytania dotyczące raportowania usługi Azure Active Directory).

