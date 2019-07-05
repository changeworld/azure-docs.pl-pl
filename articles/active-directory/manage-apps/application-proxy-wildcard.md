---
title: Aplikacje z symbolami wieloznacznymi serwera proxy aplikacji usługi Azure Active Directory | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 8cd29fc00a1c25a7c092393591060ca7e2938155
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481271"
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Aplikacje z symbolami wieloznacznymi serwera proxy aplikacji usługi Azure Active Directory

W usłudze Active Directory (Azure AD), konfigurowanie dużej liczby lokalnych aplikacji może szybko stać się bezproblemowego zarządzania i wprowadza niepotrzebne ryzyko błędów konfiguracji, jeśli wiele z nich wymaga tych samych ustawień. Za pomocą [serwera Proxy aplikacji usługi Azure AD](application-proxy.md), możesz rozwiązać ten problem przy użyciu funkcji publikowania aplikacji symbolu wieloznacznego do publikowania i zarządzanie wielu aplikacji jednocześnie. Jest to rozwiązanie, która pozwala na:

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

> http (s) :/ / *. \<domeny\>

Na przykład: `http(s)://*.adventure-works.com`. Gdy wewnętrzne i zewnętrzne adresy URL można użyć różnych domenach, najlepszym rozwiązaniem, powinna być taka sama. Publikowanie aplikacji, zostanie wyświetlony błąd, jeśli jeden z adresów URL nie zawiera symboli wieloznacznych.

Jeśli masz dodatkowe aplikacje za pomocą różnych ustawień konfiguracji, należy opublikować te wyjątki, jako osobne aplikacje zastąpić wartości domyślne ustawione dla symbolu wieloznacznego. Aplikacje bez symboli wieloznacznych zawsze pierwszeństwo aplikacje z symbolami wieloznacznymi. Z perspektywy konfiguracji są "tak" zwykłych aplikacji.

Tworzenie aplikacji z symbolami wieloznacznymi opiera się na tym samym [przepływ publikowania aplikacji](application-proxy-add-on-premises-application.md) dostępnej dla wszystkich innych aplikacji. Jedyna różnica polega na zawierać symbol wieloznaczny w adresy URL i potencjalnie konfiguracji logowania jednokrotnego.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, upewnij się, że zostały spełnione następujące wymagania.

### <a name="custom-domains"></a>Niestandardowe domeny

Gdy [domen niestandardowych](application-proxy-configure-custom-domain.md) są opcjonalne dla innych aplikacji, są one wymagane w przypadku aplikacje z symbolami wieloznacznymi. Tworzenie domen niestandardowych wymaga:

1. Utwórz zweryfikowanej domeny w obrębie platformy Azure.
2. Przekaż certyfikat SSL w formacie PFX do usługi serwera proxy aplikacji.

Należy rozważyć użycie certyfikat uniwersalny, aby dopasować aplikację, która ma zostać utworzone. Alternatywnie umożliwia także certyfikat, który znajduje się tylko określonych aplikacji. W takim przypadku tylko te aplikacje, które są wymienione w certyfikacie będą dostępne za pomocą tej aplikacji z symbolami wieloznacznymi.

Ze względów bezpieczeństwa jest to bezwględnie wymagane, i symboli wieloznacznych nie będą obsługiwane w przypadku aplikacji, które nie mogą używać domeny niestandardowej do zewnętrznego adresu URL.

### <a name="dns-updates"></a>Aktualizacje rekordów DNS

Korzystając z domenami niestandardowymi, musisz utworzyć wpis DNS przy użyciu rekordu CNAME jako zewnętrzny adres URL (na przykład `*.adventure-works.com`) wskazuje zewnętrzny adres URL punktu końcowego serwera proxy aplikacji. W przypadku aplikacji symboli wieloznacznych rekord CNAME musi wskazywać odpowiednie zewnętrzne adresy URL:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

Aby upewnić się, że Twoje CNAME zostały skonfigurowane poprawnie, można użyć [nslookup](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup) na jednym z docelowymi punktami końcowymi, na przykład `expenses.adventure-works.com`.  Twoja odpowiedź powinna zawierać już wspomniano aliasu (`<yourAADTenantId>.tenant.runtime.msappproxy.net`).

## <a name="considerations"></a>Zagadnienia do rozważenia

Poniżej przedstawiono kilka kwestii, które należy podjąć w celu aplikacje z symbolami wieloznacznymi.

### <a name="accepted-formats"></a>Akceptowane formaty

W przypadku aplikacji symboli wieloznacznych **wewnętrzny adres URL** muszą być sformatowane jako `http(s)://*.<domain>`.

![Wewnętrzny adres URL, użyj formatu HTTP (s) :/ / *. < domena >](./media/application-proxy-wildcard/22.png)

Po skonfigurowaniu **zewnętrzny adres URL**, należy użyć następującego formatu: `https://*.<custom domain>`

![Dla zewnętrznego adresu URL, użyj następującej domeny https://*.<custom format >](./media/application-proxy-wildcard/21.png)

Innych pozycji symbolu wieloznacznego, wiele symboli wieloznacznych lub inne ciągi wyrażenie regularne nie są obsługiwane i powoduje błędy.

### <a name="excluding-applications-from-the-wildcard"></a>Wykluczanie aplikacji z symbolami wieloznacznymi

Można wykluczyć aplikację z aplikacji symboli wieloznacznych

- Publikowanie aplikacji wyjątek jako regularne aplikacji
- Włączanie symboli wieloznacznych tylko dla określonych aplikacji za pomocą ustawienia DNS  

Publikowanie aplikacji jako aplikacji regularne jest preferowaną metodą, aby wykluczyć aplikację z symbolem wieloznacznym. Należy opublikować wykluczonych aplikacji przed aplikacji symboli wieloznacznych, aby upewnić się, że listy wyjątków są wymuszane na początku wybrać odpowiedni. Najbardziej konkretnej aplikacji będzie zawsze mają pierwszeństwo — do aplikacji opublikowanych jako `budgets.finance.adventure-works.com` pierwszeństwo aplikacji `*.finance.adventure-works.com`, który z kolei pierwszeństwo aplikacji `*.adventure-works.com`.

Można również ograniczyć symboli wieloznacznych, aby działał tylko dla określonych aplikacji za pomocą usługi zarządzania usługą DNS. Najlepszym rozwiązaniem należy utworzyć wpis CNAME, który zawiera symbol wieloznaczny i nieprawidłowy format zewnętrzny adres URL, które zostały skonfigurowane. Jednakże możesz zamiast tego wskazać adresów URL określonych aplikacji symbole wieloznaczne. Na przykład, zamiast z `*.adventure-works.com`, punkt `hr.adventure-works.com`, `expenses.adventure-works.com` i `travel.adventure-works.com individually` do `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`. 

Jeśli używasz tej opcji, należy również inny wpis CNAME dla wartości `AppId.domain`, na przykład `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`, również wskazując w tej samej lokalizacji. Możesz znaleźć **AppId** na stronie właściwości aplikacji zastosowań symboli wieloznacznych:

![Znajdź identyfikator aplikacji na stronie właściwości aplikacji](./media/application-proxy-wildcard/01.png)

### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>Adres URL strony głównej panelu MyApps ustawienie

Zastosowań symboli wieloznacznych jest reprezentowane przez tylko jednego kafelka [panelu MyApps](https://myapps.microsoft.com). Domyślnie ten Kafelek jest ukryty. Pokaż Kafelek i ziemi użytkowników na określonej stronie:

1. Postępuj zgodnie z wytycznymi dla [ustawienie adresu URL strony głównej](application-proxy-configure-custom-home-page.md).
2. Ustaw **Pokaż aplikacji** do **true** na stronie właściwości aplikacji.

### <a name="kerberos-constrained-delegation"></a>Ograniczone delegowanie protokołu Kerberos

Dla aplikacji za pomocą [kerberos ograniczone delegowanie (KCD) jako metodę logowania jednokrotnego](application-proxy-configure-single-sign-on-with-kcd.md), nazwę SPN podane dla metody logowania jednokrotnego może być również konieczne symbol wieloznaczny. Główna nazwa usługi może być na przykład: `HTTP/*.adventure-works.com`. Nadal musisz poszczególne nazwy SPN skonfigurowany na serwerach wewnętrznej bazy danych (na przykład `http://expenses.adventure-works.com and HTTP/travel.adventure-works.com`).

## <a name="scenario-1-general-wildcard-application"></a>Scenariusz 1: Zastosowań symboli wieloznacznych ogólne

W tym scenariuszu masz trzech różnych aplikacji, którą chcesz opublikować:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Wszystkie trzy aplikacje:

- Są używane przez wszystkich użytkowników
- Użyj *zintegrowane uwierzytelnianie Windows*
- Ma te same właściwości

Możesz opublikować aplikację symboli wieloznacznych wykonując kroki opisane w [publikowania aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md). W tym scenariuszu przyjmuje:

- Dzierżawy z następującym Identyfikatorem: `000aa000-11b1-2ccc-d333-4444eee4444e`
- Zweryfikowanej domeny o nazwie `adventure-works.com` został skonfigurowany.
- A **CNAME** wpis, który wskazuje `*.adventure-works.com` do `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` została utworzona.

Następujące [opisane kroki](application-proxy-add-on-premises-application.md), Utwórz nową aplikację serwera proxy aplikacji w dzierżawie. W tym przykładzie symbol wieloznaczny znajduje się w następujących pól:

- Wewnętrzny adres URL:

    ![Przykład: Symboli wieloznacznych w wewnętrznego adresu URL](./media/application-proxy-wildcard/42.png)

- Zewnętrzny adres URL:

    ![Przykład: Symboli wieloznacznych w zewnętrzny adres URL](./media/application-proxy-wildcard/43.png)

- Główna nazwa usługi aplikacji wewnętrznej:

    ![Przykład: Symboli wieloznacznych w konfiguracji nazwę SPN](./media/application-proxy-wildcard/44.png)

Przez opublikowanie aplikacji symboli wieloznacznych, możesz teraz uzyskiwać dostęp trzech aplikacji, przechodząc do adresów URL jest używany do (na przykład `travel.adventure-works.com`).

Konfiguracja implementuje następującą strukturę:

![Przedstawia strukturę implementowany przez przykładową konfigurację](./media/application-proxy-wildcard/05.png)

| Kolor | Opis |
| ---   | ---         |
| Niebieski  | Aplikacje jawnie opublikowane i są widoczne w witrynie Azure portal. |
| Szary  | Aplikacje możesz za pośrednictwem aplikacji nadrzędnej. |

## <a name="scenario-2-general-wildcard-application-with-exception"></a>Scenariusz 2: Zastosowań symboli wieloznacznych ogólne, z wyjątkiem

W tym scenariuszu należy ponadto trzy aplikacje ogólne innej aplikacji, `finance.adventure-works.com`, która powinna być dostępna tylko przez zespół Finanse. Za pomocą bieżącej struktury aplikacji Twoja aplikacja finansowa będzie dostępny za pośrednictwem aplikacji symboli wieloznacznych i wszyscy pracownicy. Aby to zmienić, wykluczasz aplikacji z Twojego symboli wieloznacznych, konfigurując Finanse jako oddzielną aplikację przy użyciu bardziej restrykcyjne uprawnienia.

Należy upewnić się, że rekordy CNAME istnieje, na którą wskazuje `finance.adventure-works.com` aplikacji określonego punktu końcowego, określonej na stronie serwer Proxy aplikacji dla aplikacji. W tym scenariuszu `finance.adventure-works.com` wskazuje `https://finance-awcycles.msappproxy.net/`.

Następujące [opisane kroki](application-proxy-add-on-premises-application.md), ten scenariusz wymaga następujących ustawień:

- W **wewnętrzny adres URL**, możesz ustawić **finance** zamiast symbolu wieloznacznego.

    ![Przykład: Not set zamiast symboli wieloznacznych w wewnętrznego adresu URL](./media/application-proxy-wildcard/52.png)

- W **zewnętrzny adres URL**, możesz ustawić **finance** zamiast symbolu wieloznacznego.

    ![Przykład: Not set zamiast symboli wieloznacznych w zewnętrzny adres URL](./media/application-proxy-wildcard/53.png)

- Wewnętrzny główna nazwa usługi aplikacji można ustawić **finance** zamiast symbolu wieloznacznego.

    ![Przykład: Not set zamiast symboli wieloznacznych w konfiguracji nazwę SPN](./media/application-proxy-wildcard/54.png)

Ta konfiguracja implementuje następujący scenariusz:

![Przedstawia konfigurację implementowany przez przykładowy scenariusz](./media/application-proxy-wildcard/09.png)

Ponieważ `finance.adventure-works.com` jest adresem URL bardziej szczegółowe niż `*.adventure-works.com`, ma pierwszeństwo przed. Użytkownicy, przechodząc do `finance.adventure-works.com` się w taki sposób, określona w aplikacji, finansów, zasobów. W takim przypadku tylko finansowymi wyłącznie pracownikom zajmującym mogą uzyskiwać dostęp do `finance.adventure-works.com`.

Jeśli masz wiele aplikacji opublikowana na potrzeby Finanse i masz `finance.adventure-works.com` jako zweryfikowanej domeny, można opublikować innej aplikacji z symbolami wieloznacznymi `*.finance.adventure-works.com`. Ponieważ jest to bardziej szczegółowe niż ogólnego `*.adventure-works.com`, ma pierwszeństwo, jeśli użytkownik uzyskuje dostęp do aplikacji w domenie finansowych.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat **domen niestandardowych**, zobacz [Praca z domenami niestandardowymi na serwerze Proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md).
- Aby dowiedzieć się więcej na temat **publikowania aplikacji**, zobacz [publikowania aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md)
