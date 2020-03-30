---
title: 'Samouczek: Konfigurowanie usługi ServiceNow do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak automatycznie aprowizować i wyrównywać konta użytkowników z usługi Azure AD do ServiceNow.
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
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: 9e93d4b3f1880f2ac56a32a7b85aa6801fb7c14e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205110"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usługi ServiceNow do automatycznego inicjowania obsługi administracyjnej przez użytkowników

W tym samouczku opisano kroki, które należy wykonać zarówno w usłudze ServiceNow, jak i usłudze Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego inicjowania obsługi administracyjnej użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie aprowizacji i usuwania z przepisów użytkowników i grup [do ServiceNow](https://www.servicenow.com/) przy użyciu usługi Azure AD inicjowania obsługi administracyjnej usługi. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Tworzenie użytkowników w ServiceNow
> * Usuń użytkowników w SerwisieTeraz, gdy nie wymagają już dostępu
> * Synchronizacja atrybutów użytkowników między usługą Azure AD i ServiceNow
> * Aprowizuj grupy i członkostwa w grupach w Serwisie Teraz
> * [Logowanie jednokrotne](servicenow-tutorial.md) do ServiceNow (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) do konfigurowania obsługi administracyjnej (np. administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub administrator globalny). 
* [A ServiceNow wystąpienie](https://www.servicenow.com/) Calgary lub wyższej
* [A ServiceNow Express wystąpienie](https://www.servicenow.com/) Helsinek lub wyższej
* Konto użytkownika w usłudze Teraz z rolą administratora

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia inicjowania obsługi administracyjnej
1. Dowiedz [się, jak działa usługa inicjowania obsługi administracyjnej](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Określ, kto będzie w [zakresie inicjowania obsługi administracyjnej](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Określ, jakie dane mają [być mapowane między usługą Azure AD a usługą ServiceNow](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie usługi ServiceNow do obsługi inicjowania obsługi administracyjnej przy pomocy usługi Azure AD

1. Zidentyfikuj nazwę wystąpienia ServiceNow. Nazwę wystąpienia można znaleźć w adresie URL używanym do uzyskiwania dostępu do usługi ServiceNow. W poniższym przykładzie nazwa wystąpienia jest dev35214.

![Wystąpienie ServiceNow](media/servicenow-provisioning-tutorial/servicenow_instance.png)

    
2. Uzyskaj poświadczenia dla administratora w ServiceNow. Przejdź do profilu użytkownika w ServiceNow i sprawdź, czy użytkownik ma rolę administratora. 

![Rola administratora Usługi ServiceNow](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)

## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie usługi ServiceNow z galerii aplikacji usługi Azure AD

Dodaj ServiceNow z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie inicjowania obsługi administracyjnej do ServiceNow. Jeśli wcześniej skonfigurowano servicenow dla jednocześnie, możesz użyć tej samej aplikacji. Jednak zaleca się utworzenie oddzielnej aplikacji podczas testowania integracji początkowo. Dowiedz się więcej o dodawaniu aplikacji z galerii [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określanie, kto będzie w zakresie inicjowania obsługi administracyjnej 

Usługa inicjowania obsługi administracyjnej usługi Azure AD umożliwia zakres, który będzie aprowidzony na podstawie przypisania do aplikacji i lub na podstawie atrybutów użytkownika / grupy. Jeśli wybierzesz zakres, który będzie aprowizowany do aplikacji na podstawie przypisania, można użyć następujących [kroków,](../manage-apps/assign-user-or-group-access-portal.md) aby przypisać użytkowników i grupy do aplikacji. Jeśli zdecydujesz się na zakres, który będzie aprowizować wyłącznie na podstawie atrybutów użytkownika lub grupy, można użyć filtru zakresu, jak opisano [w tym miejscu](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Podczas przypisywania użytkowników i grup do ServiceNow należy wybrać rolę inną niż **Dostęp domyślny**. Użytkownicy z rolą dostępu domyślnego są wykluczeni z inicjowania obsługi administracyjnej i będą oznaczane jako nieuwzdrowsze w dziennikach inicjowania obsługi administracyjnej. Jeśli jedyną rolą dostępową w aplikacji jest domyślna rola dostępu, można [zaktualizować manifest aplikacji,](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) aby dodać dodatkowe role. 

* Zacznij od małych. Przetestuj z małym zestawem użytkowników i grup przed wprowadzeniem do wszystkich. Gdy zakres inicjowania obsługi administracyjnej jest ustawiony na przypisanych użytkowników i grup, można kontrolować to, przypisując jednego lub dwóch użytkowników lub grup do aplikacji. Gdy zakres jest ustawiony dla wszystkich użytkowników i grup, można określić [filtr zakresu na podstawie atrybutów](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Krok 5. Konfigurowanie automatycznego inicjowania obsługi administracyjnej usługi ServiceNow 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w aplikacji TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla usługi ServiceNow w usłudze Azure AD:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **ServiceNow**.

    ![Link do usługi ServiceNow na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** wprowadź poświadczenia administratora ServiceNow i nazwę użytkownika. Kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z usługą ServiceNow. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto ServiceNow ma uprawnienia administratora i spróbuj ponownie.

    ![Inicjowania obsługi](./media/servicenow-provisioning-tutorial/provisioning.png)

6. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Wybierz **pozycję Zapisz**.

8. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z usługą.**

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do ServiceNow w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w ServiceNow dla operacji aktualizacji. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)musisz upewnić się, że interfejs API ServiceNow obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

10. W sekcji **Mapowania** wybierz pozycję **Synchronizuj grupy usługi Azure Active Directory z usługą.**

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do ServiceNow w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **właściwości dopasowania** są używane do dopasowania grup w ServiceNow dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

12. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla usługi ServiceNow, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić ServiceNow, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

15. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkowy cykl synchronizacji wszystkich użytkowników i grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Początkowy cykl trwa dłużej niż kolejne cykle, które występują co około 40 minut, tak długo, jak usługa inicjowania obsługi administracyjnej usługi Azure AD jest uruchomiona. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu inicjowania obsługi administracyjnej użyj następujących zasobów do monitorowania wdrożenia:

1. Użyj [dzienników inicjowania obsługi administracyjnej,](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) aby określić, którzy użytkownicy zostali pomyślnie lub bezskutecznie udostępnieni
2. Sprawdź [pasek postępu,](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) aby zobaczyć stan cyklu inicjowania obsługi administracyjnej i jak blisko jest do zakończenia
3. Jeśli konfiguracja inicjowania obsługi administracyjnej wydaje się być w stanie złej kondycji, aplikacja przejdzie do kwarantanny. Dowiedz się więcej o stanach kwarantanny [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów
* **InvalidLookupReference:** Podczas inicjowania obsługi administracyjnej niektórych atrybutów, takich jak Dział i Lokalizacja w ServiceNow, wartości muszą już istnieć w tabeli odwołań w ServiceNow. Na przykład w tabeli **wstawianie nazwy tabeli** w usłudze ServiceNow mogą znajdować się dwie lokalizacje (Seattle, Los Angeles) i trzy działy (Sprzedaż, Finanse, Marketing). Jeśli spróbujesz aprowizować użytkownika, gdzie jego dział jest "Sprzedaż" i lokalizacja jest "Seattle" zostanie pomyślnie zainicjowane. Jeśli spróbujesz aprowizować użytkownika z działem "Sprzedaż" i lokalizacją "LA", użytkownik nie będzie aprowizować. Lokalizacja LA musi zostać dodana do tabeli odwołań w ServiceNow lub atrybut użytkownika w usłudze Azure AD musi zostać zaktualizowany, aby dopasować format w ServiceNow. 
* **EntryJoiningPropertyValueIsMisssing:** Przejrzyj [mapowania atrybutów,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) aby zidentyfikować pasujący atrybut. Ta wartość musi być obecny na użytkownika lub grupy, którą próbujesz aprowizować. 
* Przejrzyj [interfejs API mydłem ServiceNow SOAP,](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html) aby zrozumieć wszelkie wymagania lub ograniczenia (na przykład format, aby określić kod kraju dla użytkownika)
* Niektóre wdrożenia ServiceNow wymagają zezwolenia zakresów adresów IP dla usługi inicjowania obsługi administracyjnej usługi Azure AD. Zastrzeżone zakresy adresów IP dla usługi inicjowania obsługi administracyjnej usługi Azure AD można znaleźć [tutaj](https://www.microsoft.com/download/details.aspx?id=56519) w obszarze "AzureActiveDirectoryDomainServices".
* Żądania inicjowania obsługi administracyjnej są wysyłane domyślnie na adres https://{your-instance-name}.service-now.com/{table-name} . Jeśli potrzebujesz niestandardowego adresu URL dzierżawy, możesz podać cały adres URL w polu nazwy wystąpienia.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)
