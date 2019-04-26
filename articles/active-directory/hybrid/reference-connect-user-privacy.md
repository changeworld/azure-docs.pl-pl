---
title: Usługa Azure AD Connect i użytkownika zachowania | Dokumentacja firmy Microsoft
description: W tym dokumencie opisano sposób uzyskiwania zgodności z rozporządzeniem RODO za pomocą usługi Azure AD Connect.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60455788"
---
# <a name="user-privacy-and-azure-ad-connect"></a>Prywatność użytkownika i program Azure AD Connect 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>W tym artykule dotyczy usługi Azure AD Connect i użytkownika ochrony prywatności.  Instrukcje dotyczące usługi Azure AD Connect Health i użytkownika ochrony prywatności, zobacz artykuł [tutaj](reference-connect-health-user-privacy.md).

Poprawa ochrony prywatności użytkowników w przypadku instalacji usługi Azure AD Connect na dwa sposoby:

1.  Na żądanie wyodrębnianie danych dla osoby i usunąć dane z tej osoby z instalacji
2.  Upewnij się, że żadne dane nie są przechowywane dłużej niż 48 godzin.

Zespół usługi Azure AD Connect zaleca druga opcja, ponieważ jest znacznie łatwiejsze do wdrożenia i konserwacji.

Następujące dane ochrony prywatności użytkowników są przechowywane w serwera usługi Azure AD Connect sync:
1.  Dane o osobie w **bazy danych usługi Azure AD Connect**
2.  Dane w **dziennika zdarzeń Windows** pliki zawierające informacje o osobie
3.  Dane w **pliki dzienników instalacji program Azure AD Connect** zawierających temat osoby

Klienci usługi Azure AD Connect należy używać następujących wytycznych podczas usuwania danych użytkownika:
1.  Usuń zawartość folderu, który zawiera pliki dziennika instalacji program Azure AD Connect w regularnych odstępach czasu — co najmniej raz 48 godzin
2.  Ten produkt może również tworzyć dzienniki zdarzeń.  Aby dowiedzieć się więcej na temat dzienników dzienniki zdarzeń, zobacz [w tej dokumentacji](https://msdn.microsoft.com/library/windows/desktop/aa385780.aspx).

Po usunięciu danych przez osobę z systemu źródłowego, z którego pochodzi z danych dotyczących osoby zostanie automatycznie usunięty z bazy danych programu Azure AD Connect. Brak określonej czynności z administratorów musi być zgodny z RODO.  Jednak wymaga, że danych usługi Azure AD Connect jest zsynchronizowany z źródła danych, co dwa dni.

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>Usuń zawartość folderu pliku dziennika instalacji program Azure AD Connect
Regularne sprawdzanie i usunąć zawartość **c:\programdata\aadconnect** folder — z wyjątkiem **PersistedState.Xml** pliku. Ten plik przechowuje informacje o stanie poprzedniej instalacji programu Azure Connect i jest używany, gdy przeprowadzane jest uaktualnianie instalacji. Ten plik nie zawiera żadnych danych dotyczących osoby i nie należy go usunąć.

>[!IMPORTANT]
>Nie należy usuwać plików PersistedState.xml.  Ten plik nie zawiera żadnych informacji użytkownika i przechowuje informacje o stanie poprzedniej instalacji.

Możesz przejrzeć i usunąć te pliki przy użyciu Eksploratora Windows lub skryptu, podobnie do poniższego umożliwia wykonać niezbędne czynności:


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>Planowanie tego skryptu do uruchomienia każdego 48 godzin
Wykonaj następujące kroki, aby zaplanować skrypt do uruchomienia każdego 48 godzin.

1.  Zapisz skrypt w pliku z rozszerzeniem  **&#46;PS1**, a następnie otwórz Panel sterowania i kliknij pozycję **systemów i zabezpieczeniami**.
    ![System](./media/reference-connect-user-privacy/gdpr2.png)

2.  W pozycji Narzędzia administracyjne kliknij **harmonogram zadań**.
    ![Zadanie podrzędne](./media/reference-connect-user-privacy/gdpr3.png)
3.  W harmonogramie zadań, kliknij prawym przyciskiem myszy **Biblioteka Harmonogramu zadań** i kliknij pozycję **utworzyć podstawowe zadania...**
4.  Wprowadź nazwę dla nowego zadania, a następnie kliknij przycisk **dalej**.
5.  Wybierz **codzienne** wyzwalacz zadania i kliknij pozycję **dalej**.
6.  Wartość cyklu **2 dni** i kliknij przycisk **dalej**.
7.  Wybierz **uruchomić program** jako akcji, a następnie kliknij pozycję **dalej**.
8.  Typ **PowerShell** pole dla programu/skryptu i w polu etykietą **Dodaj argumenty (opcjonalne)**, wprowadź pełną ścieżkę do skryptu, który został utworzony wcześniej, a następnie kliknij przycisk **dalej**.
9.  Następny ekran pokazuje, podsumowania zadania, które chcesz utworzyć. Sprawdź wartości, a następnie kliknij przycisk **Zakończ** do utworzenia zadania.



## <a name="next-steps"></a>Kolejne kroki
* [Przegląd zasad Privacy firmy Microsoft w Centrum zaufania](https://www.microsoft.com/trustcenter)
* [Azure AD Connect Health i ochrony prywatności użytkowników](reference-connect-health-user-privacy.md)
