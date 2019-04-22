---
title: 'Samouczek: Konfigurowanie narzędzia LinkedIn Sales Navigator dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników do narzędzia LinkedIn Sales Navigator.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: daveba
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: asmalser-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ce841a45893ebfbb0d9006e6b9eadc77f08a491
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59281611"
---
# <a name="tutorial-configure-linkedin-sales-navigator-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie narzędzia LinkedIn Sales Navigator dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazanie czynności, które należy wykonać w narzędzia LinkedIn Sales Navigator i Azure AD w celu automatycznego aprowizowania lub cofania aprowizacji kont użytkowników z usługi Azure AD do narzędzia LinkedIn Sales Navigator.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz następujące elementy:

* Dzierżawa usługi Azure Active Directory
* Narzędzia LinkedIn Sales Navigator dzierżawy 
* Konto administratora narzędzia LinkedIn Sales Navigator dostęp do Centrum konta LinkedIn

> [!NOTE]
> Usługa Azure Active Directory integruje się z usługą za pomocą narzędzia LinkedIn Sales Navigator [Standard SCIM](http://www.simplecloud.info/) protokołu.

## <a name="assigning-users-to-linkedin-sales-navigator"></a>Przypisywanie użytkowników do narzędzia LinkedIn Sales Navigator

Usługa Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście konta użytkownika automatycznego inicjowania obsługi administracyjnej tylko użytkownicy i grupy, które "przypisano" do aplikacji w usłudze Azure AD będą synchronizowane.

Przed Skonfiguruj i włącz usługę aprowizacji, należy zdecydować, jakie użytkowników i/lub grup w usłudze Azure AD reprezentują użytkowników, którzy muszą mieć dostęp do narzędzia LinkedIn Sales Navigator. Po decyzję, możesz przypisać tych użytkowników narzędzia LinkedIn Sales Navigator, wykonując instrukcje podane w tym miejscu:

[Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-sales-navigator"></a>Ważne wskazówki dotyczące przypisywania użytkowników do narzędzia LinkedIn Sales Navigator

* Zalecane jest, że jeden użytkownik usługi Azure AD można przypisać do narzędzia LinkedIn Sales Navigator do testowania konfiguracji aprowizacji. Później można przypisać dodatkowych użytkowników i/lub grup.

* Podczas przypisywania użytkownika narzędzia LinkedIn Sales Navigator, należy wybrać **użytkownika** roli w oknie dialogowym przydział. Rola "Domyślnego dostępu" nie działa w przypadku inicjowania obsługi administracyjnej.

## <a name="configuring-user-provisioning-to-linkedin-sales-navigator"></a>Konfigurowanie narzędzia LinkedIn Sales Navigator Inicjowanie obsługi użytkowników

W tej sekcji opisano sposób łączenia usługi Azure AD, do konta użytkownika Standard SCIM narzędzia LinkedIn Sales Navigator aprowizujący interfejs API i konfigurowanie inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączyć przypisane do kont użytkowników w narzędzia LinkedIn Sales Navigator na podstawie użytkownika i Przypisanie do grupy w usłudze Azure AD.

> [!TIP]
> Można też włączyć opartej na SAML logowania jednokrotnego dla narzędzia LinkedIn Sales Navigator, wykonując instrukcje podane w [witryny Azure portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatyczną aprowizację, chociaż te dwie funkcje uzupełniają się wzajemnie.

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-sales-navigator-in-azure-ad"></a>Aby skonfigurować automatyczne aprowizacją konta użytkownika narzędzia LinkedIn Sales Navigator w usłudze Azure AD:

Pierwszym krokiem jest do pobrania tokenu dostępu usługi LinkedIn. Jeśli jesteś administratorem przedsiębiorstwa, możesz samodzielnie inicjować obsługę tokenu dostępu. W Centrum konta, przejdź do **ustawienia &gt; ustawienia globalne** , a następnie otwórz **Instalatora Standard SCIM** panelu.

> [!NOTE]
> Jeśli uzyskujesz dostęp do Centrum kont, bezpośrednio, a nie za pośrednictwem łącza, można osiągnąć, go wykonując następujące kroki.

1. Zaloguj się do Centrum konta.

2. Wybierz **administratora &gt; ustawienia administratora** .

3. Kliknij przycisk **zaawansowane integracje** na lewym pasku bocznym. Nastąpi przekierowanie do Centrum konta.

4. Kliknij przycisk **+ Dodaj nową konfigurację Standard SCIM** i postępuj zgodnie z procedurą, wypełniając każdego pola.

    > [!NOTE]
    > Autoassign licencji nie jest włączona, oznacza, że tylko dane użytkownika jest zsynchronizowany.

    ![LinkedIn Sales Navigator inicjowania obsługi administracyjnej](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_1.PNG)

    > [!NOTE]
    > Po włączeniu przydziału autolicense należy pamiętać o wystąpienia aplikacji i typu licencji. Licencje są przypisane w pierwszym wróć, najpierw obsługiwać podstawy, aż wszystkie licencje są pobierane.

    ![LinkedIn Sales Navigator inicjowania obsługi administracyjnej](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_2.PNG)

5. Kliknij przycisk **Wygeneruj token**. Powinien zostać wyświetlony ekran tokenu dostępu w obszarze **token dostępu** pola.

6. Zapisywanie tokenu dostępu do komputera lub Schowka przed opuszczeniem strony.

7. Następnie zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje** sekcji.

8. Jeśli już skonfigurowano narzędzia LinkedIn Sales Navigator dla logowania jednokrotnego, wyszukiwania dla swojego wystąpienia usługi LinkedIn Sales Navigator przy użyciu pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **narzędzia LinkedIn Sales Navigator** w galerii aplikacji. Wybierz narzędzia LinkedIn Sales Navigator z wyników wyszukiwania, a następnie dodaj go do listy aplikacji.

9. Wybierz wystąpienie usługi z narzędzia LinkedIn Sales Navigator, a następnie wybierz **aprowizacji** kartę.

10. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![LinkedIn Sales Navigator inicjowania obsługi administracyjnej](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_3.PNG)

11. Wypełnij następujące pola w obszarze **poświadczeń administratora** :

    * W **adres URL dzierżawy** wprowadź https://api.linkedin.com.

    * W **klucz tajny tokenu** pole, wprowadź token dostępu został wygenerowany w kroku 1 i kliknij przycisk **Testuj połączenie** .

    * Powiadomienie o powodzeniu powinien być widoczny po stronie upperright portalu.

12. Wprowadź adres e-mail osoby lub grupy, który powinien zostać wyświetlony inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem** pola, a następnie zaznacz poniższe pole wyboru.

13. Kliknij pozycję **Zapisz**.

14. W **mapowania atrybutów** Przejrzyj atrybutów użytkowników i grup, które będą synchronizowane z usługi Azure AD z narzędzia LinkedIn Sales Navigator. Należy zauważyć, że atrybuty wybrany jako **zgodne** właściwości, które będą używane do dopasowania kont użytkowników i grup w narzędzia LinkedIn Sales Navigator operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

    ![LinkedIn Sales Navigator inicjowania obsługi administracyjnej](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_4.PNG)

15. Aby włączyć usługi Azure AD, usługi dla narzędzia LinkedIn Sales Navigator inicjowania obsługi administracyjnej, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji

16. Kliknij pozycję **Zapisz**.

Spowoduje to uruchomienie synchronizacji wstępnej użytkowników i/lub grupy przypisane do narzędzia LinkedIn Sales Navigator w sekcji Użytkownicy i grupy. Należy pamiętać, że synchronizacja początkowa może potrwać dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak usługa jest uruchomiona. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi dzienników aktywności, które opisują każdą akcję wykonaną przez usługę aprowizacji w aplikacji narzędzia LinkedIn Sales Navigator.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
