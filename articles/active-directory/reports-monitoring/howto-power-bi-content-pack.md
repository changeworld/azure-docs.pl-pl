---
title: Korzystanie z pakietu zawartości usługi Power BI dla usługi Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak używać pakietu zawartości usługi Power BI dla usługi Azure Active Directory
services: active-directory
author: cawrites
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d56802e96028b6b01b6be749405c56df2648161
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988232"
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>Jak używać pakietu zawartości Azure Active Directory Power BI

|  |
|--|
|Obecnie pakiet zawartości usługi Power BI dla usługi Azure AD używa interfejsów API programu Graph usługi Azure AD do pobierania danych z dzierżawy usługi Azure AD. W rezultacie mogą występować różnice między danymi dostępnymi w pakiecie zawartości i danymi pobranymi przy użyciu [interfejsów API programu Microsoft Graph do raportowania](concept-reporting-api.md). |
|  |

Pakiet zawartości Power BI dla Azure Active Directory (Azure AD) zawiera wstępnie skompilowane raporty, które ułatwiają zrozumienie sposobu, w jaki użytkownicy przyjmują i korzystają z funkcji usługi Azure AD. Pozwala to uzyskać wgląd we wszystkie działania w katalogu przy użyciu bogatego środowiska wizualizacji w Power BI. Możesz również utworzyć własny pulpit nawigacyjny i udostępnić go innym osobom w organizacji. 

## <a name="prerequisites"></a>Wymagania wstępne

Do korzystania z pakietu zawartości wymagana jest licencja usługi Azure AD w wersji Premium (P1/P2). Aby uaktualnić swoją wersję usługi Azure Active Directory, zobacz [Wprowadzenie do usługi Azure Active Directory w wersji Premium](../fundamentals/active-directory-get-started-premium.md).

## <a name="install-the-content-pack"></a>Zainstaluj pakiet zawartości

Zapoznaj się z [przewodnikiem Szybki Start](quickstart-install-power-bi-content-pack.md) , aby zainstalować pakiet zawartości usługi Azure AD Power BI.

### <a name="reports-included-in-this-version-of-azure-ad-logs-content-pack"></a>Raporty uwzględnione w tej wersji pakietu zawartości dzienników usługi Azure AD

Do pakietu zawartości usługi Azure AD Power BI należą następujące raporty: Raporty zawierają dane z **ostatnich 30 dni**.

**Raport dotyczący użycia i trendów aplikacji**:  Ten raport zawiera szczegółowe informacje o aplikacjach używanych w organizacji. Możesz uzyskać listę najpopularniejszych aplikacji lub zrozumieć, w jaki sposób aplikacja, która została ostatnio przeprowadzona w organizacji, jest używana. Dzięki temu można śledzić i poprawiać użycie w czasie.

**Logowania według lokalizacji i użytkowników**: Ten raport zawiera dane dotyczące wszystkich logowań wykonywanych przy użyciu tożsamości platformy Azure. Za pomocą tego raportu można przechodzić do poszczególnych logowań i udzielać odpowiedzi na takie pytania, jak:

- W jakim miejscu ten użytkownik się zalogować?
- Który użytkownik logował się najczęściej i skąd następowały logowania? 
- Czy logowanie zakończyło się pomyślnie?  
 
Możesz również filtrować wyniki, wybierając określoną datę lub lokalizację.

**Liczba unikatowych użytkowników na aplikację**:  Ten raport zawiera widok wszystkich unikatowych użytkowników korzystających z danej aplikacji. Zawiera tylko użytkowników, którzy zostali zarejestrowani w aplikacji.

**Logowanie urządzeń**: Ten raport ułatwia zapoznanie się z różnymi profilami urządzeń używanymi w organizacji i Określanie zasad dotyczących urządzeń opartych na użyciu. Zapewnia ona dane dotyczące typu systemu operacyjnego i przeglądarek używanych do logowania się do aplikacji, a także szczegółowe informacje o użytkownikach, w tym:

- Nazwa użytkownika
- Adres IP
- Location 
- Stan logowania 

**Lejk SSPR**: Ten raport pomaga zrozumieć, w jaki sposób narzędzie SSPR jest używane w organizacji. Możesz zobaczyć, ile operacji resetowania haseł próbowano wykonać za pomocą narzędzia SSPR, a ile z nich zakończyło się pomyślnie. Możesz również Dig bardziej szczegółowe informacje o błędach resetowania hasła i zrozumieć, dlaczego wystąpiły pewne awarie. 

## <a name="customize-azure-ad-activity-content-pack"></a>Dostosuj pakiet zawartości działania usługi Azure AD

**Zmień wizualizację**:  Wizualizację raportu można zmienić, klikając pozycję **Edytuj raport** i wybierając żądaną wizualizację.
 
![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/howto-power-bi-content-pack/09.png) 
 
![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/howto-power-bi-content-pack/10.png) 

**Dołącz dodatkowe pola**:  Możesz dodać pole do raportu lub usunąć je, wybierając wizualizację, do której chcesz dodać lub usunąć pole. Na przykład można dodać pole "stan logowania" do widoku tabeli, jak pokazano poniżej. 
 
![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/howto-power-bi-content-pack/11.png) 

**Przypinanie wizualizacji do pulpitu nawigacyjnego**:  Możesz dostosować pulpit nawigacyjny, dołączając własne wizualizacje do raportu i przypinając je do pulpitu nawigacyjnego. 

![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/howto-power-bi-content-pack/13.png) 
 
**Udostępnianie pulpitu nawigacyjnego**: Pulpit nawigacyjny można również udostępnić użytkownikom w organizacji. Po udostępnieniu raportu użytkownicy mogą zobaczyć pola wybrane w raporcie.
 
![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/howto-power-bi-content-pack/14.png) 

## <a name="schedule-a-daily-refresh-of-your-power-bi-report"></a>Zaplanuj codzienne odświeżanie raportu Power BI

Aby zaplanować codzienne odświeżanie raportu Power BI, przejdź do pozycji**Ustawienia** >  **zestawów danych** > Zaplanuj**odświeżanie** i ustaw go, jak pokazano poniżej.
 
![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/howto-power-bi-content-pack/15.png) 

## <a name="update-to-newer-version-of-content-pack"></a>Aktualizacja do nowszej wersji pakietu zawartości

Jeśli chcesz zaktualizować pakiet zawartości do nowszej wersji:

- Pobierz nowy pakiet zawartości i skonfiguruj go przy użyciu instrukcji opisanych w tym artykule.

- Po jego skonfigurowaniu przejdź do pozycji**Ustawienia** >  **źródła** > danych**poświadczenia źródła danych** , a następnie wprowadź ponownie swoje poświadczenia.

    ![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/howto-power-bi-content-pack/16.png) 

Po sprawdzeniu, czy nowa wersja pakietu zawartości działa zgodnie z oczekiwaniami, można usunąć starą wersję, jeśli jest to konieczne, usuwając bazowe raporty i zestawy danych skojarzone z tym pakietem zawartości.

## <a name="troubleshoot-content-pack-errors"></a>Rozwiązywanie problemów z błędami pakietów zawartości

Podczas pracy z pakietem zawartości można uruchamiać następujące błędy: 

- [Nie można odświeżyć](#refresh-failed) 
- [Nie można zaktualizować poświadczeń źródła danych](#failed-to-update-data-source-credentials) 
- [Importowanie danych trwa zbyt długo](#data-import-is-too-slow) 

Ogólną pomoc dotyczącą usługi Power BI można znaleźć w następujących [artykułach pomocy](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).

### <a name="refresh-failed"></a>Niepowodzenie odświeżania 
 
Sposób narzutu na **ten błąd**: Poczta e-mail od Power BI lub stanu niepowodzenia w historii odświeżania. 


| Przyczyna | Jak naprawić |
| ---   | ---        |
| Błędy odświeżania mogą wynikać z tego, że poświadczenia użytkowników nawiązujących połączenie z pakietem zawartości zostały zresetowane, ale nie zostały zaktualizowane w ustawieniach połączenia pakietu zawartości. | W Power BI Znajdź zestaw danych odpowiadający pulpitowi nawigacyjnemu dzienników aktywności usługi Azure AD (**dzienniki aktywności Azure Active Directory**), wybierz pozycję Zaplanuj odświeżanie, a następnie wprowadź swoje poświadczenia usługi Azure AD. |
| Odświeżanie może zakończyć się niepowodzeniem z powodu problemów z danymi w bazowym pakiecie zawartości. | [Zaplikowanie biletu pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
 
### <a name="failed-to-update-data-source-credentials"></a>Nie można zaktualizować poświadczeń źródła danych 
 
Sposób narzutu na **ten błąd**: W Power BI, gdy nawiążesz połączenie z pakietem zawartości dzienników aktywności usługi Azure AD. 

| Przyczyna | Jak naprawić |
| ---   | ---        |
| Użytkownik nawiązujący połączenie nie jest administratorem globalnym lub czytelnikiem zabezpieczeń ani administratorem zabezpieczeń. | Użyj konta, które jest administratorem globalnym lub czytelnikiem zabezpieczeń lub administratorem zabezpieczeń, aby uzyskać dostęp do pakietów zawartości. |
| Dzierżawca nie jest dzierżawcą w warstwie Premium lub nie ma co najmniej jednego użytkownika z plikiem licencji Premium. | [Zaplikowanie biletu pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
### <a name="data-import-is-too-slow"></a>Import danych jest zbyt wolny 
 
Sposób narzutu na **ten błąd**: W Power BI po nawiązaniu połączenia z pakietem zawartości proces importowania danych zaczyna przygotowywać pulpit nawigacyjny dla dzienników aktywności usługi Azure AD. Zostanie wyświetlony komunikat: **Trwa Importowanie danych...** bez dalszych postępów.  

| Przyczyna | Jak naprawić |
| ---   | ---        |
| W zależności od rozmiaru dzierżawy ten krok może potrwać od kilku minut do 30 minut. | Jeśli komunikat nie zmieni się w taki sposób, aby pokazywał pulpit nawigacyjny w ciągu godziny, należy wykonać zgłoszenie [biletu pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md).|
  
## <a name="next-steps"></a>Następne kroki

* [Zainstaluj pakiet zawartości Power BI](quickstart-install-power-bi-content-pack.md).
* [Co to są raporty usługi Azure AD?](overview-reports.md).
