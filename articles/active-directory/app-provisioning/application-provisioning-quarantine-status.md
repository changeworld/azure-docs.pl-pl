---
title: Stan inicjowania obsługi administracyjnej aplikacji kwarantanny | Dokumenty firmy Microsoft
description: Po skonfigurowaniu aplikacji do automatycznego inicjowania obsługi administracyjnej użytkownika dowiedz się, co oznacza stan inicjowania obsługi administracyjnej kwarantanny i jak ją wyczyścić.
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
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 563c049bf3d1606e87db54e3b003dac987594610
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154631"
---
# <a name="application-provisioning-in-quarantine-status"></a>Inicjowanie obsługi administracyjnej aplikacji w stanie kwarantanny

Usługa inicjowania obsługi administracyjnej usługi Azure AD monitoruje kondycję konfiguracji i umieszcza w złej kondycji aplikacje w stanie "kwarantanny". Jeśli większość lub wszystkie wywołania wykonane przeciwko systemowi docelowemu konsekwentnie nie powiedzie się z powodu błędu, na przykład nieprawidłowe poświadczenia administratora, zadanie inicjowania obsługi administracyjnej jest oznaczony jako w kwarantannie.

Podczas kwarantanny częstotliwość cykli przyrostowych jest stopniowo zmniejszana do raz dziennie. Zadanie inicjowania obsługi administracyjnej jest usuwane z kwarantanny po naprawieniu wszystkich błędów i rozpoczęciu następnego cyklu synchronizacji. Jeśli zadanie inicjowania obsługi administracyjnej pozostaje w kwarantannie przez ponad cztery tygodnie, zadanie inicjowania obsługi administracyjnej jest wyłączone (przestaje działać).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Skąd mam wiedzieć, czy moja aplikacja jest poddawana kwarantannie?

Istnieją trzy sposoby sprawdzenia, czy aplikacja jest poddawana kwarantannie:
  
- W witrynie Azure portal przejdź do **aplikacji Azure Active Directory** > **Enterprise** > &lt;*nazwa*&gt; > **obsługi administracyjnej** i przewiń do paska postępu u dołu.  

  ![Pasek stanu inicjowania obsługi administracyjnej ze stanem kwarantanny](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- W witrynie Azure portal przejdź do**dzienników inspekcji** usługi Azure Active Directory > filtruj **w usłudze Aktywność: Kwarantanna** i przejrzyj historię **kwarantanny.** >  Podczas gdy widok na pasku postępu, jak opisano powyżej pokazuje, czy inicjowanie obsługi administracyjnej jest obecnie w kwarantannie, dzienniki inspekcji umożliwiają wyświetlanie historii kwarantanny dla aplikacji. 

- Użyj żądania Microsoft Graph [Pobierz synchronizacjęObbacz](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-get?view=graph-rest-beta&tabs=http) programowo uzyskać stan zadania inicjowania obsługi administracyjnej:

        `GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/`

- Sprawdź swój adres e-mail. Gdy aplikacja jest umieszczana w kwarantannie, wysyłana jest jednorazowa wiadomość e-mail z powiadomieniem. Jeśli przyczyna kwarantanny ulegnie zmianie, zostanie wysłana zaktualizowana wiadomość e-mail z nową przyczyną kwarantanny. Jeśli nie widzisz wiadomości e-mail:

  - Upewnij się, że określono prawidłowe **powiadomienie e-mail** w konfiguracji inicjowania obsługi administracyjnej dla aplikacji.
  - Upewnij się, że w skrzynce odbiorczej wiadomości e-mail z powiadomieniem nie ma filtrowania spamu.
  - Upewnij się, że nie wypisałeś się z wiadomości e-mail.

## <a name="why-is-my-application-in-quarantine"></a>Dlaczego moja aplikacja jest poddawana kwarantannie?

Żądanie programu Microsoft Graph w celu uzyskania stanu zadania inicjowania obsługi administracyjnej zawiera następującą przyczynę kwarantanny:

- `EncounteredQuarantineException`wskazuje, że podano nieprawidłowe poświadczenia. Usługa inicjowania obsługi administracyjnej nie może ustanowić połączenia między systemem źródłowym a systemem docelowym.

- `EncounteredEscrowProportionThreshold`wskazuje, że inicjowanie obsługi administracyjnej przekroczyło próg escrow. Ten warunek występuje, gdy więcej niż 60% zdarzeń inicjowania obsługi administracyjnej nie powiodło się.

- `QuarantineOnDemand`oznacza, że wykryliśmy problem z aplikacją i ręcznie ustawiliśmy go w kwarantannie.

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Jak wyprowadzić aplikację z kwarantanny?

Najpierw rozwiąż problem, który spowodował umieszczenie aplikacji w kwarantannie.

- Sprawdź ustawienia inicjowania obsługi administracyjnej aplikacji, aby upewnić się, że [wprowadzono prawidłowe poświadczenia administratora](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning). Usługa Azure AD musi mieć możliwość ustanowienia zaufania za pomocą aplikacji docelowej. Upewnij się, że wprowadzono prawidłowe poświadczenia, a twoje konto ma niezbędne uprawnienia.

- Przejrzyj [dzienniki inicjowania obsługi administracyjnej,](../reports-monitoring/concept-provisioning-logs.md) aby dokładniej zbadać, jakie błędy powodują kwarantannę i rozwiązać błąd. Uzyskaj dostęp do dzienników inicjowania obsługi administracyjnej w witrynie Azure portal, przechodząc do dzienników inicjowania obsługi administracyjnej aplikacji usługi **Azure Active Directory** &gt; **Enterprise Apps** &gt; **(w wersji zapoznawczej)** w sekcji **Działanie.**

Po rozwiązaniu problemu uruchom ponownie zadanie inicjowania obsługi administracyjnej. Niektóre zmiany w ustawieniach inicjowania obsługi administracyjnej aplikacji, takie jak mapowania atrybutów lub filtry zakresu, automatycznie uruchomią aprowizacji dla Ciebie. Pasek postępu na stronie **inicjowania obsługi administracyjnej** aplikacji wskazuje, kiedy inicjowanie obsługi administracyjnej ostatnio rozpoczęte. Jeśli chcesz ponownie uruchomić zadanie inicjowania obsługi administracyjnej ręcznie, użyj jednej z następujących metod:  

- Użyj witryny Azure portal, aby ponownie uruchomić zadanie inicjowania obsługi administracyjnej. Na stronie **inicjowania obsługi administracyjnej** aplikacji w obszarze **Ustawienia**wybierz pozycję **Wyczyść stan i uruchom ponownie synchronizację** oraz ustaw **stan inicjowania obsługi administracyjnej** **na Włączone**. Ta akcja w pełni uruchamia ponownie usługę inicjowania obsługi administracyjnej, co może zająć trochę czasu. Pełny początkowy cykl zostanie uruchomiony ponownie, co czyści escrows, usuwa aplikację z kwarantanny i czyści wszelkie znaki wodne.

- Użyj programu Microsoft Graph, aby [ponownie uruchomić zadanie inicjowania obsługi administracyjnej](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Będziesz mieć pełną kontrolę nad tym, co ponownie uruchomić. Można wyczyścić escrows (aby ponownie uruchomić licznik escrow, który nalicza się do stanu kwarantanny), wyczyść kwarantannę (aby usunąć aplikację z kwarantanny) lub wyczyścić znaki wodne. Użyj następującego żądania:
 
       `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`
