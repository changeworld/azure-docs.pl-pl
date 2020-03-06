---
title: Wymagania wstępne dotyczące interfejsu API raportowania Azure Active Directory | Microsoft Docs
description: Informacje o wymaganiach wstępnych dotyczących uzyskiwania dostępu do interfejsu API raportowania usługi Azure AD
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
ms.openlocfilehash: 12abfc0d345c937ae886f9bfacfb8ce30227cc45
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399299"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Wymagania wstępne dotyczące uzyskiwania dostępu do interfejsu API raportowania Azure Active Directory

[Interfejsy API raportowania usługi Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-reporting-api) umożliwiają dostęp programowy do danych za pomocą zestawu interfejsów API opartych na architekturze REST. Te interfejsy API można wywoływać z języków programowania i narzędzi.

Interfejs API raportowania używa protokołu [OAuth](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) do autoryzacji dostępu do interfejsów API sieci Web.

Aby przygotować dostęp do interfejsu API raportowania, musisz:

1. [Przypisywanie ról](#assign-roles)
2. [Rejestrowanie aplikacji](#register-an-application)
3. [Udziel uprawnień](#grant-permissions)
4. [Zbierz ustawienia konfiguracji](#gather-configuration-settings)

## <a name="assign-roles"></a>Przypisywanie ról

Aby uzyskać dostęp do danych raportowania za pomocą interfejsu API, należy przypisać jedną z następujących ról:

- Czytelnik zabezpieczeń

- Administrator zabezpieczeń

- Administrator globalny


## <a name="register-an-application"></a>Rejestrowanie aplikacji

Rejestracja jest wymagana nawet w przypadku uzyskiwania dostępu do interfejsu API raportowania przy użyciu skryptu. Rejestracja zapewnia **Identyfikator aplikacji**, który jest wymagany dla wywołań autoryzacji i umożliwia kodowi otrzymywanie tokenów.

Aby skonfigurować katalog do uzyskiwania dostępu do interfejsu API raportowania usługi Azure AD, musisz zalogować się do [Azure Portal](https://portal.azure.com) przy użyciu konta administratora platformy Azure, które jest również członkiem roli katalogu **administratora globalnego** w dzierżawie usługi Azure AD.

> [!IMPORTANT]
> Aplikacje działające w ramach poświadczeń z uprawnieniami administratora mogą być bardzo wydajne, dlatego pamiętaj o zachowaniu identyfikatora aplikacji i poświadczeń tajnych w bezpiecznej lokalizacji.
> 

**Aby zarejestrować aplikację usługi Azure AD:**

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **Azure Active Directory** z okienka nawigacji po lewej stronie.
   
    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Na stronie **Azure Active Directory** wybierz pozycję **rejestracje aplikacji**.

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. Na stronie **rejestracje aplikacji** wybierz pozycję **Nowa rejestracja**.

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. Strona **rejestracji aplikacji** :

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. W polu tekstowym **Nazwa** wpisz `Reporting API application`.

    b. W polu **Typ obsługiwanych kont**wybierz opcję **konta tylko w tej organizacji**.

    c. W polu tekstowym **adres URL przekierowania** wybierz pozycję **sieć Web** wpisz `https://localhost`.

    d. Wybierz pozycję **Zarejestruj**. 


## <a name="grant-permissions"></a>Udzielenie uprawnień 

W zależności od interfejsu API, do którego chcesz uzyskać dostęp, musisz udzielić aplikacji następujących uprawnień:  

| Interfejs API | Uprawnienie |
| --- | --- |
| Usługa Active Directory systemu Windows Azure | Odczyt danych katalogu |
| Microsoft Graph | Odczytaj wszystkie dane dziennika inspekcji |


![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/36.png)

W poniższej sekcji przedstawiono kroki dla obu interfejsów API. Jeśli nie chcesz uzyskiwać dostępu do jednego z interfejsów API, możesz pominąć powiązane kroki.

**Aby przyznać aplikacji uprawnienia do używania interfejsów API:**


1. Wybierz pozycję **uprawnienia interfejsu API** , a następnie **Dodaj uprawnienie**. 

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. Na **stronie uprawnienia interfejsu API żądania**Znajdź pozycję **Obsługa STARSZEJ wersji interfejsu API** **Azure Active Directory Graph**. 

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. Na stronie **wymagane uprawnienia** wybierz pozycję **uprawnienia aplikacji**, rozwiń węzeł katalog pola wyboru **katalogu** **. readal**.  Wybierz pozycję **Dodaj uprawnienia**.

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. Na stronie **raportowanie interfejsu API aplikacji — uprawnienia interfejsu API** wybierz pozycję **Udziel zgody administratora**. 

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Uwaga: podczas rejestrowania interfejsu API **Microsoft Graph** jest domyślnie dodawane.

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/15.png)

## <a name="gather-configuration-settings"></a>Zbierz ustawienia konfiguracji 

W tej sekcji przedstawiono sposób uzyskiwania następujących ustawień z katalogu:

- Nazwa domeny
- Identyfikator klienta
- Klucz tajny klienta

Te wartości są potrzebne podczas konfigurowania wywołań do interfejsu API raportowania. 

### <a name="get-your-domain-name"></a>Pobierz nazwę domeny

**Aby uzyskać nazwę domeny:**

1. W [Azure Portal](https://portal.azure.com)w okienku nawigacji po lewej stronie wybierz pozycję **Azure Active Directory**.
   
    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Na stronie **Azure Active Directory** wybierz pozycję **niestandardowe nazwy domen**.

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Skopiuj nazwę domeny z listy domen.


### <a name="get-your-applications-client-id"></a>Pobieranie identyfikatora klienta aplikacji

**Aby uzyskać identyfikator klienta aplikacji:**

1. W [Azure Portal](https://portal.azure.com)w okienku nawigacji po lewej stronie kliknij pozycję **Azure Active Directory**.
   
    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Wybierz aplikację ze strony **rejestracje aplikacji** .

3. Na stronie aplikacja przejdź do strony **Identyfikator aplikacji** i wybierz **pozycję kliknij, aby skopiować**.

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Pobierz klucz tajny klienta aplikacji
 Należy unikać błędów próbujących uzyskać dostęp do dzienników inspekcji lub logowania przy użyciu interfejsu API.

**Aby uzyskać klucz tajny klienta aplikacji:**

1. W [Azure Portal](https://portal.azure.com)w okienku nawigacji po lewej stronie kliknij pozycję **Azure Active Directory**.
   
    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Wybierz aplikację ze strony **rejestracje aplikacji** .

3.  Na stronie **aplikacja interfejsu API** wybierz pozycję **Certyfikaty i wpisy tajne, a** następnie w sekcji wpisy **tajne klienta** kliknij pozycję **+ Nowy wpis tajny klienta**. 

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. Na stronie **Dodawanie wpisu tajnego klienta** Dodaj następujące polecenie:

    a. W polu tekstowym **Opis** wpisz `Reporting API`.

    b. Zgodnie z **wygaśnięciem**wybierz pozycję **za 2 lata**.

    c. Kliknij przycisk **Save** (Zapisz).

    d. Skopiuj wartość klucza.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Rozwiązywanie problemów z błędami w interfejsie API raportowania

W tej sekcji wymieniono typowe komunikaty o błędach, które mogą być używane podczas uzyskiwania dostępu do raportów aktywności przy użyciu interfejsu API Microsoft Graph i kroki ich rozwiązywania.

### <a name="error-failed-to-get-user-roles-from-microsoft-graph"></a>Błąd: nie można pobrać ról użytkownika z Microsoft Graph

 Zaloguj się do swojego konta przy użyciu obu przycisków logowania w interfejsie użytkownika programu Graph Explorer, aby uniknąć błędu podczas próby zalogowania się za pomocą Eksploratora grafów. 

![Eksplorator programu Graph](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-microsoft-graph"></a>Błąd: nie można przeprowadzić sprawdzania licencji Premium z Microsoft Graph 

Jeśli ten komunikat o błędzie jest uruchamiany podczas próby dostępu do logowania za pomocą Eksploratora grafów, wybierz pozycję **Modyfikuj uprawnienia** pod Twoim kontem na lewym pasku nawigacyjnym, a następnie wybierz kolejno pozycje **Tasks. ReadWrite** i **Directory. Read. All**. 

![Modyfikuj interfejs użytkownika uprawnień](./media/troubleshoot-graph-api/modify-permissions.png)

### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>Błąd: Dzierżawca nie jest B2C lub dzierżawca nie ma licencji Premium

Uzyskiwanie dostępu do raportów logowania wymaga licencji Azure Active Directory Premium 1 (P1). Jeśli ten komunikat o błędzie jest wyświetlany podczas uzyskiwania dostępu do logowania, upewnij się, że dzierżawa jest licencjonowana przy użyciu licencji usługi Azure AD P1.

### <a name="error-the-allowed-roles-does-not-include-user"></a>Błąd: dozwolone role nie obejmują użytkownika. 

 Należy unikać błędów próbujących uzyskać dostęp do dzienników inspekcji lub logowania przy użyciu interfejsu API. Upewnij się, że Twoje konto jest częścią roli **czytelnik zabezpieczeń** lub **czytelnik raportu** w dzierżawie Azure Active Directory.

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Błąd: brak uprawnienia do odczytu danych katalogu usługi AAD w aplikacji 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Błąd: brak aplikacji Microsoft Graph interfejs API "Odczyt wszystkich danych dziennika inspekcji"

Wykonaj kroki opisane w sekcji [wymagania wstępne, aby uzyskać dostęp do interfejsu API raportowania Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md) , aby upewnić się, że aplikacja działa z odpowiednim zestawem uprawnień. 

## <a name="next-steps"></a>Następne kroki

* [Pobieranie danych przy użyciu interfejsu API raportowania Azure Active Directory z certyfikatami](tutorial-access-api-with-certificates.md)
* [Dokumentacja interfejsu API inspekcji](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Dokumentacja interfejsu API raportów działań związanych z logowaniem](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
