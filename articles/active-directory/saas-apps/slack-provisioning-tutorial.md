---
title: 'Samouczek: Konfigurowanie Slack do automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego aprowizacji i usuwanie kont użytkowników Slack.
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
ms.openlocfilehash: 036112027fcf210f0ac2ff1e631c8b0bd4b5e9ef
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65964396"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Slack do automatycznej aprowizacji użytkowników

Celem tego samouczka jest Wam kroki należy wykonać w Slack i usługi Azure AD, aby automatycznie aprowizować i anulować obsługę kont użytkowników z usługi Azure AD do Slack.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz następujące elementy:

* Dzierżawa usługi Azure Active Directory
* Slack dzierżawy z [oraz plan](https://aadsyncfabric.slack.com/pricing) lub lepiej nie są włączone
* Konto użytkownika Slack z uprawnieniami administratora zespołu

Uwaga: Inicjowanie obsługi administracyjnej integracji usługi Azure AD opiera się na [API Standard SCIM Slack](https://api.slack.com/scim), który jest dostępny w usłudze Slack, teams na znak Plus zaplanować lub Lepsza.

## <a name="assigning-users-to-slack"></a>Przypisywanie użytkowników do Slack

Usługa Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście konta użytkownika automatycznego inicjowania obsługi administracyjnej tylko użytkownicy i grupy, które "przypisano" do aplikacji w usłudze Azure AD będą synchronizowane.

Przed Skonfiguruj i włącz usługę aprowizacji, należy zdecydować, jakie użytkowników i/lub grup w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji Slack. Po decyzję, możesz przypisać użytkowników do aplikacji Slack, zgodnie z instrukcjami w tym miejscu:

[Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Slack

* Zalecane jest jeden użytkownik usługi Azure AD jest przypisane do Slack do testowania konfiguracji aprowizacji. Później można przypisać dodatkowych użytkowników i/lub grup.

* Podczas przypisywania użytkowników do Slack, należy wybrać **użytkownika** lub rola "Group" w oknie dialogowym przydział. Rola "Domyślnego dostępu" nie działa w przypadku inicjowania obsługi administracyjnej.

## <a name="configuring-user-provisioning-to-slack"></a>Konfigurowaniem aprowizowania użytkowników w Slack 

Ta sekcja przeprowadzi Cię przez połączenie usługi Azure AD do konta użytkownika w Slack aprowizujący interfejs API i konfigurowanie inicjowania obsługi usługi do tworzenia, aktualizacji, a następnie wyłącz konta użytkowników przypisane w Slack, na podstawie przypisania użytkowników i grup w usłudze Azure AD.

**Porada:** Można też włączyć opartej na SAML logowania jednokrotnego dla Slack, wykonując instrukcje podane w [witryny Azure portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatyczną aprowizację, chociaż te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Aby skonfigurować automatyczne aprowizowaniem kont użytkowników do Slack w usłudze Azure AD:

1. W [witryny Azure portal](https://portal.azure.com), przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje** sekcji.

2. Jeśli już skonfigurowano Slack dla logowania jednokrotnego, wyszukiwania dla swojego wystąpienia usługi Slack, korzystając z pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **Slack** w galerii aplikacji. Wybierz Slack z wyników wyszukiwania, a następnie dodaj go do listy aplikacji.

3. Wybierz wystąpienie programu Slack, a następnie wybierz **aprowizacji** kartę.

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

   ![Slack, inicjowanie obsługi administracyjnej](./media/slack-provisioning-tutorial/Slack1.PNG)

5. W obszarze **poświadczeń administratora** kliknij **Autoryzuj**. Spowoduje to otwarcie okna dialogowego Slack autoryzacji w nowym oknie przeglądarki.

6. W nowym oknie Zaloguj Slack przy użyciu konta administratora zespołu. w oknie dialogowym autoryzacji wynikowe, wybierz Slack zespołu, który chcesz włączyć udostępnianie, a następnie wybierz **Autoryzuj**. Po zakończeniu wróć do witryny Azure portal, aby zakończyć konfigurowanie inicjowania obsługi administracyjnej.

    ![Okno dialogowe autoryzacji](./media/slack-provisioning-tutorial/Slack3.PNG)

7. W witrynie Azure portal kliknij pozycję **Testuj połączenie** aby upewnij się, Azure AD można połączyć się z aplikacji Slack. Jeśli połączenie nie powiedzie się, upewnij się, że usługi Slack konto ma uprawnienia administratora zespołu i spróbuj ponownie w kroku "Autoryzuj".

8. Wprowadź adres e-mail osoby lub grupy, który powinien zostać wyświetlony inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem** pola, a następnie zaznacz poniższe pole wyboru.

9. Kliknij pozycję **Zapisz**.

10. W sekcji mapowania, wybierz **synchronizacji Azure użytkownicy usługi Active Directory do Slack**.

11. W **mapowania atrybutów** Przejrzyj atrybutów użytkowników, które będą synchronizowane z usługi Azure AD z usługą Slack. Należy zauważyć, że atrybuty wybrany jako **zgodne** właściwości, które będą używane do dopasowania kont użytkowników w Slack w przypadku operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

12. Aby włączyć usługi Azure AD, inicjowania obsługi usługi Slack, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji

13. Kliknij pozycję **Zapisz**.

Spowoduje to uruchomienie synchronizacji wstępnej użytkowników i/lub grupy przypisane do Slack w sekcji Użytkownicy i grupy. Należy pamiętać, że synchronizacja początkowa może potrwać dłużej niż kolejne synchronizacje, które występują co około 10 minut, tak długo, jak usługa jest uruchomiona. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do udostępniania raportów działań, które opisują każdą akcję wykonaną przez usługę aprowizacji w aplikacji Slack.

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>[Opcjonalnie] Konfigurowanie grupy obiektu aprowizacji Slack

Opcjonalnie można włączyć, aprowizacja obiekty grupy z usługi Azure AD w celu Slack. To różni się od "przypisywanie grup użytkowników", w tym, że rzeczywiste grupy obiektów poza jej składowych będą replikowane z usługi Azure AD do Slack. Na przykład jeśli istnieje grupa o nazwie "My" w usłudze Azure AD, identyczne grupy o nazwie "My" zostanie utworzony wewnątrz Slack.

### <a name="to-enable-provisioning-of-group-objects"></a>Aby włączyć udostępnianie obiektów grupy:

1. W sekcji mapowania, wybierz **synchronizacji Azure grup usługi Active Directory do Slack**.

2. W bloku mapowanie atrybutu ustawić włączone Yes (tak).

3. W **mapowania atrybutów** Przejrzyj atrybuty grupy, które będą synchronizowane z usługi Azure AD z usługą Slack. Należy zauważyć, że atrybuty wybrany jako **zgodne** właściwości, które będą używane do dopasowania grup w Slack dla operacji aktualizowania. 

4. Kliknij pozycję **Zapisz**.

Ten wynik w wszystkie obiekty grupy przypisane do Slack w **użytkowników i grup** sekcji, w pełni są synchronizowane z usługi Azure AD do Slack. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi dzienników aktywności, które opisują każdą akcję wykonaną przez usługę aprowizacji w aplikacji Slack.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Podczas konfigurowania firmy Slack **displayName** atrybutu, należy zwrócić uwagę na następujące zachowania:

  * Wartości nie są unikatowe w całości, (np. 2 użytkownicy mogą mieć tę samą nazwę wyświetlaną)

  * Obsługuje znaków innych niż angielski, miejsca do magazynowania, wielkość liter. 
  
  * Dozwolone znaki interpunkcyjne obejmuje kropki, podkreślenia, łączniki, apostrofy, nawiasy kwadratowe (np. **([{}])**) oraz separatory (np. **, /;**).
  
  * Aktualizuje tylko, jeśli te dwa ustawienia zostały skonfigurowane w miejscu pracy firmy Slack/organizacji - **synchronizowanie profilu jest włączona** i **użytkownicy nie mogą zmieniać ich nazwy wyświetlanej**.
  
  * Firmy Slack **userName** atrybutu musi być w obszarze 21 znaków i mieć unikatową wartość.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
