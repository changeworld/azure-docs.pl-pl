---
title: Zabezpieczanie serwera OPC UA, klienta i aplikację serwera OPC UA przy użyciu magazynu OPC — Azure | Dokumentacja firmy Microsoft
description: Zabezpieczanie klienta OPC UA i OPC UA aplikacji serwera przy użyciu nową parę kluczy i certyfikatów przy użyciu magazynu OPC.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 5ba2dba02585598b3797dd1b490976ebe34b489e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450668"
---
# <a name="secure-opc-ua-client-and-opc-ua-server-application"></a>Zabezpieczanie serwera OPC UA, klienta i aplikacji serwera OPC UA 
OPC Vault to mikrousług, konfigurowanie, rejestrowanie i zarządzanie cyklem życia certyfikatu w przypadku serwera OPC UA i aplikacje klienckie w chmurze. W tym artykule przedstawiono sposób zabezpieczania aplikacji serwera przy użyciu nową parę kluczy i certyfikatów przy użyciu magazynu OPC klienta OPC UA i OPC UA.

W następujących ustawieniach klienta OPC testuje łączność OPC PLC. Domyślnie połączenie nie jest możliwe, ponieważ oba te składniki nie zostały jeszcze aprowizowane z odpowiednie certyfikaty. W tym przepływie pracy firma Microsoft nie używają certyfikatów z podpisem własnym składniki serwera OPC UA i zarejestrować ich za pośrednictwem serwera OPC magazynu. Zobacz poprzedni [testbed](howto-opc-vault-deploy-existing-client-plc-communication.md). Zamiast tego należy to testbed aprowizuje składników przy użyciu nowego certyfikatu, a także przy użyciu nowego klucza prywatnego, które przez Magazyn OPC są generowane. Niektóre informacje uzupełniające dotyczące zabezpieczeń OPC UA znajdują się w tym [oficjalny dokument dotyczący](https://opcfoundation.org/wp-content/uploads/2014/05/OPC-UA_Security_Model_for_Administrators_V1.00.pdf). Pełne informacje można znaleźć w specyfikacji OPC UA.

Testbed: Następujące środowisko jest skonfigurowane do testowania.

OPC Vault skrypty:
- Zabezpieczanie klienta OPC UA i OPC UA aplikacji serwera przy użyciu nowej pary kluczy i certyfikatów przy użyciu magazynu OPC.

> [!NOTE]
> Aby uzyskać więcej informacji, zobacz serwisu GitHub [repozytorium](https://github.com/Azure-Samples/iot-edge-industrial-configs#testbeds).

## <a name="generate-a-new-certificate-and-private-key"></a>Wygeneruj nowy certyfikat i klucz prywatny 
**Przygotowywanie**
- Upewnij się, że zmienne środowiskowe `$env:_PLC_OPT` i `$env:_CLIENT_OPT` są niezdefiniowane. Na przykład `$env:_PLC_OPT=""` w swoje środowiska PowerShell
- Ustaw zmienną środowiskową `$env:_OPCVAULTID` na ciąg, który pozwala na ponowne odnalezienie danych w magazynie OPC. Firma Microsoft zaleca ustawienie 6-cyfrowy numer. W naszym przykładzie "123456" został użyty jako wartość dla zmiennej.
- Upewnij się, istnieje wolumin docker `opcclient` lub `opcplc`. Skontaktuj się z `docker volume ls` i usunąć je z `docker volume rm <volumename>`. Konieczne może być również usunąć kontenerów przy użyciu `docker rm <containerid>` Jeśli woluminy są nadal używane przez kontener.

**Szybki start**
1. Przejdź do [magazynu OPC witryny sieci Web](https://opcvault.azurewebsites.net/)

1. Wybierz pozycję `Register New`

1. Wprowadź informacje OPC PLC pokazany na dane wyjściowe dziennika poprzedniego testbed `CreateSigningRequest information` obszaru do pól wejściowych na `Register New OPC UA Application` wybierz opcję `Server` jako ApplicationType.

1. Wybierz pozycję `Register`

1. Na następnej stronie `Request New Certificate for OPC UA Application` wybierz `Request new KeyPair and Certificate`

1. Na następnej stronie `Generate a new Certificate with a Signing Request` wklejania `CSR (base64 encoded)` ciąg z danych wyjściowych dzienników do `CreateRequest` pola wejściowego. Upewnij się, że kopiujesz pełny ciąg.

1. Na następnej stronie `Request New Certificate for OPC UA Application` wybierz `Request new Certificate with Signing Request`

1. Na następnej stronie `Generate a new KeyPair and for an OPC UA Application` wprowadź `CN=OpcPlc` jako SubjectName, `opcplc-<_OPCVAULTID>` (Zastąp `<_OPCVAULTID>` z Twoimi) jako nazwa_domeny, wybierz `PEM` jako PrivateKeyFormat i wprowadź hasło (nazywamy później go jako `<certpassword-string>`)

1. Wybierz pozycję `Generate New KeyPair`

1. Możesz teraz przechodzenia do przodu do `View Certificate Request Details`. Na tej stronie możesz pobrać wszystkie wymagane informacje, aby aprowizować magazynów certyfikatów z `opc-plc`.

1. Na tej stronie:  
    - Wybierz `Certificate` w `Download as Base64` i skopiuj ciąg tekstowy znajdujące się w `EncodedBase64` pola, a następnie zapisz go w celu późniejszego użycia. Nazywamy ją jako `<applicationcertbase64-string>` później. Wybierz pozycję `Back`.
    - Wybierz `PrivateKey` w `Download as Base64` i skopiuj ciąg tekstowy znajdujące się w `EncodedBase64` pola, a następnie zapisz go w celu późniejszego użycia. Nazywamy ją jako `<privatekeybase64-string>` później. Wybierz pozycję `Back`.
    - Wybierz `Issuer` w `Download as Base64` i skopiuj ciąg tekstowy znajdujące się w `EncodedBase64` pola, a następnie zapisz go w celu późniejszego użycia. Nazywamy ją jako `<addissuercertbase64-string>` później. Wybierz pozycję `Back`.
    - Wybierz `Crl` w `Download as Base64` i skopiuj ciąg tekstowy znajdujące się w `EncodedBase64` pola, a następnie zapisz go w celu późniejszego użycia. Nazywamy ją jako `<updatecrlbase64-string>` później. Wybierz pozycję `Back`.

1. Teraz ustawić w PowerShell zmiennej o nazwie `$env:_PLC_OPT`:

   `$env:_PLC_OPT="--applicationcertbase64 <applicationcertbase64-string> --privatekeybase64 <privatekeybase64-string> --certpassword <certpassword-string> --addtrustedcertbase64 <addissuercertbase64-string> --addissuercertbase64 <addissuercertbase64-string> --updatecrlbase64 <updatecrlbase64-string>"`  

    Zastąp ciągi przekazany jako opcja wartości Base64 ciągów, które można pobrać z witryny sieci Web.  

1. Powtórz ten proces pełną, począwszy od `Register New` dla klienta OPC. Są tylko następujące różnice, które należy znać:
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

**Weryfikacja**

Upewnij się, że dwa składniki nie nastąpiła istniejącego certyfikatu aplikacji. Sprawdź dane wyjściowe dziennika. Poniżej przedstawiono dane wyjściowe OPC PLC i klienta OPC ma podobne dane wyjściowe dziennika.

```
opcplc-123456 | [13:40:08 INF] There is no existing application certificate.
```
W przypadku certyfikatu aplikacji należy usunąć woluminy platformy docker, zgodnie z opisem kroków przygotowania.

Sprawdź w dzienniku, że certyfikat urzędu certyfikacji w magazynie OPC został zainstalowany w wystawcy magazynu certyfikatów również tak jak w magazynie certyfikatów zaufanych elementów równorzędnych. Poniżej przedstawiono dane wyjściowe dziennika OPC PLC i klienta OPC ma podobne dane wyjściowe dziennika. 

```
opcplc-123456 | [13:40:09 INF] Trusted issuer store contains 1 certs
opcplc-123456 | [13:40:09 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [13:40:09 INF] Trusted issuer store has 1 CRLs.
opcplc-123456 | [13:40:09 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [13:40:09 INF] Trusted peer store contains 1 certs
opcplc-123456 | [13:40:09 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [13:40:09 INF] Trusted peer store has 1 CRLs.
opcplc-123456 | [13:40:09 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [13:40:09 INF] Rejected certificate store contains 0 certs
```
OPC PLC ufać wszystkim klientom OPC UA za pomocą certyfikatów podpisanych przez Magazyn OPC.

Sprawdź w dzienniku, że formatem klucza prywatnego jest rozpoznawana jako PEM i zainstalowanie nowego certyfikatu aplikacji. Poniżej przedstawiono dane wyjściowe dziennika OPC PLC i klienta OPC ma podobne dane wyjściowe dziennika. 

```
opcplc-123456 | [13:40:09 INF] The private key for the new certificate was passed in using PEM format.
opcplc-123456 | [13:40:09 INF] Remove the existing application certificate.
opcplc-123456 | [13:40:09 INF] The new application certificate 'CN=OpcPlc' and thumbprint 'A3CB288FC1D2B7A5C08AACF531CF4A85E44A6C4C' was added to the application certificate store.
opcplc-123456 | [13:40:09 INF] Activating the new application certificate with thumbprint 'A3CB288FC1D2B7A5C08AACF531CF4A85E44A6C4C'.
```

Certyfikat aplikacji i klucz prywatny są teraz zainstalowany w magazynie certyfikatów aplikacji i używane przez aplikację serwera OPC UA.

Sprawdź, czy można pomyślnie nawiązać połączenie między klientem OPC i OPC PLC i klienta OPC w stanie pomyślnie odczytywać dane z OPC PLC. Powinny zostać wyświetlone następujące dane wyjściowe w danych wyjściowych dzienników klienta OPC:
```
opcclient-123456 | [13:40:12 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [13:40:12 INF] Session successfully created with Id ns=3;i=941910499.
opcclient-123456 | [13:40:12 INF] The session to endpoint 'opc.tcp://opcplc-123456:50000/' has 4 entries in its namespace array:
opcclient-123456 | [13:40:12 INF] Namespace index 0: http://opcfoundation.org/UA/
opcclient-123456 | [13:40:12 INF] Namespace index 1: urn:OpcPlc:opcplc-123456
opcclient-123456 | [13:40:12 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
opcclient-123456 | [13:40:12 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
opcclient-123456 | [13:40:12 INF] The server on endpoint 'opc.tcp://opcplc-123456:50000/' supports a minimal sampling interval of 0 ms.
opcclient-123456 | [13:40:12 INF] Execute 'OpcClient.OpcTestAction' action on node 'i=2258' on endpoint 'opc.tcp://opcplc-123456:50000/' with security.
opcclient-123456 | [13:40:12 INF] Action (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258') completed successfully
opcclient-123456 | [13:40:12 INF] Value (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258'): 10/21/2018 13:40:12
```
Jeśli widzisz te dane wyjściowe, następnie OPC PLC jest teraz ufające OPC klienta i na odwrót, ponieważ mają teraz te certyfikaty, które podpisany przez urząd certyfikacji lub certyfikaty zaufania, które zostały podpisane przez ten urząd certyfikacji.

### <a name="a-testbed-for-opc-publisher"></a>Testbed dla wydawcy OPC ###

**Szybki start**

Uruchom następujące polecenie programu PowerShell w folderze głównym repozytorium:
```
docker-compose -f testbed.yml up
```

**Weryfikacja**
- Sprawdź, czy dane są wysyłane do usługi IoTHub skonfigurowane przez ustawienie `_HUB_CS` przy użyciu [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) lub [narzędzia iothub-explorer](https://github.com/Azure/iothub-explorer).
- Klient testowy OPC zamierza umożliwiają konfigurowanie wydawcy OPC w celu publikowania/unpublish węzły na serwerze testowym OPC wywołania metody bezpośredniej IoTHub i wywołania metody OPC.
- Obejrzyj dane wyjściowe komunikaty o błędach.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak wdrożyć magazyn OPC do istniejącego projektu, poniżej przedstawiono sugerowany następnego kroku:

> [!div class="nextstepaction"]
> [Wdrażanie bliźniaczej reprezentacji OPC do istniejącego projektu](howto-opc-twin-deploy-existing.md)