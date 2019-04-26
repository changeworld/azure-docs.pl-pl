---
title: Szybki start — pobieranie raportów z inspekcji w witrynie Azure Portal | Microsoft Docs
description: Dowiedz się, jak pobrać raport z inspekcji przy użyciu witryny Azure Portal
services: active-directory
documentationcenter: ''
author: MarkusVi
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
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6cbea49fe39c92c8a2fc50e501cb4ef5cff74b1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60286450"
---
# <a name="quickstart-download-an-audit-report-using-the-azure-portal"></a>Szybki start: pobieranie raportu z inspekcji przy użyciu witryny Azure Portal

W tym przewodniku Szybki Start dowiesz się, jak pobrać plik CSV w dziennikach inspekcji dla dzierżawy w ciągu ostatnich 24 godzin. Do 250 000 rekordów można pobrać z witryny Azure portal. Rekordy są sortowane według większość ostatnie, więc domyślnie otrzymujesz najnowszej 250 000 rekordów. 

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne elementy:

* Dzierżawa usługi Azure Active Directory. 
* Użytkownik mający rolę **administratora zabezpieczeń**, **czytelnika zabezpieczeń** lub **administratora globalnego** dla dzierżawy. Ponadto każdy użytkownik w dzierżawie może uzyskiwać dostęp do własnych dzienników inspekcji.

## <a name="quickstart-download-an-audit-report"></a>Szybki start: Pobieranie raportu inspekcji

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz usługę **Azure Active Directory** z okienka nawigacji po lewej stronie i za pomocą przycisku **Przełącz katalog** wybierz swój katalog Active Directory.
3. Na pulpicie nawigacyjnym wybierz pozycję **Azure Active Directory**, a następnie **Dzienniki inspekcji**. 
4. Wybierz pozycję **Ostatnie 24 godziny** z listy rozwijanej filtru **Zakres dat** i wybierz przycisk **Zastosuj**, aby wyświetlić dzienniki inspekcji z ostatnich 24 godzin. 
5. Wybierz **Pobierz** przycisku Wybierz **CSV** jako plik formatu i określ nazwę pliku do pobrania plik CSV zawierający przefiltrowane rekordy. 

![Raportowanie](./media/quickstart-download-audit-report/download-audit-logs.png)

## <a name="next-steps"></a>Kolejne kroki

* [Raporty dotyczące logowań w portalu Azure Active Directory](concept-sign-ins.md)
* [Azure Active Directory reporting retention (Przechowywanie raportów w usłudze Azure Active Directory)](reference-reports-data-retention.md)
* [Azure Active Directory reporting latencies](reference-reports-latencies.md) (Opóźnienia raportowania w usłudze Azure Active Directory)
