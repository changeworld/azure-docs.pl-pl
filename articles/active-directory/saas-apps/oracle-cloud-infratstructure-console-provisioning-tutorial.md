---
title: 'Samouczek: Konfigurowanie konsoli infrastruktury Oracle Cloud Infrastructure Console do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak automatycznie aprowizować i usuwać aprowizyję kont użytkowników z usługi Azure AD na Oracle Cloud Infrastructure Console.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: e22c8746-7638-4a0f-ae08-7db0c477cf52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: Zhchia
ms.openlocfilehash: 5aa33529a1957b6e7728b3a87bacf6bb91d987ae
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378953"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie konsoli infrastruktury Oracle Cloud Infrastructure Console do automatycznego inicjowania obsługi administracyjnej przez użytkowników

W tym samouczku opisano kroki, które należy wykonać zarówno w oracle cloud infrastructure console, jak i usłudze Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego inicjowania obsługi administracyjnej użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie aprowizacji i usuwania przepisów użytkowników i grup do [Oracle Cloud Infrastructure Console](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton) przy użyciu usługi Azure AD inicjowania obsługi administracyjnej. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Tworzenie użytkowników w konsoli Oracle Cloud Infrastructure Console
> * Usuwanie użytkowników w oracle cloud infrastructure console, gdy nie wymagają już dostępu
> * Synchronizacja atrybutów użytkowników między usługą Azure AD a konsolą Oracle Cloud Infrastructure Console
> * Aprowizuj grupy i członkostwa w grupach w oracle cloud infrastructure console
> * [Logowanie jednokrotne w](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial) konsoli Oracle Cloud Infrastructure Console (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) do konfigurowania obsługi administracyjnej (np. administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub administrator globalny). 
* [Dzierżawa](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton)Oracle Cloud Infrastructure Control .
* Konto użytkownika w Oracle Cloud Infrastructure Control z uprawnieniami administratora.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia inicjowania obsługi administracyjnej
1. Dowiedz [się, jak działa usługa inicjowania obsługi administracyjnej](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Określ, kto będzie w [zakresie inicjowania obsługi administracyjnej](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Określ, jakie dane mają [być mapowane między usługą Azure AD a konsolą Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-oracle-cloud-infrastructure-console-to-support-provisioning-with-azure-ad"></a>Krok 2. Configure Oracle Cloud Infrastructure Console to support provisioning with Azure AD

1. Zaloguj się do portalu administracyjnego Oracle Cloud Infrastructure Console. W lewym górnym rogu ekranu przejdź do **pozycji Federacja tożsamości >**.

    ![Administrator Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/identity.png)

2. Kliknij adres URL wyświetlany na stronie obok konsoli oracle identity cloud service console.

    ![Oracle URL](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/url.png)

3. Kliknij **dodaj dostawcę tożsamości,** aby utworzyć nowego dostawcę tożsamości. Zapisz identyfikator IdP, który ma być używany jako część adresu URL dzierżawy. Kliknij ikonę plus obok karty **Aplikacje,** aby utworzyć narzędzie AppRole klienta OAuth i Grant IDCS Identity Domain Administrator.

    ![Ikona chmury Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/add.png)

4. Wykonaj poniższe zrzuty ekranu, aby skonfigurować aplikację. Po zakończeniu konfiguracji kliknij **zapisz**.

    ![Konfiguracja Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/configuration.png)

    ![Zasady emisji tokenów Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/token-issuance.png)

5. W obszarze konfiguracji kartę aplikacji rozwiń opcję **Informacje ogólne,** aby pobrać identyfikator klienta i klucz tajny klienta.

    ![Generowanie tokenów Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/general-information.png)

6. Aby wygenerować tajny token Base64 zakodować identyfikator klienta i klucz tajny klienta w formacie **identyfikator klienta:Klucz tajny klienta**. Zapisz tajny token. Ta wartość zostanie wprowadzona w polu **Token tajny** na karcie inicjowania obsługi administracyjnej aplikacji Oracle Cloud Infrastructure Console w witrynie Azure portal.

## <a name="step-3-add-oracle-cloud-infrastructure-console-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie konsoli Oracle Cloud Infrastructure Console z galerii aplikacji usługi Azure AD

Dodaj oracle cloud infrastructure console z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie inicjowania obsługi administracyjnej do Oracle Cloud Infrastructure Console. Jeśli wcześniej skonfigurowałeś Oracle Cloud Infrastructure Console dla aplikacji jednośliomowej, możesz użyć tej samej aplikacji. Jednak zaleca się utworzenie oddzielnej aplikacji podczas testowania integracji początkowo. Dowiedz się więcej o dodawaniu aplikacji z galerii [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określanie, kto będzie w zakresie inicjowania obsługi administracyjnej 

Usługa inicjowania obsługi administracyjnej usługi Azure AD umożliwia zakres, który będzie aprowidzony na podstawie przypisania do aplikacji i lub na podstawie atrybutów użytkownika / grupy. Jeśli wybierzesz zakres, który będzie aprowizowany do aplikacji na podstawie przypisania, można użyć następujących [kroków,](../manage-apps/assign-user-or-group-access-portal.md) aby przypisać użytkowników i grupy do aplikacji. Jeśli zdecydujesz się na zakres, który będzie aprowizować wyłącznie na podstawie atrybutów użytkownika lub grupy, można użyć filtru zakresu, jak opisano [w tym miejscu](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Podczas przypisywania użytkowników i grup do konsoli Oracle Cloud Infrastructure Console należy wybrać rolę inną niż **Dostęp domyślny**. Użytkownicy z rolą dostępu domyślnego są wykluczeni z inicjowania obsługi administracyjnej i będą oznaczane jako nieuwzdrowsze w dziennikach inicjowania obsługi administracyjnej. Jeśli jedyną rolą dostępową w aplikacji jest domyślna rola dostępu, można [zaktualizować manifest aplikacji,](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) aby dodać dodatkowe role. 

* Zacznij od małych. Przetestuj z małym zestawem użytkowników i grup przed wprowadzeniem do wszystkich. Gdy zakres inicjowania obsługi administracyjnej jest ustawiony na przypisanych użytkowników i grup, można kontrolować to, przypisując jednego lub dwóch użytkowników lub grup do aplikacji. Gdy zakres jest ustawiony dla wszystkich użytkowników i grup, można określić [filtr zakresu na podstawie atrybutów](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>Krok 5. Konfigurowanie automatycznego inicjowania obsługi administracyjnej w konsoli Oracle Cloud Infrastructure Console 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w aplikacji TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla konsoli Oracle Cloud Infrastructure Console w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Oracle Cloud Infrastructure Console**.

    ![Łącze Konsoli infrastruktury oracle w chmurze na liście Aplikacje](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** wprowadź adres URL `https://<IdP ID>.identity.oraclecloud.com/admin/v1` **dzierżawy** w formacie . Na przykład: `https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1`. Wprowadź wartość tokenu tajnego pobraną wcześniej w **tokenie tajnym**. Kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z konsolą Oracle Cloud Infrastructure Console. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Oracle Cloud Infrastructure Console ma uprawnienia administratora i spróbuj ponownie.

    ![Inicjowania obsługi](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z konsolą Oracle Cloud Infrastructure Console**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Oracle Cloud Infrastructure Console w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Oracle Cloud Infrastructure Console dla operacji aktualizacji. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)musisz upewnić się, że interfejs API oracle cloud infrastructure console obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

      |Atrybut|Typ|
      |---|---|
      |displayName|Ciąg|
      |userName|Ciąg|
      |aktywne|Wartość logiczna|
      |title|Ciąg|
      |wiadomości e-mail[wpisz eq "praca"].wartość|Ciąg|
      |preferowany Język|Ciąg|
      |name.givenName|Ciąg|
      |nazwa.familyName|Ciąg|
      |adresy[wpisz eq "praca"].sformatowany|Ciąg|
      |adresy[wpisz eq "praca"].miejscowość|Ciąg|
      |adresy[wpisz eq "praca"].region|Ciąg|
      |adresy[wpisz eq "praca"].postalCode|Ciąg|
      |adresy[wpisz eq "praca"].kraj|Ciąg|
      |adresy[wpisz eq "praca"].streetAddress|Ciąg|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|Ciąg|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Ciąg|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|Ciąg|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|Ciąg|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Dokumentacja|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|Ciąg|
      |urn:ietf:params:scim:schemas:oracle:idcs:extension:user:User:bypassNotification|Wartość logiczna|
      |urn:ietf:params:scim:schemas:oracle:idcs:extension:user:User:isFederatedUser|Wartość logiczna|

10. W sekcji **Mapowania** wybierz pozycję **Synchronizuj grupy usługi Azure Active Directory z konsolą Oracle Cloud Infrastructure Console**.

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do Oracle Cloud Infrastructure Console w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania grup w Oracle Cloud Infrastructure Console dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

      |Atrybut|Typ|
      |---|---|
      |displayName|Ciąg|
      |identyfikator zewnętrzny|Ciąg|
      |elementy członkowskie|Dokumentacja|

12. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla konsoli Oracle Cloud Infrastructure Console, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić Oracle Cloud Infrastructure Console, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

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
