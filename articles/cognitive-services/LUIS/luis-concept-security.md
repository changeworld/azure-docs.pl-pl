---
title: Zrozumienie dostęp do aplikacji LUIS - Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia LUIS dostępu.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 44380e12e6d095e8d40675af0b6b2fddc5e4c4e9
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264271"
---
# <a name="authoring-and-endpoint-user-access"></a>Dostęp użytkownika do tworzenia i punktu końcowego
Tworzenie dostępu jest dostępna dla właścicieli i współpracowników. Prywatne aplikacji dostęp do punktu końcowego jest dostępna dla właścicieli i współpracowników. W przypadku publicznych aplikacji dostępu do punktu końcowego jest dostępne dla wszystkich użytkowników, który ma własne konto LUIS i ma identyfikator aplikacji publicznych. 

## <a name="access-to-authoring"></a>Dostęp do tworzenia
Dostęp do aplikacji z [LUIS] [ LUIS] witryny sieci Web lub [tworzenia interfejsów API](https://aka.ms/luis-authoring-apis) jest kontrolowany przez właściciela aplikacji. 

Właściciel i wszystkie współpracownicy mają dostęp do tworzenia aplikacji. 

|Obejmuje tworzenie dostępu|Uwagi|
|--|--|
|Dodawanie lub usuwanie kluczy punktu końcowego||
|Eksportowanie wersji||
|Eksportuj dzienniki punktu końcowego||
|Importowanie wersji||
|Opublikowania aplikacji|Gdy aplikacja jest publiczny, każda osoba mająca tworzenia lub punkt końcowy klucz zapytania aplikacji.|
|Modyfikowanie modelu|
|Publikowanie|
|Przejrzyj zniesławiających punktu końcowego dla [active learning](label-suggested-utterances.md)|
|Uczenie|

## <a name="access-to-endpoint"></a>Dostęp do punktu końcowego
Dostęp do punktu końcowego w zapytaniu LUIS jest kontrolowany przez **publicznego** ustawienie aplikacji **ustawienia** strony. Prywatne aplikacji punktu końcowego zapytania jest sprawdzany pod kątem autoryzowanego klucza z pozostałymi trafień przydziału. Zapytanie punktu końcowego publicznych aplikacji musi również podać klucz punktu końcowego (od kto wysłał zapytanie) który również jest sprawdzany pozostałych trafień przydziału. 

Klucz punktu końcowego jest przekazywany w ciąg zapytania żądania GET lub nagłówek ogłoszenia na żądanie.

![Zestaw aplikacji do publicznego](./media/luis-concept-security/set-application-as-public.png)

### <a name="private-app-endpoint-security"></a>Zabezpieczenia punktu końcowego prywatnej aplikacji
Punkt końcowy prywatnej aplikacji jest dostępna tylko do następującego:

|Klucz i użytkownika|Wyjaśnienie|
|--|--|--|
|Klucz tworzenia właściciela| Maksymalnie 1000 punktu końcowego trafień|
|Współpracownicy tworzenia kluczy| Maksymalnie 1000 punktu końcowego trafień|
|Klucze punktu końcowego dodane z **[publikowania](publishapp.md)** strony|Właściciel i współpracownicy można dodać punktu końcowego kluczy|

Innych kluczy o przygotowaniu lub punkt końcowy ma **nie** dostępu.

### <a name="public-app-endpoint-access"></a>Dostęp do punktu końcowego aplikacji publicznych
Konfigurowanie aplikacji jako **publicznego** na **ustawienia** strony aplikacji. Po skonfigurowaniu aplikacji jako public, _żadnych_ prawidłowy LUIS tworzenia klucza lub klucza punktu końcowego LUIS można zbadać aplikacji, tak długo, jak klucz nie został użyty przydziału całego punktu końcowego.

Użytkownik, który nie jest właścicielem lub współpracownika, ma dostęp tylko do aplikacji publicznych Jeśli podany identyfikator aplikacji. LUIS nie ma publicznego _rynku_ lub w inny sposób, aby wyszukać publicznych aplikacji.  

## <a name="microsoft-user-accounts"></a>Konta Microsoft
Autorzy i współpracownicy mogą zwiększyć klucze LUIS na stronie publikowania. Konto użytkownika firmy Microsoft, który tworzy klucz LUIS w portalu Azure musi właścicielem aplikacji lub współpracownika aplikacji. 

<!--
### Individual consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then you can provide individual consent as part of the login process. 

### Administrator consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then the administrator can give individual consent via the method discussed in this [blog](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/). 
-->
## <a name="securing-the-endpoint"></a>Zabezpieczanie punktu końcowego 
Można kontrolować, kto może zobaczyć klucz punktu końcowego LUIS wywołując w środowisku do serwera. LUIS korzystają z robotów, połączenie między bot i LUIS jest już bezpieczne. Bezpośrednie wywoływanie LUIS punktu końcowego należy utworzyć interfejs API po stronie serwera (takich jak Azure [funkcja](https://azure.microsoft.com/services/functions/)) z kontrolą dostępu (takich jak [AAD](https://azure.microsoft.com/services/active-directory/)). Gdy jest wywoływana po stronie serwera interfejsu API i uwierzytelnianie i autoryzacja są weryfikowane, Przekaż wywołania do LUIS. Gdy ta strategia nie zapobiegać atakom man-in--middle, zaciemnia punktu końcowego od użytkowników, pozwala na śledzenie dostępu i umożliwia dodawanie punktu końcowego odpowiedzi rejestrowania (takich jak [usługi Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="security-compliance"></a>Zgodności zabezpieczeń
ISO 27001: 2013 i inspekcji 27018:2014 ISO z ZERO niezgodności (wyniki) w raporcie inspekcji LUIS pomyślnie ukończona. Ponadto LUIS również uzyskać CSA certyfikacji GWIAZDY z najwyższym możliwe udzielania Gold dojrzałości oceny możliwości. Azure to dostawcy usług w chmurze publicznej tylko głównych zdobyć tego certyfikatu. Aby uzyskać więcej informacji można znaleźć LUIS zawarte w instrukcji scope zaktualizowane w głównym Azure [zgodność](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) dokument, który odwołuje się do [Centrum zaufania](https://www.microsoft.com/en-us/trustcenter/compliance/iso-iec-27001) stron ISO.  

## <a name="next-steps"></a>Kolejne kroki

Zobacz [najlepsze rozwiązania w zakresie](luis-concept-best-practices.md) Aby dowiedzieć się, jak używać intencje i jednostek dla prognoz najlepsze.

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website