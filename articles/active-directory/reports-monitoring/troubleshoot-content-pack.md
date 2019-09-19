---
title: Rozwiązywanie problemów z dziennikami aktywności Azure Active Directory błędy pakietu zawartości | Microsoft Docs
description: Zawiera listę komunikatów o błędach pakietu zawartości Azure Active Directory działania oraz czynności, które należy rozwiązać.
services: active-directory
documentationcenter: ''
author: cawrites
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
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e50f2b92318ada729ad8e3405af8403f31d7b6e
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129285"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Rozwiązywanie problemów z błędami pakietów zawartości dzienników aktywności Azure Active Directory 

|  |
|--|
|Obecnie pakiet zawartości usługi Power BI dla usługi Azure AD używa interfejsów API programu Graph usługi Azure AD do pobierania danych z dzierżawy usługi Azure AD. W rezultacie mogą występować różnice między danymi dostępnymi w pakiecie zawartości i danymi pobranymi przy użyciu [interfejsów API programu Microsoft Graph do raportowania](concept-reporting-api.md). |
|  |

Podczas pracy z pakietem zawartości Power BI dla Azure Active Directory (Azure AD) możliwe jest uruchomienie następujących błędów: 

- [Nie można odświeżyć](troubleshoot-content-pack.md#refresh-failed) 
- [Nie można zaktualizować poświadczeń źródła danych](troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [Importowanie danych trwa zbyt długo](#data-import-is-too-slow) 

Ten artykuł zawiera informacje o możliwych przyczynach i sposobach naprawy tych błędów.
 
## <a name="refresh-failed"></a>Niepowodzenie odświeżania 
 
Sposób narzutu na **ten błąd**: Poczta e-mail od Power BI lub stanu niepowodzenia w historii odświeżania. 


| Przyczyna | Jak naprawić |
| ---   | ---        |
| Błędy odświeżania mogą wynikać z tego, że poświadczenia użytkowników nawiązujących połączenie z pakietem zawartości zostały zresetowane, ale nie zostały zaktualizowane w ustawieniach połączenia pakietu zawartości. | W Power BI Znajdź zestaw danych odpowiadający pulpitowi nawigacyjnemu dzienników aktywności usługi Azure AD (**dzienniki aktywności Azure Active Directory**), wybierz pozycję Zaplanuj odświeżanie, a następnie wprowadź swoje poświadczenia usługi Azure AD. |
| Odświeżanie może zakończyć się niepowodzeniem z powodu dużych zestawów danych. | Obecnie pakiet zawartości usługi Azure AD z Power BI może obsługiwać tylko małe zestawy danych (mniej niż 500 000 wierszy) z powodu niektórych ograniczeń dotyczących limitów czasu w usłudze Power BI. Jeśli występują błędy ograniczania przepustowości lub odświeżenie nie powiedzie się z powodu problemów z przekroczeniem limitu czasu, może to być spowodowane próbą pobrania dużego zestawu danych. Skróć okres zapytania i spróbuj ponownie.|
 
 
## <a name="failed-to-update-data-source-credentials"></a>Nie można zaktualizować poświadczeń źródła danych 
 
Sposób narzutu na **ten błąd**: W Power BI, gdy nawiążesz połączenie z pakietem zawartości dzienników aktywności usługi Azure AD. 

| Przyczyna | Jak naprawić |
| ---   | ---        |
| Użytkownik nawiązujący połączenie nie jest administratorem globalnym lub czytelnikiem zabezpieczeń ani administratorem zabezpieczeń. | Użyj konta, które jest administratorem globalnym lub czytelnikiem zabezpieczeń lub administratorem zabezpieczeń, aby uzyskać dostęp do pakietów zawartości. |
| Dzierżawca nie jest dzierżawcą w warstwie Premium lub nie ma co najmniej jednego użytkownika z plikiem licencji Premium. | [Zaplikowanie biletu pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 


## <a name="data-import-is-too-slow"></a>Import danych jest zbyt wolny 
 
Sposób narzutu na **ten błąd**: W Power BI po nawiązaniu połączenia z pakietem zawartości proces importowania danych zaczyna przygotowywać pulpit nawigacyjny dla dzienników aktywności usługi Azure AD. Zostanie wyświetlony komunikat: **Trwa Importowanie danych...** bez dalszych postępów.  

| Przyczyna | Jak naprawić |
| ---   | ---        |
| W zależności od rozmiaru dzierżawy ten krok może potrwać od kilku minut do 30 minut. | Jeśli komunikat nie zmieni się w taki sposób, aby pokazywał pulpit nawigacyjny w ciągu godziny, należy wykonać zgłoszenie [biletu pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>Następne kroki

* [Zainstaluj pakiet zawartości Power BI dla raportów usługi Azure AD](quickstart-install-power-bi-content-pack.md).
* [Korzystanie z pakietu zawartości Power BI do raportów usługi Azure AD w celu wizualizacji danych](howto-power-bi-content-pack.md)
* [Jak uzyskać pomoc techniczną dla usługi Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md)
