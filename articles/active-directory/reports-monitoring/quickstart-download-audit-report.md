---
title: Szybki start — pobieranie raportów z inspekcji w witrynie Azure Portal | Microsoft Docs
description: Dowiedz się, jak pobrać raport z inspekcji przy użyciu witryny Azure Portal
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4de121ea-f4aa-4c8a-aae4-700c2c5e97a2
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 09/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d6ecf62b46c25ec7cf4ab80219d1bc220e4eb4cc
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46367410"
---
# <a name="quickstart-download-an-audit-report-using-the-azure-portal"></a>Szybki start: pobieranie raportu z inspekcji przy użyciu witryny Azure Portal

W tym przewodniku Szybki start dowiesz się, jak pobrać dzienniki inspekcji dla swojej dzierżawy z ostatnich 24 godzin.

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne elementy:

* Dzierżawa usługi Azure Active Directory. 
* Użytkownik mający rolę administratora zabezpieczeń, czytelnika zabezpieczeń lub administratora globalnego dla dzierżawy. Ponadto każdy użytkownik w dzierżawie może uzyskiwać dostęp do własnych dzienników inspekcji.

## <a name="quickstart-download-an-audit-report"></a>Szybki start: pobieranie raportu z inspekcji

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz usługę **Azure Active Directory** z okienka nawigacji po lewej stronie i za pomocą przycisku **Przełącz katalog** wybierz swój katalog Active Directory.
3. Na pulpicie nawigacyjnym wybierz pozycję **Azure Active Directory**, a następnie **Dzienniki inspekcji**. 
4. Wybierz pozycję **Ostatnie 24 godziny** z listy rozwijanej filtru **Zakres dat** i wybierz przycisk **Zastosuj**, aby wyświetlić dzienniki inspekcji z ostatnich 24 godzin. 
5. Wybierz przycisk **Pobierz**, aby pobrać plik CSV zawierający odfiltrowane rekordy. 

![Raportowanie](./media/quickstart-download-audit-report/download-audit-logs.png)

## <a name="next-steps"></a>Następne kroki

* [Raporty dotyczące logowań w portalu Azure Active Directory](concept-sign-ins.md)
* [Azure Active Directory reporting retention (Przechowywanie raportów w usłudze Azure Active Directory)](reference-reports-data-retention.md)
* [Azure Active Directory reporting latencies](reference-reports-latencies.md) (Opóźnienia raportowania w usłudze Azure Active Directory)
