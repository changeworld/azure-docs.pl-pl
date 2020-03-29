---
title: Usługa Azure AD Connect i prywatność użytkowników | Dokumenty firmy Microsoft
description: W tym dokumencie opisano sposób uzyskiwania zgodności RODO z usługą Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f5d3125b7b77e8ce7a943f640c44615049ab160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60455788"
---
# <a name="user-privacy-and-azure-ad-connect"></a>Prywatność użytkowników i usługa Azure AD Connect 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Ten artykuł dotyczy usługi Azure AD Connect i prywatności użytkowników.  Aby uzyskać informacje na temat usługi Azure AD Connect Health i prywatności użytkowników, zobacz artykuł [tutaj](reference-connect-health-user-privacy.md).

Zwiększ prywatność użytkowników w przypadku instalacji usługi Azure AD Connect na dwa sposoby:

1.  Na żądanie, wyodrębnić dane dla osoby i usunąć dane od tej osoby z instalacji
2.  Upewnij się, że żadne dane nie są przechowywane dłużej niż 48 godzin.

Zespół usługi Azure AD Connect zaleca drugą opcję, ponieważ jest znacznie łatwiejsze do zaimplementowania i utrzymania.

Serwer synchronizacji usługi Azure AD Connect przechowuje następujące dane dotyczące prywatności użytkownika:
1.  Dane o osobie w **bazie danych usługi Azure AD Connect**
2.  Dane w plikach **dziennika zdarzeń systemu Windows,** które mogą zawierać informacje o osobie
3.  Dane w **plikach dziennika instalacji usługi Azure AD Connect,** które mogą zawierać informacje o osobie

Klienci usługi Azure AD Connect powinni używać następujących wskazówek podczas usuwania danych użytkownika:
1.  Regularnie usuwaj zawartość folderu zawierającego pliki dziennika instalacji usługi Azure AD Connect — co najmniej co 48 godzin
2.  Ten produkt może również tworzyć dzienniki zdarzeń.  Aby dowiedzieć się więcej o dziennikach dzienników zdarzeń, zapoznaj się z [dokumentacją tutaj](https://msdn.microsoft.com/library/windows/desktop/aa385780.aspx).

Dane o osobie są automatycznie usuwane z bazy danych usługi Azure AD Connect, gdy dane tej osoby są usuwane z systemu źródłowego, z którego pochodzą. Nie jest wymagane żadne konkretne działania ze strony administratorów, aby były zgodne z RODO.  Jednak wymaga, aby dane usługi Azure AD Connect były synchronizowane ze źródłem danych co najmniej co dwa dni.

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>Usuwanie zawartości folderu dziennika instalacji usługi Azure AD Connect
Regularnie sprawdzaj i usuwaj zawartość folderu **c:\programdata\aadconnect** – z wyjątkiem pliku **PersistedState.Xml.** Ten plik zachowuje stan poprzedniej instalacji usługi Azure A Connect i jest używany podczas wykonywania instalacji uaktualnienia. Ten plik nie zawiera żadnych danych o osobie i nie powinien być usuwany.

>[!IMPORTANT]
>Nie należy usuwać pliku PersistedState.xml.  Ten plik nie zawiera żadnych informacji o użytkowniku i zachowuje stan poprzedniej instalacji.

Można przejrzeć i usunąć te pliki za pomocą Eksploratora Windows lub użyć skryptu, takiego jak następujące, aby wykonać niezbędne akcje:


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>Zaplanuj uruchamianie tego skryptu co 48 godzin
Poniższe kroki można zaplanować uruchamianie skryptu co 48 godzin.

1.  Zapisz skrypt w pliku z rozszerzeniem **&#46;PS1**, a następnie otwórz Panel sterowania i kliknij **systemy i zabezpieczenia**.
    ![System](./media/reference-connect-user-privacy/gdpr2.png)

2.  W nagłówku Narzędzia administracyjne kliknij pozycję **Zaplanuj zadania**.
    ![Zadanie podrzędne](./media/reference-connect-user-privacy/gdpr3.png)
3.  W Harmonogramie zadań kliknij prawym przyciskiem myszy **bibliotekę harmonogramu zadań** i kliknij pozycję **Utwórz zadanie podstawowe...**
4.  Wprowadź nazwę nowego zadania i kliknij przycisk **Dalej**.
5.  Wybierz **opcję Codziennie** dla wyzwalacza zadania i kliknij przycisk **Dalej**.
6.  Ustaw cykl na **2 dni** i kliknij przycisk **Dalej**.
7.  Wybierz **pozycję Uruchom program** jako akcję i kliknij przycisk **Dalej**.
8.  Wpisz program **PowerShell** w polu programu/skryptu, a w polu **Dodaj argumenty (opcjonalnie)** wprowadź pełną ścieżkę do skryptu utworzonego wcześniej, a następnie kliknij przycisk **Dalej**.
9.  Na następnym ekranie zostanie wyświetlenie podsumowania zadania, które zamierzasz utworzyć. Sprawdź wartości i kliknij przycisk **Zakończ,** aby utworzyć zadanie.



## <a name="next-steps"></a>Następne kroki
* [Zapoznaj się z zasadami zachowania poufności firmy Microsoft w Centrum zaufania](https://www.microsoft.com/trustcenter)
* [Kondycja usługi Azure AD Connect i prywatność użytkowników](reference-connect-health-user-privacy.md)
