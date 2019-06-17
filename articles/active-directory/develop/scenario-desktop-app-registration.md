---
title: Aplikacja klasyczna wywołuje interfejsy API (Rejestrowanie aplikacji) — sieci web platforma tożsamości firmy Microsoft
description: Naucz się tworzyć aplikację klasyczną wywołuje interfejsy API (Rejestrowanie aplikacji) sieci web
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ab2701a82da0b8f7bc4e23a3d947be905593e85
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057213"
---
# <a name="desktop-app-that-calls-web-apis---app-registration"></a>Aplikacja klasyczna, że wywołania sieci web interfejsy API — rejestrowanie aplikacji

Ten artykuł zawiera specyfikę rejestracji aplikacji dla aplikacji klasycznych.

## <a name="supported-accounts-types"></a>Typy obsługiwanych kont

Typy kont, są obsługiwane w aplikacji klasycznej zależą od środowiska, aby światła w górę, a zatem w przepływach chcesz użyć.

### <a name="audience-for-interactive-token-acquisition"></a>Odbiorców interaktywne uzyskanie tokenu

Jeśli uwierzytelnianie interakcyjne korzysta z aplikacji pulpitu, możesz zalogować się użytkowników z żadnej [typ konta](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)

### <a name="audience-for-desktop-app-silent-flows"></a>Grupy odbiorców dla aplikacji klasycznej dyskretnej przepływów

- Jeśli zamierzasz używać uwierzytelniania zintegrowanego Windows lub nazwy użytkownika/hasła, aplikacja wymaga logowania użytkowników w własnej dzierżawy (LOB Deweloper) lub w usłudze Azure Active directory organizacji (scenariusz niezależnego dostawcy oprogramowania). Te przepływy uwierzytelniania nie są obsługiwane w przypadku osobistych kont Microsoft
- Jeśli chcesz korzystać z tego przepływu kodu urządzenia, nie możesz zalogować użytkowników z ich osobistych kont Microsoft jeszcze
- Jeśli użytkownicy logują się wraz z tożsamościami społecznościowymi, przekazując urząd B2C i zasad, można używać tylko uwierzytelnianie interakcyjne i nazwy użytkownika i hasła.

## <a name="redirect-uris"></a>Identyfikatory URI przekierowania

Ponownie identyfikatory URI przekierowania do użycia w aplikacji klasycznej zależy przepływ, którego chcesz użyć.

- Jeśli używasz **przeprowadzić uwierzytelnianie interakcyjne** lub **przepływu kodu urządzenia**, będziesz chciał użyć `https://login.microsoftonline.com/common/oauth2/nativeclient`. Ta konfiguracja będzie osiągnięcie, klikając odpowiedni adres URL w **uwierzytelniania** sekcji dla aplikacji
  
  > [!IMPORTANT]
  > Już dziś platformy MSAL.NET domyślnie aplikacje pulpitu w systemie Windows korzysta inny identyfikator URI przekierowania (`urn:ietf:wg:oauth:2.0:oob`). W przyszłości będzie chcemy zmienić to ustawienie domyślne, a w związku z tym firma Microsoft zaleca użycie `https://login.microsoftonline.com/common/oauth2/nativeclient`

- Aplikacji tylko za pomocą uwierzytelniania zintegrowanego Windows, nazwy użytkownika/hasła, nie trzeba zarejestrować identyfikator URI przekierowania dla aplikacji. W rzeczywistości te przepływy są rund do punktu końcowego v2.0 platforma tożsamości firmy Microsoft i aplikacja nie będzie wywołanie zwrotne w dowolnym określonym identyfikatorze URI. 
- W celu odróżnienia przepływ kodu urządzenia, zintegrowane uwierzytelnianie Windows i nazwy użytkownika/hasła z przepływ aplikacji zawierających poufne dane klienta, który nie ma identyfikatory URI przekierowania albo (klient poświadczeń przepływ używany w aplikacjach demon), musisz express czy aplikacja jest aplikacją kliencką publicznych. Ta konfiguracja jest osiągane, przechodząc do **uwierzytelniania** sekcji dla aplikacji, a w **Zaawansowane ustawienia** podsekcję, wybierz polecenie **tak**, użytkownik **Traktować aplikacji w publicznych klienta** (w **domyślny typ klienta** akapitu)

  ![Zezwalaj na publicznych klienta](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>Uprawnienia do interfejsu API

Aplikacje klasyczne wywoływania interfejsów API w imieniu zalogowanego użytkownika. Muszą zażądać uprawnień delegowanych. Nie można ich zażądać uprawnień aplikacji (tylko obsługę w [aplikacje demona](scenario-daemon-overview.md))

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Aplikacja klasyczna — Konfiguracja aplikacji](scenario-desktop-app-configuration.md)
