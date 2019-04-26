---
title: Szybki start — pobieranie raportów logowania w witrynie Azure Portal | Microsoft Docs
description: Dowiedz się, jak pobrać raport logowania przy użyciu witryny Azure Portal
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 9131f208-1f90-4cc1-9c29-085cacd69317
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
ms.openlocfilehash: 86bc72f69903134afa3750ad6b72486a713b6cc0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60285878"
---
# <a name="quickstart-download-a-sign-in-report-using-the-azure-portal"></a>Szybki start: pobieranie raportu logowania przy użyciu witryny Azure Portal

W tym przewodniku Szybki start dowiesz się, jak pobrać dane dotyczące logowania dla swojej dzierżawy z ostatnich 24 godzin. Do 250 000 rekordów można pobrać z witryny Azure portal. Rekordy są sortowane według większość ostatnie, więc domyślnie otrzymujesz najnowszej 250 000 rekordów. 

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne elementy:

* Dzierżawa usługi Azure Active Directory z licencją Premium, dla której ma być wyświetlony raport aktywności związanej z logowaniem. Aby uaktualnić swoją wersję usługi Azure Active Directory, zobacz [Wprowadzenie do usługi Azure Active Directory w wersji Premium](../fundamentals/active-directory-get-started-premium.md). Pamiętaj, że jeśli przed uaktualnieniem nie istniały żadne dane działań, po przejściu na licencję premium może minąć kilka dni, zanim te dane pojawią się w raportach.
* Użytkownik mający rolę **administratora zabezpieczeń**, **czytelnika zabezpieczeń**, **czytelnika raportów** lub **administratora globalnego** dla dzierżawy. Ponadto każdy użytkownik w dzierżawie może uzyskiwać dostęp do informacji o swoim logowaniu.

## <a name="quickstart-download-a-sign-in-report"></a>Szybki start: Pobieranie raportu logowań

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz usługę **Azure Active Directory** z okienka nawigacji po lewej stronie i za pomocą przycisku **Przełącz katalog** wybierz swój katalog Active Directory.
3. Na pulpicie nawigacyjnym wybierz pozycję **Azure Active Directory**, a następnie **Logowania**. 
4. Wybierz pozycję **Ostatnie 24 godziny** z listy rozwijanej filtru **Data** i wybierz przycisk **Zastosuj**, aby wyświetlić logowania z ostatnich 24 godzin. 
5. Wybierz **Pobierz** przycisku Wybierz **CSV** jako plik formatu i określ nazwę pliku do pobrania plik CSV zawierający przefiltrowane rekordy. 

![Raportowanie](./media/quickstart-download-sign-in-report/download-sign-ins.png)

## <a name="next-steps"></a>Kolejne kroki

* [Raporty dotyczące logowań w portalu Azure Active Directory](concept-sign-ins.md)
* [Azure Active Directory reporting retention (Przechowywanie raportów w usłudze Azure Active Directory)](reference-reports-data-retention.md)
* [Azure Active Directory reporting latencies](reference-reports-latencies.md) (Opóźnienia raportowania w usłudze Azure Active Directory)
