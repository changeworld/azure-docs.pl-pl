---
title: Zagadnienia dotyczące platformy Windows Universal (Biblioteka Microsoft Authentication Library dla platformy .NET) | Azure
description: Dowiedz się więcej o tu konkretne uwagi przy użyciu Universal Windows Platform Biblioteka Microsoft Authentication Library for .NET (platformy MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83fb999b0cf66cfd8d96e82d23ed43626352a8aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544133"
---
# <a name="universal-windows-platform-specific-considerations-with-msalnet"></a>Uniwersalne zagadnienia specyficzne dla platformy Windows przy użyciu platformy MSAL.NET
W systemie iOS Xamarin istnieje kilka kwestii, które należy wziąć pod uwagę podczas przy użyciu platformy MSAL.NET.

## <a name="the-usecorporatenetwork-property"></a>Właściwość UseCorporateNetwork
Na platformie WinRT `PublicClientApplication` ma następującą właściwość logiczna ``UseCorporateNetwork``. Ta właściwość umożliwia aplikacjom Win8.1 i platformy uniwersalnej systemu Windows korzystać ze zintegrowanego uwierzytelniania Windows (i w związku z tym usługa rejestracji Jednokrotnej z użytkownika zalogowanego w systemie operacyjnym), gdy użytkownik jest zalogowany przy użyciu konta w federacyjnych Azure AD dzierżawy. Ta funkcja korzysta Książka adresowa systemu Windows (Broker uwierzytelniania sieci Web). 

> [!IMPORTANT]
> Ustawienie tej właściwości na wartość true przyjęto założenie, Deweloper aplikacji ma włączone zintegrowane Windows Authentication (Zintegrowane) w aplikacji. W tym:
> - W ``Package.appxmanifest`` dla aplikacji platformy uniwersalnej systemu Windows, w **możliwości** kartę, należy włączyć następujące możliwości:
>   - Uwierzytelnianie w przedsiębiorstwie
>   - Sieci prywatne (klient i serwer)
>   - Certyfikat użytkownika

Zintegrowane nie jest włączona domyślnie, ponieważ aplikacje żądające możliwości uwierzytelniania w przedsiębiorstwie lub udostępnione certyfikaty użytkownika wymagać wyższego poziomu weryfikacji do zaakceptowania w Windows Store, a nie wszyscy deweloperzy mogą chcieć wykonać wyższego poziom weryfikacji. 

Podstawowej implementacji platformy UWP (książka adresowa systemu Windows) nie działa prawidłowo w przedsiębiorstwach, w którym włączono dostęp warunkowy. Objaw jest, że użytkownik próbuje zalogować się za pomocą Windows hello i proponuje się wprowadzenie, aby wybrać certyfikat, ale certyfikat dla numeru pin nie zostanie znaleziony, lub użytkownik zdecyduje się je, ale nigdy nie monitowany o numer Pin. Obejście polega na użyciu alternatywną metodą (nazwę użytkownika/hasło + telefonu uwierzytelniania), ale środowisko nie jest dobry. 

## <a name="next-steps"></a>Kolejne kroki
Szczegółowe informacje znajdują się w następujących przykładach:

Sample | Platforma | Opis 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | Platforma UWP | Aplikacja kliencka Universal Windows Platform przy użyciu platformy msal.net, uzyskiwanie dostępu do programu Microsoft Graph w celu uwierzytelniania użytkowników za pomocą punktu końcowego v2.0 usługi Azure AD. <br>![Topologia](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Prosta aplikacja Xamarin Forms przedstawiająca sposób użycia biblioteki MSAL do uwierzytelniania konta Microsoft i usługi Azure AD za pośrednictwem punktu końcowego v2.0 usługi AAD i dostęp do programu Microsoft Graph przy użyciu powstały token. <br>![Topologia](media/msal-net-uwp-considerations/topology-xamarin-native.png)|