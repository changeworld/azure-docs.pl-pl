---
title: Korzystanie z pakietu zawartości usługi Power BI dla usługi Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak używać pakietu zawartości usługi Power BI dla usługi Azure Active Directory
services: active-directory
author: priyamohanram
manager: mtillman
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.component: report-monitor
ms.date: 12/06/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: c0326a6b611d5f3d5633db2d2b64b8cdc15e10a7
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816688"
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>Korzystanie z pakietu zawartości usługi Power BI dla usługi Azure Active Directory

|  |
|--|
|Obecnie pakiet zawartości usługi Azure AD w usłudze Power BI używa interfejsów API programu Graph usługi Azure AD do pobierania danych z dzierżawą usługi Azure AD. W rezultacie może zostać wyświetlony niektóre różnice między danymi, które są dostępne w pakiecie zawartości i dane pobrane przy użyciu [interfejsów API programu Microsoft Graph dla raportowania](concept-reporting-api.md). |
|  |

Jako IT administrator musisz zrozumieć, jak użytkownicy przyjmują i korzystać z funkcji usługi Azure Active Directory. Dzięki temu można zaplanować infrastrukturę IT oraz komunikację zwiększyć użycie, i w pełni wykorzystać funkcje usługi Azure AD. Pakiet zawartości usługi Power BI dla usługi Azure Active Directory daje możliwość pogłębioną analizę danych w taki sposób, aby zebrać więcej szczegółowych informacji o co się dzieje z katalogiem. Dzięki integracji interfejsów API usługi Azure Active Directory do usługi Power BI można łatwo pobrać wstępnie skompilowanych pakietu zawartości i uzyskać wgląd w całą aktywność w usłudze Azure Active Directory za pomocą rozbudowanego środowiska wizualizacji, usługa Power BI oferuje. Możesz utworzyć własny pulpit nawigacyjny i w prosty sposób udostępnić go innym osobom w organizacji. 

## <a name="prerequisites"></a>Wymagania wstępne

Należy licencję usługi Azure AD premium (P1/P2) używać pakietu zawartości. 

## <a name="install-the-content-pack"></a>Zainstalowanie pakietu zawartości

Zapoznaj się z [Szybki Start](quickstart-install-power-bi-content-pack.md) do zainstalowania pakietu zawartości usługi Azure AD w usłudze Power BI.

## <a name="what-can-i-do-with-this-content-pack"></a>Co można zrobić z tym pakietem zawartości?

Zanim przejdziemy do tego, co można zrobić z tym pakietem zawartości, omówimy pokrótce znajdujące się w nim raporty. Dane raportu dotyczą **ostatnich 30 dni**.

### <a name="reports-included-in-this-version-of-azure-active-directory-logs-content-pack"></a>Raporty zawarte w tej wersji pakietu zawartości usługi Azure Active Directory

**App Usage and Trend report** (Raport dotyczący użycia i trendu aplikacji): uzyskaj wgląd w aplikacje używane w organizacji oraz dowiedz się, które są najczęściej używane i kiedy. Ten raport służy do gromadzenia informacji dotyczących sposobu używania aplikacji, które zostały niedawno wdrożone w organizacji, lub sprawdzenia popularności poszczególnych aplikacji. W ten sposób można poprawić sposób korzystania z nich, jeśli dana aplikacja nie jest używana.

**Sign-ins by location and users** (Logowania według lokalizacji i użytkowników): uzyskaj wgląd w informacje o wszystkich logowaniach wykonywanych przy użyciu tożsamości Azure i poznaj tożsamość użytkowników. Dzięki temu możesz zagłębić się w szczegóły poszczególnych logowań i odpowiedzieć na pytania:

- Skąd loguje się dany użytkownik?
- Który użytkownik logował się najczęściej i skąd następowały logowania? 
- Czy logowanie zakończyło się pomyślnie?  
 
Możesz przejść do szczegółów, klikając określoną datę lub lokalizację.

**Unique users per app** (Liczba unikatowych użytkowników aplikacji): wyświetla wszystkich unikatowych użytkowników korzystających z danej aplikacji. Dotyczy tylko użytkowników, którzy *pomyślnie* zalogowali się do aplikacji.

**Device sign-ins** (Logowania do urządzeń): wyświetla typ systemu operacyjnego i przeglądarki używane przez użytkowników w organizacji oraz szczegółowe informacje dotyczące użytkowników, takie jak:

- Nazwa użytkownika
- Adres IP
- Lokalizacja 
- Stan logowania 

Dzięki temu raportowi można zapoznać się z różnymi profilami urządzeń używanych w organizacji i określić zasady zarządzania urządzeniami w oparciu o informacje o ich użyciu.

**SSPR Funnel** (Lejek SSPR): pomaga w zrozumieniu procesu resetowania haseł w Twojej organizacji. Dowiedz się, ile wystąpiło prób resetowania haseł przy użyciu narzędzia SSPR oraz ile spośród nich zakończyło się pomyślnie. Posługując się raportem, przeanalizuj próby zresetowania hasła zakończone niepowodzeniem i poznaj ich przyczyny. Ten raport pozwala lepiej zrozumieć sposób używania narzędzia SSPR w organizacji, co może pomóc w podejmowaniu prawidłowych decyzji.

## <a name="customizing-azure-ad-activity-content-pack"></a>Dostosowywanie pakietu zawartości usługi Azure AD Activity

**Zmień wizualizację**: możesz zmienić wizualizację raportu, klikając opcję **Edytuj raport** i wybierając wizualizację.
 
![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/howto-power-bi-content-pack/09.png) 
 
![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/howto-power-bi-content-pack/10.png) 

**Dołącz dodatkowe pola**: w raporcie można dodawać pola i je usuwać. W tym celu wystarczy wybrać element wizualny, którego ma dotyczyć zmiana. W poniższym przykładzie w widoku tabeli zostanie dodane pole „Sign-in Status” (Stan zalogowania). 
 
![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/howto-power-bi-content-pack/11.png) 

**Przypnij wizualizacje do pulpitu nawigacyjnego**: można dostosować pulpit nawigacyjny, dołączając własne wizualizacje do raportu i przypinając je do pulpitu nawigacyjnego. W poniższym przykładzie nowy filtr o nazwie „Sign-in Status” został dodany i dołączony do raportu. Zmieniono również wizualizację z wykresu słupkowego na liniowy. Ten nowy element wizualny można przypiąć do pulpitu nawigacyjnego.

![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/howto-power-bi-content-pack/12.png) 

![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/howto-power-bi-content-pack/13.png) 
 

 


**Udostępnianie pulpitu nawigacyjnego**: po utworzeniu własnej zawartości można udostępnić pulpit nawigacyjny użytkownikom w organizacji. Należy pamiętać, że po udostępnieniu raportu jego użytkownicy widzą pola, które zostały wybrane w raporcie.
 
![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/howto-power-bi-content-pack/14.png) 



## <a name="scheduling-a-daily-refresh-of-your-power-bi-report"></a>Planowanie codziennego odświeżania raportu usługi Power BI

Aby zaplanować codzienne odświeżanie raportu usługi Power BI, przejdź do obszaru **Zestawy danych > Ustawienia > Zaplanuj odświeżanie** i ustaw go, jak pokazano poniżej.
 
![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/howto-power-bi-content-pack/15.png) 

## <a name="updating-to-newer-version-of-content-pack"></a>Aktualizacja do nowszej wersji pakietu zawartości

Jeśli chcesz zaktualizować pakiet zawartości do nowszej wersji:

- Pobierz nowy pakiet zawartości i skonfiguruj go zgodnie z instrukcjami zawartymi w tym artykule.

- Po skonfigurowaniu pakietu przejdź do obszaru **Źródło danych > Ustawienia > Poświadczenia dostępu do źródła danych** i ponownie wprowadź swoje poświadczenia, jak pokazano poniżej

    ![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/howto-power-bi-content-pack/16.png) 

Po uruchomieniu nowej wersji pakietu zawartości można w razie potrzeby pozbyć się starej wersji, usuwając skojarzone z nim raporty i zestawy danych.

## <a name="still-having-issues"></a>Nadal masz problemy? 

Zapoznaj się z [przewodnikiem rozwiązywania problemów](troubleshoot-content-pack.md). Ogólną pomoc dotyczącą usługi Power BI można znaleźć w następujących [artykułach pomocy](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
 

## <a name="next-steps"></a>Kolejne kroki

* [Zainstaluj pakiet zawartości usługi Power BI](quickstart-install-power-bi-content-pack.md).
* [Rozwiązywanie problemów z błędami pakietu zawartości](troubleshoot-content-pack.md).
* [Co to są raporty w usłudze Azure AD? ](overview-reports.md).
