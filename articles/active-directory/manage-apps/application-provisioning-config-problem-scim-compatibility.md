---
title: Znane problemy i rozwiązania dzięki zgodności protokołu 2.0 Standard SCIM usługi aprowizacji użytkownika usługi Azure AD | Dokumentacja firmy Microsoft
description: Rozwiązywanie typowych problemów ze zgodnością protokołu zmierzyła się z zespołem podczas dodawania aplikacji spoza galerii, która obsługuje do usługi Azure AD w wersji 2.0 Standard SCIM
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9a0e595d2120d3cdccd42c502a83de9d5ed3ff4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65963177"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Znane problemy i rozwiązania dzięki zgodności protokołu 2.0 Standard SCIM usługi aprowizacji użytkownika usługi Azure AD

Azure Active Directory (Azure AD), mogą automatycznie obsługiwać użytkowników i grup do dowolnej aplikacji lub systemu, który jest fronted przez usługę sieci web za pomocą interfejsu określonego w [System protokołu Standard międzydomenowe Identity Management (SCIM) w wersji 2.0 Specyfikacja](https://tools.ietf.org/html/draft-ietf-scim-api-19). 

Obsługa usługi Azure AD protokołu 2.0 Standard SCIM jest opisana w [przy użyciu systemu dla międzydomenowe Identity Management (Standard SCIM) aby automatycznie aprowizować użytkowników i grup z usługi Azure Active Directory do aplikacji](use-scim-to-provision-users-and-groups.md), które zawiera listę określonych części protokołu, który implementuje, aby automatycznie aprowizować użytkowników i grup z usługi Azure AD do aplikacji obsługujących standard SCIM w wersji 2.0.

W tym artykule opisano bieżącej i wcześniejszych problemów z użytkownikiem usługi Azure AD, inicjowania obsługi usługi gotowość do protokołu Standard SCIM 2.0 oraz sposób obejścia tych problemów.

> [!IMPORTANT]
> Najnowsza aktualizacja usługi Azure AD użytkownik aprowizacji usługi Standard SCIM klienta została wprowadzona od 18 grudnia 2018 r. Ta aktualizacja skierowana znanymi problemami, wymienione w poniższej tabeli. Zobacz często zadawane pytania poniżej, aby uzyskać więcej informacji na temat tej aktualizacji.

## <a name="scim-20-compliance-issues-and-status"></a>Problemy ze zgodnością w wersji Standard SCIM 2.0 i stan

| **Problem ze zgodnością w wersji 2.0 Standard SCIM** |  **Naprawiono?** | **Napraw daty**  |  
|---|---|---|
| Usługa Azure AD wymaga "/ Standard scim" w katalogu głównym aplikacji przez adres URL punktu końcowego Standard SCIM  | Yes  |  18 grudnia 2018 r. | 
| Atrybuty rozszerzenia używać znaku kropki "."notacji przed nazwami atrybutów zamiast średnikami":" notacji |  Tak  | 18 grudnia 2018 r.  | 
|  Atrybuty wielowartościowe w żądaniach Patch zawiera nieprawidłową ścieżkę składnia filtru | Tak  |  18 grudnia 2018 r.  | 
|  Żądania tworzenia grupy zawierają nieprawidłowy schemat identyfikatora URI | Yes  |  18 grudnia 2018 r.  |  

## <a name="were-the-services-fixes-described-automatically-applied-to-my-pre-existing-scim-app"></a>Zostały poprawki usługi opisane, automatycznie stosowane do mojej istniejących aplikacji Standard SCIM?

Nie. Zgodnie z jej będzie stanowiła istotną zmianę dla aplikacji Standard SCIM, które zostały zakodowane do pracy z starsze zachowanie, zmiany nie zostały automatycznie stosowane do istniejących aplikacji.

Zmiany zostaną zastosowane do wszystkich nowych [aplikacji Standard SCIM spoza galerii](configure-single-sign-on-non-gallery-applications.md) skonfigurowane w witrynie Azure portal po dacie poprawki.

Aby uzyskać informacje dotyczące migracji istniejącego użytkownika zadania inicjowania obsługi administracyjnej do uwzględnienia najnowszych poprawek zobacz następną sekcję.

## <a name="can-i-migrate-an-existing-scim-based-user-provisioning-job-to-include-the-latest-service-fixes"></a>Czy można migrować istniejące Standard SCIM użytkownika oparty na zadania inicjowania obsługi administracyjnej do uwzględnienia najnowszych poprawek usługi?

Tak. Jeśli korzystasz już z tego wystąpienia aplikacji dla logowania jednokrotnego, a chcesz zmigrować istniejące zadanie inicjowania obsługi administracyjnej w celu uwzględnienia najnowszych poprawek, wykonaj poniższą procedurę. Ta procedura opisuje sposób użycia interfejsu API programu Microsoft Graph i Eksploratora interfejsu API Microsoft Graph, aby usunąć stare zadania inicjowania obsługi administracyjnej z istniejącej aplikacji Standard SCIM i utworzyć nową wykazuje nowe zachowanie.

> [!NOTE]
> Jeśli aplikacja jest nadal w fazie projektowania i jeszcze nie został wdrożony dla logowania jednokrotnego lub inicjowanie obsługi użytkowników, najprostszym rozwiązaniem jest usunięcie wpisu aplikacji w **usługi Azure Active Directory > aplikacje dla przedsiębiorstw**części witryny Azure portal i po prostu Dodaj nowy wpis dla aplikacji przy użyciu **tworzenie aplikacji > spoza galerii** opcji. Jest to alternatywa do uruchomionego w poniższej procedurze.
 
1. Zaloguj się do witryny Azure portal pod https://portal.azure.com.
2. W **usługi Azure Active Directory > aplikacje dla przedsiębiorstw** sekcji w witrynie Azure Portal zlokalizuj i wybierz istniejącą aplikację Standard SCIM.
3. W **właściwości** sekcji Twoją istniejącą aplikację Standard SCIM kopiowania **obiektu o identyfikatorze**.
4. W nowym oknie przeglądarki internetowej przejdź do https://developer.microsoft.com/graph/graph-explorer i zaloguj się jako administrator dzierżawy usługi Azure AD, w którym aplikacja zostanie dodany.
5. W Eksploratorze programu Graph uruchom poniższe polecenie, aby zlokalizować identyfikator zadania inicjowania obsługi administracyjnej. Zastąp "[identyfikator obiektu]" w usłudze identyfikator podmiotu zabezpieczeń (identyfikator obiektu:) skopiowane z trzeciego stopnia.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![Pobierz zadania](./media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "wykonywania zadań") 


6. W wynikach skopiować pełny ciąg "ID", który rozpoczyna się od "customappsso" lub "Standard scim".
7. Uruchom poniższe polecenie, aby pobrać konfiguracji Mapowanie atrybutów, dzięki czemu można wykonać kopię zapasową. Użyj tego samego [— identyfikator obiektu] jako przed i Zastąp [identyfikator zadania] z Identyfikatorem zadania inicjowania obsługi administracyjnej, kopiowane w ostatnim kroku.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![Pobieranie schematu](./media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "Pobieranie schematu") 

8. Skopiuj dane wyjściowe JSON w ostatnim kroku i zapisz go w pliku tekstowym. Zawiera wszystkie niestandardowe-mapowania atrybutów dodane do Twojej aplikacji stary i powinna być około kilku tysięcy wierszy JSON.
9. Uruchom poniższe polecenie, aby usunąć zadanie inicjowania obsługi administracyjnej:
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Uruchom poniższe polecenie, aby utworzyć nowe zadanie inicjowania obsługi administracyjnej, który ma najnowsze poprawki usługi.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. W wynikach ostatnim kroku należy skopiować pełny ciąg "ID", który rozpoczyna się od "Standard scim". Opcjonalnie Zastosuj ponownie swoje stare mapowania atrybutów, uruchamiając poniższe polecenie, zastępując [identyfikator nowego zadania] identyfikator zadania został skopiowany, a następnie wprowadzenie który za pomocą pliku JSON dane wyjściowe z kroku 7 # jako treści żądania.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. Wróć do okna przeglądarki usługi pierwszy sieci web i wybierz **aprowizacji** kartę dla swojej aplikacji.
13. Sprawdź konfigurację, a następnie uruchom zadanie inicjowania obsługi administracyjnej. 

## <a name="can-i-add-a-new-non-gallery-app-that-has-the-old-user-provisioning-behavior"></a>Czy można dodać nowej aplikacji spoza galerii, która ma stare aprowizacji zachowania użytkowników?

Tak. Jeśli było kodowane stare zachowanie, które istniały przed poprawki i muszą wdrażać nowe wystąpienie aplikacji, wykonaj poniższą procedurę. Tę procedurę w tym artykule opisano sposób używania interfejsu API programu Microsoft Graph i interfejsu API Microsoft Graph explorer Standard SCIM zadanie inicjowania obsługi administracyjnej, która wykazuje stare zachowanie.
 
1. Zaloguj się do witryny Azure portal pod https://portal.azure.com.
2. w **usługi Azure Active Directory > aplikacje dla przedsiębiorstw > Tworzenie aplikacji** sekcji w witrynie Azure Portal utworzyć nową **spoza galerii** aplikacji.
3. W **właściwości** części Twojej nowej aplikacji niestandardowej kopiowania **obiektu o identyfikatorze**.
4. W nowym oknie przeglądarki internetowej przejdź do https://developer.microsoft.com/graph/graph-explorer i zaloguj się jako administrator dzierżawy usługi Azure AD, w którym aplikacja zostanie dodany.
5. W Eksploratorze programu Graph uruchom poniższe polecenie, aby zainicjować konfiguracji aprowizacji dla aplikacji.
   Zastąp "[identyfikator obiektu]" w usłudze identyfikator podmiotu zabezpieczeń (identyfikator obiektu:) skopiowane z trzeciego stopnia.

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. Wróć do okna przeglądarki usługi pierwszy sieci web i wybierz **aprowizacji** kartę dla swojej aplikacji.
7. Ukończ użytkownika konfigurację aprowizacji, tak jak zwykle.


## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej o aprowizację i anulowanie obsługi do aplikacji SaaS](user-provisioning.md)

