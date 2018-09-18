---
title: Usługa Azure Active Directory reporting opóźnienia | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o ilość czasu, jaki zajmuje zdarzeń do raportowania do witryny Azure portal
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 12/15/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: b81c66acc0a90ba9b74cf1f4fb34ef7a545837f9
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45736610"
---
# <a name="azure-active-directory-reporting-latencies"></a>Usługa Azure Active Directory reporting opóźnienia

Za pomocą [raportowania](../active-directory-preview-explainer.md) w usłudze Azure Active Directory, otrzymasz wszystkie informacje, należy ustalić środowiska. Ilość czasu potrzebnego do raportowania danych pojawienie się w witrynie Azure portal jest nazywany także czas oczekiwania. 

Ten temat zawiera informacje o czas oczekiwania dla wszystkich raportowania kategorie w witrynie Azure portal. 


## <a name="activity-reports"></a>Raporty dotyczące działań

Istnieją dwa obszary raportowania działania:

- **Działania związane z logowaniem** — informacje na temat użycia zarządzanych aplikacji i działania użytkownika związane z logowaniem
- **Dzienniki inspekcji** — informacje o aktywności systemu obejmujące zarządzanie użytkownikami i grupami oraz zarządzane aplikacje i działania dotyczące katalogu

W poniższej tabeli wymieniono opóźnienie dotycząca raporty aktywności.

| Raport | Czas oczekiwania (95%) |Opóźnienie (99%)|
| :-- | --- | --- | 
| Dzienniki inspekcji | 2 min  | 5 min  |
| Logowania | 2 min  | 5 min |


## <a name="security-reports"></a>Raporty dotyczące zabezpieczeń

Istnieją dwa obszary raporty o zabezpieczeniach:

- **Ryzykowne logowania** — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika. 
- **Użytkownicy oflagowani w związku z ryzykiem** — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone. 

W poniższej tabeli wymieniono opóźnienie dotycząca raporty dotyczące zabezpieczeń.

| Raport | Minimalne | Średnia | Maksimum |
| :-- | --- | --- | --- |
| Narażeni użytkownicy          | 5 minut   | 15 minut  | 2 godziny  |
| Ryzykowne logowania         | 5 minut   | 15 minut  | 2 godziny  |

## <a name="risk-events"></a>Zdarzenia o podwyższonym ryzyku

Usługa Azure Active Directory korzysta z algorytmów uczenia maszynowego adaptacyjne i algorytmy heurystyczne wykryć podejrzane akcje, które są powiązane z kontami użytkowników. Każdy wykryty podejrzanych działań są przechowywane w zdarzeniu wywoływanej ryzyko rekordu.

Poniższa tabela zawiera informacje opóźnienie dla zdarzeń o podwyższonym ryzyku.

| Raport | Minimalne | Średnia | Maksimum |
| :-- | --- | --- | --- |
| Logowania z anonimowych adresów IP |5 minut |15 minut |2 godziny |
| Logowania z nieznanych lokalizacji |5 minut |15 minut |2 godziny |
| Użytkownicy z ujawnionymi poświadczeniami |2 godziny |4 godziny |8 godzin |
| Niemożliwa podróż do nietypowych lokalizacji |5 minut |1 godzina |8 godzin  |
| Logowania z zainfekowanych urządzeń |2 godziny |4 godziny |8 godzin  |
| Logowania z adresów IP związanych z podejrzanymi działaniami |2 godziny |4 godziny |8 godzin  |



## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz dowiedzieć się więcej o raporty aktywności w witrynie Azure portal, zobacz:

- [Raporty dotyczące logowań w portalu usługi Azure Active Directory](concept-sign-ins.md)
- [Raporty dotyczące inspekcji w portalu Azure Active Directory](concept-audit-logs.md)

Jeśli chcesz dowiedzieć się więcej o raportom o zabezpieczeniach w witrynie Azure portal, zobacz:

- [Raport zabezpieczeń dotyczący narażonych w portalu Azure Active Directory użytkowników](concept-user-at-risk.md)
- [Raport dotyczący ryzykownych logowań w portalu usługi Azure Active Directory](concept-risky-sign-ins.md)

Jeśli chcesz dowiedzieć się więcej na temat zdarzeń o podwyższonym ryzyku, zobacz [zdarzeń o podwyższonym ryzyku Azure Active Directory](concept-risk-events.md).
