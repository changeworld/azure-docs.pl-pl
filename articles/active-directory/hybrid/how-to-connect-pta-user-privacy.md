---
title: Prywatność użytkownika i na platformie Azure uwierzytelniania przekazywanego usługi Active Directory | Dokumentacja firmy Microsoft
description: Ten artykuł dotyczy uwierzytelnianie przekazywane usługi Azure Active Directory (Azure AD) i RODO zgodności.
services: active-directory
keywords: Azure AD Connect uwierzytelniania przekazywanego, RODO, wymaganych składników dla usługi Azure AD, logowania jednokrotnego, logowanie jednokrotne
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
ms.openlocfilehash: f1a7b740a6b248a12fa3d95f85f602ef7a8b2fa5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60242381"
---
# <a name="user-privacy-and-azure-active-directory-pass-through-authentication"></a>Uwierzytelnianie przekazywane usługi Active Directory rozwiązania prywatność użytkownika i na platformie Azure


[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Omówienie

Uwierzytelnianie przekazywane usługi AD systemu Azure tworzy następujący typ dziennika może zawierać dane osobowe:

- Pliki dziennika śledzenia usługi Azure AD Connect.
- Pliki dziennika śledzenia agenta uwierzytelniania.
- Pliki dziennika zdarzeń Windows.

Poprawa ochrony prywatności użytkowników dla uwierzytelniania przekazywanego na dwa sposoby:

1.  Na żądanie wyodrębnianie danych dla osoby i Usuń dane z tej osoby z instalacji.
2.  Upewnij się, że żadne dane nie są przechowywane dłużej niż 48 godzin.

Druga opcja zdecydowanie zaleca się jak jest łatwiejsze do wdrożenia i konserwacji. Poniżej przedstawiono instrukcje dla każdego typu dziennika:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Usuń pliki dziennika śledzenia program Azure AD Connect

Sprawdź zawartość **%ProgramData%\AADConnect** folder i Usuń śledzenia dziennika zawartość (**śledzenia -\*.log** plików) z tego folderu, w ciągu 48 godzin od instalowanie lub uaktualnianie usługi Azure AD Connect lub modyfikując konfigurację uwierzytelniania przekazywanego, ta akcja może utworzyć danych objętych RODO.

>[!IMPORTANT]
>Nie usuwaj **PersistedState.xml** pliku w tym folderze, ponieważ ten plik jest używany do zarządzania stanem poprzedniej instalacji programu Azure AD Connect i jest używany po zakończeniu instalacji uaktualnienia. Ten plik nigdy nie będzie zawierać żadnych danych dotyczących osoby i nigdy nie należy go usunąć.

Możesz przejrzeć i usuń te pliki dziennika śledzenia przy użyciu Eksploratora Windows lub poniższy skrypt programu PowerShell można użyć do wykonania niezbędnych akcji:

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Zapisz skrypt w pliku z ". PS1 "rozszerzenia. Uruchom ten skrypt, zgodnie z potrzebami.

Aby dowiedzieć się więcej o powiązanych wymagania dotyczące usługi Azure AD Connect RODO, zobacz [w tym artykule](reference-connect-user-privacy.md).

### <a name="delete-authentication-agent-event-logs"></a>Usuń agenta uwierzytelniania, dzienniki zdarzeń

Ten produkt może również utworzyć **dzienniki zdarzeń Windows**. Aby dowiedzieć się więcej, przeczytaj [w tym artykule](https://msdn.microsoft.com/library/windows/desktop/aa385780(v=vs.85).aspx).

Aby wyświetlić dzienniki powiązanych z agentem uwierzytelniania przekazywanego, otwórz **Podgląd zdarzeń** aplikacji na serwerze i wyboru w obszarze **Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin usług i aplikacji** .

### <a name="delete-authentication-agent-trace-log-files"></a>Usuń pliki dziennika śledzenia Agent uwierzytelniania

Należy regularnie Sprawdź zawartość <strong>%ProgramData%\Microsoft\Azure AD Connect Agent\Trace uwierzytelniania\</ strong > i usuń zawartość tego folderu, co 48 godzin. 

>[!IMPORTANT]
>Jeśli jest uruchomiona usługa agenta uwierzytelniania, nie będzie można usunąć bieżącego pliku dziennika w folderze. Zatrzymaj usługę przed podjęciem ponownej próby. Aby uniknąć błędów logowania użytkownika, użytkownik powinien zostać skonfigurowany uwierzytelniania przekazywanego, aby uzyskać [wysokiej dostępności](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

Możesz przejrzeć i usunąć te pliki przy użyciu Eksploratora Windows, lub można użyć poniższego skryptu do wykonywania czynności niezbędnych:

```
$Files = ((Get-childitem -Path "$env:programdata\microsoft\azure ad connect authentication agent\trace" -Recurse).VersionInfo).FileName 
 
Foreach ($file in $files) { 
    {Remove-Item -Path $File -Force} 
}
```

Aby zaplanować uruchomienie tego skryptu co 48 godzin, wykonaj następujące kroki:

1.  Zapisz skrypt w pliku z ". PS1 "rozszerzenia.
2.  Otwórz **Panelu sterowania** i kliknij pozycję **System i zabezpieczenia**.
3.  W obszarze **narzędzia administracyjne** nagłówek, kliknij pozycję "**harmonogram zadań**".
4.  W **harmonogram zadań**, kliknij prawym przyciskiem myszy "**Biblioteka Harmonogramu zadań**"i kliknij pozycję"**Utwórz o podstawowe zadania...** ".
5.  Wprowadź nazwę dla nowego zadania, a następnie kliknij przycisk **dalej**.
6.  Wybierz pozycję "**codzienne**" dla **wyzwalacz zadania** i kliknij przycisk **dalej**.
7.  Wartość cyklu dwa dni, a następnie kliknij przycisk **dalej**.
8.  Wybierz pozycję "**uruchomić program**" jako akcję i kliknij przycisk **dalej**.
9.  Typ "**PowerShell**"w polu dla programu/skryptu i w polu z etykietą"**Dodaj argumenty (opcjonalne)**", wprowadź pełną ścieżkę do skryptu, który został utworzony wcześniej, a następnie kliknij przycisk **dalej**.
10. Następny ekran pokazuje, podsumowania zadania, które chcesz utworzyć. Sprawdź wartości, a następnie kliknij przycisk **Zakończ** do utworzenia zadania:
 
### <a name="note-about-domain-controller-logs"></a>Należy pamiętać o dziennikach kontrolera domeny

Jeśli rejestrowanie inspekcji jest włączona, ten produkt może generować dzienniki zabezpieczeń dla kontrolerów domeny. Aby dowiedzieć się więcej na temat konfigurowania zasad inspekcji, przeczytaj ten [artykułu](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>Kolejne kroki
* [Przegląd zasad Privacy firmy Microsoft w Centrum zaufania](https://www.microsoft.com/trustcenter)
* [**Rozwiązywanie problemów z** ](tshoot-connect-pass-through-authentication.md) — Dowiedz się, jak rozwiązać typowe problemy z funkcją.
