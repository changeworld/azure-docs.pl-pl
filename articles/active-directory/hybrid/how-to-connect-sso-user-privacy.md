---
title: Prywatność użytkownika i usługa Azure AD bezproblemowe logowanie jednokrotne | Dokumentacja firmy Microsoft
description: Ten artykuł dotyczy bezproblemowego logowania jednokrotnego usługi Azure Active Directory (Azure AD) i RODO zgodności.
services: active-directory
keywords: Co to jest program Azure AD Connect, RODO, wymaganych składników dla usługi Azure AD, logowania jednokrotnego, logowanie jednokrotne
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9311c1060b953e87f163cb482db14cdd43f50d3d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60242109"
---
# <a name="user-privacy-and-azure-ad-seamless-single-sign-on"></a>Prywatność użytkownika i usługi Azure AD bezproblemowego logowania jednokrotnego

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Omówienie


Usługa Azure bezproblemowe logowanie Jednokrotne AD tworzy następujący typ dziennika może zawierać dane osobowe: 

- Pliki dziennika śledzenia usługi Azure AD Connect.

Poprawa ochrony prywatności użytkowników bezproblemowego logowania jednokrotnego na dwa sposoby:

1.  Na żądanie wyodrębnianie danych dla osoby i Usuń dane z tej osoby z instalacji.
2.  Upewnij się, że żadne dane nie są przechowywane dłużej niż 48 godzin.

Druga opcja zdecydowanie zaleca się jak jest łatwiejsze do wdrożenia i konserwacji. Zobacz, postępując zgodnie z instrukcjami dla każdego typu dziennika:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Usuń pliki dziennika śledzenia program Azure AD Connect

Sprawdź zawartość **%ProgramData%\AADConnect** folder i Usuń śledzenia dziennika zawartość (**śledzenia -\*.log** plików) z tego folderu, w ciągu 48 godzin od instalowanie lub uaktualnianie usługi Azure AD Connect lub modyfikując konfigurację bezproblemowe logowanie Jednokrotne, ta akcja może utworzyć danych objętych RODO.

>[!IMPORTANT]
>Nie usuwaj **PersistedState.xml** pliku w tym folderze, ponieważ ten plik jest używany do zarządzania stanem poprzedniej instalacji programu Azure AD Connect i jest używany po zakończeniu instalacji uaktualnienia. Ten plik nigdy nie będzie zawierać żadnych danych dotyczących osoby i nigdy nie należy go usunąć.

Możesz przejrzeć i usuń te pliki dziennika śledzenia przy użyciu Eksploratora Windows lub poniższy skrypt programu PowerShell można użyć do wykonania niezbędnych akcji:

```powershell
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Zapisz skrypt w pliku z ". PS1 "rozszerzenia. Uruchom ten skrypt, zgodnie z potrzebami.

Aby dowiedzieć się więcej o powiązanych wymagania dotyczące usługi Azure AD Connect RODO, zobacz [w tym artykule](reference-connect-user-privacy.md).

### <a name="note-about-domain-controller-logs"></a>Należy pamiętać o dziennikach kontrolera domeny

Jeśli rejestrowanie inspekcji jest włączona, ten produkt może generować dzienniki zabezpieczeń dla kontrolerów domeny. Aby dowiedzieć się więcej na temat konfigurowania zasad inspekcji, przeczytaj ten [artykułu](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>Kolejne kroki

* [Przegląd zasad Privacy firmy Microsoft w Centrum zaufania](https://www.microsoft.com/trustcenter)
  - [**Rozwiązywanie problemów z** ](tshoot-connect-sso.md) — Dowiedz się, jak rozwiązać typowe problemy z funkcją.
  - [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) — w przypadku zgłaszania sugestie dotyczące nowych funkcji.