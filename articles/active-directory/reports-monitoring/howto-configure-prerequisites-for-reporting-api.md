---
title: Wymagania wstępne dla interfejsu API raportowania usługi Azure Active Directory | Dokumenty firmy Microsoft
description: Dowiedz się więcej o wymaganiach wstępnych dostępu do interfejsu API raportowania usługi Azure AD
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fd3580ca03fa49d428904c6da78fdf9cda202c7
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991266"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Wymagania wstępne dostępu do interfejsu API raportowania usługi Azure Active Directory

[Interfejsy API raportowania usługi Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-reporting-api) umożliwiają dostęp programowy do danych za pomocą zestawu interfejsów API opartych na architekturze REST. Te interfejsy API można wywołać z języków programowania i narzędzi.

Interfejs API raportowania używa [OAuth](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) do autoryzowania dostępu do interfejsów API sieci web.

Aby przygotować dostęp do interfejsu API raportowania, należy:

1. [Przypisywanie ról](#assign-roles)
2. [Wymagania licencyjne](#license-requirements)
3. [Rejestrowanie aplikacji](#register-an-application)
4. [Udzielenie uprawnień](#grant-permissions)
5. [Zbieranie ustawień konfiguracji](#gather-configuration-settings)

## <a name="assign-roles"></a>Przypisywanie ról

Aby uzyskać dostęp do danych raportowania za pośrednictwem interfejsu API, musisz mieć przypisaną jedną z następujących ról:

- Czytelnik zabezpieczeń

- Administrator zabezpieczeń

- Administrator globalny

## <a name="license-requirements"></a>Wymagania licencyjne

Aby uzyskać dostęp do raportów logowania dla dzierżawy, dzierżawa usługi Azure AD musi mieć skojarzoną licencję usługi Azure AD Premium. Licencja usługi Azure AD Premium P1 (lub wyższej) jest wymagana do uzyskiwania dostępu do raportów logowania dla dowolnej dzierżawy usługi Azure AD. Alternatywnie, jeśli typem katalogu jest usługa Azure AD B2C, raporty logowania są dostępne za pośrednictwem interfejsu API bez żadnych dodatkowych wymagań licencyjnych. 


## <a name="register-an-application"></a>Rejestrowanie aplikacji

Rejestracja jest potrzebna, nawet jeśli uzyskujesz dostęp do interfejsu API raportowania przy użyciu skryptu. Rejestracja daje **identyfikator aplikacji**, który jest wymagany dla wywołań autoryzacji i umożliwia kod do odbierania tokenów.

Aby skonfigurować katalog, aby uzyskać dostęp do interfejsu API raportowania usługi Azure AD, należy zalogować się do [witryny Azure portal](https://portal.azure.com) za pomocą konta administratora platformy Azure, który jest również członkiem roli katalogu **administratora globalnego** w dzierżawie usługi Azure AD.

> [!IMPORTANT]
> Aplikacje działające w obszarze poświadczeń z uprawnieniami administratora mogą być bardzo wydajne, dlatego należy zachować identyfikator aplikacji i tajne poświadczenia w bezpiecznej lokalizacji.
> 

**Aby zarejestrować aplikację usługi Azure AD:**

1. W [witrynie Azure portal](https://portal.azure.com)wybierz **pozycję Azure Active Directory** z lewego okienka nawigacji.
   
    ![Rejestrowanie aplikacji](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Na stronie **Usługi Azure Active Directory** wybierz pozycję **Rejestracje aplikacji**.

    ![Rejestrowanie aplikacji](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. Na stronie **Rejestracje aplikacji** wybierz pozycję **Nowa rejestracja**.

    ![Rejestrowanie aplikacji](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. Strona **Rejestracja aplikacji:**

    ![Rejestrowanie aplikacji](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. W **Name** polach tekstowych `Reporting API application`Nazwa wpisz .

    b. W przypadku **typu Obsługiwane konta**wybierz pozycję Konta tylko w tej **organizacji**.

    d. W polu **tekstowym Przekierowanie** `https://localhost`adresu URL wybierz pozycję **Web** textbox wpisz .

    d. Wybierz pozycję **Zarejestruj**. 


## <a name="grant-permissions"></a>Udzielenie uprawnień 

W zależności od interfejsu API, do którego chcesz uzyskać dostęp, musisz udzielić aplikacji następujących uprawnień:  

| interfejs API | Uprawnienie |
| --- | --- |
| Windows Azure Active Directory | Odczytywanie danych katalogu |
| Microsoft Graph | Przeczytaj wszystkie dane dziennika inspekcji |


![Rejestrowanie aplikacji](./media/howto-configure-prerequisites-for-reporting-api/36.png)

W poniższej sekcji wymieniono kroki dotyczące obu interfejsów API. Jeśli nie chcesz uzyskać dostępu do jednego z interfejsów API, możesz pominąć powiązane kroki.

**Aby udzielić aplikacji uprawnień do korzystania z interfejsów API:**


1. Wybierz **pozycję Uprawnienia interfejsu API,** a następnie dodaj **uprawnienie**. 

    ![Rejestrowanie aplikacji](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. Na **stronie Żądania uprawnień interfejsu API**znajdź **pozycję Obsługa starszego programu API** Azure Active **Directory Graph**. 

    ![Rejestrowanie aplikacji](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. Na stronie **Wymagane uprawnienia** wybierz pozycję **Uprawnienia aplikacji**, rozwiń pole wyboru **Katalog.ReadAll**. **Directory**  Wybierz pozycję **Dodaj uprawnienia**.

    ![Rejestrowanie aplikacji](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. Na stronie **Raportowanie aplikacji interfejsu API — uprawnienia interfejsu API** wybierz pozycję **Udzielaj zgody administratora**. 

    ![Rejestrowanie aplikacji](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Uwaga: **Program Microsoft Graph** jest dodawany domyślnie podczas rejestracji interfejsu API.

    ![Rejestrowanie aplikacji](./media/howto-configure-prerequisites-for-reporting-api/15.png)

## <a name="gather-configuration-settings"></a>Zbieranie ustawień konfiguracji 

W tej sekcji pokazano, jak uzyskać następujące ustawienia z katalogu:

- Nazwa domeny
- Identyfikator klienta
- Klucz tajny klienta

Te wartości są potrzebne podczas konfigurowania wywołań interfejsu API raportowania. 

### <a name="get-your-domain-name"></a>Uzyskaj swoją nazwę domeny

**Aby uzyskać nazwę domeny:**

1. W [witrynie Azure portal](https://portal.azure.com)w lewym okienku nawigacji wybierz pozycję **Azure Active Directory**.
   
    ![Rejestrowanie aplikacji](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Na stronie **Usługi Azure Active Directory** wybierz pozycję **Niestandardowe nazwy domen**.

    ![Rejestrowanie aplikacji](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Skopiuj nazwę domeny z listy domen.


### <a name="get-your-applications-client-id"></a>Uzyskaj identyfikator klienta aplikacji

**Aby uzyskać identyfikator klienta aplikacji:**

1. W [witrynie Azure portal](https://portal.azure.com)w lewym okienku nawigacji kliknij pozycję **Azure Active Directory**.
   
    ![Rejestrowanie aplikacji](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Wybierz aplikację ze strony **Rejestracje aplikacji.**

3. Na stronie aplikacji przejdź do **pozycji Identyfikator aplikacji** i wybierz pozycję **Kliknij, aby skopiować**.

    ![Rejestrowanie aplikacji](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Uzyskaj klucz tajny klienta aplikacji
 Unikaj błędów podczas próby uzyskania dostępu do dzienników inspekcji lub logowania się przy użyciu interfejsu API.

**Aby uzyskać klucz tajny klienta aplikacji:**

1. W [witrynie Azure portal](https://portal.azure.com)w lewym okienku nawigacji kliknij pozycję **Azure Active Directory**.
   
    ![Rejestrowanie aplikacji](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Wybierz aplikację ze strony **Rejestracje aplikacji.**

3.  Wybierz **certyfikaty i wpisy tajne** na stronie **Aplikacji interfejsu API,** w sekcji **Wpisy klientów** kliknij przycisk + Nowy klucz tajny **klienta**. 

    ![Rejestrowanie aplikacji](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. Na stronie **Dodawanie klucza tajnego klienta** dodaj:

    a. W **Description** polu tekstowym `Reporting API`Opis wpisz .

    b. Jako **Wygasa**, wybierz **w ciągu 2 lat**.

    d. Kliknij przycisk **Zapisz**.

    d. Skopiuj wartość klucza.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Rozwiązywanie problemów z błędami w interfejsie API raportowania

W tej sekcji wymieniono typowe komunikaty o błędach, na które można napotkać podczas uzyskiwania dostępu do raportów aktywności przy użyciu interfejsu API programu Microsoft Graph i kroków dotyczących ich rozwiązania.

### <a name="error-failed-to-get-user-roles-from-microsoft-graph"></a>Błąd: nie można uzyskać ról użytkowników z programu Microsoft Graph

 Zaloguj się na swoje konto przy użyciu obu przycisków logowania w interfejsie użytkownika Graph Explorer, aby uniknąć błędu podczas próby zalogowania się przy użyciu Eksploratora wykresu. 

![Eksplorator programu Graph](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-microsoft-graph"></a>Błąd: nie można sprawdzić licencji premium w programie Microsoft Graph 

Jeśli ten komunikat o błędzie zostanie wyświetlony podczas próby uzyskania dostępu do logów za pomocą Eksploratora wykresu, wybierz pozycję **Modyfikuj uprawnienia** pod kontem na lewej stronie urządzenia nawigacyjnego, a następnie wybierz pozycję **Tasks.ReadWrite** i **Directory.Read.All**. 

![Modyfikowanie interfejsu użytkownika uprawnień](./media/troubleshoot-graph-api/modify-permissions.png)

### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>Błąd: dzierżawca nie jest B2C lub dzierżawca nie ma licencji premium

Uzyskiwanie dostępu do raportów logowania wymaga licencji premium usługi Azure Active Directory 1 (P1). Jeśli ten komunikat o błędzie jest wyświetlany podczas uzyskiwania dostępu do logowania, upewnij się, że dzierżawa jest licencjonowana z licencją usługi Azure AD P1.

### <a name="error-the-allowed-roles-does-not-include-user"></a>Błąd: Dozwolone role nie obejmują użytkownika. 

 Unikaj błędów podczas próby uzyskania dostępu do dzienników inspekcji lub logowania się przy użyciu interfejsu API. Upewnij się, że twoje konto jest częścią roli **czytnika zabezpieczeń** lub **programu Report Reader** w dzierżawie usługi Azure Active Directory.

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Błąd: aplikacja brakuje uprawnienia AAD "Odczyt danych katalogu" 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Błąd: aplikacja brakuje interfejsu API programu Microsoft Graph "Odczyt wszystkich danych dziennika inspekcji"

Wykonaj kroki opisane w [wymaganiach wstępnych, aby uzyskać dostęp do interfejsu API raportowania usługi Azure Active Directory,](howto-configure-prerequisites-for-reporting-api.md) aby upewnić się, że aplikacja jest uruchomiona z odpowiednim zestawem uprawnień. 

## <a name="next-steps"></a>Następne kroki

* [Pobieranie danych przy użyciu interfejsu API raportowania usługi Azure Active Directory z certyfikatami](tutorial-access-api-with-certificates.md)
* [Inspekcja odwołania interfejsu API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Odwołanie do interfejsu API raportu aktywności logowania](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
