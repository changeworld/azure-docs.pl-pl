---
title: Omówienie dostępu do aplikacji usługi LUIS — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dostęp do tworzenia usługi LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry
ms.openlocfilehash: 13b769a0b5a940e0f3dd5f2e0cc3567d9879ee0d
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223783"
---
# <a name="authoring-and-endpoint-user-access"></a>Dostęp użytkownika do tworzenia i punktu końcowego
Tworzenie dostępu jest dostępna dla właścicieli i współpracowników. Dla aplikacji sieci prywatnej dostępu do punktu końcowego jest dostępna dla właścicieli i współpracowników. W przypadku publicznych aplikacji dostęp do punktu końcowego jest dostępny dla wszystkich, ma swoje własne konto usługi LUIS, która zawiera identyfikator aplikacji publicznych. 

## <a name="access-to-authoring"></a>Dostęp do tworzenia
Dostęp do aplikacji z [LUIS](luis-reference-regions.md#luis-website) witryny sieci Web lub [Tworzenie interfejsów API](https://aka.ms/luis-authoring-apis) jest kontrolowana przez właściciela aplikacji. 

Właściciela i współpracowników wszystkie mają dostęp do tworzenia aplikacji. 

|Tworzenie dostępu zawiera|Uwagi|
|--|--|
|Dodawanie lub usuwanie punktu końcowego kluczy||
|Eksportowanie wersji||
|Eksportuj dzienniki punktu końcowego||
|Importowanie wersji||
|Upublicznić aplikacji|Gdy aplikacja jest publiczny, każda osoba z kluczem tworzenia lub punkt końcowy kwerendy aplikacji.|
|Modyfikowanie modelu|
|Publikowanie|
|Przejrzyj wypowiedzi punktu końcowego dla [aktywne uczenie](luis-how-to-review-endoint-utt.md)|
|Szkolenie|

## <a name="access-to-endpoint"></a>Dostęp do punktu końcowego
Dostęp do punktu końcowego do zapytania usługi LUIS jest kontrolowany przez **publicznych** ustawienie aplikacji **ustawienia** strony. Zapytanie o punkt końcowy aplikacji prywatnych są sprawdzane pod kątem autoryzowanego klucza z pozostałymi trafień limitu przydziału. Zapytanie punktu końcowego aplikacji publicznej musi również podać klucz punktu końcowego (z spojrzenia wykonuje zapytanie) który również jest sprawdzane dla pozostałych trafień limitu przydziału. 

Klucz punktu końcowego jest przekazywany w ciąg zapytania żądania GET projektu i zlecania nagłówek wpisu.

![Zestaw aplikacji na publiczną](./media/luis-concept-security/set-application-as-public.png)

### <a name="private-app-endpoint-security"></a>Zabezpieczenia punktu końcowego aplikacji prywatnych
Punkt końcowy aplikacji prywatnych jest dostępna tylko dla następujących czynności:

|Klucz i użytkownika|Wyjaśnienie|
|--|--|--|
|Klucz tworzenia właściciela| Maksymalnie 1000 punkt końcowy trafień|
|Współpracowników tworzenia kluczy| Maksymalnie 1000 punkt końcowy trafień|
|Punkt końcowy klucze dodane z **[Publikuj](luis-how-to-publish-app.md)** strony|Właściciela i współpracowników dodać klucze punktu końcowego|

Inne klucze tworzenia lub punkt końcowy ma **nie** dostępu.

### <a name="public-app-endpoint-access"></a>Dostęp do punktu końcowego publicznego aplikacji
Konfigurowanie aplikacji jako **publicznych** na **ustawienia** strony aplikacji. Po skonfigurowaniu aplikacji jako publiczne, _wszelkie_ prawidłowe LUIS tworzenia klucza lub klucza punktu końcowego usługi LUIS mogą wysyłać zapytania swojej aplikacji, tak długo, jak klucz nie został użyty przydział całego punktu końcowego.

Użytkownik, który nie jest właścicielem lub współpracownika, dostęp tylko do aplikacji publicznej Jeśli podany identyfikator aplikacji. Usługa LUIS nie ma publicznej _rynku_ lub w inny sposób wyszukiwania dla aplikacji w sieci publicznej.  

## <a name="microsoft-user-accounts"></a>Konta użytkowników firmy Microsoft
Autorzy i współpracowników dodać klucze do usługi LUIS na stronie publikowania. Konto Microsoft, które tworzy klucz usługi LUIS w witrynie Azure portal musi właściciela aplikacji lub Współautor aplikacji. 

Zobacz [użytkownik dzierżawy usługi Azure Active Directory](luis-how-to-account-settings.md#azure-active-directory-tenant-user) Aby dowiedzieć się więcej na temat kont użytkowników usługi Active Directory. 

<!--
### Individual consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then you can provide individual consent as part of the login process. 

### Administrator consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then the administrator can give individual consent via the method discussed in this [blog](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/). 
-->
## <a name="securing-the-endpoint"></a>Zabezpieczanie punktu końcowego 
Można kontrolować, kto może wyświetlać klucza punktu końcowego usługi LUIS, wywołując ją w środowisku serwer serwer. Usługa LUIS korzystają z robota, połączenie między bot i LUIS jest już bezpieczne. Bezpośrednie wywoływanie punktu końcowego usługi LUIS należy utworzyć interfejs API po stronie serwera (takich jak Azure [funkcja](https://azure.microsoft.com/services/functions/)) przy użyciu kontrolowanego dostępu (takich jak [AAD](https://azure.microsoft.com/services/active-directory/)). Gdy wywoływany jest interfejs API po stronie serwera i uwierzytelnianie i autoryzacja są weryfikowane, Przekaż wywołań do usługi LUIS. Chociaż ta strategia nie zapobiegać atakom typu man-in--middle, zaciemnia punktu końcowego od swoich użytkowników, pozwala na śledzenie dostępu i umożliwia dodawanie rejestrowania odpowiedzi punktu końcowego (takie jak [usługi Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="security-compliance"></a>Zgodności zabezpieczeń
Usługa LUIS zakończona pomyślnie, ISO 27001: 2013 i inspekcji 27018:2014 ISO z ZEROWĄ niezgodności (wyniki) w raporcie inspekcji. Ponadto usługa LUIS również uzyskać certyfikacji STAR CSA z najwyższą możliwe Award Gold dojrzałości możliwości oceny. Azure jest chmura publiczna jedyny duży dostawca usług w zdobyć ten certyfikat. Aby uzyskać więcej informacji można znaleźć usługi LUIS zawarte w instrukcji scope zaktualizowane w głównym platformy Azure [zgodność](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) dokument, który odwołuje się do [Centrum zaufania](https://www.microsoft.com/en-us/trustcenter/compliance/iso-iec-27001) stron ISO.  

## <a name="next-steps"></a>Kolejne kroki

Zobacz [najlepszych rozwiązań](luis-concept-best-practices.md) dowiesz się, jak na potrzeby prognoz najlepszych intencje i podmioty.