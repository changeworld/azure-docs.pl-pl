---
title: Aplikacje wieloznaczne w serwer proxy aplikacji usługi Azure AD platformy Azure
description: Dowiedz się, jak korzystać z symbolami wieloznacznymi aplikacji na serwerze proxy aplikacji usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2018
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5a9e7be5f582051e03cba08733fcbfa697cc8f5
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275049"
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Aplikacje z symbolami wieloznacznymi serwera proxy aplikacji usługi Azure Active Directory

W usłudze Active Directory (Azure AD), konfigurowanie dużej liczby lokalnych aplikacji może szybko stać się bezproblemowego zarządzania i wprowadza niepotrzebne ryzyko błędów konfiguracji, jeśli wiele z nich wymaga tych samych ustawień. Za pomocą [usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy.md)można rozwiązać ten problem, używając wieloznacznego publikowania aplikacji w celu publikowania wielu aplikacji jednocześnie i zarządzania nimi. Jest to rozwiązanie, która pozwala na:

- Upraszcza czynności administracyjnych
- Zmniejsz liczbę potencjalnych błędów konfiguracji
- Umożliwianie użytkownikom bezpiecznego dostępu do większej ilości zasobów

Ten artykuł zawiera informacje, które należy skonfigurować publikowanie aplikacji z symbolami wieloznacznymi w danym środowisku.

## <a name="create-a-wildcard-application"></a>Tworzenie aplikacji symboli wieloznacznych

Aby utworzyć aplikację symbolu wieloznacznego (*), jeśli masz grupę aplikacji z taką samą konfigurację. Potencjalne kandydatami do zastosowań symboli wieloznacznych są aplikacji do udostępniania następujące ustawienia:

- Grupy użytkowników, którzy mają dostęp do nich
- Metoda rejestracji Jednokrotnej
- Protokół dostępu (http, https)

Można opublikować aplikacji z symbolami wieloznacznymi, w przypadku zarówno wewnętrzne i zewnętrzne adresy URL w następującym formacie:

> http (s)://*.\> domeny\<

Na przykład: `http(s)://*.adventure-works.com`.

Gdy wewnętrzne i zewnętrzne adresy URL można użyć różnych domenach, najlepszym rozwiązaniem, powinna być taka sama. Publikowanie aplikacji, zostanie wyświetlony błąd, jeśli jeden z adresów URL nie zawiera symboli wieloznacznych.

Jeśli masz dodatkowe aplikacje za pomocą różnych ustawień konfiguracji, należy opublikować te wyjątki, jako osobne aplikacje zastąpić wartości domyślne ustawione dla symbolu wieloznacznego. Aplikacje bez symboli wieloznacznych zawsze pierwszeństwo aplikacje z symbolami wieloznacznymi. Z perspektywy konfiguracji są "tak" zwykłych aplikacji.

Tworzenie aplikacji wieloznacznej bazuje na tym samym [przepływie publikowania aplikacji](application-proxy-add-on-premises-application.md) , który jest dostępny dla wszystkich innych aplikacji. Jedyna różnica polega na zawierać symbol wieloznaczny w adresy URL i potencjalnie konfiguracji logowania jednokrotnego.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, upewnij się, że zostały spełnione te wymagania.

### <a name="custom-domains"></a>Domeny niestandardowe

Chociaż [domeny niestandardowe](application-proxy-configure-custom-domain.md) są opcjonalne dla wszystkich innych aplikacji, są one wymagane w przypadku aplikacji wieloznacznych. Tworzenie domen niestandardowych wymaga:

1. Utwórz zweryfikowaną domenę na platformie Azure.
1. Przekaż certyfikat SSL w formacie PFX do usługi serwera proxy aplikacji.

Należy rozważyć użycie certyfikat uniwersalny, aby dopasować aplikację, która ma zostać utworzone. Alternatywnie umożliwia także certyfikat, który znajduje się tylko określonych aplikacji. W takim przypadku tylko te aplikacje, które są wymienione w certyfikacie będą dostępne za pomocą tej aplikacji z symbolami wieloznacznymi.

Ze względów bezpieczeństwa jest to bezwględnie wymagane, i symboli wieloznacznych nie będą obsługiwane w przypadku aplikacji, które nie mogą używać domeny niestandardowej do zewnętrznego adresu URL.

### <a name="dns-updates"></a>Aktualizacje rekordów DNS

W przypadku korzystania z domen niestandardowych należy utworzyć wpis DNS z rekordem CNAME dla zewnętrznego adresu URL (na przykład `*.adventure-works.com`) wskazującego zewnętrzny adres URL punktu końcowego serwera proxy aplikacji. W przypadku aplikacji z symbolami wieloznacznymi rekord CNAME musi wskazywać odpowiednie zewnętrzne adresy URL:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

Aby upewnić się, że rekord CNAME został prawidłowo skonfigurowany, można użyć [polecenia nslookup](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup) na jednym z docelowych punktów końcowych, na przykład `expenses.adventure-works.com`.  Odpowiedź powinna zawierać już wymieniony alias (`<yourAADTenantId>.tenant.runtime.msappproxy.net`).

## <a name="considerations"></a>Zagadnienia do rozważenia

Poniżej przedstawiono kilka kwestii, które należy wziąć pod uwagę w przypadku aplikacji wieloznacznych.

### <a name="accepted-formats"></a>Akceptowane formaty

W przypadku aplikacji z symbolami wieloznacznymi **wewnętrzny adres URL** musi być sformatowany jako `http(s)://*.<domain>`.

![W przypadku wewnętrznego adresu URL Użyj formatu http (s)://*. > domeny\<](./media/application-proxy-wildcard/22.png)

W przypadku konfigurowania **zewnętrznego adresu URL**należy użyć następującego formatu: `https://*.<custom domain>`

![W przypadku zewnętrznego adresu URL Użyj formatu https://*.\<> domeny niestandardowej](./media/application-proxy-wildcard/21.png)

Innych pozycji symbolu wieloznacznego, wiele symboli wieloznacznych lub inne ciągi wyrażenie regularne nie są obsługiwane i powoduje błędy.

### <a name="excluding-applications-from-the-wildcard"></a>Wykluczanie aplikacji z symbolami wieloznacznymi

Można wykluczyć aplikację z aplikacji symboli wieloznacznych

- Publikowanie aplikacji wyjątek jako regularne aplikacji
- Włączanie symboli wieloznacznych tylko dla określonych aplikacji za pomocą ustawienia DNS

Publikowanie aplikacji jako aplikacji regularne jest preferowaną metodą, aby wykluczyć aplikację z symbolem wieloznacznym. Należy opublikować wykluczonych aplikacji przed aplikacji symboli wieloznacznych, aby upewnić się, że listy wyjątków są wymuszane na początku wybrać odpowiedni. Najbardziej konkretna aplikacja zawsze będzie mieć pierwszeństwo — aplikacja opublikowana jako `budgets.finance.adventure-works.com` ma pierwszeństwo przed `*.finance.adventure-works.com`aplikacji, która z kolei ma pierwszeństwo przed `*.adventure-works.com`aplikacji.

Można również ograniczyć symboli wieloznacznych, aby działał tylko dla określonych aplikacji za pomocą usługi zarządzania usługą DNS. Najlepszym rozwiązaniem należy utworzyć wpis CNAME, który zawiera symbol wieloznaczny i nieprawidłowy format zewnętrzny adres URL, które zostały skonfigurowane. Jednakże możesz zamiast tego wskazać adresów URL określonych aplikacji symbole wieloznaczne. Na przykład zamiast `*.adventure-works.com`, wskaż `hr.adventure-works.com`, `expenses.adventure-works.com` i `travel.adventure-works.com individually` do `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`.

Jeśli użyjesz tej opcji, potrzebujesz również innego wpisu CNAME dla wartości `AppId.domain`, na przykład `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`, wskazujący tę samą lokalizację. **Identyfikator appid** można znaleźć na stronie właściwości aplikacji symboli wieloznacznych:

![Znajdź identyfikator aplikacji na stronie właściwości aplikacji](./media/application-proxy-wildcard/01.png)

### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>Adres URL strony głównej panelu MyApps ustawienie

Aplikacja z symbolami wieloznacznymi jest reprezentowana przy użyciu tylko jednego kafelka w [panelu Moje aplikacje](https://myapps.microsoft.com). Domyślnie ten Kafelek jest ukryty. Pokaż Kafelek i ziemi użytkowników na określonej stronie:

1. Postępuj zgodnie z wytycznymi dotyczącymi [ustawiania adresu URL strony głównej](application-proxy-configure-custom-home-page.md).
1. Na stronie właściwości aplikacji ustaw dla opcji **Pokaż aplikację** **wartość true** .

### <a name="kerberos-constrained-delegation"></a>Ograniczone delegowanie protokołu Kerberos

W przypadku aplikacji korzystających z [ograniczonego delegowania protokołu Kerberos (KCD) jako metody logowania jednokrotnego](application-proxy-configure-single-sign-on-with-kcd.md)Nazwa SPN określona dla metody SSO może również wymagać symbolu wieloznacznego. Na przykład nazwa SPN może: `HTTP/*.adventure-works.com`. Nadal musisz mieć skonfigurowane indywidualne nazwy SPN na serwerach zaplecza (na przykład `http://expenses.adventure-works.com and HTTP/travel.adventure-works.com`).

## <a name="scenario-1-general-wildcard-application"></a>Scenariusz 1: Zastosowań symboli wieloznacznych ogólne

W tym scenariuszu masz trzech różnych aplikacji, którą chcesz opublikować:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Wszystkie trzy aplikacje:

- Są używane przez wszystkich użytkowników
- Użyj *zintegrowanego uwierzytelniania systemu Windows*
- Ma te same właściwości

Można opublikować wieloznaczną aplikację, wykonując czynności opisane w temacie [publikowanie aplikacji przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md). W tym scenariuszu przyjmuje:

- Dzierżawca z następującym IDENTYFIKATORem: `000aa000-11b1-2ccc-d333-4444eee4444e`
- Została skonfigurowana zweryfikowana domena o nazwie `adventure-works.com`.
- Wpis **CNAME** , który wskazuje `*.adventure-works.com` do `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` został utworzony.

Postępując zgodnie z [opisanymi krokami](application-proxy-add-on-premises-application.md), utworzysz nową aplikację serwera proxy aplikacji w dzierżawie. W tym przykładzie symbol wieloznaczny znajduje się w następujących pól:

- Wewnętrzny adres URL:

    ![Przykład: symbol wieloznaczny w wewnętrznym adresie URL](./media/application-proxy-wildcard/42.png)

- Zewnętrzny adres URL:

    ![Przykład: symbol wieloznaczny w zewnętrznym adresie URL](./media/application-proxy-wildcard/43.png)

- Główna nazwa usługi aplikacji wewnętrznej:

    ![Przykład: symbol wieloznaczny w konfiguracji nazwy SPN](./media/application-proxy-wildcard/44.png)

Publikując wieloznaczną aplikację, możesz teraz uzyskać dostęp do swoich trzech aplikacji, przechodząc do adresów URL, które są używane do (na przykład `travel.adventure-works.com`).

Konfiguracja implementuje następującą strukturę:

![Pokazuje strukturę zaimplementowaną przez przykładową konfigurację](./media/application-proxy-wildcard/05.png)

| Kolor | Opis |
| ---   | ---         |
| Niebieski  | Aplikacje jawnie opublikowane i widoczne w Azure Portal. |
| Szary  | Aplikacje możesz za pośrednictwem aplikacji nadrzędnej. |

## <a name="scenario-2-general-wildcard-application-with-exception"></a>Scenariusz 2: Symbol wieloznaczny ogólne aplikacji, z wyjątkiem

W tym scenariuszu oprócz trzech ogólnych aplikacji jest inna aplikacja, `finance.adventure-works.com`, która powinna być dostępna tylko przez dział finansów. Za pomocą bieżącej struktury aplikacji Twoja aplikacja finansowa będzie dostępny za pośrednictwem aplikacji symboli wieloznacznych i wszyscy pracownicy. Aby to zmienić, wykluczasz aplikacji z Twojego symboli wieloznacznych, konfigurując Finanse jako oddzielną aplikację przy użyciu bardziej restrykcyjne uprawnienia.

Należy upewnić się, że rekordy CNAME istnieją, które wskazują, że `finance.adventure-works.com` do punktu końcowego określonego dla aplikacji, określonego na stronie serwer proxy aplikacji. W tym scenariuszu `finance.adventure-works.com` punkty `https://finance-awcycles.msappproxy.net/`.

Zgodnie z [opisanymi krokami](application-proxy-add-on-premises-application.md)ten scenariusz wymaga następujących ustawień:

- W **wewnętrznym adresie URL**ustawiasz opcję **finanse** zamiast symbolu wieloznacznego.

    ![Przykład: Ustaw wartość finanse zamiast symbolu wieloznacznego w wewnętrznym adresie URL](./media/application-proxy-wildcard/52.png)

- W **zewnętrznym adresie URL**ustawiasz opcję **finanse** zamiast symbolu wieloznacznego.

    ![Przykład: Ustaw finanse zamiast symbolu wieloznacznego w zewnętrznym adresie URL](./media/application-proxy-wildcard/53.png)

- Wewnętrzna Nazwa SPN aplikacji wewnętrznej ustawiana jako **finanse** zamiast symbolu wieloznacznego.

    ![Przykład: Ustaw finanse zamiast symbolu wieloznacznego w konfiguracji nazwy SPN](./media/application-proxy-wildcard/54.png)

Ta konfiguracja implementuje następujący scenariusz:

![Pokazuje konfigurację zaimplementowaną w przykładowym scenariuszu](./media/application-proxy-wildcard/09.png)

Ponieważ `finance.adventure-works.com` jest bardziej szczegółowym adresem URL niż `*.adventure-works.com`, ma pierwszeństwo. Użytkownicy przechodzenia do `finance.adventure-works.com` mają środowisko określone w aplikacji finanse. W takim przypadku tylko pracownicy finanse mogą uzyskać dostęp do `finance.adventure-works.com`.

Jeśli masz wiele aplikacji opublikowanych na potrzeby finansów i masz `finance.adventure-works.com` jako zweryfikowaną domenę, możesz opublikować kolejną wieloznaczną aplikację `*.finance.adventure-works.com`. Ponieważ jest to bardziej szczegółowe niż ogólne `*.adventure-works.com`, ma pierwszeństwo w przypadku, gdy użytkownik uzyskuje dostęp do aplikacji w domenie finansów.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o **domenach niestandardowych**, zobacz [Praca z domenami niestandardowymi w usłudze Azure serwer proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md).
- Aby dowiedzieć się więcej o **publikowaniu aplikacji**, zobacz [publikowanie aplikacji przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md)
