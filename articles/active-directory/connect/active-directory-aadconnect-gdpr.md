---
title: Azure AD Connect i użytkownika prywatności | Dokumentacja firmy Microsoft
description: Ten dokument zawiera opis sposobu uzyskiwania GDPR zgodności z programem Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: billmath
ms.openlocfilehash: a1fa7f58040b420bf52d89a57b1234416c2fb939
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="user-privacy-and-azure-ad-connect"></a>Zasady zachowania poufności użytkownika i Azure AD Connect 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Ten artykuł dotyczy usługi Azure AD Connect i użytkownika prywatności.  Aby uzyskać informacje dotyczące usługi Azure AD Connect Health i użytkownika prywatności, zobacz artykuł [tutaj](../../active-directory/connect-health/active-directory-aadconnect-health-gdpr.md).

Zgodność prywatności użytkownika dla instalacji usługi Azure AD Connect można połączyć się z na dwa sposoby:

1.  Na żądanie wyodrębniania danych osoby i usuwanie danych z tej osoby z instalacji
2.  Upewnij się, że żadne dane nie są przechowywane poza 48 godzin.

Zespół usługi Azure AD Connect zaleca druga opcja, ponieważ jest znacznie łatwiejsze do wdrożenia i konserwacji.

Następujące dane prywatności użytkownika przechowywane na serwerze synchronizacji Azure AD Connect:
1.  Dane dotyczące osoby w **bazy danych Azure AD Connect**
2.  Dane w **dziennika zdarzeń systemu Windows** pliki zawierające informacje dotyczące osoby.
3.  Dane w **pliki dziennika instalacji Azure AD Connect** zawierających temat osoby

Klienci usługi Azure AD Connect usunięcie danych użytkownika należy używać następujących wytycznych:
1.  Usuń zawartość folderu, który zawiera pliki dziennika instalacji Azure AD Connect na bieżąco — co najmniej raz 48 godzin.
2.  Ten produkt może także utworzyć dzienników zdarzeń.  Aby dowiedzieć się więcej o dziennikach dzienników zdarzeń, zobacz [dokumentacji tutaj](https://msdn.microsoft.com/library/windows/desktop/aa385780.aspx).

Dane dotyczące osoby zostanie automatycznie usunięta z bazy danych programu Azure AD Connect po usunięciu tej osoby danych z którego pochodzi z systemu źródłowego. Żadna konkretna akcja ze strony administratorów musi być zgodny z GDPR.  Jednak wymaga, aby danych Azure AD Connect jest zsynchronizowany z źródła danych, co co najmniej dwa dni.

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>Usuń zawartość folderu pliku dziennika instalacji Azure AD Connect
Regularnie Sprawdź i usunąć zawartość **c:\programdata\aadconnect** folderu — z wyjątkiem **PersistedState.Xml** pliku. Ten plik zachowuje swój stan poprzedniej instalacji programu Azure A Connect i jest używany podczas instalacji uaktualnienia. Ten plik nie zawiera żadnych danych dotyczących osoby i nie należy go usunąć.

>[!IMPORTANT]
>Nie należy usuwać pliku PersistedState.xml.  Ten plik nie zawiera żadnych informacji użytkownika i przechowuje informacje o stanie poprzedniej instalacji.

Możesz przejrzeć i usunąć te pliki przy użyciu Eksploratora Windows lub można wykonać niezbędne czynności skryptu, takiego jak następujące:


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>Planowanie tego skryptu do uruchomienia co 48 godzin
Wykonaj następujące kroki, aby zaplanować uruchomienie skryptu co 48 godzin.

1.  Zapisz skrypt w pliku z rozszerzeniem  **&#46;PS1**, otwórz Panel sterowania i kliknij pozycję **systemów i zabezpieczeń**.
    ![System](media\active-directory-aadconnect-gdpr\gdpr2.png)

2.  W pozycji Narzędzia administracyjne kliknij **harmonogram zadań**.
    ![Zadanie podrzędne](media\active-directory-aadconnect-gdpr\gdpr3.png)
3.  W harmonogramie zadań, kliknij prawym przyciskiem myszy **Biblioteka Harmonogramu zadań** i wybierz polecenie **Tworzenie podstawowych zadań...**
4.  Wprowadź nazwę dla nowego zadania, a następnie kliknij przycisk **dalej**.
5.  Wybierz **codzienne** wyzwalacz zadania i kliknij na **dalej**.
6.  Wartość cyklu **2 dni** i kliknij przycisk **dalej**.
7.  Wybierz **uruchomić program** jako akcji, a następnie kliknij polecenie **dalej**.
8.  Typ **PowerShell** pole dla skryptu lub programu, a w polu z etykietą **Dodaj argumenty (opcjonalnie)**, wprowadź pełną ścieżkę do skryptu, który został utworzony wcześniej, a następnie kliknij przycisk **dalej**.
9.  Następnym ekranie zawiera podsumowanie informacji o zadaniu, które zostały utworzone. Sprawdź wartości, a następnie kliknij przycisk **Zakończ** do utworzenia zadania.



## <a name="next-steps"></a>Kolejne kroki
* [Przejrzyj zasady Privacy firmy Microsoft w Centrum zaufania](https://www.microsoft.com/trustcenter)
- [Azure AD Connect Health i zasady zachowania poufności użytkownika](../../active-directory/connect-health/active-directory-aadconnect-health-gdpr.md)
