---
title: Bezpieczna komunikacja klienta OPC i PLC OPC za pomocą magazynu OPC — Azure | Dokumentacja firmy Microsoft
description: Zabezpieczanie komunikacji klienta OPC i OPC PLC, rejestrując swoje certyfikaty przy użyciu urzędu certyfikacji w magazynie OPC.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 30eedd982fa0536ce45506c159de6d04132e9a14
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61451093"
---
# <a name="secure-the-communication-of-opc-client-and-opc-plc"></a>Bezpieczna komunikacja klienta OPC i OPC PLC

OPC Vault to mikrousług, konfigurowanie, rejestrowanie i zarządzanie cyklem życia certyfikatów dla serwera OPC UA i aplikacje klienckie w chmurze. W tym artykule dowiesz się, jak zabezpieczyć komunikację klienta OPC i OPC PLC, rejestrując swoje certyfikaty przy użyciu urzędu certyfikacji w magazynie OPC.

W następujących ustawieniach klienta OPC sprawdza łączność OPC PLC. Domyślnie połączenie jest niemożliwe, ponieważ oba te składniki nie są udostępnione za pomocą odpowiednie certyfikaty. Jeśli składnika serwera OPC UA nie zostało jeszcze aprowizowane przy użyciu certyfikatu, ta aplikacja wygeneruje certyfikat z podpisem własnym przy uruchamianiu. Jednak certyfikat można podpisany przez urząd certyfikacji (CA) i zainstalować w składniku OPC UA. Po tej operacji dla klienta OPC i OPC PLC, połączenie jest włączone. Przepływ pracy poniżej opisano proces. Niektóre informacje uzupełniające dotyczące zabezpieczeń OPC UA znajdują się w [w tym dokumencie](https://opcfoundation.org/wp-content/uploads/2014/05/OPC-UA_Security_Model_for_Administrators_V1.00.pdf) oficjalny dokument. Pełne informacje można znaleźć w specyfikacji OPC UA.

Testbed: Następujące środowisko jest skonfigurowane do testowania.

OPC Vault skrypty:
- Zabezpieczanie komunikacji klienta OPC i OPC PLC, rejestrując swoje certyfikaty przy użyciu urzędu certyfikacji w magazynie OPC.

> [!NOTE]
> Aby uzyskać więcej informacji, zobacz serwisu GitHub [repozytorium](https://github.com/Azure-Samples/iot-edge-industrial-configs#testbeds).

## <a name="generate-a-self-signed-certificate-on-startup"></a>Generowanie certyfikatu z podpisem własnym przy uruchamianiu

**Przygotowywanie**

- Upewnij się, że zmienne środowiskowe `$env:_PLC_OPT` i `$env:_CLIENT_OPT` są niezdefiniowane, na przykład `$env:_PLC_OPT=""` w swoje środowiska PowerShell.

- Ustaw zmienną środowiskową `$env:_OPCVAULTID` na ciąg, który pozwala na ponowne odnalezienie danych w magazynie OPC. Dozwolone są tylko znaki alfanumeryczne. W naszym przykładzie "123456" został użyty jako wartość tej zmiennej.

- Upewnij się, nie ma woluminów docker `opcclient` lub `opcplc`. Skontaktuj się z `docker volume ls` i usunąć je z `docker volume rm <volumename>`. Konieczne może być również usunąć kontenerów przy użyciu `docker rm <containerid>` Jeśli woluminy są nadal używane przez kontener.

**Szybki start**

Uruchom następujące polecenie programu PowerShell w folderze głównym repozytorium:

```
docker-compose -f connecttest.yml up
```

**Weryfikacja**

Sprawdź w dzienniku, że nie istnieją żadne certyfikaty zainstalowana przy pierwszym uruchomieniu. Oto dane wyjściowe dziennika OPC PLC (podobne pojawia się dla tych klienta OPC):...
```
opcplc-123456 | [20:51:32 INF] Trusted issuer store contains 0 certs
opcplc-123456 | [20:51:32 INF] Trusted issuer store has 0 CRLs.
opcplc-123456 | [20:51:32 INF] Trusted peer store contains 0 certs
opcplc-123456 | [20:51:32 INF] Trusted peer store has 0 CRLs.
opcplc-123456 | [20:51:32 INF] Rejected certificate store contains 0 certs
```
Jeśli widzisz certyfikaty zgłaszane, wykonaj powyższe kroki przygotowania, a następnie usuń woluminy platformy docker.

Upewnij się, że połączenie OPC PLC zakończyła się niepowodzeniem. Powinny zostać wyświetlone następujące dane wyjściowe w danych wyjściowych dzienników klienta OPC:

```
opcclient-123456 | [20:51:35 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [20:51:36 ERR] Session creation to endpoint 'opc.tcp://opcplc-123456:50000/' failed 1 time(s). Please verify if server is up and OpcClient configuration is correct.
opcclient-123456 | Opc.Ua.ServiceResultException: Certificate is not trusted.
```
Przyczyna niepowodzenia jest, certyfikat nie jest zaufany. Oznacza to, że `opc-client` próbujesz się połączyć `opc-plc` i Odebrano odpowiedź, który wskazuje, że `opc-plc` nie ufa `opc-client`, wynikiem `opc-plc` nie można zweryfikować certyfikatu, `opc-client` udostępnił. Jest to certyfikat z podpisem własnym bez dalszej konfiguracji certyfikatu `opc-plc`, i w związku z tym połączenie nie powiodło się.

## <a name="sign-and-install-certificates-in-opc-ua-components"></a>Zaloguj się i instalowania certyfikatów w składnikach serwera OPC UA

**Przygotowywanie**
1. Spójrz na dane wyjściowe dziennika w kroku 1 i pobrać "Informacje o CreateSigningRequest" OPC PLC i klienta OPC. W tym miejscu danych wyjściowych jest wyświetlana tylko dla OPC PLC:

    ```
    opcplc-123456 | [20:51:32 INF] ----------------------- CreateSigningRequest information ------------------
    opcplc-123456 | [20:51:32 INF] ApplicationUri: urn:OpcPlc:opcplc-123456
    opcplc-123456 | [20:51:32 INF] ApplicationName: OpcPlc
    opcplc-123456 | [20:51:32 INF] ApplicationType: Server
    opcplc-123456 | [20:51:32 INF] ProductUri: https://github.com/azure-samples/iot-edge-opc-plc
    opcplc-123456 | [20:51:32 INF] DiscoveryUrl[0]: opc.tcp://opcplc-123456:50000
    opcplc-123456 | [20:51:32 INF] ServerCapabilities: DA
    opcplc-123456 | [20:51:32 INF] CSR (base64 encoded):
    opcplc-123456 | MIICmzCCAYMCAQAwETEPMA0GA1UEAwwGT3BjUGxjMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAwTvlbinAPWPxR9Lw1ndGsrLGy8GiqVOxyGaDpPUcMchX0k0/ncg28h7xrB2H1PThdiZxUJuUwsNM74HrVgt
    ofmXhA4dLM1cTxZHyJVFjl2L3vK5M58NNf6UNdKcB0x3LyuoT6mAIMXmCioqymFCk1TMzIMzbAe7JVAdUaSRBP1vuqQ1rV/cfNAe35dKQW4aPYgl7pR5f1hqprcDu/oca67X8L4kTl4oN0/bCYTk+Ibcd9cG462oAN+bSwbHn8a2jNky8rGsofA
    o9DOT+0ALPhk6CApCYIP2yxoI/kT188eqUUxzAFF9nyU79AyCkpGPuY8DGMyf56pDofgtGpfY3wQIDAQABoEUwQwYJKoZIhvcNAQkOMTYwNDAyBgNVHREEKzAphhh1cm46T3BjUGxjOm9wY3BsYy0xMjM0NTaCDW9wY3BsYy0xMjM0NTYwDQYJK
    oZIhvcNAQELBQADggEBAAsZLoOLzS2VhDcQRu0QhRbG7CGAxX19l7fDCG2WjU7lTFnCvYVTWTYyaY61ljmrWc7IbCaQdMJM8GRnAnvAzUh/PBDxkOX7NqI2+8F1yQOHgs/AfKuppOd6DIP8EzFAHnc0H85jay6zFdmIDWoWwpy0ACqOVooOTKST
    7uty0mT87bj8Cdy1yf4mvBNQx+nsuTbKgxWCBxGYAyg9dIL2uKL0aeB/ROW5Gkelz5sCEzQ1fFDokUA4oC5QiATQBN3cY7EmvRbPgdToY7CpRN3iiO7J+7bC7BP9YKfuE34E8xOFpskHPHAPf3r002/L0S67HyuVSXLUj1+Jc0LeAAF9Bw0=
    opcplc-123456 | [20:51:32 INF] ---------------------------------------------------------------------------
    ```
    
1. Przejdź do [magazynu OPC witryny sieci Web](https://opcvault.azurewebsites.net/).

1. Wybierz pozycję `Register New`

1. Wprowadź informacje OPC PLC z danych wyjściowych dzienników `CreateSigningRequest information` obszaru do pól wejściowych na `Register New OPC UA Application` wybierz opcję `Server` jako ApplicationType.

1. Wybierz pozycję `Register`.

1. Na następnej stronie `Request New Certificate for OPC UA Application` wybierz `Request new Certificate with Signing Request`.

1. Na następnej stronie `Generate a new Certificate with a Signing Request` wklejania `CSR (base64 encoded)` ciąg z danych wyjściowych dzienników do `CreateRequest` pola wejściowego. Upewnij się, że kopiujesz pełny ciąg.

1. Wybierz pozycję `Generate New Certificate`.

1. Możesz teraz przechodzenia do przodu do `View Certificate Request Details`. Na tej stronie możesz pobrać wszystkie wymagane informacje, aby aprowizować magazynów certyfikatów z `opc-plc`.

1. Na tej stronie:  
    - Wybierz `Certificate` w `Download as Base64` i skopiuj ciąg tekstowy znajdujące się w `EncodedBase64` pola, a następnie zapisz go w celu późniejszego użycia. Nazywamy ją jako `<applicationcertbase64-string>` później. Wybierz pozycję `Back`.

    - Wybierz `Issuer` w `Download as Base64` i skopiuj ciąg tekstowy znajdujące się w `EncodedBase64` pola, a następnie zapisz go w celu późniejszego użycia. Nazywamy ją jako `<addissuercertbase64-string>` później. Wybierz pozycję `Back`.

    - Wybierz `Crl` w `Download as Base64` i skopiuj ciąg tekstowy znajdujące się w `EncodedBase64` pola, a następnie zapisz go w celu późniejszego użycia. Nazywamy ją jako `<updatecrlbase64-string>` później. Wybierz pozycję `Back`.

1. Teraz ustawić w PowerShell zmiennej o nazwie `$env:_PLC_OPT`:

    ```
    `$env:_PLC_OPT="--applicationcertbase64 <applicationcertbase64-string> --addtrustedcertbase64 <addissuercertbase64-string> --addissuercertbase64 <addissuercertbase64-string> --updatecrlbase64 <updatecrlbase64-string>"`  
    ```
    > [!NOTE] 
    > Zastąp ciągi przekazany jako opcja wartości Base64 ciągów, które można pobrać z witryny sieci Web.

Powtórz ten proces pełną, począwszy od `Register New` (krok 3 powyżej) dla klienta OPC. Są tylko następujące różnice, które należy znać:

- Korzystają z danych wyjściowych dzienników `opcclient`.
- Wybierz `Client` jako ApplicationType podczas rejestracji.
- Użyj `$env:_CLIENT_OPT` jako nazwę zmiennej programu PowerShell.

> [!NOTE]
> Podczas pracy w tym scenariuszu, użytkownik może uznały, `<addissuercertbase64-string>` i `<updatecrlbase64-string>` wartości są identyczne dla `opcplc` i `opcclient`. To ma wartość true dla naszych przypadków użycia i zaoszczędzić trochę czasu, podczas wykonywania kroków.

**Szybki start**

Uruchom następujące polecenie programu PowerShell w folderze głównym repozytorium:

```
docker-compose -f connecttest.yml up
```

**Weryfikacja** Sprawdź, czy dwa składniki mają teraz podpisanego certyfikaty aplikacji. Sprawdź dane wyjściowe dziennika dla następujących danych wyjściowych:

```
opcplc-123456 | [20:54:38 INF] Starting to add certificate(s) to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Certificate 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' and thumbprint 'BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83' was added to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Starting to add certificate(s) to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Certificate 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' and thumbprint 'BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83' was added to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Starting to update the current CRL.
opcplc-123456 | [20:54:38 INF] Remove the current CRL from the trusted peer store.
opcplc-123456 | [20:54:38 INF] The new CRL issued by 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' was added to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Remove the current CRL from the trusted issuer store.
opcplc-123456 | [20:54:38 INF] The new CRL issued by 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' was added to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Start updating the current application certificate.
opcplc-123456 | [20:54:38 INF] The current application certificate has SubjectName 'CN=OpcPlc' and thumbprint '8FD43F66479398BDA3AAF5B193199A6657632B49'.
opcplc-123456 | [20:54:39 INF] Remove the existing application certificate with thumbprint '8FD43F66479398BDA3AAF5B193199A6657632B49'.
opcplc-123456 | [20:54:39 INF] The new application certificate 'CN=OpcPlc' and thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586' was added to the application certificate store.
opcplc-123456 | [20:54:39 INF] Activating the new application certificate with thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586'.
opcplc-123456 | [20:54:39 INF] Application certificate with thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586' found in the application certificate store.
opcplc-123456 | [20:54:39 INF] Application certificate is for ApplicationUri 'urn:OpcPlc:opcplc-123456', ApplicationName 'OpcPlc' and Subject is 'OpcPlc'
 ```
Certyfikat aplikacji ma i podpisany przez urząd certyfikacji.

Sprawdź w dzienniku, czy są obecnie zainstalowane certyfikaty. Poniżej przedstawiono dane wyjściowe dziennika OPC PLC i klienta OPC ma podobne dane wyjściowe.
```
opcplc-123456 | [20:54:39 INF] Trusted issuer store contains 1 certs
opcplc-123456 | [20:54:39 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [20:54:39 INF] Trusted issuer store has 1 CRLs.
opcplc-123456 | [20:54:39 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [20:54:39 INF] Trusted peer store contains 1 certs
opcplc-123456 | [20:54:39 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [20:54:39 INF] Trusted peer store has 1 CRLs.
opcplc-123456 | [20:54:39 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [20:54:39 INF] Rejected certificate store contains 0 certs
```
Wystawca certyfikatu aplikacji to urząd certyfikacji `CN=Azure IoT OPC Vault CA, O=Microsoft Corp.` i OPC PLC zaufania również wszystkie certyfikaty podpisane przez ten urząd certyfikacji.


Sprawdź, czy połączenie OPC PLC został pomyślnie utworzony, a klient OPC może odczytywać dane z OPC PLC. Powinny zostać wyświetlone następujące dane wyjściowe w danych wyjściowych dzienników klienta OPC:
```
opcclient-123456 | [20:54:42 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [20:54:42 INF] Session successfully created with Id ns=3;i=1085867946.
opcclient-123456 | [20:54:42 INF] The session to endpoint 'opc.tcp://opcplc-123456:50000/' has 4 entries in its namespace array:
opcclient-123456 | [20:54:42 INF] Namespace index 0: http://opcfoundation.org/UA/
opcclient-123456 | [20:54:42 INF] Namespace index 1: urn:OpcPlc:opcplc-123456
opcclient-123456 | [20:54:42 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
opcclient-123456 | [20:54:42 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
opcclient-123456 | [20:54:42 INF] The server on endpoint 'opc.tcp://opcplc-123456:50000/' supports a minimal sampling interval of 0 ms.
opcclient-123456 | [20:54:42 INF] Execute 'OpcClient.OpcTestAction' action on node 'i=2258' on endpoint 'opc.tcp://opcplc-123456:50000/' with security.
opcclient-123456 | [20:54:42 INF] Action (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258') completed successfully
opcclient-123456 | [20:54:42 INF] Value (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258'): 10/20/2018 20:54:42
```
Jeśli widzisz te dane wyjściowe, następnie OPC PLC jest teraz ufające OPC klienta i na odwrót, ponieważ mają teraz te certyfikaty, które podpisany przez urząd certyfikacji i relacje zaufania certyfikatów, które gdzie podpisane przez ten urząd certyfikacji.

> [!NOTE] 
> Chociaż pokazaliśmy pierwsze kroki weryfikacji dwóch tylko w przypadku OPC PLC te potrzebne można zweryfikować również klienta OPC.


## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak wdrożyć magazyn OPC do istniejącego projektu, poniżej przedstawiono sugerowany następnego kroku:

> [!div class="nextstepaction"]
> [Wdrażanie bliźniaczej reprezentacji OPC do istniejącego projektu](howto-opc-twin-deploy-existing.md)