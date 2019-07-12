---
title: Komunikat o błędzie pojawia się na stronie aplikacji po zalogowaniu | Dokumentacja firmy Microsoft
description: Jak rozwiązywać problemy przy użyciu konta usługi Azure AD, jeśli aplikacja zwróci komunikat o błędzie.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23e6a3d0b533dccc3c3111382b014907d5c026ab
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612663"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>Na stronie aplikacji zawiera komunikat o błędzie po użytkownik loguje się

W tym scenariuszu usługi Azure Active Directory (Azure AD) loguje się użytkownik. Jednak aplikacja wyświetli komunikat o błędzie i nie zezwolić użytkownikom na zakończenie przepływ logowania. Problem polega na tym, że aplikacja nie zaakceptował odpowiedzi, który wystawił usługi Azure AD.

Istnieje kilka możliwych powodów dlaczego aplikacja nie zaakceptował odpowiedzi z usługi Azure AD. Jeśli komunikat o błędzie nie wyraźnie określić, co nie ma odpowiedzi, spróbuj wykonać następujące czynności:

-   Jeśli aplikacja znajduje się w galerii usługi Azure AD, należy sprawdzić, czy wykonano czynności opisane w [sposób debugowania opartej na SAML logowania jednokrotnego do aplikacji w usłudze Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).

-   Użyj narzędzia, takiego jak [Fiddler](https://www.telerik.com/fiddler) do przechwytywania żądania, odpowiedzi i tokenu języka SAML.

-   Czy wysłać odpowiedź SAML dostawcy aplikacji i poproś o tym, czego brakuje.

## <a name="attributes-are-missing-from-the-saml-response"></a>Atrybuty są nieobecne odpowiedzi SAML

Aby dodać atrybut w konfiguracji usługi Azure AD, który zostanie wysłany w odpowiedzi usługa Azure AD, wykonaj następujące kroki:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako administrator globalny lub współadministratora.

2. W górnej części okienka nawigacji po lewej stronie wybierz **wszystkich usług** do otwierania rozszerzenia usługi Azure AD.

3. Typ **usługi Azure Active Directory** w filtru pole wyszukiwania, a następnie wybierz **usługi Azure Active Directory**.

4. Wybierz **aplikacje dla przedsiębiorstw** w okienku nawigacji usługi Azure AD.

5. Wybierz **wszystkie aplikacje** Aby wyświetlić listę aplikacji.

   > [!NOTE]
   > Jeśli nie widzisz aplikacji, które mają używać **filtru** formant w górnej części **listę wszystkich aplikacji**. Ustaw **Pokaż** opcję "Wszystkie aplikacje".

6. Wybierz aplikację, który chcesz skonfigurować logowanie jednokrotne.

7. Po załadowaniu aplikacji wybierz **logowanie jednokrotne** w okienku nawigacji.

8. W **atrybutów użytkownika** zaznacz **Wyświetl i Edytuj wszystkie inne atrybuty użytkownika**. W tym miejscu możesz zmienić atrybuty do wysłania do aplikacji w tokenie języka SAML, gdy użytkownicy logują się.

   Aby dodać atrybut:

   1. Wybierz **Dodaj atrybut**. Wprowadź **nazwa**i wybierz **wartość** z listy rozwijanej.

   1.  Wybierz pozycję **Zapisz**. Zostanie wyświetlony nowy atrybut w tabeli.

9. Zapisz konfigurację.

   Gdy następnym razem użytkownik loguje się do aplikacji, usługi Azure AD będzie wysyłać nowy atrybut w odpowiedzi SAML.

## <a name="the-app-doesnt-identify-the-user"></a>Aplikacja nie identyfikacji użytkownika

Logowanie do aplikacji nie powiedzie się, ponieważ brakuje atrybutu, takiego jak rola w odpowiedzi SAML. Lub zakończy się niepowodzeniem, ponieważ aplikacja oczekuje innego formatu lub wartość **NameID** atrybutu (identyfikator użytkownika).

Jeśli używasz [usługi Azure AD automatyczna aprowizacja użytkowników](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) do tworzenia, obsługi i usuwanie użytkowników w aplikacji, sprawdź, czy użytkownik został aprowizowany do aplikacji SaaS. Aby uzyskać więcej informacji, zobacz [żadni użytkownicy są aprowizowane do aplikacji galerii usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-no-users-provisioned).

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Dodaj atrybut do konfiguracji aplikacji usługi Azure AD

Aby zmienić wartość identyfikatora użytkownika, wykonaj następujące kroki:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako administrator globalny lub współadministratora.

2. Wybierz **wszystkich usług** w górnej części okienka nawigacji po lewej stronie, aby otworzyć rozszerzenia usługi Azure AD.

3. Typ **usługi Azure Active Directory** w filtru pole wyszukiwania, a następnie wybierz **usługi Azure Active Directory**.

4. Wybierz **aplikacje dla przedsiębiorstw** w okienku nawigacji usługi Azure AD.

5. Wybierz **wszystkie aplikacje** Aby wyświetlić listę aplikacji.

   > [!NOTE]
   > Jeśli nie widzisz aplikacji, które mają używać **filtru** formant w górnej części **listę wszystkich aplikacji**. Ustaw **Pokaż** opcję "Wszystkie aplikacje".

6. Wybierz aplikację, którą chcesz skonfigurować na potrzeby logowania jednokrotnego.

7. Po załadowaniu aplikacji wybierz **logowanie jednokrotne** w okienku nawigacji.

8. W obszarze **atrybutów użytkownika**, wybierz Unikatowy identyfikator dla użytkownika z **identyfikator użytkownika** listy rozwijanej.

## <a name="change-the-nameid-format"></a>Zmień format identyfikatora NameID

Jeśli aplikacja oczekuje na inny format **NameID** atrybutu (identyfikator użytkownika), zobacz [nameID edycji](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#editing-nameid) zmienić format identyfikatora NameID.

Usługa Azure AD wybiera format **NameID** atrybutu (identyfikator użytkownika) na podstawie wartości, którą wybrano lub formatu, które są wymagane przez aplikację w SAML AuthRequest. Aby uzyskać więcej informacji, zobacz sekcję "NameIDPolicy" [pojedynczego logowania jednokrotnego protokołu SAML](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol#nameidpolicy).

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>Aplikacja oczekuje, że inny podpis metody dla odpowiedzi SAML

Aby zmienić, które części tokenu SAML są podpisane cyfrowo przez usługę Azure AD, wykonaj następujące kroki:

1. Otwórz [witryny Azure portal](https://portal.azure.com/) i zaloguj się jako administrator globalny lub współadministratora.

2. Wybierz **wszystkich usług** w górnej części okienka nawigacji po lewej stronie, aby otworzyć rozszerzenia usługi Azure AD.

3. Typ **usługi Azure Active Directory** w filtru pole wyszukiwania, a następnie wybierz **usługi Azure Active Directory**.

4. Wybierz **aplikacje dla przedsiębiorstw** w okienku nawigacji usługi Azure AD.

5. Wybierz **wszystkie aplikacje** Aby wyświetlić listę aplikacji.

   > [!NOTE]
   > Jeśli nie widzisz aplikacji, które chcesz użyć **filtru** formant w górnej części **listę wszystkich aplikacji**. Ustaw **Pokaż** opcję "Wszystkie aplikacje".

6. Wybierz aplikację, który chcesz skonfigurować logowanie jednokrotne.

7. Po załadowaniu aplikacji wybierz **logowanie jednokrotne** w okienku nawigacji.

8. W obszarze **certyfikat podpisywania SAML**, wybierz opcję **Pokaż zaawansowane ustawienia podpisywania certyfikatów**.

9. Wybierz **opcja podpisywania** który oczekuje, że aplikacja spośród tych opcji:

   * **Podpisz odpowiedź SAML**
   * **Zaloguj się odpowiedź i potwierdzenie SAML**
   * **Podpisz potwierdzenie SAML**

   Gdy następnym razem użytkownik loguje się do aplikacji, usługi Azure AD podpisze części wybranej odpowiedzi SAML.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>Aplikacja oczekuje, że algorytm podpisywania SHA-1

Domyślnie usługa Azure AD podpisuje SAML token przy użyciu algorytmu najbardziej bezpieczna. Zaleca się, że nie zmieniaj algorytm podpisywania, który ma być *SHA-1* chyba, że aplikacja wymaga SHA-1.

Aby zmienić algorytm podpisywania, wykonaj następujące kroki:

1. Otwórz [witryny Azure portal](https://portal.azure.com/) i zaloguj się jako administrator globalny lub współadministratora.

2. Wybierz **wszystkich usług** w górnej części okienka nawigacji po lewej stronie, aby otworzyć rozszerzenia usługi Azure AD.

3. Typ **usługi Azure Active Directory** w filtru pole wyszukiwania, a następnie wybierz **usługi Azure Active Directory**.

4. Wybierz **aplikacje dla przedsiębiorstw** w okienku nawigacji usługi Azure AD.

5. Wybierz **wszystkie aplikacje** Aby wyświetlić listę aplikacji.

   > [!NOTE]
   > Jeśli nie widzisz aplikacji, które chcesz użyć **filtru** formant w górnej części **listę wszystkich aplikacji**. Ustaw **Pokaż** opcję "Wszystkie aplikacje".

6. Wybierz aplikację, który chcesz skonfigurować logowanie jednokrotne.

7. Po załadowaniu aplikacji wybierz **logowanie jednokrotne** z okienka nawigacji po lewej stronie aplikacji.

8. W obszarze **certyfikat podpisywania SAML**, wybierz opcję **Pokaż zaawansowane ustawienia podpisywania certyfikatów**.

9. Wybierz **algorytmu SHA-1** jako **algorytm podpisywania**.

   Gdy następnym razem użytkownik loguje się do aplikacji, usługi Azure AD podpisze tokenu SAML przy użyciu algorytmu SHA-1.

## <a name="next-steps"></a>Kolejne kroki
[Jak debugować opartej na SAML logowania jednokrotnego do aplikacji w usłudze Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).
