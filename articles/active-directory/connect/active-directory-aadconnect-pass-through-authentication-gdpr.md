---
title: Zasady zachowania poufności użytkownika a platformą Azure uwierzytelniania przekazywanego usługi Active Directory | Dokumentacja firmy Microsoft
description: Ten artykuł dotyczy usługi Azure Active Directory (Azure AD) przekazywanego uwierzytelniania i GDPR zgodności.
services: active-directory
keywords: Azure AD Connect przekazywanego uwierzytelniania, GDPR, wymaganych składników dla usługi Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 3343cebb85124f19fe773822e296312abad53d96
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591178"
---
# <a name="user-privacy-and-azure-active-directory-pass-through-authentication"></a>Zasady zachowania poufności użytkownika a platformą Azure uwierzytelniania przekazywanego usługi Active Directory


[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Przegląd

Azure AD przekazywanego uwierzytelniania tworzy następującego typu dziennika, który może zawierać dane osobowe:

- Pliki dziennika śledzenia usługi Azure AD Connect.
- Pliki dziennika śledzenia Agent uwierzytelniania.
- Pliki dziennika zdarzeń systemu Windows.

Poprawa ochrony prywatności użytkowników dla uwierzytelniania przekazywanego na dwa sposoby:

1.  Na żądanie wyodrębnić dane dla osoby i usuwanie danych z tej osoby z instalacji.
2.  Upewnij się, że żadne dane nie są przechowywane poza 48 godzin.

Druga opcja zdecydowanie zaleca się, ponieważ możliwe jest łatwiejsze do wdrożenia i konserwacji. Poniżej przedstawiono instrukcje dla każdego typu dziennika:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Usuń pliki dziennika śledzenia Azure AD Connect

Sprawdź zawartość **%ProgramData%\AADConnect** folder i Usuń śledzenia dziennik zawartość (**śledzenia -\*log** pliki) tego folderu w ciągu 48 godzin Instalowanie lub uaktualnianie Azure AD Connect lub modyfikowanie konfiguracji uwierzytelniania przekazywanego, jak ta akcja może utworzyć danych objętych GDPR.

>[!IMPORTANT]
>Nie należy usuwać **PersistedState.xml** pliku w tym folderze, ponieważ ten plik jest używany do zarządzania stanem poprzedniej instalacji programu Azure AD Connect i jest używany po zakończeniu instalacji uaktualnienia. Ten plik nigdy nie będzie zawierać wszystkie dane dotyczące osoby i nigdy nie powinien zostać usunięty.

Możesz przejrzeć i usunąć te pliki dziennika śledzenia przy użyciu Eksploratora Windows, lub można wykonać niezbędne czynności następujący skrypt programu PowerShell:

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Zapisz skrypt w pliku z ". PS1 "rozszerzenia. Uruchom ten skrypt, zgodnie z potrzebami.

Aby dowiedzieć się więcej o związane z usługi Azure AD Connect GDPR wymagania, zobacz [w tym artykule](active-directory-aadconnect-gdpr.md).

### <a name="delete-authentication-agent-event-logs"></a>Usuń dzienniki zdarzeń uwierzytelniania agenta

Ten produkt może także utworzyć **dzienniki zdarzeń systemu Windows**. Aby dowiedzieć się więcej, należy przeczytać [w tym artykule](https://msdn.microsoft.com/library/windows/desktop/aa385780(v=vs.85).aspx).

Aby wyświetlić dzienniki związanych z agentem uwierzytelniania przekazywanego, otwórz **Podgląd zdarzeń** aplikacji na serwerze i sprawdź w obszarze **Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin usług i aplikacji** .

### <a name="delete-authentication-agent-trace-log-files"></a>Usuń pliki dziennika śledzenia Agent uwierzytelniania

Należy regularnie sprawdzać zawartości **%ProgramData%\Microsoft\Azure AD Connect Agent\Trace uwierzytelniania\**  i usuń zawartość tego folderu co 48 godzin. 

>[!IMPORTANT]
>Jeśli Agent uwierzytelniania usługa jest uruchomiona, nie będzie można usunąć bieżącego pliku dziennika w folderze. Zatrzymaj usługę przed podjęciem ponownej próby. Aby uniknąć błędów logowania użytkownika, należy została już skonfigurowana uwierzytelniania przekazywanego [wysokiej dostępności](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

Możesz przejrzeć i usunąć te pliki przy użyciu Eksploratora Windows lub można wykonać niezbędne czynności następujący skrypt:

```
$Files = ((Get-childitem -Path "$env:programdata\microsoft\azure ad connect authentication agent\trace" -Recurse).VersionInfo).FileName 
 
Foreach ($file in $files) { 
    {Remove-Item -Path $File -Force} 
}
```

Aby zaplanować tego skryptu do uruchomienia co 48 godzin, wykonaj następujące kroki:

1.  Zapisz skrypt w pliku z ". PS1 "rozszerzenia.
2.  Otwórz **Panelu sterowania** i wybierz polecenie **System i zabezpieczenia**.
3.  W obszarze **narzędzia administracyjne** nagłówek, kliknij pozycję "**harmonogram zadań**".
4.  W **harmonogram zadań**, kliknij prawym przyciskiem myszy "**Biblioteka Harmonogramu zadań**"i wybierz polecenie"**Utwórz o podstawowych zadań...** ".
5.  Wprowadź nazwę dla nowego zadania, a następnie kliknij przycisk **dalej**.
6.  Wybierz opcję "**codzienne**" dla **wyzwalacz zadania** i kliknij przycisk **dalej**.
7.  Ustaw cykl dwa dni, a następnie kliknij przycisk **dalej**.
8.  Wybierz opcję "**uruchomić program**" jako akcję i kliknij przycisk **dalej**.
9.  Typ "**PowerShell**"w polu dla skryptu lub programu, a w polu z etykietą"**Dodaj argumenty (opcjonalnie)**", wprowadź pełną ścieżkę do skryptu, który został utworzony wcześniej, a następnie kliknij przycisk **dalej**.
10. Następnym ekranie zawiera podsumowanie informacji o zadaniu, które zostały utworzone. Sprawdź wartości, a następnie kliknij przycisk **Zakończ** można utworzyć zadania:
 
### <a name="note-about-domain-controller-logs"></a>Należy pamiętać o dziennikach kontrolera domeny

Jeśli włączono rejestrowanie inspekcji, ten produkt może generować dzienniki zabezpieczeń dla kontrolerów domeny. Aby dowiedzieć się więcej o konfigurowaniu zasad inspekcji, przeczytaj to [artykułu](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>Kolejne kroki
* [Przejrzyj zasady Privacy firmy Microsoft w Centrum zaufania](https://www.microsoft.com/trustcenter)
- [**Rozwiązywanie problemów z** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) — Dowiedz się, jak rozwiązać typowe problemy z funkcją.
