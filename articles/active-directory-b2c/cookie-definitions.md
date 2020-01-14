---
title: Definicje plików cookie — Azure Active Directory B2C | Microsoft Docs
description: Zawiera definicje plików cookie używanych w Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 66de4559ed006735f53ff993cce29370428b9998
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930890"
---
# <a name="cookies-definitions-for-azure-active-directory-b2c"></a>Definicje plików cookie dla Azure Active Directory B2C

W poniższej tabeli wymieniono pliki cookie używane w Azure Active Directory B2C.

| Nazwa | Domain | Wygaśnięcie | Przeznaczenie |
| ----------- | ------ | -------------------------- | --------- |
| x-ms-cpim-admin | main.b2cadmin.ext.azure.com | Koniec [sesji przeglądarki](session-behavior.md) | Przechowuje dane członkostwa użytkowników między dzierżawcami. Dzierżawy, których użytkownik jest członkiem i poziom członkostwa (administrator lub użytkownik). |
| x-ms-cpim-slice | login.microsoftonline.com, b2clogin.com, domena z marką | Koniec [sesji przeglądarki](session-behavior.md) | Służy do kierowania żądaniami do odpowiedniego wystąpienia produkcyjnego. |
| x-ms-cpim-trans | login.microsoftonline.com, b2clogin.com, domena z marką | Koniec [sesji przeglądarki](session-behavior.md) | Służy do śledzenia transakcji (liczba żądań uwierzytelniania do Azure AD B2C) i bieżącej transakcji. |
| x-ms-cpim-sso:{Id} | login.microsoftonline.com, b2clogin.com, domena z marką | Koniec [sesji przeglądarki](session-behavior.md) | Używany do konserwacji sesji logowania jednokrotnego. |
| x-ms-cpim-cache:{id}_n | login.microsoftonline.com, b2clogin.com, domena z marką | Koniec [sesji przeglądarki](session-behavior.md), pomyślne uwierzytelnienie | Używane do obsługi stanu żądania. |
| x-ms-cpim-csrf | login.microsoftonline.com, b2clogin.com, domena z marką | Koniec [sesji przeglądarki](session-behavior.md) | Token podrabiania żądania dla wielu witryn używany do ochrony CRSF. |
| x-ms-cpim-dc | login.microsoftonline.com, b2clogin.com, domena z marką | Koniec [sesji przeglądarki](session-behavior.md) | Używany do Azure AD B2C routingu sieciowego. |
| x-ms-cpim-ctx | login.microsoftonline.com, b2clogin.com, domena z marką | Koniec [sesji przeglądarki](session-behavior.md) | Kontekst |
| x-ms-cpim-rp | login.microsoftonline.com, b2clogin.com, domena z marką | Koniec [sesji przeglądarki](session-behavior.md) | Używany do przechowywania danych członkostwa dla dzierżawy dostawcy zasobów. |
| x-ms-cpim-rc | login.microsoftonline.com, b2clogin.com, domena z marką | Koniec [sesji przeglądarki](session-behavior.md) | Używany do przechowywania pliku cookie przekazywania. |
