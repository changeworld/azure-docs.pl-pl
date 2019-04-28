---
title: Konfigurowanie łączności SSL w usłudze Azure Database for PostgreSQL
description: Instrukcje i informacje, aby skonfigurować usługi Azure Database for PostgreSQL i skojarzonych aplikacji, aby prawidłowo używać połączeń SSL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 5a0fc99052b18dc1fa837147aa914a473d27d832
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60871417"
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

### <a name="install-a-cert-decoder-on-your-machine"></a>Zainstaluj dekodera certyfikatu na komputerze 
Możesz użyć [OpenSSL](https://github.com/openssl/openssl) zdekodować pliku certyfikatu, potrzebne dla aplikacji w taki sposób bezpiecznie połączyć się z serwerem bazy danych. Aby dowiedzieć się, jak zainstalować protokół OpenSSL, zobacz [instrukcje dotyczące instalacji OpenSSL](https://github.com/openssl/openssl/blob/master/INSTALL). 


### <a name="decode-your-certificate-file"></a>Dekoduj plik certyfikatu
Pobrany plik certyfikatu głównego urzędu certyfikacji jest w zaszyfrowanym formacie. Odszyfrować plik certyfikatu za pomocą biblioteki OpenSSL. Aby to zrobić, uruchom następujące polecenie OpenSSL:

```
openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
```

### <a name="connecting-to-azure-database-for-postgresql-with-ssl-certificate-authentication"></a>Łączenia z bazą danych Azure database for PostgreSQL przy użyciu uwierzytelniania certyfikatów SSL
Teraz, gdy zostały pomyślnie zdekodowany certyfikat, teraz można podłączyć do serwera bazy danych bezpiecznie za pośrednictwem protokołu SSL. Aby umożliwić weryfikację certyfikatu serwera, certyfikat musi być uporządkowana ~/.postgresql/root.crt pliku w katalogu macierzystym użytkownika. (W programie Microsoft Windows plik jest o nazwie % APPDATA%\postgresql\root.crt.). 

#### <a name="connect-using-psql"></a>Połącz za pomocą narzędzia psql
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

## <a name="next-steps"></a>Kolejne kroki
Przejrzyj różne opcje łączności aplikacji, zgodnie z [biblioteki połączeń dla usługi Azure Database for postgresql w warstwie](concepts-connection-libraries.md).
