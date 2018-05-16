---
title: Włączenie dostępu zdalnego do programu SharePoint przy użyciu serwera Proxy aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Zawiera podstawowe informacje dotyczące sposobu integracji lokalnego programu SharePoint server z serwera Proxy aplikacji usługi Azure AD.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 49b5620fb95e9c67b0ff0e314534dc8499714b63
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Włączenie dostępu zdalnego do programu SharePoint przy użyciu serwera Proxy aplikacji usługi Azure AD

W tym artykule omówiono sposób integracji lokalnego programu SharePoint server z serwera Proxy aplikacji usługi Azure Active Directory (Azure AD).

Aby włączyć dostęp zdalny do programu SharePoint przy użyciu serwera Proxy aplikacji usługi Azure AD, wykonaj następujące sekcje w tym artykule krok po kroku.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że już programu SharePoint 2013 lub nowszej w danym środowisku. Ponadto należy wziąć pod uwagę następujące wymagania wstępne:

* SharePoint obejmuje natywnej obsługi protokołu Kerberos. W związku z tym użytkownicy, którzy uzyskują dostęp do wewnętrznych witryn zdalnie za pośrednictwem serwera Proxy aplikacji usługi Azure AD można założyć, poproś go o środowisko rejestracji jednokrotnej (SSO).

* Taki scenariusz obejmuje zmiany konfiguracji serwera programu SharePoint. Firma Microsoft zaleca używanie środowiska przemieszczania. W ten sposób można najpierw aktualizacji z serwerem przemieszczania i następnie ułatwienia testowania cyklu przed przejściem do środowiska produkcyjnego.

* Firma Microsoft wymaga protokołu SSL na opublikowanego adresu URL. Należy skonfigurować protokół SSL w witrynie wewnętrzny włączona do sprawdzenia, czy łącza są wysyłane mapowane poprawnie. Jeśli nie skonfigurowano protokół SSL, zobacz [skonfigurować protokół SSL dla programu SharePoint 2013](https://blogs.msdn.microsoft.com/fabdulwahab/2013/01/20/configure-ssl-for-sharepoint-2013) instrukcje. Ponadto upewnij się, że maszyny łącznika ufa certyfikatowi wystawiane. (Certyfikat nie musi być publicznie wydanych.)

## <a name="step-1-set-up-single-sign-on-to-sharepoint"></a>Krok 1: Konfigurowanie rejestracji jednokrotnej w programie SharePoint

Dla lokalnych aplikacji korzystających z uwierzytelniania systemu Windows można osiągnąć rejestracji jednokrotnej (SSO) z protokołu uwierzytelniania Kerberos i funkcję ograniczonego delegowania protokołu Kerberos (KCD). Delegowanie KCD, po skonfigurowaniu umożliwia łącznika serwera Proxy aplikacji w celu uzyskania tokenu systemu Windows dla użytkownika, nawet wtedy, gdy użytkownik nie zalogował się do systemu Windows bezpośrednio. Aby dowiedzieć się więcej na temat KCD, zobacz [omówienie delegowania ograniczonego protokołu Kerberos](https://technet.microsoft.com/library/jj553400.aspx).

Aby skonfigurować delegowanie KCD programu SharePoint server, korzystając z procedur w kolejnych sekcjach:

### <a name="ensure-that-sharepoint-is-running-under-a-service-account"></a>Upewnij się, że SharePoint działa w ramach konta usługi

Najpierw upewnij się, że SharePoint działa w ramach konta usługi zdefiniowanych — system lokalny, Usługa lokalna lub Usługa sieciowa. W tym dołączenie głównych nazw usług (SPN) prawidłowego konta. Nazwy SPN są, jak protokół Kerberos identyfikuje różnych usług. I musisz mieć konto później, aby skonfigurować w ramach delegowania KCD.

> [!NOTE]
Musisz mieć wcześniej utworzone konto usługi Azure AD dla usługi. Zalecamy, aby umożliwić automatyczne zmienianie hasła. Aby uzyskać więcej informacji na temat pełnego zestawu kroków i rozwiązywanie problemów, zobacz [skonfigurować automatyczne zmienianie hasła w programie SharePoint 2013](https://technet.microsoft.com/library/ff724280.aspx).

Aby upewnić się, że lokacje są uruchomione na koncie usługi zdefiniowane, wykonaj następujące czynności:

1. Otwórz **Administracja centralna programu SharePoint 2013** lokacji.
2. Przejdź do **zabezpieczeń** i wybierz **Konfigurowanie kont usług**.
3. Wybierz **— SharePoint — 80. pula aplikacji sieci Web**. Opcje mogą być nieco inne na podstawie nazwy puli sieci web, lub jeśli domyślnie puli sieci web używa protokołu SSL.

  ![Opcje dotyczące konfigurowania konta usługi](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

4. Jeśli **wybierz konto dla tego składnika** pole jest ustawione na **Usługa lokalna** lub **usługi sieciowej**, musisz utworzyć konto. Jeśli nie, zakończeniu i można przenieść do następnej sekcji.
5. Wybierz **rejestrowanie nowego zarządzanego konta**. Po utworzeniu konta, musisz ustawić **puli aplikacji sieci Web** przed użyciem tego konta.

### <a name="configure-sharepoint-for-kerberos"></a>Konfigurowanie programu SharePoint dla protokołu Kerberos

Delegowanie KCD umożliwia wykonania jednokrotną do serwera programu SharePoint.

Aby skonfigurować uwierzytelnianie Kerberos w witrynie programu SharePoint:

1. Otwórz **Administracja centralna programu SharePoint 2013** lokacji.
2. Przejdź do **Zarządzanie aplikacjami**, wybierz pozycję **zarządzania aplikacjami sieci web**i wybierz pozycję witryny programu SharePoint. W tym przykładzie jest **SharePoint — 80**.

  ![Zaznaczenie witryny programu SharePoint](./media/application-proxy-integrate-with-sharepoint-server/manage-web-applications.png)

3. Kliknij przycisk **dostawców uwierzytelniania** na pasku narzędzi.
4. W **dostawców uwierzytelniania** kliknij **strefa domyślna** Aby wyświetlić ustawienia.
5. W **Edytuj uwierzytelnianie** okno dialogowe pola, przewiń w dół do momentu wyświetlenia **typy uwierzytelniania oświadczeń**. Upewnij się, że oba **włączyć uwierzytelnianie systemu Windows** i **zintegrowane uwierzytelnianie systemu Windows** są wybrane.
6. W polu listy rozwijanej w polu zintegrowane uwierzytelnianie systemu Windows, upewnij się, że **Negotiate (Kerberos)** jest zaznaczone.

  ![Okno dialogowe Edytowanie uwierzytelniania](./media/application-proxy-integrate-with-sharepoint-server/service-edit-authentication.png)

7. W dolnej części **Edytuj uwierzytelnianie** okno dialogowe, kliknij przycisk **zapisać**.

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>Ustaw główną nazwę usługi dla konta usługi programu SharePoint

Aby skonfigurować delegowanie KCD, trzeba będzie zidentyfikować usługi programu SharePoint, do uruchamiania jako konto usługi, który został skonfigurowany. Zidentyfikuj usługę przez ustawienie Nazwa SPN. Aby uzyskać więcej informacji, zobacz [główne nazwy usług](https://technet.microsoft.com/library/cc961723.aspx).

Format nazwy SPN jest:

```
<service class>/<host>:<port>
```

Format nazwy SPN:

* _usługi klasy_ jest unikatową nazwę usługi. Dla programu SharePoint, możesz użyć **HTTP**.

* _host_ jest w pełni kwalifikowanej domeny lub nazwę NetBIOS, czy usługa jest uruchomiona na hoście. Witryny programu SharePoint ten tekst może być konieczne jest adres URL witryny, w zależności od wersji programu IIS, którego używasz.

* _port_ jest opcjonalna.

Jeśli nazwa FQDN serwera programu SharePoint:

```
sharepoint.demo.o365identity.us
```

To jest nazwa SPN:

```
HTTP/sharepoint.demo.o365identity.us demo
```

Może być konieczne również ustawiać nazwy SPN dla określonych witryn na serwerze. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie Kerberos skonfigurować](https://technet.microsoft.com/library/cc263449(v=office.12).aspx). Należy zwrócić szczególną uwagę do sekcji "Tworzenie główne nazwy usług dla aplikacji sieci Web przy użyciu uwierzytelniania Kerberos".

Najprostszym sposobem można ustawić nazwy SPN jest wykonaj formatów nazwy SPN, które mogą być już dostępne dla witryny. Skopiuj te nazwy SPN, aby zarejestrować względem konta usługi. W tym celu:

1. Przejdź do witryny na podstawie nazwy SPN z innego komputera.
 Po wykonaniu czynności odpowiedniego zbioru bilety Kerberos są buforowane na komputerze. Bilety zawierają nazwę SPN w docelowej lokacji, które zostały wybrane.

2. SPN dla tej lokacji można pobierać za pomocą narzędzie [Klist](http://web.mit.edu/kerberos/krb5-devel/doc/user/user_commands/klist.html). W oknie polecenia, które działa w tym samym kontekście jako użytkownik, który uzyskał dostęp do witryny w przeglądarce uruchom następujące polecenie:
```
Klist
```
Klist zwraca zestaw docelowy nazwy SPN. W tym przykładzie wartość zaznaczony jest główną nazwę usługi, które ma potrzebne:

  ![Przykład Klist wyników](./media/application-proxy-integrate-with-sharepoint-server/remote-sharepoint-target-service.png)

4. Teraz, gdy masz główną nazwę usługi, upewnij się, że został on poprawnie skonfigurowany na koncie usługi skonfigurowanego wcześniej aplikacji sieci web. Uruchom następujące polecenie w wierszu polecenia z uprawnieniami administratora domeny:

 ```
 setspn -S http/sharepoint.demo.o365identity.us demo\sp_svc
 ```

 To polecenie ustawia główną nazwę usługi dla konta usługi programu SharePoint, na którym działa jako _demo\sp_svc_.

 Zastąp _http/sharepoint.demo.o365identity.us_ z główną nazwę usługi dla serwera i _demo\sp_svc_ z kontem usługi w danym środowisku. Polecenia Setspn wyszukiwanie nazwy SPN, przed dodaniem go. W takim przypadku można napotkać **zduplikowane wartości nazwy SPN** błędu. Jeśli zostanie wyświetlony ten błąd, upewnij się, że wartość jest skojarzony z kontem usługi.

Aby sprawdzić, czy nazwa SPN został dodany przy użyciu polecenia Setspn z opcją -l. Aby dowiedzieć się więcej na temat tego polecenia, zobacz [Setspn](https://technet.microsoft.com/library/cc731241.aspx).

### <a name="ensure-that-the-connector-is-set-as-a-trusted-delegate-to-sharepoint"></a>Upewnij się, że łącznik jest ustawiony jako zaufany delegata w programie SharePoint

Delegowanie KCD należy skonfigurować, tak aby usługi serwera Proxy aplikacji usługi Azure AD mają możliwość delegowania tożsamości użytkowników do usługi programu SharePoint. Skonfiguruj KCD, włączając łącznik serwera Proxy aplikacji można pobrać biletów Kerberos dla użytkowników, którzy zostali uwierzytelnieni w usłudze Azure AD. Następnie tego serwera w takim przypadku przekazuje kontekst do aplikacji docelowej lub SharePoint.

Aby skonfigurować w ramach delegowania KCD, powtórz następujące czynności dla każdej maszyny łącznika:

1. Zaloguj się jako administrator domeny do kontrolera domeny, a następnie otwórz **użytkownicy usługi Active Directory i komputery**.
2. Znajdź komputera, na którym działa łącznik na. W tym przykładzie jest tym samym serwerze programu SharePoint.
3. Kliknij dwukrotnie komputer, a następnie kliknij przycisk **delegowania** kartę.
4. Upewnij się, że ustawiono ustawienia delegowania **Ufaj temu komputerowi w delegowaniu tylko do określonych usług**. Następnie wybierz opcję **Użyj dowolnego protokołu uwierzytelniania**.

  ![Ustawienia delegowania](./media/application-proxy-integrate-with-sharepoint-server/delegation-box.png)

5. Kliknij przycisk **Dodaj** , kliknij **użytkowników lub komputerów**, a następnie zlokalizuj konto usługi.

  ![Dodawanie nazwy SPN konta usługi](./media/application-proxy-integrate-with-sharepoint-server/users-computers.png)

6. Na liście nazw SPN wybierz jedną, która wcześniej utworzoną dla konta usługi.
7. Kliknij przycisk **OK**. Kliknij przycisk **OK** ponownie, aby zapisać zmiany.

## <a name="step-2-enable-remote-access-to-sharepoint"></a>Krok 2: Włączanie dostępu zdalnego w programie SharePoint

Po włączeniu programu SharePoint dla protokołu Kerberos i KCD skonfigurowany, możesz przystąpić do publikowania farmy programu SharePoint dla dostępu zdalnego za pośrednictwem serwera Proxy aplikacji usługi Azure AD.

1. Publikowanie witryny programu SharePoint z następującymi ustawieniami. Aby uzyskać instrukcje, zobacz [publikowania aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-publish-azure-portal.md). 
   - **Wewnętrzny adres URL**: adres URL witryny programu SharePoint, takich jak **https://SharePoint/**. W tym przykładzie, upewnij się użyć **protokołu https**
   - **Metoda uwierzytelniania wstępnego**: Azure Active Directory
   - **Tłumaczenie adresów URL w nagłówkach**: nie

   >[!TIP]
   >SharePoint używa _nagłówek hosta_ wartość do wyszukania lokacji. Generuje on również linki, na podstawie tej wartości. Net efekt jest każde łącze, które generuje SharePoint opublikowanych adres URL, który jest niepoprawnie skonfigurowany do używania zewnętrznego adresu URL. Ustawienie wartości **tak** umożliwia również łącznik, aby przesłać żądanie do aplikacji zaplecza. Jednakże, ustawiając wartość **nr** oznacza, że łącznik nie będzie wysyłać nazwy wewnętrznej hosta. Zamiast tego łącznika wysyła nagłówek hosta jako adres URL opublikowanej aplikacji zaplecza.

   ![Publikuj jako aplikację programu SharePoint](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

2. Po opublikowaniu aplikacji należy skonfigurować ustawienia znak pojedynczego następujące czynności:

   1. Na stronie aplikacji w portalu, wybierz **logowanie jednokrotne**.
   2. Pojedynczy logowania jednokrotnego, wybierz tryb **zintegrowane uwierzytelnianie systemu Windows**.
   3. Ustaw wewnętrznych aplikacji głównej nazwy usługi do wcześniej skonfigurowane wartości. W tym przykładzie, który będzie **http/sharepoint.demo.o365identity.us**.

   ![Konfigurowanie zintegrowanego uwierzytelniania systemu Windows dla logowania jednokrotnego](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

3. Aby zakończyć konfigurowanie aplikacji, przejdź do **użytkowników i grup** sekcji i przypisać użytkownikom uzyskiwanie dostępu do tej aplikacji. 

## <a name="step-3-ensure-that-sharepoint-knows-about-the-external-url"></a>Krok 3: Upewnij się, że zna zewnętrznego adresu URL programu SharePoint

Ostatni etap jest zapewnienie programu SharePoint można znaleźć lokacji na podstawie zewnętrznego adresu URL, dzięki czemu renderowania łącza na podstawie tego zewnętrznego adresu URL. W tym celu Konfigurowanie mapowań dostępu alternatywnego witryny programu SharePoint.

1. Otwórz **Administracja centralna programu SharePoint 2013** lokacji.
2. W obszarze **ustawienia systemu**, wybierz pozycję **Konfigurowanie mapowań dostępu alternatywnego**. Zostanie otwarte okno mapowań dostępu alternatywnego.

  ![Pole mapowań dostępu alternatywnego](./media/application-proxy-integrate-with-sharepoint-server/alternate-access1.png)

3. Na liście rozwijanej obok **kolekcji mapowania dostępu alternatywnego**, wybierz pozycję **zmiany alternatywnego dostępu mapowania kolekcji**.
4. Wybierz lokację — na przykład **SharePoint — 80**.
5. Można dodać opublikowanego adresu URL jako wewnętrzny adres URL lub publiczny adres URL. W tym przykładzie używa publicznego adresu URL jako ekstranetu.
6. Kliknij przycisk **Edytuj publiczne adresy URL** w **ekstranet** ścieżkę, a następnie wprowadź zewnętrzny adres URL, który został utworzony podczas publikowania aplikacji. Na przykład wprowadź **https://sharepoint-iddemo.msappproxy.net**.

  ![Wprowadzanie ścieżki](./media/application-proxy-integrate-with-sharepoint-server/alternate-access3.png)

7. Kliknij pozycję **Zapisz**.

Można teraz uzyskać dostępu do witryny programu SharePoint zewnętrznie za pośrednictwem serwera Proxy aplikacji usługi Azure AD.

## <a name="next-steps"></a>Kolejne kroki

- [Praca z domenami niestandardowymi w serwera Proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md)
- [Zrozumienie łączniki serwera Proxy aplikacji usługi Azure AD](application-proxy-connectors.md)

