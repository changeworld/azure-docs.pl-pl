---
title: Omówienie trybu urządzenia udostępnionego
titleSuffix: Microsoft identity platform | Azure
description: Dowiedz się więcej o trybie urządzenia udostępnionego, aby włączyć udostępnianie urządzeń dla pracowników pierwszej linii.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 514782d62f117af5bfff4a5d2b3354c4e263eece
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550242"
---
# <a name="overview-of-shared-device-mode"></a>Omówienie trybu udostępnionego urządzenia

Tryb urządzenia udostępnionego jest funkcją usługi Azure Active Directory, która umożliwia tworzenie aplikacji obsługujących pracowników pierwszej linii i włączanie trybu urządzenia udostępnionego na urządzeniach wdrożonych na nich.

> [!NOTE]
> Ta funkcja jest dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-are-firstline-workers"></a>Co to są pracownicy pierwszej linii?

Pracownicy pierwszej linii to pracownicy handlu detalicznego, pracownicy działu konserwacji i terenu, personel medyczny i inni użytkownicy, którzy nie siedzą przed komputerem ani nie używają firmowej poczty e-mail do współpracy. W poniższych sekcjach przedstawiono aspekty i wyzwania związane z obsługą pracowników pierwszej linii, a następnie wprowadzenie do funkcji dostarczonych przez firmę Microsoft, które umożliwiają korzystanie z aplikacji przez pracowników pierwszej linii organizacji.

### <a name="challenges-of-supporting-firstline-workers"></a>Wyzwania związane ze wspieraniem pracowników pierwszej linii

Włączanie przepływów pracy pracownika pierwszej linii obejmuje wyzwania, które zwykle nie są przedstawiane przez typowych pracowników informacji. Takie wyzwania mogą obejmować wysoki wskaźnik obrotów i mniejszą znajomość podstawowych narzędzi zwiększających produktywność organizacji. Aby wzmocnić pozycję pracowników pierwszej linii, organizacje przyjmują różne strategie. Niektórzy przyjmują strategię "bring-your-own-device" (BYOD), w której ich pracownicy korzystają z aplikacji biznesowych na telefonie osobistym, podczas gdy inni zapewniają swoim pracownikom współdzielone urządzenia, takie jak iPady lub tablety z Androidem.

### <a name="supporting-multiple-users-on-devices-designed-for-one-user"></a>Obsługa wielu użytkowników na urządzeniach przeznaczonych dla jednego użytkownika

Ponieważ urządzenia przenośne z systemem iOS lub Android zostały zaprojektowane dla pojedynczych użytkowników, większość aplikacji optymalizuje ich środowisko do użytku przez jednego użytkownika. Część tego zoptymalizowanego środowiska oznacza włączenie logowania jednokrotnego między aplikacjami i utrzymywanie zalogowania użytkowników na swoim urządzeniu. Gdy użytkownik usuwa swoje konto z aplikacji, aplikacja zazwyczaj nie uważa go za zdarzenie związane z zabezpieczeniami. Wiele aplikacji zachowuje nawet poświadczenia użytkownika w celu szybkiego logowania. Być może doświadczyłeś tego samodzielnie po usunięciu aplikacji z urządzenia mobilnego, a następnie ponownym zainstalowaniu aplikacji, tylko po to, aby odkryć, że nadal jesteś zalogowany.

### <a name="global-sign-in-and-sign-out-sso"></a>Globalne logowanie i wylogowywanie się (Logowanie sytego)

Aby umożliwić pracownikom organizacji korzystanie z jej aplikacji w puli urządzeń udostępnionych przez tych pracowników, deweloperzy muszą włączyć przeciwne środowisko. Pracownicy powinni być w stanie wybrać urządzenie z puli i wykonać jeden gest, aby "uczynić go ich" na czas ich zmiany. Pod koniec zmiany powinni być w stanie wykonać kolejny gest, aby wylogować się globalnie na urządzeniu, a wszystkie informacje osobiste i firmowe zostaną usunięte, aby mogli zwrócić je do puli urządzeń. Ponadto, jeśli pracownik zapomni się wylogować, urządzenie powinno zostać automatycznie wylogowane po zakończeniu zmiany i/lub po okresie bezczynności.

Usługa Azure Active Directory umożliwia te scenariusze za pomocą funkcji o nazwie **tryb urządzenia udostępnionego**.

## <a name="introducing-shared-device-mode"></a>Wprowadzenie trybu współdzielonego urządzenia

Jak wspomniano, tryb urządzenia udostępnionego jest funkcją usługi Azure Active Directory, która umożliwia:

* Tworzenie aplikacji obsługujących pracowników pierwszej linii
* Wdrażanie urządzeń w pracownikach pierwszej linii i włączanie trybu współużytkowania urządzenia

### <a name="build-applications-that-support-firstline-workers"></a>Tworzenie aplikacji obsługujących pracowników pierwszej linii

Pracownicy pierwszej linii w aplikacjach można obsługiwać za pomocą biblioteki uwierzytelniania Firmy Microsoft (MSAL) i [aplikacji Microsoft Authenticator,](../user-help/user-help-auth-app-overview.md) aby włączyć stan urządzenia o nazwie *tryb urządzenia udostępnionego*. Gdy urządzenie jest w trybie urządzenia udostępnionego, firma Microsoft udostępnia aplikacji informacje umożliwiające modyfikowanie jego zachowania na podstawie stanu użytkownika na urządzeniu, chroniąc dane użytkownika.

Obsługiwane funkcje to:

* **Zaloguj się na całym urządzeniu użytkownika** za pośrednictwem dowolnej obsługiwanej aplikacji.
* **Wyloguj się z urządzenia użytkownika** za pośrednictwem dowolnej obsługiwanej aplikacji.
* **Zapytaj o stan urządzenia,** aby ustalić, czy aplikacja znajduje się na urządzeniu, które jest w trybie urządzenia udostępnionego.
* **Kwerenda stan urządzenia użytkownika** na urządzeniu, aby ustalić, czy coś się zmieniło od czasu ostatniego użycia aplikacji.

Obsługa współdzielonego trybu urządzenia powinna być uważana za ulepszenie zabezpieczeń i uaktualnienie funkcji aplikacji i może pomóc w zwiększeniu jej wdrażania w wysoce regulowanych środowiskach, takich jak opieka zdrowotna i finanse.

Użytkownicy są zależni od Ciebie, aby upewnić się, że ich dane nie są wyciekane do innego użytkownika. Tryb urządzenia udostępniania zapewnia przydatne sygnały wskazujące aplikacji, że nastąpiła zmiana, którą należy zarządzać. Aplikacja jest odpowiedzialna za sprawdzanie stanu użytkownika na urządzeniu za każdym razem, gdy aplikacja jest używana, czyszczenie danych poprzedniego użytkownika. Obejmuje to, jeśli jest ponownie załadowany z tła w wielozadaniowości. W przypadku zmiany użytkownika należy upewnić się, że zarówno dane poprzedniego użytkownika są czyszczone, jak i że wszystkie buforowane dane wyświetlane w aplikacji są usuwane. Zalecamy, aby zawsze przeprowadzić dokładny proces weryfikacji zabezpieczeń po dodaniu do aplikacji funkcji trybu współużytkującego urządzenia.

Aby uzyskać szczegółowe informacje na temat modyfikowania aplikacji w trybie urządzenia udostępnionego, zobacz [sekcję Następne kroki](#next-steps) na końcu tego artykułu.

### <a name="deploy-devices-to-firstline-workers-and-turn-on-shared-device-mode"></a>Wdrażanie urządzeń w pracownikach pierwszej linii i włączanie trybu współużytkowania urządzenia

Gdy aplikacje obsługują tryb urządzenia udostępnionego i zawierają wymagane dane i zmiany zabezpieczeń, można anonsować je jako użyteczne przez pracowników pierwszej linii.

Administratorzy urządzeń organizacji mogą wdrażać swoje urządzenia i aplikacje w swoich sklepach i miejscach pracy za pomocą rozwiązania do zarządzania urządzeniami przenośnymi (MDM), takiego jak Microsoft Intune. Częścią procesu inicjowania obsługi administracyjnej jest oznaczanie urządzenia jako *urządzenia udostępnionego*. Administratorzy konfigurują tryb urządzenia udostępnionego, wdrażając [aplikację Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) i ustawiając tryb urządzenia udostępnionego za pomocą parametrów konfiguracji. Po wykonaniu tych kroków wszystkie aplikacje obsługujące tryb urządzenia udostępnionego będą używać aplikacji Microsoft Authenticator do zarządzania stanem użytkownika i dostarczania funkcji zabezpieczeń dla urządzenia i organizacji.

## <a name="next-steps"></a>Następne kroki

Obsługujemy platformy iOS i Android dla trybu współdzielonego urządzenia. Zapoznaj się z poniższą dokumentacją dla platformy, aby rozpocząć obsługę pracowników pierwszej linii w aplikacjach.

* [Obsługa trybu współużytkowania urządzenia dla systemu iOS](msal-ios-shared-devices.md)
* [Obsługa trybu współdzielonego urządzenia dla systemu Android](msal-android-shared-devices.md)
