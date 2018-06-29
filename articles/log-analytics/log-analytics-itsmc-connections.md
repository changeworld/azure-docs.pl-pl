---
title: Obsługiwane połączenia z łącznikiem zarządzania usługi IT w Azure Log Analytics | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje o sposobie łączenia z IT usługi zarządzania łącznika (ITSMC) w OMS Log Analytics, aby centralnie monitorować i zarządzać nimi elementy robocze Zarządzanie usługami IT — zarządzanie usługami IT — produktów/usług.
documentationcenter: ''
author: JYOTHIRMAISURI
manager: riyazp
editor: ''
ms.assetid: 8231b7ce-d67f-4237-afbf-465e2e397105
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: v-jysur
ms.openlocfilehash: 1c1c294a877a996dc0e6cb916b038dc0ca27ed5c
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061713"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Zarządzanie usługami IT — produktów/usług Uzyskuj dostęp do łącznika zarządzania usługi IT
Ten artykuł zawiera informacje o tym, jak skonfigurować połączenie między Zarządzanie usługami IT — produktu/usługi i IT usługi zarządzania łącznika (ITSMC) w analizy dzienników do centralnego zarządzania elementami pracy. Aby uzyskać więcej informacji o ITSMC, zobacz [omówienie](log-analytics-itsmc-overview.md).

Obsługiwane są następujące Zarządzanie usługami IT — produktów/usług. Wybierz produkt, aby wyświetlić szczegółowe informacje o tym, jak nawiązać ITSMC produktu.

- [System Center Service Manager](#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](#connect-provance-to-it-service-management-connector-in-azure)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-azure)

> [!NOTE]

> Zarządzanie usługami IT — łącznika można połączyć tylko do wystąpień usługi ServiceNow oparte na chmurze. Lokalne usługi ServiceNow wystąpień nie są obecnie obsługiwane.

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-azure"></a>Łączenie programu System Center Service Manager do usługi IT łącznika zarządzania na platformie Azure

Poniższe sekcje zawierają szczegółowe informacje na temat nawiązywania połączenia z ITSMC na platformie Azure produktu System Center Service Manager.

### <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że są spełnione następujące wymagania wstępne:

- ITSMC zainstalowane. Więcej informacji: [Dodawanie rozwiązania do zarządzania łącznika usługi IT](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Aplikacja sieci Web programu Service Manager (aplikacja sieci Web) jest wdrożone i skonfigurowane. Informacje dotyczące aplikacji sieci Web jest [tutaj](#create-and-deploy-service-manager-web-app-service).
- Połączenia hybrydowe utworzone i skonfigurowane. Więcej informacji: [skonfiguruj hybrydowych połączenia](#configure-the-hybrid-connection).
- Obsługiwane wersje programu Service Manager: 2012 R2 lub 2016.
- Rola użytkownika: [operator zaawansowany](https://technet.microsoft.com/library/ff461054.aspx).

### <a name="connection-procedure"></a>Procedura połączenia

ITSMC nawiązać połączenia z wystąpieniem programu System Center Service Manager, należy użyć następującej procedury:

1. W portalu Azure, przejdź do **wszystkie zasoby** i poszukaj **ServiceDesk(YourWorkspaceName)**

2.  W obszarze **źródeł danych obszaru roboczego** kliknij **połączeń Zarządzanie usługami IT —**.

    ![Nowe połączenie](./media/log-analytics-itsmc/add-new-itsm-connection.png)

3. W górnej części okienku po prawej stronie, kliknij przycisk **Dodaj**.

4. Podaj informacje zgodnie z opisem w poniższej tabeli, a następnie kliknij przycisk **OK** do utworzenia połączenia.

> [!NOTE]

> Te parametry są obowiązkowe.

| **Pole** | **Opis** |
| --- | --- |
| **Nazwa połączenia**   | Wpisz nazwę wystąpienia programu System Center Service Manager, który chcesz połączyć z ITSMC.  Możesz użyć tej nazwy później skonfigurować elementy robocze w tym wystąpieniu / wyświetlić szczegółowy dziennik analizy. |
| **Typ partnera**   | Wybierz **programu System Center Service Manager**. |
| **Adres URL serwera**   | Wpisz adres URL aplikacji sieci Web programu Service Manager. Więcej informacji na temat aplikacji sieci Web programu Service Manager jest [tutaj](#create-and-deploy-service-manager-web-app-service).
| **Identyfikator klienta**   | Wpisz identyfikator klienta, generowany (przy użyciu skryptu automatyczne) w celu uwierzytelniania aplikacji sieci Web. Więcej informacji na temat zautomatyzowanego skryptu [tutaj.](log-analytics-itsmc-service-manager-script.md)|
| **Klucz tajny klienta**   | Wpisz klucz tajny klienta, generowany dla tego identyfikatora.   |
| **Zakres synchronizacji danych**   | Wybierz elementy pracy programu Service Manager, które mają być synchronizowane za pośrednictwem ITSMC.  Pracy, te elementy są importowane do analizy dzienników. **Opcje:** incydenty, żądania zmiany.|
| **Synchronizowanie danych** | Wpisz liczbę ostatnich dni, które mają dane z. **Maksymalny limit**: 120 dni. |
| **Utwórz nowy element konfiguracji w rozwiązaniu Zarządzanie usługami IT —** | Wybierz tę opcję, jeśli chcesz utworzyć elementy konfiguracji w produkcie Zarządzanie usługami IT —. Po wybraniu pakietu OMS tworzy dotyczy konfiguracji (ci) jako elementy konfiguracji (w przypadku nieistniejących SIC) w obsługiwany system Zarządzanie usługami IT —. **Domyślna**: wyłączone. |

![Usługa Menedżera połączeń](./media/log-analytics-itsmc/service-manager-connection.png)

**Gdy pomyślnie nawiązano połączenie i zsynchronizowane**:

- Wybrane elementy robocze z programu Service Manager są importowane do Azure **analizy dzienników.** Można wyświetlić podsumowanie tych elementów roboczych na kafelku łącznika zarządzania usługi IT.

- Można utworzyć zdarzenia z alertów analizy dzienników lub rekordów dziennika lub Azure alertów w tym wystąpieniu programu Service Manager.


Dowiedz się więcej: [Zarządzanie usługami IT — tworzenie elementów roboczych z alertów Azure](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-and-deploy-service-manager-web-app-service"></a>Tworzenie i wdrażanie aplikacji sieci web programu Service Manager

Aby połączyć lokalnymi Service Manager z ITSMC na platformie Azure, firma Microsoft opracowała aplikacji sieci Web programu Service Manager w witrynie GitHub.

Aby skonfigurować aplikację sieci Web Zarządzanie usługami IT — dla programu Service Manager, wykonaj następujące czynności:

- **Wdrażanie aplikacji sieci Web** — wdrażanie aplikacji sieci Web, ustaw właściwości i uwierzytelniania za pomocą usługi Azure AD. Możesz wdrożyć aplikację sieci web za pomocą [zautomatyzowanego skryptu](log-analytics-itsmc-service-manager-script.md) że firma Microsoft udostępnia użytkownik.
- **Skonfiguruj połączenie hybrydowe** - [Skonfiguruj połączenie z tym](#configure-the-hybrid-connection), ręcznie.

#### <a name="deploy-the-web-app"></a>Wdrażanie aplikacji sieci web
Użyj automatycznego [skryptu](log-analytics-itsmc-service-manager-script.md) Aby wdrożyć aplikację sieci Web, ustaw właściwości oraz uwierzytelniania za pomocą usługi Azure AD.

Uruchom skrypt, podając następujące wymagane szczegóły:

- Szczegóły subskrypcji platformy Azure
- Nazwa grupy zasobów
- Lokalizacja
- Szczegóły serwera programu Service Manager (nazwa serwera, domeny, nazwę użytkownika i hasło)
- Prefiks nazwy witryny dla aplikacji sieci Web
- Namespace magistrali usług.

Skrypt tworzy aplikacji sieci Web przy użyciu nazwy określonej (wraz z kilku dodatkowe ciągi go). Generuje on **adres URL aplikacji sieci Web**, **identyfikator klienta** i **klucz tajny klienta**.

Zapisywanie wartości możesz użyć ich podczas tworzenia połączenia z ITSMC.

**Sprawdź instalację aplikacji sieci Web**

1. Przejdź do **portalu Azure** > **zasobów**.
2. Wybierz aplikację sieci Web, kliknij przycisk **ustawienia** > **ustawienia aplikacji**.
3. Potwierdź informacje podane w czasie wdrażania aplikacji za pomocą skryptu wystąpienia programu Service Manager.

### <a name="configure-the-hybrid-connection"></a>Skonfiguruj połączenie hybrydowe

Poniższa procedura umożliwia skonfigurowanie połączenie hybrydowe, które łączy wystąpienia programu Service Manager z ITSMC na platformie Azure.

1. Znajdź aplikację sieci Web programu Service Manager w obszarze **zasobów Azure**.
2. Kliknij przycisk **ustawienia** > **sieci**.
3. W obszarze **połączeń hybrydowych**, kliknij przycisk **skonfiguruj punkty końcowe połączenia hybrydowego**.

    ![Sieciowe połączenie hybrydowe](./media/log-analytics-itsmc/itsmc-hybrid-connection-networking-and-end-points.png)
4. W **połączeń hybrydowych** bloku, kliknij przycisk **Dodaj połączenie hybrydowe**.

    ![Dodaj połączenie hybrydowe](./media/log-analytics-itsmc/itsmc-new-hybrid-connection-add.png)

5. W **dodać połączeń hybrydowych** bloku, kliknij przycisk **tworzenie hybrydowych nowego połączenia**.

    ![Nowe połączenie hybrydowe](./media/log-analytics-itsmc/itsmc-create-new-hybrid-connection.png)

6. Wpisz następujące wartości:

    - **Nazwa punktu końcowego**: Określ nazwę nowego połączenia hybrydowego.
    -  **Punkt końcowy hosta**: nazwa FQDN serwera zarządzania programu Service Manager.
    - **Port punktu końcowego**: wpisz 5724
    - **Przestrzeń nazw magistrali usług**: Użyj istniejącej przestrzeni nazw magistrali usług lub Utwórz nową.
    - **Lokalizacja**: Wybierz lokalizację.
    -  **Nazwa**: Określ nazwę do magistrali usług, jeśli jej tworzenia.

    ![Wartości połączenia hybrydowego](./media/log-analytics-itsmc/itsmc-new-hybrid-connection-values.png)
6. Kliknij przycisk **OK** zamknąć **Tworzenie połączenia hybrydowego** bloku i rozpocząć tworzenie połączenia hybrydowego.

    Po utworzeniu połączenia hybrydowego jest wyświetlany w bloku.

7. Po utworzeniu połączenia hybrydowe, wybierz połączenie i kliknij przycisk **Dodaj wybrane połączenie hybrydowe**.

    ![Nowe połączenie hybrydowe](./media/log-analytics-itsmc/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>Skonfiguruj ustawienia odbiornika

Poniższa procedura umożliwia skonfigurowanie ustawień odbiornika połączenia hybrydowego.

1. W **połączeń hybrydowych** bloku, kliknij przycisk **pobrać Menedżera połączeń** i zainstaluj go na komputerze, na którym jest uruchomione wystąpienie programu System Center Service Manager.

    Po zakończeniu instalacji **interfejsu użytkownika Menedżera połączeń hybrydowych** opcja jest dostępna w obszarze **Start** menu.

2. Kliknij przycisk **interfejsu użytkownika Menedżera połączeń hybrydowych** , zostanie wyświetlony monit o podanie poświadczeń platformy Azure.

3. Logowanie przy użyciu poświadczeń platformy Azure i wybierz subskrypcję, w której utworzono połączenie hybrydowe.

4. Kliknij pozycję **Zapisz**.

Połączenia hybrydowe został pomyślnie połączony.

![połączenia hybrydowe powiodło się](./media/log-analytics-itsmc/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]

> Po hybrydowego tworzone jest połączenie, sprawdzić i przetestować połączenie, przechodząc na stronę wdrożonej aplikacji sieci Web programu Service Manager. Upewnij się, że połączenie zostanie nawiązane, przed podjęciem próby nawiązania połączenia ITSMC na platformie Azure.

Na poniższej ilustracji próbki przedstawiono szczegóły połączenia:

![Testowanie połączenia hybrydowego](./media/log-analytics-itsmc/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-azure"></a>Połączenie usługi ServiceNow z usługi IT łącznika zarządzania na platformie Azure

Poniższe sekcje zawierają szczegółowe informacje na temat nawiązywania połączenia produktu usługi ServiceNow z ITSMC na platformie Azure.

### <a name="prerequisites"></a>Wymagania wstępne
Upewnij się, że są spełnione następujące wymagania wstępne:
- ITSMC zainstalowane. Więcej informacji: [Dodawanie rozwiązania do zarządzania łącznika usługi IT](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Usługi ServiceNow obsługiwane wersje: Kingston, Dżakarta, Stambuł, Helsinki, serwera Geneva.

**Administratorzy usługi ServiceNow musi wykonać następujące czynności w ich wystąpienia usługi ServiceNow**:
- Generują identyfikator klienta i klucz tajny klienta usługi ServiceNow produktu. Aby uzyskać informacje na temat generowania identyfikator klienta i klucz tajny zobacz poniższe informacje zgodnie z wymaganiami:

    - [Konfigurowanie uwierzytelniania OAuth dla Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurowanie uwierzytelniania OAuth dla Dżakarta](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurowanie uwierzytelniania OAuth dla Stambuł](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurowanie uwierzytelniania OAuth dla Helsinkach](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurowanie uwierzytelniania OAuth dla serwera Geneva](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)


- Zainstaluj aplikację użytkownika integracji OMS firmy Microsoft (usługi ServiceNow aplikacji). [Dowiedz się więcej](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
- Utworzenie roli użytkownika integracji dla zainstalowanej aplikacji użytkownika. Informacje dotyczące sposobu tworzenia roli użytkownika integracji [tutaj](#create-integration-user-role-in-servicenow-app).

### <a name="connection-procedure"></a>**Procedura połączenia**
Użyj poniższej procedury, aby utworzyć połączenie usługi ServiceNow:


1. W portalu Azure, przejdź do **wszystkie zasoby** i poszukaj **ServiceDesk(YourWorkspaceName)**

2.  W obszarze **źródeł danych obszaru roboczego** kliknij **połączeń Zarządzanie usługami IT —**.
    ![Nowe połączenie](./media/log-analytics-itsmc/add-new-itsm-connection.png)

3. W górnej części okienku po prawej stronie, kliknij przycisk **Dodaj**.

4. Podaj informacje zgodnie z opisem w poniższej tabeli, a następnie kliknij przycisk **OK** do utworzenia połączenia.


> [!NOTE]
> Te parametry są obowiązkowe.

| **Pole** | **Opis** |
| --- | --- |
| **Nazwa połączenia**   | Wpisz nazwę wystąpienia usługi ServiceNow, który chcesz połączyć z ITSMC.  Ta nazwa można użyć w dalszej części OMS, podczas konfigurowania elementów roboczych w tym zarządzanie usługami IT — / wyświetlić szczegółowy dziennik analizy. |
| **Typ partnera**   | Wybierz **ServiceNow**. |
| **Nazwa użytkownika**   | Wpisz nazwę użytkownika integracji utworzony w aplikacji usługi ServiceNow obsługuje połączenie ITSMC. Więcej informacji: [roli użytkownika aplikacji tworzenia usługi ServiceNow](#create-integration-user-role-in-servicenow-app).|
| **Hasło**   | Wpisz hasło skojarzone z tą nazwą użytkownika. **Uwaga**: nazwa użytkownika i hasło są używane do generowania tokenów uwierzytelniania tylko, a nie są przechowywane w dowolnym miejscu w ramach usługi ITSMC.  |
| **Adres URL serwera**   | Wpisz adres URL wystąpienia usługi ServiceNow, który chcesz połączyć się ITSMC. |
| **Identyfikator klienta**   | Wpisz identyfikator klienta, który ma być używany do uwierzytelniania protokołu OAuth2, który można wygenerować wcześniej.  Więcej informacji na temat generowania identyfikator klienta i klucz tajny: [instalacji uwierzytelniania OAuth](http://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Klucz tajny klienta**   | Wpisz klucz tajny klienta, generowany dla tego identyfikatora.   |
| **Zakres synchronizacji danych**   | Wybierz elementy robocze usługi ServiceNow, które mają być synchronizowane z usługą Analiza dzienników Azure, za pośrednictwem ITSMC.  Wybrane wartości są importowane do analizy dzienników.   **Opcje:** zdarzenia i żądania zmiany.|
| **Synchronizowanie danych** | Wpisz liczbę ostatnich dni, które mają dane z. **Maksymalny limit**: 120 dni. |
| **Utwórz nowy element konfiguracji w rozwiązaniu Zarządzanie usługami IT —** | Wybierz tę opcję, jeśli chcesz utworzyć elementy konfiguracji w produkcie Zarządzanie usługami IT —. Po wybraniu ITSMC tworzy dotyczy konfiguracji (ci) jako elementy konfiguracji (w przypadku nieistniejących SIC) w obsługiwany system Zarządzanie usługami IT —. **Domyślna**: wyłączone. |

![Połączenie usługi ServiceNow](./media/log-analytics-itsmc/itsm-connection-servicenow-connection-latest.png)

**Gdy pomyślnie nawiązano połączenie i zsynchronizowane**:

- Wybrane elementy robocze z wystąpienia usługi ServiceNow są importowane do Azure **analizy dzienników.** Można wyświetlić podsumowanie tych elementów roboczych na kafelku łącznika zarządzania usługi IT.

- Można utworzyć zdarzenia z alertów analizy dzienników lub rekordów dziennika lub Azure alertów w tym wystąpieniu usługi ServiceNow.

Dowiedz się więcej: [Zarządzanie usługami IT — tworzenie elementów roboczych z alertów Azure](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-integration-user-role-in-servicenow-app"></a>Utworzenie roli użytkownika integracji usługi ServiceNow aplikacji

Użytkownik następującej procedury:

1.  Odwiedź stronę [magazynu usługi ServiceNow](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) i zainstaluj **użytkownika aplikacji dla usługi ServiceNow i integracja z usługą OMS Microsoft** do swojego wystąpienia usługi ServiceNow.
2.  Po instalacji można znaleźć na pasku nawigacyjnym po lewej stronie wystąpienia usługi ServiceNow, wyszukaj i wybierz integrator OMS firmy Microsoft.  
3.  Kliknij przycisk **listy kontrolnej instalacji**.

    Stan jest wyświetlany jako **nie zakończyć** Jeśli jeszcze rola użytkownika ma być utworzony.

4.  W polach tekstowych obok **tworzenia integracji użytkownika**, wprowadź nazwę użytkownika dla użytkownika, który może łączyć się ITSMC na platformie Azure.
5.  Wprowadź hasło dla tego użytkownika, a następnie kliknij przycisk **OK**.  

>[!NOTE]

> Można używać tych poświadczeń do nawiązania połączenia usługi ServiceNow na platformie Azure.

Nowo utworzony użytkownik zostanie wyświetlony z domyślne role przypisane.

**Domyślne role**:
- personalize_choices
- import_transformer
-   x_mioms_microsoft.user
-   ITIL
-   template_editor
-   view_changer

Po pomyślnym utworzeniu użytkownika, stan **Sprawdź listę kontrolną instalacji** przenosi zakończony, wyświetlania szczegółów roli użytkownika utworzone dla aplikacji.

> [!NOTE]

> Zarządzanie usługami IT — łącznika można wysłać zdarzenia do usługi ServiceNow bez inne moduły zainstalowane w wystąpieniu usługi ServiceNow. Jeśli używasz modułu EventManagement wystąpienia usługi ServiceNow i chcesz utworzyć zdarzenia lub alerty za pomocą łącznika usługi ServiceNow, należy dodać następujące role użytkownika integracji:

>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-azure"></a>Połącz Provance usługi IT łącznika zarządzania na platformie Azure

Poniższe sekcje zawierają szczegółowe informacje na temat nawiązać produktu Provance ITSMC na platformie Azure.


### <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że są spełnione następujące wymagania wstępne:


- ITSMC zainstalowane. Więcej informacji: [Dodawanie rozwiązania do zarządzania łącznika usługi IT](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Provance aplikacji powinny zostać zarejestrowane w usłudze Azure AD - oraz Identyfikatora klienta jest dostępna. Aby uzyskać szczegółowe informacje, zobacz [jak skonfigurować uwierzytelnianie usługi active directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

- Rola: Administrator.

### <a name="connection-procedure"></a>Procedura połączenia

Użyj poniższej procedury, aby utworzyć połączenie Provance:

1. W portalu Azure, przejdź do **wszystkie zasoby** i poszukaj **ServiceDesk(YourWorkspaceName)**

2.  W obszarze **źródeł danych obszaru roboczego** kliknij **połączeń Zarządzanie usługami IT —**.
    ![Nowe połączenie](./media/log-analytics-itsmc/add-new-itsm-connection.png)

3. W górnej części okienku po prawej stronie, kliknij przycisk **Dodaj**.

4. Podaj informacje zgodnie z opisem w poniższej tabeli, a następnie kliknij przycisk **OK** do utworzenia połączenia.

> [!NOTE]

> Te parametry są obowiązkowe.

| **Pole** | **Opis** |
| --- | --- |
| **Nazwa połączenia**   | Wpisz nazwę wystąpienia Provance, który chcesz połączyć z ITSMC.  Możesz użyć tej nazwy później skonfigurować elementy robocze w tym zarządzanie usługami IT — / wyświetlić szczegółowy dziennik analizy. |
| **Typ partnera**   | Wybierz **Provance**. |
| **Nazwa użytkownika**   | Wpisz nazwę użytkownika, który może łączyć się ITSMC.    |
| **Hasło**   | Wpisz hasło skojarzone z tą nazwą użytkownika. **Uwaga:** nazwę użytkownika i hasło są używane do generowania tylko tokeny uwierzytelniania i nie są przechowywane w dowolnym miejscu w ramach usługi ITSMC. _|
| **Adres URL serwera**   | Wpisz adres URL wystąpienia Provance, który chcesz połączyć się ITSMC. |
| **Identyfikator klienta**   | Wpisz identyfikator klienta do uwierzytelniania połączenia, który można wygenerować wystąpienia Provance.  Więcej informacji na temat Identyfikatora klienta, zobacz [jak skonfigurować uwierzytelnianie usługi active directory](../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md). |
| **Zakres synchronizacji danych**   | Wybierz elementy robocze Provance, które mają być synchronizowane z usługą Analiza dzienników Azure, za pośrednictwem ITSMC.  Pracy, te elementy są importowane do analizy dzienników.   **Opcje:** incydenty, żądania zmiany.|
| **Synchronizowanie danych** | Wpisz liczbę ostatnich dni, które mają dane z. **Maksymalny limit**: 120 dni. |
| **Utwórz nowy element konfiguracji w rozwiązaniu Zarządzanie usługami IT —** | Wybierz tę opcję, jeśli chcesz utworzyć elementy konfiguracji w produkcie Zarządzanie usługami IT —. Po wybraniu ITSMC tworzy dotyczy konfiguracji (ci) jako elementy konfiguracji (w przypadku nieistniejących SIC) w obsługiwany system Zarządzanie usługami IT —. **Domyślna**: wyłączone.|

![Provance połączenia](./media/log-analytics-itsmc/itsm-connections-provance-latest.png)

**Gdy pomyślnie nawiązano połączenie i zsynchronizowane**:

- Wybrane elementy robocze z tego wystąpienia Provance są importowane do Azure **analizy dzienników.** Można wyświetlić podsumowanie tych elementów roboczych na kafelku łącznika zarządzania usługi IT.

- Można utworzyć zdarzenia z alertów analizy dzienników lub rekordów dziennika lub Azure alertów w tym wystąpieniu Provance.

Dowiedz się więcej: [Zarządzanie usługami IT — tworzenie elementów roboczych z alertów Azure](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

## <a name="connect-cherwell-to-it-service-management-connector-in-azure"></a>Połącz Cherwell usługi IT łącznika zarządzania na platformie Azure

Poniższe sekcje zawierają szczegółowe informacje na temat nawiązać produktu Cherwell ITSMC na platformie Azure.

### <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że są spełnione następujące wymagania wstępne:

- ITSMC zainstalowane. Więcej informacji: [Dodawanie rozwiązania do zarządzania łącznika usługi IT](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Wygenerowany identyfikator klienta. Więcej informacji: [generowania Identyfikatora klienta dla Cherwell](#generate-client-id-for-cherwell).
- Rola: Administrator.

### <a name="connection-procedure"></a>Procedura połączenia

Użyj poniższej procedury, aby utworzyć połączenie Provance:

1. W portalu Azure, przejdź do **wszystkie zasoby** i poszukaj **ServiceDesk(YourWorkspaceName)**

2.  W obszarze **źródeł danych obszaru roboczego** kliknij **połączeń Zarządzanie usługami IT —**.
    ![Nowe połączenie](./media/log-analytics-itsmc/add-new-itsm-connection.png)

3. W górnej części okienku po prawej stronie, kliknij przycisk **Dodaj**.

4. Podaj informacje zgodnie z opisem w poniższej tabeli, a następnie kliknij przycisk **OK** do utworzenia połączenia.

> [!NOTE]

> Te parametry są obowiązkowe.

| **Pole** | **Opis** |
| --- | --- |
| **Nazwa połączenia**   | Wpisz nazwę wystąpienia Cherwell, który chcesz połączyć się ITSMC.  Możesz użyć tej nazwy później skonfigurować elementy robocze w tym zarządzanie usługami IT — / wyświetlić szczegółowy dziennik analizy. |
| **Typ partnera**   | Wybierz **Cherwell.** |
| **Nazwa użytkownika**   | Wpisz nazwę użytkownika Cherwell, który może łączyć się ITSMC. |
| **Hasło**   | Wpisz hasło skojarzone z tą nazwą użytkownika. **Uwaga:** nazwę użytkownika i hasło są używane do generowania tylko tokeny uwierzytelniania i nie są przechowywane w dowolnym miejscu w ramach usługi ITSMC.|
| **Adres URL serwera**   | Wpisz adres URL wystąpienia Cherwell, który chcesz połączyć się ITSMC. |
| **Identyfikator klienta**   | Wpisz identyfikator klienta do uwierzytelniania połączenia, który można wygenerować wystąpienia Cherwell.   |
| **Zakres synchronizacji danych**   | Wybierz elementy robocze Cherwell, które chcesz zsynchronizować za pośrednictwem ITSMC.  Pracy, te elementy są importowane do analizy dzienników.   **Opcje:** incydenty, żądania zmiany. |
| **Synchronizowanie danych** | Wpisz liczbę ostatnich dni, które mają dane z. **Maksymalny limit**: 120 dni. |
| **Utwórz nowy element konfiguracji w rozwiązaniu Zarządzanie usługami IT —** | Wybierz tę opcję, jeśli chcesz utworzyć elementy konfiguracji w produkcie Zarządzanie usługami IT —. Po wybraniu ITSMC tworzy dotyczy konfiguracji (ci) jako elementy konfiguracji (w przypadku nieistniejących SIC) w obsługiwany system Zarządzanie usługami IT —. **Domyślna**: wyłączone. |


![Provance połączenia](./media/log-analytics-itsmc/itsm-connections-cherwell-latest.png)

**Gdy pomyślnie nawiązano połączenie i zsynchronizowane**:

- Wybrane elementy robocze z tego wystąpienia Cherwell są importowane do Azure **analizy dzienników.** Można wyświetlić podsumowanie tych elementów roboczych na kafelku łącznika zarządzania usługi IT.

- Można utworzyć zdarzenia z alertów analizy dzienników lub rekordów dziennika lub Azure alertów w tym wystąpieniu Cherwell.

Dowiedz się więcej: [Zarządzanie usługami IT — tworzenie elementów roboczych z alertów Azure](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Generuj identyfikator klienta dla Cherwell

Aby wygenerować identyfikator/klucz klienta dla Cherwell, użyj następującej procedury:

1. Zaloguj się do swojego wystąpienia Cherwell jako administrator.
2. Kliknij przycisk **zabezpieczeń** > **ustawień klienta interfejsu API REST Edytuj**.
3. Wybierz **Utwórz nowy klient** > **klucz tajny klienta**.

    ![Identyfikator użytkownika Cherwell](./media/log-analytics-itsmc/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>Kolejne kroki
 - [Tworzenie elementów roboczych Zarządzanie usługami IT — Azure alertów](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
