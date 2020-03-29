---
title: 'Usługa Azure AD Connect: Co to jest moduł programu ADConnectivityTool PowerShell | Dokumenty firmy Microsoft'
description: W tym dokumencie przedstawiono nowy moduł programu ADConnectivity PowerShell i sposób, w jaki można go używać do rozwiązywania problemów.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 4/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd5340cd8c802df4ffbe0207b5401d2fee4e207e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "64571112"
---
# <a name="troubleshoot-azure-ad-connectivity-with-the-adconnectivitytool-powershell-module"></a>Rozwiązywanie problemów z łącznością usługi Azure AD za pomocą modułu ADConnectivityTool PowerShell

Narzędzie ADConnectivity jest modułem programu PowerShell, który jest używany w jednym z następujących:

- Podczas instalacji, gdy problem z łącznością sieciową uniemożliwia pomyślne sprawdzenie poprawności poświadczeń usługi Active Directory podanych przez użytkownika w Kreatorze.
- Opublikuj instalację przez użytkownika, który wywołuje funkcje z sesji programu PowerShell.

Narzędzie znajduje się w pliku: **C:\Program Files\Microsoft Azure Active Directory Connect\Tools\ ADConnectivityTool.psm1** 

## <a name="adconnectivitytool-during-installation"></a>ADConnectivityTool podczas instalacji

Na stronie **Połącz katalogi** w Kreatorze usługi Azure AD Connect, jeśli wystąpi problem z siecią, narzędzie ADConnectivityTool automatycznie użyje jednej ze swoich funkcji, aby określić, co się dzieje.  Można rozważyć następujące problemy z siecią:

- Nazwa lasu, który podał użytkownik, została wpisana nieprawidłowo lub powiedziała, że Forest nie istnieje 
- Port UDP 389 jest zamknięty w kontrolerach domeny skojarzonych z lasem dostarczonym przez użytkownika
- Poświadczenia podane w oknie "Konto lasu usługi AD" nie mają uprawnień do pobierania kontrolerów domeny skojarzonych z lasem docelowym
- Każdy z portów TCP 53, 88 lub 389 jest zamknięty w kontrolerach domeny skojarzonych z lasem, który użytkownik podał 
- Zarówno port (lub porty TCP) UDP 389, jak i TCP są zamknięte
- Nie można rozpoznać systemu DNS dla podanych kontrolerów domeny Forest i\lub skojarzonych z nim kontrolerów domeny

Za każdym razem, gdy którykolwiek z tych problemów zostanie znaleziony, w Kreatorze AADConnect zostanie wyświetlony powiązany komunikat o błędzie:


![Błąd](media/how-to-connect-adconnectivitytools/error1.png)

Na przykład podczas próby dodania katalogu na ekranie **Połącz katalogi** usługa Azure AD Connect musi to zweryfikować i oczekuje, że będzie można komunikować się z kontrolerem domeny za pomocą portu 389.  Jeśli nie, zobaczymy błąd, który jest pokazany na powyższym zrzucie ekranu.  

Co faktycznie dzieje się w tle, jest to, że usługa Azure AD Connect wywołuje `Start-NetworkConnectivityDiagnosisTools` funkcję.  Ta funkcja jest wywoływana, gdy sprawdzanie poprawności poświadczeń kończy się niepowodzeniem z powodu problemu z łącznością sieciową.

Na koniec szczegółowy plik dziennika jest generowany za każdym razem, gdy narzędzie jest wywoływane z kreatora. Dziennik znajduje się w **pliku C:\ProgramData\AADConnect\ADConnectivityTool-\<\<data> - czas>.log**

## <a name="adconnectivitytools-post-installation"></a>ADConnectivityTools po instalacji
Po zainstalowaniu usługi Azure AD Connect można użyć dowolnej z funkcji modułu ADConnectivityTools PowerShell.  

Informacje referencyjne dotyczące funkcji można [ADConnectivityTools Reference](reference-connect-adconnectivitytools.md) znaleźć w

### <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

Będziemy wywoływać tę **funkcję,** ponieważ można ją wywołać ręcznie tylko po zaimportowaniu pliku ADConnectivityTool.psm1 do programu PowerShell. 

Ta funkcja wykonuje tę samą logikę, którą uruchamia Kreator połączenia usługi Azure AD w celu sprawdzenia poprawności podanych poświadczeń usługi AD.  Jednak zapewnia znacznie bardziej szczegółowe wyjaśnienie problemu i sugerowane rozwiązanie. 

Sprawdzanie poprawności łączności składa się z następujących kroków:
-   Pobierz obiekt FQDN domeny (w pełni kwalifikowana nazwa domeny)
-   Sprawdź, czy jeśli użytkownik wybierze opcję "Utwórz nowe konto USŁUGI AD", poświadczenia te należą do grupy Administratorzy przedsiębiorstwa
-   Pobierz obiekt ZNU WLE
-   Upewnij się, że co najmniej jedna domena skojarzona z wcześniej uzyskanym obiektem FQDN lasu jest osiągalna
-   Sprawdź, czy poziom funkcjonalności lasu to Windows Server 2003 lub wyższy.

Użytkownik będzie mógł dodać katalog, jeśli wszystkie te akcje zostały wykonane pomyślnie.

Jeśli użytkownik uruchamia tę funkcję po rozwiązaniu problemu (lub jeśli w ogóle nie występuje problem), dane wyjściowe wskażą, że użytkownik powróci do Kreatora usługi Azure AD Connect i spróbuj ponownie wstawić poświadczenia.



## <a name="next-steps"></a>Następne kroki
- [Azure AD Connect: Accounts and permissions](reference-connect-accounts-permissions.md) (Azure AD Connect: konta i uprawnienia)
- [Instalacja ekspresowa](how-to-connect-install-express.md)
- [Instalacja niestandardowa](how-to-connect-install-custom.md)
- [Informacje o module ADConnectivityTools](reference-connect-adconnectivitytools.md)

