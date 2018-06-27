---
title: Rozpoczynanie pracy serwera Proxy aplikacji usługi Azure AD - zainstalować łącznik | Dokumentacja firmy Microsoft
description: Włącz serwer Proxy aplikacji w portalu Azure i zainstaluj łączniki dla zwrotnego serwera proxy.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 0ac14f792c63ea06a484eb5b522c4d33958538ed
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025905"
---
# <a name="get-started-with-application-proxy-and-install-the-connector"></a>Rozpoczynanie pracy z serwera Proxy aplikacji i zainstalować łącznik
W tym artykule przedstawiono procedurę włączania serwera proxy aplikacji usługi Microsoft Azure AD dla katalogu w chmurze w ramach usługi Azure AD.

Jeśli nie masz jeszcze znane korzyści w zakresie zabezpieczeń i wydajności serwera Proxy aplikacji oferuje organizacji, Dowiedz się więcej o [jak zapewnić bezpieczny zdalny dostęp do aplikacji lokalnych](application-proxy.md).

## <a name="application-proxy-prerequisites"></a>Wymagania wstępne serwera proxy aplikacji
Wymagania umożliwiające włączenie i używanie usług serwera proxy aplikacji:

* [Subskrypcja usługi Microsoft Azure AD w warstwie Podstawowa lub Premium](../fundamentals/active-directory-whatis.md) i katalog usługi Azure AD, dla którego jesteś administratorem globalnym.
* Serwer z systemem Windows Server 2012 R2 lub 2016, w którym można zainstalować łącznik serwera Proxy aplikacji. Serwer musi mieć możliwość łączenia się z usługami serwera Proxy aplikacji w chmurze i lokalnych aplikacji, które są publikowane.
  * Dla rejestracji jednokrotnej do aplikacji opublikowanych przy użyciu ograniczone delegowanie protokołu Kerberos tej maszyny powinny być przyłączonych do domeny w tej samej domenie AD, aplikacje, które są publikowane. Aby uzyskać informacje, zobacz [KCD dla logowania jednokrotnego przy użyciu serwera Proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md).

Jeśli organizacja używa serwerów proxy, aby nawiązać połączenie z Internetem, przeczytaj [pracy przy użyciu istniejących lokalnych serwerów proxy](application-proxy-configure-connectors-with-proxy-servers.md) szczegółowe informacje na temat konfigurowania je przed rozpoczęciem pracy z serwerem Proxy aplikacji.

## <a name="open-your-ports"></a>Otwieranie portów sieci

Aby przygotować swoje środowisko dla serwera Proxy aplikacji usługi Azure AD, należy najpierw włączyć komunikację w centrach danych platformy Azure. Jeśli na ścieżce znajduje się zapora, upewnij się, że jest otwarta, aby umożliwić łącznikowi wysyłanie żądań protokołu HTTPS (TCP) do serwera proxy aplikacji.

1. Otwórz następujące porty, aby **wychodzących** ruchu:

   | Numer portu | Jak jest używany |
   | --- | --- |
   | 80 | Pobieranie odwołania certyfikatu list podczas sprawdzania poprawności certyfikatu SSL |
   | 443 | Cała komunikacja wychodząca z usługą serwera Proxy aplikacji |

   Jeśli Zapora wymusza ruch odpowiednio dla użytkowników generujących, należy otworzyć te porty dla ruchu z usług systemu Windows, które działają jako usługa sieciowa.

   > [!IMPORTANT]
   > Tabela odzwierciedla wymagania dotyczące portów dla wersji łącznika 1.5.132.0 i nowszych. Jeśli nadal masz starszą wersję łącznika, należy włączyć następujące porty oprócz 8080 80 i 443: 5671 9350 9090 9091, 9352, 10100 — 10120.
   >
   >Aby uzyskać informacje o aktualizowaniu łączniki do najnowszej wersji, zobacz [łączniki serwera Proxy aplikacji usługi AD zrozumieć Azure](application-proxy-connectors.md#automatic-updates).

2. Jeśli zapora lub serwer proxy zezwala na listę dozwolonych podobnej DNS, możesz połączeń dozwolonych msappproxy.net i servicebus.windows.net. Jeśli nie, musisz zezwolić na dostęp do [zakresy IP centrum danych Azure](https://www.microsoft.com/download/details.aspx?id=41653), które są aktualizowane co tydzień.

3. Firma Microsoft używa czterech adresów do weryfikowania certyfikatów. Zezwalaj na dostęp do następujących adresów URL. Jeśli nie zostało to jeszcze zrobione dla innych produktów:
   * mscrl.microsoft.com:80
   * crl.microsoft.com:80
   * ocsp.msocsp.com:80
   * www.microsoft.com:80

4. Twoje łącznika musi mieć dostęp do login.windows.net i login.microsoftonline.com dla procesu rejestracji.


## <a name="install-and-register-a-connector"></a>Instalowanie i rejestrowanie łącznika
1. Zaloguj się jako administrator w [portalu Azure](https://portal.azure.com/).
2. Bieżący katalog jest wyświetlany w obszarze swoją nazwę użytkownika w prawym górnym rogu. Jeśli musisz zmienić katalogów, zaznacz tę ikonę.
3. Przejdź do **usługi Azure Active Directory** > **serwera Proxy aplikacji**.

   ![Przejdź do serwera Proxy aplikacji](./media/application-proxy-enable/app_proxy_navigate.png)

4. Wybierz **Pobierz łącznik**.

   ![Pobierz łącznik](./media/application-proxy-enable/download_connector.png)

5. Uruchom plik **AADApplicationProxyConnectorInstaller.exe** na serwerze przygotowanym zgodnie z wymaganiami wstępnymi.
6. Postępuj zgodnie z instrukcjami w kreatorze, aby przeprowadzić instalację. Podczas instalacji wyświetlany jest monit o zarejestrowanie łącznika serwera proxy aplikacji dzierżawy usługi Azure AD.

   * Podaj poświadczenia administratora globalnego usługi Azure AD. Administrator globalny dzierżawy może mieć inne poświadczenia platformy Microsoft Azure niż Twoje.
   * Upewnij się, że administrator, który rejestruje łącznik, jest w tym samym katalogu, w którym włączono usługę serwera proxy aplikacji. Na przykład jeśli domena dzierżawy to contoso.com, administratorem powinien być admin@contoso.com lub dowolny alias w tej domenie.
   * Jeśli **Konfiguracja zwiększonych zabezpieczeń** ustawiono **na** na serwerze, na którym jest instalowany łącznik, może nie być wyświetlana na ekranie rejestracji. Aby uzyskać dostęp, postępuj zgodnie z instrukcjami w komunikacie o błędzie. Upewnij się, że pozycja Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer jest wyłączona.

Aby zapewnić wysoką dostępność, należy wdrożyć co najmniej dwa łączniki. Każdy łącznik musi zostać zarejestrowany oddzielnie.

## <a name="test-that-the-connector-installed-correctly"></a>Test, który jest poprawnie zainstalowany łącznik

Można potwierdzić, że nowy łącznik prawidłowo zainstalowane przez zaewidencjonowanie go albo portalu Azure lub na serwerze. 

W portalu Azure, zaloguj się do dzierżawy i przejdź do **usługi Azure Active Directory** > **serwera Proxy aplikacji**. Wszystkie łączniki i grup łącznika są wyświetlane na tej stronie. Wybierz łącznik, aby wyświetlić jego szczegóły. lub przenieś go do grupy różnych łącznika. 

Na serwerze Sprawdź listę aktywnych usług dla łącznika i aktualizator łącznika. Te dwie usługi należy natychmiast uruchomione, ale jeśli nie, włącz je: 

   * **Łącznik serwera proxy aplikacji usługi Microsoft AAD** umożliwia łączność

   * **Aktualizator łącznika serwera Proxy aplikacji usługi AAD Microsoft** jest automatyczna usługa aktualizacji. Aktualizacji sprawdza dostępność nowych wersji łącznika i aktualizuje go w razie potrzeby.

   ![Usługi łącznika serwera proxy aplikacji — zrzut ekranu](./media/application-proxy-enable/app_proxy_services.png)

Aby uzyskać informacje na temat łączników i jak bądź na bieżąco, zobacz [łączniki serwera Proxy aplikacji usługi AD zrozumieć Azure](application-proxy-connectors.md).


## <a name="next-steps"></a>Kolejne kroki
Teraz można przystąpić do [publikowania aplikacji za pomocą serwera proxy aplikacji](application-proxy-publish-azure-portal.md).

Jeśli masz aplikacje, które znajdują się w różnych sieciach lub innej lokalizacji, należy użyć grup łącznika do organizowania różnych łączniki do jednostek logicznych. Dowiedz się więcej o [pracy z łącznikami serwera proxy aplikacji](application-proxy-connector-groups.md).
