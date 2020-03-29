---
title: Formanty sesji w zasadach dostępu warunkowego — usługa Azure Active Directory
description: Co to są formanty sesji w zasadach dostępu warunkowego usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: e99b9b87f939d614679fdecf24c9d36d99bf2938
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671885"
---
# <a name="conditional-access-session"></a>Dostęp warunkowy: sesja

W ramach zasad dostępu warunkowego administrator może korzystać z formantów sesji, aby włączyć ograniczone środowisko w określonych aplikacjach w chmurze.

![Zasady dostępu warunkowego z kontrolą dotacji wymagającą uwierzytelniania wieloskładnikowego](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>Ograniczenia wymuszone aplikacją

Organizacje mogą używać tego formantu, aby wymagać usługi Azure AD do przekazywania informacji o urządzeniu do wybranych aplikacji w chmurze. Informacje o urządzeniu umożliwiają aplikacjom w chmurze poznanie, czy połączenie jest inicjowane ze zgodnego urządzenia, czy z urządzenia przyłączanego do domeny. Ta kontrolka obsługuje tylko usługi SharePoint Online i Exchange Online jako wybrane aplikacje w chmurze. Po wybraniu tej opcji aplikacja w chmurze używa informacji o urządzeniu, aby zapewnić użytkownikom, w zależności od stanu urządzenia, ograniczone lub pełne środowisko.

Aby uzyskać więcej informacji na temat używania i konfigurowania ograniczeń wymuszonych przez aplikację, zobacz następujące artykuły:

- [Włączanie ograniczonego dostępu za pomocą usługi SharePoint Online](/sharepoint/control-access-from-unmanaged-devices)
- [Włączanie ograniczonego dostępu za pomocą usługi Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>Kontrola aplikacji dostępu warunkowego

Kontrola aplikacji dostępu warunkowego używa architektury odwrotnego serwera proxy i jest jednoznacznie zintegrowana z dostępem warunkowym usługi Azure AD. Dostęp warunkowy usługi Azure AD umożliwia wymuszanie kontroli dostępu w aplikacjach organizacji na podstawie określonych warunków. Warunki określają, kto (użytkownik lub grupa użytkowników) i jakie (które aplikacje w chmurze) i gdzie (które lokalizacje i sieci) zasady dostępu warunkowego jest stosowany do. Po określeniu warunków można kierować użytkowników do [usługi Microsoft Cloud App Security,](/cloud-app-security/what-is-cloud-app-security) gdzie można chronić dane za pomocą kontroli aplikacji dostępu warunkowego, stosując kontrolki dostępu i sesji.

Kontrola dostępu warunkowego aplikacji umożliwia monitorowanie i kontrolowanie dostępu do aplikacji użytkowników i sesji w czasie rzeczywistym na podstawie zasad dostępu i sesji. Zasady dostępu i sesji są używane w portalu cloud app security w celu dalszego udoskonalania filtrów i ustawiania akcji, które należy podjąć u użytkownika. Dzięki zasadom dostępu i sesji można:

- Zapobiegaj eksfiltracji danych: można zablokować pobieranie, wycinanie, kopiowanie i drukowanie poufnych dokumentów na, na przykład, na urządzeniach niezarządzanych.
- Ochrona podczas pobierania: Zamiast blokować pobieranie poufnych dokumentów, możesz wymagać, aby dokumenty były etykietowane i chronione za pomocą usługi Azure Information Protection. Ta akcja gwarantuje, że dokument jest chroniony, a dostęp użytkownika jest ograniczony w potencjalnie ryzykownej sesji.
- Zapobiegaj przekazywaniu plików bez etykiety: Zanim poufny plik zostanie przekazany, rozpowszechniony i używany przez inne osoby, należy upewnić się, że plik ma właściwą etykietę i ochronę. Można upewnić się, że pliki bez etykiety z zawartością wrażliwą są blokowane przed przekazywaniem, dopóki użytkownik nie klasyfikuje zawartości.
- Monitorowanie sesji użytkownika pod kątem zgodności: ryzykowni użytkownicy są monitorowani, gdy logują się do aplikacji, a ich akcje są rejestrowane z poziomu sesji. Można zbadać i przeanalizować zachowanie użytkownika, aby zrozumieć, gdzie i na jakich warunkach zasady sesji powinny być stosowane w przyszłości.
- Zablokuj dostęp: możesz szczegółowo zablokować dostęp dla określonych aplikacji i użytkowników w zależności od kilku czynników ryzyka. Na przykład można je zablokować, jeśli używają certyfikatów klienta jako formy zarządzania urządzeniami.
- Blokowanie działań niestandardowych: niektóre aplikacje mają unikatowe scenariusze, które niosą ze sobą ryzyko, na przykład wysyłanie wiadomości z poufnych treści w aplikacjach, takich jak Microsoft Teams lub Slack. W tego rodzaju scenariuszach można skanować wiadomości w poszukiwaniu poufnych treści i blokować je w czasie rzeczywistym.

Aby uzyskać więcej informacji, zobacz artykuł [Wdrażanie kontroli aplikacji dostępu warunkowego dla polecanych aplikacji](/cloud-app-security/proxy-deployment-aad).

## <a name="sign-in-frequency-preview"></a>Częstotliwość logowania (podgląd)

Częstotliwość logowania określa okres, w którym użytkownik zostanie poproszony o ponowne zalogowanie się podczas próby uzyskania dostępu do zasobu.

Ustawienie częstotliwości logowania działa z aplikacjami, które zaimplementowały protokoły OAUTH2 lub OIDC zgodnie ze standardami. Większość natywnych aplikacji firmy Microsoft dla systemów Windows, Mac i Mobile, w tym następujące aplikacje sieci Web, jest zgodna z tym ustawieniem.

- Word, Excel, PowerPoint Online
- OneNote Online
- Office.com
- Portal administracyjny usługi O365
- Exchange Online
- Usługi SharePoint i OneDrive
- Klient internetowy zespołów
- Dynamics CRM Online
- Portal Azure

Aby uzyskać więcej informacji, zobacz artykuł [Konfigurowanie zarządzania sesjami uwierzytelniania za pomocą programu Dostęp warunkowy](howto-conditional-access-session-lifetime.md#user-sign-in-frequency).

## <a name="persistent-browser-session-preview"></a>Trwała sesja przeglądarki (wersja zapoznawcza)

Trwała sesja przeglądarki pozwala użytkownikom pozostać zalogowanym po zamknięciu i ponownym otwarciu okna przeglądarki.

Aby uzyskać więcej informacji, zobacz artykuł [Konfigurowanie zarządzania sesjami uwierzytelniania za pomocą programu Dostęp warunkowy](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions).

## <a name="next-steps"></a>Następne kroki

- [Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

- [Tryb samego raportu](concept-conditional-access-report-only.md)
