---
title: 'Samouczek: Konfigurowanie usług Salesforce do automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i usługą Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 49384b8b-3836-4eb1-b438-1c46bb9baf6f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 9ece2e0f56522582e53e827ac6db7f33b1c8cb7e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432552"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usług Salesforce do automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazanie kroków wymaganych do wykonania w usłudze Salesforce a usługą Azure AD, aby automatycznie aprowizować i anulować obsługę użytkowników kont z usługi Azure AD do usługi Salesforce.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz następujące elementy:

*   Dzierżawy usługi Azure Active directory
*   Dzierżawy Salesforce.com

>[!IMPORTANT] 
>Jeśli używasz konta w wersji próbnej Salesforce.com, będzie on mógł skonfigurować aprowizację użytkowników automatycznych. Konta wersji próbnej nie mają włączony, dopóki nabywane są niezbędne dostęp do interfejsu API. Można obejść to ograniczenie, przy użyciu BEZPŁATNEJ [konta dewelopera](https://developer.salesforce.com/signup) do ukończenia tego samouczka.

Jeśli używasz środowisku piaskownicy usługi Salesforce, zobacz [samouczkiem integracji usługi Salesforce piaskownicy](https://go.microsoft.com/fwLink/?LinkID=521879).

## <a name="assigning-users-to-salesforce"></a>Przypisywanie użytkowników do usługi Salesforce

Usługa Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście Inicjowanie obsługi administracyjnej konta użytkowników są synchronizowane tylko użytkowników i grup, które "przypisano" do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i włącz usługę aprowizacji, musisz zdecydować, którzy użytkownicy lub grupy w usłudze Azure AD muszą mieć dostęp do aplikacji Salesforce. Po utworzeniu tej decyzji, tych użytkowników można przypisać do aplikacji Salesforce, postępując zgodnie z instrukcjami w [przypisać użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Ważne wskazówki dotyczące przypisywania użytkowników do usługi Salesforce

*   Zalecane jest, że jeden użytkownik usługi Azure AD jest przypisany do usługi Salesforce do testowania konfiguracji aprowizacji. Później można przypisać dodatkowych użytkowników i/lub grup.

*  Podczas przypisywania użytkowników do usługi Salesforce, należy wybrać poprawnej roli użytkownika. Rola "Domyślnego dostępu" nie działa w przypadku inicjowania obsługi administracyjnej

    > [!NOTE]
    > Ta aplikacja importuje role niestandardowe z usługi Salesforce, jako część procesu inicjowania obsługi administracyjnej klienta, warto wybrać podczas przypisywania użytkowników

## <a name="enable-automated-user-provisioning"></a>Włączanie użytkownika automatyczne Inicjowanie obsługi administracyjnej.

Ta sekcja przeprowadzi Cię przez połączenie usługi Azure AD do konta użytkownika w usłudze Salesforce aprowizujący interfejs API i konfigurowanie inicjowania obsługi usługi do tworzenia, aktualizacji, a następnie wyłącz kont użytkowników przypisanych do usługi Salesforce na podstawie przypisania użytkowników i grup w usłudze Azure AD.

>[!Tip]
>Można też włączyć opartej na SAML logowania jednokrotnego dla usług Salesforce, postępując zgodnie z instrukcjami dostarczone w [witryny Azure portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatyczną aprowizację, chociaż te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurowanie automatycznej konta aprowizacji użytkowników

Jest celem tej sekcji omówiono sposób włączania aprowizacji użytkowników, kont użytkowników usługi Active Directory do usługi Salesforce.

1. W [witryny Azure portal](https://portal.azure.com), przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje** sekcji.

1. Jeśli już skonfigurowano usługi Salesforce dla logowania jednokrotnego, wyszukiwanie wystąpienia programu Salesforce za pomocą pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **Salesforce** w galerii aplikacji. Wybierz usługi Salesforce z wyników wyszukiwania, a następnie dodaj go do listy aplikacji.

1. Wybierz wystąpienia programu Salesforce, a następnie wybierz **aprowizacji** kartę.

1. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![aprowizowanie](./media/salesforce-provisioning-tutorial/provisioning.png)

1. W obszarze **poświadczeń administratora** sekcji, skonfiguruj następujące ustawienia konfiguracji:
   
    a. W **nazwa użytkownika administratora** pola tekstowego, typ, nazwa, która ma konta usługi Salesforce **Administrator systemu** profilu w witrynie Salesforce.com przypisane.
   
    b. W **hasło administratora** pole tekstowe, wpisz hasło dla tego konta.

1. Aby bezpieczeństwa Salesforce należy uzyskać token, otwórz nową kartę i zaloguj do tego samego konta administratora usługi Salesforce. W prawym górnym rogu strony, kliknij swoją nazwę użytkownika, a następnie kliknij przycisk **ustawienia**.

     ![Włączanie automatycznej aprowizacji użytkowników](./media/salesforce-provisioning-tutorial/sf-my-settings.png "Włączanie automatycznej aprowizacji użytkowników")

1. W okienku nawigacji po lewej stronie kliknij **Moje dane osobowe** rozwiń sekcję powiązane, a następnie kliknij przycisk **zresetować moje tokenu zabezpieczeń**.
  
    ![Włączanie automatycznej aprowizacji użytkowników](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "Włączanie automatycznej aprowizacji użytkowników")

1. Na **zresetować tokenu zabezpieczeń** kliknij **zresetować tokenu zabezpieczeń** przycisku.

    ![Włączanie automatycznej aprowizacji użytkowników](./media/salesforce-provisioning-tutorial/sf-reset-token.png "Włączanie automatycznej aprowizacji użytkowników")

1. Sprawdź skrzynkę odbiorczą poczty e-mail skojarzony z tym kontem administratora. Wyszukaj wiadomości e-mail z usługami Salesforce.com, który zawiera nowy token.

1. Skopiuj token, przejdź do okna w usłudze Azure AD i wklej go do **klucz tajny tokenu** pola.

1. **Adres URL dzierżawy** powinny być wprowadzane w przypadku wystąpienia programu Salesforce znajduje się w chmurze dla instytucji rządowych Salesforce. W przeciwnym razie jest to opcjonalne. Wprowadź adres URL dzierżawy w formacie "https://\<your wystąpienia\>. my.salesforce.com," Zastępowanie \<your wystąpienia\> nazwą wystąpienia usługi Salesforce.

1. W witrynie Azure portal kliknij pozycję **Testuj połączenie** aby zapewnić usłudze Azure AD można połączyć się z aplikacji Salesforce.

1. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia o błędach aprowizacji i zaznacz poniższe pole wyboru.

1. Kliknij przycisk **Zapisz.**  
    
1.  W sekcji mapowania, wybierz **synchronizacji Azure użytkownicy usługi Active Directory do usługi Salesforce.**

1. W **mapowania atrybutów** Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do usługi Salesforce. Należy zauważyć, że atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w usłudze Salesforce dla operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

1. Aby włączyć usługi Azure AD, inicjowania obsługi usługi dla usług Salesforce, zmień **stanie aprowizacji** do **na** w sekcji Ustawienia

1. Kliknij przycisk **Zapisz.**

Spowoduje to uruchomienie synchronizacji wstępnej użytkowników i/lub grupy przypisane do usługi Salesforce w sekcji Użytkownicy i grupy. Należy pamiętać, że synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak usługa jest uruchomiona. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi dzienników aktywności, które opisują każdą akcję wykonaną przez usługę aprowizacji w aplikacji Salesforce.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-tutorial)
