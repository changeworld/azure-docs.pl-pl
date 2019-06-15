---
title: Włączanie pilotażu usługi Azure Multi-Factor Authentication
description: W tym samouczku włączysz usługę Azure AD Multi-Factor Authentication dla pilotażowej grupy użytkowników
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: a229eef3d49964e71becf066d67f60b18da6fc18
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67113238"
---
# <a name="tutorial-complete-an-azure-multi-factor-authentication-pilot-roll-out"></a>Samouczek: przeprowadzanie pilotażowego wdrożenia usługi Azure Multi-Factor Authentication

W tym samouczku możesz opisano konfigurowanie zasad dostępu warunkowego, włączania usługi Azure Multi-Factor Authentication (Azure MFA), podczas logowania się w witrynie Azure Portal. Zasady są wdrażane i testowane dla określonej grupy użytkowników pilotażowych. Wdrożenia usługi Azure MFA przy użyciu dostępu warunkowego można swobodnie istotne dla organizacji i Administratorzy w porównaniu do tradycyjnych metod wymuszone.

> [!div class="checklist"]
> * Włączanie usługi Azure Multi-Factor Authentication
> * Testowanie usługi Azure Multi-Factor Authentication

## <a name="prerequisites"></a>Wymagania wstępne

* Działająca dzierżawa usługi Azure AD z włączoną co najmniej próbną wersją licencji.
* Konto z uprawnieniami administratora globalnego.
* Testowe konto użytkownika niebędącego administratorem i znane hasło do tego konta na potrzeby testowania. Jeśli chcesz utworzyć użytkownika, zobacz artykuł [Szybki start: Dodawanie nowych użytkowników do usługi Azure Active Directory](../add-users-azure-active-directory.md).
* Grupa pilotażowa na potrzeby testów z użytkownikiem niebędącym administratorem jako jej członkiem. Jeśli chcesz utworzyć grupę, zobacz artykuł [Tworzenie grupy i dodawanie do niej członków w usłudze Azure Active Directory](../active-directory-groups-create-azure-portal.md).

## <a name="enable-azure-multi-factor-authentication"></a>Włączanie usługi Azure Multi-Factor Authentication

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego.
1. Przejdź do **usługi Azure Active Directory**, **dostępu warunkowego**
1. Wybierz pozycję **Nowe zasady**
1. Nadaj swoim zasadom nazwę **Pilotaż MFA**
1. W obszarze **użytkownicy i grupy** wybierz przycisk radiowy **Wybieranie użytkowników i grup**
    * Wybierz grupę pilotażową utworzoną w sekcji dotyczącej wymagań wstępnych w tym artykule
    * Kliknij przycisk **Gotowe**
1. W obszarze **Aplikacje w chmurze** wybierz przycisk radiowy **Wybierz aplikacje**
    * Aplikacją w chmurze dla witryny Azure Portal jest **Microsoft Azure Management**
    * Kliknij pozycję **Wybierz**
    * Kliknij przycisk **Gotowe**
1. Pomiń sekcję **Warunki**
1. W obszarze **Udzielanie** upewnij się, że zaznaczono przycisk radiowy **Udzielanie dostępu**
    * Pole wyboru dla opcji **Wymagaj uwierzytelniania wieloskładnikowego**
    * Kliknij pozycję **Wybierz**
1. Pomiń sekcję **Sesja**
1. Ustaw przełącznik **Włącz zasady** na pozycji **Włączone**
1. Kliknij przycisk **Utwórz**.

## <a name="test-azure-multi-factor-authentication"></a>Testowanie usługi Azure Multi-Factor Authentication

Aby potwierdzić, że zasady dostępu warunkowego działa, możesz przetestować, logując się do zasobu, który nie należy wymagać uwierzytelniania Wieloskładnikowego, a następnie do witryny Azure portal, który wymaga uwierzytelniania Wieloskładnikowego.

1. Otwórz nowe okno przeglądarki w trybie incognito lub InPrivate i przejdź do adresu [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com).
   * Zaloguj się za pomocą konta użytkownika testowego utworzonego w sekcji wymagań wstępnych w tym artykule i zwróć uwagę, czy nie jest wymagane uwierzytelnianie wieloskładnikowe.
   * Zamknij okno przeglądarki.
2. Otwórz nowe okno przeglądarki w trybie incognito lub InPrivate i przejdź do adresu [https://portal.azure.com](https://portal.azure.com).
   * Zaloguj się za pomocą konta użytkownika testowego utworzonego w sekcji wymagań wstępnych w tym artykule i zwróć uwagę, że teraz powinna być wymagana rejestracja w usłudze Azure Multi-Factor Authentication oraz jej użycie.
   * Zamknij okno przeglądarki.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zdecydujesz, że nie chcesz już korzystać z funkcji skonfigurowanych w ramach tego samouczka, wprowadź następujące zmiany.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do **usługi Azure Active Directory**, **dostępu warunkowego**.
1. Wybierz zasady dostępu warunkowego, który został utworzony.
1. Kliknij przycisk **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku włączono usługę Azure Multi-Factor Authentication. Przejdź do następnego samouczka, aby zobaczyć, jak można zintegrować usługę Azure Identity Protection ze środowiskami samoobsługowego resetowania haseł i uwierzytelniania wieloskładnikowego.

> [!div class="nextstepaction"]
> [Ocena ryzyka podczas logowania](tutorial-risk-based-sspr-mfa.md)
