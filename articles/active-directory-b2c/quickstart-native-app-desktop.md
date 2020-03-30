---
title: 'Szybki start: konfigurowanie logowania do aplikacji klasycznej'
titleSuffix: Azure AD B2C
description: W tym przewodniku Szybki start uruchom przykładową aplikację klasyczną WPF, która używa usługi Azure Active Directory B2C w celu zapewnienia logowania konta.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/12/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ebed2f5e8664bd4336219f9387b8d27c8f3a1c59
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78187308"
---
# <a name="quickstart-set-up-sign-in-for-a-desktop-app-using-azure-active-directory-b2c"></a>Szybki start: konfigurowanie logowania dla aplikacji klasycznej przy użyciu usługi Azure Active Directory B2C

Usługa Azure Active Directory B2C (Azure AD B2C) zapewnia zarządzanie tożsamościami w chmurze w celu ochrony aplikacji, firm i klientów. Usługa Azure AD B2C umożliwia aplikacjom uwierzytelnianie względem kont społecznościowych i firmowych za pomocą protokołów zgodnych z otwartymi standardami. W tym przewodniku Szybki start przykładowa aplikacja klasyczna platformy Windows Presentation Foundation (WPF) jest używana do logowania za pomocą dostawcy tożsamości społecznościowych i wywoływania chronionego internetowego interfejsu API usługi Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) z **obciążeniem ASP.NET i tworzenia sieci Web.**
- Konto społecznościowe z Facebooka, Google lub Microsoftu.
- [Pobierz plik zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/msalv3.zip) lub sklonuj repozytorium [Azure-Samples/active-directory-b2c-dotnet-desktop](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) z github.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
    ```

## <a name="run-the-application-in-visual-studio"></a>Uruchamianie aplikacji w programie Visual Studio

1. W folderze projektu przykładowej aplikacji otwórz rozwiązanie **active-directory-b2c-wpf.sln** w programie Visual Studio.
2. Naciśnij klawisz **F5**, aby debugować aplikację.

## <a name="sign-in-using-your-account"></a>Logowanie się przy użyciu swojego konta

1. Kliknij pozycję **Zaloguj**, aby uruchomić przepływ pracy **Rejestrowanie lub logowanie**.

    ![Zrzut ekranu przykładowej aplikacji WPF](./media/quickstart-native-app-desktop/wpf-sample-application.png)

    Przykład obsługuje kilka opcji rejestracji. Opcje te obejmują korzystanie z dostawcy tożsamości społecznościowej lub tworzenie konta lokalnego przy użyciu adresu e-mail. W przypadku tego przewodnika Szybki start użyj konta dostawcy tożsamości społecznościowej z Facebooka, Google lub Firmy Microsoft.


2. Usługa Azure AD B2C przedstawia stronę logowania dla fikcyjnej firmy o nazwie Fabrikam dla przykładowej aplikacji sieci web. Aby zarejestrować się przy użyciu dostawcy tożsamości dla sieci społecznościowej, kliknij przycisk dostawcy tożsamości, którego chcesz użyć.

    ![Strona logowania lub rejestracji z dostawcami tożsamości](./media/quickstart-native-app-desktop/sign-in-or-sign-up-wpf.png)

    Uwierzytelniasz się (zaloguj się) przy użyciu poświadczeń konta społecznościowego i autoryzujesz aplikację do odczytu informacji z konta społecznościowego. Po udzieleniu dostępu aplikacji może ona pobrać informacje z profilu na koncie w sieci społecznościowej, takie jak Twoje nazwisko i miasto.

2. Zakończ proces logowania dla dostawcy tożsamości.

    Szczegóły profilu na nowym koncie są wstępnie wypełnione informacjami z konta w sieci społecznościowej.

## <a name="edit-your-profile"></a>Edycja profilu

Usługa Azure AD B2C zawiera funkcję umożliwiającą użytkownikom aktualizowanie profilów. Przykładowa aplikacja internetowa używa przepływu użytkownika profilu edycji usługi Azure AD B2C dla przepływu pracy.

1. Na pasku menu aplikacji kliknij pozycję **Edit profile** (Edytuj profil), aby edytować utworzony profil.

    ![Przycisk Edytuj profil wyróżniony w przykładowej aplikacji WPF](./media/quickstart-native-app-desktop/edit-profile-wpf.png)

2. Wybierz dostawcę tożsamości skojarzonego z utworzonym kontem. Jeśli na przykład podczas tworzenia konta korzystasz z Facebooka jako dostawcy tożsamości, wybierz Pozycję Facebook, aby zmodyfikować powiązane szczegóły profilu.

3. W polu **Display name** zmień swoją nazwę wyświetlaną, a w polu **City** — miejscowość, a następnie kliknij przycisk **Continue** (Kontynuuj).

    Nowy token dostępu zostanie wyświetlony w polu tekstowym *Informacje o tokenie*. Jeśli chcesz zweryfikować zmiany profilu, skopiuj i wklej token dostępu do dekodera tokenów na stronie https://jwt.ms.

## <a name="access-a-protected-api-resource"></a>Uzyskiwanie dostępu do chronionego zasobu interfejsu API

Kliknij pozycję **Wywołaj interfejs API**, aby wysłać żądanie do chronionego zasobu.

![Wywoływanie interfejsu API](./media/quickstart-native-app-desktop/call-api-wpf.png)

Aplikacja dołącza token dostępu usługi Azure AD do żądania wysłanego do chronionego zasobu internetowego interfejsu API. Internetowy interfejs API odsyła z powrotem nazwę wyświetlaną zawartą w tokenie dostępu.

Pomyślnie użyto konta użytkownika usługi Azure AD B2C do wykonania autoryzowanego wywołania chronionego internetowego interfejsu API usługi Azure AD B2C.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz wypróbować inne przewodniki Szybki start lub samouczki usługi Azure AD B2C, możesz użyć swojej dzierżawy usługi Azure AD B2C. Możesz [usunąć dzierżawę usługi Azure AD B2C](faq.md#how-do-i-delete-my-azure-ad-b2c-tenant), gdy nie będzie już potrzebna.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start użyto przykładowej aplikacji klasycznej do:

* Logowanie się przy za pomocą niestandardowej strony logowania
* Logowanie się za pomocą dostawcy tożsamości społecznościowej
* Tworzenie konta usługi Azure AD B2C
* Wywoływanie internetowego interfejsu API chronionego przez usługę Azure AD B2C

Wprowadzenie do tworzenia własnej dzierżawy usługi Azure AD B2C.

> [!div class="nextstepaction"]
> [Tworzenie dzierżawy usługi Azure Active Directory B2C w witrynie Azure Portal](tutorial-create-tenant.md)
