---
title: Szybki start — pobieranie raportów z inspekcji w witrynie Azure Portal | Microsoft Docs
description: Dowiedz się, jak pobrać raport z inspekcji przy użyciu witryny Azure Portal
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 4de121ea-f4aa-4c8a-aae4-700c2c5e97a2
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d702810ae7de03a14e50f56c2b8e819c31f3ea9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56192714"
---
# <a name="quickstart-download-an-audit-report-using-the-azure-portal"></a>Szybki start: pobieranie raportu z inspekcji przy użyciu witryny Azure Portal

W tym przewodniku Szybki start dowiesz się, jak pobrać dzienniki inspekcji dla swojej dzierżawy z ostatnich 24 godzin. Z witryny Azure Portal można pobrać maksymalnie 5000 rekordów. Rekordy są sortowane w kolejności od najnowszego, więc domyślnie otrzymujesz 5000 najnowszych rekordów. 

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne elementy:

* Dzierżawa usługi Azure Active Directory. 
* Użytkownik mający rolę **administratora zabezpieczeń**, **czytelnika zabezpieczeń** lub **administratora globalnego** dla dzierżawy. Ponadto każdy użytkownik w dzierżawie może uzyskiwać dostęp do własnych dzienników inspekcji.

## <a name="quickstart-download-an-audit-report"></a>Szybki start: Pobieranie raportu inspekcji

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
