---
title: 'Samouczek: Konfigurowanie zoomu do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak automatycznie aprowizować i usuwać aprowizyjanie kont użytkowników z usługi Azure AD do powiększenia.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/3/2019
ms.author: Zhchia
ms.openlocfilehash: 94c261da0c935cb7a41dde768069099b4e5ed251
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384079"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie zoomu do automatycznego inicjowania obsługi administracyjnej przez użytkowników

W tym samouczku opisano kroki, które należy wykonać w usłudze Zoom i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego inicjowania obsługi administracyjnej użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie aprowizacji i usuwania przepisów użytkowników i grup do [powiększenia](https://zoom.us/pricing/) przy użyciu usługi azure ad inicjowania obsługi administracyjnej. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Tworzenie użytkowników w powiększeniu
> * Usuń użytkowników w zoomie, gdy nie wymagają już dostępu
> * Synchronizacja atrybutów użytkownika między usługą Azure AD i Zoom
> * [Logowanie jednokrotne do](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-tutorial) zoomu (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) do konfigurowania obsługi administracyjnej (np. administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub administrator globalny). 
* [Dzierżawa powiększenia](https://zoom.us/pricing).
* Konto użytkownika w powiększeniu z uprawnieniami administratora.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia inicjowania obsługi administracyjnej
1. Dowiedz [się, jak działa usługa inicjowania obsługi administracyjnej](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Określ, kto będzie w [zakresie inicjowania obsługi administracyjnej](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Określ, jakie dane mają [być mapowane między usługą Azure AD a zoomem](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-zoom-to-support-provisioning-with-azure-ad"></a>Krok 2. Configure Zoom to support provisioning with Azure AD

1. Zaloguj się do [konsoli administracyjnej powiększenia](https://zoom.us/signin). Przejdź do **pozycji Powiększenie zaawansowane > dla deweloperów** w lewym okienku nawigacji.

    ![Integracje zoomu](media/zoom-provisioning-tutorial/zoom01.png)

2. Przejdź do **pozycji Zarządzaj** w prawym górnym rogu strony. 

    ![Zoom Zainstaluj](media/zoom-provisioning-tutorial/zoom02.png)

3. Przejdź do utworzonej aplikacji usługi Azure AD. 
    
    ![Aplikacja Zoom](media/zoom-provisioning-tutorial/zoom03.png)

4. Wybierz **pozycję Poświadczenia aplikacji** w lewym okienku nawigacji.

    ![Aplikacja Zoom](media/zoom-provisioning-tutorial/zoom04.png)

5. Skopiuj i zapisz **token JWT**. Ta wartość zostanie wprowadzona w polu **Tajny token** na karcie Inicjowanie obsługi administracyjnej aplikacji powiększenia w witrynie Azure portal. Jeśli potrzebujesz nowego tokenu bez wygasania, musisz ponownie skonfigurować czas wygaśnięcia, który automatycznie wygeneruje nowy token. 

    ![Zoom Zainstaluj](media/zoom-provisioning-tutorial/zoom05.png)

## <a name="step-3-add-zoom-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie powiększenia z galerii aplikacji usługi Azure AD

Dodaj powiększenie z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie inicjowania obsługi administracyjnej do powiększenia. Jeśli wcześniej skonfigurowałeś zoom dla jednokrotnego przygotowania, możesz użyć tej samej aplikacji. Jednak zaleca się utworzenie oddzielnej aplikacji podczas testowania integracji początkowo. Dowiedz się więcej o dodawaniu aplikacji z galerii [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określanie, kto będzie w zakresie inicjowania obsługi administracyjnej 

Usługa inicjowania obsługi administracyjnej usługi Azure AD umożliwia zakres, który będzie aprowidzony na podstawie przypisania do aplikacji i lub na podstawie atrybutów użytkownika / grupy. Jeśli wybierzesz zakres, który będzie aprowizowany do aplikacji na podstawie przypisania, można użyć następujących [kroków,](../manage-apps/assign-user-or-group-access-portal.md) aby przypisać użytkowników i grupy do aplikacji. Jeśli zdecydujesz się na zakres, który będzie aprowizować wyłącznie na podstawie atrybutów użytkownika lub grupy, można użyć filtru zakresu, jak opisano [w tym miejscu](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Podczas przypisywania użytkowników i grup do zoomu należy wybrać rolę inną niż **Dostęp domyślny**. Użytkownicy z rolą dostępu domyślnego są wykluczeni z inicjowania obsługi administracyjnej i będą oznaczane jako nieuwzdrowsze w dziennikach inicjowania obsługi administracyjnej. Jeśli jedyną rolą dostępową w aplikacji jest domyślna rola dostępu, można [zaktualizować manifest aplikacji,](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) aby dodać dodatkowe role. 

* Zacznij od małych. Przetestuj z małym zestawem użytkowników i grup przed wprowadzeniem do wszystkich. Gdy zakres inicjowania obsługi administracyjnej jest ustawiony na przypisanych użytkowników i grup, można kontrolować to, przypisując jednego lub dwóch użytkowników lub grup do aplikacji. Gdy zakres jest ustawiony dla wszystkich użytkowników i grup, można określić [filtr zakresu na podstawie atrybutów](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-zoom"></a>Krok 5. Konfigurowanie automatycznego inicjowania obsługi administracyjnej w powiększeniu 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w aplikacji TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla usługi Azure AD:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Powiększenie**.

    ![Link aplikacji Zoom na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** wprowadź adres `https://api.zoom.us/scim` URL **dzierżawy**. Wprowadź wartość **tokenu JWT** pobraną wcześniej w **tokenie tajnym**. Kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z zoomem. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto powiększenia ma uprawnienia administratora i spróbuj ponownie.

    ![Aprowerowowanie powiększenia](./media/zoom-provisioning-tutorial/provisioning.png)

6. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Wybierz **pozycję Zapisz**.

8. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory do powiększenia**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do powiększenia w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w powiększeniu dla operacji aktualizacji. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)musisz upewnić się, że interfejs API powiększenia obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

   |Atrybut|Typ|
   |---|---|
   |userName|Ciąg|
   |aktywne|Wartość logiczna|
   |name.givenName|Ciąg|
   |nazwa.familyName|Ciąg|
   |e-maile[wpisz eq "praca"]|Ciąg|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Ciąg|

10. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla powiększenia, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić zoomowi, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

13. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkowy cykl synchronizacji wszystkich użytkowników i grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Początkowy cykl trwa dłużej niż kolejne cykle, które występują co około 40 minut, tak długo, jak usługa inicjowania obsługi administracyjnej usługi Azure AD jest uruchomiona. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu inicjowania obsługi administracyjnej użyj następujących zasobów do monitorowania wdrożenia:

1. Użyj [dzienników inicjowania obsługi administracyjnej,](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) aby określić, którzy użytkownicy zostali pomyślnie lub bezskutecznie udostępnieni
2. Sprawdź [pasek postępu,](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) aby zobaczyć stan cyklu inicjowania obsługi administracyjnej i jak blisko jest do zakończenia
3. Jeśli konfiguracja inicjowania obsługi administracyjnej wydaje się być w stanie złej kondycji, aplikacja przejdzie do kwarantanny. Dowiedz się więcej o stanach kwarantanny [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="connector-limitations"></a>Ograniczenia złącza
* Zoom pozwala tylko maksymalnie 9,999 podstawowych użytkowników dzisiaj.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../manage-apps/check-status-user-account-provisioning.md)
