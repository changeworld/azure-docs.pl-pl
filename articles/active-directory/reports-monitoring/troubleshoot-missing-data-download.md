---
title: 'Rozwiązywanie problemów: Brak danych w pobranych dziennikach aktywności | Microsoft Docs'
description: Zawiera rozwiązanie problemu dotyczącego braku danych w pobranych dziennikach aktywności usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: db96bd99a25ca7803dbdfe412e2cddbbcb57c0e4
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72817523"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Nie mogę znaleźć wszystkich danych w pobranych dziennikach aktywności Azure Active Directory

## <a name="symptoms"></a>Objawy

Pobrano dzienniki aktywności (inspekcji lub logowania), ale nie widać wszystkich rekordów dla wybranego czasu. Dlaczego? 

 ![Raportowanie](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>Przyczyna

Po pobraniu dzienników aktywności w Azure Portal ograniczamy do 250 000 rekordów, posortowanych według ostatnio używanych elementów. 

## <a name="resolution"></a>Rozdzielczość

Można wykorzystać [interfejsy API raportowania usługi Azure AD](concept-reporting-api.md), aby pobrać do miliona rekordów z dowolnego okresu.

## <a name="next-steps"></a>Następne kroki

* [Raporty Azure Active Directory często zadawane pytania](reports-faq.md)

