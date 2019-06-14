---
title: Korzystanie z pakietu zawartości usługi Power BI dla usługi Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak używać pakietu zawartości usługi Power BI dla usługi Azure Active Directory
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 934562147fedcc81b16fd1ad2534af5662ef4b78
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60437672"
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>Jak używać pakietu zawartości usługi Azure Active Directory usługi Power BI

|  |
|--|
|Obecnie pakiet zawartości usługi Power BI dla usługi Azure AD używa interfejsów API programu Graph usługi Azure AD do pobierania danych z dzierżawy usługi Azure AD. W rezultacie mogą występować różnice między danymi dostępnymi w pakiecie zawartości i danymi pobranymi przy użyciu [interfejsów API programu Microsoft Graph do raportowania](concept-reporting-api.md). |
|  |

Usługa Power BI zawartości, że pakiet usługi Azure Active Directory (Azure AD) zawiera wstępnie utworzone raporty ułatwiające Dowiedz się, jak użytkownicy przyjmują i korzystać z funkcji usługi Azure AD. Dzięki temu można uzyskać wgląd w wszystkim działaniom w katalogiem, za pomocą rozbudowanego środowiska wizualizacji w usłudze Power BI. Można również utworzyć własny pulpit nawigacyjny i udostępniać je innym osobom w organizacji. 

## <a name="prerequisites"></a>Wymagania wstępne

Należy licencję usługi Azure AD premium (P1/P2) używać pakietu zawartości. Aby uaktualnić swoją wersję usługi Azure Active Directory, zobacz [Wprowadzenie do usługi Azure Active Directory w wersji Premium](../fundamentals/active-directory-get-started-premium.md).

## <a name="install-the-content-pack"></a>Zainstalowanie pakietu zawartości

Zapoznaj się z [Szybki Start](quickstart-install-power-bi-content-pack.md) do zainstalowania pakietu zawartości usługi Azure AD w usłudze Power BI.

### <a name="reports-included-in-this-version-of-azure-ad-logs-content-pack"></a>Raporty zawarte w tej wersji usługi Azure AD, dzienniki pakietu zawartości

Następujące raporty znajdują się w pakiecie zawartości usługi Azure AD w usłudze Power BI. Raporty zawierają dane z **ostatnich 30 dni**.

**Raport użycia aplikacji oraz trendów**:  Ten raport zapewnia wgląd w aplikacje używane w Twojej organizacji. Pobierz listę najpopularniejszych aplikacji lub zrozumienie sposobu korzystania z aplikacji, której ostatnio etapowo wdrażana w Twojej organizacji. Dzięki temu można śledzić i zwiększyć użycie wraz z upływem czasu.

**Logowania według lokalizacji i użytkowników**: Ten raport zawiera dane na wszystkich logowań wykonywane przy użyciu usługi Azure Identity. Dzięki temu raportowi można przechodzić do poszczególnych logowań i odpowiedzieć na pytania:

- Gdzie ten użytkownik Zaloguj się za pomocą?
- Który użytkownik logował się najczęściej i skąd następowały logowania? 
- Czy logowanie zakończyło się pomyślnie?  
 
Wyniki można również filtrować, wybierając określoną datę lub lokalizację.

**Liczba unikatowych użytkowników na aplikację**:  Ten raport zawiera widok wszystkich unikatowych użytkowników korzystających z danej aplikacji. Zawierają one tylko użytkownicy, którzy mają "*pomyślnie*" zalogowali się do aplikacji.

**Urządzenie logowania**: Ten raport pomaga zrozumieć z różnymi profilami urządzeń używanych w organizacji i określić zasady zarządzania urządzeniami na podstawie użycia. Zapewnia dane dotyczące typu systemu operacyjnego i przeglądarki używane do logowania do aplikacji, wraz ze szczegółowymi informacjami o użytkownikach, w tym:

- Nazwa użytkownika
- Adres IP
- Lokalizacja 
- Stan logowania 

**Lejek SSPR**: Ten raport pomaga zrozumieć, jak narzędzie do samoobsługowego resetowania HASEŁ jest używany w Twojej organizacji. Można wyświetlić hasło ile wystąpiło prób resetowania przy użyciu narzędzia SSPR oraz ile z nich zakończyły się pomyślnie. Możesz zagłębić się niepowodzenia operacji resetowania hasła i zrozumieć przyczyny. 

## <a name="customize-azure-ad-activity-content-pack"></a>Dostosowywanie pakietu zawartości usługi Azure AD Activity

**Zmień wizualizację**:  Zmień wizualizację raportu, klikając **Edytuj raport** i wybierz wizualizację.
 
![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/howto-power-bi-content-pack/09.png) 
 
![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/howto-power-bi-content-pack/10.png) 

**Dołącz dodatkowe pola**:  Można dodać pola do raportu lub usuń go, wybierając wizualizację, dla których chcesz dodawać i usuwać pola. Na przykład można dodać pole "sign-in status" w widoku tabeli, jak pokazano poniżej. 
 
![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/howto-power-bi-content-pack/11.png) 

**Przypinanie wizualizacji do pulpitu nawigacyjnego**:  Możesz dostosować pulpit nawigacyjny, dołączając własne wizualizacje do raportu i przypinanie jej do pulpitu nawigacyjnego. 

![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/howto-power-bi-content-pack/13.png) 
 
**Udostępnianie pulpitu nawigacyjnego**: Można także udostępnić pulpit nawigacyjny z użytkownikami w Twojej organizacji. Po udostępnieniu raportu jego użytkownicy widzą pola, które wybrano w raporcie.
 
![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/howto-power-bi-content-pack/14.png) 

## <a name="schedule-a-daily-refresh-of-your-power-bi-report"></a>Planowanie codziennego odświeżania raportu usługi Power BI

Aby zaplanować codzienne odświeżanie raportu usługi Power BI, przejdź do **zestawów danych** > **ustawienia** > **Zaplanuj odświeżanie** i ustawić go tak, jak pokazano poniżej.
 
![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/howto-power-bi-content-pack/15.png) 

## <a name="update-to-newer-version-of-content-pack"></a>Aktualizacja do nowszej wersji pakietu zawartości

Jeśli chcesz zaktualizować pakiet zawartości do nowszej wersji:

- Pobierz nowy pakiet zawartości i skonfiguruj go zgodnie z instrukcjami w tym artykule.

- Po skonfigurowaniu go, przejdź do obszaru **źródła danych** > **ustawienia** > **poświadczenia źródła danych** i ponownie wprowadź swoje poświadczenia.

    ![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/howto-power-bi-content-pack/16.png) 

Po upewnieniu się, że nowa wersja pakietu zawartości działa zgodnie z oczekiwaniami, możesz usunąć starą wersję, jeśli to konieczne, usuwając podstawowych raportów i zestawów danych skojarzonych z tym pakietem zawartości.

## <a name="troubleshoot-content-pack-errors"></a>Rozwiązywanie problemów z błędami pakietu zawartości

Podczas pracy z pakietem zawartości, możliwe jest wystąpiły następujące błędy: 

- [Odświeżanie nie powiodło się](#refresh-failed) 
- [Nie można zaktualizować poświadczeń źródła danych](#failed-to-update-data-source-credentials) 
- [Importowanie danych trwa zbyt długo](#data-import-is-too-slow) 

Ogólną pomoc dotyczącą usługi Power BI można znaleźć w następujących [artykułach pomocy](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).

### <a name="refresh-failed"></a>Odświeżanie nie powiodło się 
 
**Jak ten błąd jest udostępniane**: Wyślij wiadomość e-mail z usługi Power BI lub stan niepowodzenia w historii odświeżania. 


| Przyczyna | Jak naprawić |
| ---   | ---        |
| Odśwież błędu, który może być spowodowane błędy, jeśli poświadczenia użytkowników, nawiązywania połączenia z pakietem zawartości zostały resetowania, ale nie są aktualizowane w ustawieniach połączenia pakietu zawartości. | W usłudze Power BI, Znajdź zestaw danych, odpowiadający pulpitu nawigacyjnego dzienników aktywności usługi Azure AD (**rejestruje działanie usługi Azure Active Directory**), wybierz harmonogram odświeżania, a następnie wprowadź swoje poświadczenia usługi Azure AD. |
| Odświeżanie może zakończyć się niepowodzeniem z powodu problemów z danych podstawowych pakietu zawartości. | [Bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
 
### <a name="failed-to-update-data-source-credentials"></a>Nie można zaktualizować poświadczeń źródła danych 
 
**Jak ten błąd jest udostępniane**: W usłudze Power BI, po nawiązaniu połączenia z pakietem zawartości dzienników aktywności usługi Azure AD. 

| Przyczyna | Jak naprawić |
| ---   | ---        |
| Użytkownik nawiązujący połączenie nie jest administratorem globalnym lub Czytelnik zabezpieczeń lub administrator zabezpieczeń. | Użyj konta administratora globalnego lub Czytelnik zabezpieczeń lub administrator zabezpieczeń do dostępu do pakietów zawartości. |
| Dzierżawy nie jest dzierżawą usługi Premium lub nie ma co najmniej jeden użytkownik mający licencję Premium pliku. | [Bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
### <a name="data-import-is-too-slow"></a>Importowanie danych jest zbyt wolno 
 
**Jak ten błąd jest udostępniane**: W usłudze Power BI po połączeniu z pakietem zawartości, proces importowania danych rozpoczyna się przygotować pulpitu nawigacyjnego do działania usługi Azure AD dzienniki. Zostanie wyświetlony komunikat: **Importowanie danych...**  bez żadnych dalszych postępów.  

| Przyczyna | Jak naprawić |
| ---   | ---        |
| W zależności od rozmiaru dzierżawy usługi ten krok może potrwać od kilku minut do 30 minut. | Jeśli komunikat nie zmienia się na wyświetlanie pulpitu nawigacyjnego w ciągu godziny, [bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md).|
  
## <a name="next-steps"></a>Kolejne kroki

* [Zainstaluj pakiet zawartości usługi Power BI](quickstart-install-power-bi-content-pack.md).
* [Co to są raporty w usłudze Azure AD? ](overview-reports.md).
