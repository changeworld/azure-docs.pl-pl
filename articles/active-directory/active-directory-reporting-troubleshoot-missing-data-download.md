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
ms.component: compliance-reports
ms.date: 01/15/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d0638404ec6f5b6d13aa207ef54913c1bd3ecc1a
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232503"
---
# <a name="i-cant-find-any-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Nie można znaleźć żadnych danych w dziennikach działania usługi Azure Active Directory pobrany


## <a name="symptoms"></a>Objawy

Pobrano dzienniki aktywności (inspekcji lub logowania), ale nie widać wszystkich rekordów dla wybranego czasu. Dlaczego? 

 ![Raportowanie](./media/active-directory-reporting-troubleshoot-missing-data-download/01.png)
 

## <a name="cause"></a>Przyczyna

Podczas pobierania dzienników aktywności w portalu Azure, jest ograniczona skali do 5000 rekordy posortowane według ostatniego. 

## <a name="resolution"></a>Rozwiązanie

Można wykorzystać [interfejsy API raportowania usługi Azure AD](active-directory-reporting-api-getting-started.md), aby pobrać do miliona rekordów z dowolnego okresu. Nasze podejście zalecane jest uruchomienie skryptu zgodnie z harmonogramem, który wywołuje raportowania interfejsów API, aby pobrać rekordy w sposób przyrostowy w danym okresie czasu (na przykład codziennie lub co tydzień).

## <a name="next-steps"></a>Kolejne kroki
Zobacz temat[Azure Active Directory reporting FAQ](active-directory-reporting-faq.md) (Często zadawane pytania dotyczące raportowania usługi Azure Active Directory).

