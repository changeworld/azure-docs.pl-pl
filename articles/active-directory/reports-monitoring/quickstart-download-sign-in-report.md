---
title: Szybki start — pobieranie raportów logowania w witrynie Azure Portal | Microsoft Docs
description: Dowiedz się, jak pobrać raport logowania przy użyciu witryny Azure Portal
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 9131f208-1f90-4cc1-9c29-085cacd69317
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 7b16fb718e689eec8ea016b513d866390b2328e0
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54815565"
---
# <a name="quickstart-download-a-sign-in-report-using-the-azure-portal"></a>Szybki start: pobieranie raportu logowania przy użyciu witryny Azure Portal

W tym przewodniku Szybki start dowiesz się, jak pobrać dane dotyczące logowania dla swojej dzierżawy z ostatnich 24 godzin. Z witryny Azure Portal można pobrać maksymalnie 5000 rekordów. Rekordy są sortowane w kolejności od najnowszego, więc domyślnie otrzymujesz 5000 najnowszych rekordów. 

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne elementy:

* Dzierżawa usługi Azure Active Directory z licencją Premium, dla której ma być wyświetlony raport aktywności związanej z logowaniem. Aby uaktualnić swoją wersję usługi Azure Active Directory, zobacz [Wprowadzenie do usługi Azure Active Directory w wersji Premium](../fundamentals/active-directory-get-started-premium.md). Pamiętaj, że jeśli przed uaktualnieniem nie istniały żadne dane działań, po przejściu na licencję premium może minąć kilka dni, zanim te dane pojawią się w raportach.
* Użytkownik mający rolę **administratora zabezpieczeń**, **czytelnika zabezpieczeń**, **czytelnika raportów** lub **administratora globalnego** dla dzierżawy. Ponadto każdy użytkownik w dzierżawie może uzyskiwać dostęp do informacji o swoim logowaniu.

## <a name="quickstart-download-a-sign-in-report"></a>Szybki start: Pobieranie raportu logowań

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz usługę **Azure Active Directory** z okienka nawigacji po lewej stronie i za pomocą przycisku **Przełącz katalog** wybierz swój katalog Active Directory.
3. Na pulpicie nawigacyjnym wybierz pozycję **Azure Active Directory**, a następnie **Logowania**. 
4. Wybierz pozycję **Ostatnie 24 godziny** z listy rozwijanej filtru **Data** i wybierz przycisk **Zastosuj**, aby wyświetlić logowania z ostatnich 24 godzin. 
5. Wybierz przycisk **Pobierz**, aby pobrać plik CSV zawierający odfiltrowane rekordy. 

![Raportowanie](./media/quickstart-download-sign-in-report/download-sign-ins.png)

## <a name="next-steps"></a>Następne kroki

* [Raporty dotyczące logowań w portalu Azure Active Directory](concept-sign-ins.md)
* [Azure Active Directory reporting retention (Przechowywanie raportów w usłudze Azure Active Directory)](reference-reports-data-retention.md)
* [Azure Active Directory reporting latencies](reference-reports-latencies.md) (Opóźnienia raportowania w usłudze Azure Active Directory)
