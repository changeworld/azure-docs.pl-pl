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
ms.date: 01/15/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: bf50dbf942dc7a82afbb60455be45b6c4b287ccd
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2018
ms.locfileid: "43782179"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Rozwiązywanie problemów z działanie usługi Azure Active Directory rejestruje błędy pakietu zawartości 

|  |
|--|
|Obecnie pakiet zawartości usługi Azure AD w usłudze Power BI używa interfejsów API programu Graph usługi Azure AD do pobierania danych z dzierżawą usługi Azure AD. W rezultacie może zostać wyświetlony niektóre różnice między danymi, które są dostępne w pakiecie zawartości i dane pobrane przy użyciu [interfejsów API programu Microsoft Graph dla raportowania](concept-reporting-api.md). |
|  |

Podczas pracy z pakietu zawartości usługi Power BI dla usługi Azure Active Directory w wersji zapoznawczej, jest możliwe, że wystąpiły następujące błędy: 

- [Odświeżanie nie powiodło się](troubleshoot-content-pack.md#refresh-failed) 
- [Nie można zaktualizować poświadczeń źródła danych](troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [Importowanie danych trwa zbyt długo](troubleshoot-content-pack.md#importing-of-data-is-taking-too-long) 
 
Ten artykuł zawiera informacje na temat możliwych przyczyn i jak naprawić te błędy.
 
## <a name="refresh-failed"></a>Odświeżanie nie powiodło się 
 
**Jak ten błąd jest udostępniane**: wiadomości E-mail z usługi Power BI lub stan niepowodzenia w historii odświeżania. 


| Przyczyna | Jak naprawić |
| ---   | ---        |
| Odśwież błędu, który może być spowodowane błędy, jeśli poświadczenia użytkowników, nawiązywania połączenia z pakietem zawartości zostały resetowania, ale nie są aktualizowane w ustawieniach połączenia pakietu zawartości. | W usłudze Power BI Znajdź zestaw danych, odpowiadający pulpitu nawigacyjnego dzienników działanie usługi Azure Active Directory (działanie usługi Azure Active Directory dzienniki), wybierz pozycję Zaplanuj odświeżanie, a następnie wprowadź poświadczenia usługi Azure AD. |
| Odświeżanie może zakończyć się niepowodzeniem z powodu problemów z danych podstawowych pakietu zawartości. | Utwórz bilet pomocy technicznej. Aby uzyskać więcej informacji, zobacz [jak uzyskać pomoc techniczną dotyczącą usługi Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
 
## <a name="failed-to-update-data-source-credentials"></a>Nie można zaktualizować poświadczeń źródła danych 
 
**Jak ten błąd jest udostępniane**: W usłudze Power BI, nawiązywania połączenia z pakietem zawartości dzienników (wersja zapoznawcza) działanie usługi Azure Active Directory. 

| Przyczyna | Jak naprawić |
| ---   | ---        |
| Użytkownik nawiązujący połączenie nie jest administratorem globalnym lub Czytelnik zabezpieczeń lub administratorem zabezpieczeń. | Użyj konta administratora globalnego lub Czytelnik zabezpieczeń lub Administrator zabezpieczeń do dostępu do pakietów zawartości. |
| Dzierżawy nie jest dzierżawą usługi Premium lub nie ma co najmniej jeden użytkownik mający licencję Premium pliku. | Utwórz bilet pomocy technicznej. Aby uzyskać więcej informacji, zobacz [jak uzyskać pomoc techniczną dotyczącą usługi Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 

 

## <a name="importing-of-data-is-taking-too-long"></a>Importowanie danych trwa zbyt długo 
 
**Jak ten błąd jest udostępniane**: W usłudze Power BI po połączeniu pakietu zawartości, proces importowania danych rozpoczyna się przygotować pulpitu nawigacyjnego dla dziennika działanie usługi Azure Active Directory. Zostanie wyświetlony komunikat: "*importowanie danych...* "  

| Przyczyna | Jak naprawić |
| ---   | ---        |
| W zależności od rozmiaru dzierżawy usługi tego kroku może potrwać od kilka minut do 30 minut. | Po prostu o cierpliwość. Jeśli komunikat nie zmienia się na wyświetlanie pulpitu nawigacyjnego w ciągu godziny, zgłoś bilet pomocy technicznej. Aby uzyskać więcej informacji, zobacz [jak uzyskać pomoc techniczną dotyczącą usługi Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>Kolejne kroki

Aby zainstalować pakiet zawartości Power BI dla usługi Azure Active Directory w wersji zapoznawczej, kliknij przycisk [tutaj](https://powerbi.microsoft.com/blog/azure-active-directory-meets-power-bi/).


