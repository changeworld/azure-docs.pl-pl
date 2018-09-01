---
title: Narzędzie Pomocnik dostosowywania interfejsu użytkownika strony w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Narzędzie pomocnika używany do przedstawiania funkcji dostosowywania interfejsu użytkownika strony w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/07/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1a37a37dbed3b5ef9733f1105444529b4d255bcf
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43336785"
---
# <a name="azure-active-directory-b2c-a-helper-tool-used-to-demonstrate-the-page-user-interface-ui-customization-feature"></a>Usługa Azure Active Directory B2C: Pomocnika narzędzie służące do zademonstrowania funkcji dostosowywania interfejsu użytkownika strony
W tym artykule jest uzupełnieniem do [główny artykuł dostosowywania interfejsu użytkownika](active-directory-b2c-reference-ui-customization.md) w usłudze Azure Active Directory (Azure AD) B2C. Poniżej opisano sposób wykonywania funkcji dostosowywania interfejsu użytkownika strony za pomocą przykładowej zawartości do obsługi kodu HTML i CSS, udostępniliśmy.

## <a name="get-an-azure-ad-b2c-tenant"></a>Uzyskiwanie dzierżawy usługi Azure AD B2C
Zanim będzie można dostosować niczego, konieczne będzie [Uzyskiwanie dzierżawy usługi Azure AD B2C](active-directory-b2c-get-started.md) Jeśli nie masz jeszcze jeden.

## <a name="create-a-sign-up-or-sign-in-policy"></a>Tworzenie zasad rejestracji lub logowania
Przykłady udostępniliśmy może służyć do customze dwóch stron w [zasad rejestracji lub logowania](active-directory-b2c-reference-policies.md): [ujednolicona strona logowania](active-directory-b2c-reference-ui-customization.md) i [strony samodzielnie atrybuty](active-directory-b2c-reference-ui-customization.md). Gdy [Tworzenie zasad rejestracji lub logowania](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy), Dodaj konta lokalnego (adres e-mail), Facebook, Google i Microsoft jako **dostawców tożsamości**. Są to tylko dostawców tożsamości, które akceptują naszej przykładowej zawartości HTML.  Można również dodać podzbiór tych dostawców tożsamości, w razie potrzeby.

## <a name="register-an-application"></a>Zarejestruj aplikację
Konieczne będzie [rejestrowania aplikacji](active-directory-b2c-app-registration.md) w dzierżawie B2C, który może służyć do wykonywania swoich zasad. Po zarejestrowaniu aplikacji, masz kilka opcji umożliwiających uruchamiany zasadach rejestracji:

* Tworzenie jednej z aplikacji szybkiego startu wymienionych w sekcji "Wprowadzenie" usługi Azure AD B2C [Zaloguj się i logowanie użytkowników w aplikacjach](active-directory-b2c-overview.md).
* Użyj wstępnie utworzonych [Plac zabaw dla usługi Azure AD B2C](https://aadb2cplayground.azurewebsites.net) aplikacji. Jeśli zdecydujesz się używać placu zabaw, muszą zarejestrować aplikację w dzierżawie B2C, za pomocą **identyfikator URI przekierowania** `https://aadb2cplayground.azurewebsites.net/`.
* Użyj **Uruchom teraz** przycisk na zasady w [witryny Azure portal](https://portal.azure.com/).

## <a name="customize-your-policy"></a>Dostosuj zasady
Aby dostosować wygląd i działanie zasad, musisz najpierw utworzyć pliki HTML i CSS przy użyciu określonych konwencji usługi Azure AD B2C. Następnie możesz przekazać zawartości statycznej publicznie dostępnych lokalizacji, aby usługa Azure AD B2C do niego dostęp. Może to być własne dedykowanym serwerze sieci web, usługi Azure Blob Storage, Azure Content Delivery Network lub jakikolwiek inny statyczny hostowania zasobów dostawca. Jedynymi wymogami są, że zawartość jest dostępna za pośrednictwem protokołu HTTPS i jest możliwy przy użyciu mechanizmu CORS. Po zostały ujawnione statycznej zawartości w sieci web, możesz edytować zasady, aby wskazywało tę lokalizację i prezentowanie zawartości do klientów. [Główny artykuł dostosowywania interfejsu użytkownika](active-directory-b2c-reference-ui-customization.md) opisano szczegółowo, jak działa funkcja dostosowania usługi Azure AD B2C.

Do celów tego samouczka opracowaliśmy już niektóre przykładowej zawartości i hostowanej go w usłudze Azure Blob Storage. Przykładowej zawartości jest bardzo proste dostosowanie w motywie fikcyjnej firmy "Wingtip Toys". Aby wypróbować tę funkcję w własnych zasad, wykonaj następujące kroki:

1. Zaloguj się do dzierżawy na [witryny Azure portal](https://portal.azure.com/) i przejdź do bloku funkcji B2C.
2. Kliknij przycisk **zasady rejestracji lub logowania**kliknij zasady, a po edycji (na przykład "b2c\_1\_logowania\_się\_logowania\_w").
3. Kliknij przycisk **dostosowanie interfejsu użytkownika strony** i następnie **ujednolicona strona rejestracji lub logowania**.
4. Przełącz **Użyj niestandardowej strony** przełączyć się do **tak**. W **identyfikator URI strony niestandardowe** wprowadź `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/unified.html`. Kliknij przycisk **OK**.
5. Kliknij przycisk **strona rejestracji dla kont lokalnych**. Przełącz **Użyj niestandardowego szablonu** przełączyć się do **tak**. W **identyfikator URI strony niestandardowe** wprowadź `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/selfasserted.html`.
6. Powtórz ten sam punkt dla **stronę rejestracji konta w sieci społecznościowej**.
   Kliknij przycisk **OK** dwa razy, aby zamknąć bloki dostosowywania interfejsu użytkownika.
7. Kliknij pozycję **Zapisz**.

Teraz możesz wypróbować dostosowane zasady. Możesz użyć swojej aplikacji lub Plac zabaw dla usługi Azure AD B2C, jeśli chcesz, ale możesz też po prostu kliknąć **Uruchom teraz** polecenia w bloku zasad. Wybierz swoją aplikację w polu listy rozwijanej i wybierz odpowiedni identyfikator URI przekierowania. Kliknij przycisk **Uruchom teraz** przycisku. Zostanie otwarta nowa karta przeglądarki i możesz uruchomić za pomocą interfejsu użytkownika rejestrację w usłudze aplikacji przy użyciu nowej zawartości w miejscu!

## <a name="upload-the-sample-content-to-azure-blob-storage"></a>Przekaż przykładowej zawartości do usługi Azure Blob Storage
Jeśli chcesz używać magazynu obiektów Blob Azure do hostowania zawartości strony, możesz utworzyć konto magazynu i przekazywanie plików za pomocą narzędzia pomocnika naszej usługi B2C.

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Kliknij przycisk **+ nowy** > **dane + magazyn** > **konta magazynu**. Konieczne będzie subskrypcji platformy Azure, aby utworzyć konto usługi Azure Blob Storage. Możesz utworzyć konto bezpłatnej wersji próbnej w [witryny sieci Web Azure](https://azure.microsoft.com/pricing/free-trial/).
3. Podaj **nazwa** magazynu konta (na przykład "contoso") i wybierz odpowiednie opcje dla **warstwa cenowa**, **grupy zasobów** i  **Subskrypcja**. Upewnij się, że masz **Przypnij do tablicy startowej** zaznaczoną opcją. Kliknij pozycję **Utwórz**.
4. Wróć do tablicy startowej, a następnie kliknij konto magazynu, który został utworzony.
5. W **Podsumowanie** kliknij **kontenery**, a następnie kliknij przycisk **+ Dodaj**.
6. Podaj **nazwa** kontenera (na przykład "b2c") i wybierz **Blob** jako **dostęp typu**. Kliknij przycisk **OK**.
7. Kontener, który został utworzony pojawi się na liście na **obiektów blob** bloku. Zanotuj adres URL kontenera; na przykład powinien wyglądać podobnie do `https://contoso.blob.core.windows.net/b2c`. Zamknij **obiektów blob** bloku.
8. W bloku konta magazynu, kliknij przycisk **klucze** i zanotuj wartości **nazwa konta magazynu** i **podstawowy klucz dostępu** pola.

> [!NOTE]
> **Podstawowy klucz dostępu** jest ważnym poświadczeniem zabezpieczeń.
> 
> 

### <a name="download-the-helper-tool-and-sample-files"></a>Pobierz pliki narzędzia i przykładowe pomocnika
Możesz pobrać [usługi Azure Blob Storage pomocnicze narzędzie i przykładowe pliki jako plik .zip](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip) lub sklonować ten projekt z serwisu GitHub:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

To repozytorium zawiera `sample_templates\wingtip` katalogu, który zawiera przykład kodu HTML, CSS i obrazów. Szablony te można odwoływać się do konta usługi Azure Blob Storage należy edytować pliki HTML. Otwórz `unified.html` i `selfasserted.html` i Zamień wszystkie wystąpienia `https://localhost` adresem URL z własnego kontenera, zapisaną w poprzednich krokach. Należy użyć ścieżka bezwzględna plików HTML, ponieważ w tym przypadku będzie udostępniania HTML przez usługę Azure AD w domenie `tenantname.b2clogin.com`.

### <a name="upload-the-sample-files"></a>Przekazywanie plików przykładowych
W tym samym repozytorium Rozpakuj `B2CAzureStorageClient.zip` i uruchom `B2CAzureStorageClient.exe` pliku w ciągu. Ten program będzie po prostu przekazać wszystkie pliki w katalogu, który określisz do swojego konta magazynu i włączyć dostęp CORS dla tych plików. Jeśli wykonano powyższe kroki plików HTML i CSS teraz będzie skierowana do swojego konta magazynu. Należy pamiętać, że nazwa konta magazynu części, które poprzedza `blob.core.windows.net`, na przykład `contoso`. Możesz sprawdzić, czy zawartość został przekazany poprawnie przez próby uzyskania dostępu do `https://{storage-account-name}.blob.core.windows.net/{container-name}/wingtip/unified.html` w przeglądarce. Również użyć [ http://test-cors.org/ ](http://test-cors.org/) aby upewnić się, że zawartość jest teraz włączono mechanizm CORS. (Wyszukaj "stan XHR: 200" w wyniku.)

### <a name="customize-your-policy-again"></a>Dostosuj zasady, ponownie
Teraz, gdy już przesłano przykładowej zawartości do konta magazynu, należy zmodyfikować zasady tworzenia konta, aby odwoływać się do niego. Powtórz kroki od ["Dostosowywanie zasad"](#customize-your-policy) sekcji powyżej, tym razem przy użyciu adresów URL swoje konto magazynu. Na przykład lokalizacji usługi `unified.html` będzie plik `<url-of-your-container>/wingtip/unified.html`.

Teraz możesz używać **Uruchom teraz** przycisku lub aplikację ponownie wykonywanie zasadach. Wynik powinien wyglądać prawie dokładnie takie same — użyte w obu przypadkach taki sam przykładowy HTML i CSS. Jednak zasady teraz odwołują się do własnego wystąpienia usługi Azure Blob Storage i jest bezpłatne Przekaż pliki ponownie jako. Aby uzyskać więcej informacji na temat dostosowywania HTML i CSS, zobacz [główny artykuł dostosowywania interfejsu użytkownika](active-directory-b2c-reference-ui-customization.md).

