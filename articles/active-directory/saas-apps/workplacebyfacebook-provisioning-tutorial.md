---
title: 'Samouczek: Konfigurowanie miejsca pracy przez serwis Facebook w celu automatycznego aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Workplace by Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f040ff4c8e59f764676aa6fdd9460ec94641684a
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881797"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie miejsca pracy przez serwis Facebook w celu automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w miejscu pracy przez serwis Facebook i usługę Azure AD w celu automatycznego aprowizacji i dezaktywowania kont użytkowników z usługi Azure AD do miejsca pracy w serwisie Facebook.

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją Workplace by Facebook potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Miejsce pracy w ramach subskrypcji z włączoną obsługą logowania jednokrotnego w serwisie Facebook

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-workplace-by-facebook"></a>Przypisywanie użytkowników do miejsca pracy przez serwis Facebook

Azure Active Directory używa koncepcji o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi kont użytkowników są synchronizowane tylko użytkownicy i grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem usługi aprowizacji należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do miejsca pracy przez aplikację w serwisie Facebook. Po ustaleniu tych użytkowników możesz przypisać je do miejsca pracy przez aplikację Facebook, postępując zgodnie z poniższymi instrukcjami:

[Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-workplace-by-facebook"></a>Ważne porady dotyczące przypisywania użytkowników do miejsca pracy przez serwis Facebook

*   Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do miejsca pracy przez serwis Facebook w celu przetestowania konfiguracji aprowizacji. Dodatkowych użytkowników i/lub grupy można przypisywać później.

*   Podczas przypisywania użytkownika do miejsca pracy przez serwis Facebook należy wybrać prawidłową rolę użytkownika. Rola "dostęp domyślny" nie działa w przypadku aprowizacji.

## <a name="enable-user-provisioning"></a>Włącz Inicjowanie obsługi użytkowników

Ta sekcja przeprowadzi Cię przez proces łączenia usługi Azure AD z miejscem pracy przez interfejs API aprowizacji konta użytkownika w serwisie Facebook i konfigurowania usługi aprowizacji w celu tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w miejscu pracy przez usługę Facebook w oparciu o użytkowników i grupę przypisanie w usłudze Azure AD.

>[!Tip]
>Możesz również włączyć logowanie jednokrotne oparte na protokole SAML w miejscu pracy przez serwis Facebook, postępując zgodnie z instrukcjami podanymi w [Azure Portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji, chociaż te dwie funkcje napadają nawzajem.

### <a name="to-configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Aby skonfigurować Inicjowanie obsługi konta użytkownika w miejscu pracy przez serwis Facebook w usłudze Azure AD:

Celem tej sekcji jest zaprojektowanie sposobu włączania obsługi kont użytkowników Active Directory w miejscu pracy przez serwis Facebook.

Usługa Azure AD obsługuje automatyczne synchronizowanie szczegółowych informacji o koncie przypisanych użytkowników do miejsca pracy przez serwis Facebook. Ta automatyczna synchronizacja umożliwia miejsce pracy przez serwis Facebook w celu uzyskania danych potrzebnych do autoryzowania dostępu do użytkowników przed podjęciem próby zalogowania się po raz pierwszy. W przypadku odwołania dostępu do usługi Azure AD użytkownicy z miejsca pracy w serwisie Facebook są również nieudostępniani.

1. W [Azure Portal](https://portal.azure.com)przejdź do sekcji **Azure Active Directory** > aplikacje dla**wszystkich aplikacji** w**przedsiębiorstwie** > .

2. Jeśli masz już skonfigurowany obszar roboczy w serwisie Facebook do logowania jednokrotnego, Wyszukaj swoje wystąpienie miejsca pracy przez serwis Facebook przy użyciu pola wyszukiwania. W przeciwnym razie wybierz pozycję **Dodaj** i Wyszukaj **miejsce pracy przez serwis Facebook** w galerii aplikacji. Wybierz pozycję miejsce pracy według serwisu Facebook z wyników wyszukiwania, a następnie dodaj ją do listy aplikacji.

3. Wybierz swoje wystąpienie miejsca pracy przez serwis Facebook, a następnie wybierz kartę **Inicjowanie obsługi** .

4. Ustaw **tryb aprowizacji** na **automatyczny**. 

    ![aprowizowanie](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

5. W sekcji **poświadczenia administratora** wprowadź token dostępu z miejsca pracy przez administratora serwisu Facebook i ustaw wartość adresu URL dzierżawy na `https://www.facebook.com/scim/v1/` . Zobacz te [instrukcje](https://developers.facebook.com/docs/workplace/integrations/custom-integrations/apps) dotyczące tworzenia tokenu dostępu do miejsca pracy. 

6. W Azure Portal kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z miejscem pracy przez aplikację w serwisie Facebook. Jeśli połączenie nie powiedzie się, upewnij się, że miejsce pracy przez konto w serwisie Facebook ma uprawnienia administratora zespołu.

7. Wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji w polu **E-mail powiadomienia** , i zaznacz pole wyboru.

8. Kliknij przycisk **Zapisz.**

9. W sekcji mapowania wybierz pozycję **synchronizuj Azure Active Directory użytkowników w miejscu pracy przez serwis Facebook.**

10. W sekcji **mapowania atrybutów** Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do miejsca pracy w serwisie Facebook. Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania do kont użytkowników w miejscu pracy przez serwis Facebook dla operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

11. Aby włączyć usługę Azure AD Provisioning w miejscu pracy przez serwis Facebook, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

12. Kliknij przycisk **Zapisz.**

Aby uzyskać więcej informacji na temat konfigurowania automatycznej aprowizacji, zobacz[https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)

Możesz teraz utworzyć konto testowe. Poczekaj aż do 20 minut, aby sprawdzić, czy konto zostało zsynchronizowane z miejscem pracy w serwisie Facebook.

> [!NOTE]
> Pracujemy ściśle z miejscem pracy przez zespół w serwisie Facebook, aby upewnić się, że aplikacja usługi Azure AD została zatwierdzona i spełnia nowe wytyczne.   

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](workplacebyfacebook-tutorial.md)
