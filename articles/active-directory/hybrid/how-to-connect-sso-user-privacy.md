---
title: Prywatność użytkowników i bezproblemowe logowanie usługi Azure AD bez umowy | Dokumenty firmy Microsoft
description: Ten artykuł dotyczy bezproblemowego logowania jednokrotnego usługi Azure Active Directory (Azure AD) i zgodności z RODO.
services: active-directory
keywords: co to jest Usługa Azure AD Connect, RODO, wymagane składniki dla usługi Azure AD, logowania jednokrotnego, logowania jednokrotnego
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60242109"
---
# <a name="user-privacy-and-azure-ad-seamless-single-sign-on"></a>Prywatność użytkownika i bezproblemowe logowanie jednokrotne do usługi Azure AD

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Omówienie


Bezproblemowe logowanie usługi Azure AD zawiera następujący typ dziennika, który może zawierać dane osobowe: 

- Pliki dziennika śledzenia usługi Azure AD Connect.

Zwiększ prywatność użytkowników w celu bezproblemowego logowania jednokrotnego na dwa sposoby:

1.  Na żądanie, wyodrębnić dane dla osoby i usunąć dane z tej osoby z instalacji.
2.  Upewnij się, że żadne dane nie są przechowywane dłużej niż 48 godzin.

Zdecydowanie zalecamy drugą opcję, ponieważ łatwiej jest zaimplementować i utrzymać. Zobacz następujące instrukcje dla każdego typu dziennika:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Usuwanie plików dziennika śledzenia usługi Azure AD Connect

Sprawdź zawartość folderu **%ProgramData%\AADConnect** i usuń zawartość dziennika śledzenia **(trace-\*.log** files) tego folderu w ciągu 48 godzin od zainstalowania lub uaktualnienia usługi Azure AD Connect lub zmodyfikowania bezproblemowej konfiguracji logowania jednokrotnego, ponieważ ta akcja może tworzyć dane objęte RODO.

>[!IMPORTANT]
>Nie usuwaj pliku **PersistedState.xml** w tym folderze, ponieważ ten plik jest używany do utrzymania stanu poprzedniej instalacji usługi Azure AD Connect i jest używany po zakończeniu instalacji uaktualnienia. Ten plik nigdy nie będzie zawierał żadnych danych o osobie i nigdy nie powinien zostać usunięty.

Można przejrzeć i usunąć te pliki dziennika śledzenia przy użyciu Eksploratora Windows lub użyć następującego skryptu programu PowerShell do wykonania niezbędnych akcji:

```powershell
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Zapisz skrypt w pliku z ". PS1". Uruchom ten skrypt w razie potrzeby.

Aby dowiedzieć się więcej o powiązanych wymaganiach RODO usługi Azure AD Connect, zobacz [ten artykuł](reference-connect-user-privacy.md).

### <a name="note-about-domain-controller-logs"></a>Uwaga dotycząca dzienników kontrolera domeny

Jeśli rejestrowanie inspekcji jest włączone, ten produkt może generować dzienniki zabezpieczeń dla kontrolerów domeny. Aby dowiedzieć się więcej na temat konfigurowania zasad inspekcji, przeczytaj ten [artykuł](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>Następne kroki

* [Zapoznaj się z zasadami zachowania poufności firmy Microsoft w Centrum zaufania](https://www.microsoft.com/trustcenter)
  - [**Rozwiązywanie problemów**](tshoot-connect-sso.md) — dowiedz się, jak rozwiązać typowe problemy z tą funkcją.
  - [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) — do składania nowych żądań funkcji.