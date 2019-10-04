---
title: Stan aprowizacji aplikacji dla kwarantanny | Microsoft Docs
description: Po skonfigurowaniu aplikacji do automatycznego inicjowania obsługi administracyjnej, Dowiedz się, co to jest stan aprowizacji środków kwarantanny i jak go wyczyścić.
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
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 704e217cd7ddea988b6a9812627aba8c8468fb73
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71955499"
---
# <a name="application-provisioning-in-quarantine-status"></a>Inicjowanie obsługi aplikacji w stanie kwarantanny

Usługa Azure AD Provisioning monitoruje kondycję konfiguracji i umieszcza w stanie "Kwarantanna" aplikacje w złej kondycji. Jeśli większość lub wszystkie wywołania związane z systemem docelowym są spójne niepowodzeniem z powodu błędu, na przykład nieprawidłowe poświadczenia administratora, zadanie aprowizacji zostanie oznaczone jako w kwarantannie.

Podczas kwarantanny Częstotliwość cykli przyrostowych jest stopniowo zmniejszana do raz dziennie. Zadanie aprowizacji jest usuwane z kwarantanny po naprawieniu wszystkich błędów i uruchomieniu następnego cyklu synchronizacji. Jeśli zadanie aprowizacji pozostaje w kwarantannie przez ponad cztery tygodnie, zadanie aprowizacji jest wyłączone (zatrzymuje działanie).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Jak mogę sprawdzić, czy moja aplikacja znajduje się w kwarantannie?

Istnieją trzy sposoby, aby sprawdzić, czy aplikacja znajduje się w kwarantannie:
  
- W Azure Portal przejdź do **Azure Active Directory** >  aplikacje dla**przedsiębiorstw** >  @ no__t-4*Nazwa aplikacji*&gt; @ no__t-7**Provisioning** i przewiń na pasek postępu u dołu.  

  ![Pasek stanu aprowizacji przedstawiający stan kwarantanny](media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- Użyj żądania Microsoft Graph [Get synchronizationJob](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-get?view=graph-rest-beta&tabs=http) , aby programowo pobrać stan zadania aprowizacji:

        `GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/`

- Sprawdź swój adres e-mail. Gdy aplikacja zostanie umieszczona w kwarantannie, zostanie wysłana jednorazowa wiadomość e-mail z powiadomieniem. W przypadku zmiany przyczyny kwarantanny zostanie wysłana zaktualizowana wiadomość e-mail z informacją o nowej przyczynie kwarantanny. Jeśli nie widzisz wiadomości e-mail:

  - Upewnij się, że podano prawidłowy **adres E-mail powiadomienia** w konfiguracji aprowizacji dla aplikacji.
  - Upewnij się, że w skrzynce odbiorczej wiadomości e-mail z powiadomieniem nie ma filtrowania spamu.
  - Upewnij się, że nie masz subskrypcji wiadomości e-mail.

## <a name="why-is-my-application-in-quarantine"></a>Dlaczego moja aplikacja jest poddana kwarantannie?

Microsoft Graph żądanie pobrania stanu zadania aprowizacji wskazuje następujące przyczyny kwarantanny:

- `EncounteredQuarantineException` wskazuje, że podano nieprawidłowe poświadczenia. Usługa aprowizacji nie może nawiązać połączenia między systemem źródłowym i systemem docelowym.

- wartość `EncounteredEscrowProportionThreshold` wskazuje, że inicjowanie obsługi przekroczyło próg Escrow. Ten stan występuje, gdy więcej niż 60% zdarzeń aprowizacji nie powiodło się.

- `QuarantineOnDemand` oznacza, że wykryto problem z aplikacją i ręcznie ustawił ją na kwarantannę.

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Jak mogę uzyskać mojej aplikacji z kwarantanny?

Najpierw należy rozwiązać problem, który spowodował umieszczenie aplikacji w kwarantannie.

- Sprawdź ustawienia aprowizacji aplikacji, aby upewnić się, że [wprowadzono prawidłowe poświadczenia administratora](configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning). Usługa Azure AD musi być w stanie ustanowić relację zaufania z aplikacją docelową. Upewnij się, że wprowadzono prawidłowe poświadczenia, a Twoje konto ma wymagane uprawnienia.

- Przejrzyj [dzienniki aprowizacji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) , aby dokładniej zbadać, jakie błędy powodują Kwarantanna i rozwiązać problem. Uzyskaj dostęp do dzienników aprowizacji w Azure Portal, przechodząc do **Azure Active Directory** &gt; **Enterprise Apps** &gt; **Provisioning Logs (wersja zapoznawcza)** w sekcji **działanie** .

Po rozwiązaniu problemu należy ponownie uruchomić zadanie aprowizacji. Pewne zmiany ustawień aprowizacji aplikacji, takie jak mapowania atrybutów lub filtry zakresu, będą automatycznie ponownie uruchamiać Inicjowanie obsługi. Pasek postępu na stronie **aprowizacji** aplikacji wskazuje czas ostatniego uruchomienia aprowizacji. Jeśli konieczne jest ręczne ponowne uruchomienie zadania aprowizacji, należy użyć jednej z następujących metod:  

- Użyj Azure Portal, aby ponownie uruchomić zadanie aprowizacji. Na stronie **aprowizacji** aplikacji w obszarze **Ustawienia**wybierz pozycję **Wyczyść stan i ponownie uruchom synchronizację** , a następnie ustaw **stan aprowizacji** na **włączone**. Ta akcja powoduje w pełni ponowne uruchomienie usługi aprowizacji, która może zająć trochę czasu. Pełny cykl początkowy zostanie uruchomiony ponownie, co oznacza, że usługa Escrow usunie aplikację z kwarantanny i wyczyści wszystkie znaki wodne.

- Użyj Microsoft Graph, aby [ponownie uruchomić zadanie aprowizacji](https://docs.microsoft.com/en-us/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Będziesz mieć pełną kontrolę nad tym, co zostało ponownie uruchomione. Możesz wybrać opcję wyczyszczenia usługi Escrow (aby ponownie uruchomić licznik Escrow, który naliczy na status kwarantanny), wyczyścić opcję kwarantanny (w celu usunięcia aplikacji z kwarantanny) lub wyczyścić znaki wodne. Użyj następującego żądania:
 
       `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`