---
title: Zagadnienia dotyczące platformy uniwersalnej systemuposłowego (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o zagadnieniach dotyczących korzystania z platformy uniwersalnej systemu Windows (UWP) z biblioteką uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2eeec28569cf31af4542d6cd7aca1fb27d77b1e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132528"
---
# <a name="considerations-for-using-universal-windows-platform-with-msalnet"></a>Zagadnienia dotyczące korzystania z platformy uniwersalnej systemu Windows z MSAL.NET
Deweloperzy aplikacji korzystających z platformy uniwersalnej systemu Windows (UWP) z MSAL.NET należy wziąć pod uwagę pojęcia, które prezentuje ten artykuł.

## <a name="the-usecorporatenetwork-property"></a>Właściwość UseCorporateNetwork
Na platformie Środowiska Wykonawczego systemu `PublicClientApplication` Windows (WinRT) ma właściwość `UseCorporateNetwork`logiczną . Ta właściwość umożliwia aplikacjom systemu Windows 8.1 i aplikacjom platformy uniwersalnej systemu Windows korzystanie ze zintegrowanego uwierzytelniania systemu Windows (IWA), jeśli użytkownik jest zalogowany na konto, które ma dzierżawę federacyjnej usługi Azure Active Directory (Azure AD). Użytkownicy zalogowali się do systemu operacyjnego, mogą również używać logowania jednokrotnego. Po ustawieniu `UseCorporateNetwork` właściwości MSAL.NET używa brokera uwierzytelniania internetowego (WAB).

> [!IMPORTANT]
> Ustawienie `UseCorporateNetwork` właściwości true zakłada, że deweloper aplikacji włączył IWA w aplikacji. Aby włączyć IWA:
> - W aplikacji platformy uniwersalnej systemu Wizjudy `Package.appxmanifest`na karcie **Możliwości** włącz następujące funkcje:
>   - **Uwierzytelnianie przedsiębiorstwa**
>   - **Sieci prywatne (serwer & klienta)**
>   - **Udostępniony certyfikat użytkownika**

IWA nie jest domyślnie włączona, ponieważ sklep Microsoft Store wymaga wysokiego poziomu weryfikacji, zanim zaakceptuje aplikacje, które żądają możliwości uwierzytelniania przedsiębiorstwa lub certyfikatów użytkowników udostępnionych. Nie wszyscy deweloperzy chcą wykonać ten poziom weryfikacji.

Na platformie platformy platformy uniwersalnej systemu Windows podstawowa implementacja WAB nie działa poprawnie w scenariuszach przedsiębiorstwa, w których jest włączony dostęp warunkowy. Użytkownicy widzą objawy tego problemu podczas próby zalogowania się przy użyciu funkcji Windows Hello. Gdy użytkownik zostanie poproszony o wybranie certyfikatu:

- Nie znaleziono certyfikatu numeru PIN.
- Po wybraniu certyfikatu użytkownik nie jest monitowany o numer PIN.

Możesz spróbować uniknąć tego problemu, używając alternatywnej metody, takiej jak hasło nazwy użytkownika i uwierzytelnianie telefonu, ale środowisko nie jest dobre.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Niektórzy klienci zgłosili następujący błąd logowania w określonych środowiskach przedsiębiorstwa, w których wiedzą, że mają połączenie z Internetem i że połączenie współpracuje z siecią publiczną.

```Text
We can't connect to the service you need right now. Check your network connection or try this again later.
```

Można uniknąć tego problemu, upewniając się, że WAB (podstawowy składnik Systemu Windows) zezwala na sieci prywatnej. Można to zrobić, ustawiając klucz rejestru:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

Aby uzyskać więcej informacji, zobacz [Broker uwierzytelniania sieci Web - Fiddler](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>Następne kroki
Poniższe przykłady zawierają więcej informacji.

Sample | Platforma | Opis 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | Platforma UWP | Aplikacja kliencka platformy uniwersalnej systemu Windows, która używa MSAL.NET. Uzyskuje dostęp do programu Microsoft Graph dla użytkownika, który uwierzytelnia się przy użyciu punktu końcowego usługi Azure AD 2.0. <br>![Topologia](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Prosta aplikacja Xamarin Forms, która pokazuje, jak używać usługi MSAL do uwierzytelniania kont osobistych firmy Microsoft i usługi Azure AD za pośrednictwem punktu końcowego usługi Azure AD 2.0. Pokazano również, jak uzyskać dostęp do programu Microsoft Graph i pokazuje wynikowy token. <br>![Topologia](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
