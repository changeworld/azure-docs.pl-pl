---
title: 'Samouczek: Konfigurowanie usługi LinkedIn podniesienie poziomu do automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników do wyniesienia rozgrywek LinkedIn.
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
ms.date: 03/28/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: adefb0c88e88a8bfb4b896c0788654e478ff4555
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65963693"
---
# <a name="tutorial-configure-linkedin-elevate-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usługi LinkedIn podniesienie poziomu do automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazanie czynności, które należy wykonać w podniesienie poziomu usługi LinkedIn i Azure AD do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników z usługi Azure AD do wyniesienia rozgrywek LinkedIn.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz następujące elementy:

* Dzierżawa usługi Azure Active Directory
* Dzierżawca usługi LinkedIn podnieść poziom
* Konto administratora w LinkedIn podnieść poziom dzięki dostępowi do Centrum konta LinkedIn

> [!NOTE]
> Usługa Azure Active Directory integruje się z usługą LinkedIn podniesienie poziomu przy użyciu [Standard SCIM](http://www.simplecloud.info/) protokołu.

## <a name="assigning-users-to-linkedin-elevate"></a>Przypisywanie użytkowników do wyniesienia rozgrywek LinkedIn

Usługa Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście konta użytkownika automatycznego inicjowania obsługi administracyjnej tylko użytkownicy i grupy, które "przypisano" do aplikacji w usłudze Azure AD będą synchronizowane.

Przed Skonfiguruj i włącz usługę aprowizacji, należy zdecydować, jakie użytkowników i/lub grup w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do wyniesienia rozgrywek LinkedIn. Po decyzję, możesz przypisać użytkowników do wyniesienia rozgrywek LinkedIn, zgodnie z instrukcjami w tym miejscu:

[Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-elevate"></a>Ważne wskazówki dotyczące przypisywania użytkowników do wyniesienia rozgrywek LinkedIn

* Zalecane jest, pojedynczego użytkownika usługi Azure AD można przypisać do wyniesienia rozgrywek LinkedIn, aby przetestować konfigurację aprowizacji. Później można przypisać dodatkowych użytkowników i/lub grup.

* Podczas przypisywania użytkowników do wyniesienia rozgrywek LinkedIn, musisz wybrać **użytkownika** roli w oknie dialogowym przydział. Rola "Domyślnego dostępu" nie działa w przypadku inicjowania obsługi administracyjnej.

## <a name="configuring-user-provisioning-to-linkedin-elevate"></a>Konfiguracja aprowizacji użytkowników do wyniesienia rozgrywek LinkedIn

Ta sekcja przeprowadzi Cię przez połączenie usługi Azure AD do podniesienia LinkedIn Standard SCIM konta użytkownika aprowizujący interfejs API i konfigurowanie inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączyć przypisane do kont użytkowników w LinkedIn podniesienie poziomu na podstawie użytkownika i przypisanie do grupy w usłudze Azure AD.

**Porada:** Można też włączone opartej na SAML logowania jednokrotnego dla podniesienia poziomu usługi LinkedIn, postępując zgodnie z instrukcjami dostarczone w [witryny Azure portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatyczną aprowizację, chociaż te dwie funkcje uzupełniają się wzajemnie.

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-elevate-in-azure-ad"></a>Aby skonfigurować automatyczne aprowizowaniem kont użytkowników do usługi LinkedIn podniesienia uprawnień w usłudze Azure AD:

Pierwszym krokiem jest do pobrania tokenu dostępu usługi LinkedIn. Jeśli jesteś administratorem przedsiębiorstwa, możesz samodzielnie inicjować obsługę tokenu dostępu. W Centrum konta, przejdź do **ustawienia &gt; ustawienia globalne** , a następnie otwórz **Instalatora Standard SCIM** panelu.

> [!NOTE]
> Jeśli uzyskujesz dostęp do Centrum kont, bezpośrednio, a nie za pośrednictwem łącza, można osiągnąć, go wykonując następujące kroki.

1. Zaloguj się do Centrum konta.

2. Wybierz **administratora &gt; ustawienia administratora** .

3. Kliknij przycisk **zaawansowane integracje** na lewym pasku bocznym. Nastąpi przekierowanie do Centrum konta.

4. Kliknij przycisk **+ Dodaj nową konfigurację Standard SCIM** i postępuj zgodnie z procedurą, wypełniając każdego pola.

    > [!NOTE]
    > Autoassign licencji nie jest włączona, oznacza, że tylko dane użytkownika jest zsynchronizowany.

    ![LinkedIn podnieść poziom udostępniania](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate1.PNG)

    > [!NOTE]
    > Po włączeniu przydziału autolicense należy pamiętać o wystąpienia aplikacji i typu licencji. Licencje są przypisane w pierwszym wróć, najpierw obsługiwać podstawy, aż wszystkie licencje są pobierane.

    ![LinkedIn podnieść poziom udostępniania](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate2.PNG)

5. Kliknij przycisk **Wygeneruj token**. Powinien zostać wyświetlony ekran tokenu dostępu w obszarze **token dostępu** pola.

6. Zapisywanie tokenu dostępu do komputera lub Schowka przed opuszczeniem strony.

7. Następnie zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje** sekcji.

8. Jeśli już skonfigurowano podniesienie poziomu usługi LinkedIn dla logowania jednokrotnego, wyszukiwania dla swojego wystąpienia usługi LinkedIn podniesienie poziomu przy użyciu pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **podnieść LinkedIn** w galerii aplikacji. Wybierz podniesienie poziomu usługi LinkedIn w wynikach wyszukiwania, a następnie dodaj go do listy aplikacji.

9. Wybierz wystąpienie usługi LinkedIn podniesienie poziomu, a następnie wybierz **aprowizacji** kartę.

10. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![LinkedIn podnieść poziom udostępniania](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate3.PNG)

11. Wypełnij następujące pola w obszarze **poświadczeń administratora** :

    * W **adres URL dzierżawy** wprowadź `https://api.linkedin.com`.

    * W **klucz tajny tokenu** pole, wprowadź token dostępu został wygenerowany w kroku 1 i kliknij przycisk **Testuj połączenie** .

    * Powiadomienie o powodzeniu powinien być widoczny po stronie upperright portalu.

12. Wprowadź adres e-mail osoby lub grupy, który powinien zostać wyświetlony inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem** pola, a następnie zaznacz poniższe pole wyboru.

13. Kliknij pozycję **Zapisz**.

14. W **mapowania atrybutów** Przejrzyj atrybuty użytkowników i grup, które będą synchronizowane z usługi Azure AD do wyniesienia rozgrywek LinkedIn. Należy zauważyć, że atrybuty wybrany jako **zgodne** właściwości, które będą używane do dopasowania kont użytkowników i grup w podniesienie poziomu usługi LinkedIn dla operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

    ![LinkedIn podnieść poziom udostępniania](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate4.PNG)

15. Aby włączyć usługi Azure AD, inicjowania obsługi usługi LinkedIn podniesienie poziomu, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji

16. Kliknij pozycję **Zapisz**.

Spowoduje to uruchomienie synchronizacji wstępnej użytkowników i/lub grupy przypisane do wyniesienia rozgrywek LinkedIn w sekcji Użytkownicy i grupy. Należy pamiętać, że synchronizacja początkowa może potrwać dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak usługa jest uruchomiona. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi dzienników aktywności, zawierające wszystkie akcje wykonywane przez usługę aprowizacji w aplikacji podniesienie poziomu usługi LinkedIn.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
