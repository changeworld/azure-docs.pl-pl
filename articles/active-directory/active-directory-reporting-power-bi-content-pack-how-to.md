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
ms.component: compliance-reports
ms.date: 12/06/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 531d192bf024def1a90d9e5034645b6ec6e0c0e1
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225426"
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>Korzystanie z pakietu zawartości usługi Power BI dla usługi Azure Active Directory

Zrozumienie, jak użytkownicy korzystają z funkcji usługi Azure Active Directory, jest bardzo ważne dla Ciebie jako administratora IT. Dzięki temu można zaplanować infrastrukturę IT oraz komunikację w celu zwiększenia użycia i maksymalnego wykorzystania funkcji usługi AAD. Pakiet zawartości usługi Power BI dla usługi Azure Active Directory umożliwia pogłębioną analizę danych, ułatwiającą zrozumienie sposobu działania usługi Azure Active Directory w przypadku różnych funkcji, na których polegasz.  Dzięki integracji interfejsów API usługi Azure Active Directory z usługą Power BI można łatwo pobrać gotowe pakiety zawartości i uzyskać informacje o wszystkich działaniach usługi Azure Active Directory za pomocą rozbudowanego środowiska wizualizacji oferowanego przez usługę Power BI. Możesz utworzyć własny pulpit nawigacyjny i w prosty sposób udostępnić go innym osobom w organizacji. 

W tym temacie przedstawiono instrukcje krok po kroku dotyczące instalacji i używania pakietu zawartości w danym środowisku.

## <a name="installation"></a>Instalacja  

**Aby zainstalować pakiet zawartości usługi Power BI:**

1. Zaloguj się do [usługi Power BI](https://app.powerbi.com/groups/me/getdata/services) za pomocą konta Power BI (jest to to samo konto, co w przypadku usługi O365 lub Azure AD).

2. W dolnej części okienka nawigacji po lewej stronie wybierz opcję **Pobierz dane**.

    ![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/01.png)
 
3. W polu **Usługi** kliknij opcję **Pobierz**.
   
    ![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/02.png)

4.  Wyszukaj usługę **Azure Active Directory**.

    ![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/03.png)
 
5.  Po wyświetleniu monitu wpisz swój identyfikator dzierżawy Azure AD, a następnie kliknij przycisk **Dalej**.

    > [!TIP] 
    > Aby szybko uzyskać identyfikator dzierżawy usługi Office 365 / Azure AD, zaloguj się do portalu usługi Azure AD, za pomocą wyszczególniania przejdź do katalogu i skopiuj wartość **Identyfikator katalogu** ze strony [**Właściwości**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).

    ![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/04.png) 

6.  Kliknij przycisk **Zaloguj**. 
 
    ![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/05.png) 



7.  Wprowadź nazwę użytkownika i hasło, a następnie kliknij przycisk **Zaloguj**.
 
    ![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/06.png) 

8.  W oknie dialogowym zgody na aplikację kliknij przycisk **Akceptuj**.
 
9.  Po utworzeniu pulpitu nawigacyjnego dzienników aktywności usługi Azure Active Directory kliknij go.
 
    ![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/08.png) 

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
 
![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/09.png) 
 
![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/10.png) 

**Dołącz dodatkowe pola**: w raporcie można dodawać pola i je usuwać. W tym celu wystarczy wybrać element wizualny, którego ma dotyczyć zmiana. W poniższym przykładzie w widoku tabeli zostanie dodane pole „Sign-in Status” (Stan zalogowania). 
 
![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/11.png) 

**Przypnij wizualizacje do pulpitu nawigacyjnego**: można dostosować pulpit nawigacyjny, dołączając własne wizualizacje do raportu i przypinając je do pulpitu nawigacyjnego. W poniższym przykładzie nowy filtr o nazwie „Sign-in Status” został dodany i dołączony do raportu. Zmieniono również wizualizację z wykresu słupkowego na liniowy. Ten nowy element wizualny można przypiąć do pulpitu nawigacyjnego.

![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/12.png) 

![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/13.png) 
 

 


**Udostępnianie pulpitu nawigacyjnego**: po utworzeniu własnej zawartości można udostępnić pulpit nawigacyjny użytkownikom w organizacji. Należy pamiętać, że po udostępnieniu raportu jego użytkownicy widzą pola, które zostały wybrane w raporcie.
 
![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/14.png) 



## <a name="scheduling-a-daily-refresh-of-your-power-bi-report"></a>Planowanie codziennego odświeżania raportu usługi Power BI

Aby zaplanować codzienne odświeżanie raportu usługi Power BI, przejdź do obszaru **Zestawy danych > Ustawienia > Zaplanuj odświeżanie** i ustaw go, jak pokazano poniżej.
 
![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/15.png) 

## <a name="updating-to-newer-version-of-content-pack"></a>Aktualizacja do nowszej wersji pakietu zawartości

Jeśli chcesz zaktualizować pakiet zawartości do nowszej wersji:

- Pobierz nowy pakiet zawartości i skonfiguruj go zgodnie z instrukcjami zawartymi w tym artykule.

- Po skonfigurowaniu pakietu przejdź do obszaru **Źródło danych > Ustawienia > Poświadczenia dostępu do źródła danych** i ponownie wprowadź swoje poświadczenia, jak pokazano poniżej

    ![Pakiet zawartości usługi Power BI dla usługi Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/16.png) 

Po uruchomieniu nowej wersji pakietu zawartości można w razie potrzeby pozbyć się starej wersji, usuwając skojarzone z nim raporty i zestawy danych.

## <a name="still-having-issues"></a>Nadal masz problemy? 

Zapoznaj się z [przewodnikiem rozwiązywania problemów](active-directory-reporting-troubleshoot-content-pack.md). Ogólną pomoc dotyczącą usługi Power BI można znaleźć w następujących [artykułach pomocy](https://powerbi.microsoft.com/en-us/documentation/powerbi-service-get-started/).
 

## <a name="next-steps"></a>Kolejne kroki

Omówienie funkcji raportowania można znaleźć w temacie [Raporty w usłudze Azure Active Directory](active-directory-reporting-azure-portal.md).
