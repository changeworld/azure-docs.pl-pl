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
ms.component: compliance-reports
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: a9dc30f1d303c6849ed016d0bc81ce1bf3d3d9d5
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390623"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Wymagania wstępne dotyczące dostępu do usługi Azure Active Directory, interfejsu API raportowania

[Interfejsy API raportowania usługi Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) umożliwiają dostęp programowy do danych za pomocą zestawu interfejsów API opartych na architekturze REST. Te interfejsy API można wywoływać przy użyciu różnych języków i narzędzi do programowania.

Interfejs API raportowania używa [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) do autoryzacji dostępu do interfejsów API sieci web.

Aby przygotować Ci dostęp do interfejsu API raportowania, należy:

1. Przypisz role
2. Rejestrowanie aplikacji
3. Udzielenie uprawnień
4. Zbieranie ustawień konfiguracji



## <a name="assign-roles"></a>Przypisz role

Aby uzyskać dostęp do danych raportowania za pomocą interfejsu API, musisz mieć jedną z następujących ról, które są przypisane:

- Odczytywanie zabezpieczeń

- Administrator zabezpieczeń

- Administrator globalny




## <a name="register-an-application"></a>Rejestrowanie aplikacji

Musisz zarejestrować aplikację, nawet jeśli uzyskujesz dostęp do interfejsu API raportowania za pomocą skryptu. Daje to **identyfikator aplikacji**, który jest wymagany przez wywołanie autoryzacji i umożliwia swój kod, aby odbierać tokeny.

Aby skonfigurować katalog na dostęp do interfejsu API raportowania usługi Azure AD, musisz zarejestrować się do witryny Azure portal przy użyciu konta administratora platformy Azure, który jest również członkiem **administratora globalnego** rola katalogu w dzierżawie usługi Azure AD.

> [!IMPORTANT]
> Aplikacje działające w ramach poświadczeń z uprawnieniami "Administrator", takich jak to mogą być bardzo skuteczne, więc upewnij się zabezpieczyć poświadczenia Identyfikatora/wpisów tajnych aplikacji.
> 


**Aby zarejestrować aplikację usługi Azure Active Directory:**

1. W [witryny Azure portal](https://portal.azure.com), w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory**.
   
    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Na **usługi Azure Active Directory** kliknij **rejestracje aplikacji**.

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/02.png) 

3. Na **rejestracje aplikacji** w pasku narzędzi u góry, kliknij **rejestrowanie nowej aplikacji**.

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/03.png)

4. Na **Utwórz** strony, wykonaj następujące czynności:

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/04.png)

    a. W **nazwa** polu tekstowym wpisz `Reporting API application`.

    b. Jako **typ aplikacji**, wybierz opcję **aplikacji sieci Web / interfejs API**.

    c. W **adres URL logowania** polu tekstowym wpisz `https://localhost`.

    d. Kliknij pozycję **Utwórz**. 


## <a name="grant-permissions"></a>Udzielenie uprawnień 

W zależności od interfejsu API, którego chcesz uzyskać dostęp musisz przyznać aplikacji następujące uprawnienia:  

| Interfejs API | Uprawnienie |
| --- | --- |
| Windows Azure Active Directory | Odczytuj dane katalogu |
| Microsoft Graph | Odczytywać wszystkie dane dziennika inspekcji |


![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/36.png)


W poniższej sekcji przedstawiono kroki dla obu interfejsów API. Jeśli nie chcesz uzyskać dostęp do jednego z interfejsów API, można pominąć kroki powiązane.
 

**Aby udzielić uprawnień aplikacji do użycia interfejsów API:**

1. Na **rejestracje aplikacji** na liście aplikacji, kliknij polecenie **aplikacji interfejsu API raportowania usługi**.

2. Na **aplikacji interfejsu API raportowania usługi** w pasku narzędzi u góry, kliknij **ustawienia**. 

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

3. Na **ustawienia** kliknij **wymagane uprawnienia**. 

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/06.png)

4. Na **wymagane uprawnienia** strony w **API** kliknij **Windows Azure Active Directory**. 

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/07.png)

5. Na **Włącz dostęp za pomocą** wybierz opcję **Odczyt danych katalogu** i usuń zaznaczenie opcji **Zaloguj się i odczytuj profil użytkownika**. 

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/08.png)

6. Na pasku narzędzi u góry kliknij **Zapisz**.

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/15.png)

7. Na **wymagane uprawnienia** w pasku narzędzi u góry, kliknij **Dodaj**.

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/32.png)

8. Na **dostępu Dodaj interfejs API** kliknij **wybierz interfejs API**.

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/31.png)

9. Na **wybierz interfejs API** kliknij **programu Microsoft Graph**, a następnie kliknij przycisk **wybierz**.

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/33.png)

10. Na **Włącz dostęp za pomocą** wybierz opcję **odczytywać wszystkie dane dziennika inspekcji**, a następnie kliknij przycisk **wybierz**.  

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/34.png)


11. Na **dostępu Dodaj interfejs API** kliknij **gotowe**.  

12. Na **wymagane uprawnienia** strony, na pasku narzędzi u góry. Kliknij przycisk **Udziel uprawnień**, a następnie kliknij przycisk **tak**.

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/17.png)


## <a name="gather-configuration-settings"></a>Zbieranie ustawień konfiguracji 

W tej sekcji dowiesz się, jak uzyskać następujące ustawienia z katalogiem:

- Nazwa domeny
- Identyfikator klienta
- Klucz tajny klienta

Te wartości będą potrzebne podczas konfigurowania wywołania interfejsu API raportowania. 

### <a name="get-your-domain-name"></a>Pobierz nazwę domeny

**Aby uzyskać nazwy domeny:**

1. W [witryny Azure portal](https://portal.azure.com), w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory**.
   
    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Na **usługi Azure Active Directory** kliknij **niestandardowe nazwy domen**.

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/09.png) 

3. Skopiuj nazwę domeny, z listy domen.


### <a name="get-your-applications-client-id"></a>Pobieranie Identyfikatora klienta aplikacji

**Aby uzyskać identyfikator klienta Twojej aplikacji:**

1. W [witryny Azure portal](https://portal.azure.com), w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory**.
   
    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Na **rejestracje aplikacji** na liście aplikacji, kliknij polecenie **aplikacji interfejsu API raportowania usługi**.

3. Na **aplikacji interfejsu API raportowania usługi** strony, u **identyfikator aplikacji**, kliknij przycisk **kliknij, aby skopiować**.

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/11.png) 



### <a name="get-your-applications-client-secret"></a>Pobierz klucz tajny klienta aplikacji
Aby uzyskać klucz tajny klienta aplikacji, musisz utworzyć nowy klucz i zapisać jego wartość po zapisaniu nowego klucza, ponieważ nie jest możliwe do pobierania tej wartości w dalszej części już.

**Aby wyświetlić klucz tajny klienta Twojej aplikacji:**

1. W [witryny Azure portal](https://portal.azure.com), w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory**.
   
    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Na **rejestracje aplikacji** na liście aplikacji, kliknij polecenie **aplikacji interfejsu API raportowania usługi**.


3. Na **aplikacji interfejsu API raportowania usługi** w pasku narzędzi u góry, kliknij **ustawienia**. 

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

4. Na **ustawienia** strony w **dostępu APIR** kliknij **klucze**. 

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/12.png)


5. Na **klucze** strony, wykonaj następujące czynności:

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/14.png)

    a. W **opis** polu tekstowym wpisz `Reporting API`.

    b. Jako **Expires**, wybierz opcję **za 2 lata**.

    c. Kliknij pozycję **Zapisz**.

    d. Skopiuj wartość klucza.


## <a name="next-steps"></a>Kolejne kroki

* [Pobieranie danych przy użyciu usługi Azure Active Directory, interfejsu API raportowania przy użyciu certyfikatów](active-directory-reporting-api-with-certificates.md)
* [Pierwsze wrażenie dotyczące interfejsów API raportowania](active-directory-reporting-api-getting-started-azure-portal.md)
* [Dokumentacja interfejsu API inspekcji](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Raport aktywności logowania, dokumentacja interfejsu API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
