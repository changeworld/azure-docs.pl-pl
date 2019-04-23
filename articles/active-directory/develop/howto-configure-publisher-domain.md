---
title: Konfigurowanie domeny wydawcy aplikacji | Azure
description: Dowiedz się, jak skonfigurować domena wydawcy aplikacji, aby powiadomić użytkowników, z którego jest wysyłane swoje informacje.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.author: celested
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: efbf448770bfcf797d6bf01cd3c28dc98023adff
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59793897"
---
# <a name="how-to-configure-an-applications-publisher-domain-preview"></a>Instrukcje: Domena wydawcy aplikacji (wersja zapoznawcza) Konfiguruj

Domena wydawcy aplikacji będzie wyświetlana użytkownikom na [monit o wyrażenie zgody aplikacji](application-consent-experience.md) aby powiadomić użytkowników, z którego jest wysyłane swoje informacje. Wielodostępne aplikacje, które są zarejestrowane po 21 maja 2019 r, które nie mają domena wydawcy są wyświetlane jako **niezweryfikowanych**. Wielodostępne aplikacje to aplikacje, które obsługują konta poza jednym katalogu organizacji; na przykład obsługi wszystkich kont usługi Azure AD lub obsługują wszystkie konta usługi Azure AD i osobistych kont Microsoft.

## <a name="new-applications"></a>Nowe aplikacje

Podczas rejestrowania nowej aplikacji domena wydawcy aplikacji może być równa wartości domyślnej. Wartość zależy od tego, gdzie aplikacja jest zarejestrowany, szczególnie, czy aplikacja jest zarejestrowana w dzierżawie i tego, czy dzierżawa ma dzierżawy zweryfikowanych domen.

W przypadku domen zweryfikować dzierżawy głównej zweryfikowanej domeny dzierżawy zostaną domyślnie domena wydawcy aplikacji. Jeśli nie istnieją żadne dzierżawy zweryfikowanych domen (co ma miejsce w przypadku aplikacji nie jest zarejestrowany w dzierżawie), domena wydawcy aplikacji zostanie ustawiona na wartość null.

W poniższej tabeli podsumowano zachowanie domyślne wartości domeny wydawcy.  

| Zweryfikowano dzierżawy domen | Wartość domyślna domena wydawcy |
|-------------------------|----------------------------|
| wartość null | wartość null |
| *.onmicrosoft.com | *.onmicrosoft.com |
| -*. onmicrosoft.com<br/>-domain1.com<br/>-domain2.com (podstawowy) | domain2.com |

Jeśli nie ustawiono domena wydawcy aplikacji z wieloma dzierżawami, czy jest ustawiony do domeny, która kończy się na. onmicrosoft.com, wyświetli się monit o wyrażenie zgody aplikacji **niezweryfikowanych** zamiast domena wydawcy.

## <a name="grandfathered-applications"></a>Wyjątkowo aplikacji

Jeśli aplikacja została zarejestrowana przed 21 maja 2019 r nie wyświetli monit o wyrażenie zgody aplikacji **niezweryfikowanych** Jeśli nie ustawiono domena wydawcy. Zaleca się, że wartość zostanie ustawiona wydawcy domeny, dzięki czemu użytkownicy mogą zobaczyć te informacje na monit o wyrażenie zgody aplikacji.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Konfigurowanie domeny wydawcy w witrynie Azure portal

Aby ustawić domena wydawcy aplikacji, wykonaj następujące kroki.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.

1. Jeśli Twoje konto znajduje się w więcej niż jednej dzierżawy usługi Azure AD:
   1. Wybierz profil z menu w prawym górnym rogu strony, a następnie **Przełącz katalog**.
   1. Zmień sesję dzierżawy usługi Azure AD, w którym chcesz utworzyć aplikację.

1. Przejdź do [usługi Azure Active Directory > Rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) Aby znaleźć i wybrać aplikację, którą chcesz skonfigurować.

   Po wybraniu aplikacji, zobaczysz aplikacji **Przegląd** strony.

1. Z poziomu aplikacji **Przegląd** wybierz opcję **znakowania** sekcji.

1. Znajdź **domena wydawcy** pola, a następnie wybierz jedną z następujących opcji:

   - Wybierz **konfigurowanie domeny** Jeśli nie skonfigurowano już domeny.
   - Wybierz **domena aktualizacji** Jeśli domeny została już skonfigurowana.

Jeśli aplikacja zostanie zarejestrowana w dzierżawie, zobaczysz dwie karty, które można wybierać: **Wybierz zweryfikowaną domenę** i **Sprawdź nową domenę**.

Jeśli aplikacja nie jest zarejestrowana w dzierżawie, będą widoczne tylko opcję, aby sprawdzić nową domenę dla aplikacji.

### <a name="to-verify-a-new-domain-for-your-app"></a>Aby sprawdzić nową domenę dla aplikacji

1. Utwórz plik o nazwie `microsoft-identity-association.json` i wklej poniższy fragment kodu JSON.

   ```json
   {
      "associatedApplications": [
        {
           "applicationId": "{YOUR-APP-ID-HERE}"
        }
      ]
    }
   ```

1. Zastąp symbol zastępczy *{YOUR-APP-ID — tutaj}* identyfikatorem aplikacji (klienta), która odnosi się do aplikacji.

1. Hostowanie plik w lokalizacji: `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json`. Zastąp symbol zastępczy *{YOUR-DOMAIN-tutaj}* był zgodny z zweryfikowaną domeną.

1. Kliknij przycisk **Zweryfikuj i Zapisz domeny** przycisku.

### <a name="to-select-a-verified-domain"></a>Aby wybrać zweryfikowanej domeny

- Jeśli Twoja dzierżawa ma zweryfikowanych domen, wybierz jedną z domen z **wybierz zweryfikowaną domenę** listy rozwijanej.

## <a name="implications-on-the-app-consent-prompt"></a>Wpływ na aplikację monit o zgodę

— Konfigurowanie domeny publisher ma wpływ na co użytkownicy widzą na monit o wyrażenie zgody aplikacji. Aby w pełni poznać składniki monit o wyrażenie zgody, zobacz [zrozumienie napotyka zgody aplikacji](application-consent-experience.md).

W poniższej tabeli opisano zachowanie dla aplikacji utworzonych przed 21 maja 2019 r.

![Monit o wyrażenie zgody dla aplikacji utworzonych przed 21 maja 2019 r.](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

Zachowanie dla nowych aplikacji utworzonych po 21 maja 2019 będzie zależeć od domeny wydawcy i typu aplikacji. W poniższej tabeli opisano zmiany, które należy się spodziewać się przy użyciu różnych kombinacji konfiguracji.

![Monit o zgodę dla aplikacji utworzonych po 21 maja 2019 r.](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>Wpływ na identyfikatory URI przekierowania

Aplikacje, które logują użytkowników przy użyciu dowolnej pracy lub konta służbowego lub osobistego konta Microsoft ([wielodostępnych](single-and-multi-tenant-apps.md)) są kilku ograniczeniom podczas określania identyfikatory URI przekierowania.

### <a name="single-root-domain-restriction"></a>Ograniczenia domen z jednym elementem głównym

Jeśli wartość domeny wydawcy dla aplikacji wielodostępnych jest równa null, aplikacje są ograniczone do udostępniania z jednym elementem głównym domeny dla identyfikatorów URI przekierowań. Na przykład następujących kombinacji wartości jest niedozwolona, ponieważ domeny katalogu głównego, contoso.com, nie jest zgodna fabrikam.com.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>Ograniczenia poddomeny

Poddomen są dozwolone, ale należy jawnie zarejestrować domeny katalogu głównego. Na przykład gdy następujące identyfikatory URI mają domenę pojedynczego elementu głównego, połączenie nie jest dozwolone.

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

Jednak jeśli jawnie dodawane przez dewelopera domeny katalogu głównego, połączenie jest dozwolone.

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>Wyjątki

Z zastrzeżeniem ograniczeń domenie z jednym elementem głównym nie ma następujących przypadkach:

- Aplikacje jednej dzierżawy lub aplikacji przeznaczonych dla kont w jednym katalogu
- Użyj nazwy localhost, ponieważ identyfikatory URI przekierowania
- Identyfikatory URI przekierowania za pomocą niestandardowych schematów (innego niż HTTP lub HTTPS)

## <a name="configure-publisher-domain-programmatically"></a>Programowe Konfigurowanie domena wydawcy

Obecnie brak obsługi interfejsu API REST lub programu PowerShell do konfigurowania domeny wydawcy programowo.
