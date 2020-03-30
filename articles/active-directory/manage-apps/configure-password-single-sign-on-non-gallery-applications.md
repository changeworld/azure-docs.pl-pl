---
title: Jak skonfigurować logowanie jednokrotne hasła dla aplikacji usługi Azure AD | Dokumenty firmy Microsoft
description: Jak skonfigurować logowanie jednokrotne (Logowanie jednokrotne) do aplikacji dla przedsiębiorstw usługi Azure AD na platformie tożsamości firmy Microsoft (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 563bda275b73f76b042b5e57a9909ca78c504bb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063530"
---
# <a name="configure-password-single-sign-on"></a>Konfigurowanie logowania jednokrotnego hasła

Po [dodaniu aplikacji galerii](add-gallery-app.md) lub [aplikacji sieci web spoza galerii](add-non-gallery-app.md) do aplikacji platformy Azure AD Enterprise, jedną z dostępnych opcji logowania jednokrotnego jest [logowanie jednookie](what-is-single-sign-on.md#password-based-sso). Ta opcja jest dostępna dla każdej sieci Web ze stroną logowania HTML. Logowanie logowania opartego na hasłach, nazywane również magazynowanie haseł, umożliwia zarządzanie dostępem użytkowników i hasłami do aplikacji sieci web, które nie obsługują federacji tożsamości. Jest to również przydatne w scenariuszach, w których kilku użytkowników musi udostępnić jedno konto, na przykład na kontach aplikacji mediów społecznościowych w organizacji. 

Logowanie logowanie oparte na hasłach to świetny sposób na szybkie rozpoczęcie integracji aplikacji z usługą Azure AD i umożliwia:

-   Włącz **logowanie jednokrotne dla użytkowników,** bezpiecznie przechowując i odtwarzając nazwy użytkowników i hasła dla aplikacji zintegrowanej z usługą Azure AD

-   **Obsługa aplikacji, które wymagają wielu pól logowania** dla aplikacji, które wymagają więcej niż tylko pól nazwy użytkownika i hasła do logowania

-   **Dostosowywanie etykiet** pól wprowadzania nazwy użytkownika i hasła, które użytkownicy widzą w [Panelu dostępu do aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) po wprowadzeniu poświadczeń

-   Zezwalaj **użytkownikom** na podanie własnych nazw użytkowników i haseł dla istniejących kont aplikacji, które wpisują ręcznie w [Panelu dostępu do aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Zezwalaj **członkowi grupy biznesowej na** określanie nazw użytkowników i haseł przypisanych do użytkownika przy użyciu funkcji Dostępu do aplikacji [samoobsługowej](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)

-   Zezwalaj **administratorowi** na określanie nazwy użytkownika i hasła, które mają być używane przez osoby lub grupy podczas logowania się do aplikacji przy użyciu funkcji Aktualizuj poświadczenia 

## <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli aplikacja nie została dodana do dzierżawy usługi Azure AD, zobacz [Dodawanie aplikacji galerii](add-gallery-app.md) lub [Dodawanie aplikacji spoza galerii.](add-non-gallery-app.md)

## <a name="open-the-app-and-select-password-single-sign-on"></a>Otwórz aplikację i wybierz logowanie jednokrotne hasła

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator aplikacji w chmurze lub administrator aplikacji dla dzierżawy usługi Azure AD.

2. Przejdź do **aplikacji Azure Active Directory** > **Enterprise**. Pojawi się losowa próbka aplikacji w dzierżawie usługi Azure AD. 

3. W menu **Typ aplikacji** wybierz polecenie **Wszystkie aplikacje**, a następnie wybierz polecenie **Zastosuj**.

4. Wprowadź nazwę aplikacji w polu wyszukiwania, a następnie wybierz aplikację z wyników.

5. W sekcji **Zarządzanie** wybierz pozycję **Logowanie jednokrotne**. 

6. Wybierz **opcję Oparte na hasłach**.

7. Wprowadź adres URL strony logowania aplikacji opartej na sieci Web. Ten ciąg musi być stroną zawierającą pole wprowadzania nazwy użytkownika.

   ![Logowanie jednookrotne oparte na hasłach](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

8. Wybierz **pozycję Zapisz**. Usługa Azure AD próbuje przeanalizować stronę logowania pod kątem wprowadzania danych dotyczących nazwy użytkownika i wprowadzania hasła. Jeśli próba zakończy się pomyślnie, gotowe. 
 
> [!NOTE]
> Następnym krokiem jest [przypisanie użytkowników lub grup do aplikacji](methods-for-assigning-users-and-groups.md). Po przypisaniu użytkowników i grup można podać poświadczenia, które mają być używane w imieniu użytkownika podczas logowania się do aplikacji. Wybierz **pozycję Użytkownicy i grupy**, zaznacz pole wyboru dla wiersza użytkownika lub grupy, a następnie kliknij pozycję Aktualizuj **poświadczenia**. Następnie wprowadź nazwę użytkownika i hasło, które mają być używane w imieniu użytkownika lub grupy. W przeciwnym razie użytkownicy zostaną poproszeni o wprowadzenie poświadczeń po uruchomieniu.
 

## <a name="manual-configuration"></a>Konfiguracja ręczna

Jeśli próba analizowania usługi Azure AD nie powiedzie się, można skonfigurować logowanie ręcznie.

1. W obszarze ** \<Nazwa aplikacji> Konfiguracja**wybierz **pozycję \<Konfiguruj nazwę aplikacji> Ustawienia logowania jednokrotnego hasła,** aby wyświetlić stronę **Konfigurowanie logowania.** 

2. Wybierz **pozycję Ręczne wykrywanie pól logowania**. Zostaną wyświetlone dodatkowe instrukcje opisujące ręczne wykrywanie pól logowania.

   ![Ręczna konfiguracja logowania jednokrotnego opartego na hasłach](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. Wybierz **pozycję Przechwyć pola logowania**. Strona stanu przechwytywania zostanie otwarta na nowej karcie z informacją, że **trwa przechwytywanie metadanych**wiadomości.

4. Jeśli na nowej karcie pojawi się pole **Wymagane rozszerzenie panelu dostępu,** wybierz pozycję **Zainstaluj teraz,** aby zainstalować rozszerzenie rozszerzenia **moje aplikacje bezpieczne logowanie.** (Rozszerzenie przeglądarki wymaga przeglądarki Microsoft Edge, Chrome lub Firefox). Następnie zainstaluj, uruchom i włącz rozszerzenie oraz odśwież stronę stanu przechwytywania.

   Rozszerzenie przeglądarki otwiera kolejną kartę wyświetlającej wprowadzony adres URL.
5. Na karcie z wprowadzonym adresem URL przejdź przez proces logowania. Wypełnij pola nazwy użytkownika i hasła i spróbuj się zalogować. (Nie musisz podawać poprawnego hasła).

   Monit z prośbą o zapisanie przechwyconych pól logowania.
6. Kliknij przycisk **OK**. Rozszerzenie przeglądarki aktualizuje stronę stanu przechwytywania z komunikatem **Metadane zostały zaktualizowane dla aplikacji**. Karta przeglądarki zostanie zamknięta.

7. Na stronie **logowania konfigurowania** usługi Azure AD wybierz pozycję **Ok, mogłem pomyślnie zalogować się do aplikacji.**

8. Kliknij przycisk **OK**.

Po przechwyceniu strony logowania można przypisać użytkowników i grupy oraz skonfigurować zasady poświadczeń, podobnie jak zwykłe [aplikacje logowania do logowania](what-is-single-sign-on.md)pod hasłem .

> [!NOTE]
> Możesz przesłać logo kafelka dla aplikacji za pomocą przycisku **Przekaż logo** na karcie **Konfiguruj** aplikację.

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie użytkowników lub grup do aplikacji](methods-for-assigning-users-and-groups.md)
- [Konfigurowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika](../app-provisioning/configure-automatic-user-provisioning-portal.md)
