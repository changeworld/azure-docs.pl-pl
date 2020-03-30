---
title: Dodawanie Google jako dostawcy tożsamości dla B2B — Azure AD
description: Skonserwajowanie z Google, aby umożliwić użytkownikom-gościom logowanie się do aplikacji usługi Azure AD za pomocą własnego konta Gmail
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72c18e48c27942c7bea47931ec79a31af941064e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126663"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Dodawanie Google jako dostawcy tożsamości dla użytkowników-gości B2B

Konfiguruj federację w Google, możesz zezwolić zaproszonym użytkownikom na logowanie się do udostępnionych aplikacji i zasobów za pomocą własnych kont Gmail bez konieczności tworzenia kont Microsoft (MSA). 

> [!NOTE]
> Federacja Google została zaprojektowana specjalnie dla użytkowników Gmaila. Aby łączyć się z domenami G Suite, użyj [funkcji bezpośredniej federacji.](direct-federation.md)

## <a name="what-is-the-experience-for-the-google-user"></a>Jakie jest doświadczenie użytkownika Google?
Gdy wysyłasz zaproszenie do użytkownika Google Gmail, użytkownik-gość powinien uzyskać dostęp do udostępnionych aplikacji lub zasobów za pomocą łącza zawierającego kontekst dzierżawy. Ich doświadczenie różni się w zależności od tego, czy są już zalogowane w Google:
  - Jeśli użytkownik-gość nie jest zalogowany w Google, zostanie wyświetlony monit o zalogowanie się do Google.
  - Jeśli użytkownik-gość jest już zalogowany w Google, zostanie wyświetlony monit o wybranie konta, którego chce użyć. Muszą wybrać konto, którego użyłeś do ich zaproszenia.

Jeśli użytkownik-gość widzi błąd "nagłówek za długo", może spróbować wyczyścić swoje pliki cookie lub otworzyć prywatne lub incognito okno i spróbować zalogować się ponownie.

![Zrzut ekranu przedstawiający stronę logowania google](media/google-federation/google-sign-in.png)

## <a name="limitations"></a>Ograniczenia

Zespoły w pełni obsługują użytkowników gości Google na wszystkich urządzeniach. Użytkownicy Google mogą logować się do `https://teams.microsoft.com`zespołów z wspólnego punktu końcowego, takiego jak .

Typowe punkty końcowe innych aplikacji mogą nie obsługiwać użytkowników Google. Użytkownicy-goście Google muszą zalogować się za pomocą linku zawierającego informacje o dzierżawie. Poniżej przedstawiono przykłady:
  * `https://myapps.microsoft.com/?tenantid=<your tenant id>`
  * `https://portal.azure.com/<your tenant id>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   Jeśli użytkownicy gości Google spróbują `https://myapps.microsoft.com` `https://portal.azure.com`użyć linku, takiego jak lub , otrzymają błąd.

Możesz również nadać użytkownikom gościOm Google bezpośredni link do aplikacji lub zasobu, `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`o ile ten link zawiera informacje o dzierżawie, na przykład. 

## <a name="step-1-configure-a-google-developer-project"></a>Krok 1: Konfigurowanie projektu programisty Google
Najpierw utwórz nowy projekt w Konsoli Google Developers, aby uzyskać identyfikator klienta i klucz tajny klienta, który można później dodać do usługi Azure AD. 
1. Przejdź do interfejsów API https://console.developers.google.comGoogle pod adresem , i zaloguj się za pomocą konta Google. Zalecamy korzystanie z udostępnionego konta Google zespołu.
2. Utwórz nowy projekt: na pulpicie nawigacyjnym wybierz pozycję **Utwórz projekt**, a następnie wybierz pozycję **Utwórz**. Na stronie Nowy projekt wprowadź **nazwę projektu,** a następnie wybierz pozycję **Utwórz**.
   
   ![Zrzut ekranu przedstawiający stronę Nowego projektu dla Google](media/google-federation/google-new-project.png)

3. Upewnij się, że nowy projekt jest zaznaczony w menu projektu. Następnie w obszarze **interfejsy API & Services**wybierz **ekran zgody OAuth**.

4. Wybierz **pozycję Zewnętrzne**, a następnie wybierz pozycję **Utwórz**. 
5. Na **ekranie zgody OAuth**wprowadź **nazwę aplikacji**. (Pozostaw inne ustawienia).

   ![Zrzut ekranu przedstawiający opcję ekranu zgody Google OAuth](media/google-federation/google-oauth-consent-screen.png)

6. Przewiń do sekcji **Autoryzowane domeny** i wprowadź microsoftonline.com.

   ![Zrzut ekranu przedstawiający sekcję Autoryzowane domeny](media/google-federation/google-oauth-authorized-domains.png)

7. Wybierz **pozycję Zapisz**.

8. Wybierz **pozycję Poświadczenia**. W menu **Tworzenie poświadczeń** wybierz polecenie **Identyfikator klienta OAuth**.

   ![Zrzut ekranu przedstawiający opcję tworzenia poświadczeń przez interfejsy API Google](media/google-federation/google-api-credentials.png)

9. W obszarze **Typ aplikacji**wybierz pozycję Aplikacja **sieci Web**, a następnie w obszarze **Autoryzowane identyfikatory URI przekierowania**wprowadź następujące identyfikatory URI:
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>(gdzie `<directory id>` jest twój identyfikator katalogu)
   
     > [!NOTE]
     > Aby znaleźć identyfikator katalogu, przejdź https://portal.azure.comdo programu **Azure Active Directory**i w obszarze Azure Active Directory wybierz pozycję **Właściwości** i skopiuj **identyfikator katalogu**.

   ![Zrzut ekranu przedstawiający sekcję URI autoryzowanego przekierowania](media/google-federation/google-create-oauth-client-id.png)

10. Wybierz **pozycję Utwórz**. Skopiuj identyfikator klienta i klucz tajny klienta, który będzie używany podczas dodawania dostawcy tożsamości w portalu usługi Azure AD.

   ![Zrzut ekranu przedstawiający identyfikator klienta OAuth i klucz tajny klienta](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Krok 2: Konfigurowanie federacji Google w usłudze Azure AD 
Teraz ustawisz identyfikator klienta Google i klucz tajny klienta, wprowadzając go w portalu usługi Azure AD lub za pomocą programu PowerShell. Sprawdź konfigurację federacji Google, zapraszając się przy użyciu adresu Gmail i próbując zrealizować zaproszenie za pomocą zaproszonego konta Google. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>Aby skonfigurować federację Google w portalu usługi Azure AD 
1. Przejdź do [witryny Azure portal](https://portal.azure.com). W lewym okienku wybierz pozycję **Azure Active Directory**. 
2. Wybierz **pozycję Relacje organizacyjne**.
3. Wybierz **pozycję Dostawcy tożsamości**, a następnie kliknij przycisk **Google.**
4. Wprowadź nazwę. Następnie wprowadź identyfikator klienta i klucz tajny klienta, który uzyskałeś wcześniej. Wybierz **pozycję Zapisz**. 

   ![Zrzut ekranu przedstawiający stronę Dodaj dostawcę tożsamości Google](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>Aby skonfigurować federację Google przy użyciu programu PowerShell
1. Zainstaluj najnowszą wersję modułu programu Azure AD PowerShell for Graph[(AzureADPreview).](https://www.powershellgallery.com/packages/AzureADPreview)
2. Uruchom następujące polecenie: `Connect-AzureAD`.
3. W wierszu logowania zaloguj się za pomocą zarządzanego konta administratora globalnego.  
4. Uruchom następujące polecenie: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Użyj identyfikatora klienta i klucza tajnego klienta z aplikacji utworzonej w "Kroku 1: Konfigurowanie projektu programisty Google". Aby uzyskać więcej informacji, zobacz [new-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) artykułu. 
 
## <a name="how-do-i-remove-google-federation"></a>Jak usunąć federację Google?
Możesz usunąć konfigurację federacji Google. Jeśli to zrobisz, użytkownicy-goście Google, którzy już zrealizowali zaproszenie, nie będą mogli się zalogować, ale możesz ponownie udzielić im dostępu do zasobów, usuwając je z katalogu i ponownie zapraszając. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>Aby usunąć federację Google w portalu usługi Azure AD: 
1. Przejdź do [witryny Azure portal](https://portal.azure.com). W lewym okienku wybierz pozycję **Azure Active Directory**. 
2. Wybierz **pozycję Relacje organizacyjne**.
3. Wybierz **dostawców tożsamości**.
4. W wierszu **Google** wybierz menu kontekstowe (**...**), a następnie wybierz pozycję **Usuń**. 
   
   ![Zrzut ekranu przedstawiający opcję Usuń dla dostawcy tożsamości społecznościowych](media/google-federation/google-social-identity-providers.png)

1. Wybierz **pozycję Tak,** aby potwierdzić usunięcie. 

### <a name="to-delete-google-federation-by-using-powershell"></a>Aby usunąć federację Google przy użyciu programu PowerShell: 
1. Zainstaluj najnowszą wersję modułu programu Azure AD PowerShell for Graph[(AzureADPreview).](https://www.powershellgallery.com/packages/AzureADPreview)
2. Uruchom polecenie `Connect-AzureAD`.  
4. W wierszu logowania zaloguj się za pomocą zarządzanego konta administratora globalnego.  
5. Wprowadź następujące polecenie:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Aby uzyskać więcej informacji, zobacz [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview). 
