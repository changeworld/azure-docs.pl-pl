---
title: Wprowadzenie do serwera Proxy aplikacji usługi Azure AD — Instalowanie łącznika | Dokumentacja firmy Microsoft
description: Włącz serwer Proxy aplikacji w witrynie Azure portal i zainstaluj łączniki dla zwrotnego serwera proxy.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: barbkess
ms.reviewer: japere
ms.custom: it-pro
ms.openlocfilehash: 9a869055613da6465a9beda9b8edc1bf812b6dfe
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51712113"
---
# <a name="get-started-with-application-proxy-and-install-the-connector"></a>Rozpoczynanie pracy z usługą serwera Proxy aplikacji i zainstalować łącznik
W tym artykule przedstawiono kroki, aby włączyć serwer Proxy aplikacji w usłudze Azure Active Directory (Azure AD).

Jeśli nie masz jeszcze pamiętać o korzyści z zabezpieczeń i wydajności serwera Proxy aplikacji udostępnia w Twojej organizacji, Dowiedz się więcej o [jak zapewnić bezpieczny dostęp zdalny do aplikacji lokalnych](application-proxy.md).

## <a name="prerequisites"></a>Wymagania wstępne
Aby włączyć serwer Proxy aplikacji, potrzebne są:

* A [subskrypcji usługi Microsoft Azure AD podstawowa lub premium](https://azure.microsoft.com/pricing/details/active-directory). 
* Konto administratora aplikacji.

### <a name="windows-server"></a>Windows server
Wymagany serwer z systemem Windows Server 2012 R2 lub nowszym na której można zainstalować łącznik serwera Proxy aplikacji. Serwer musi łączyć się z usługi serwera Proxy aplikacji na platformie Azure i aplikacji lokalnych, które publikujesz.

Systemu windows server musi mieć protokół TLS 1.2, włączone, przed zainstalowaniem łącznika serwera Proxy aplikacji. Istniejące łączniki z wersjami starszymi niż 1.5.612.0 będą w dalszym ciągu działać we wcześniejszych wersjach TLS do odwołania. Aby włączyć protokół TLS 1.2:

1. Ustaw następujące klucze rejestru:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

2. Uruchom ponownie serwer

Logowanie jednokrotne do aplikacji, które używają delegowania Contrained protokołu Kerberos (KCD) serwer Windows i aplikacji, które są publikowane na potrzeby znajdować się w tej samej domenie usługi Active Directory. Aby uzyskać więcej informacji, zobacz [ograniczonego delegowania protokołu Kerberos do logowania jednokrotnego przy użyciu serwera Proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md).
  
### <a name="proxy-servers"></a>Serwery proxy

Jeśli Twoja organizacja używa serwerów proxy, aby nawiązać połączenie z Internetem, należy skonfigurować je dla serwera Proxy aplikacji.  Aby uzyskać więcej informacji, zobacz [pracy przy użyciu istniejących serwerów proxy lokalnych](application-proxy-configure-connectors-with-proxy-servers.md). 



## <a name="open-your-ports"></a>Otwieranie z portów

Aby przygotować środowisko dla serwera Proxy aplikacji usługi Azure AD, należy najpierw włączyć komunikację z centrów danych platformy Azure. Jeśli na ścieżce znajduje się zapora, upewnij się, że jest otwarta, aby umożliwić łącznikowi wysyłanie żądań protokołu HTTPS (TCP) do serwera proxy aplikacji.

1. Otwórz następujące porty **wychodzącego** ruchu:

   | Numer portu | Jak są używane |
   | --- | --- |
   | 80 | Pobieranie odwołań certyfikatów list podczas sprawdzania poprawności certyfikatu SSL |
   | 443 | Cała komunikacja wychodząca przy użyciu usługi Serwer Proxy aplikacji |

   Jeśli Zapora wymusza ruchu sieciowym pogrupowane według pochodzące użytkowników, należy otworzyć te porty dla ruchu z usługi Windows, które są uruchamiane jako usługa sieciowa.

   > [!IMPORTANT]
   > Tabela odzwierciedla wymaganiach dotyczących portów dla wersji łącznika 1.5.132.0 i nowszych. Jeśli nadal masz starszą wersję łącznika, również należy włączyć następujące porty oprócz 8080 80 i 443: 5671, 9350 9090-9091 9352, 10100 — 10120.
   >
   >Aby uzyskać informacje o aktualizowaniu łączników do najnowszej wersji, zobacz [łączników serwera Proxy aplikacji usługi AD Azure zrozumienie](application-proxy-connectors.md#automatic-updates).

2. Jeśli zapora lub serwer proxy umożliwia DNS umieszczania na białej liście, możesz umieścić na liście dozwolonych połączeń: msappproxy.net i servicebus.windows.net. Jeśli nie, musisz zezwolić na dostęp do [zakresów adresów IP centrum danych Azure](https://www.microsoft.com/download/details.aspx?id=41653), które są aktualizowane co tydzień.

3. Firma Microsoft używa czterech adresów, aby zweryfikować certyfikaty. Zezwalaj na dostęp do następujących adresów URL, jeśli nie zostało zrobione w przypadku innych produktów:
   * mscrl.microsoft.com:80
   * crl.microsoft.com:80
   * ocsp.msocsp.com:80
   * www.microsoft.com:80

4. Łącznik musi mieć dostęp do login.windows.net i login.microsoftonline.com dla procesu rejestracji.


## <a name="install-and-register-a-connector"></a>Instalowanie i rejestrowanie łącznika
1. Zaloguj się jako administrator w [witryny Azure portal](https://portal.azure.com/).
2. Bieżącego katalogu pojawia się w obszarze swoją nazwę użytkownika w prawym górnym rogu. Jeśli potrzebujesz zmienić katalogi, wybierz tę ikonę.
3. Przejdź do **usługi Azure Active Directory** > **serwera Proxy aplikacji**.

   ![Przejdź do serwera Proxy aplikacji](./media/application-proxy-enable/app_proxy_navigate.png)

4. Wybierz **Pobierz łącznik**.

   ![Pobierz łącznik](./media/application-proxy-enable/download_connector.png)

5. Uruchom plik **AADApplicationProxyConnectorInstaller.exe** na serwerze przygotowanym zgodnie z wymaganiami wstępnymi.
6. Postępuj zgodnie z instrukcjami w kreatorze, aby przeprowadzić instalację. Podczas instalacji wyświetlany jest monit o zarejestrowanie łącznika serwera Proxy aplikacji dzierżawy usługi Azure AD.

   * Podaj poświadczenia administratora globalnego usługi Azure AD. Administrator globalny dzierżawy może mieć inne poświadczenia platformy Microsoft Azure niż Twoje.
   * Upewnij się, że administrator, który rejestruje łącznik, jest w tym samym katalogu, w którym włączono usługę serwera proxy aplikacji. Na przykład jeśli domena dzierżawy to contoso.com, administratorem powinien być admin@contoso.com lub dowolny alias w tej domenie.
   * Jeśli **Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer** ustawiono **na** na serwerze, na którym jest instalowany łącznik, mogą nie być wyświetlane na ekranie rejestracji. Aby uzyskać dostęp, postępuj zgodnie z instrukcjami w komunikacie o błędzie. Upewnij się, że pozycja Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer jest wyłączona.

Aby zapewnić wysoką dostępność, należy wdrożyć co najmniej dwa łączniki. Każdy łącznik musi zostać zarejestrowany oddzielnie.

## <a name="test-that-the-connector-installed-correctly"></a>Test, który jest poprawnie zainstalowany łącznik

Aby potwierdzić, że nowy łącznik zainstalowane poprawnie przez sprawdzenie, czy je w jednej witrynie Azure portal lub na serwerze. 

W witrynie Azure portal, zaloguj się do dzierżawy i przejdź do **usługi Azure Active Directory** > **serwera Proxy aplikacji**. Wszystkie łączniki i grupy łączników są wyświetlane na tej stronie. Wybierz łącznik, aby wyświetlić jego szczegóły, lub przenieść ją do grupy inny łącznik. 

Na serwerze Sprawdź listę aktywnych usług dla łącznika i aktualizator łącznika. Te dwie usługi powinny uruchomić, działanie od razu, ale jeśli nie, włącz je: 

   * **Łącznik serwera proxy aplikacji usługi Microsoft AAD** umożliwia łączność

   * **Aktualizator łącznika serwera Proxy aplikacji usługi Microsoft AAD** to automatyczna usługa aktualizacji. Aktualizacji sprawdza dostępność nowych wersji łącznika i aktualizuje go zgodnie z potrzebami.

   ![Usługi łącznika serwera proxy aplikacji — zrzut ekranu](./media/application-proxy-enable/app_proxy_services.png)

Aby uzyskać informacje na temat łączników i jak bądź na bieżąco, zobacz [łączników serwera Proxy aplikacji usługi AD Azure zrozumienie](application-proxy-connectors.md).


## <a name="next-steps"></a>Kolejne kroki
Teraz można przystąpić do [publikowania aplikacji za pomocą serwera proxy aplikacji](application-proxy-publish-azure-portal.md).

Jeśli masz aplikacje znajdujące się w oddzielnych sieciach lub w różnych lokalizacjach, należy zorganizować różne łączniki w jednostki logiczne za pomocą grup łączników. Dowiedz się więcej o [pracy z łącznikami serwera proxy aplikacji](application-proxy-connector-groups.md).
