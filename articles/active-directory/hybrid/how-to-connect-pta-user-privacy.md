---
title: Prywatność użytkowników i uwierzytelnianie przekazywane usługi Azure Active Directory | Dokumenty firmy Microsoft
description: Ten artykuł dotyczy uwierzytelniania przekazywanego usługi Azure Active Directory (Azure AD) i zgodności z RODO.
services: active-directory
keywords: Uwierzytelnianie przekazywania usługi Azure AD Connect, RODO, wymagane składniki dla usługi Azure AD, logowania jednokrotnego, logowania jednokrotnego
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0af1c42e7e2c163e7f9e7407d0236e35bfacf8e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76931009"
---
# <a name="user-privacy-and-azure-active-directory-pass-through-authentication"></a>Prywatność użytkownika i uwierzytelnianie przekazywane usługi Azure Active Directory


[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Omówienie

Uwierzytelnianie przekazywane usługi Azure AD tworzy następujący typ dziennika, który może zawierać dane osobowe:

- Pliki dziennika śledzenia usługi Azure AD Connect.
- Pliki dziennika śledzenia agenta uwierzytelniania.
- Pliki dziennika zdarzeń systemu Windows.

Zwiększ prywatność użytkowników w zakresie uwierzytelniania przekazywanego na dwa sposoby:

1.  Na żądanie, wyodrębnić dane dla osoby i usunąć dane z tej osoby z instalacji.
2.  Upewnij się, że żadne dane nie są przechowywane dłużej niż 48 godzin.

Zdecydowanie zalecamy drugą opcję, ponieważ łatwiej jest zaimplementować i utrzymać. Poniżej przedstawiono instrukcje dla każdego typu dziennika:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Usuwanie plików dziennika śledzenia usługi Azure AD Connect

Sprawdź zawartość folderu **%ProgramData%\AADConnect** i usuń zawartość dziennika śledzenia **(trace-\*.log** files) tego folderu w ciągu 48 godzin od zainstalowania lub uaktualnienia usługi Azure AD Connect lub zmodyfikowania konfiguracji uwierzytelniania przekazywanego, ponieważ ta akcja może tworzyć dane objęte RODO.

>[!IMPORTANT]
>Nie usuwaj pliku **PersistedState.xml** w tym folderze, ponieważ ten plik jest używany do utrzymania stanu poprzedniej instalacji usługi Azure AD Connect i jest używany po zakończeniu instalacji uaktualnienia. Ten plik nigdy nie będzie zawierał żadnych danych o osobie i nigdy nie powinien zostać usunięty.

Można przejrzeć i usunąć te pliki dziennika śledzenia przy użyciu Eksploratora Windows lub użyć następującego skryptu programu PowerShell do wykonania niezbędnych akcji:

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Zapisz skrypt w pliku z ". PS1". Uruchom ten skrypt w razie potrzeby.

Aby dowiedzieć się więcej o powiązanych wymaganiach RODO usługi Azure AD Connect, zobacz [ten artykuł](reference-connect-user-privacy.md).

### <a name="delete-authentication-agent-event-logs"></a>Usuwanie dzienników zdarzeń agenta uwierzytelniania

Ten produkt może również tworzyć **dzienniki zdarzeń systemu Windows**. Aby dowiedzieć się więcej, przeczytaj [ten artykuł](https://msdn.microsoft.com/library/windows/desktop/aa385780(v=vs.85).aspx).

Aby wyświetlić dzienniki związane z agentem uwierzytelniania przekazywania, otwórz aplikację **Podgląd zdarzeń** na serwerze i zaznacz w obszarze **Dzienniki aplikacji i usług\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

### <a name="delete-authentication-agent-trace-log-files"></a>Usuń pliki dziennika śledzenia agenta uwierzytelniania

Należy regularnie sprawdzać zawartość **%ProgramData%\Microsoft\Azure AD Connect Authentication Agent\Trace** i usuwać zawartość tego folderu co 48 godzin. 

>[!IMPORTANT]
>Jeśli usługa Agent uwierzytelniania jest uruchomiona, nie będzie można usunąć bieżącego pliku dziennika w folderze. Zatrzymaj usługę przed ponowną próbą. Aby uniknąć błędów logowania użytkownika, należy już skonfigurować uwierzytelnianie przekazywane dla [wysokiej dostępności](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

Można przejrzeć i usunąć te pliki za pomocą Eksploratora Windows lub użyć następującego skryptu do wykonania niezbędnych akcji:

```
$Files = ((Get-childitem -Path "$env:programdata\microsoft\azure ad connect authentication agent\trace" -Recurse).VersionInfo).FileName 
 
Foreach ($file in $files) { 
    {Remove-Item -Path $File -Force} 
}
```

Aby zaplanować uruchamianie tego skryptu co 48 godzin, wykonaj następujące czynności:

1.  Zapisz skrypt w pliku z ". PS1".
2.  Otwórz **Panel sterowania** i kliknij system i **bezpieczeństwo**.
3.  W **rubryce Narzędzia administracyjne** kliknij "**Zaplanuj zadania**".
4.  W **harmonogramie zadań**kliknij prawym przyciskiem myszy "**Biblioteka harmonogramów zadań**" i kliknij "**Utwórz zadanie podstawowe...**".
5.  Wprowadź nazwę nowego zadania i kliknij przycisk **Dalej**.
6.  Wybierz **"Codziennie"** dla **wyzwalacza zadania** i kliknij przycisk **Dalej**.
7.  Ustaw cykl na dwa dni i kliknij przycisk **Dalej**.
8.  Wybierz opcję **"Uruchom program"** jako akcję i kliknij przycisk **Dalej**.
9.  Wpisz **"PowerShell**" w polu programu/skryptu, a w polu **"Dodaj argumenty (opcjonalnie)**" wprowadź pełną ścieżkę do skryptu utworzonego wcześniej, a następnie kliknij przycisk **Dalej**.
10. Na następnym ekranie zostanie wyświetlenie podsumowania zadania, które zamierzasz utworzyć. Sprawdź wartości i kliknij przycisk **Zakończ,** aby utworzyć zadanie:
 
### <a name="note-about-domain-controller-logs"></a>Uwaga dotycząca dzienników kontrolera domeny

Jeśli rejestrowanie inspekcji jest włączone, ten produkt może generować dzienniki zabezpieczeń dla kontrolerów domeny. Aby dowiedzieć się więcej na temat konfigurowania zasad inspekcji, przeczytaj ten [artykuł](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>Następne kroki
* [Zapoznaj się z zasadami zachowania poufności firmy Microsoft w Centrum zaufania](https://www.microsoft.com/trustcenter)
* [**Rozwiązywanie problemów**](tshoot-connect-pass-through-authentication.md) — dowiedz się, jak rozwiązać typowe problemy z tą funkcją.
