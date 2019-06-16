---
title: Obsługiwane połączenia przy użyciu łącznika zarządzania usługami IT w usłudze Azure Log Analytics | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje na temat nawiązywania połączenia narzędzia ITSM produktów/usług za pomocą IT Service Management Connector (ITSMC) w usłudze Azure Monitor centralne monitorowanie i zarządzanie nimi elementy robocze ITSM.
documentationcenter: ''
author: jyothirmaisuri
manager: riyazp
editor: ''
ms.assetid: 8231b7ce-d67f-4237-afbf-465e2e397105
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: v-jysur
ms.openlocfilehash: ffd9c4bfc934faff1664ff39c0e979a9d6c09487
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399777"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Połączenia narzędzia ITSM produktów/usług za pomocą łącznika zarządzania usługami IT
Ten artykuł zawiera informacje o tym, jak skonfigurować połączenie między jego produkt/usługę ITSM i IT Service Management Connector (ITSMC) w usłudze Log Analytics, aby centralnie zarządzać elementami roboczymi. Aby uzyskać więcej informacji na temat ITSMC zobacz [Przegląd](../../azure-monitor/platform/itsmc-overview.md).

Obsługiwane są następujące narzędzia ITSM produktów/usług. Wybierz produkt, aby wyświetlić szczegółowe informacje o tym, jak nawiązać ITSMC produktu.

- [System Center Service Manager](#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](#connect-provance-to-it-service-management-connector-in-azure)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-azure)

> [!NOTE]
> 
> Łącznik ITSM może łączyć się tylko z wystąpienia usługi ServiceNow oparte na chmurze. Wystąpienia usługi ServiceNow w środowisku lokalnym nie są obecnie obsługiwane.

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-azure"></a>Łączenie programu System Center Service Manager do usługi IT łącznika zarządzania na platformie Azure

Poniższe sekcje zawierają szczegółowe informacje dotyczące sposobu nawiązywania produktu System Center Service Manager ITSMC na platformie Azure.

### <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że zostały spełnione następujące wymagania wstępne:

- ITSMC zainstalowane. Więcej informacji: [Dodawanie IT usługi rozwiązania do zarządzania łącznika](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Aplikacja sieci Web programu Service Manager (aplikacja sieci Web) jest wdrożone i skonfigurowane. Informacje dotyczące aplikacji sieci Web są [tutaj](#create-and-deploy-service-manager-web-app-service).
- Połączenie hybrydowe utworzone i skonfigurowane. Więcej informacji: [Konfigurowanie hybrydowego połączenia](#configure-the-hybrid-connection).
- Obsługiwane wersje programu Service Manager:  2012 R2 lub 2016.
- Rola użytkownika:  [Operator zaawansowany](https://technet.microsoft.com/library/ff461054.aspx).

### <a name="connection-procedure"></a>Procedura połączenia

Za pomocą poniższej procedury, aby połączyć wystąpienie usługi System Center Service Manager z ITSMC:

1. W witrynie Azure portal, przejdź do **wszystkie zasoby** i poszukaj **ServiceDesk(YourWorkspaceName)**

2.  W obszarze **źródła danych obszaru roboczego** kliknij **połączenia narzędzia ITSM**.

    ![Nowe połączenie](media/itsmc-connections/add-new-itsm-connection.png)

3. W górnej części w okienku po prawej stronie kliknij pozycję **Dodaj**.

4. Podaj informacje zgodnie z opisem w poniższej tabeli, a następnie kliknij przycisk **OK** do utworzenia połączenia.

> [!NOTE]
> 
> Te parametry są obowiązkowe.

| **Pole** | **Opis** |
| --- | --- |
| **Nazwa połączenia**   | Wpisz nazwę wystąpienia System Center Service Manager, który chcesz połączyć z ITSMC.  Nazwa ta będzie używana później przy Konfiguruj elementy robocze w tym wystąpieniu / wyświetlić szczegółowy dziennik analizy. |
| **Typ partnera**   | Wybierz **programu System Center Service Manager**. |
| **Adres URL serwera**   | Wpisz adres URL aplikacji sieci Web programu Service Manager. Więcej informacji o aplikacji sieci Web programu Service Manager jest [tutaj](#create-and-deploy-service-manager-web-app-service).
| **Identyfikator klienta**   | Wpisz identyfikator klienta, który został wygenerowany (przy użyciu skryptu automatyczne) w celu uwierzytelniania aplikacji sieci Web. Więcej informacji na temat zautomatyzowanego skryptu jest [tutaj.](../../azure-monitor/platform/itsmc-service-manager-script.md)|
| **Client Secret (Wpis tajny klienta)**   | Wpisz klucz tajny klienta generowane dla tego identyfikatora.   |
| **Synchronizowanie danych**   | Wybierz elementy robocze programu Service Manager, które mają być synchronizowane za pomocą ITSMC.  Pracy, te elementy są importowane do usługi Log Analytics. **Opcje:**  Zdarzenia, żądania zmiany.|
| **Zakres synchronizacji danych** | Wpisz liczbę w ciągu ostatnich dni, które mają dane. **Maksymalny limit**: 120 dni. |
| **Utwórz nowy element konfiguracji w rozwiązaniu narzędzia ITSM** | Wybierz tę opcję, jeśli chcesz utworzyć elementy konfiguracji w produkcie ITSM. Po wybraniu usługi Log Analytics tworzy konfiguracji (ci) dotyczy jako elementy konfiguracji (w przypadku nieistniejących CIs), w obsługiwanym systemie ITSM. **Domyślne**: wyłączone. |

![Połączenie programu Service manager](media/itsmc-connections/service-manager-connection.png)

**Pomyślnie nawiązano połączenie, gdy synchronizowane**:

- Wybrane elementy robocze z programu Service Manager są importowane do usługi Azure **usługi Log Analytics.** Możesz wyświetlić podsumowanie tych elementów roboczych na kafelku łącznika zarządzania usługami IT.

- Zdarzenia można utworzyć z alertów usługi Log Analytics lub rekordy dziennika lub alertów platformy Azure, w tym wystąpieniu programu Service Manager.


Więcej informacji: [Utwórz elementy robocze ITSM z alertów platformy Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-and-deploy-service-manager-web-app-service"></a>Tworzenie i wdrażanie aplikacji sieci web programu Service Manager

Aby połączyć z programu Service Manager w środowisku lokalnym za pomocą ITSMC na platformie Azure, firma Microsoft opracowała aplikację sieci Web programu Service Manager w witrynie GitHub.

Aby skonfigurować aplikację sieci Web ITSM dla programu Service Manager, wykonaj następujące czynności:

- **Wdrażanie aplikacji sieci Web** — wdrażanie aplikacji sieci Web, ustaw właściwości i uwierzytelnianie za pomocą usługi Azure AD. Możesz wdrożyć aplikację sieci web za pomocą [zautomatyzowanego skryptu](../../azure-monitor/platform/itsmc-service-manager-script.md) udostępnione przez firmę Microsoft jest użytkownik.
- **Konfigurowanie połączenia hybrydowego** - [konfigurowania tego połączenia](#configure-the-hybrid-connection), ręcznie.

#### <a name="deploy-the-web-app"></a>Wdrażanie aplikacji sieci web
Użyj zautomatyzowanej [skryptu](../../azure-monitor/platform/itsmc-service-manager-script.md) Aby wdrożyć aplikację sieci Web, ustaw właściwości, a uwierzytelnianie za pomocą usługi Azure AD.

Uruchom skrypt, podając następujące wymagane szczegóły:

- Szczegóły subskrypcji platformy Azure
- Nazwa grupy zasobów
- Lokalizacja
- Szczegóły serwera programu Service Manager (nazwa serwera, domeny, nazwę użytkownika i hasło)
- Prefiks nazwy witryny dla aplikacji sieci Web
- ServiceBus Namespace.

Skrypt tworzy aplikację internetową, przy użyciu nazwy, który określiłeś (wraz z kilku dodatkowe ciągi, aby była unikatowa). Generuje on **adres URL aplikacji sieci Web**, **identyfikator klienta** i **klucz tajny klienta**.

Zapisz wartości można ich używać podczas tworzenia połączenia za pomocą ITSMC.

**Sprawdź instalację aplikacji sieci Web**

1. Przejdź do **witryny Azure portal** > **zasobów**.
2. Wybierz aplikację sieci Web, kliknij przycisk **ustawienia** > **ustawienia aplikacji**.
3. Upewnij się, informacje o wystąpienia programu Service Manager, podane w czasie wdrażania aplikacji za pomocą skryptu.

### <a name="configure-the-hybrid-connection"></a>Konfigurowanie połączenia hybrydowego

Poniższa procedura umożliwia skonfigurowanie połączenia hybrydowego, który łączy z wystąpienia programu Service Manager przy użyciu ITSMC na platformie Azure.

1. Możesz znaleźć aplikacji sieci Web programu Service Manager, w obszarze **zasobów platformy Azure**.
2. Kliknij przycisk **ustawienia** > **sieć**.
3. W obszarze **połączeń hybrydowych**, kliknij przycisk **skonfigurować swoje punkty końcowe połączenia hybrydowego**.

    ![Sieciowe połączenie hybrydowe](media/itsmc-connections/itsmc-hybrid-connection-networking-and-end-points.png)
4. W **połączeń hybrydowych** bloku kliknij **Dodaj połączenie hybrydowe**.

    ![Dodaj połączenie hybrydowe](media/itsmc-connections/itsmc-new-hybrid-connection-add.png)

5. W **dodawanie połączeń hybrydowych** bloku kliknij **Utwórz nowy hybrydowy połączenia**.

    ![Nowe połączenie hybrydowe](media/itsmc-connections/itsmc-create-new-hybrid-connection.png)

6. Wpisz następujące wartości:

   - **Nazwa punktu końcowego**: Określ nazwę dla nowego połączenia hybrydowego.
   - **Host punktu końcowego**: Nazwa FQDN serwera zarządzania programu Service Manager.
   - **Port punktu końcowego**: Typ 5724
   - **Przestrzeń nazw magistrali usług**: Użyj istniejącej przestrzeni nazw magistrali usług lub Utwórz nową.
   - **Lokalizacja**: Wybierz lokalizację.
   - **Nazwa**: Określ nazwę usługi servicebus, jeśli tworzysz go.

     ![Wartości połączenia hybrydowego](media/itsmc-connections/itsmc-new-hybrid-connection-values.png)
6. Kliknij przycisk **OK** zamknąć **Tworzenie połączenia hybrydowego** bloku i rozpocząć tworzenie połączenia hybrydowego.

    Po utworzeniu połączenia hybrydowego jest wyświetlany w bloku.

7. Po utworzeniu połączenia hybrydowego wybierz połączenie i kliknij przycisk **Dodaj wybrane połączenie hybrydowe**.

    ![Nowe połączenie hybrydowe](media/itsmc-connections/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>Konfigurowanie konfiguracja odbiornika

Poniższa procedura umożliwia skonfigurowanie ustawień odbiornika połączenia hybrydowego.

1. W **połączeń hybrydowych** bloku kliknij **Pobierz Menedżera połączeń** i zainstaluj go na komputerze, na którym jest uruchomione wystąpienie programu System Center Service Manager.

    Po zakończeniu instalacji **interfejsu użytkownika Menedżera połączeń hybrydowych** opcja jest dostępna w ramach **Start** menu.

2. Kliknij przycisk **interfejsu użytkownika Menedżera połączeń hybrydowych** , zostanie wyświetlony monit o podanie poświadczeń platformy Azure.

3. Zaloguj się przy użyciu swoich poświadczeń platformy Azure i wybierz swoją subskrypcję, w której utworzono połączenie hybrydowe.

4. Kliknij pozycję **Zapisz**.

Nawiązano połączenie hybrydowe.

![połączenie hybrydowe pomyślne](media/itsmc-connections/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> Po rozwiązania hybrydowego jest tworzone połączenie, sprawdź i przetestuj połączenie, przechodząc do wdrożonej aplikacji sieci Web programu Service Manager. Upewnij się, że połączenie zostanie nawiązane, zanim spróbujesz połączyć się z ITSMC na platformie Azure.

Na poniższej ilustracji przykład przedstawiono szczegółowe informacje o pomyślnym nawiązaniu połączenia:

![Test połączenia hybrydowego](media/itsmc-connections/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-azure"></a>Łączenie usługi ServiceNow z usługą IT łącznika zarządzania na platformie Azure

Poniższe sekcje zawierają szczegółowe informacje dotyczące sposobu nawiązywania produktu ServiceNow ITSMC na platformie Azure.

### <a name="prerequisites"></a>Wymagania wstępne
Upewnij się, że zostały spełnione następujące wymagania wstępne:
- ITSMC zainstalowane. Więcej informacji: [Dodawanie IT usługi rozwiązania do zarządzania łącznika](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Usługi ServiceNow obsługiwane wersje: Madryt, Londyn, Kingston, Dżakarta, Stambuł, Helsinki, Geneva.

**Administratorzy usługi ServiceNow musi wykonać następujące czynności w swoje wystąpienie usługi ServiceNow**:
- Generuj identyfikator klienta oraz klucz tajny klienta dla produktu ServiceNow. Aby uzyskać informacje na temat generowania Identyfikatora klienta i klucz tajny zobacz następujące informacje zgodnie z wymaganiami:

    - [Konfigurowanie uwierzytelniania OAuth dla Madryt](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Skonfiguruj protokół OAuth w Londynie](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurowanie uwierzytelniania OAuth dla Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Skonfiguruj protokół OAuth w Dżakarcie](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurowanie uwierzytelniania OAuth dla Stambuł](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Skonfiguruj protokół OAuth w Helsinkach](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Skonfiguruj protokół OAuth w Genewie](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)


- Zainstaluj aplikację użytkownika dla integracji Microsoft Log Analytics (aplikacji ServiceNow). [Dowiedz się więcej](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
- Utworzenie roli użytkownika integrację dla aplikacji użytkownika. Informacje dotyczące sposobu tworzenia roli użytkownika integracji [tutaj](#create-integration-user-role-in-servicenow-app).

### <a name="connection-procedure"></a>**Procedura połączenia**
Aby utworzyć połączenie usługi ServiceNow, należy użyć następującej procedury:


1. W witrynie Azure portal, przejdź do **wszystkie zasoby** i poszukaj **ServiceDesk(YourWorkspaceName)**

2.  W obszarze **źródła danych obszaru roboczego** kliknij **połączenia narzędzia ITSM**.
    ![Nowe połączenie](media/itsmc-connections/add-new-itsm-connection.png)

3. W górnej części w okienku po prawej stronie kliknij pozycję **Dodaj**.

4. Podaj informacje zgodnie z opisem w poniższej tabeli, a następnie kliknij przycisk **OK** do utworzenia połączenia.


> [!NOTE]
> Te parametry są obowiązkowe.

| **Pole** | **Opis** |
| --- | --- |
| **Nazwa połączenia**   | Wpisz nazwę wystąpienia usługi ServiceNow, który chcesz połączyć się z ITSMC.  Możesz użyć tej nazwy później w usłudze Log Analytics, Konfiguruj elementy robocze w tym ITSM / wyświetlić szczegółowy dziennik analizy. |
| **Typ partnera**   | Wybierz **ServiceNow**. |
| **Nazwa użytkownika**   | Wpisz nazwę użytkownika integracji, utworzony w aplikacji usługi ServiceNow obsługuje połączenia ITSMC. Więcej informacji: [Utworzenie roli użytkownika aplikacji usługi ServiceNow](#create-integration-user-role-in-servicenow-app).|
| **Hasło**   | Wpisz hasło skojarzone z tą nazwą użytkownika. **Uwaga**: Nazwa użytkownika i hasło są używane do generowania tokenów uwierzytelniania tylko, a nie są przechowywane w dowolnym miejscu w ramach usługi ITSMC.  |
| **Adres URL serwera**   | Wpisz adres URL wystąpienia usługi ServiceNow, którą chcesz nawiązać połączenie z ITSMC. |
| **Identyfikator klienta**   | Wpisz identyfikator klienta, który chcesz użyć na potrzeby uwierzytelniania OAuth2, który został wcześniej wygenerowany.  Więcej informacji na temat generowania Identyfikatora klienta i klucz tajny:   [Ustawienia uwierzytelniania OAuth](https://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Client Secret (Wpis tajny klienta)**   | Wpisz klucz tajny klienta generowane dla tego identyfikatora.   |
| **Zakres synchronizacji danych**   | Wybierz elementy robocze usługi ServiceNow, które mają być synchronizowane z usługą Azure Log Analytics za pośrednictwem ITSMC.  Wybrane wartości są importowane do usługi log analytics.   **Opcje:**  Zdarzenia i żądania zmiany.|
| **Synchronizowanie danych** | Wpisz liczbę w ciągu ostatnich dni, które mają dane. **Maksymalny limit**: 120 dni. |
| **Utwórz nowy element konfiguracji w rozwiązaniu narzędzia ITSM** | Wybierz tę opcję, jeśli chcesz utworzyć elementy konfiguracji w produkcie ITSM. Po wybraniu ITSMC tworzy konfiguracji (ci) dotyczy jako elementy konfiguracji (w przypadku nieistniejących CIs), w obsługiwanym systemie ITSM. **Domyślne**: wyłączone. |

![Połączenie usługi ServiceNow](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

**Pomyślnie nawiązano połączenie, gdy synchronizowane**:

- Wybrane elementy robocze z wystąpienia usługi ServiceNow są importowane do usługi Azure **usługi Log Analytics.** Możesz wyświetlić podsumowanie tych elementów roboczych na kafelku łącznika zarządzania usługami IT.

- Zdarzenia można utworzyć z alertów usługi Log Analytics lub rekordy dziennika lub alertów platformy Azure, w tym wystąpieniu usługi ServiceNow.

Więcej informacji: [Utwórz elementy robocze ITSM z alertów platformy Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-integration-user-role-in-servicenow-app"></a>Utworzenie roli użytkownika integracji w aplikacji usługi ServiceNow

Użytkownik następującej procedury:

1. Odwiedź stronę [magazynu usługi ServiceNow](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) i zainstaluj **aplikacji przez użytkownika usługi ServiceNow i Microsoft OMS integracji** do swojego wystąpienia usługi ServiceNow.
   
   >[!NOTE]
   >W ramach ciągłego przejście z programu Microsoft Operations Management Suite (OMS) do usługi Azure Monitor OMS jest teraz nazywana usługi Log Analytics.     
2. Po zakończeniu instalacji można znaleźć na pasku nawigacyjnym po lewej stronie wystąpienia usługi ServiceNow, wyszukiwania i wybierz pozycję Microsoft OMS integrator.  
3. Kliknij przycisk **listy kontrolnej instalacji**.

   Stan jest wyświetlany jako **niemożliwe** Jeśli roli użytkownika jest jeszcze ma zostać utworzony.

4. W polach tekstowych obok **użytkownicy integracji Utwórz**, wprowadź nazwę użytkownika dla użytkownika, które można podłączyć do ITSMC na platformie Azure.
5. Wprowadź hasło dla tego użytkownika, a następnie kliknij przycisk **OK**.  

> [!NOTE]
> 
> Te poświadczenia służą do nawiązania połączenia usługi ServiceNow na platformie Azure.

Domyślne role przypisane wyświetlana nowo utworzonego użytkownika.

**Domyślne role**:
- personalize_choices
- import_transformer
-   x_mioms_microsoft.user
-   ITIL
-   template_editor
-   view_changer

Po użytkownik została pomyślnie utworzona, stan **Sprawdź listy kontrolnej instalacji** przenosi ukończone, wyświetlanie szczegółów roli użytkownika utworzone dla aplikacji.

> [!NOTE]
> 
> Łącznik ITSM może wysyłać zdarzeń do usługi ServiceNow, bez innych modułów, zainstalowanym wystąpieniem usługi ServiceNow. Jeśli przy użyciu modułu EventManagement w Twoim wystąpieniu usługi ServiceNow i chcesz, aby utworzyć zdarzenia i alerty w usługi ServiceNow przy użyciu łącznika, należy dodać następujące role użytkownika integracji:
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-azure"></a>Nawiązać Provance IT Service Management Connector w usłudze Azure

Poniższe sekcje zawierają szczegółowe informacje dotyczące sposobu nawiązywania produktu Provance ITSMC na platformie Azure.


### <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że zostały spełnione następujące wymagania wstępne:


- ITSMC zainstalowane. Więcej informacji: [Dodawanie IT usługi rozwiązania do zarządzania łącznika](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Aplikacja provance powinien być zarejestrowany w usłudze Azure AD — oraz identyfikator klienta jest dostępna. Aby uzyskać szczegółowe informacje, zobacz [jak skonfigurować uwierzytelnianie usługi active directory](../../app-service/configure-authentication-provider-aad.md).

- Rola użytkownika:  Administrator.

### <a name="connection-procedure"></a>Procedura połączenia

Użyj poniższej procedury, aby utworzyć połączenie Provance:

1. W witrynie Azure portal, przejdź do **wszystkie zasoby** i poszukaj **ServiceDesk(YourWorkspaceName)**

2.  W obszarze **źródła danych obszaru roboczego** kliknij **połączenia narzędzia ITSM**.
    ![Nowe połączenie](media/itsmc-connections/add-new-itsm-connection.png)

3. W górnej części w okienku po prawej stronie kliknij pozycję **Dodaj**.

4. Podaj informacje zgodnie z opisem w poniższej tabeli, a następnie kliknij przycisk **OK** do utworzenia połączenia.

> [!NOTE]
> 
> Te parametry są obowiązkowe.

| **Pole** | **Opis** |
| --- | --- |
| **Nazwa połączenia**   | Wpisz nazwę wystąpienia Provance, który chcesz połączyć z ITSMC.  Nazwa ta będzie używana później przy Konfiguruj elementy robocze w tym ITSM / wyświetlić szczegółowy dziennik analizy. |
| **Typ partnera**   | Wybierz **Provance**. |
| **Nazwa użytkownika**   | Wpisz nazwę użytkownika, które można podłączyć do ITSMC.    |
| **Hasło**   | Wpisz hasło skojarzone z tą nazwą użytkownika. **Uwaga:** Nazwa użytkownika i hasło są używane do generowania tokenów uwierzytelniania tylko, a nie są przechowywane w dowolnym miejscu w ramach usługi ITSMC. _|
| **Adres URL serwera**   | Wpisz adres URL wystąpienia Provance, którą chcesz nawiązać połączenie z ITSMC. |
| **Identyfikator klienta**   | Wpisz identyfikator klienta do uwierzytelniania tego połączenia, który został wygenerowany w wystąpieniu usługi Provance.  Więcej informacji na temat identyfikator klienta, zobacz [jak skonfigurować uwierzytelnianie usługi active directory](../../app-service/configure-authentication-provider-aad.md). |
| **Zakres synchronizacji danych**   | Wybierz elementy robocze Provance, które mają być synchronizowane z usługą Azure Log Analytics za pośrednictwem ITSMC.  Pracy, te elementy są importowane do usługi log analytics.   **Opcje:**   Zdarzenia, żądania zmiany.|
| **Synchronizowanie danych** | Wpisz liczbę w ciągu ostatnich dni, które mają dane. **Maksymalny limit**: 120 dni. |
| **Utwórz nowy element konfiguracji w rozwiązaniu narzędzia ITSM** | Wybierz tę opcję, jeśli chcesz utworzyć elementy konfiguracji w produkcie ITSM. Po wybraniu ITSMC tworzy konfiguracji (ci) dotyczy jako elementy konfiguracji (w przypadku nieistniejących CIs), w obsługiwanym systemie ITSM. **Domyślne**: wyłączone.|

![Provance połączenia](media/itsmc-connections/itsm-connections-provance-latest.png)

**Pomyślnie nawiązano połączenie, gdy synchronizowane**:

- Wybrane elementy robocze w tym wystąpieniu Provance są importowane do usługi Azure **usługi Log Analytics.** Możesz wyświetlić podsumowanie tych elementów roboczych na kafelku łącznika zarządzania usługami IT.

- Zdarzenia można utworzyć z alertów usługi Log Analytics lub rekordy dziennika lub alertów platformy Azure, w tym wystąpieniu Provance.

Więcej informacji: [Utwórz elementy robocze ITSM z alertów platformy Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

## <a name="connect-cherwell-to-it-service-management-connector-in-azure"></a>Cherwell nawiązać połączenie z IT Service Management Connector w usłudze Azure

Poniższe sekcje zawierają szczegółowe informacje dotyczące sposobu nawiązywania produktu Cherwell ITSMC na platformie Azure.

### <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że zostały spełnione następujące wymagania wstępne:

- ITSMC zainstalowane. Więcej informacji: [Dodawanie IT usługi rozwiązania do zarządzania łącznika](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Wygenerowany identyfikator klienta. Więcej informacji: [Generuj identyfikator klienta dla Cherwell](#generate-client-id-for-cherwell).
- Rola użytkownika:  Administrator.

### <a name="connection-procedure"></a>Procedura połączenia

Użyj poniższej procedury, aby utworzyć połączenie Provance:

1. W witrynie Azure portal, przejdź do **wszystkie zasoby** i poszukaj **ServiceDesk(YourWorkspaceName)**

2.  W obszarze **źródła danych obszaru roboczego** kliknij **połączenia narzędzia ITSM**.
    ![Nowe połączenie](media/itsmc-connections/add-new-itsm-connection.png)

3. W górnej części w okienku po prawej stronie kliknij pozycję **Dodaj**.

4. Podaj informacje zgodnie z opisem w poniższej tabeli, a następnie kliknij przycisk **OK** do utworzenia połączenia.

> [!NOTE]
> 
> Te parametry są obowiązkowe.

| **Pole** | **Opis** |
| --- | --- |
| **Nazwa połączenia**   | Wpisz nazwę, którą chcesz nawiązać połączenie z ITSMC wystąpienia Cherwell.  Nazwa ta będzie używana później przy Konfiguruj elementy robocze w tym ITSM / wyświetlić szczegółowy dziennik analizy. |
| **Typ partnera**   | Wybierz **Cherwell.** |
| **Nazwa użytkownika**   | Wpisz nazwę użytkownika Cherwell, które można podłączyć do ITSMC. |
| **Hasło**   | Wpisz hasło skojarzone z tą nazwą użytkownika. **Uwaga:** Nazwa użytkownika i hasło są używane do generowania tokenów uwierzytelniania tylko, a nie są przechowywane w dowolnym miejscu w ramach usługi ITSMC.|
| **Adres URL serwera**   | Wpisz adres URL wystąpienia Cherwell, którą chcesz nawiązać połączenie z ITSMC. |
| **Identyfikator klienta**   | Wpisz identyfikator klienta do uwierzytelniania tego połączenia, który został wygenerowany w wystąpieniu usługi Cherwell.   |
| **Zakres synchronizacji danych**   | Wybierz elementy robocze Cherwell, które mają być synchronizowane za pomocą ITSMC.  Pracy, te elementy są importowane do usługi log analytics.   **Opcje:**  Zdarzenia, żądania zmiany. |
| **Synchronizowanie danych** | Wpisz liczbę w ciągu ostatnich dni, które mają dane. **Maksymalny limit**: 120 dni. |
| **Utwórz nowy element konfiguracji w rozwiązaniu narzędzia ITSM** | Wybierz tę opcję, jeśli chcesz utworzyć elementy konfiguracji w produkcie ITSM. Po wybraniu ITSMC tworzy konfiguracji (ci) dotyczy jako elementy konfiguracji (w przypadku nieistniejących CIs), w obsługiwanym systemie ITSM. **Domyślne**: wyłączone. |


![Provance połączenia](media/itsmc-connections/itsm-connections-cherwell-latest.png)

**Pomyślnie nawiązano połączenie, gdy synchronizowane**:

- Wybrane elementy robocze w tym wystąpieniu Cherwell są importowane do usługi Azure **usługi Log Analytics.** Możesz wyświetlić podsumowanie tych elementów roboczych na kafelku łącznika zarządzania usługami IT.

- Zdarzenia można utworzyć z alertów usługi Log Analytics lub rekordy dziennika lub alertów platformy Azure, w tym wystąpieniu Cherwell.

Więcej informacji: [Utwórz elementy robocze ITSM z alertów platformy Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Generuj identyfikator klienta dla Cherwell

Aby wygenerować klucz/Identyfikatora klienta dla Cherwell, należy wykonać następującą procedurę:

1. Zaloguj się do swojego wystąpienia Cherwell jako administrator.
2. Kliknij przycisk **zabezpieczeń** > **ustawienia klienta interfejsu API REST Edytuj**.
3. Wybierz **Utwórz nowy klient** > **klucz tajny klienta**.

    ![Identyfikator użytkownika Cherwell](media/itsmc-connections/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>Kolejne kroki
 - [Utwórz elementy robocze ITSM z alertów platformy Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
