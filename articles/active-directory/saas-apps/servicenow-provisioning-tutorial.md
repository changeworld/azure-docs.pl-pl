---
title: 'Samouczek: Konfigurowanie usługi ServiceNow na potrzeby automatycznego aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD do usługi ServiceNow.
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
ms.date: 08/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85783339c7d1348f598f924f14d9b40cd0c8cd22
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967178"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning-with-azure-active-directory"></a>Samouczek: Konfigurowanie usługi ServiceNow na potrzeby automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w usługi ServiceNow i usłudze Azure AD w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników z usługi Azure AD do usługi ServiceNow.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą ServiceNow, potrzebujesz następujących elementów:

- Subskrypcji usługi Azure AD
- W przypadku usługi ServiceNow — wystąpienie lub dzierżawa usługi ServiceNow w wersji Calgary lub wyższej
- W przypadku usługi ServiceNow Express — wystąpienie usługi ServiceNow Express w wersji Helsinki lub wyższej

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska w wersji próbnej usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="assigning-users-to-servicenow"></a>Przypisywanie użytkowników do usługi ServiceNow

Azure Active Directory używa koncepcji o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi kont użytkowników są synchronizowane tylko użytkownicy i grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem usługi aprowizacji należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji usługi ServiceNow. Po ustaleniu tych użytkowników możesz przypisać je do aplikacji usługi ServiceNow, postępując zgodnie z poniższymi instrukcjami: [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)


> [!IMPORTANT]
>*   Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do usługi ServiceNow w celu przetestowania konfiguracji aprowizacji. Dodatkowych użytkowników i/lub grupy można przypisywać później.
>*   Podczas przypisywania użytkownika do usługi ServiceNow należy wybrać prawidłową rolę użytkownika. Rola "dostęp domyślny" nie działa w przypadku aprowizacji.
>*   Aby uzyskać więcej informacji na temat tworzenia i konfigurowania ról w usłudze Azure AD, Skorzystaj z [](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) tego linku

## <a name="enable-automated-user-provisioning"></a>Włącz automatyczne Inicjowanie obsługi użytkowników

Ta sekcja przeprowadzi Cię przez proces łączenia się z interfejsem API aprowizacji usługi Azure AD do konta użytkownika usługi ServiceNow, a następnie Konfigurując usługę aprowizacji do tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w usłudze usługi ServiceNow na podstawie przypisywania użytkowników i grup w usłudze Azure AD.

> [!TIP]
>Możesz również włączyć funkcję logowania jednokrotnego opartego na protokole SAML dla usługi ServiceNow, postępując zgodnie z instrukcjami podanymi w [Azure Portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji, chociaż te dwie funkcje napadają nawzajem.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurowanie automatycznego inicjowania obsługi konta użytkownika

1. W [Azure Portal](https://portal.azure.com)przejdź do sekcji **Azure Active Directory > aplikacje dla przedsiębiorstw > Wszystkie aplikacje** .

1. Jeśli już skonfigurowano usługi ServiceNow do logowania jednokrotnego, Wyszukaj wystąpienie elementu usługi ServiceNow przy użyciu pola wyszukiwania. W przeciwnym razie wybierz pozycję **Dodaj** i Wyszukaj **usługi ServiceNow** w galerii aplikacji. Wybierz pozycję usługi ServiceNow z wyników wyszukiwania, a następnie dodaj ją do listy aplikacji.

1. Wybierz wystąpienie elementu usługi ServiceNow, a następnie wybierz kartę **Inicjowanie obsługi** .

1. Ustaw tryb **aprowizacji** na **automatyczny**. 

    ![aprowizowanie](./media/servicenow-provisioning-tutorial/provisioning.png)

1. W sekcji poświadczenia administratora wykonaj następujące czynności:
   
    a. W polu tekstowym **Nazwa wystąpienia usługi ServiceNow** wpisz nazwę wystąpienia usługi ServiceNow.

    b. W polu tekstowym **Nazwa użytkownika administratora usługi ServiceNow** wpisz nazwę użytkownika administratora.

    c. W polu tekstowym **hasło administratora usługi ServiceNow** hasło administratora.

1. W Azure Portal kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z aplikacją usługi ServiceNow. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi ServiceNow ma uprawnienia administratora zespołu, a następnie spróbuj ponownie wykonać krok **"poświadczenia administratora"** .

1. Wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji w polu **E-mail powiadomienia** , i zaznacz pole wyboru.

1. Kliknij przycisk **Zapisz.**

1. W sekcji mapowania wybierz pozycję **synchronizuj Azure Active Directory użytkowników do usługi ServiceNow.**

1. W sekcji **mapowania atrybutów** Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do usługi ServiceNow. Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie usługi ServiceNow for Updates. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

1. Aby włączyć usługę Azure AD Provisioning dla usługi usługi ServiceNow, Zmień **stan aprowizacji** na **włączone** w sekcji Ustawienia.

1. Kliknij przycisk **Zapisz.**

Rozpocznie się początkową synchronizację wszystkich użytkowników i/lub grup przypisanych do usługi ServiceNow w sekcji Użytkownicy i grupy. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak usługa jest uruchomiona. Za pomocą sekcji **szczegóły synchronizacji** można monitorować postęp i wykonywać linki do dzienników aktywności aprowizacji, które opisują wszystkie akcje wykonywane przez usługę aprowizacji w aplikacji usługi ServiceNow.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](servicenow-tutorial.md)


