---
title: Rozwiązywanie problemów z działanie usługi Azure Active Directory błędy pakietu zawartości dziennika | Dokumentacja firmy Microsoft
description: Zawiera listę komunikatów o błędach pakietu zawartości działanie usługi Azure Active Directory oraz kroki rozwiązywania tych problemów.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 06/07/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ee49ae56122fe596a4490914677d91d2f0348f6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66807525"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Rozwiązywanie problemów z działanie usługi Azure Active Directory rejestruje błędy pakietu zawartości 

|  |
|--|
|Obecnie pakiet zawartości usługi Power BI dla usługi Azure AD używa interfejsów API programu Graph usługi Azure AD do pobierania danych z dzierżawy usługi Azure AD. W rezultacie mogą występować różnice między danymi dostępnymi w pakiecie zawartości i danymi pobranymi przy użyciu [interfejsów API programu Microsoft Graph do raportowania](concept-reporting-api.md). |
|  |

Podczas pracy z pakietu zawartości usługi Power BI dla usługi Azure Active Directory (Azure AD), możliwe jest wystąpiły następujące błędy: 

- [Odświeżanie nie powiodło się](troubleshoot-content-pack.md#refresh-failed) 
- [Nie można zaktualizować poświadczeń źródła danych](troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [Importowanie danych trwa zbyt długo](#data-import-is-too-slow) 

Ten artykuł zawiera informacje na temat możliwych przyczyn i jak naprawić te błędy.
 
## <a name="refresh-failed"></a>Odświeżanie nie powiodło się 
 
**Jak ten błąd jest udostępniane**: Wyślij wiadomość e-mail z usługi Power BI lub stan niepowodzenia w historii odświeżania. 


| Przyczyna | Jak naprawić |
| ---   | ---        |
| Odśwież błędu, który może być spowodowane błędy, jeśli poświadczenia użytkowników, nawiązywania połączenia z pakietem zawartości zostały resetowania, ale nie są aktualizowane w ustawieniach połączenia pakietu zawartości. | W usłudze Power BI, Znajdź zestaw danych, odpowiadający pulpitu nawigacyjnego dzienników aktywności usługi Azure AD (**rejestruje działanie usługi Azure Active Directory**), wybierz harmonogram odświeżania, a następnie wprowadź swoje poświadczenia usługi Azure AD. |
| Odświeżanie może zakończyć się niepowodzeniem z powodu dużych zestawów danych. | Obecnie platforma Azure AD zawartości pakietu z usługą Power BI może obsługiwać tylko niewielkich zestawów danych (mniej niż 500,00 wierszy) ze względu na pewne ograniczenia dotyczące limitów czasu w usłudze Power BI. Jeśli wystąpią błędy ograniczania przepływności lub odświeżanie zakończy się niepowodzeniem z powodu przekroczenia limitu czasu problemów, to może podejmowana próba pobrania duży zestaw danych. Skróć okres czasu, w zapytaniu i spróbuj ponownie.|
 
 
## <a name="failed-to-update-data-source-credentials"></a>Nie można zaktualizować poświadczeń źródła danych 
 
**Jak ten błąd jest udostępniane**: W usłudze Power BI, po nawiązaniu połączenia z pakietem zawartości dzienników aktywności usługi Azure AD. 

| Przyczyna | Jak naprawić |
| ---   | ---        |
| Użytkownik nawiązujący połączenie nie jest administratorem globalnym lub Czytelnik zabezpieczeń lub administrator zabezpieczeń. | Użyj konta administratora globalnego lub Czytelnik zabezpieczeń lub administrator zabezpieczeń do dostępu do pakietów zawartości. |
| Dzierżawy nie jest dzierżawą usługi Premium lub nie ma co najmniej jeden użytkownik mający licencję Premium pliku. | [Bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 


## <a name="data-import-is-too-slow"></a>Importowanie danych jest zbyt wolno 
 
**Jak ten błąd jest udostępniane**: W usłudze Power BI po połączeniu z pakietem zawartości, proces importowania danych rozpoczyna się przygotować pulpitu nawigacyjnego do działania usługi Azure AD dzienniki. Zostanie wyświetlony komunikat: **Importowanie danych...**  bez żadnych dalszych postępów.  

| Przyczyna | Jak naprawić |
| ---   | ---        |
| W zależności od rozmiaru dzierżawy usługi ten krok może potrwać od kilku minut do 30 minut. | Jeśli komunikat nie zmienia się na wyświetlanie pulpitu nawigacyjnego w ciągu godziny, [bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>Kolejne kroki

* [Zainstaluj pakiet zawartości usługi Power BI dla raportów usługi Azure AD](quickstart-install-power-bi-content-pack.md).
* [Użyj zawartości pakietu dla usługi Azure AD raportów umożliwia wizualizowanie danych usługi Power BI](howto-power-bi-content-pack.md)
* [Jak uzyskać pomoc techniczną dla usługi Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md)
