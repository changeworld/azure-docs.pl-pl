---
title: Wymagania wstępne dotyczące dostęp do usługi Azure Active Directory raportowania interfejsu API | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o wymaganiach wstępnych można uzyskać dostępu do raportowania interfejsu API usługi Azure AD
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
ms.openlocfilehash: 6d842b1af74c1b276f367e0ff15703880f7560aa
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36224790"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Wymagania wstępne dotyczące dostęp do usługi Azure Active Directory raportowania interfejsu API

[Interfejsy API raportowania usługi Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) umożliwiają dostęp programowy do danych za pomocą zestawu interfejsów API opartych na architekturze REST. Te interfejsy API można wywoływać przy użyciu różnych języków i narzędzi do programowania.

Raportowania używa interfejsu API [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) do autoryzacji dostępu do interfejsów API sieci web.

Aby przygotować dostęp do interfejsu API raportowania, należy:

1. Przypisz role
2. Rejestrowanie aplikacji
3. Udzielenie uprawnień
4. Zbierz ustawienia konfiguracji



## <a name="assign-roles"></a>Przypisz role

Aby uzyskać dostęp do danych raportowania za pośrednictwem interfejsu API, należy mieć jeden z następujących ról przypisane:

- Odczytywanie zabezpieczeń

- Administrator zabezpieczeń

- Administrator globalny




## <a name="register-an-application"></a>Rejestrowanie aplikacji

Należy zarejestrować aplikację, nawet jeśli uzyskujesz dostęp do interfejsu API raportowania przy użyciu skryptu. Zapewnia to **identyfikator aplikacji**, który jest wymagany przez wywołanie autoryzacji i umożliwia swój kod, aby odbierać tokeny.

Aby skonfigurować dostęp raportowania interfejsu API usługi Azure AD do katalogu, należy zalogować się do portalu Azure przy użyciu konta administratora platformy Azure, który jest również członkiem **administratora globalnego** roli katalogu w dzierżawie usługi Azure AD.

> [!IMPORTANT]
> Aplikacje uruchomione w ramach poświadczeń z uprawnieniami "Administrator", jak mogą być bardzo zaawansowane, więc Pamiętaj zapewnić bezpieczeństwo poświadczeń identyfikator/klucz tajny aplikacji.
> 


**Aby zarejestrować aplikację usługi Azure Active Directory:**

1. W [portalu Azure](https://portal.azure.com), w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory**.
   
    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Na **usługi Azure Active Directory** kliknij przycisk **rejestracji aplikacji**.

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/02.png) 

3. Na **rejestracji aplikacji** na pasku narzędzi u góry, kliknij przycisk **nowej rejestracji aplikacji**.

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/03.png)

4. Na **Utwórz** wykonaj następujące czynności:

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/04.png)

    a. W **nazwa** pole tekstowe, typ `Reporting API application`.

    b. Jako **typu aplikacji**, wybierz pozycję **aplikacji sieci Web / interfejs API**.

    c. W **adres URL logowania** pole tekstowe, typ `https://localhost`.

    d. Kliknij przycisk **Utwórz**. 


## <a name="grant-permissions"></a>Udzielenie uprawnień 

W zależności od interfejsu API, który chcesz uzyskać dostęp musisz przyznać aplikacji następujące uprawnienia:  

| Interfejs API | Uprawnienie |
| --- | --- |
| Windows Azure Active Directory | Odczytaj dane katalogu |
| Microsoft Graph | Odczytać wszystkie inspekcji dane dziennika |


![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/36.png)


W poniższej sekcji przedstawiono kroki dla obu interfejsów API. Jeśli nie chcesz uzyskać dostępu do jednego z interfejsów API, można pominąć kroki pokrewne.
 

**Aby udzielić uprawnień aplikacji do użycia interfejsów API:**

1. Na **rejestracji aplikacji** na liście aplikacji, kliknij przycisk **aplikacji interfejsu API raportowania**.

2. Na **aplikacji interfejsu API raportowania** na pasku narzędzi u góry, kliknij przycisk **ustawienia**. 

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

3. Na **ustawienia** kliknij przycisk **wymagane uprawnienia**. 

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/06.png)

4. Na **wymagane uprawnienia** strony w **interfejsu API** kliknij **Windows Azure Active Directory**. 

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/07.png)

5. Na **Włącz dostęp** wybierz pozycję **odczytuj dane katalogu** i usuń zaznaczenie **Zaloguj się i odczytuj profil użytkownika**. 

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/08.png)

6. Na pasku narzędzi u góry kliknij **zapisać**.

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/15.png)

7. Na **wymagane uprawnienia** na pasku narzędzi u góry, kliknij przycisk **Dodaj**.

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/32.png)

8. Na **dostępu Dodaj interfejsu API** kliknij przycisk **wybierz interfejs API**.

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/31.png)

9. Na **wybierz interfejs API** kliknij przycisk **Microsoft Graph**, a następnie kliknij przycisk **wybierz**.

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/33.png)

10. Na **Włącz dostęp** wybierz pozycję **odczytać wszystkich danych dziennika inspekcji,**, a następnie kliknij przycisk **wybierz**.  

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/34.png)


11. Na **dostępu Dodaj interfejsu API** kliknij przycisk **gotowe**.  

12. Na **wymagane uprawnienia** strony na pasku narzędzi u góry. Kliknij przycisk **udzielanie uprawnień**, a następnie kliknij przycisk **tak**.

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/17.png)


## <a name="gather-configuration-settings"></a>Zbierz ustawienia konfiguracji 

W tej sekcji przedstawiono sposób uzyskać następujące ustawienia z katalogiem:

- Nazwa domeny
- Identyfikator klienta
- Klucz tajny klienta

Te wartości są wymagane podczas konfigurowania wywołania interfejsu API raportowania. 

### <a name="get-your-domain-name"></a>Pobierz nazwę domeny

**Aby uzyskać nazwy domeny:**

1. W [portalu Azure](https://portal.azure.com), w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory**.
   
    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Na **usługi Azure Active Directory** kliknij przycisk **niestandardowych nazw domen**.

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/09.png) 

3. Skopiuj nazwę domeny, z listy domen.


### <a name="get-your-applications-client-id"></a>Pobieranie Identyfikatora klienta aplikacji

**Aby uzyskać identyfikator klienta aplikacji:**

1. W [portalu Azure](https://portal.azure.com), w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory**.
   
    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Na **rejestracji aplikacji** na liście aplikacji, kliknij przycisk **aplikacji interfejsu API raportowania**.

3. Na **aplikacji interfejsu API raportowania** strony, na **identyfikator aplikacji**, kliknij przycisk **kliknij, aby skopiować**.

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/11.png) 



### <a name="get-your-applications-client-secret"></a>Pobierz klucz tajny klienta aplikacji
Aby uzyskać klucz tajny klienta aplikacji, musisz utworzyć nowy klucz i zapisać jego wartości podczas zapisywania nowego klucza, ponieważ nie jest możliwe później już pobierania tej wartości.

**Aby uzyskać klucz tajny klienta aplikacji:**

1. W [portalu Azure](https://portal.azure.com), w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory**.
   
    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Na **rejestracji aplikacji** na liście aplikacji, kliknij przycisk **aplikacji interfejsu API raportowania**.


3. Na **aplikacji interfejsu API raportowania** na pasku narzędzi u góry, kliknij przycisk **ustawienia**. 

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

4. Na **ustawienia** strony w **dostępu APIR** kliknij **klucze**. 

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/12.png)


5. Na **klucze** wykonaj następujące czynności:

    ![Zarejestruj aplikację](./media/active-directory-reporting-api-prerequisites-azure-portal/14.png)

    a. W **opis** pole tekstowe, typ `Reporting API`.

    b. Jako **Expires**, wybierz pozycję **w 2 lata**.

    c. Kliknij pozycję **Zapisz**.

    d. Skopiuj wartość klucza.


## <a name="next-steps"></a>Następne kroki

- [Pobierz dane przy użyciu usługi Azure Active Directory raportowania interfejsu API z certyfikatami](active-directory-reporting-api-with-certificates.md).

- [Pierwsze wrażenie dotyczące interfejsów API raportowania](active-directory-reporting-api-getting-started-azure-portal.md#explore)

- [Tworzenie własnego rozwiązania](active-directory-reporting-api-getting-started-azure-portal.md#customize)

