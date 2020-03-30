---
title: Dodawanie użytkowników współpracy B2B jako pracownika informacji — usługa Azure AD
description: Współpraca B2B umożliwia pracownikom informacji i właścicielom aplikacji dodawanie użytkowników-gości do usługi Azure AD w celu uzyskania dostępu | Dokumenty firmy Microsoft
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: abb5c6939d8c88db35a776aa8f2c075a4bdcc609
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77565421"
---
# <a name="how-users-in-your-organization-can-invite-guest-users-to-an-app"></a>Jak użytkownicy w organizacji mogą zapraszać użytkowników-gości do aplikacji

Po dodaniu użytkownika-gościa do katalogu w usłudze Azure AD właściciel aplikacji może wysłać użytkownikowi-gościowi bezpośrednie łącze do aplikacji, którą chcesz udostępnić. Administratorzy usługi Azure AD mogą również skonfigurować samoobsługowe zarządzanie dla aplikacji opartych na galerii lub SAML w dzierżawie usługi Azure AD. W ten sposób właściciele aplikacji mogą zarządzać własnymi użytkownikami-gośćmi, nawet jeśli użytkownicy-goście nie zostali jeszcze dodana do katalogu. Gdy aplikacja jest skonfigurowana do samoobsługi, właściciel aplikacji używa panelu dostępu, aby zaprosić użytkownika-gościa do aplikacji lub dodać użytkownika-gościa do grupy, która ma dostęp do aplikacji. Samoobsługowe zarządzanie aplikacjami dla galerii i aplikacji opartych na SAML wymaga wstępnej konfiguracji przez administratora. Poniżej przedstawiono podsumowanie kroków konfiguracji (aby uzyskać bardziej szczegółowe instrukcje, zobacz [Wymagania wstępne](#prerequisites) później na tej stronie):

 - Włączanie zarządzania grupami samoobsługowymi dla dzierżawy
 - Tworzenie grupy do przypisania do aplikacji i uczynienie go właścicielem
 - Konfigurowanie aplikacji do samoobsługi i przypisywanie grupy do aplikacji

> [!NOTE]
> W tym artykule opisano sposób konfigurowania zarządzania samoobsługowego dla galerii i aplikacji opartych na SAML, które zostały dodane do dzierżawy usługi Azure AD. Można również [skonfigurować samoobsługowe grupy usługi Office 365,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) aby użytkownicy mogli zarządzać dostępem do własnych grup usługi Office 365. Aby uzyskać więcej sposobów udostępniania plików i aplikacji pakietu Office użytkownikom-gościom, zobacz [Dostęp gościa w grupach usługi Office 365](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6) oraz [pliki lub foldery programu SharePoint programu SharePoint](https://support.office.com/article/share-sharepoint-files-or-folders-1fe37332-0f9a-4719-970e-d2578da4941c).

## <a name="invite-a-guest-user-to-an-app-from-the-access-panel"></a>Zapraszanie użytkownika-gościa do aplikacji z Panelu dostępu

Po skonfigurowaniu aplikacji do samoobsługi właściciele aplikacji mogą użyć własnego panelu dostępu, aby zaprosić użytkownika-gościa do aplikacji, którą chcą udostępnić. Użytkownik-gość nie musi być dodawany do usługi Azure AD z wyprzedzeniem. 

1. Otwórz panel dostępu, `https://myapps.microsoft.com`przechodząc do pliku .
2. Wskaż aplikację, wybierz elipsy (**...**), a następnie wybierz pozycję **Zarządzaj aplikacją**.
 
   ![Zrzut ekranu przedstawiający podmenu Zarządzanie aplikacją dla aplikacji Salesforce](media/add-users-iw/access-panel-manage-app.png)
 
3. U góry listy użytkowników wybierz **+** pozycję .
   
   ![Zrzut ekranu przedstawiający symbol plusa dodawania członków do aplikacji](media/add-users-iw/access-panel-manage-app-add-user.png)
   
4. W polu wyszukiwania **Dodaj członków** wpisz adres e-mail użytkownika-gościa. Opcjonalnie możesz dodać komunikat powitalny.
   
   ![Zrzut ekranu przedstawiający okno Dodawanie członków do dodawania gościa](media/add-users-iw/access-panel-invitation.png)
   
5. Wybierz **pozycję Dodaj,** aby wysłać zaproszenie do użytkownika-gościa. Po wysłaniu zaproszenia konto użytkownika zostanie automatycznie dodane do katalogu jako gość.

## <a name="invite-someone-to-join-a-group-that-has-access-to-the-app"></a>Zapraszanie innej osoby do dołączenia do grupy, która ma dostęp do aplikacji
Po skonfigurowaniu aplikacji do samoobsługi właściciele aplikacji mogą zapraszać użytkowników-gości do grup, którymi zarządzają, które mają dostęp do aplikacji, które mają być udostępniane. Użytkownicy-goście nie muszą już istnieć w katalogu. Właściciel aplikacji wykonuje następujące kroki, aby zaprosić użytkownika-gościa do grupy, aby mógł uzyskać dostęp do aplikacji.

1. Upewnij się, że jesteś właścicielem grupy samoobsługowej, która ma dostęp do aplikacji, którą chcesz udostępnić.
2. Otwórz panel dostępu, `https://myapps.microsoft.com`przechodząc do pliku .
3. Wybierz aplikację **Grupy.**
   
   ![Zrzut ekranu przedstawiający aplikację Grupy w Panelu dostępu](media/add-users-iw/access-panel-groups.png)
   
4. W obszarze **Grupy,** których jestem właścicielem wybierz grupę, która ma dostęp do aplikacji, którą chcesz udostępnić.
   
   ![Zrzut ekranu przedstawiający, gdzie wybrać grupę w grupach, których jestem właścicielem](media/add-users-iw/access-panel-groups-i-own.png)
   
5. U góry listy członków grupy **+** wybierz opcję .
   
   ![Zrzut ekranu przedstawiający symbol plusa dodawania członków do grupy](media/add-users-iw/access-panel-groups-add-member.png)
   
6. W polu wyszukiwania **Dodaj członków** wpisz adres e-mail użytkownika-gościa. Opcjonalnie możesz dodać komunikat powitalny.
   
   ![Zrzut ekranu przedstawiający okno Dodawanie członków do dodawania gościa](media/add-users-iw/access-panel-invitation.png)
   
7. Wybierz **pozycję Dodaj,** aby automatycznie wysłać zaproszenie do użytkownika-gościa. Po wysłaniu zaproszenia konto użytkownika zostanie automatycznie dodane do katalogu jako gość.


## <a name="prerequisites"></a>Wymagania wstępne

Samoobsługowe zarządzanie aplikacjami wymaga wstępnej konfiguracji przez administratora globalnego i administratora usługi Azure AD. W ramach tej konfiguracji skonfigurujesz aplikację do samoobsługi i przypiszesz grupę do aplikacji, którą może zarządzać właściciel aplikacji. Można również skonfigurować grupę, aby umożliwić każdemu żądanie członkostwa, ale wymaga zgody właściciela grupy. (Dowiedz się więcej o [zarządzaniu grupami samoobsługowymi).](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) 

> [!NOTE]
> Nie można dodawać użytkowników-gości do grupy dynamicznej ani do grupy synchronizowanej z lokalną usługą Active Directory.

### <a name="enable-self-service-group-management-for-your-tenant"></a>Włączanie zarządzania grupami samoobsługowymi dla dzierżawy
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator globalny.
2. W panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.
3. Wybierz **opcję Grupy**.
4. W obszarze **Ustawienia**wybierz pozycję **Ogólne**.
5. W obszarze **Samoobsługowe zarządzanie grupami**obok **pozycji Właściciele mogą zarządzać żądaniami członkostwa w grupach w Panelu dostępu**wybierz pozycję **Tak**.
6. Wybierz **pozycję Zapisz**.

### <a name="create-a-group-to-assign-to-the-app-and-make-the-user-an-owner"></a>Tworzenie grupy do przypisania do aplikacji i uczynienie go właścicielem
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator usługi Azure AD lub administrator globalny.
2. W panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.
3. Wybierz **opcję Grupy**.
4. Wybierz **pozycję Nowa grupa**.
5. W obszarze **Typ grupy**wybierz pozycję **Zabezpieczenia**.
6. Wpisz odpowiedni tekst w polach **Nazwa grupy** i **Opis grupy**.
7. W obszarze **Typ członkostwa**wybierz pozycję **Przypisane**.
8. Wybierz **pozycję Utwórz**i zamknij stronę **Grupa.**
9. Na stronie **Grupy — wszystkie grupy** otwórz grupę. 
10. W obszarze **Zarządzaj**wybierz pozycję **Właściciele** > **Dodaj właścicieli**. Wyszukaj użytkownika, który powinien zarządzać dostępem do aplikacji. Wybierz użytkownika, a następnie kliknij przycisk **Wybierz**.

### <a name="configure-the-app-for-self-service-and-assign-the-group-to-the-app"></a>Konfigurowanie aplikacji do samoobsługi i przypisywanie grupy do aplikacji
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator usługi Azure AD lub administrator globalny.
2. W okienku nawigacji wybierz pozycję **Azure Active Directory**.
3. W obszarze **Zarządzanie**wybierz pozycję **Aplikacje przedsiębiorstwa** > **Wszystkie aplikacje**.
4. Na liście aplikacji znajdź i otwórz aplikację.
5. W obszarze **Zarządzaj**wybierz pozycję **Logowanie jednokrotne**i skonfiguruj aplikację do logowania jednokrotnego. (Aby uzyskać szczegółowe informacje, [zobacz, jak zarządzać logowaniem jednokrotnym dla aplikacji dla przedsiębiorstw).)](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications)
6. W obszarze **Zarządzaj**wybierz pozycję **Samoobsługa**i skonfiguruj dostęp do aplikacji samoobsługowych. (Aby uzyskać szczegółowe informacje, [zobacz, jak korzystać z dostępu do aplikacji samoobsługowej](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to).) 

    > [!NOTE]
    > Dla ustawienia **Do której grupy należy dodać przypisanych użytkowników?** wybierz grupę utworzoną w poprzedniej sekcji.
7. W obszarze **Zarządzaj**wybierz **pozycję Użytkownicy i grupy**i sprawdź, czy utworzona grupa samoobsługowa jest wyświetlana na liście.
8. Aby dodać aplikację do Panelu dostępu właściciela grupy, wybierz pozycję **Dodaj użytkowników** > **i grupy**. Wyszukaj właściciela grupy i wybierz użytkownika, kliknij przycisk **Wybierz**, a następnie kliknij pozycję **Przypisz,** aby dodać użytkownika do aplikacji.

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły dotyczące współpracy usługi Azure AD B2B:

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Jak administratorzy usługi Azure Active Directory dodawać użytkowników współpracy B2B?](add-users-administrator.md)
- [Wykup zaproszenia do współpracy B2B](redemption-experience.md)
- [Licencjonowanie funkcji współpracy B2B w usłudze Azure AD](licensing-guidance.md)
