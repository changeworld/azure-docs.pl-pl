---
title: Samouczek — integracja istniejącego lasu i nowego lasu z pojedynczą dzierżawą usługi Azure AD przy użyciu inicjowania obsługi administracyjnej w chmurze usługi Azure AD Connect.
description: Samouczek.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa612ad30ae0faa42071613be15c1d91fb96b8f6
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80332264"
---
# <a name="integrate-an-existing-forest-and-a-new-forest-with-a-single-azure-ad-tenant"></a>Integrowanie istniejącego i nowego lasu z jedną dzierżawą usługi Azure AD

W tym samouczku można zainicjować dodawanie inicjowania obsługi administracyjnej w chmurze do istniejącego środowiska tożsamości hybrydowej. 

![Utwórz](media/tutorial-existing-forest/existing-forest-new-forest.png)

Można użyć środowiska utworzonego w tym samouczku do testowania lub do zapoznania się z jak działa tożsamości hybrydowej. 

W tym scenariuszu istnieje istniejący las zsynchronizowany przy użyciu synchronizacji usługi Azure AD Connect z dzierżawą usługi Azure AD. I masz nowy las, który chcesz zsynchronizować z tej samej dzierżawy usługi Azure AD. Zostanie skonfigurowana inicjowanie obsługi administracyjnej w chmurze dla nowego lasu. 

## <a name="prerequisites"></a>Wymagania wstępne
### <a name="in-the-azure-active-directory-admin-center"></a>W centrum administracyjnym usługi Azure Active Directory

1. Utwórz konto administratora globalnego tylko w chmurze w dzierżawie usługi Azure AD. W ten sposób można zarządzać konfiguracją dzierżawy, jeśli usługi lokalne nie powiodą się lub staną się niedostępne. Dowiedz się więcej o [dodawaniu globalnego konta administratora tylko w chmurze](../active-directory-users-create-azure-portal.md). Wykonanie tego kroku ma kluczowe znaczenie dla zapewnienia, że nie zostaniesz zablokowany z dzierżawy.
2. Dodaj co najmniej jedną [niestandardową nazwę domeny](../active-directory-domains-add-azure-portal.md) do dzierżawy usługi Azure AD. Użytkownicy mogą logować się przy jednej z tych nazw domen.

### <a name="in-your-on-premises-environment"></a>W środowisku lokalnym

1. Tożsamość serwera hosta przyłączonego do domeny z systemem Windows Server 2012 R2 lub nowszym z minimalną pamięcią RAM 4 GB i środowiskiem uruchomieniowym .NET 4.7.1+ 

2. Jeśli między serwerami a usługą Azure AD znajduje się zapora, skonfiguruj następujące elementy:
   - Upewnij się, że agenci mogą składać żądania *wychodzące* do usługi Azure AD za pomocą następujących portów:

     | Numer portu | Zastosowanie |
     | --- | --- |
     | **80** | Pobiera listy odwołania certyfikatów (CRL) podczas sprawdzania poprawności certyfikatu TLS/SSL |
     | **443** | Obsługuje całą komunikację wychodzącą z usługą |
     | **8080** (opcjonalnie) | Agenci zgłaszają swój stan co 10 minut za portem 8080, jeśli port 443 jest niedostępny. Ten stan jest wyświetlany w portalu usługi Azure AD. |
     
     Jeśli zapora wymusza reguły zgodnie z użytkownikami źródłowymi, otwórz te porty dla ruchu z usług systemu Windows, które działają jako usługa sieciowa.
   - Jeśli zapora lub serwer proxy umożliwia określenie bezpiecznych sufiksów, należy dodać połączenia do ** \*msappproxy.net** i ** \*.servicebus.windows.net**. Jeśli nie, zezwalaj na dostęp do [zakresów adresów IP centrum danych platformy Azure,](https://www.microsoft.com/download/details.aspx?id=41653)które są aktualizowane co tydzień.
   - Twoi agenci potrzebują dostępu do **login.windows.net** i **login.microsoftonline.com** do wstępnej rejestracji. Otwórz również zaporę dla tych adresów URL.
   - Aby potwierdzić certyfikat, odblokuj następujące adresy URL: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80**i **www\.microsoft.com:80**. Ponieważ te adresy URL są używane do sprawdzania poprawności certyfikatów w innych produktach firmy Microsoft, te adresy URL mogą być już odblokowane.

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Instalowanie agenta inicjowania obsługi administracyjnej usługi Azure AD Connect
1. Zaloguj się do serwera przyłączone do domeny.  Jeśli używasz [samouczka środowiska podstawowej usługi AD i platformy Azure,](tutorial-basic-ad-azure.md) będzie to DC1.
2. Zaloguj się do witryny Azure portal przy użyciu poświadczeń administratora globalnego tylko w chmurze.
3. Po lewej stronie wybierz pozycję **Azure Active Directory**, kliknij pozycję **Azure AD Connect** i w centrum wybierz pozycję **Zarządzaj inicjowania obsługi administracyjnej (wersja zapoznawcza)**.</br>
![Azure Portal](media/how-to-install/install6.png)</br>
4. Kliknij na "Agent pobierania"
5. Uruchamianie agenta inicjowania obsługi administracyjnej usługi Azure AD Connect
6. Na ekranie powityla **zaakceptuj** postanowienia licencyjne i kliknij przycisk **Zainstaluj**.</br>
![Ekran powitalny](media/how-to-install/install1.png)</br>

7. Po zakończeniu tej operacji zostanie uruchomiony kreator konfiguracji.  Zaloguj się za pomocą konta administratora globalnego usługi Azure AD.  Należy pamiętać, że jeśli masz włączone rozszerzone zabezpieczenia IE, spowoduje to zablokowanie logowania.  W takim przypadku zamknij instalację, wyłącz rozszerzone zabezpieczenia IE w Menedżerze serwera i kliknij **Kreatora aprowizowania połączenia AAD,** aby ponownie uruchomić instalację.
8. Na ekranie **Połącz usługa Active Directory** kliknij pozycję **Dodaj katalog,** a następnie zaloguj się za pomocą konta administratora domeny usługi Active Directory.  UWAGA: Konto administratora domeny nie powinno mieć wymagań dotyczących zmiany hasła. W przypadku wygaśnięcia lub zmiany hasła należy ponownie skonfigurować agenta przy użyciu nowych poświadczeń. Ta operacja spowoduje dodanie katalogu lokalnego.  Kliknij przycisk **alej**.</br>
![Ekran powitalny](media/how-to-install/install3.png)</br>

9. Na ekranie **Konfiguracja pełna** kliknij przycisk **Potwierdź**.  Ta operacja zarejestruje i uruchomi ponownie agenta.</br>
![Ekran powitalny](media/how-to-install/install4.png)</br>

10. Po zakończeniu tej operacji powinien zostać wyświetlony komunikat: **Konfiguracja agenta została pomyślnie zweryfikowana.**  Możesz kliknąć przycisk **Zakończ**.</br>
![Ekran powitalny](media/how-to-install/install5.png)</br>
11. Jeśli nadal widzisz początkowy ekran powitalny, kliknij przycisk **Zamknij**.


## <a name="verify-agent-installation"></a>Weryfikowanie instalacji agenta
Weryfikacja agenta odbywa się w witrynie Azure portal i na serwerze lokalnym, który jest uruchomiony agenta.

### <a name="azure-portal-agent-verification"></a>Weryfikacja agenta usługi Azure portal
Aby sprawdzić, czy agent jest postrzegany przez platformę Azure, wykonaj następujące kroki:

1. Zaloguj się do Portalu Azure.
2. Po lewej stronie wybierz pozycję **Azure Active Directory**, kliknij pozycję **Azure AD Connect** i w centrum wybierz pozycję **Zarządzaj inicjowania obsługi administracyjnej (wersja zapoznawcza)**.</br>
![Azure Portal](media/how-to-install/install6.png)</br>

3.  Na ekranie **inicjowania obsługi administracyjnej usługi Azure AD (wersja zapoznawcza)** kliknij pozycję **Przejrzyj wszystkich agentów**.
![Inicjowanie obsługi administracyjnej usługi Azure AD](media/how-to-install/install7.png)</br>
 
4. Na **ekranie lokalnych agentów inicjowania obsługi administracyjnej** zobaczysz zainstalowane agentów.  Sprawdź, czy dany agent istnieje i jest oznaczony **jako aktywny**.
![Agenci inicjowania obsługi administracyjnej](media/how-to-install/verify1.png)</br>

### <a name="on-the-local-server"></a>Na serwerze lokalnym
Aby sprawdzić, czy agent jest uruchomiony, wykonaj następujące kroki:

1.  Zaloguj się do serwera przy użyciu konta administratora
2.  Otwórz **usługi,** przechodząc do niego lub przechodząc do strony Start/Run/Services.msc.
3.  W obszarze **Usługi**upewnij się, że program **Microsoft Azure AD Connect Agent Updater** i agent **aprowizowania usługi Microsoft Azure AD Connect** są obecne, a stan jest **uruchomiony**.
![Usługi](media/how-to-troubleshoot/troubleshoot1.png)

## <a name="configure-azure-ad-connect-cloud-provisioning"></a>Konfigurowanie inicjowania obsługi administracyjnej usługi Azure AD Connect
 Konfigurowanie inicjowania obsługi administracyjnej za pomocą następujących czynności:

1.  Zaloguj się do portalu usługi Azure AD.
2.  Kliknij pozycję **Usługa Azure Active Directory**
3.  Kliknij pozycję **Usługa Azure AD Connect**
4.  Wybierz **pozycję Zarządzaj inicjowania obsługi administracyjnej (wersja zapoznawcza)**
![](media/how-to-configure/manage1.png)
5.  Kliknij **pozycję Nowa konfiguracja**
![](media/tutorial-single-forest/configure1.png)
7.  Na ekranie konfiguracji wprowadź **wiadomość e-mail z powiadomieniem**, przesuń selektor do **opcji Włącz** i kliknij przycisk **Zapisz**.
![](media/tutorial-single-forest/configure2.png)
1.  Stan konfiguracji powinien być teraz **zdrowy**.
![](media/how-to-configure/manage4.png)

## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Weryfikowanie utworzenia użytkowników i przeprowadzania synchronizacji
Teraz sprawdzisz, czy użytkownicy, którzy mieli w naszym katalogu lokalnym zostały zsynchronizowane i teraz istnieją w naszej dzierżawy usługi Azure AD.  Ukończenie tego procesu może potrwać kilka godzin.  Aby potwierdzić, że użytkownicy zostali zsynchronizowani, wykonaj następujące czynności.


1. Przejdź do witryny [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta z subskrypcją platformy Azure.
2. W obszarze po lewej stronie wybierz pozycję **Azure Active Directory**.
3. W obszarze **Zarządzaj** wybierz pozycję **Użytkownicy**.
4. Zweryfikuj, czy nowi użytkownicy są widoczni w dzierżawie</br>
![Synchronizacja](media/tutorial-single-forest/synchronize1.png)</br>

## <a name="test-signing-in-with-one-of-our-users"></a>Testowanie logowania się przy użyciu jednego z kont użytkowników

1. Przejdź do[https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Zaloguj się przy użyciu konta użytkownika utworzonego w nowej dzierżawie.  Należy zalogować się przy użyciu następującego formatu: (user@domain.onmicrosoft.com). Użyj tego samego hasła, za pomocą którego użytkownik loguje się lokalnie.</br>
   ![Weryfikacja](media/tutorial-single-forest/verify1.png)</br>

Teraz pomyślnie skonfigurować środowisko tożsamości hybrydowej, które można użyć do testowania i zapoznania się z tym, co ma do zaoferowania platforma Azure.

## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest aprowizacja w chmurze programu Azure AD Connect?](what-is-cloud-provisioning.md)
