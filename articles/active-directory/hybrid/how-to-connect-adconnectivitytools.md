---
title: 'Program Azure AD Connect: Co to jest moduł programu PowerShell ADConnectivityTool | Dokumentacja firmy Microsoft'
description: Ten dokument stanowi wprowadzenie nowego modułu programu ADConnectivity PowerShell
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b2471b28132b26c41844b620f6dcf49e3802a6e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60245660"
---
# <a name="what-is-the-adconnectivitytool-powershell-module"></a>Co to jest moduł programu PowerShell ADConnectivityTool?

Narzędzie ADConnectivity jest moduł programu PowerShell, używanym w jednym z następujących czynności:

- Podczas instalacji, gdy jest problem z łącznością sieciową, uniemożliwia pomyślne sprawdzanie poprawności poświadczeń usługi Active Directory użytkownika podane w kreatorze.
- Po zakończeniu instalacji przez użytkownika, który wywołuje funkcje w sesji programu PowerShell.

Narzędzie znajduje się w: **C:\Program Files\Microsoft Azure Active Directory Connect\Tools\ ADConnectivityTool.psm1** 

## <a name="adconnectivitytool-during-installation"></a>ADConnectivityTool podczas instalacji

Na **Podłączanie katalogów** stronie usługi Azure AD Connect kreatora, w sytuacji problem z siecią będzie ADConnectivityTool automatycznie użyj jednej z jej funkcji można określić, co się dzieje.  Jednym z następujących jest uznawana za problemy z siecią:

- Nazwa lasu z podanym użytkownikiem została błędnie wpisana lub wspomniane lasu nie istnieje. 
- UDP port 389 zostanie zamknięty w kontrolerach domeny skojarzone z lasem, użytkownik podał
- Poświadczenia podane w oknie "Konto lasu usługi AD" nie ma uprawnień do pobierania kontrolerów domeny skojarzonych z obiektem docelowym lasu
- Dowolne porty TCP 53, 88 lub 389 są zamknięte w kontrolerach domeny skojarzone z lasem, użytkownik podał 
- Zarówno UDP 389 i TCP port (lub portów) są zamknięte
- DNS nie można rozpoznać dla podanego lasu i/lub jej skojarzone kontrolerów domeny

Zawsze, gdy dowolne z tych problemów znajdują się, komunikat o błędzie powiązany jest wyświetlany w kreatora AADConnect:


![Błąd](media/how-to-connect-adconnectivitytools/error1.png)

Na przykład, gdy podejmujemy próbę Dodaj katalog na **Podłączanie katalogów** ekranu, program Azure AD Connect wymaga to zweryfikować i oczekuje, że może nawiązać komunikacji z kontrolerem domeny przez port 389.  Jeśli jest ona nieosiągalna, zobaczymy błędu, który jest wyświetlany w powyższym zrzucie ekranu.  

Co faktycznie się dzieje w tle jest wywoływany program Azure AD Connect `Start-NetworkConnectivityDiagnosisTools` funkcji.  Ta funkcja jest wywoływana, gdy sprawdzanie poprawności poświadczeń nie powiodło się z powodu problemu z łącznością sieciową.

Na koniec szczegółowy plik dziennika jest generowany w narzędziu jest wywoływana z poziomu kreatora. Dziennik znajduje się w **C:\ProgramData\AADConnect\ADConnectivityTool-\<daty >-\<czas > .log**

## <a name="adconnectivitytools-post-installation"></a>ADConnectivityTools po zakończeniu instalacji
Po zainstalowaniu usługi Azure AD Connect funkcje w module ADConnectivityTools PowerShell może służyć.  

Można znaleźć informacje na temat funkcji w [ADConnectivityTools odwołania](reference-connect-adconnectivitytools.md)

### <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

Firma Microsoft zamierza wyróżnienia tej funkcji, ponieważ jest to możliwe **tylko** można wywołać ręcznie po zaimportowaniu ADConnectivityTool.psm1 w programie PowerShell. 

Ta funkcja wykonuje tę samą logikę, jak usługa Azure AD Connect jest uruchomiony do sprawdzania poprawności podanych poświadczeń usługi AD.  Jednak zapewnia znacznie bardziej szczegółowy opis o problemie i sugerowane rozwiązanie. 

Walidacja łączności składa się z następujących czynności:
-   Pobierz obiekt Nazwa FQDN domeny (w pełni kwalifikowaną nazwę domeny)
-   Zweryfikuj, że jeśli użytkownik wybrał opcję "Utwórz nowe AD konto", te poświadczenia jest należeć do grupy Administratorzy przedsiębiorstwa
-   Obiekt nazwy FQDN lasu Get
-   Upewnij się, że co najmniej jedną domenę skojarzony z obiektem wcześniej uzyskanej FQDN lasu jest dostępny
-   Sprawdź, czy poziom funkcjonalności lasu systemu Windows Server 2003 lub nowszej.

Użytkownik będzie mógł dodać katalog, jeśli wszystkie te działania zostały wykonane pomyślnie.

Jeśli użytkownik uruchamia tej funkcji po problem został rozwiązany (lub nie ma problemu istnieje w ogóle) dane wyjściowe będą wskazywać dla użytkownika przejść z powrotem do usługi Azure AD Kreatora programu Connect i spróbuj ponownie włożyć poświadczenia.



## <a name="next-steps"></a>Następne kroki
- [Azure AD Connect: Konta i uprawnienia](reference-connect-accounts-permissions.md)
- [Instalacja ekspresowa](how-to-connect-install-express.md)
- [Instalacja niestandardowa](how-to-connect-install-custom.md)
- [Odwołanie ADConnectivityTools](reference-connect-adconnectivitytools.md)

