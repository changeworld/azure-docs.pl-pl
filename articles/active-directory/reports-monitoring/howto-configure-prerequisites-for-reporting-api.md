---
title: Wymagania wstępne dotyczące uzyskiwania dostępu do interfejsu API raportowania Azure Active Directory | Microsoft Docs
description: Informacje o wymaganiach wstępnych dotyczących uzyskiwania dostępu do interfejsu API raportowania usługi Azure AD
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f925a86504d68fd08b83c63e4da8b37b4aa25f85
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989899"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Wymagania wstępne dotyczące uzyskiwania dostępu do interfejsu API raportowania Azure Active Directory

[Interfejsy API raportowania usługi Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) umożliwiają dostęp programowy do danych za pomocą zestawu interfejsów API opartych na architekturze REST. Te interfejsy API można wywoływać przy użyciu różnych języków i narzędzi do programowania.

Interfejs API raportowania używa protokołu [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) do autoryzacji dostępu do interfejsów API sieci Web.

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


## <a name="register-an-application"></a>Zarejestruj aplikację

Musisz zarejestrować aplikację, nawet jeśli uzyskujesz dostęp do interfejsu API raportowania przy użyciu skryptu. Zapewnia to **Identyfikator aplikacji**, który jest wymagany dla wywołań autoryzacji i umożliwia kodowi otrzymywanie tokenów.

Aby skonfigurować katalog do uzyskiwania dostępu do interfejsu API raportowania usługi Azure AD, musisz zalogować się do [Azure Portal](https://portal.azure.com) przy użyciu konta administratora platformy Azure, które jest również członkiem roli katalogu **administratora globalnego** w dzierżawie usługi Azure AD.

> [!IMPORTANT]
> Aplikacje działające w ramach poświadczeń z uprawnieniami administratora mogą być bardzo wydajne, dlatego pamiętaj o zachowaniu identyfikatora aplikacji i poświadczeń tajnych w bezpiecznej lokalizacji.
> 

**Aby zarejestrować aplikację usługi Azure AD:**

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **Azure Active Directory** z okienka nawigacji po lewej stronie.
   
    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Na stronie **Azure Active Directory** wybierz pozycję **rejestracje aplikacji**.

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. Na stronie **rejestracje aplikacji** wybierz pozycję **rejestracja nowej aplikacji**.

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. Na stronie **Tworzenie** wykonaj następujące czynności:

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. W polu tekstowym **Nazwa** wpisz `Reporting API application`.

    b. W obszarze **Typ aplikacji**wybierz pozycję **aplikacja sieci Web/interfejs API**.

    c. W polu tekstowym **adres URL logowania** wpisz `https://localhost`.

    d. Wybierz pozycję **Utwórz**. 


## <a name="grant-permissions"></a>Udzielenie uprawnień 

W zależności od interfejsu API, do którego chcesz uzyskać dostęp, musisz udzielić aplikacji następujących uprawnień:  

| interfejs API | Uprawnienie |
| --- | --- |
| Azure Active Directory systemu Windows | Odczytaj dane katalogu |
| Microsoft Graph | Odczytaj wszystkie dane dziennika inspekcji |


![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/36.png)

W poniższej sekcji przedstawiono kroki dla obu interfejsów API. Jeśli nie chcesz uzyskiwać dostępu do jednego z interfejsów API, możesz pominąć powiązane kroki.

**Aby przyznać aplikacji uprawnienia do używania interfejsów API:**

1. Wybierz aplikację ze strony **rejestracje aplikacji** i wybierz pozycję **Ustawienia**. 

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. Na stronie **Ustawienia** wybierz pozycję **wymagane uprawnienia**. 

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. Na stronie **wymagane uprawnienia** na liście **interfejs API** kliknij pozycję **Windows Azure Active Directory**. 

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. Na stronie **Włączanie dostępu** wybierz pozycję **Odczytaj dane katalogu** i usuń zaznaczenie opcji **Zaloguj się i odczytaj profil użytkownika**. 

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Na pasku narzędzi u góry kliknij przycisk **Zapisz**.

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/15.png)

6. Na stronie **wymagane uprawnienia** na pasku narzędzi u góry kliknij przycisk **Dodaj**.

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/32.png)

7. Na stronie **Dodawanie dostępu do interfejsu API** kliknij pozycję **Wybierz interfejs API**.

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/31.png)

8. Na stronie **Wybieranie interfejsu API** kliknij przycisk **Microsoft Graph**, a następnie kliknij przycisk **Wybierz**.

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/33.png)

9. Na stronie **Włączanie dostępu** wybierz opcję **Czytaj wszystkie dane dziennika inspekcji**, a następnie kliknij przycisk **Wybierz**.  

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/34.png)

10. Na stronie **Dodawanie dostępu do interfejsu API** kliknij przycisk **gotowe**.  

11. Na stronie **wymagane uprawnienia** na pasku narzędzi u góry. Kliknij pozycję **Udziel uprawnień**, a następnie kliknij przycisk **tak**.

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/17.png)


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
Aby uzyskać klucz tajny klienta aplikacji, musisz utworzyć nowy klucz i zapisać jego wartość przy zapisywaniu nowego klucza, ponieważ nie można już pobrać tej wartości później.

**Aby uzyskać klucz tajny klienta aplikacji:**

1. W [Azure Portal](https://portal.azure.com)w okienku nawigacji po lewej stronie kliknij pozycję **Azure Active Directory**.
   
    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Wybierz aplikację ze strony **rejestracje aplikacji** .

3. Na stronie aplikacja na pasku narzędzi u góry wybierz pozycję **Ustawienia**. 

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/05.png)

4. Na stronie **Ustawienia** w sekcji dostęp do **interfejsu API** kliknij pozycję **klucze**. 

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. Na stronie **klucze** wykonaj następujące czynności:

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/14.png)

    a. W polu tekstowym **Opis** wpisz `Reporting API`.

    b. Zgodniez wygaśnięciem wybierz pozycję **za 2 lata**.

    c. Kliknij polecenie **Zapisz**.

    d. Skopiuj wartość klucza.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Rozwiązywanie problemów z błędami w interfejsie API raportowania

W tej sekcji wymieniono typowe komunikaty o błędach, które można napotkać podczas uzyskiwania dostępu do raportów aktywności przy użyciu programu MS interfejs API programu Graph i kroków dla ich rozwiązania.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>Błąd wewnętrzny serwera HTTP 500 podczas uzyskiwania dostępu do punktu końcowego Microsoft Graph v2

Obecnie nie obsługujemy punktu końcowego Microsoft Graph v2 — upewnij się, że uzyskujesz dostęp do dzienników aktywności przy użyciu punktu końcowego Microsoft Graph v1.

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>Błąd: Nie można pobrać ról użytkownika z grafu usługi AD

Ten komunikat o błędzie może pojawić się podczas próby dostępu do logowania za pomocą Eksploratora grafów. Upewnij się, że logujesz się do swojego konta przy użyciu obu przycisków logowania w interfejsie użytkownika programu Graph Explorer, jak pokazano na poniższej ilustracji. 

![Eksplorator programu Graph](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>Błąd: Nie można przeprowadzić kontroli licencji Premium na podstawie grafu usługi AD 

Jeśli ten komunikat o błędzie jest uruchamiany podczas próby dostępu do logowania za pomocą Eksploratora grafów, wybierz pozycję **Modyfikuj uprawnienia** pod Twoim kontem na lewym pasku nawigacyjnym, a następnie wybierz kolejno pozycje **Tasks. ReadWrite** i **Directory. Read. All**. 

![Modyfikuj interfejs użytkownika uprawnień](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Błąd: Żadna dzierżawa nie jest B2C lub dzierżawa nie ma licencji Premium

Uzyskiwanie dostępu do raportów logowania wymaga licencji Azure Active Directory Premium 1 (P1). Jeśli ten komunikat o błędzie jest wyświetlany podczas uzyskiwania dostępu do logowania, upewnij się, że dzierżawa jest licencjonowana przy użyciu licencji usługi Azure AD P1.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Błąd: Użytkownik nie znajduje się w dozwolonych rolach 

Jeśli ten komunikat o błędzie jest wyświetlany podczas próby dostępu do dzienników inspekcji lub logowania przy użyciu interfejsu API, upewnij się, że konto należy do roli **czytelnik zabezpieczeń** lub **czytelnik raportu** w dzierżawie Azure Active Directory. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Błąd: Brak uprawnienia do odczytu danych katalogu usługi AAD w aplikacji 

Wykonaj kroki opisane w sekcji [wymagania wstępne, aby uzyskać dostęp do interfejsu API raportowania Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md) , aby upewnić się, że aplikacja działa z odpowiednim zestawem uprawnień. 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>Błąd: Brak uprawnienia MSGraph interfejsu API "Odczyt wszystkich danych dziennika inspekcji" w aplikacji

Wykonaj kroki opisane w sekcji [wymagania wstępne, aby uzyskać dostęp do interfejsu API raportowania Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md) , aby upewnić się, że aplikacja działa z odpowiednim zestawem uprawnień. 

## <a name="next-steps"></a>Następne kroki

* [Pobieranie danych przy użyciu interfejsu API raportowania Azure Active Directory z certyfikatami](tutorial-access-api-with-certificates.md)
* [Dokumentacja interfejsu API inspekcji](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Dokumentacja interfejsu API raportów działań związanych z logowaniem](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
