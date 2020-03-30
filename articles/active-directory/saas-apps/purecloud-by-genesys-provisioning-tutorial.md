---
title: 'Samouczek: Konfigurowanie technologii PureCloud by Genesys w celu automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak automatycznie aprowizować i usuwać aprowizycje kont użytkowników z usługi Azure AD do PureCloud przez Genesys.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 5f04b88b-117e-40da-a15c-e3732b240d5d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2020
ms.author: Zhchia
ms.openlocfilehash: 119690b9046821ab538d879e1209c6ef77277370
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77370687"
---
# <a name="tutorial-configure-purecloud-by-genesys-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie PureCloud by Genesys do automatycznego inicjowania obsługi administracyjnej przez użytkowników

W tym samouczku opisano kroki, które należy wykonać zarówno w purecloud przez Genesys i usługi Azure Active Directory (Azure AD) do konfigurowania automatycznego inicjowania obsługi administracyjnej użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie aprowizacji i usuwania przepisów użytkowników i grup [purecloud przez Genesys](https://www.genesys.com) przy użyciu usługi azure ad inicjowania obsługi administracyjnej usługi. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Tworzenie użytkowników w PureCloud przez Genesys
> * Usuń użytkowników w PureCloud przez Genesys, gdy nie wymagają już dostępu
> * Zachowaj synchronizację atrybutów użytkownika między usługą Azure AD i PureCloud przez Genesys
> * Udostępnianie grup i członkostw w grupach purecloud przez Genesys
> * [Logowanie jednokrotne do](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial) PureCloud by Genesys (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) do konfigurowania obsługi administracyjnej (np. administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub administrator globalny). 
* [Organizacja](https://help.mypurecloud.com/?p=81984)PureCloud .
* Użytkownik z [uprawnieniami](https://help.mypurecloud.com/?p=24360) do tworzenia klienta Oauth.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia inicjowania obsługi administracyjnej
1. Dowiedz [się, jak działa usługa inicjowania obsługi administracyjnej](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Określ, kto będzie w [zakresie inicjowania obsługi administracyjnej](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Określ, jakie dane mają [być mapowane między usługą Azure AD a PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-purecloud-by-genesys-to-support-provisioning-with-azure-ad"></a>Krok 2. Configure PureCloud by Genesys to support provisioning with Azure AD

1. Utwórz [klienta Oauth](https://help.mypurecloud.com/?p=188023) skonfigurowanego w organizacji PureCloud.
2. Wygeneruj token [za pomocą klienta oauth](https://developer.mypurecloud.com/api/rest/authorization/use-client-credentials.html).
3. Jeśli chcesz automatycznie aprowizować członkostwo w grupie w purecloud, musisz [utworzyć grupy](https://help.mypurecloud.com/?p=52397) w PureCloud o identycznej nazwie do grupy w usłudze Azure AD.

## <a name="step-3-add-purecloud-by-genesys-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie purecloud by Genesys z galerii aplikacji usługi Azure AD

Dodaj PureCloud by Genesys z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie inicjowania obsługi administracyjnej purecloud przez Genesys. Jeśli wcześniej skonfigurowałeś PureCloud by Genesys dla aplikacji SSO, możesz użyć tej samej aplikacji. Jednak zaleca się utworzenie oddzielnej aplikacji podczas testowania integracji początkowo. Dowiedz się więcej o dodawaniu aplikacji z galerii [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określanie, kto będzie w zakresie inicjowania obsługi administracyjnej 

Usługa inicjowania obsługi administracyjnej usługi Azure AD umożliwia zakres, który będzie aprowidzony na podstawie przypisania do aplikacji i lub na podstawie atrybutów użytkownika / grupy. Jeśli wybierzesz zakres, który będzie aprowizowany do aplikacji na podstawie przypisania, można użyć następujących [kroków,](../manage-apps/assign-user-or-group-access-portal.md) aby przypisać użytkowników i grupy do aplikacji. Jeśli zdecydujesz się na zakres, który będzie aprowizować wyłącznie na podstawie atrybutów użytkownika lub grupy, można użyć filtru zakresu, jak opisano [w tym miejscu](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Podczas przypisywania użytkowników i grup do PureCloud przez Genesys, należy wybrać rolę inną niż **Domyślny dostęp**. Użytkownicy z rolą dostępu domyślnego są wykluczeni z inicjowania obsługi administracyjnej i będą oznaczane jako nieuwzdrowsze w dziennikach inicjowania obsługi administracyjnej. Jeśli jedyną rolą dostępową w aplikacji jest domyślna rola dostępu, można [zaktualizować manifest aplikacji,](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) aby dodać dodatkowe role. 

* Zacznij od małych. Przetestuj z małym zestawem użytkowników i grup przed wprowadzeniem do wszystkich. Gdy zakres inicjowania obsługi administracyjnej jest ustawiony na przypisanych użytkowników i grup, można kontrolować to, przypisując jednego lub dwóch użytkowników lub grup do aplikacji. Gdy zakres jest ustawiony dla wszystkich użytkowników i grup, można określić [filtr zakresu na podstawie atrybutów](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-purecloud-by-genesys"></a>Krok 5. Konfigurowanie automatycznego inicjowania obsługi administracyjnej usługi PureCloud przez Genesys 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w aplikacji TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-purecloud-by-genesys-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla usługi PureCloud przez Genesys w usłudze Azure AD:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **PureCloud by Genesys**.

    ![Link do aplikacji PureCloud by Genesys na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** wprowadź adres URL interfejsu API PureCloud przez Genesys i token Oauth w polach **ADRES URL dzierżawy** i **token tajny.** Adres URL interfejsu API będzie `{{API Url}}/api/v2/scim/v2`tak skonstruowany, że będzie on używany przy użyciu adresu URL interfejsu API dla regionu PureCloud w [Centrum deweloperów PureCloud.](https://developer.mypurecloud.com/api/rest/index.html) Kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z usługą PureCloud by Genesys. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto PureCloud by Genesys ma uprawnienia administratora i spróbuj ponownie.

    ![Inicjowania obsługi](./media/purecloud-by-genesys-provisioning-tutorial/provisioning.png)

6. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Wybierz **pozycję Zapisz**.

8. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z usługą PureCloud by Genesys**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do PureCloud przez Genesys w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w PureCloud przez Genesys dla operacji aktualizacji. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)musisz upewnić się, że interfejs API PureCloud by Genesys obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

     |Atrybut|Typ|
     |---|---|
     |userName|Ciąg|
     |aktywne|Wartość logiczna|
     |displayName|Ciąg|
     |wiadomości e-mail[wpisz eq "praca"].wartość|Ciąg|
     |title|Ciąg|
     |phoneNumbers[wpisz eq "mobile"].wartość|Ciąg|
     |phoneNumbers[wpisz eq "praca"].wartość|Ciąg|
     |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Ciąg|
     |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Tematy pomocy|

10. W sekcji **Mapowania** wybierz pozycję **Synchronizuj grupy usługi Azure Active Directory z purecloud przez Genesys**.

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do PureCloud przez Genesys w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania grup w PureCloud przez Genesys dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany. PureCloud by Genesys nie obsługuje tworzenia lub usuwania grup i obsługuje tylko aktualizowanie grup.

      |Atrybut|Typ|
      |---|---|
      |displayName|Ciąg|
      |identyfikator zewnętrzny|Ciąg|
      |elementy członkowskie|Tematy pomocy|

12. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla purecloud przez Genesys, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić PureCloud by Genesys, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

15. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkowy cykl synchronizacji wszystkich użytkowników i grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Początkowy cykl trwa dłużej niż kolejne cykle, które występują co około 40 minut, tak długo, jak usługa inicjowania obsługi administracyjnej usługi Azure AD jest uruchomiona. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu inicjowania obsługi administracyjnej użyj następujących zasobów do monitorowania wdrożenia:

* Użyj [dzienników inicjowania obsługi administracyjnej,](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) aby określić, którzy użytkownicy zostali pomyślnie lub bezskutecznie udostępnieni
* Sprawdź [pasek postępu,](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) aby zobaczyć stan cyklu inicjowania obsługi administracyjnej i jak blisko jest do zakończenia
* Jeśli konfiguracja inicjowania obsługi administracyjnej wydaje się być w stanie złej kondycji, aplikacja przejdzie do kwarantanny. Dowiedz się więcej o stanach kwarantanny [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../manage-apps/check-status-user-account-provisioning.md)
