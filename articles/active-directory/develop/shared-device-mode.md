---
title: Tryb udostępnionego urządzenia dla urządzeń z systemem Android | Azure
description: Dowiedz się więcej o trybie udostępnionego urządzenia, który umożliwia pracownikom Firstline współużytkowanie urządzenia z systemem Android
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 1/15/2020
ms.author: twhitney
ms.reviwer: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 53fa82cf6eaaba09353ba21a12ae9677b9264b1a
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701471"
---
# <a name="shared-device-mode-for-android-devices"></a>Tryb udostępnionego urządzenia dla urządzeń z systemem Android

> [!NOTE]
> Ta funkcja jest dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pracownicy Firstline, tacy jak detaliczni, członkowie zespołu lotniczego i pracownicy usług polowych, często wykorzystują udostępnione urządzenie przenośne do pracy. Sprawia to problemy po rozpoczęciu udostępniania haseł lub numerów PIN w celu uzyskania dostępu do danych klienta i firmowych na udostępnionym urządzeniu.

Tryb udostępnionego urządzenia umożliwia skonfigurowanie urządzenia z systemem Android w taki sposób, aby można je było łatwo udostępnić wielu pracownikom. Pracownicy mogą szybko logować się i uzyskiwać dostęp do informacji o klientach. Po zakończeniu przesunięcia lub przeprowadzenia zadania mogą się wylogować z urządzenia, a następnie być natychmiast gotowe do użycia przez następnego pracownika.

Tryb udostępnionego urządzenia udostępnia również zarządzanie tożsamością utworzoną przez firmę Microsoft.

Aby utworzyć aplikację trybu udostępnionego urządzenia, deweloperzy i Administratorzy urządzeń w chmurze współpracują ze sobą:

- Deweloperzy piszą aplikację jednokontową (aplikacje z wieloma kontami nie są obsługiwane w trybie udostępnionego urządzenia), dodają `"shared_device_mode_supported": true` do konfiguracji aplikacji i zapisują kod w celu obsługi takich elementów jak udostępnione urządzenie do wylogowania.
- Administratorzy urządzeń przygotowuje urządzenie do udostępnienia przez zainstalowanie aplikacji Authenticator i ustawienie trybu udostępniania urządzenia przy użyciu aplikacji Authenticator. Tylko użytkownicy znajdujący się w roli [administratora urządzenia w chmurze](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#cloud-device-administrator) mogą umieścić urządzenie w trybie udostępniania przy użyciu [aplikacji Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview). Członkostwo w rolach organizacyjnych można skonfigurować w Azure Portal: **Azure Active Directory** > **role i Administratorzy** > **administratorem urządzeń w chmurze**.

 Ten artykuł koncentruje się głównie na tym, co deweloperzy powinni wziąć pod uwagę.

## <a name="single-vs-multiple-account-applications"></a>Pojedyncze aplikacje vs z wieloma kontami

Aplikacje opracowane przy użyciu zestawu SDK Microsoft Authentication Library (MSAL) mogą zarządzać pojedynczym kontem lub wieloma kontami. Aby uzyskać szczegółowe informacje, zobacz [tryb pojedynczego konta lub tryb wielu kont](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account). Funkcje platformy tożsamości firmy Microsoft dostępne dla aplikacji różnią się w zależności od tego, czy aplikacja działa w trybie pojedynczego konta, czy też w trybie wielu kont.

**Aplikacje trybu udostępnionego urządzenia działają tylko w trybie jednego konta**.

> [!IMPORTANT]
> Aplikacje obsługujące tryb wielu kont nie mogą być uruchamiane na urządzeniu udostępnionym. Jeśli pracownik załaduje aplikację, która nie obsługuje trybu pojedynczego konta, nie zostanie uruchomiona na urządzeniu udostępnionym.
>
> Aplikacje utworzone przed zestawem SDK MSAL zostały uruchomione w trybie wielu kont i należy je zaktualizować w celu obsługi trybu pojedynczego konta, zanim będą mogły być uruchamiane na urządzeniu trybu współużytkowanego.

**Obsługa zarówno jednego konta, jak i wielu kont**

Aplikację można skompilować do obsługi uruchamiania na urządzeniach osobistych i na urządzeniach udostępnionych. Jeśli aplikacja obsługuje obecnie wiele kont i chcesz obsługiwać tryb udostępnionego urządzenia, Dodaj obsługę trybu pojedynczego konta.

Możesz również zmienić zachowanie aplikacji, w zależności od typu urządzenia, na którym działa. Użyj `ISingleAccountPublicClientApplication.isSharedDevice()`, aby określić, kiedy program ma być uruchamiany w trybie jednego konta.

Istnieją dwa różne interfejsy reprezentujące typ urządzenia, na którym aplikacja jest włączona. Po zażądaniu wystąpienia aplikacji z fabryki aplikacji MSAL, poprawny obiekt aplikacji jest dostarczany automatycznie.

Poniższy model obiektów ilustruje typ obiektu, który może zostać wyświetlony i co oznacza w kontekście urządzenia udostępnionego:

![model dziedziczenia aplikacji klienta publicznego](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

Należy wykonać sprawdzanie typu i rzutowanie na odpowiedni interfejs, gdy uzyskasz `PublicClientApplication` obiektu. Poniższy kod sprawdza tryb wielu kont lub pojedynczy tryb konta i rzutuje odpowiednio obiekt aplikacji:

```java
private IPublicClientApplication mApplication;

        // Running in personal-device mode?
        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        // Running in shared-device mode?
        } else if (mApplication instanceOf ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

Poniższe różnice mają zastosowanie w zależności od tego, czy aplikacja jest uruchomiona na urządzeniu udostępnionym lub osobistym:

|  | Urządzenie trybu udostępnionego  | U rządzenie osobiste |
|---------|---------|---------|
| **Konta**     | Pojedyncze konto | Wiele kont |
| **Logowanie** | Cały świat | Cały świat |
| **Wylogowywanie** | Cały świat | Każda aplikacja może kontrolować, czy wylogowywanie jest lokalne dla aplikacji, czy dla rodziny aplikacji. |
| **Obsługiwane typy kont** | Tylko konta służbowe | Obsługiwane konta osobiste i służbowe  |

## <a name="why-you-may-want-to-only-support-single-account-mode"></a>Dlaczego warto obsługiwać tylko tryb pojedynczego konta

Jeśli piszesz aplikację, która będzie używana tylko dla procesów roboczych Firstline przy użyciu urządzenia udostępnionego, zalecamy zapisanie aplikacji w celu zapewnienia obsługi tylko trybu pojedynczego konta. Obejmuje to większość aplikacji, które są zadaniami, takimi jak aplikacje do rekordów medycznych, aplikacje fakturowania i większość aplikacji biznesowych. Tryb tylko do obsługi jednego konta upraszcza programowanie, ponieważ nie trzeba implementować dodatkowych funkcji, które są częścią aplikacji z wieloma kontami.

## <a name="what-happens-when-the-device-mode-changes"></a>Co się stanie, gdy zmieni się tryb urządzenia

Jeśli aplikacja jest uruchomiona w trybie wielu kont, a administrator umieści urządzenie w trybie udostępnionego urządzenia, wszystkie konta na urządzeniu zostaną wyczyszczone z aplikacji i przejścia aplikacji do trybu pojedynczego konta.

## <a name="shared-device-sign-out-and-the-overall-app-lifecycle"></a>Logowanie do udostępnionego urządzenia i ogólny cykl życia aplikacji

Po wylogowaniu się użytkownika należy podjąć działania w celu ochrony prywatności i danych użytkownika. Jeśli na przykład tworzysz aplikację do rejestrowania medycznego, upewnij się, że gdy użytkownik wyloguje wcześniej wyświetlone rekordy pacjenta są usuwane. Aplikacja musi być przygotowana do tego celu i sprawdzać przy każdym uruchomieniu pierwszego planu.

Gdy aplikacja korzysta z usługi MSAL do wylogowania użytkownika w aplikacji uruchomionej na urządzeniu w trybie udostępnionym, konto zalogowane i tokeny w pamięci podręcznej zostaną usunięte zarówno z aplikacji, jak i urządzenia.

Na poniższym diagramie przedstawiono ogólny cykl życia aplikacji oraz typowe zdarzenia, które mogą wystąpić podczas działania aplikacji. Diagram obejmuje od momentu uruchomienia działania, logowania się i wylogowywania konta oraz sposobu, w jaki zdarzenia takie jak Wstrzymywanie, wznawianie i zatrzymywanie działania mieszczą się w programie.

![Cykl życia aplikacji dla urządzenia udostępnionego](media/v2-shared-device-mode/lifecycle.png)

## <a name="next-steps"></a>Następne kroki

Wypróbuj [tryb użytkowania urządzeń udostępnionych w aplikacji dla systemu Android](tutorial-v2-shared-device-mode.md) , który pokazuje, jak uruchomić aplikację Firstline Worker na urządzeniu z systemem Android w trybie współdzielonym.
