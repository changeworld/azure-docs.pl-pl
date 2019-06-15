---
title: Dodaj użytkowników we współpracy B2B jako pracownik przetwarzający informacje — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Współpraca B2B umożliwia pracownikom przetwarzającym informacje i właściciele użytkowników-gości można dodawać do usługi Azure AD, aby uzyskać dostęp do | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: e8606a0d4e203e1a910a5cd15ca83a622f5286bd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65812542"
---
# <a name="how-users-in-your-organization-can-invite-guest-users-to-an-app"></a>Jak użytkownicy w organizacji mogą zapraszać gości do aplikacji

Po Gość użytkownik został dodany do katalogu w usłudze Azure AD, właściciel aplikacji może wysłać użytkownik-Gość bezpośredni link do aplikacji, które chcą udostępniać. Administratorzy usługi AD systemu Azure można też skonfigurować Samoobsługowe zarządzanie galerii lub aplikacje oparte na protokole SAML, w ramach ich dzierżawy usługi Azure AD. W ten sposób właścicieli aplikacji można zarządzać ich własnych użytkowników-gości, nawet jeśli użytkownicy-goście nie zostały jeszcze dodane do katalogu. Gdy aplikacja jest skonfigurowana do używania samoobsługowego, właściciel aplikacji używa panelu dostępu zaproszenie użytkownika-gościa do aplikacji lub dodać użytkownika-gościa do grupy, która ma dostęp do aplikacji. Zarządzanie aplikacjami samoobsługi dla galerii i aplikacje oparte na protokole SAML wymaga pewnych początkowej konfiguracji przez administratora. Poniżej przedstawiono podsumowanie kroków instalacji (Aby uzyskać szczegółowe instrukcje, zobacz [wymagania wstępne](#prerequisites) dalej na tej stronie):

 - Włącz samoobsługowe zarządzanie grupami dla Twojej dzierżawy
 - Tworzenie grupy w celu przypisywania do aplikacji i ustawić użytkownika jako właściciela
 - Konfigurowanie aplikacji na potrzeby samoobsługi użytkowników i Przypisz grupę do aplikacji

> [!NOTE]
> W tym artykule opisano sposób konfigurowania samoobsługi zarządzanie za pomocą galerii i aplikacje oparte na protokole SAML, które zostały dodane do dzierżawy usługi Azure AD. Możesz również [Konfigurowanie grup samoobsługi usługi Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) , dzięki czemu użytkownicy mogą zarządzać dostępem do ich własnych grupach usługi Office 365. Aby uzyskać więcej sposobów użytkownicy mogą udostępniać pliki pakietu Office i aplikacji użytkowników-gości, zobacz [dostęp gościa w grupach usługi Office 365](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6) i [SharePoint udostępniania plików lub folderów](https://support.office.com/article/share-sharepoint-files-or-folders-1fe37332-0f9a-4719-970e-d2578da4941c).

## <a name="invite-a-guest-user-to-an-app-from-the-access-panel"></a>Zaproś użytkownika-gościa do aplikacji przy użyciu panelu dostępu

Po skonfigurowaniu aplikacji do samoobsługi właścicieli aplikacji można użyć panelu dostępu, można zaprosić użytkownika-gościa do aplikacji, które chcą udostępniać. Użytkownik-Gość niekoniecznie nie musi zostać dodane do usługi Azure AD z wyprzedzeniem. 

1. Otwórz Panel dostępu, przechodząc do `https://myapps.microsoft.com`.
2. Wskaż aplikacji, wybierz wielokropek ( **...** ), a następnie wybierz pozycję **aplikacji Zarządzanie**.
 
   ![Zrzut ekranu przedstawiający Zarządzanie aplikacji podmenu aplikacji Salesforce](media/add-users-iw/access-panel-manage-app.png)
 
3. W górnej części listy użytkowników, wybierz **+** .
   
   ![Zrzut ekranu przedstawiający symbol znaku plus dotyczące dodawania członków do aplikacji](media/add-users-iw/access-panel-manage-app-add-user.png)
   
4. W **dodawać członków** polu wyszukiwania, wpisz adres e-mail do użytkownika gościa. Opcjonalnie możesz dodać komunikat powitalny.
   
   ![Zrzut ekranu przedstawiający dodawanie członków okno dodawania gościa](media/add-users-iw/access-panel-invitation.png)
   
5. Wybierz **Dodaj** można wysłać zaproszenia do użytkownika gościa. Po wysłaniu zaproszenia konto użytkownika zostanie automatycznie dodane do katalogu jako gość.

## <a name="invite-someone-to-join-a-group-that-has-access-to-the-app"></a>Poproś kogoś dołączenia do grupy, które ma dostęp do aplikacji
Po skonfigurowaniu aplikacji do samoobsługi właścicieli aplikacji można zaprosić użytkowników-gości do grup, którymi zarządzają, które mają dostęp do aplikacji, które chcą udostępniać. Użytkownicy-goście nie trzeba już istnieje w katalogu. Właściciel aplikacji wykonaj poniższe kroki, aby zaprosić użytkownika gościa do grupy, dzięki czemu mogą uzyskiwać dostęp do aplikacji.

1. Upewnij się, że jesteś właścicielem grupy samoobsługi, który ma dostęp do aplikacji, którą chcesz udostępnić.
2. Otwórz Panel dostępu, przechodząc do `https://myapps.microsoft.com`.
3. Wybierz **grup** aplikacji.
   
   ![Zrzut ekranu przedstawiający aplikację grup na panelu dostępu](media/add-users-iw/access-panel-groups.png)
   
4. W obszarze **grup własnym**, wybierz grupę, która ma dostęp do aplikacji, którą chcesz udostępnić.
   
   ![Zrzut ekranu przedstawiający miejsce wybrać grupę, w obszarze grupy jestem właścicielem](media/add-users-iw/access-panel-groups-i-own.png)
   
5. W górnej części listy członków grupy wybierz **+** .
   
   ![Zrzut ekranu przedstawiający symbol znaku plus dotyczące dodawania członków do grupy](media/add-users-iw/access-panel-groups-add-member.png)
   
6. W **dodawać członków** polu wyszukiwania, wpisz adres e-mail do użytkownika gościa. Opcjonalnie możesz dodać komunikat powitalny.
   
   ![Zrzut ekranu przedstawiający dodawanie członków okno dodawania gościa](media/add-users-iw/access-panel-invitation.png)
   
7. Wybierz **Dodaj** można automatycznie wysłać zaproszenia do użytkownika gościa. Po wysłaniu zaproszenia konto użytkownika zostanie automatycznie dodane do katalogu jako gość.


## <a name="prerequisites"></a>Wymagania wstępne

Zarządzanie aplikacjami samoobsługi wymaga pewnych początkowej konfiguracji przez administratora globalnego i administratora usługi Azure AD. W ramach tej instalacji możesz skonfigurować aplikację do samoobsługi i przypisania grupy do aplikacji, która może zarządzać właściciela aplikacji. Można również skonfigurować grupy aby mogła zadzwonić do żądania członkostwa, ale wymagają zatwierdzenia jako właściciela grupy. (Dowiedz się więcej o [Samoobsługowe zarządzanie grupami](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management).) 

> [!NOTE]
> Nie można dodać użytkowników-gości, grupę dynamiczną lub grupy, która jest synchronizowana z usługą Active Directory w środowisku lokalnym.

### <a name="enable-self-service-group-management-for-your-tenant"></a>Włącz samoobsługowe zarządzanie grupami dla Twojej dzierżawy
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako Administrator globalny.
2. W panelu nawigacyjnym wybierz **usługi Azure Active Directory**.
3. Wybierz **grup**.
4. W obszarze **ustawienia**, wybierz opcję **ogólne**.
5. W obszarze **Zarządzanie grupami usługi samoobsługowego**obok pozycji **właściciele mogą zarządzać żądaniami członkostwa grupy w panelu dostępu**, wybierz opcję **tak**.
6. Wybierz pozycję **Zapisz**.

### <a name="create-a-group-to-assign-to-the-app-and-make-the-user-an-owner"></a>Tworzenie grupy w celu przypisywania do aplikacji i ustawić użytkownika jako właściciela
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako Administrator globalny lub administrator usługi Azure AD.
2. W panelu nawigacyjnym wybierz **usługi Azure Active Directory**.
3. Wybierz **grup**.
4. Wybierz **nową grupę**.
5. W obszarze **typ grupy**, wybierz opcję **zabezpieczeń**.
6. Wpisz **Nazwa grupy** i **opis grupy**.
7. W obszarze **Typ członkostwa**, wybierz opcję **przypisane**.
8. Wybierz **Utwórz**i Zamknij **grupy** strony.
9. Na **grupy — wszystkie grupy** strony, otwórz grupę. 
10. W obszarze **Zarządzaj**, wybierz opcję **właścicieli** > **Dodawanie właścicieli**. Wyszukaj użytkownika, który należy zarządzać dostępem do aplikacji. Wybierz użytkownika, a następnie kliknij przycisk **wybierz**.

### <a name="configure-the-app-for-self-service-and-assign-the-group-to-the-app"></a>Konfigurowanie aplikacji na potrzeby samoobsługi użytkowników i Przypisz grupę do aplikacji
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako Administrator globalny lub administrator usługi Azure AD.
2. W okienku nawigacji wybierz **usługi Azure Active Directory**.
3. W obszarze **Zarządzaj**, wybierz opcję **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.
4. Na liście aplikacji należy znaleźć i otworzyć aplikację.
5. W obszarze **Zarządzaj**, wybierz opcję **logowanie jednokrotne**i skonfigurować wniosek o logowanie jednokrotne. (Aby uzyskać więcej informacji, zobacz [jak zarządzać logowania jednokrotnego dla aplikacji korporacyjnych](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal).)
6. W obszarze **Zarządzaj**, wybierz opcję **samoobsługi**i skonfiguruj dostęp do aplikacji Sklep internetowy. (Aby uzyskać więcej informacji, zobacz [sposób używania dostępu do aplikacji Sklep internetowy](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to).) 

    > [!NOTE]
    > Ustawienia **grupę, do której należy dodać przypisanych użytkowników?** wybierz grupę utworzoną w poprzedniej sekcji.
7. W obszarze **Zarządzaj**, wybierz opcję **użytkowników i grup**i sprawdź, czy utworzoną grupę samoobsługi pojawia się na liście.
8. Aby dodać aplikację do panelu dostępu do właściciela grupy, wybierz **Dodaj użytkownika** > **użytkowników i grup**. Wyszukaj właściciela grupy i wybrać użytkownika, kliknij przycisk **wybierz**, a następnie kliknij przycisk **przypisać** dodać użytkownika do aplikacji.

## <a name="next-steps"></a>Kolejne kroki

Na współpracy B2B usługi Azure AD, zobacz następujące artykuły:

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Jak Administratorzy usługi Azure Active Directory dodać użytkowników we współpracy B2B?](add-users-administrator.md)
- [Realizacja zaproszenia współpracy B2B](redemption-experience.md)
- [Licencjonowanie funkcji współpracy B2B w usłudze Azure AD](licensing-guidance.md)
