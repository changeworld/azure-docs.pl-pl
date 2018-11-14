---
title: Wymagania wstępne dotyczące dostępu do usługi Azure Active Directory, interfejsu API raportowania | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o wymaganiach wstępnych dostępu do usługi Azure AD, interfejsu API raportowania
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: f72d15707d9f56b9e9b5a5d527d1204007c40afa
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51621976"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Wymagania wstępne dotyczące dostępu do usługi Azure Active Directory, interfejsu API raportowania

[Interfejsy API raportowania usługi Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) umożliwiają dostęp programowy do danych za pomocą zestawu interfejsów API opartych na architekturze REST. Te interfejsy API można wywoływać przy użyciu różnych języków i narzędzi do programowania.

Interfejs API raportowania używa [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) do autoryzacji dostępu do interfejsów API sieci web.

Aby przygotować Ci dostęp do interfejsu API raportowania, należy:

1. [Przypisywanie ról](#assign-roles)
2. [Rejestrowanie aplikacji](#register-an-application)
3. [Udziel uprawnień](#grant-permissions)
4. [Zbieranie ustawień konfiguracji](#gather-configuration-settings)

## <a name="assign-roles"></a>Przypisywanie ról

Aby uzyskać dostęp do danych raportowania za pomocą interfejsu API, musisz mieć jedną z następujących ról, które są przypisane:

- Odczytywanie zabezpieczeń

- Administrator zabezpieczeń

- Administrator globalny


## <a name="register-an-application"></a>Rejestrowanie aplikacji

Musisz zarejestrować aplikację, nawet jeśli uzyskujesz dostęp do interfejsu API raportowania za pomocą skryptu. Daje to **identyfikator aplikacji**, co jest wymagane dla autoryzacji wywołań i umożliwia swój kod, aby odbierać tokeny.

Aby skonfigurować katalog na dostęp do interfejsu API raportowania usługi Azure AD, musisz się zalogować do [witryny Azure portal](https://portal.azure.com) przy użyciu konta administratora platformy Azure, który jest również członkiem **administratora globalnego** rola katalogu w ramach dzierżawy usługi Azure AD.

> [!IMPORTANT]
> Aplikacje działające w ramach poświadczeń z uprawnieniami administratora może być bardzo skuteczne, dlatego Pamiętaj o zapewnienie Identyfikatora aplikacji i wpisu tajnego poświadczeń w bezpiecznej lokalizacji.
> 

**Aby zarejestrować aplikację usługi Azure AD:**

1. W [witryny Azure portal](https://portal.azure.com), wybierz opcję **usługi Azure Active Directory** z okienka nawigacji po lewej stronie.
   
    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. W **usługi Azure Active Directory** wybierz opcję **rejestracje aplikacji**.

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. Z **rejestracje aplikacji** wybierz opcję **rejestrowanie nowej aplikacji**.

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. W **Utwórz** strony, wykonaj następujące czynności:

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. W **nazwa** polu tekstowym wpisz `Reporting API application`.

    b. Jako **typ aplikacji**, wybierz opcję **aplikacji sieci Web / interfejs API**.

    c. W **adres URL logowania** polu tekstowym wpisz `https://localhost`.

    d. Wybierz pozycję **Utwórz**. 


## <a name="grant-permissions"></a>Udzielenie uprawnień 

W zależności od interfejsu API, którego chcesz uzyskać dostęp musisz przyznać aplikacji następujące uprawnienia:  

| Interfejs API | Uprawnienie |
| --- | --- |
| Windows Azure Active Directory | Odczytaj dane katalogu |
| Microsoft Graph | Odczytywać wszystkie dane dziennika inspekcji |


![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/36.png)

W poniższej sekcji przedstawiono kroki dla obu interfejsów API. Jeśli nie chcesz uzyskać dostęp do jednego z interfejsów API, można pominąć kroki powiązane.

**Aby udzielić uprawnień aplikacji do użycia interfejsów API:**

1. Wybierz swoją aplikację z **rejestracje aplikacji** strony i wybierz **ustawienia**. 

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. Na **ustawienia** wybierz opcję **wymagane uprawnienia**. 

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. Na **wymagane uprawnienia** strony w **API** kliknij **Windows Azure Active Directory**. 

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. Na **Włącz dostęp za pomocą** wybierz opcję **Odczyt danych katalogu** i usuń zaznaczenie opcji **Zaloguj się i odczytuj profil użytkownika**. 

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Na pasku narzędzi u góry kliknij **Zapisz**.

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/15.png)

6. Na **wymagane uprawnienia** w pasku narzędzi u góry, kliknij **Dodaj**.

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/32.png)

7. Na **dostępu Dodaj interfejs API** kliknij **wybierz interfejs API**.

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/31.png)

8. Na **wybierz interfejs API** kliknij **programu Microsoft Graph**, a następnie kliknij przycisk **wybierz**.

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/33.png)

9. Na **Włącz dostęp za pomocą** wybierz opcję **odczytywać wszystkie dane dziennika inspekcji**, a następnie kliknij przycisk **wybierz**.  

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/34.png)

10. Na **dostępu Dodaj interfejs API** kliknij **gotowe**.  

11. Na **wymagane uprawnienia** strony, na pasku narzędzi u góry. Kliknij przycisk **Udziel uprawnień**, a następnie kliknij przycisk **tak**.

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/17.png)


## <a name="gather-configuration-settings"></a>Zbieranie ustawień konfiguracji 

W tej sekcji dowiesz się, jak uzyskać następujące ustawienia z katalogiem:

- Nazwa domeny
- Identyfikator klienta
- Klucz tajny klienta

Te wartości będą potrzebne podczas konfigurowania wywołania interfejsu API raportowania. 

### <a name="get-your-domain-name"></a>Pobierz nazwę domeny

**Aby uzyskać nazwy domeny:**

1. W [witryny Azure portal](https://portal.azure.com), w okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory**.
   
    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Na **usługi Azure Active Directory** wybierz opcję **niestandardowe nazwy domen**.

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Skopiuj nazwę domeny, z listy domen.


### <a name="get-your-applications-client-id"></a>Pobieranie Identyfikatora klienta aplikacji

**Aby uzyskać identyfikator klienta Twojej aplikacji:**

1. W [witryny Azure portal](https://portal.azure.com), w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory**.
   
    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Wybierz swoją aplikację z **rejestracje aplikacji** strony.

3. Ze strony aplikacji, przejdź do **identyfikator aplikacji** i wybierz **kliknij, aby skopiować**.

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Pobierz klucz tajny klienta aplikacji
Aby uzyskać klucz tajny klienta aplikacji, musisz utworzyć nowy klucz i zapisać jego wartość po zapisaniu nowego klucza, ponieważ nie jest możliwe do pobierania tej wartości w dalszej części już.

**Aby wyświetlić klucz tajny klienta Twojej aplikacji:**

1. W [witryny Azure portal](https://portal.azure.com), w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory**.
   
    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Wybierz swoją aplikację z **rejestracje aplikacji** strony.

3. Na pasku narzędzi u góry, na stronie aplikacji wybierz **ustawienia**. 

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/05.png)

4. Na **ustawienia** stronie **dostęp do interfejsu API** kliknij **klucze**. 

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. Na **klucze** strony, wykonaj następujące czynności:

    ![Zarejestruj aplikację](./media/howto-configure-prerequisites-for-reporting-api/14.png)

    a. W **opis** polu tekstowym wpisz `Reporting API`.

    b. Jako **Expires**, wybierz opcję **za 2 lata**.

    c. Kliknij pozycję **Zapisz**.

    d. Skopiuj wartość klucza.


## <a name="next-steps"></a>Kolejne kroki

* [Pobieranie danych przy użyciu usługi Azure Active Directory, interfejsu API raportowania przy użyciu certyfikatów](tutorial-access-api-with-certificates.md)
* [Dokumentacja interfejsu API inspekcji](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Raport aktywności logowania, dokumentacja interfejsu API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
