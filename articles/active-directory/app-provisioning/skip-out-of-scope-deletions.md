---
title: Pomiń usuwanie użytkowników poza zakresem | Dokumenty firmy Microsoft
description: Dowiedz się, jak zastąpić domyślne zachowanie usuwania inicjowania obsługi administracyjnej użytkowników poza zakres.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1668c022a0f067a381ba09b397c7d38c99ad074
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522453"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope"></a>Pomiń usuwanie kont użytkowników, które wykraczają poza zakres

Domyślnie aparat inicjowania obsługi administracyjnej usługi Azure AD usuwa lub wyłącza użytkowników, którzy wychodzą poza zakres. Jednak w niektórych scenariuszach, takich jak Workday to AD Użytkownik przychodzący inicjowania obsługi administracyjnej to zachowanie może nie być oczekiwane i można zastąpić to zachowanie domyślne.  

W tym przewodniku opisano sposób używania interfejsu API programu Microsoft Graph i eksploratora interfejsu API programu Microsoft Graph do ustawiania flagi ***SkipOutOfScopeDeletions,*** która steruje przetwarzaniem kont, które wykraczają poza zakres. 
* Jeśli ***SkipOutOfScopeDeletions*** jest ustawiona na 0 (false), a następnie konta, które wykraczają poza zakres zostanie wyłączony w docelowych
* Jeśli ***SkipOutOfScopeDeletions*** jest ustawiona na 1 (true), a następnie konta, które wykraczają poza zakres nie zostanie wyłączona w docelowych Ta flaga jest ustawiona na poziomie *aplikacji inicjowania obsługi administracyjnej* i można skonfigurować za pomocą interfejsu API wykresu. 

Ponieważ ta konfiguracja jest szeroko używana z aplikacją *inicjowania obsługi administracyjnej użytkowników usługi Workday to Active Directory,* poniższe kroki obejmują zrzuty ekranu aplikacji Workday. Jednak może to być również używane ze **wszystkimi innymi aplikacjami,** takimi jak (ServiceNow, Salesforce, Dropbox itp.).

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Krok 1: Pobieranie identyfikatora głównego usługi aplikacji inicjowania obsługi administracyjnej (identyfikator obiektu)

1. Uruchom [witrynę Azure portal](https://portal.azure.com)i przejdź do sekcji Właściwości aplikacji inicjującej inicjowanie obsługi administracyjnej. Na przykład, jeśli chcesz wyeksportować *mapowanie aplikacji aprowizacji użytkowników usługi AD do* usługi AD, przejdź do sekcji Właściwości tej aplikacji. 
1. W sekcji Właściwości aplikacji do inicjowania obsługi administracyjnej skopiuj wartość guid skojarzoną z *polem Identyfikator obiektu.* Ta wartość jest również nazywana **ServicePrincipalId** aplikacji i będzie używana w operacjach Graph Explorer.

   ![Identyfikator jednostki usługi aplikacji workday](./media/skip-out-of-scope-deletions/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>Krok 2: Zaloguj się do Eksploratora wykresów firmy Microsoft

1. Uruchamianie [Eksploratora wykresów firmy Microsoft](https://developer.microsoft.com/graph/graph-explorer)
1. Kliknij przycisk "Zaloguj się za pomocą firmy Microsoft" i zaloguj się przy użyciu poświadczeń administratora globalnego usługi Azure AD lub administratora aplikacji.

    ![Logowanie do wykresu](./media/skip-out-of-scope-deletions/wd_export_02.png)

1. Po pomyślnym zalogowaniu zobaczysz szczegóły konta użytkownika w lewym okienku.

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>Krok 3: Uzyskaj istniejące poświadczenia aplikacji i szczegóły łączności

W Eksploratorze wykresu firmy Microsoft uruchom następującą kwerendę GET zastępującą [servicePrincipalId] **z servicePrincipalId** wyodrębniony z [kroku 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```

   ![GET kwerenda zadania](./media/skip-out-of-scope-deletions/skip-03.png)

Skopiuj odpowiedź do pliku tekstowego. Będzie wyglądać jak tekst JSON pokazano poniżej, z wartościami wyróżnionymi na żółto specyficzne dla wdrożenia. Dodaj do końca linie wyróżnione na zielono i zaktualizuj hasło połączenia Workday wyróżnione na niebiesko. 

   ![UZYSKAJ odpowiedź na zadania](./media/skip-out-of-scope-deletions/skip-04.png)

Oto blok JSON, aby dodać do mapowania. 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>Krok 4: Aktualizowanie punktu końcowego wpisów tajnych flagą SkipOutOfScopeDeletions

W Eksploratorze wykresu uruchom poniższe polecenie, aby zaktualizować punkt końcowy wpisów tajnych flagą ***SkipOutOfScopeDeletions.*** 

W poniższym adresie URL zamień [servicePrincipalId] **na adres ServicePrincipalId** wyodrębniony z [kroku 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id). 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
Skopiuj zaktualizowany tekst z kroku 3 do "Treści żądania" i ustaw nagłówek "Typ zawartości" na "application/json" w "Nagłówki żądań". 

   ![Wniosek PUT](./media/skip-out-of-scope-deletions/skip-05.png)

Kliknij na "Uruchom zapytanie". 

Należy uzyskać dane wyjściowe jako "Sukces — kod stanu 204". 

   ![Odpowiedź PUT](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>Krok 5: Sprawdź, czy użytkownicy poza zakresem nie są wyłączeni

Można przetestować wyniki tej flagi w oczekiwanym zachowaniu, aktualizując reguły zakresu, aby pominąć określonego użytkownika. W poniższym przykładzie wykluczamy pracownika o identyfikatorze 21173 (który był wcześniej w zakresie) przez dodanie nowej reguły określania zakresu: 

   ![Przykład zakresu](./media/skip-out-of-scope-deletions/skip-07.png)

W następnym cyklu inicjowania obsługi administracyjnej usługa inicjowania obsługi administracyjnej usługi Azure AD określi, że użytkownik 21173 wyszedł poza zakres i jeśli właściwość SkipOutOfScopeDeletions jest włączona, reguła synchronizacji dla tego użytkownika wyświetli komunikat, jak pokazano poniżej: 

   ![Przykład zakresu](./media/skip-out-of-scope-deletions/skip-08.png)


