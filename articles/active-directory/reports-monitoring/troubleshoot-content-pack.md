---
title: Rozwiązywanie problemów z działanie usługi Azure Active Directory błędy pakietu zawartości dziennika | Dokumentacja firmy Microsoft
description: Zawiera listę komunikatów o błędach pakietu zawartości działanie usługi Azure Active Directory oraz kroki rozwiązywania tych problemów.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 65747da92a3cad770cd9d474d27645782f7306b9
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52998728"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Rozwiązywanie problemów z działanie usługi Azure Active Directory rejestruje błędy pakietu zawartości 

|  |
|--|
|Obecnie pakiet zawartości usługi Power BI dla usługi Azure AD używa interfejsów API programu Graph usługi Azure AD do pobierania danych z dzierżawy usługi Azure AD. W rezultacie mogą występować różnice między danymi dostępnymi w pakiecie zawartości i danymi pobranymi przy użyciu [interfejsów API programu Microsoft Graph do raportowania](concept-reporting-api.md). |
|  |

Podczas pracy z pakietu zawartości usługi Power BI dla usługi Azure Active Directory (Azure AD), możliwe jest wystąpiły następujące błędy: 

- [Odświeżanie nie powiodło się](troubleshoot-content-pack.md#refresh-failed) 
- [Nie można zaktualizować poświadczeń źródła danych](troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [Importowanie danych trwa zbyt długo](troubleshoot-content-pack.md#importing-of-data-is-taking-too-long) 

Ten artykuł zawiera informacje na temat możliwych przyczyn i jak naprawić te błędy.
 
## <a name="refresh-failed"></a>Odświeżanie nie powiodło się 
 
**Jak ten błąd jest udostępniane**: wiadomości E-mail z usługi Power BI lub stan niepowodzenia w historii odświeżania. 


| Przyczyna | Jak naprawić |
| ---   | ---        |
| Odśwież błędu, który może być spowodowane błędy, jeśli poświadczenia użytkowników, nawiązywania połączenia z pakietem zawartości zostały resetowania, ale nie są aktualizowane w ustawieniach połączenia pakietu zawartości. | W usłudze Power BI, Znajdź zestaw danych, odpowiadający pulpitu nawigacyjnego dzienników aktywności usługi Azure AD (**rejestruje działanie usługi Azure Active Directory**), wybierz harmonogram odświeżania, a następnie wprowadź swoje poświadczenia usługi Azure AD. |
| Odświeżanie może zakończyć się niepowodzeniem z powodu problemów z danych podstawowych pakietu zawartości. | [Bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
 
## <a name="failed-to-update-data-source-credentials"></a>Nie można zaktualizować poświadczeń źródła danych 
 
**Jak ten błąd jest udostępniane**: W usłudze Power BI, gdy połączysz się działanie usługi Azure AD dzienniki pakietu zawartości. 

| Przyczyna | Jak naprawić |
| ---   | ---        |
| Użytkownik nawiązujący połączenie nie jest administratorem globalnym lub Czytelnik zabezpieczeń lub administrator zabezpieczeń. | Użyj konta administratora globalnego lub Czytelnik zabezpieczeń lub administrator zabezpieczeń do dostępu do pakietów zawartości. |
| Dzierżawy nie jest dzierżawą usługi Premium lub nie ma co najmniej jeden użytkownik mający licencję Premium pliku. | [Bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 


## <a name="data-import-is-too-slow"></a>Importowanie danych jest zbyt wolno 
 
**Jak ten błąd jest udostępniane**: W usłudze Power BI po połączeniu z pakietem zawartości, proces importowania danych rozpoczyna się przygotować pulpitu nawigacyjnego do działania usługi Azure AD dzienniki. Zostanie wyświetlony komunikat: **importowanie danych...**  bez żadnych dalszych postępów.  

| Przyczyna | Jak naprawić |
| ---   | ---        |
| W zależności od rozmiaru dzierżawy usługi ten krok może potrwać od kilku minut do 30 minut. | Jeśli komunikat nie zmienia się na wyświetlanie pulpitu nawigacyjnego w ciągu godziny, [bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>Kolejne kroki

* [Zainstaluj pakiet contect usługi Power BI dla usługi Azure AD raportów](quickstart-install-power-bi-content-pack.md).
* [Użyj zawartości pakietu dla usługi Azure AD raportów umożliwia wizualizowanie danych usługi Power BI](howto-power-bi-content-pack.md)
* [Jak uzyskać pomoc techniczną dla usługi Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md)
