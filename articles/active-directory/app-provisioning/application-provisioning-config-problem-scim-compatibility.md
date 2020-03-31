---
title: Znane problemy ze zgodnością protokołu SCIM 2.0 — usługa Azure AD
description: Jak rozwiązać typowe problemy ze zgodnością protokołów napotkane podczas dodawania aplikacji nietajfy, która obsługuje scim 2.0 do usługi Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dff0d4f8f0062c00351b60174c63d9c19bdfa15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522938"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Znane problemy i rozwiązania z zgodnością protokołu SCIM 2.0 usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD

Usługa Azure Active Directory (Azure AD) może automatycznie aprowizować użytkowników i grupy do dowolnej aplikacji lub systemu, która jest obsługiwana przez usługę sieci web z interfejsem zdefiniowanym w [specyfikacji protokołu System dla zarządzania tożsamościami między domenami (SCIM) 2.0](https://tools.ietf.org/html/draft-ietf-scim-api-19). 

Obsługa usługi Azure AD dla protokołu SCIM 2.0 jest opisana w [using System for Cross-Domain Identity Management (SCIM) do automatycznego inicjowania obsługi użytkowników i grup z usługi Azure Active Directory do aplikacji](use-scim-to-provision-users-and-groups.md), która wyświetla listę określonych części protokołu, który implementuje w celu automatycznego aprowizowania użytkowników i grup z usługi Azure AD do aplikacji obsługujących scim 2.0.

W tym artykule opisano bieżące i wcześniejsze problemy z obsługą obsługi administracyjnej usługi Azure AD zgodnie z protokołem SCIM 2.0 i jak obejść te problemy.

> [!IMPORTANT]
> Najnowsza aktualizacja klienta SCIM usługi inicjowania obsługi administracyjnej dla użytkowników usługi Azure AD została dokonana 18 grudnia 2018 r. Ta aktualizacja rozwiązała znane problemy ze zgodnością wymienione w poniższej tabeli. Zobacz często zadawane pytania poniżej, aby uzyskać więcej informacji na temat tej aktualizacji.

## <a name="scim-20-compliance-issues-and-status"></a>Problemy ze zgodnością i stan scim 2.0

| **Problem ze zgodnością z programem SCIM 2.0** |  **Stałe?** | **Data poprawek**  |  
|---|---|---|
| Usługa Azure AD wymaga, aby "/scim" znajdować się w katalogu głównym adresu URL punktu końcowego scim aplikacji  | Tak  |  18 grudnia 2018 r. | 
| Atrybuty rozszerzenia używają kropki "." notacji przed nazwami atrybutów zamiast dwukropka ":" notacja |  Tak  | 18 grudnia 2018 r.  | 
|  Żądania poprawek dla atrybutów wielowartościowych zawierają nieprawidłową składnię filtru ścieżki | Tak  |  18 grudnia 2018 r.  | 
|  Żądania tworzenia grupy zawierają nieprawidłowy identyfikator URI schematu | Tak  |  18 grudnia 2018 r.  |  

## <a name="were-the-services-fixes-described-automatically-applied-to-my-pre-existing-scim-app"></a>Czy opisane poprawki usług zostały automatycznie zastosowane do mojej istniejącej aplikacji SCIM?

Nie. Ponieważ stanowiłoby to przełomową zmianę w aplikacjach SCIM, które zostały zakodowane do pracy ze starszym zachowaniem, zmiany nie zostały automatycznie zastosowane do istniejących aplikacji.

Zmiany są stosowane do wszystkich nowych [aplikacji scim innych](../manage-apps/configure-single-sign-on-non-gallery-applications.md) niż galeria skonfigurowane w witrynie Azure portal, po dacie poprawki.

Aby uzyskać informacje na temat migracji wcześniej istniejącego zadania inicjowania obsługi administracyjnej użytkownika w celu uwzględnienia najnowszych poprawek, zobacz następną sekcję.

## <a name="can-i-migrate-an-existing-scim-based-user-provisioning-job-to-include-the-latest-service-fixes"></a>Czy mogę przeprowadzić migrację istniejącego zadania inicjowania obsługi administracyjnej użytkownika opartego na usłudze SCIM, aby uwzględnić najnowsze poprawki usługi?

Tak. Jeśli już używasz tego wystąpienia aplikacji do logowania jednokrotnego i musisz przeprowadzić migrację istniejącego zadania inicjowania obsługi administracyjnej, aby uwzględnić najnowsze poprawki, wykonaj poniższą procedurę. W tej procedurze opisano sposób używania interfejsu API programu Microsoft Graph i eksploratora interfejsu API programu Microsoft Graph w celu usunięcia starego zadania inicjowania obsługi administracyjnej z istniejącej aplikacji SCIM i utworzenia nowego, który wykazuje nowe zachowanie.

> [!NOTE]
> Jeśli aplikacja jest nadal w fazie rozwoju i nie została jeszcze wdrożona dla logowania jednokrotnego lub inicjowania obsługi administracyjnej użytkownika, najprostszym rozwiązaniem jest usunięcie wpisu aplikacji w sekcji **Azure Active Directory > Enterprise Applications** w witrynie Azure portal i po prostu dodaj nowy wpis dla aplikacji przy użyciu opcji **Utwórz aplikację > Nietajna.** Jest to alternatywa dla uruchomienia poniższej procedury.
 
1. Zaloguj się do https://portal.azure.comwitryny Azure portal w .
2. W sekcji **Azure Active Directory > Enterprise Applications** w witrynie Azure portal znajdź i wybierz istniejącą aplikację SCIM.
3. W sekcji **Właściwości** istniejącej aplikacji SCIM skopiuj **identyfikator obiektu**.
4. W nowym oknie przeglądarki sieci https://developer.microsoft.com/graph/graph-explorer Web przejdź do i zaloguj się jako administrator dzierżawy usługi Azure AD, w którym dodawana jest aplikacja.
5. W Eksploratorze wykresu uruchom poniższe polecenie, aby zlokalizować identyfikator zadania inicjowania obsługi administracyjnej. Zamień "[object-id]" identyfikatorem jednostki usługi (identyfikatorem obiektu) skopiowanym z trzeciego kroku.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![Zdobądź oferty pracy](media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "Zdobądź oferty pracy") 


6. W wynikach skopiuj pełny ciąg "ID", który zaczyna się od "customappsso" lub "scim".
7. Uruchom poniższe polecenie, aby pobrać konfigurację mapowania atrybutów, aby utworzyć kopię zapasową. Użyj tego samego [object-id] jak poprzednio i zastąp [job-id] identyfikatorem zadania inicjowania obsługi administracyjnej skopiowanym z ostatniego kroku.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![Pobierz schemat](media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "Pobierz schemat") 

8. Skopiuj dane wyjściowe JSON z ostatniego kroku i zapisz go w pliku tekstowym. Zawiera wszystkie niestandardowe mapowania atrybutów, które zostały dodane do starej aplikacji i powinny być około kilku tysięcy wierszy JSON.
9. Uruchom poniższe polecenie, aby usunąć zadanie inicjowania obsługi administracyjnej:
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Uruchom poniższe polecenie, aby utworzyć nowe zadanie inicjowania obsługi administracyjnej, które ma najnowsze poprawki usługi.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. W wynikach ostatniego kroku skopiuj pełny ciąg "ID", który zaczyna się od "scim". Opcjonalnie ponownie zastosuj stare mapowania atrybutów, uruchamiając polecenie poniżej, zastępując [new-job-id] nowym identyfikatorem zadania, który właśnie skopiowano, i wprowadzając dane wyjściowe JSON z kroku #7 jako treść żądania.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. Wróć do pierwszego okna przeglądarki sieci Web i wybierz kartę **Inicjowanie obsługi administracyjnej** dla aplikacji.
13. Sprawdź konfigurację, a następnie uruchom zadanie inicjowania obsługi administracyjnej. 

## <a name="can-i-add-a-new-non-gallery-app-that-has-the-old-user-provisioning-behavior"></a>Czy mogę dodać nową aplikację niebędącą galerią, która ma stare zachowanie inicjowania obsługi administracyjnej użytkownika?

Tak. Jeśli zakodowano aplikację do starego zachowania, które istniały przed poprawkami i trzeba wdrożyć nowe wystąpienie, wykonaj poniższą procedurę. W tej procedurze opisano sposób używania interfejsu API programu Microsoft Graph i eksploratora interfejsu API programu Microsoft Graph do tworzenia zadania inicjowania obsługi administracyjnej systemu SCIM, które wykazuje stare zachowanie.
 
1. Zaloguj się do https://portal.azure.comwitryny Azure portal w .
2. w sekcji **Azure Active Directory > Enterprise Applications > Create application** w witrynie Azure portal utwórz nową aplikację **niebędącą galerią.**
3. W sekcji **Właściwości** nowej aplikacji niestandardowej skopiuj **identyfikator obiektu**.
4. W nowym oknie przeglądarki sieci https://developer.microsoft.com/graph/graph-explorer Web przejdź do i zaloguj się jako administrator dzierżawy usługi Azure AD, w którym dodawana jest aplikacja.
5. W Eksploratorze wykresu uruchom poniższe polecenie, aby zainicjować konfigurację inicjowania obsługi administracyjnej aplikacji.
   Zamień "[object-id]" identyfikatorem jednostki usługi (identyfikatorem obiektu) skopiowanym z trzeciego kroku.

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. Wróć do pierwszego okna przeglądarki sieci Web i wybierz kartę **Inicjowanie obsługi administracyjnej** dla aplikacji.
7. Wykonaj konfigurację inicjowania obsługi administracyjnej użytkownika, jak zwykle.


## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o inicjowaniu obsługi administracyjnej i usuwaniu obsługi administracyjnej aplikacji SaaS](user-provisioning.md)

