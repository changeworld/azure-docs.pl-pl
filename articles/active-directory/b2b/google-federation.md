---
title: Dodaj firmę Google jako dostawcę tożsamości dla B2B-Azure Active Directory | Microsoft Docs
description: Sfederować z firmą Google, aby umożliwić użytkownikom-Gościom logowanie się do aplikacji usługi Azure AD przy użyciu własnego konta Gmail
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b26679542753d5fb429c33e4220c23a3937c5cb
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430443"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users-preview"></a>Dodaj firmę Google jako dostawcę tożsamości dla użytkowników gościa B2B (wersja zapoznawcza)

|     |
| --- |
| Google Federation jest publiczną funkcją w wersji zapoznawczej Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz temat [Dodatkowe warunki użytkowania dotyczące wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Konfigurując Federacji za pomocą usługi Google, możesz zezwolić zaproszonym użytkownikom na logowanie się do udostępnionych aplikacji i zasobów przy użyciu własnych kont usługi Gmail, bez konieczności tworzenia kont Microsoft (kont MSA) lub Azure AD. Firma Google Federation została zaprojektowana specjalnie dla użytkowników usługi Gmail. Aby sfederować z domenami usługi G Suite, zamiast tego należy użyć [funkcji Federacji bezpośredniej](direct-federation.md) .
> [!NOTE]
> Użytkownicy usługi Google Guest muszą się zalogować przy użyciu linku zawierającego kontekst dzierżawy (na przykład `https://myapps.microsoft.com/?tenantid=<tenant id>` lub `https://portal.azure.com/<tenant id>` lub w przypadku zweryfikowanej domeny `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`). Bezpośrednie linki do aplikacji i zasobów działają również tak długo, jak w przypadku kontekstu dzierżawy. Użytkownicy-Goście nie mogą obecnie zalogować się za pomocą punktów końcowych, które nie mają kontekstu dzierżawy. Na przykład użycie `https://myapps.microsoft.com`, `https://portal.azure.com` lub wspólnych punktów końcowych zespołów spowoduje wystąpienie błędu.
 
## <a name="what-is-the-experience-for-the-google-user"></a>Co to jest środowisko użytkownika Google?
Po wysłaniu zaproszenia do użytkownika usługi Google Gmail użytkownik-Gość powinien uzyskać dostęp do udostępnionych aplikacji lub zasobów przy użyciu linku zawierającego kontekst dzierżawy. Ich środowisko pracy różni się w zależności od tego, czy są już zalogowane w usłudze Google:
  - Jeśli użytkownik-Gość nie jest zalogowany do usługi Google, zostanie wyświetlony monit o zalogowanie do usługi Google.
  - Jeśli użytkownik-Gość jest już zalogowany do usługi Google, zostanie wyświetlony monit o wybranie konta, które ma być używane. Muszą wybrać konto użyte do zaproszenia.

Jeśli użytkownik-Gość widzi błąd "nagłówek jest zbyt długi", może wypróbować wyczyszczenie plików cookie lub otworzyć okno prywatne lub incognito i spróbować zalogować się ponownie.

![Zrzut ekranu przedstawiający stronę logowania Google](media/google-federation/google-sign-in.png)

## <a name="step-1-configure-a-google-developer-project"></a>Krok 1. Konfigurowanie projektu dla deweloperów Google
Najpierw utwórz nowy projekt w konsoli firmy Google Developers w celu uzyskania identyfikatora klienta i klucza tajnego klienta, który można później dodać do usługi Azure AD. 
1. Przejdź do interfejsów API firmy Google pod adresem https://console.developers.google.com i zaloguj się przy użyciu konta Google. Zalecamy używanie udostępnionego konta Google zespołu.
2. Utwórz nowy projekt: na pulpicie nawigacyjnym wybierz pozycję **Utwórz projekt**, a następnie wybierz pozycję **Utwórz**. Na stronie Nowy projekt wprowadź **nazwę projektu**, a następnie wybierz pozycję **Utwórz**.
   
   ![Zrzut ekranu przedstawiający nową stronę projektu dla usługi Google](media/google-federation/google-new-project.png)

3. Upewnij się, że nowy projekt został wybrany w menu Projekt. Następnie otwórz menu w lewym górnym rogu i wybierz pozycję **interfejsy api & Services** > **poświadczenia**.

   ![Zrzut ekranu przedstawiający opcję poświadczeń usługi Google API](media/google-federation/google-api.png)
 
4. Wybierz kartę **ekran wyrażania zgody na uwierzytelnianie OAuth** i wprowadź **nazwę aplikacji**. (Pozostaw pozostałe ustawienia).

   ![Zrzut ekranu przedstawiający opcję Ekran zgody usługi Google OAuth](media/google-federation/google-oauth-consent-screen.png)

5. Przewiń do sekcji **autoryzowane domeny** i wprowadź microsoftonline.com.

   ![Zrzut ekranu przedstawiający sekcję autoryzowane domeny](media/google-federation/google-oauth-authorized-domains.png)

6. Wybierz pozycję **Zapisz**.

7. Wybierz kartę **poświadczenia** . W menu **Tworzenie poświadczeń** wybierz pozycję **Identyfikator klienta OAuth**.

   ![Zrzut ekranu przedstawiający opcję tworzenia poświadczeń usługi Google API](media/google-federation/google-api-credentials.png)

8. W obszarze **Typ aplikacji**wybierz pozycję **aplikacja sieci Web**, a następnie w obszarze **autoryzowane identyfikatory URI przekierowania**wprowadź następujące identyfikatory URI:
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>(gdzie `<directory id>` to identyfikator katalogu)
   
     > [!NOTE]
     > Aby znaleźć identyfikator katalogu, przejdź do https://portal.azure.com, a w obszarze **Azure Active Directory**wybierz pozycję **Właściwości** i skopiuj **Identyfikator katalogu**.

   ![Zrzut ekranu przedstawiający sekcję autoryzowane identyfikatory URI przekierowania](media/google-federation/google-create-oauth-client-id.png)

9. Wybierz pozycję **Utwórz**. Skopiuj identyfikator klienta i klucz tajny klienta, który będzie używany podczas dodawania dostawcy tożsamości w portalu usługi Azure AD.

   ![Zrzut ekranu przedstawiający identyfikator klienta OAuth i klucz tajny klienta](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Krok 2. Konfigurowanie usługi Google Federation w usłudze Azure AD 
Teraz ustawisz identyfikator klienta Google i klucz tajny klienta, wprowadzając go w portalu usługi Azure AD lub przy użyciu programu PowerShell. Pamiętaj, aby przetestować konfigurację Federacji Google, zapraszając siebie przy użyciu adresu Gmail i próbując zrealizować zaproszenie przy użyciu zaproszonego konta Google. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>Aby skonfigurować usługę Google Federation w portalu usługi Azure AD 
1. Przejdź do witryny [Azure Portal](https://portal.azure.com). W lewym okienku wybierz pozycję **Azure Active Directory**. 
2. Wybierz **relacje organizacyjne**.
3. Wybierz pozycję **dostawcy tożsamości**, a następnie kliknij przycisk **Google** .
4. Wprowadź nazwę. Następnie wprowadź wcześniej otrzymany identyfikator klienta i klucz tajny klienta. Wybierz pozycję **Zapisz**. 

   ![Zrzut ekranu przedstawiający stronę Dodawanie dostawcy usługi Google Identity](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>Aby skonfigurować usługi Google Federation przy użyciu programu PowerShell
1. Zainstaluj najnowszą wersję modułu PowerShell usługi Azure AD dla programu Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Uruchom następujące polecenie: `Connect-AzureAD`.
3. W wierszu logowania zaloguj się przy użyciu zarządzanego konta administratora globalnego.  
4. Uruchom następujące polecenie: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Użyj identyfikatora klienta i klucza tajnego klienta z aplikacji utworzonej w sekcji "krok 1: Konfigurowanie projektu usługi Google Developer". Aby uzyskać więcej informacji, zobacz artykuł [New-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) . 
 
## <a name="how-do-i-remove-google-federation"></a>Jak mogę usunąć usługi Google Federation?
Możesz usunąć konfigurację usługi Google Federation. W takim przypadku użytkownicy-Goście usługi Google, którzy już skorzystali z zaproszenia, nie będą mogli się zalogować, ale możesz uzyskać dostęp do swoich zasobów ponownie, usuwając je z katalogu i ponownie zapraszając je. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>Aby usunąć usługę Google Federation w portalu usługi Azure AD: 
1. Przejdź do witryny [Azure Portal](https://portal.azure.com). W lewym okienku wybierz pozycję **Azure Active Directory**. 
2. Wybierz **relacje organizacyjne**.
3. Wybierz pozycję **dostawcy tożsamości**.
4. W wierszu **Google** wybierz menu kontekstowe ( **...** ), a następnie wybierz pozycję **Usuń**. 
   
   ![Zrzut ekranu przedstawiający opcję usuwania dla dostawcy tożsamości społecznościowej](media/google-federation/google-social-identity-providers.png)

1. Wybierz pozycję **tak** , aby potwierdzić usunięcie. 

### <a name="to-delete-google-federation-by-using-powershell"></a>Aby usunąć usługi Google Federation przy użyciu programu PowerShell: 
1. Zainstaluj najnowszą wersję modułu PowerShell usługi Azure AD dla programu Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Uruchom polecenie `Connect-AzureAD`.  
4. W oknie Logowanie w wierszu Zaloguj się przy użyciu zarządzanego konta administratora globalnego.  
5. Wprowadź następujące polecenie:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Aby uzyskać więcej informacji, zobacz [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview). 
