---
title: 'Samouczek: Konfigurowanie usługi ServiceNow dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Informacje o sposobie automatycznego aprowizowania lub cofania aprowizacji kont użytkowników z usługi Azure AD do usługi ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19b3e4cc5ba4bc0173721947bd1e1a680ca7b3a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60869844"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning-with-azure-active-directory"></a>Samouczek: Konfigurowanie usługi ServiceNow dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory

Celem tego samouczka jest pokazanie czynności, które należy wykonać w usługi ServiceNow i Azure AD w celu automatycznego aprowizowania lub cofania aprowizacji kont użytkowników z usługi Azure AD do usługi ServiceNow.

> [!NOTE]
> W tym samouczku opisano łącznika, który został zbudowany na podstawie usługi aprowizacji użytkownika usługi Azure AD. Ważne szczegółowe informacje na temat tej usługi nie, jak działa i często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą ServiceNow, potrzebujesz następujących elementów:

- Subskrypcji usługi Azure AD
- W przypadku usługi ServiceNow — wystąpienie lub dzierżawa usługi ServiceNow w wersji Calgary lub wyższej
- W przypadku usługi ServiceNow Express — wystąpienie usługi ServiceNow Express w wersji Helsinki lub wyższej

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska usługi Azure AD w wersji próbnej, możesz skorzystać z [miesięcznej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).


## <a name="assigning-users-to-servicenow"></a>Przypisywanie użytkowników do usługi ServiceNow

Usługa Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście Inicjowanie obsługi administracyjnej konta użytkowników są synchronizowane tylko użytkowników i grup, które "przypisano" do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i włącz usługę aprowizacji, musisz zdecydować, jakie użytkowników i/lub grup w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji usługi ServiceNow. Po decyzję, możesz przypisać użytkowników do aplikacji usługi ServiceNow, zgodnie z instrukcjami w tym miejscu: [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)


> [!IMPORTANT]
>*   Zalecane jest, pojedynczego użytkownika usługi Azure AD jest przypisany do usługi ServiceNow do testowania konfiguracji aprowizacji. Później można przypisać dodatkowych użytkowników i/lub grup.
>*   Podczas przypisywania użytkowników do usługi ServiceNow, należy wybrać poprawnej roli użytkownika. Rola "Domyślnego dostępu" nie działa w przypadku inicjowania obsługi administracyjnej.

## <a name="enable-automated-user-provisioning"></a>Włączanie użytkownika automatyczne Inicjowanie obsługi administracyjnej.

Ta sekcja przeprowadzi Cię przez połączenie usługi Azure AD do konta użytkownika usługi ServiceNow firmy aprowizujący interfejs API i konfigurowanie inicjowania obsługi usługi do tworzenia, aktualizacji, a następnie wyłącz konta użytkowników przypisane w usługi ServiceNow na podstawie przypisania użytkowników i grup w usłudze Azure AD.

> [!TIP]
>Można też włączyć opartej na SAML logowania jednokrotnego dla usługi ServiceNow, wykonując instrukcje podane w [witryny Azure portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatyczną aprowizację, chociaż te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurowanie automatycznej konta aprowizacji użytkowników

1. W [witryny Azure portal](https://portal.azure.com), przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje** sekcji.

1. Jeśli już skonfigurowano usługi ServiceNow dla logowania jednokrotnego, wyszukiwania dla swojego wystąpienia usługi ServiceNow przy użyciu pola wyszukiwania. W przeciwnym razie wybierz **Dodaj** i wyszukaj **ServiceNow** w galerii aplikacji. Wybierz usługi ServiceNow z wyników wyszukiwania, a następnie dodaj go do listy aplikacji.

1. Wybierz wystąpienie usługi ServiceNow, a następnie wybierz **aprowizacji** kartę.

1. Ustaw **aprowizacji** tryb **automatyczne**. 

    ![aprowizowanie](./media/servicenow-provisioning-tutorial/provisioning.png)

1. W sekcji poświadczenia administratora wykonaj następujące czynności:
   
    a. W **nazwa wystąpienia usługi ServiceNow** polu tekstowym wpisz nazwę wystąpienia usługi ServiceNow.

    b. W **nazwy użytkownika administratora usługi ServiceNow** polu tekstowym wpisz nazwę użytkownika administratora.

    c. W **usługi ServiceNow, hasło administratora** pola tekstowego, hasło administratora.

1. W witrynie Azure portal kliknij pozycję **Testuj połączenie** aby zapewnić usłudze Azure AD można połączyć się z aplikacji usługi ServiceNow. Jeśli połączenie nie powiedzie się, upewnij się, Twoje konto usługi ServiceNow ma uprawnienia administratora zespołu i spróbuj **"Poświadczeń administratora"** krok ponownie.

1. Wprowadź adres e-mail osoby lub grupy, który powinien zostać wyświetlony inicjowania obsługi administracyjnej powiadomienia o błędach w **wiadomość E-mail z powiadomieniem** pola, a następnie zaznacz pole wyboru.

1. Kliknij przycisk **Zapisz.**

1. W sekcji mapowania, wybierz **synchronizacji Azure użytkownicy usługi Active Directory do usługi ServiceNow.**

1. W **mapowania atrybutów** Przejrzyj atrybutów użytkowników, które są synchronizowane z usługi Azure AD z usługą ServiceNow. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w usługi ServiceNow dla operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

1. Aby włączyć usługi Azure AD, inicjowania obsługi usługi dla usługi ServiceNow, zmień **stanie aprowizacji** do **na** w sekcji Ustawienia

1. Kliknij przycisk **Zapisz.**

Rozpoczyna się wstępna synchronizacja użytkowników i/lub grupy przypisane do usługi ServiceNow w sekcji Użytkownicy i grupy. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak usługa jest uruchomiona. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi dzienników aktywności, które opisują każdą akcję wykonaną przez usługę aprowizacji w aplikacji usługi ServiceNow.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](servicenow-tutorial.md)


