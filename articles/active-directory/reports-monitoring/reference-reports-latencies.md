---
title: Usługa Azure Active Directory reporting opóźnienia | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o ilość czasu, jaki zajmuje zdarzeń do raportowania do witryny Azure portal
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d4d02f9e99acb68db126ccde465f73cc2294e06
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65594019"
---
# <a name="azure-active-directory-reporting-latencies"></a>Usługa Azure Active Directory reporting opóźnienia

Opóźnienie to ilość czasu dla usługi Azure Active Directory (Azure AD) raportowania danych pojawiają się w [witryny Azure portal](https://portal.azure.com). W tym artykule wymieniono oczekiwane opóźnienie dla różnych typów raportów. 

## <a name="activity-reports"></a>Raporty dotyczące działań

Istnieją dwa typy raportów dotyczących działań:

- [Operacje logowania](concept-sign-ins.md) — zawiera informacje na temat użycia zarządzanych aplikacji i użytkownika działań logowania
- [Dzienniki inspekcji](concept-audit-logs.md) — zawiera informacje o użytkownikach i grupach, zarządzanych aplikacji oraz działania dotyczące katalogu aktywności systemu

W poniższej tabeli wymieniono opóźnienie dotycząca raporty aktywności. 

> [!NOTE]
> **Czas oczekiwania (95. percentyl)** odnosi się do czasu za pomocą którego 95% dzienników jest informowany, i **opóźnienie (99. percentyl)** odnosi się do czasu raportowane 99% dzienniki. 
>

| Raport | Czas oczekiwania (95. percentyl) |Opóźnienie (99. percentyl)|
| :-- | --- | --- |
| Dzienniki inspekcji | 2 min  | 5 min  |
| Logowania | 2 min  | 5 min |

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Termin może wyświetlać danych działania po otrzymaniu licencji premium?

Jeśli masz już danych dotyczących działań związanych z bezpłatnej licencji, a następnie natychmiast go zobaczyć, w czasie uaktualniania. Jeśli nie masz żadnych danych, następnie potrwa jeden lub dwa dni, dane wyświetlane w raportach, po uaktualnieniu do licencji premium.

## <a name="security-reports"></a>Raporty dotyczące zabezpieczeń

Istnieją dwa typy raportów dotyczących zabezpieczeń:

- [Ryzykowne logowania](concept-risky-sign-ins.md) — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika. 
- [Użytkownicy oflagowani w związku z ryzykiem](concept-user-at-risk.md) — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone. 

W poniższej tabeli wymieniono opóźnienie dotycząca raporty dotyczące zabezpieczeń.

| Raport | Minimalne | Średnia | Maksimum |
| :-- | --- | --- | --- |
| Narażeni użytkownicy          | 5 minut   | 15 minut  | 2 godziny  |
| Ryzykowne logowania         | 5 minut   | 15 minut  | 2 godziny  |

## <a name="risk-events"></a>Zdarzenia o podwyższonym ryzyku

Usługa Azure AD używa adaptacyjnych algorytmów uczenia maszynowego i algorytmy heurystyczne wykryć podejrzane akcje, które są powiązane z kontami użytkowników. Każdej wykrytej podejrzanych działań są przechowywane w rekord nazywany **zdarzenie o podwyższonym ryzyku**.

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

* [Omówienie raportów w usłudze Azure AD](overview-reports.md)
* [Programowy dostęp do raportów usługi Azure AD](concept-reporting-api.md)
* [Zdarzenia o podwyższonym ryzyku usługi Azure Active Directory](concept-risk-events.md)
