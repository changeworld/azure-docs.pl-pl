---
title: 'Usługa Azure Active Directory B2C: Narzędzie Pomocnik dostosowania strony interfejsu użytkownika | Dokumentacja firmy Microsoft'
description: Narzędzie Pomocnik, używany do przedstawiania funkcji dostosowywania interfejsu użytkownika strony w usłudze Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 01/07/2017
ms.author: davidmu
ms.openlocfilehash: de3d430ad984c9b173f77138417532badc3a7497
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="azure-active-directory-b2c-a-helper-tool-used-to-demonstrate-the-page-user-interface-ui-customization-feature"></a>Usługa Azure Active Directory B2C: Pomocnika narzędzie służące do prezentacja funkcji dostosowywania interfejsu użytkownika strony
W tym artykule jest dodatek do [główny artykuł dostosowania interfejsu użytkownika](active-directory-b2c-reference-ui-customization.md) w usłudze Azure Active Directory (Azure AD) B2C. W poniższych krokach opisano sposób wykonywania przy użyciu zawartość HTML i CSS przykładową, która przygotowaliśmy dostosowywanie funkcji interfejsu użytkownika strony.

## <a name="get-an-azure-ad-b2c-tenant"></a>Uzyskiwanie dzierżawy usługi Azure AD B2C
Przed dostosowaniem niczego, konieczne będzie [uzyskać dzierżawę usługi Azure AD B2C](active-directory-b2c-get-started.md) Jeśli nie został wcześniej.

## <a name="create-a-sign-up-or-sign-in-policy"></a>Tworzenie zasad rejestracji lub logowania
Przykłady przygotowaliśmy może służyć do customze dwóch stron w [zasad rejestracji i logowania](active-directory-b2c-reference-policies.md): [ujednoliconego strony logowania](active-directory-b2c-reference-ui-customization.md) i [strony własnym potwierdzona atrybutów](active-directory-b2c-reference-ui-customization.md). Gdy [tworzenia zasad dotyczących tworzenia konta lub logowanie](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy), Dodaj konta lokalnego (adres e-mail), Facebook, Google i Microsoft jako **dostawców tożsamości**. Są to tylko IDPs, akceptujących naszej próbki zawartość HTML.  Jeśli chcesz, możesz także dodać podzbiór tych IDPs.

## <a name="register-an-application"></a>Rejestrowanie aplikacji
Konieczne będzie [zarejestrować aplikację](active-directory-b2c-app-registration.md) w dzierżawie usługi B2C, który może służyć do wykonywania zasady. Po zarejestrowaniu aplikacji, masz kilka opcji, które służą do aktualnie ma uruchomiony zasadach rejestracji:

* Tworzenie jednego z usługi Azure AD B2C aplikacji szybki start wymienionych w sekcji "Wprowadzenie" [podpisywania i logowanie użytkowników w twoich aplikacjach](active-directory-b2c-overview.md).
* Użyj wstępnie przygotowanych [Plac zabaw dla usługi Azure AD B2C](https://aadb2cplayground.azurewebsites.net) aplikacji. Jeśli chcesz użyć Plac zabaw dla musi zarejestrować aplikację w dzierżawie usługi B2C przy użyciu **identyfikator URI przekierowania** `https://aadb2cplayground.azurewebsites.net/`.
* Użyj **Uruchom teraz** przycisk na zasady w [portalu Azure](https://portal.azure.com/).

## <a name="customize-your-policy"></a>Dostosuj zasady
Aby dostosować wygląd i działanie zasad, musisz najpierw utworzyć pliki HTML i CSS przy użyciu określonych konwencji usługi Azure AD B2C. Można następnie przekazać statycznego zawartość do lokalizacji publicznej, aby usługi Azure AD B2C do niego dostęp. Może być własną dedykowanym serwerze sieci web, magazyn obiektów Blob Azure, Azure Content Delivery Network lub wszelkie inne statyczne hosting zasobów dostawcy. Jedynymi wymogami są, że zawartość jest dostępna za pośrednictwem protokołu HTTPS i jest możliwy przy użyciu mechanizmu CORS. Po zostały uwidocznione statycznej zawartości w sieci web, można edytować zasady, aby wskazywało tę lokalizację i prezentowanie zawartości do klientów. [Główny artykuł dostosowania interfejsu użytkownika](active-directory-b2c-reference-ui-customization.md) opisano szczegółowo, jak działa funkcja dostosowywania usługi Azure AD B2C.

Do celów tego samouczka opracowaliśmy już niektóre przykłady i hostowanej go w magazynie obiektów Blob Azure. Zawartość próbki jest bardzo proste dostosowania w motywie fikcyjnej firmy "Wingtip Toys". Aby wypróbować jej możliwości w własnych zasad, wykonaj następujące kroki:

1. Zaloguj się do dzierżawy na [portalu Azure](https://portal.azure.com/) i przejdź do bloku funkcji B2C.
2. Kliknij przycisk **zasad rejestracji i logowania**, kliknij przycisk z zasadami i kliknij pozycję Edytuj (na przykład "b2c\_1\_znak\_się\_znak\_w").
3. Kliknij przycisk **dostosowywania interfejsu użytkownika strony** , a następnie **stronę tworzenia konta lub logowanie Unified**.
4. Przełącz **niestandardowe strony** przełączyć się do **tak**. W **strony niestandardowego identyfikatora URI** wprowadź `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/unified.html`. Kliknij przycisk **OK**.
5. Kliknij przycisk **stronę tworzenia konta lokalnego konta**. Przełącz **Użyj niestandardowego szablonu** przełączyć się do **tak**. W **strony niestandardowego identyfikatora URI** wprowadź `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/selfasserted.html`.
6. Powtórz krok tego samego **strony rejestracji społecznościowych konta**.
   Kliknij przycisk **OK** dwa razy, aby zamknąć bloków dostosowania interfejsu użytkownika.
7. Kliknij pozycję **Zapisz**.

Teraz można wypróbować zasady niestandardowe. Jeśli chcesz, ale mogą także po prostu kliknij można użyć własnych aplikacji lub usługi Azure AD B2C Plac zabaw dla **Uruchom teraz** polecenia w bloku zasad. W polu listy rozwijanej wybierz aplikację, a następnie wybierz odpowiedni identyfikator URI przekierowania. Kliknij przycisk **Uruchom teraz** przycisku. Zostanie otwarty na nowej karcie przeglądarki i można uruchomić za pomocą interfejsu użytkownika skorzystania z aplikacji przy użyciu nowej zawartości w miejscu!

## <a name="upload-the-sample-content-to-azure-blob-storage"></a>Przekaż zawartość próbki do magazynu obiektów Blob Azure
Jeśli chcesz używać magazynu obiektów Blob Azure, aby udostępnić zawartość strony, można utworzyć konta magazynu i użyć naszych Narzędzie Pomocnik B2C przekazywania plików.

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Kliknij przycisk **+ nowy** > **dane i magazyn** > **konta magazynu**. Konieczne będzie subskrypcji platformy Azure, aby utworzyć konto magazynu obiektów Blob Azure. Możesz utworzyć konto bezpłatnej wersji próbnej na [witryny sieci Web Azure](https://azure.microsoft.com/pricing/free-trial/).
3. Podaj **nazwa** dla magazynu konta (na przykład "contoso") i wybierz odpowiednie opcje dla **warstwa cenowa**, **grupy zasobów** i  **Subskrypcja**. Upewnij się, że masz **Przypnij do tablicy startowej** zaznaczoną opcją. Kliknij przycisk **Utwórz**.
4. Wróć do tablicy startowej, a następnie kliknij konto magazynu, który został właśnie utworzony.
5. W **Podsumowanie** kliknij **kontenery**, a następnie kliknij przycisk **+ Dodaj**.
6. Podaj **nazwa** dla kontenera (na przykład "b2c") i wybierz **obiektu Blob** jako **dostęp typu**. Kliknij przycisk **OK**.
7. Kontener, w którym utworzono będą wyświetlane na liście na **obiekty BLOB** bloku. Zanotuj adres URL kontenera; na przykład powinien wyglądać podobnie do `https://contoso.blob.core.windows.net/b2c`. Zamknij **obiekty BLOB** bloku.
8. W bloku konto magazynu, kliknij polecenie **klucze** i zanotuj wartości **nazwy konta magazynu** i **podstawowy klucz dostępu** pola.

> [!NOTE]
> **Podstawowy klucz dostępu** jest ważne poświadczenie zabezpieczeń.
> 
> 

### <a name="download-the-helper-tool-and-sample-files"></a>Pobierz pliki narzędzia i próbki pomocnika
Możesz pobrać [magazyn obiektów Blob Azure pliki narzędzia i próbki pomocnika jako plik .zip](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip) lub sklonować go z witryny GitHub:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

To repozytorium zawiera `sample_templates\wingtip` katalogu, który zawiera przykład HTML, CSS i obrazy. Te szablony do odwołania konta magazynu obiektów Blob Azure należy edytować pliki HTML. Otwórz `unified.html` i `selfasserted.html` i Zastąp wszystkie wystąpienia `https://localhost` z adresem URL własnych kontenera zapisaną w poprzednich krokach. Ścieżka bezwzględna plików HTML należy użyć, ponieważ w takim przypadku HTML zostanie obsłużona przez usługę Azure AD w domenie `https://login.microsoftonline.com`.

### <a name="upload-the-sample-files"></a>Przekaż przykładowe pliki
W tym samym repozytorium, Rozpakuj `B2CAzureStorageClient.zip` i uruchom `B2CAzureStorageClient.exe` pliku w ciągu. Ten program będzie po prostu Przekaż wszystkie pliki w katalogu wskazanym na koncie magazynu i włączyć CORS dostęp do tych plików. Jeśli kroki powyżej zostały wykonane, pliki HTML i CSS będzie teraz wskazuje konta magazynu. Należy pamiętać, że nazwa konta magazynu części poprzedzający `blob.core.windows.net`, na przykład `contoso`. Możesz sprawdzić, czy zawartość, został przesłany poprawnie przez próby uzyskania dostępu do `https://{storage-account-name}.blob.core.windows.net/{container-name}/wingtip/unified.html` w przeglądarce. Również użyć [ http://test-cors.org/ ](http://test-cors.org/) aby upewnić się, czy zawartość jest teraz włączone CORS. (Wyszukaj "stan XHR: 200" w wyniku.)

### <a name="customize-your-policy-again"></a>Dostosuj zasady, ponownie
Teraz, gdy zawartość przykładową przekazanego do konta magazynu, należy edytować zasadach rejestracji do utworzenia odwołania. Powtórz kroki od ["Dostosować zgodnie z zasadami"](#customize-your-policy) sekcji powyżej, przy użyciu adresów URL swoje konto magazynu. Na przykład lokalizację użytkownika `unified.html` plik może być `<url-of-your-container>/wingtip/unified.html`.

Teraz można używać **Uruchom teraz** przycisku lub aplikacji można wykonać ponownie z zasadami. Wynik powinien wyglądać prawie dokładnie takie same — można użyć tych samych próbkach HTML i CSS w obu przypadkach. Jednak zasad odwołuje się teraz wystąpienia magazynu obiektów Blob Azure i mogą edytować i należy przekazać pliki ponownie jako. Aby uzyskać więcej informacji o dostosowywaniu HTML i CSS, zapoznaj się [główny artykuł dostosowania interfejsu użytkownika](active-directory-b2c-reference-ui-customization.md).

