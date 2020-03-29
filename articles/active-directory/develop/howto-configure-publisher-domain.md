---
title: Konfigurowanie domeny wydawcy aplikacji | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak skonfigurować domenę wydawcy aplikacji, aby poinformować użytkowników o miejscu wysyłania ich informacji.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/05/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.openlocfilehash: 68040c8ee22454c300296493b6c840eabbca98aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76697136"
---
# <a name="how-to-configure-an-applications-publisher-domain"></a>Jak: Konfigurowanie domeny wydawcy aplikacji

Domena wydawcy aplikacji jest wyświetlana użytkownikom na [monit o zgodę aplikacji,](application-consent-experience.md) aby poinformować użytkowników, gdzie są wysyłane ich informacje. Aplikacje dla wielu dzierżawców, które są zarejestrowane po 21 maja 2019 r., które nie mają domeny wydawcy, są wyświetlane jako **niezweryfikowane**. Aplikacje wielodostępne to aplikacje obsługujące konta poza jednym katalogiem organizacyjnym; na przykład obsługuje wszystkie konta usługi Azure AD lub obsługuje wszystkie konta usługi Azure AD i osobiste konta Microsoft.

## <a name="new-applications"></a>Nowe aplikacje

Po zarejestrowaniu nowej aplikacji domena wydawcy aplikacji może być ustawiona na wartość domyślną. Wartość zależy od tego, gdzie aplikacja jest zarejestrowana, w szczególności, czy aplikacja jest zarejestrowana w dzierżawie i czy dzierżawca ma domeny zweryfikowane dzierżawy.

Jeśli istnieją domeny zweryfikowane przez dzierżawę, domena wydawcy aplikacji domyślnie będzie domeną podstawową zweryfikowaną w dzierżawie. Jeśli nie ma żadnych domen zweryfikowanych dzierżawy (co ma miejsce, gdy aplikacja nie jest zarejestrowana w dzierżawie), domena wydawcy aplikacji zostanie ustawiona na wartość null.

W poniższej tabeli podsumowano domyślne zachowanie wartości domeny wydawcy.  

| Domeny zweryfikowane przez dzierżawę | Domyślna wartość domeny wydawcy |
|-------------------------|----------------------------|
| wartość null | wartość null |
| *.onmicrosoft.com | *.onmicrosoft.com |
| - *.onmicrosoft.com<br/>- domain1.com<br/>- domain2.com (podstawowy) | domain2.com |

Jeśli domena wydawcy aplikacji z wieloma dzierżawami nie jest ustawiona lub jest ustawiona na domenę, która kończy się na onmicrosoft.com, monit o zgodę aplikacji będzie wyświetlany **niezweryfikowany** zamiast domeny wydawcy.

## <a name="grandfathered-applications"></a>Aplikacje z dziadkami

Jeśli aplikacja została zarejestrowana przed 21 maja 2019 r., monit o zgodę aplikacji nie będzie wyświetlany **niezweryfikowany,** jeśli nie ustawiono domeny wydawcy. Zalecamy ustawienie wartości domeny wydawcy, aby użytkownicy mogli zobaczyć te informacje w pytaniu o zgodę aplikacji.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Konfigurowanie domeny wydawcy przy użyciu portalu Azure

Aby ustawić domenę wydawcy aplikacji, wykonaj następujące czynności.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.

1. Jeśli Twoje konto znajduje się w więcej niż jednej dzierżawie usługi Azure AD:
   1. Wybierz swój profil z menu w prawym górnym rogu strony, a następnie **przełącz katalog**.
   1. Zmień sesję na dzierżawę usługi Azure AD, w której chcesz utworzyć aplikację.

1. Przejdź do [usługi Azure Active Directory > rejestracji aplikacji,](https://go.microsoft.com/fwlink/?linkid=2083908) aby znaleźć i wybrać aplikację, którą chcesz skonfigurować.

   Po wybraniu aplikacji zobaczysz stronę **Przegląd** aplikacji.

1. Na stronie **Przegląd** aplikacji wybierz sekcję **Znakowanie.**

1. Znajdź pole **Domeny programu Publisher** i wybierz jedną z następujących opcji:

   - Wybierz **pozycję Konfiguruj domenę,** jeśli domena nie została jeszcze skonfigurowana.
   - Wybierz **pozycję Aktualizuj domenę,** jeśli domena została już skonfigurowana.

Jeśli aplikacja jest zarejestrowana w dzierżawie, zobaczysz dwie karty do wyboru: **Wybierz zweryfikowaną domenę** i **Zweryfikuj nową domenę**.

Jeśli aplikacja nie jest zarejestrowana w dzierżawie, zobaczysz tylko opcję weryfikacji nowej domeny dla aplikacji.

### <a name="to-verify-a-new-domain-for-your-app"></a>Aby zweryfikować nową domenę aplikacji

1. Utwórz plik `microsoft-identity-association.json` o nazwie i wklej następujący fragment kodu JSON.

   ```json
   {
      "associatedApplications": [
        {
           "applicationId": "{YOUR-APP-ID-HERE}"
        }
      ]
    }
   ```

1. Zastąp symbol zastępczy *{YOUR-APP-ID-HERE}* identyfikatorem aplikacji (klienta), który odpowiada twojej aplikacji.

1. Hostuj plik `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json`pod adresem: . Zastąp symbol zastępczy *{TWOJA DOMENA-TUTAJ},* aby dopasować go do zweryfikowanej domeny.

1. Kliknij przycisk **Zweryfikuj i zapisz domenę.**

### <a name="to-select-a-verified-domain"></a>Aby wybrać zweryfikowaną domenę

- Jeśli dzierżawa zweryfikowała domeny, wybierz jedną z domen z listy **rozwijanej Wybierz zweryfikowaną domenę.**

>[!Note]
> Oczekiwany nagłówek "Typ zawartości", który `application/json`powinien zostać zwrócony, to . Jeśli używasz czegoś innego, jak np.`application/json; charset=utf-8` 
> 
>``` "Verification of publisher domain failed. Error getting JSON file from https:///.well-known/microsoft-identity-association. The server returned an unexpected content type header value. " ```
>

## <a name="implications-on-the-app-consent-prompt"></a>Wpływ na monit o zgodę aplikacji

Konfigurowanie domeny wydawcy ma wpływ na to, co użytkownicy widzą w pytaniu o zgodę aplikacji. Aby w pełni zrozumieć składniki monitu o zgodę, zobacz [Opis środowiska zgody aplikacji](application-consent-experience.md).

W poniższej tabeli opisano zachowanie aplikacji utworzonych przed 21 maja 2019 r.

![Monit o wyrażenie zgody dla aplikacji utworzonych przed 21 maja 2019 r.](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

Zachowanie dla nowych aplikacji utworzonych po 21 maja 2019 r. będzie zależeć od domeny wydawcy i typu aplikacji. W poniższej tabeli opisano zmiany, których należy się spodziewać w przypadku różnych kombinacji konfiguracji.

![Monit o wyrażenie zgody dla aplikacji utworzonych po 21 maja 2019 r.](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>Implikacje dotyczące przekierowania identyfikatorów URI

Aplikacje, które logują się do użytkowników przy za pomocą dowolnego konta służbowego lub szkolnego lub osobistych kont Microsoft[(wielodostępnych)](single-and-multi-tenant-apps.md)podlegają kilku ograniczeniom podczas określania URI przekierowania.

### <a name="single-root-domain-restriction"></a>Ograniczenie pojedynczej domeny głównej

Gdy wartość domeny wydawcy dla aplikacji z wieloma dzierżawami jest ustawiona na wartość null, aplikacje są ograniczone do udostępniania jednej domeny głównej dla przekierowanych identyfikatorów URI. Na przykład następująca kombinacja wartości nie jest dozwolona, ponieważ domena główna, contoso.com, nie jest zgodna fabrikam.com.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>Ograniczenia poddomeny

Poddomeny są dozwolone, ale należy jawnie zarejestrować domenę główną. Na przykład podczas gdy następujące identyfikatory URI współużytkuje jedną domenę główną, kombinacja nie jest dozwolona.

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

Jeśli jednak deweloper jawnie doda domenę główną, kombinacja jest dozwolona.

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>Wyjątki

Następujące przypadki nie podlegają ograniczeniu pojedynczej domeny głównej:

- Aplikacje dla jednej dzierżawy lub aplikacje przeznaczone dla kont w jednym katalogu
- Korzystanie z localhost jako przekierowanie identyfikatorów URI
- Przekierowywanie identyfikatorów URI ze schematami niestandardowymi (bez protokołu HTTP lub HTTPS)

## <a name="configure-publisher-domain-programmatically"></a>Programowo konfigurowanie domeny wydawcy

Obecnie nie ma interfejsu API REST ani obsługi programu PowerShell do programowania konfigurowania domeny wydawcy.
