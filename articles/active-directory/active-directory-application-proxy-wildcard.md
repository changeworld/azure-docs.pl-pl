---
title: Aplikacje symbolu wieloznacznego w serwer proxy aplikacji usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystać z aplikacji symbolu wieloznacznego w serwer proxy aplikacji usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: d5450da1-9e06-4d08-8146-011c84922ab5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: ea6817f80925c1989db13488472457e44801e7a8
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Aplikacje symbolu wieloznacznego w serwer proxy aplikacji usługi Azure Active Directory 

W usłudze Active Directory (Azure AD), konfigurowanie dużą liczbę lokalnych aplikacji może szybko stać się bezproblemowego zarządzania i wprowadza niepotrzebne ryzyko błędów konfiguracji, jeśli wiele z nich wymaga tych samych ustawień. Z [serwera Proxy aplikacji usługi Azure AD](manage-apps/application-proxy.md), przy użyciu funkcji publikowania aplikacji symboli wieloznacznych, publikowanie i zarządzanie jednocześnie wiele aplikacji może rozwiązać ten problem. Jest to rozwiązanie, które umożliwia:

-   Upraszcza czynności administracyjnych
-   Zmniejsz liczbę potencjalne błędy konfiguracji
-   Umożliwianie użytkownikom bezpiecznego dostępu do większej ilości zasobów

Ten artykuł zawiera informacje, które należy skonfigurować publikowanie aplikacji symbolu wieloznacznego w danym środowisku.


## <a name="create-a-wildcard-application"></a>Tworzenie aplikacji symboli wieloznacznych 

Jeśli masz grupę aplikacji z taką samą konfigurację można utworzyć mapowania zastosowań symboli wieloznacznych (*). Potencjalne kandydatów do zastosowań symboli wieloznacznych są aplikacji do udostępniania następujące ustawienia:

- Grupy użytkowników, którzy mają do nich dostęp
- Metoda rejestracji Jednokrotnej
- Dostęp Protokół (http, https)

Publikowanie aplikacji przy użyciu symboli wieloznacznych przypadku zarówno wewnętrzne i zewnętrzne adresy URL w następującym formacie:

> http (s) :/ / *. \<domeny\>

Na przykład: `http(s)://*.adventure-works.com`. Gdy wewnętrzne i zewnętrzne adresy URL można użyć różnych domenach, najlepszym rozwiązaniem, powinny być takie same. Publikowanie aplikacji, zostanie wyświetlony błąd, jeśli jeden z adresów URL nie zawiera symboli wieloznacznych.

Jeśli masz dodatkowe aplikacje z różnych ustawień konfiguracji, należy opublikować te wyjątki jako osobne aplikacji, aby zastąpić wartości domyślne ustawione dla symbolu wieloznacznego. Aplikacje bez symbolu wieloznacznego zawsze mają pierwszeństwo względem aplikacji symboli wieloznacznych. Z perspektywy konfiguracji są "tak" regularne aplikacji.

Tworzenie aplikacji symboli wieloznacznych jest oparte na tym samym [przepływu publikowania aplikacji](manage-apps/application-proxy-publish-azure-portal.md) dostępnej dla wszystkich innych aplikacji. Jedyna różnica polega na tym, czy zawiera symbol wieloznaczny adresy URL i potencjalnie konfiguracji logowania jednokrotnego.


## <a name="prerequisites"></a>Wymagania wstępne

### <a name="custom-domains"></a>Niestandardowe domeny

Gdy [domen niestandardowych](manage-apps/application-proxy-configure-custom-domain.md) są opcjonalne dla wszystkich innych aplikacji, są one wymagane w przypadku aplikacji symboli wieloznacznych. Utworzenie domeny niestandardowej wymaga:

1. Utwórz zweryfikowanej domeny w systemie Azure 
2. Przekaż certyfikat SSL w formacie PFX do serwera proxy aplikacji.

Należy rozważyć użycie certyfikat uniwersalny odpowiadające aplikacji, które mają być tworzone. Alternatywnie można użyć również certyfikat, który znajduje się tylko określone aplikacje. W takim przypadku tylko te aplikacje, które są wymienione w certyfikacie będzie dostępny za pośrednictwem tego mapowania zastosowań symboli wieloznacznych.

Ze względów bezpieczeństwa jest to wymagane twardym i firma Microsoft nie obsługuje symboli wieloznacznych dla aplikacji, których nie można używać domeny niestandardowej dla zewnętrznego adresu URL.

### <a name="dns-updates"></a>Aktualizacje DNS

Korzystając z domeny niestandardowe, należy utworzyć wpis DNS z rekordu CNAME jako zewnętrzny adres URL (na przykład `*.adventure-works.com`) wskazuje zewnętrzny adres URL punktu końcowego serwera proxy aplikacji. W przypadku aplikacji symboli wieloznacznych rekord CNAME musi wskazywać odpowiednich zewnętrzne adresy URL na:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

Aby upewnić się, że Twoje CNAME zostały skonfigurowane poprawnie, możesz użyć [nslookup](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup) na jednym z docelowymi punktami końcowymi, na przykład `expenses.adventure-works.com`.  Odpowiedź powinna zawierać alias już wspomniano (`<yourAADTenantId>.tenant.runtime.msappproxy.net`).


## <a name="considerations"></a>Zagadnienia do rozważenia


### <a name="accepted-formats"></a>Akceptowanych formatów

W przypadku aplikacji symboli wieloznacznych **wewnętrznego adresu URL** musi być sformatowany jako `http(s)://*.<domain>`. 

![Identyfikator aplikacji](./media/active-directory-application-proxy-wildcard\22.png)


Po skonfigurowaniu **zewnętrzny adres URL**, należy użyć następującego formatu: `https://*.<custom domain>` 

![Identyfikator aplikacji](./media/active-directory-application-proxy-wildcard\21.png)

Inne pozycje symbol wieloznaczny, wiele symboli wieloznacznych i innych ciągów wyrażeń regularnych nie są obsługiwane i powoduje błędy.


### <a name="excluding-applications-from-the-wildcard"></a>Wykluczanie aplikacji symbol wieloznaczny

Można wykluczyć aplikację z zastosowań symboli wieloznacznych przez

- Publikowanie aplikacji wyjątek jako regularne aplikacji 
- Włączanie symboli wieloznacznych tylko dla określonych aplikacji za pomocą ustawienia DNS  


Publikowanie aplikacji jako aplikacji regularne jest preferowaną metodą wykluczyć aplikację z symbolem wieloznacznym. Należy opublikować wykluczonych aplikacji przed aplikacjom symbolu wieloznacznego wymusić od początku listy wyjątków. Specyficzny aplikacji będzie zawsze mają pierwszeństwo — aplikacji publikowanych jako `budgets.finance.adventure-works.com` aplikacja ma pierwszeństwo przed `*.finance.adventure-works.com`, który z kolei ma pierwszeństwo przed aplikacji `*.adventure-works.com`. 

Można również ograniczyć symboli wieloznacznych, aby działał tylko dla określonych aplikacji do zarządzania infrastrukturą DNS. Jako najlepsze rozwiązanie należy utworzyć wpis CNAME, który zawiera symbol wieloznaczny i nieprawidłowy format zewnętrzny adres URL, które zostały skonfigurowane. Zamiast tego można jednak punktu adresy URL aplikacji do symboli wieloznacznych. Na przykład zamiast z `*.adventure-works.com`, punkt `hr.adventure-works.com`, `expenses.adventure-works.com` i `travel.adventure-works.com individually` do `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`. 

Jeśli używasz tej opcji, należy również inny wpis CNAME dla wartości `AppId.domain`, na przykład `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`, również wskazujące w tej samej lokalizacji. Można znaleźć **AppId** na stronie właściwości aplikacji zastosowań symboli wieloznacznych:

![Identyfikator aplikacji](./media/active-directory-application-proxy-wildcard\01.png)



### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>Ustawienie adresu URL strony głównej panelu MyApps

Zastosowań symboli wieloznacznych jest reprezentowana z tylko jednego kafelka w [panelu MyApps](https://myapps.microsoft.com). Domyślnie ten Kafelek jest ukryty. Aby wyświetlić kafelka i mieć ziemi użytkowników na danej stronie:

1. Postępuj zgodnie z wytycznymi dla [ustawienie adresu URL strony głównej](manage-apps/application-proxy-configure-custom-home-page.md).
2. Ustaw **Pokaż aplikacji** do **true** na stronie właściwości aplikacji.

### <a name="kerberos-constrained-delegation"></a>Ograniczone delegowanie protokołu Kerberos

Dla aplikacji za pomocą [kerberos ograniczonego delegowania (KCD) jako metodę logowania jednokrotnego](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md), wymienione nazwy SPN dla metody logowania jednokrotnego mogą także symbol wieloznaczny. Na przykład może być nazwa SPN: `HTTP/*.adventure-works.com`. Nadal trzeba poszczególnych nazw SPN skonfigurowanych na serwerach wewnętrznej bazy danych (na przykład `http://expenses.adventure-works.com and HTTP/travel.adventure-works.com`).



## <a name="scenario-1-general-wildcard-application"></a>Scenariusz 1: Mapowania zastosowań symboli wieloznacznych ogólne

W tym scenariuszu należy trzech różnych aplikacji, którą chcesz opublikować:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Wszystkie trzy aplikacje:

- Są używane przez wszystkich użytkowników
- Użyj *zintegrowane uwierzytelnianie systemu Windows* 
- Ma takie same właściwości


Można opublikować zastosowań symboli wieloznacznych, wykonując kroki opisane w temacie [publikowanie aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](manage-apps/application-proxy-publish-azure-portal.md). W tym scenariuszu przyjęto:

- Dzierżawcy z następującym Identyfikatorem: `000aa000-11b1-2ccc-d333-4444eee4444e` 

- Zweryfikowanej domeny o nazwie `adventure-works.com` został skonfigurowany.

- A **CNAME** wpis wskazujący `*.adventure-works.com` do `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` został utworzony.

Po [opisane kroki](manage-apps/application-proxy-publish-azure-portal.md), Utwórz nową aplikację serwera proxy aplikacji w dzierżawie. W tym przykładzie symbol wieloznaczny znajduje się w następujących pól:

- Wewnętrzny adres URL:

    ![Wewnętrzny adres URL](./media/active-directory-application-proxy-wildcard\42.png)


- Zewnętrzny adres URL:

    ![Zewnętrzny adres URL](./media/active-directory-application-proxy-wildcard\43.png)

 
- Aplikacja wewnętrzna nazwa SPN: 

    ![Konfiguracja nazwy SPN](./media/active-directory-application-proxy-wildcard\44.png)


Publikując zastosowań symboli wieloznacznych, można uzyskać dostęp trzy aplikacje przechodząc do adresów URL jest używany jako (na przykład `travel.adventure-works.com`).

Konfiguracja implementuje następującą strukturę:

![Identyfikator aplikacji](./media/active-directory-application-proxy-wildcard\05.png)

| Kolor | Opis |
| ---   | ---         |
| Niebieski  | Aplikacje opublikowane jawnie i będą widoczne w portalu Azure. |
| Szary  | Aplikacje można dostępny za pośrednictwem aplikacji nadrzędnej. |




## <a name="scenario-2-general-wildcard-application-with-exception"></a>Scenariusz 2: Mapowania zastosowań symboli wieloznacznych ogólne z powodu wyjątku

W tym scenariuszu należy ponadto trzech aplikacji ogólne innej aplikacji, `finance.adventure-works.com`, które powinny być dostępne tylko przez zespół finansowych. Z bieżącej struktury aplikacji będą dostępne za pośrednictwem aplikacji symboli wieloznacznych i wszyscy pracownicy aplikacji finansowych. Aby zmienić to ustawienie, można wykluczyć aplikację z z symboli wieloznacznych przez skonfigurowanie Finance jako osobne aplikacji z bardziej restrykcyjnymi uprawnieniami.



Należy upewnić się, że rekordy CNAME tego `finance.adventure-works.com` do końcowego określonych aplikacji, na stronie serwer Proxy aplikacji dla aplikacji. W tym scenariuszu `finance.adventure-works.com` wskazuje `https://finance-awcycles.msappproxy.net/`. 

Po [opisane kroki](manage-apps/application-proxy-publish-azure-portal.md), ten scenariusz wymaga następujących ustawień:


- W **wewnętrznego adresu URL**, możesz ustawić **finance** zamiast symbolu wieloznacznego. 

    ![Wewnętrzny adres URL](./media/active-directory-application-proxy-wildcard\52.png)

- W **zewnętrzny adres URL**, możesz ustawić **finance** zamiast symbolu wieloznacznego. 

    ![Zewnętrzny adres URL](./media/active-directory-application-proxy-wildcard\53.png)

- Wewnętrzny SPN aplikacji należy ustawić **finance** zamiast symbolu wieloznacznego.

    ![Konfiguracja nazwy SPN](./media/active-directory-application-proxy-wildcard\54.png)


Ta konfiguracja implementuje następujący scenariusz:

![Scenariusz](./media/active-directory-application-proxy-wildcard\09.png)

Ponieważ `finance.adventure-works.com` jest bardziej określony adres URL niż `*.adventure-works.com`, ma pierwszeństwo przed. Użytkownicy, przechodząc do `finance.adventure-works.com` doświadczenie określone w aplikacji do zasobów finansowych. W takim przypadku tylko pracowników finansowego mają dostęp do `finance.adventure-works.com`.

Jeśli masz wiele aplikacji publikowanych finansów i masz `finance.adventure-works.com` jako zweryfikowanej domeny, można opublikować innego mapowania zastosowań symboli wieloznacznych `*.finance.adventure-works.com`. Ponieważ jest to bardziej szczegółowy niż ogólnego `*.adventure-works.com`, ma pierwszeństwo, jeśli użytkownik uzyskuje dostęp do aplikacji w domenie finansowych.


## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat:

- **Domen niestandardowych**, zobacz [Praca z domenami niestandardowymi w serwera Proxy aplikacji usługi Azure AD](manage-apps/application-proxy-configure-custom-domain.md).

- **Publikowanie aplikacji**, zobacz [publikowanie aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](manage-apps/application-proxy-publish-azure-portal.md)


