---
title: Konfiguracja połączenia SSL w bazie danych Azure dla PostgreSQL
description: Instrukcje i informacje do konfigurowania bazy danych Azure do PostgreSQL i skojarzone aplikacje prawidłowe korzystanie z połączeń SSL.
services: postgresql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: kfile
ms.service: postgresql
ms.custom: ''
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 0a4a7041a905470f895921cfedf2bd94e8466966
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql"></a>Konfiguracja połączenia SSL w bazie danych Azure dla PostgreSQL
Bazy danych platformy Azure dla PostgreSQL preferowany, połączenie klienta aplikacji w usłudze PostgreSQL przy użyciu protokołu Secure Sockets Layer (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacją kliencką ułatwia ochronę przed atakami typu man-in-the-middle dzięki szyfrowaniu strumienia danych między serwerem a aplikacją.

Domyślnie usługa bazy danych PostgreSQL skonfigurowano wymagają połączenia SSL. Opcjonalnie możesz wyłączyć wymaganie protokołu SSL w celu łączenia się z usługą baza danych, jeśli aplikacja kliencka nie obsługuje łączności SSL. 

## <a name="enforcing-ssl-connections"></a>Wymuszanie połączeń SSL
Wszystkie bazy danych dla serwerów PostgreSQL za pośrednictwem portalu Azure i interfejsu wiersza polecenia Azure wymuszanie połączeń SSL jest domyślnie włączona. 

Podobnie parametry połączenia, które są wstępnie zdefiniowane w ustawieniach "Parametry połączenia" w obszarze serwera w portalu Azure obejmują wymaganych parametrów dla typowych języków nawiązać połączenia z serwerem bazy danych przy użyciu protokołu SSL. Parametr SSL w zależności od łącznika, na przykład "ssl = true" lub "sslmode = wymagają" lub "sslmode = wymagane" i innych zmian.

## <a name="configure-enforcement-of-ssl"></a>Konfigurowanie wymuszania protokołu SSL
Opcjonalnie można wyłączyć wymuszenie łączności SSL. Microsoft Azure zaleca, aby włączyć zawsze **połączenia SSL wymusić** ustawienie w celu zwiększenia bezpieczeństwa.

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal
Odwiedź bazy danych Azure, PostgreSQL serwera, a następnie kliknij przycisk **zabezpieczenia połączeń**. Użyj przycisku przełączania, aby włączyć lub wyłączyć **połączenia SSL wymusić** ustawienie. Następnie kliknij przycisk **zapisać**. 

![Zabezpieczenia połączeń — Wyłącz wymuszanie protokołu SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Można potwierdzić ustawienia przeglądając **omówienie** stronę, aby zobaczyć **stan wymuszenia SSL** wskaźnika.

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure
Można włączyć lub wyłączyć **wymuszania ssl** przy użyciu parametru `Enabled` lub `Disabled` odpowiednio wartości wiersza polecenia platformy Azure.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Upewnij się, obsługuje Twojej aplikacji lub struktury połączeń SSL
Wiele wspólnej struktury aplikacji używających PostgreSQL dla swoich usług bazy danych, takich jak Drupal i Django, nie należy włączać SSL domyślnie podczas instalacji. Włączenie łączności SSL muszą być konfigurowane po instalacji lub za pomocą polecenia interfejsu wiersza polecenia specyficzne dla aplikacji. Serwer PostgreSQL wymusza połączeń SSL i skojarzonej aplikacji nie jest skonfigurowany prawidłowo, aplikacja może uniemożliwić połączenie z serwerem bazy danych. Zapoznaj się z dokumentacją aplikacji, aby dowiedzieć się, jak włączyć połączeń SSL.


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Aplikacje wymagające weryfikacji certyfikatów dla połączeń SSL
W niektórych przypadkach aplikacje wymagają lokalnego pliku certyfikatu wygenerowana przez zaufany urząd certyfikacji, plik certyfikatu (.cer) nawiązać bezpieczne. Zobacz poniższe kroki, aby pobrać plik cer, dekodowanie certyfikatu i powiązać go z aplikacji.

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>Pobierz plik certyfikatu z urzędu certyfikacji 
Certyfikat potrzebne do komunikowania się za pośrednictwem protokołu SSL z bazą danych Azure dla znajduje się serwer PostgreSQL [tutaj](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt). Pobierz plik certyfikatu lokalnie.

### <a name="download-and-install-openssl-on-your-machine"></a>Pobierz i zainstaluj biblioteki OpenSSL na tym komputerze 
Zdekodować plik certyfikatu, wymagane bezpieczne połączenie z serwerem bazy danych aplikacji, musisz zainstalować biblioteki OpenSSL na komputerze lokalnym.

#### <a name="for-linux-os-x-or-unix"></a>For Linux, OS X, or Unix
Biblioteki OpenSSL znajdują się w kodzie źródłowym bezpośrednio z [Foundation oprogramowania biblioteki OpenSSL](http://www.openssl.org). Poniższe instrukcje pomagają przez kroki niezbędne do zainstalowania biblioteki OpenSSL na komputerze z systemem Linux. W tym artykule używa poleceń znane pracować nad Ubuntu 12.04 i wyższych.

Otwórz sesję terminala i Pobierz biblioteki OpenSSL.
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
Teraz biblioteki OpenSSL jest skonfigurowany prawidłowo, należy skompilować go przekonwertować certyfikatu. Aby skompilować, uruchom następujące polecenie:

```bash
make
```
Po zakończeniu kompilacji wszystko jest gotowe do zainstalowania biblioteki OpenSSL jako plik wykonywalny, uruchamiając następujące polecenie:
```bash
make install
```
Aby upewnić się, że biblioteki OpenSSL pomyślnie zostały zainstalowane w systemie, uruchom następujące polecenie i sprawdź, upewnij się, że możesz uzyskać tych samych danych wyjściowych.

```bash
/usr/local/openssl/bin/openssl version
```
W przypadku powodzenia powinien zostać wyświetlony następujący komunikat.
```bash
OpenSSL 1.1.0e 7 Apr 2014
```

#### <a name="for-windows"></a>Dla systemu Windows
Instalowanie biblioteki OpenSSL na komputerach z systemem Windows można zrobić w następujący sposób:
1. **(Zalecane)**  Za pomocą wbudowanych funkcji Bash dla systemu Windows w oknie 10 i nowszych, biblioteki OpenSSL jest instalowany domyślnie. Instrukcje dotyczące włączania funkcji Bash dla systemu Windows w systemie Windows 10 można znaleźć [tutaj](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide).
2. Do pobierania aplikacji Win32/64, dostarczone przez społeczność. Podczas Foundation oprogramowania biblioteki OpenSSL Podaj ani nie zatwierdza wszystkie określone składniki Instalatora Windows, zawierają listę dostępnych instalatorów [tutaj](https://wiki.openssl.org/index.php/Binaries).

### <a name="decode-your-certificate-file"></a>Dekoduj plik certyfikatu
Pobrany plik główny urząd certyfikacji jest w formacie zaszyfrowanym. Użyj biblioteki OpenSSL zdekodować pliku certyfikatu. Aby to zrobić, uruchom to polecenie biblioteki OpenSSL:

```dos
openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
```

### <a name="connecting-to-azure-database-for-postgresql-with-ssl-certificate-authentication"></a>Łączenie z bazą danych Azure dla PostgreSQL przy użyciu uwierzytelniania certyfikatu SSL
Teraz, certyfikatu pomyślnie zostały zdekodowany, można teraz połączenie z serwerem bazy danych bezpiecznie za pośrednictwem protokołu SSL. Aby umożliwić weryfikacji certyfikatu serwera, certyfikat muszą znajdować się w ~/.postgresql/root.crt pliku w katalogu macierzystego użytkownika. (Microsoft Windows plik jest o nazwie % APPDATA%\postgresql\root.crt.). Poniżej znajdują się instrukcje dotyczące nawiązywania połączenia z bazą danych Azure dla PostgreSQL.

#### <a name="using-psql-command-line-utility"></a>Za pomocą narzędzia wiersza polecenia psql
Poniższy przykład pokazuje, jak nawiązywać połączeń z serwerem PostgreSQL za pomocą narzędzia wiersza polecenia psql. Użyj `root.crt` plik utworzony i `sslmode=verify-ca` lub `sslmode=verify-full` opcji.

Przy użyciu interfejsu wiersza polecenia PostgreSQL, uruchom następujące polecenie:
```bash
psql "sslmode=verify-ca sslrootcert=root.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=mylogin@mydemoserver"
```
W razie powodzenia pojawi się następujące dane wyjściowe:
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

#### <a name="using-pgadmin-gui-tool"></a>Za pomocą narzędzia pgAdmin graficznego interfejsu użytkownika
Konfigurowanie pgAdmin 4 do bezpieczne łączenie się za pośrednictwem protokołu SSL wymaga ustawienia `SSL mode = Verify-CA` lub `SSL mode = Verify-Full` w następujący sposób:

![Zrzut ekranu przedstawiający pgAdmin — połączenie — tryb SSL wymagają](./media/concepts-ssl-connection-security/2-pgadmin-ssl.png)

## <a name="next-steps"></a>Kolejne kroki
Przegląd różnych opcji łączności aplikacji po [biblioteki połączeń dla bazy danych Azure dla PostgreSQL](concepts-connection-libraries.md).
