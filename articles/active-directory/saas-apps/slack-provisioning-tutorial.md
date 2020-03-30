---
title: 'Samouczek: Inicjowanie obsługi administracyjnej dla slacka — usługa Azure AD'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania z obsługi administracyjnej kont użytkowników w slacku.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdc912c2df435f9b7e591d7c5475e126e6b0aeb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062833"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie slacka do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest pokazanie kroki, które należy wykonać w Slack i azure AD, aby automatycznie aprowizować i de-provision kont użytkowników z usługi Azure AD do Slack.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące elementy:

* Dzierżawa usługi Azure Active Directory
* Najemca Slack z [planem Plus](https://aadsyncfabric.slack.com/pricing) lub lepiej włączony
* Konto użytkownika w slacku z uprawnieniami administratora zespołu

Uwaga: Integracja inicjowania obsługi administracyjnej usługi Azure AD opiera się na [interfejsie API slack SCIM](https://api.slack.com/scim), który jest dostępny dla zespołów Slack w planie Plus lub lepiej.

## <a name="assigning-users-to-slack"></a>Przypisywanie użytkowników do slacka

Usługa Azure Active Directory używa pojęcia o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej konta użytkownika tylko użytkownicy i grupy, które zostały "przypisane" do aplikacji w usłudze Azure AD zostaną zsynchronizowane.

Przed skonfigurowaniem i włączeniem usługi inicjowania obsługi administracyjnej należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji Slack. Po podjęciu decyzji możesz przypisać tych użytkowników do aplikacji Slack, postępując zgodnie z instrukcjami tutaj:

[Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>Ważne wskazówki dotyczące przypisywania użytkowników do slacka

* Zaleca się, że jeden użytkownik usługi Azure AD jest przypisany do Slack do testowania konfiguracji inicjowania obsługi administracyjnej. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do Slack, należy wybrać **rolę Użytkownika** lub "Grupuj" w oknie dialogowym przypisania. Rola "Dostęp domyślny" nie działa w przypadku inicjowania obsługi administracyjnej.

## <a name="configuring-user-provisioning-to-slack"></a>Konfigurowanie inicjowania obsługi administracyjnej użytkownika w slacku 

W tej sekcji przewodnik po połączeniu usługi Azure AD z interfejsem API inicjowania obsługi administracyjnej konta użytkownika slacka i konfigurowaniu usługi inicjowania obsługi administracyjnej w celu tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w slacku na podstawie przypisania użytkownika i grupy w usłudze Azure AD.

**Wskazówka:** Można również włączyć samoostylowanie jednokrotne dla slacka oparte na saml, postępując zgodnie z instrukcjami podanymi w [witrynie Azure portal.](https://portal.azure.com) Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej konta użytkownika w slacku w usłudze Azure AD:

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do sekcji **Azure Active Directory > Enterprise Apps > wszystkie aplikacje.**

2. Jeśli skonfigurowano już slack do logowania jednokrotnego, wyszukaj wystąpienie Slack przy użyciu pola wyszukiwania. W przeciwnym razie wybierz pozycję **Dodaj** i wyszukaj **pozycję Zapas w** galerii aplikacji. Wybierz pozycję Slack z wyników wyszukiwania i dodaj go do listy aplikacji.

3. Wybierz wystąpienie Slack, a następnie wybierz kartę **Inicjowanie obsługi administracyjnej.**

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

   ![Niesprętające zapasy](./media/slack-provisioning-tutorial/slack1.png)

5. W sekcji **Poświadczenia administratora** kliknij pozycję **Autoryzuj**. Spowoduje to otwarcie okna dialogowego autoryzacji slack w nowym oknie przeglądarki.

6. W nowym oknie zaloguj się do Slacka przy użyciu konta administratora zespołu. w oknie dialogowym autoryzacji wynikowej wybierz zespół Slack, dla którego chcesz włączyć inicjowanie obsługi administracyjnej, a następnie wybierz pozycję **Autoryzuj**. Po zakończeniu wróć do witryny Azure Portal, aby ukończyć konfigurację inicjowania obsługi administracyjnej.

    ![Okno dialogowe autoryzacji](./media/slack-provisioning-tutorial/slackauthorize.png)

7. W witrynie Azure portal kliknij pozycję **Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z aplikacją Slack. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Slack ma uprawnienia administratora zespołu i spróbuj ponownie wykonać krok "Autoryzuj".

8. Wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej w polu **Wiadomości e-mail z powiadomieniem,** i zaznacz pole wyboru poniżej.

9. Kliknij przycisk **Zapisz**.

10. W sekcji Mapowania wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z slackiem**.

11. W sekcji **Mapowania atrybutów** przejrzyj atrybuty użytkownika, które będą synchronizowane z usługi Azure AD na Slack. Należy zauważyć, że atrybuty wybrane jako **pasujące** właściwości będą używane do dopasowania kont użytkowników w Slack dla operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić wszelkie zmiany.

12. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla slacka, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia**

13. Kliknij przycisk **Zapisz**.

Spowoduje to rozpoczęcie początkowej synchronizacji wszystkich użytkowników i/lub grup przypisanych do Slack w sekcji Użytkownicy i grupy. Należy zauważyć, że synchronizacja początkowa potrwa dłużej niż kolejne synchronizacje, które występują co około 10 minut, o ile usługa jest uruchomiona. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i śledzić łącza do raportów aktywności inicjowania obsługi administracyjnej, które opisują wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej w aplikacji Slack.

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>[Opcjonalnie] Konfigurowanie inicjowania obsługi administracyjnej obiektu grupy w slacku

Opcjonalnie można włączyć inicjowanie obsługi administracyjnej obiektów grupy z usługi Azure AD do Slack. Różni się to od "przypisywania grup użytkowników", w tym rzeczywisty obiekt grupy oprócz jego członków będą replikowane z usługi Azure AD do Slack. Na przykład jeśli masz grupę o nazwie "Moja grupa" w usłudze Azure AD, identyczna grupa o nazwie "Moja grupa" zostanie utworzona wewnątrz Slack.

### <a name="to-enable-provisioning-of-group-objects"></a>Aby włączyć inicjowanie obsługi administracyjnej obiektów grupy:

1. W sekcji Mapowania wybierz pozycję **Synchronizuj grupy usługi Azure Active Directory z slackiem**.

2. W bloku Mapowanie atrybutów ustaw włączone jako Tak.

3. W sekcji **Mapowania atrybutów** przejrzyj atrybuty grupy, które będą synchronizowane z usługi Azure AD na Slack. Należy zauważyć, że atrybuty wybrane jako **pasujące** właściwości będą używane do dopasowania grup w Slack dla operacji aktualizacji. 

4. Kliknij przycisk **Zapisz**.

Powoduje to, że wszystkie obiekty grupy przypisane do Slack w **użytkownicy i grupy** sekcji są w pełni zsynchronizowane z usługi Azure AD do Slack. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i obserwować łącza do dzienników aktywności inicjowania obsługi administracyjnej, które opisują wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej w aplikacji Slack.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Ograniczenia złącza

* Podczas konfigurowania atrybutu **nazwa wyświetlania** Slacka należy pamiętać o następujących zachowaniach:

  * Wartości nie są całkowicie unikatowe (np. 2 użytkowników może mieć taką samą nazwę wyświetlaną)

  * Obsługuje znaki inne niż angielskie, spacje, wielkie litery. 
  
  * Dozwolona interpunkcja obejmuje kropki, podkreślenia, łączniki, apostrofy, nawiasy (np. **( [ { } ] )**) i separatory (np. **, / ;**
  
  * Tylko aktualizacje, jeśli te dwa ustawienia są skonfigurowane w miejscu pracy/organizacji Slacka - **synchronizacja profilu jest włączona,** a **użytkownicy nie mogą zmienić swojej nazwy wyświetlanej**.
  
* Atrybut **userName** slacka musi mieć mniej niż 21 znaków i mieć unikatową wartość.

* Slack umożliwia tylko dopasowanie do atrybutów **userName** i **email**.  

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
