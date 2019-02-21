---
title: Konfigurowanie łączności SSL w usłudze Azure Database for PostgreSQL
description: Instrukcje i informacje, aby skonfigurować usługi Azure Database for PostgreSQL i skojarzonych aplikacji, aby prawidłowo używać połączeń SSL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 13a1ed626e7741c90cf902c9ed01911985ca8424
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56453447"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql"></a>Konfigurowanie łączności SSL w usłudze Azure Database for PostgreSQL
Usługa Azure Database for postgresql w warstwie preferuje łączenia aplikacji klienta do usługi PostgreSQL, przy użyciu protokołu Secure Sockets Layer (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacją kliencką ułatwia ochronę przed atakami typu man-in-the-middle dzięki szyfrowaniu strumienia danych między serwerem a aplikacją.

Domyślnie usługa bazy danych PostgreSQL skonfigurowano wymagają połączenia SSL. Opcjonalnie możesz wyłączyć wymaganie protokołu SSL w celu łączenia się z usługą bazy danych, jeśli Twoja aplikacja kliencka nie obsługuje łączności SSL. 

## <a name="enforcing-ssl-connections"></a>Wymuszanie połączeń SSL
Dla wszystkich baz danych dla serwerów MySQL dostarczanymi za pośrednictwem witryny Azure portal i interfejsu wiersza polecenia platformy Azure wymuszanie połączeń SSL jest domyślnie włączona. 

Podobnie parametry połączenia, które są wstępnie zdefiniowane w ustawieniach "Parametrów połączenia" w danych serwera w witrynie Azure portal zawierają parametry wymagane dla języków nawiązać połączenie z serwerem bazy danych przy użyciu protokołu SSL. Parametr SSL zależy od łącznika, na przykład "ssl = true" lub "sslmode = wymagają" lub "sslmode = wymagane" i inne.

## <a name="configure-enforcement-of-ssl"></a>Konfigurowanie wymuszania protokołu SSL
Opcjonalnie można wyłączyć wymuszania łączności SSL. Microsoft Azure zaleca zawsze umożliwiające **połączenia Wymuszaj połączenie SSL** ustawienie w celu uzyskania zwiększonych zabezpieczeń.

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal
Odwiedź stronę usługi Azure Database for postgresql w warstwie serwera, a następnie kliknij przycisk **zabezpieczenia połączeń**. Użyj przycisku przełącznika, aby włączyć lub wyłączyć **połączenia Wymuszaj połączenie SSL** ustawienie. Następnie kliknij przycisk **Zapisz**. 

![Zabezpieczenia połączeń — Wyłącz wymuszanie protokołu SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Możesz sprawdzić ustawienia, przeglądając **Przegląd** strony, aby zobaczyć **stan wymuszenia protokołu SSL** wskaźnika.

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure
Można włączać lub wyłączać **Wymuszanie protokołu ssl** przy użyciu parametru `Enabled` lub `Disabled` wartości odpowiednio w wiersza polecenia platformy Azure.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Upewnij się, Twoja aplikacja lub framework obsługuje połączenia SSL
Wiele typowych struktur aplikacji używających PostgreSQL dla swoich usług bazy danych, takich jak Drupal i Django, nie należy włączać SSL domyślnie podczas instalacji. Włączanie łączności SSL musi odbywać się po zakończeniu instalacji lub za pomocą poleceń interfejsu wiersza polecenia dotyczące tej aplikacji. Jeśli Twoim serwerze PostgreSQL jest wymuszanie połączeń SSL i skojarzonej aplikacji nie został prawidłowo skonfigurowany, aplikacja może zakończyć się niepowodzeniem nawiązać połączenia z serwerem bazy danych. Zapoznaj się z dokumentacją aplikacji, aby dowiedzieć się, jak włączyć połączenia SSL.


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Aplikacje wymagające weryfikacji certyfikatu dla połączenia SSL
W niektórych przypadkach aplikacje wymagają lokalnego pliku certyfikatu wygenerowana przez zaufany urząd certyfikacji (CA) pliku certyfikatu (.cer) nawiązać bezpiecznego połączenia. Zobacz poniższe kroki, aby uzyskać plik cer, dekodowanie certyfikatu i powiąż go z aplikacją.

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>Pobierz plik certyfikatu z urząd certyfikacji (CA) 
Certyfikat umożliwia komunikację za pośrednictwem protokołu SSL za pomocą usługi Azure Database dla serwera PostgreSQL [tutaj](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt). Pobierz plik certyfikatu lokalnie.

### <a name="download-and-install-openssl-on-your-machine"></a>Pobrać i zainstalować protokół OpenSSL na komputerze 
Do zdekodowania pliku certyfikatu, potrzebne dla aplikacji w taki sposób bezpiecznie połączyć się z serwerem bazy danych, należy zainstalować protokół OpenSSL na komputerze lokalnym.

#### <a name="for-linux-os-x-or-unix"></a>Dla systemu Linux, Unix lub OS X
Biblioteki OpenSSL znajdują się w kodzie źródłowym bezpośrednio z [OpenSSL Software Foundation](https://www.openssl.org). Poniższe instrukcje pomagają przez kroki niezbędne do zainstalowania OpenSSL na komputerze z systemem Linux. W tym artykule użyto poleceń znanych do pracy na Ubuntu 12.04 lub nowszy.

Otwórz sesję terminala i Pobierz OpenSSL.
```bash
wget http://www.openssl.org/source/openssl-1.1.0e.tar.gz
``` 
Wyodrębnij pliki z pobranego pakietu.
```bash
tar -xvzf openssl-1.1.0e.tar.gz
```
Wprowadź katalog, w którym zostały wyodrębnione pliki. Domyślnie należy go w następujący sposób.

```bash
cd openssl-1.1.0e
```
Konfigurowanie biblioteki OpenSSL, wykonując następujące polecenie. Jeśli pliki w folderze ma inny niż /usr/local/openssl, upewnij się zmienić jedną z następujących czynności.

```bash
./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl
```
Teraz, gdy OpenSSL jest skonfigurowana poprawnie, należy skompilować go przekonwertować certyfikatu. Aby skompilować, uruchom następujące polecenie:

```bash
make
```
Po zakończeniu kompilacji możesz zainstalować protokół OpenSSL, jako plik wykonywalny, uruchamiając następujące polecenie:
```bash
make install
```
Aby sprawdzić, czy został pomyślnie zainstalowany biblioteki OpenSSL, w systemie, uruchom następujące polecenie i sprawdź, upewnij się, że uzyskujesz ten sam wynik.

```bash
/usr/local/openssl/bin/openssl version
```
W przypadku powodzenia powinien zostać wyświetlony następujący komunikat.
```bash
OpenSSL 1.1.0e 7 Apr 2014
```

#### <a name="for-windows"></a>W przypadku systemu Windows
Instalowanie OpenSSL na komputerze Windows może odbywać się w następujący sposób:
1. **(Zalecane)**  Za pomocą wbudowanych funkcji Bash Windows w Windows 10 i nowszych, OpenSSL jest instalowany domyślnie. Instrukcje dotyczące włączania funkcji Bash Windows w systemie Windows 10 można znaleźć [tutaj](https://msdn.microsoft.com/commandline/wsl/install_guide).
2. Instrukcje pobierania aplikacji Win32/64, dostarczone przez społeczność. OpenSSL Software Foundation nie zapewniają ani nie popiera żadnych szczególnych instalatory Windows, ale oferują, gdy lista dostępnych instalatory [tutaj](https://wiki.openssl.org/index.php/Binaries).

### <a name="decode-your-certificate-file"></a>Dekoduj plik certyfikatu
Pobrany plik certyfikatu głównego urzędu certyfikacji jest w zaszyfrowanym formacie. Odszyfrować plik certyfikatu za pomocą biblioteki OpenSSL. Aby to zrobić, uruchom następujące polecenie OpenSSL:

```dos
openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
```

### <a name="connecting-to-azure-database-for-postgresql-with-ssl-certificate-authentication"></a>Łączenia z bazą danych Azure database for PostgreSQL przy użyciu uwierzytelniania certyfikatów SSL
Teraz, gdy zostały pomyślnie zdekodowany certyfikat, teraz można podłączyć do serwera bazy danych bezpiecznie za pośrednictwem protokołu SSL. Aby umożliwić weryfikację certyfikatu serwera, certyfikat musi być uporządkowana ~/.postgresql/root.crt pliku w katalogu macierzystym użytkownika. (W programie Microsoft Windows plik jest o nazwie % APPDATA%\postgresql\root.crt.). Następujące instrukcje na temat łączenia z bazą danych Azure database for PostgreSQL.

#### <a name="using-psql-command-line-utility"></a>Przy użyciu wiersza polecenia psql
Poniższy przykład pokazuje, jak można pomyślnie nawiązać połączenie z serwerem PostgreSQL, korzystając z wiersza polecenia psql. Użyj `root.crt` pliku utworzonego i `sslmode=verify-ca` lub `sslmode=verify-full` opcji.

Przy użyciu interfejsu wiersza polecenia PostgreSQL, wykonaj następujące polecenie:
```bash
psql "sslmode=verify-ca sslrootcert=root.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=mylogin@mydemoserver"
```
W przypadku powodzenia pojawi się następujące dane wyjściowe:
```bash
Password for user mylogin@mydemoserver:
psql (9.6.2)
WARNING: Console code page (437) differs from Windows code page (1252)
     8-bit characters might not work correctly. See psql reference
     page "Notes for Windows users" for details.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=>
```

#### <a name="using-pgadmin-gui-tool"></a>Za pomocą narzędzia z graficznym interfejsem użytkownika pgAdmin
Konfigurowanie narzędzia pgAdmin, 4, aby bezpiecznie połączyć się przez protokół SSL wymaga ustawienia `SSL mode = Verify-CA` lub `SSL mode = Verify-Full` w następujący sposób:

![Zrzut ekranu przedstawiający tryb SSL narzędzia pgAdmin - connection - wymagają](./media/concepts-ssl-connection-security/2-pgadmin-ssl.png)

## <a name="next-steps"></a>Kolejne kroki
Przejrzyj różne opcje łączności aplikacji, zgodnie z [biblioteki połączeń dla usługi Azure Database for postgresql w warstwie](concepts-connection-libraries.md).
