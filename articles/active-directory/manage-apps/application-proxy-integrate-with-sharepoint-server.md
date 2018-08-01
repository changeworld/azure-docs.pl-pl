---
title: Zdalny dostęp do programu SharePoint przy użyciu serwera Proxy aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Zawiera podstawowe informacje dotyczące sposobu integrowania programu SharePoint server w środowisku lokalnym dzięki serwerowi Proxy aplikacji usługi Azure AD.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/06/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: fcd02e264d5e85b1bef7e75d2a6375d6bf5e18c0
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39363953"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Zdalny dostęp do programu SharePoint przy użyciu serwera Proxy aplikacji usługi Azure AD

W tym artykule omówiono sposób integrowania programu SharePoint server w środowisku lokalnym dzięki serwerowi Proxy aplikacji usługi Azure Active Directory (Azure AD).

Aby włączyć dostęp zdalny do programu SharePoint przy użyciu serwera Proxy aplikacji usługi Azure AD, wykonaj sekcje w tym artykule krok po kroku.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że masz już program SharePoint 2013 lub nowszym w danym środowisku. Ponadto należy wziąć pod uwagę następujące wymagania wstępne:

* Program SharePoint obsługuje natywnego protokołu Kerberos. W związku z tym użytkownicy, którzy uzyskują dostęp do wewnętrznych witryn firmowych zdalnie za pośrednictwem serwera Proxy aplikacji usługi Azure AD, można założyć na środowisko logowania jednokrotnego (SSO).

* Ten scenariusz obejmuje zmiany w konfiguracji serwera programu SharePoint. Firma Microsoft zaleca korzystanie w środowisku przejściowym. Dzięki temu można dokonać aktualizacji serwera przejściowego najpierw i następnie ułatwienia cyklu testowania przed przejściem do środowiska produkcyjnego.

* Firma Microsoft wymaga protokołu SSL w adresie URL publikacji. Musisz mieć włączony w wewnętrznej witrynie, aby upewnić się, czy łącza są wysyłane mapowane poprawnie protokół SSL. Jeśli nie skonfigurowano protokół SSL, zobacz [Konfigurowanie protokołu SSL dla programu SharePoint 2013](https://blogs.msdn.microsoft.com/fabdulwahab/2013/01/20/configure-ssl-for-sharepoint-2013) instrukcje. Ponadto upewnij się, że na maszynie łącznika ufa certyfikat, który możesz wydać. (Certyfikat nie musi być publicznie wydanych.)

## <a name="step-1-set-up-single-sign-on-to-sharepoint"></a>Krok 1: Konfigurowanie logowania jednokrotnego do programu SharePoint

Aplikacje lokalne, które korzystają z uwierzytelniania Windows można osiągnąć logowania jednokrotnego (SSO) przy użyciu protokołu uwierzytelniania Kerberos i funkcję ograniczonego delegowania protokołu Kerberos (KCD). Ograniczonego delegowania protokołu Kerberos, po skonfigurowaniu umożliwia łącznik serwera Proxy aplikacji w celu uzyskania tokenu Windows użytkownika, nawet wtedy, gdy użytkownik nie zarejestrował się usłudze Windows bezpośrednio. Aby dowiedzieć się więcej na temat ograniczonego delegowania protokołu Kerberos, zobacz [omówienie delegowania ograniczonego protokołu Kerberos](https://technet.microsoft.com/library/jj553400.aspx).

Do skonfigurowania ograniczonego delegowania protokołu Kerberos dla programu SharePoint server, korzystając z procedur w kolejnych sekcjach:

### <a name="ensure-that-sharepoint-is-running-under-a-service-account"></a>Upewnij się, że program SharePoint jest uruchomiony w ramach konta usługi

Najpierw upewnij się, że program SharePoint jest uruchomiony w ramach konta usługi zdefiniowane — system lokalny, Usługa lokalna lub usługi sieciowej. W tym dołączenie głównych nazw usług (SPN) prawidłowe konto. Nazwy SPN są, jak protokół Kerberos identyfikuje różnych usług. I musisz mieć konto później, aby skonfigurować ograniczonego delegowania protokołu Kerberos.

> [!NOTE]
Musisz mieć wcześniej utworzone konto usługi Azure AD dla usługi. Sugerujemy, umożliwiający automatyczną zmianę hasła. Aby uzyskać więcej informacji na temat pełnego zestawu kroków i rozwiązywanie problemów, zobacz [skonfigurować automatyczną zmianę hasła w programie SharePoint 2013](https://technet.microsoft.com/library/ff724280.aspx).

Aby upewnić się, że lokacje są uruchomione na koncie usługi zdefiniowane, wykonaj następujące czynności:

1. Otwórz **administracji centralnej programu SharePoint 2013** lokacji.
2. Przejdź do **zabezpieczeń** i wybierz **Konfigurowanie kont usług**.
3. Wybierz **- SharePoint - 80. pula aplikacji sieci Web**. Opcje mogą być nieco inne na podstawie nazwy puli w sieci web, lub jeśli puli sieci web domyślnie używa protokołu SSL.

  ![Opcje dotyczące konfigurowania konta usługi](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

4. Jeśli **wybierz konto dla tego składnika** pole jest ustawione na **Usługa lokalna** lub **Usługa sieciowa**, musisz utworzyć konto. W przeciwnym razie jesteś gotowy i przejściem do następnej sekcji.
5. Wybierz **zarejestrować nowego zarządzanego konta**. Po utworzeniu konta należy ustawić **puli aplikacji sieci Web** zanim będzie można użyć tego konta.

### <a name="configure-sharepoint-for-kerberos"></a>Konfigurowanie programu SharePoint dla protokołu Kerberos

Używasz ograniczonego delegowania protokołu Kerberos do wykonywania logowania jednokrotnego do serwera programu SharePoint.

Aby skonfigurować witryny programu SharePoint dla uwierzytelniania Kerberos:

1. Otwórz **administracji centralnej programu SharePoint 2013** lokacji.
2. Przejdź do **Zarządzanie aplikacjami**, wybierz opcję **Zarządzaj aplikacjami sieci web**i wybierz witrynę programu SharePoint. W tym przykładzie jest **SharePoint — 80**.

  ![Wybieranie witryny programu SharePoint](./media/application-proxy-integrate-with-sharepoint-server/manage-web-applications.png)

3. Kliknij przycisk **dostawców uwierzytelniania** na pasku narzędzi.
4. W **dostawców uwierzytelniania** kliknij **domyślna strefa** Aby wyświetlić ustawienia.
5. W **Edytuj uwierzytelnianie** okna dialogowego pole, przewiń w dół, aż zobaczysz **typy uwierzytelniania oświadczeń**. Upewnij się, że oba **Włącz uwierzytelnianie Windows** i **zintegrowane uwierzytelnianie Windows** są zaznaczone.
6. Upewnij się, że w polu listy rozwijanej w polu zintegrowane uwierzytelnianie Windows **Negotiate (Kerberos)** jest zaznaczone.

  ![Okno dialogowe Edytowanie uwierzytelniania](./media/application-proxy-integrate-with-sharepoint-server/service-edit-authentication.png)

7. W dolnej części **Edytuj uwierzytelnianie** okno dialogowe, kliknij przycisk **Zapisz**.

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>Ustaw główną nazwę usługi dla konta usługi programu SharePoint

Przed rozpoczęciem konfigurowania ograniczonego delegowania protokołu Kerberos, należy zidentyfikować usługi programu SharePoint, uruchomione jako konto usługi, które zostały skonfigurowane. Należy określić usługi, ustawiając nazwę SPN. Aby uzyskać więcej informacji, zobacz [główne nazwy usług](https://technet.microsoft.com/library/cc961723.aspx).

Format nazwy SPN jest następujący:

```
<service class>/<host>:<port>
```

Format nazwy SPN:

* _usługi klasy_ jest unikatową nazwą usługi. W programie SharePoint, należy użyć **HTTP**.

* _host_ jest w pełni kwalifikowaną lub nazwę NetBIOS tego usługa jest uruchomiona na hoście. Witryny programu SharePoint ten tekst może być konieczne jest adres URL witryny, w zależności od wersji usług IIS, którego używasz.

* _port_ jest opcjonalne.

Jeśli nazwa FQDN serwera programu SharePoint:

```
sharepoint.demo.o365identity.us
```

To jest główna nazwa usługi:

```
HTTP/sharepoint.demo.o365identity.us demo
```

Również może być konieczne ustawianie nazwy SPN dla określonych witryn na serwerze. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie Kerberos skonfigurować](https://technet.microsoft.com/library/cc263449(v=office.12).aspx). Należy zwracać szczególną uwagę na sekcję "Tworzenie główne nazwy usługi dla aplikacji sieci Web przy użyciu uwierzytelniania Kerberos".

Można ustawić nazwy SPN najłatwiej wykonać formatów nazwy SPN, które mogą być już dostępne dla Twoich witryn. Skopiuj te nazwy SPN, aby zarejestrować względem konta usługi. W tym celu:

1. Przejdź do witryny przy użyciu nazwy SPN z innego komputera.
 Po wykonaniu odpowiedni zestaw bilety protokołu Kerberos jest buforowana na maszynie. Bilety zawierają nazwę SPN w lokacji docelowej, które zostały wybrane.

2. Nazwę SPN dla tej lokacji możesz ściągnąć przy użyciu narzędzia o nazwie [Klist](http://web.mit.edu/kerberos/krb5-devel/doc/user/user_commands/klist.html). W oknie polecenia, które działa w tym samym kontekście jako użytkownik, który uzyskał dostęp do witryny w przeglądarce uruchom następujące polecenie:
```
Klist
```
Klist następnie zwraca zestaw elementów docelowych nazwy SPN. W tym przykładzie wartość wyróżnione jest główną nazwę usługi, które jest potrzebne:

  ![Przykładowe wyniki Klist](./media/application-proxy-integrate-with-sharepoint-server/remote-sharepoint-target-service.png)

4. Teraz, gdy masz nazwę SPN, upewnij się, że został on poprawnie skonfigurowany na koncie usługi skonfigurowanej wcześniej aplikacji sieci web. Uruchom następujące polecenie w wierszu polecenia jako administrator domeny:

 ```
 setspn -S http/sharepoint.demo.o365identity.us demo\sp_svc
 ```

 To polecenie ustawia nazwę SPN dla konta usługi programu SharePoint, które są uruchomione jako _demo\sp_svc_.

 Zastąp _http/sharepoint.demo.o365identity.us_ z główną nazwą usługi serwera i _demo\sp_svc_ przy użyciu konta usługi w danym środowisku. Polecenia Setspn wyszukuje nazwy SPN, przed dodaniem go. W takim przypadku można napotkać **zduplikowana wartość SPN** błędu. Jeśli zostanie wyświetlony ten błąd, upewnij się, że wartość jest skojarzony z kontem usługi.

Aby sprawdzić, czy nazwa SPN została dodana przy użyciu polecenia Setspn z opcją -l. Aby dowiedzieć się więcej na temat tego polecenia, zobacz [Setspn](https://technet.microsoft.com/library/cc731241.aspx).

### <a name="ensure-that-the-connector-is-set-as-a-trusted-delegate-to-sharepoint"></a>Upewnij się, że łącznik jest ustawiony jako zaufanego delegata do programu SharePoint

Konfigurowanie ograniczonego delegowania protokołu Kerberos, tak, aby usługa serwera Proxy aplikacji usługi Azure AD mogą delegować uprawnienia do tożsamości użytkowników do usługi SharePoint. Konfigurowanie ograniczonego delegowania protokołu Kerberos, włączając łącznik serwera Proxy aplikacji pobrać bilety protokołu Kerberos dla użytkowników, którzy zostali uwierzytelnieni w usłudze Azure AD. Następnie ten serwer przekazuje kontekst do aplikacji docelowej lub programu SharePoint w tym przypadku.

Aby skonfigurować ograniczonego delegowania protokołu Kerberos, wykonaj następujące czynności dla każdej maszyny łącznika:

1. Zaloguj się jako administrator domeny do kontrolera domeny, a następnie otwórz **użytkownicy usługi Active Directory i komputery**.
2. Znajdź komputera, na którym jest zasilany z łącznika. W tym przykładzie jest tym samym serwerze programu SharePoint.
3. Kliknij dwukrotnie komputer, a następnie kliknij przycisk **delegowania** kartę.
4. Upewnij się, że ustawienia delegowania są ustawione na **Ufaj temu komputerowi w delegowaniu tylko do określonych usług**. Następnie wybierz **Użyj dowolnego protokołu uwierzytelniania**.

  ![Ustawienia delegowania](./media/application-proxy-integrate-with-sharepoint-server/delegation-box.png)

5. Kliknij przycisk **Dodaj** przycisku, kliknij przycisk **użytkowników lub komputerów**, a następnie zlokalizuj konto usługi.

  ![Dodawanie nazwy SPN dla konta usługi](./media/application-proxy-integrate-with-sharepoint-server/users-computers.png)

6. Na liście nazw SPN wybierz jedną, która została wcześniej utworzona dla konta usługi.
7. Kliknij przycisk **OK**. Kliknij przycisk **OK** ponownie, aby zapisać zmiany.

## <a name="step-2-enable-remote-access-to-sharepoint"></a>Krok 2: Włączanie dostępu zdalnego w programie SharePoint

Teraz, po włączeniu programu SharePoint dla protokołu Kerberos i skonfigurowanym ograniczonego delegowania protokołu Kerberos, jesteś gotowy do opublikowania farmy programu SharePoint dla dostępu zdalnego za pośrednictwem serwera Proxy aplikacji usługi Azure AD.

1. Publikowanie witryny programu SharePoint z następującymi ustawieniami. Aby uzyskać instrukcje krok po kroku, zobacz [publikowanie aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-publish-azure-portal.md). 
   - **Wewnętrzny adres URL**: adres URL witryny programu SharePoint, takich jak **https://SharePoint/**. W tym przykładzie, upewnij się, że używasz **protokołu https**
   - **Metoda uwierzytelniania wstępnego**: Usługa Azure Active Directory
   - **Przetłumacz URL w nagłówkach**: Brak

   >[!TIP]
   >Program SharePoint używa _nagłówek hosta_ wartość do wyszukania w witrynie. Polecenie to generuje także łączy na podstawie tej wartości. Efektem sieciowym jest to dowolny link, który generuje programie SharePoint opublikowanego adresu URL, który jest prawidłowo skonfigurowany do używania zewnętrznego adresu URL. Ustawienie wartości **tak** umożliwia również łącznik aby przekazywać żądania do aplikacji zaplecza. Jednakże, ustawiając wartość na **nie** oznacza, że łącznik nie będzie wysyłać nazwę hosta wewnętrznego. Zamiast tego łącznika wysyła nagłówek hosta jako adres URL opublikowanej aplikacji zaplecza.

   ![Publikowanie programu SharePoint jako aplikację](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

2. Po opublikowaniu aplikacji pojedynczego ustawień logowania jednokrotnego należy skonfigurować następujące czynności:

   1. Na stronie aplikacji w portalu wybierz **logowanie jednokrotne**.
   2. Pojedynczego logowania jednokrotnego, wybierz tryb **zintegrowane uwierzytelnianie Windows**.
   3. Główna nazwa usługi aplikacji wewnętrznej należy ustawić wartość, które zostały ustawione wcześniej. W tym przykładzie wyniesie **http/sharepoint.demo.o365identity.us**.

   ![Konfigurowanie uwierzytelniania Windows zintegrowanego logowania jednokrotnego](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

3. Aby zakończyć konfigurowanie aplikacji, przejdź do **użytkowników i grup** sekcji, a następnie przypisać użytkownikom na dostęp do tej aplikacji. 

## <a name="step-3-ensure-that-sharepoint-knows-about-the-external-url"></a>Krok 3: Upewnij się, że zna zewnętrzny adres URL programu SharePoint

Ostatni etap jest zapewnienie programu SharePoint można znaleźć witryny oparte na zewnętrzny adres URL, tak aby renderowania łączy na podstawie tego zewnętrznego adresu URL. W tym celu Konfigurowanie alternatywnych mapowań dostępu dla witryny programu SharePoint.

1. Otwórz **administracji centralnej programu SharePoint 2013** lokacji.
2. W obszarze **ustawień systemowych**, wybierz opcję **Konfigurowanie alternatywnych mapowań dostępu**. Zostanie otwarte okno mapowania dostępu alternatywnego.

  ![Alternatywnych mapowań dostępu pole](./media/application-proxy-integrate-with-sharepoint-server/alternate-access1.png)

3. Na liście rozwijanej obok **kolekcji mapowania dostępu alternatywnego**, wybierz opcję **zmiana alternatywny dostęp mapowanie kolekcji**.
4. Wybierz lokację — na przykład **SharePoint — 80**.
5. Można dodać adres URL opublikowany jako wewnętrzny adres URL lub publiczny adres URL. W tym przykładzie użyto publiczny adres URL jako ekstranetu.
6. Kliknij przycisk **Edytuj publiczne adresy URL** w **ekstranet** ścieżki, a następnie wprowadź zewnętrzny adres URL, który został utworzony podczas publikowania aplikacji. Na przykład, wprowadź **https://sharepoint-iddemo.msappproxy.net**.

  ![Wprowadzanie ścieżki](./media/application-proxy-integrate-with-sharepoint-server/alternate-access3.png)

7. Kliknij pozycję **Zapisz**.

Możesz teraz uzyskać dostęp do witryny programu SharePoint zewnętrznie za pośrednictwem serwera Proxy aplikacji usługi Azure AD.

## <a name="next-steps"></a>Kolejne kroki

- [Praca z domenami niestandardowymi na serwerze Proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md)
- [Omówienie łączników serwera Proxy aplikacji usługi Azure AD](application-proxy-connectors.md)

