---
title: Uwierzytelnianie oparte na nagłówka z usługą PingAccess dla serwera Proxy aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Publikowanie aplikacji z usługą PingAccess i serwera Proxy aplikacji obsługują uwierzytelnianie oparte na nagłówka.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/11/2017
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecba35a54c530e97a3f96bd8cfb5fa217d300f83
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65783572"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Na podstawie nagłówka uwierzytelniania dla logowania jednokrotnego przy użyciu serwera Proxy aplikacji i PingAccess

Serwer Proxy aplikacji Active Directory i PingAccess Azure wspólnie opracowały razem klientów usługi Azure Active Directory z dostępem do jeszcze większej liczby aplikacji. Rozwija PingAccess [istniejące oferty serwera Proxy aplikacji](application-proxy.md) obejmujący dostęp pojedynczego logowania jednokrotnego do aplikacji, które używające nagłówków na potrzeby uwierzytelniania.

## <a name="what-is-pingaccess-for-azure-ad"></a>Co to jest oprogramowanie PingAccess dla usługi Azure AD?

Usługa PingAccess dla usługi Azure Active Directory to oferta pingaccess umożliwiająca można udzielić użytkownikom dostępu i logowania jednokrotnego do aplikacji, które używające nagłówków na potrzeby uwierzytelniania. Serwer Proxy aplikacji traktuje te aplikacje, takie jak każdy inny, za pomocą usługi Azure AD do uwierzytelniania dostępu i następnie przekazywanie ruchu za pośrednictwem usługi łącznika. Usługa PingAccess znajduje się przed aplikacje i tłumaczy token dostępu z usługi Azure AD na nagłówek, aby aplikacja otrzymuje uwierzytelniania w formacie, który może odczytać.

Użytkownicy nie będą zauważyć nic innego po zalogowaniu się do użycia w aplikacjach firmowych. One nadal pracować z dowolnego miejsca na dowolnym urządzeniu. 

Ponieważ łączników serwera Proxy aplikacji bezpośrednie zdalny ruch do wszystkich aplikacji, niezależnie od ich typu uwierzytelniania, będą one nadal równoważenia obciążenia, automatycznego, jak również.

## <a name="how-do-i-get-access"></a>Jak uzyskać dostęp?

Ponieważ ten scenariusz jest oferowana za pośrednictwem usługi Azure Active Directory i PingAccess, potrzebujesz licencji dla obu usług. Jednak subskrypcje usługi Azure Active Directory — wersja Premium obejmują licencję PingAccess podstawowe, który obejmuje maksymalnie 20 aplikacji. Jeśli trzeba opublikować więcej niż 20 aplikacji opartej na nagłówkach, możesz kupić dodatkową licencję z usługą PingAccess. 

Aby uzyskać więcej informacji, zobacz [Wersje usługi Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Publikuj swoje aplikacje na platformie Azure

Ten artykuł jest przeznaczony dla osób, które publikujesz aplikację, w tym scenariuszu po raz pierwszy. Przedstawiono sposób rozpoczęcia pracy z aplikacji i PingAccess, oprócz kroki publikowania. Jeśli skonfigurowano już obie te usługi, ale przypomnienia informacji na temat publikowania kroki, możesz pominąć instalacja łącznika i przejdź do [Dodaj swoją aplikację do usługi Azure AD przy użyciu serwera Proxy aplikacji](#add-your-app-to-azure-ad-with-application-proxy).

>[!NOTE]
>Ponieważ ten scenariusz jest partnerstwa między usługą Azure AD i PingAccess, istnieją niektóre instrukcje w witrynie Ping Identity.

### <a name="install-an-application-proxy-connector"></a>Zainstaluj łącznik serwera Proxy aplikacji

Jeśli już masz włączony serwer Proxy aplikacji i mieć zainstalowanego łącznika, możesz pominąć tę sekcję i przejdź do [Dodaj swoją aplikację do usługi Azure AD przy użyciu serwera Proxy aplikacji](#add-your-app-to-azure-ad-with-application-proxy).

Łącznik serwera Proxy aplikacji jest usługę systemu Windows Server, która kieruje ruch z pracownicy zdalni do opublikowanej aplikacji. Aby uzyskać szczegółowe instrukcje dotyczące instalacji, zobacz [Włączanie serwera Proxy aplikacji w witrynie Azure portal](application-proxy-add-on-premises-application.md).

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator aplikacji.
2. Wybierz **usługi Azure Active Directory** > **serwera proxy aplikacji**.
3. Wybierz **Pobierz łącznik** aby rozpocząć pobieranie łącznika serwera Proxy aplikacji. Postępuj zgodnie z instrukcjami instalacji.

   ![Włącz serwer Proxy aplikacji i Pobierz łącznik](./media/application-proxy-configure-single-sign-on-with-ping-access/install-connector.png)

4. Pobierając łącznik automatycznie należy włączyć serwer Proxy aplikacji dla katalogu, ale jeśli nie możesz wybrać **Włączanie serwera Proxy aplikacji**.


### <a name="add-your-app-to-azure-ad-with-application-proxy"></a>Dodaj swoją aplikację do usługi Azure AD przy użyciu serwera Proxy aplikacji

Istnieją dwie akcje, które należy wykonać w witrynie Azure portal. Najpierw musisz opublikować aplikację przy użyciu serwera Proxy aplikacji. Następnie należy zebrać pewne informacje o aplikacji, można użyć podczas czynności PingAccess.

Wykonaj następujące kroki, aby opublikować aplikację. Bardziej szczegółowy przewodnik dotyczący kroki 1-8, zobacz [publikowania aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md).

1. Jeśli nie jest w ostatniej sekcji, zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator aplikacji.
2. Wybierz **usługi Azure Active Directory** > **aplikacje dla przedsiębiorstw**.
3. Wybierz **Dodaj** w górnej części bloku.
4. Wybierz **aplikacje lokalne**.
5. Wypełnij wymagane pola informacji o nowej aplikacji. Użyj poniższych wskazówek, aby ustawienia:
   - **Wewnętrzny adres URL**: Zwykle należy podać adres URL, który umożliwia przejście do strony logowania aplikacji podczas korzystania z sieci firmowej. W tym scenariuszu łącznika musi traktować PingAccess proxy jako pierwsza strona aplikacji. Użyj tego formatu: `https://<host name of your PA server>:<port>`. Numer portu to 3000 domyślnie, ale można go skonfigurować w PingAccess.

     > [!WARNING]
     > Dla tego typu logowania jednokrotnego wewnętrzny adres URL musi używać protokołu https i nie może korzystać z protokołu http.

   - **Metoda wstępnego uwierzytelnienia**: Usługa Azure Active Directory
   - **Przetłumacz URL w nagłówkach**: Nie

   >[!NOTE]
   >Jeśli jest to swoją pierwszą aplikację, należy użyć portu 3000 do uruchamiania i wróć, aby zaktualizować to ustawienie, jeśli zmienisz konfigurację PingAccess. Jeśli jest to drugi lub nowszych aplikacji, będzie to muszą być zgodne odbiornika skonfigurowaniu PingAccess. Dowiedz się więcej o [słuchaczy w PingAccess](https://documentation.pingidentity.com/pingaccess/pa31/index.shtml#Listeners.html).

6. Wybierz **Dodaj** w dolnej części bloku. Aplikacja zostanie dodany, a zostanie otwarte menu skrócone.
7. Wybierz z menu skrócone **przypisać użytkownika do testowania**, i Dodaj co najmniej jednego użytkownika do aplikacji. Upewnij się, że to konto testu ma dostęp do aplikacji w środowisku lokalnym.
8. Wybierz **przypisać** można zapisać przypisania użytkownika testowego.
9. W bloku zarządzania aplikacji wybierz **logowanie jednokrotne**.
10. Wybierz **logowanie na podstawie nagłówka** z menu rozwijanego. Wybierz pozycję **Zapisz**.

    >[!TIP]
    >Jeśli po raz pierwszy przy użyciu opartej na nagłówkach logowania jednokrotnego, musisz zainstalować oprogramowanie PingAccess. Aby upewnić się, że Twoja subskrypcja platformy Azure jest automatycznie kojarzony z instalacji oprogramowanie PingAccess, użyj łącza w tym — strona logowanie jednokrotne można pobrać oprogramowanie PingAccess. Możesz teraz otworzyć witryny pobierania lub późniejszy powrót do tej strony. 

    ![Wybierz pozycję logowanie na podstawie nagłówka](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.PNG)

11. Zamknij blok aplikacje przedsiębiorstwa lub przewiń do lewej strony, aby wrócić do menu usługi Azure Active Directory.
12. Wybierz pozycję **Rejestracje aplikacji**.

    ![Wybieranie rejestracji aplikacji](./media/application-proxy-configure-single-sign-on-with-ping-access/app-registrations.png)

13. Wybierz aplikację, właśnie został dodany, następnie **adresy URL odpowiedzi**.

    ![Wybierz adresy URL odpowiedzi](./media/application-proxy-configure-single-sign-on-with-ping-access/reply-urls.png)

14. Sprawdź, czy zewnętrzny adres URL, który jest przypisany do aplikacji w taki sposób, w kroku 5 jest na liście adresów URL odpowiedzi. Jeśli nie, dodaj je teraz.
15. W bloku ustawień aplikacji, wybierz **wymagane uprawnienia**.

    ![Wybierz wymagane uprawnienia](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

16. Wybierz pozycję **Dodaj**. W przypadku interfejsu API, wybierz **Windows Azure Active Directory**, następnie **wybierz**. Uprawnienia, wybierz **odczytu i zapisuj wszystkie aplikacje** i **Zaloguj się i odczytuj profil użytkownika**, następnie **wybierz** i **gotowe**.  

    ![Wybierz uprawnienia](./media/application-proxy-configure-single-sign-on-with-ping-access/select-permissions.png)

17. Przyznaj uprawnienia, zanim zamkniesz ekran uprawnień. 
    ![Udziel uprawnień](./media/application-proxy-configure-single-sign-on-with-ping-access/grantperms.png)

### <a name="collect-information-for-the-pingaccess-steps"></a>Gromadzenie informacji o krokach PingAccess

1. W bloku ustawień aplikacji, wybierz **właściwości**. 

   ![Wybieranie właściwości](./media/application-proxy-configure-single-sign-on-with-ping-access/properties.png)

2. Zapisz **identyfikator aplikacji** wartość. Służy to do Identyfikatora klienta po skonfigurowaniu PingAccess.
3. W bloku ustawień aplikacji, wybierz **klucze**.

   ![Wybierz klucze](./media/application-proxy-configure-single-sign-on-with-ping-access/Keys.png)

4. Utwórz klucz, wprowadzając opis klucza i wybierając datę wygaśnięcia z menu rozwijanego.
5. Wybierz pozycję **Zapisz**. Identyfikator GUID jest wyświetlany w **wartość** pola.

   Zmniejsz tę wartość, nie będzie można zobaczyć, jak to ponownie po zamknięciu tego okna.

   ![Utwórz nowy klucz](./media/application-proxy-configure-single-sign-on-with-ping-access/create-keys.png)

6. Zamknij bloku rejestracje aplikacji lub przewiń do lewej strony, aby wrócić do menu usługi Azure Active Directory.
7. Wybierz **właściwości**.
8. Zapisz **identyfikator katalogu** identyfikatora GUID.

### <a name="optional---update-graphapi-to-send-custom-fields"></a>Opcjonalne - GraphAPI aktualizacji do wysyłania niestandardowych pól

Aby uzyskać listę tokeny zabezpieczające, które wysyła usługi Azure AD w celu uwierzytelniania, zobacz [odwołania do tokenu usługi Azure AD](../develop/v1-id-and-access-tokens.md). Oświadczenia niestandardowego wysyłający innych tokenów, należy użyć Graph Explorer lub manifest dla aplikacji w witrynie Azure Portal Aby ustawić pole aplikacji *acceptMappedClaims* do **True**.    

W tym przykładzie użyto Graph Explorer:

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application> 

{
  "acceptMappedClaims":true
}
```
W tym przykładzie użyto [witryny Azure portal](https://portal.azure.com) można zaktualizować *acceptedMappedClaims* pola:
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator aplikacji.
2. Wybierz **usługi Azure Active Directory** > **rejestracje aplikacji**.
3. Wybierz swoją aplikację > **manifestu**.
4. Wybierz **Edytuj**, wyszukaj *acceptedMappedClaims* pola, a następnie zmień wartość na **true**.
![Manifest aplikacji](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-ping-access-manifest.PNG)
1. Wybierz pozycję **Zapisz**.

>[!NOTE]
>Aby użyć oświadczenia niestandardowego, musi również mieć zasad niestandardowych, zdefiniowanych i przypisane do aplikacji.  Ta zasada powinna zawierać wszystkie wymagane atrybutów niestandardowych.
>
>Definicja zasad i przypisania może odbywać się przy użyciu programu PowerShell, usługi Azure AD Graph Explorer lub MS Graph.  Jeśli robisz to w programie PowerShell, należy najpierw użyć `New-AzureADPolicy` i przypisz je do aplikacji przy użyciu `Set-AzureADServicePrincipalPolicy`.  Aby uzyskać więcej informacji, zobacz [dokumentacji usługi Azure AD Policy](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

### <a name="optional---use-a-custom-claim"></a>Opcjonalne — Użyj oświadczenia niestandardowego
Aby aplikacja oświadczenia niestandardowego i zawiera dodatkowe pola, upewnij się, czy masz również [utworzone oświadczenia niestandardowe mapowanie zasad i przypisać je do aplikacji](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

## <a name="download-pingaccess-and-configure-your-app"></a>Pobierz oprogramowanie PingAccess i konfigurowanie aplikacji

Teraz, że ukończono wszystkie kroki konfiguracji usługi Azure Active Directory, możesz przejść do konfigurowania PingAccess. 

Szczegółowe kroki PingAccess częścią tego scenariusza jest nadal w dokumentacji Ping Identity [skonfigurować oprogramowanie PingAccess dla usługi Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html).

Te kroki opisują proces pobierania konta PingAccess, jeśli nie masz jeszcze jeden, instalowany jest serwer PingAccess i tworzenie połączenia dostawcy usługi Azure AD OIDC za pomocą Identyfikatora katalogu, który został skopiowany z witryny Azure portal. Następnie przy użyciu wartości klucza i identyfikator aplikacji, aby utworzyć sesję sieci Web na oprogramowanie PingAccess. Po tym można Konfigurowanie mapowania tożsamości i utworzyć hostów wirtualnych, witryn i aplikacji.

### <a name="test-your-app"></a>Testowanie aplikacji

Po zakończeniu wszystkie te kroki aplikacji powinna być uruchomiona. Aby przetestować go, otwórz przeglądarkę i przejdź do zewnętrznego adresu URL, który został utworzony, gdy aplikacja została opublikowana na platformie Azure. Zaloguj się przy użyciu konta testowego, który jest przypisany do aplikacji.

## <a name="next-steps"></a>Kolejne kroki

- [Skonfigurować oprogramowanie PingAccess dla usługi Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)
- [W jaki sposób serwer Proxy aplikacji usługi Azure AD zapewnia logowanie jednokrotne](application-proxy-single-sign-on.md)
- [Rozwiązywanie problemów z serwera Proxy aplikacji](application-proxy-troubleshoot.md)
