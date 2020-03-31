---
title: Tryb współdzielonego urządzenia dla urządzeń z systemem Android | Azure
description: Dowiedz się więcej o trybie urządzenia współdzielonego, który umożliwia pracownikom pierwszej linii udostępnianie urządzenia z Systemem Android
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 1/15/2020
ms.author: marsma
ms.reviwer: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 9928b64d286cc5072f28f7cc17e4af3e95662cd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085618"
---
# <a name="shared-device-mode-for-android-devices"></a>Tryb udostępnionego urządzenia dla urządzeń z systemem Android

> [!NOTE]
> Ta funkcja jest dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pracownicy pierwszej linii, tacy jak pracownicy handlu detalicznego, członkowie załogi lotniczej i pracownicy obsługi w terenie, często korzystają ze współdzielonego urządzenia mobilnego do wykonywania swojej pracy. Staje się to problematyczne, gdy zaczynają udostępniać hasła lub numery PIN, aby uzyskać dostęp do danych klienta i firmy na udostępnionym urządzeniu.

Tryb urządzenia współdzielonego umożliwia skonfigurowanie urządzenia z systemem Android, tak aby można je było łatwo udostępnić wielu pracownikom. Pracownicy mogą szybko logować się i uzyskiwać dostęp do informacji o klientach. Po zakończeniu zmiany lub zadania mogą wylogować się z urządzenia i będzie ono natychmiast gotowe do użycia przez następnego pracownika.

Tryb urządzenia udostępnionego zapewnia również zarządzanie urządzeniem z tożsamością firmy Microsoft.

Aby utworzyć aplikację trybu urządzenia udostępnionego, deweloperzy i administratorzy urządzeń w chmurze współpracują ze sobą:

- Deweloperzy piszą aplikację z jednym kontem (aplikacje z wieloma `"shared_device_mode_supported": true` kontami nie są obsługiwane w trybie urządzenia udostępnionego), dodają do konfiguracji aplikacji i zapisują kod do obsługi rzeczy, takich jak wylogowywanie się z urządzenia udostępnionego.
- Administratorzy urządzeń przygotowują urządzenie do udostępnienia, instalując aplikację uwierzytelniającą i ustawiając urządzenie w trybie udostępnionym przy użyciu aplikacji uwierzytelniającego. Tylko użytkownicy, którzy są w roli Administrator urządzeń w [chmurze,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#cloud-device-administrator) mogą umieścić urządzenie w trybie udostępnionym za pomocą [aplikacji Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview). Członkostwo ról organizacyjnych w witrynie Azure portal można skonfigurować za pomocą: **ról usługi Azure Active Directory** > i**administratorów** > **urządzeń w chmurze administratora urządzeń w chmurze.**

 W tym artykule skupiono się przede wszystkim na tym, o czym powinni myśleć deweloperzy.

## <a name="single-vs-multiple-account-applications"></a>Aplikacje jedno- i wielokondycjowe

Aplikacje napisane przy użyciu biblioteki uwierzytelniania Microsoft SDK (MSAL) mogą zarządzać jednym kontem lub wieloma kontami. Aby uzyskać szczegółowe informacje, zobacz [tryb jednego konta lub tryb wielu kont](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account). Funkcje platformy tożsamości firmy Microsoft dostępne dla aplikacji różnią się w zależności od tego, czy aplikacja działa w trybie jednego konta, czy w trybie wielu kont.

**Udostępnione aplikacje w trybie urządzenia działają tylko w trybie jednego konta**.

> [!IMPORTANT]
> Aplikacje obsługujące tylko tryb wielu kont nie mogą być uruchamiane na urządzeniu udostępnionym. Jeśli pracownik ładuje aplikację, która nie obsługuje trybu pojedynczego konta, nie będzie działać na urządzeniu udostępnionym.
>
> Aplikacje napisane przed wydaniem pliku MSAL SDK są uruchamiane w trybie wielu kont i muszą zostać zaktualizowane w celu obsługi trybu pojedynczego konta, zanim będą mogły działać na urządzeniu w trybie współużytkowym.

**Obsługa zarówno kont jedno- jak i wielokondycjowych**

Aplikację można sbudować w celu obsługi urządzeń osobistych i urządzeń udostępnionych. Jeśli aplikacja obsługuje obecnie wiele kont i chcesz obsługiwać tryb urządzenia udostępnionego, dodaj obsługę trybu pojedynczego konta.

Możesz też chcieć, aby aplikacja zmieniła swoje zachowanie w zależności od typu urządzenia, na które jest uruchomiona. Służy `ISingleAccountPublicClientApplication.isSharedDevice()` do określania, kiedy ma być uruchamiany w trybie jednego konta.

Istnieją dwa różne interfejsy, które reprezentują typ urządzenia, na które jest na ma być aplikacja. Gdy żądasz wystąpienia aplikacji z fabryki aplikacji MSAL, prawidłowy obiekt aplikacji jest dostarczany automatycznie.

Następujący model obiektu ilustruje typ obiektu, który może zostać wyświetlony i co to oznacza w kontekście urządzenia udostępnionego:

![model dziedziczenia aplikacji klienta publicznego](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

Musisz sprawdzić typ i rzutować do odpowiedniego interfejsu po `PublicClientApplication` otrzymaniu obiektu. Następujący kod sprawdza tryb wielu kont lub tryb pojedynczego konta i odpowiednio rzuca obiekt aplikacji:

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

Następujące różnice mają zastosowanie w zależności od tego, czy aplikacja jest uruchomiona na urządzeniu udostępnionym, czy osobistym:

|  | Urządzenie trybu współdzielonego  | Urządzenie osobiste |
|---------|---------|---------|
| **Konta**     | Pojedyncze konto | Wiele kont |
| **Logowanie** | Globalny | Globalny |
| **Wyrejestrowywania** | Globalny | Każda aplikacja może kontrolować, czy wylogowywanie jest lokalne dla aplikacji lub dla rodziny aplikacji. |
| **Obsługiwane typy konta** | Tylko konta robocze | Obsługiwane konta osobiste i służbowe  |

## <a name="why-you-may-want-to-only-support-single-account-mode"></a>Dlaczego możesz obsługiwać tylko tryb jednego konta

Jeśli piszesz aplikację, która będzie używana tylko dla pracowników pierwszej linii przy użyciu urządzenia udostępnionego, zaleca się, aby napisać aplikację do obsługi tylko tryb jednego konta. Obejmuje to większość aplikacji, które koncentrują się na zadaniach, takich jak aplikacje dokumentacji medycznej, aplikacje do faktur i większość aplikacji biznesowych. Tylko obsługa trybu pojedynczego konta upraszcza rozwój, ponieważ nie trzeba implementować dodatkowych funkcji, które są częścią aplikacji z wieloma kontami.

## <a name="what-happens-when-the-device-mode-changes"></a>Co się stanie, gdy zmienia się tryb urządzenia

Jeśli aplikacja jest uruchomiona w trybie wielu kont, a administrator przełącza urządzenie w trybie urządzenia udostępnionego, wszystkie konta na urządzeniu są czyszczone z aplikacji, a aplikacja przechodzi do trybu pojedynczego konta.

## <a name="shared-device-sign-out-and-the-overall-app-lifecycle"></a>Wylogowywanie się z urządzenia udostępnionego i cały cykl życia aplikacji

Gdy użytkownik się wyloguje, musisz podjąć działania w celu ochrony prywatności i danych użytkownika. Jeśli na przykład budujesz aplikację dokumentacji medycznej, upewnij się, że gdy użytkownik wyloguje się wcześniej z wyświetlanych rekordów pacjentów, zostaną wyczyszczone. Wniosek musi być przygotowany na to i sprawdzić za każdym razem, gdy wchodzi na pierwszy plan.

Gdy aplikacja używa msal do wylogowania użytkownika w aplikacji uruchomionej na urządzeniu, które jest w trybie udostępnionym, zalogowane konto i tokeny buforowane są usuwane zarówno z aplikacji, jak i z urządzenia.

Na poniższym diagramie przedstawiono ogólny cykl życia aplikacji i typowe zdarzenia, które mogą wystąpić podczas pracy aplikacji. Diagram obejmuje od czasu uruchomienia działania, logowania i wylogowywania konta oraz sposobu, w jaki zdarzenia, takie jak wstrzymywanie, wznawianie i zatrzymywanie działania, pasują.

![Cykl życia aplikacji urządzenia udostępnionego](media/v2-shared-device-mode/lifecycle.png)

## <a name="next-steps"></a>Następne kroki

Wypróbuj [tryb Używania udostępnionego urządzenia w](tutorial-v2-shared-device-mode.md) samouczku aplikacji systemu Android, który pokazuje, jak uruchomić aplikację pracownika pierwszej linii na urządzeniu z systemem Android w trybie współdzielonym.
